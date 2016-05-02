---
layout: post
title: Spark 程序依賴問題兩則
---

### 上傳依賴

用 maven 打包出來的 jar 由於沒有包括依賴，提交到 Spark 會因為找不到依賴而無法執行。簡單的解決方法是用 [maven-assembly-plugin][] 打包出一個包含了所有依賴的、可以獨立使用的 jar（俗稱 fat jar）。我並不喜歡這種方式，原因有二：

1. 生成的 jar 包羅萬象，所以非常大。比如我的程序用這種方式打包的話，通常會生成 100M+ 的包。
2. 不容易解決不同 jar 中 resource 的衝突。比如 Spring 可以用 `classpath*:application-context.xml` 解析所有 jar 中的 `application-context.xml`；而 maven-assembly-plugin 打包出來卻只能保留一個 application-context.xml。

利用 `spark-submit` 的 `--jars` 參數，我們不必把所有東西都打包到一起。它可以將本地的 jars 上傳到 Spark driver 和 executors 並添加到 classpath 里。但是，maven 將所有的 jar 都保存在 `$HOME/.m2` 目錄中，我們要如何收集到項目所需的依賴呢？[maven-dependency-plugin][] 的 [copy-dependencies][] 功能可以幫我們做到。這個功能會將項目依賴的 jar 都複製到指定目錄。比如，如下配置會將所有的 runtime 和 compile 依賴複製到 `project_path/target/lib`：

```xml
<plugin>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>copy-dependencies</goal>
            </goals>
            <configuration>
                <outputDirectory>${project.build.directory}/lib</outputDirectory>
                <includeScope>runtime</includeScope>
            </configuration>
        </execution>
    </executions>
</plugin>
```

在提交任務時，需要在 `--jars` 後指明每一個 jar 的完整路径。我使用一個 ruby 腳本輔助：

```ruby
exec "spark-submit",
     "--jars", Dir[File.join(project_path, "target/lib/*.jar")].join(","),
     ...
```

[maven-assembly-plugin]: https://maven.apache.org/components/plugins/maven-assembly-plugin/usage.html
[maven-dependency-plugin]: http://maven.apache.org/plugins/maven-dependency-plugin/
[copy-dependencies]: http://maven.apache.org/plugins/maven-dependency-plugin/examples/copying-project-dependencies.html

### 依賴衝突

我的程序使用 gson 解析從 kafka 中接收的數據。在單獨測試時，解析模塊正常工作，而提交到 Spark 後卻抛出解析異常。一番調試後，發現在 Spark 上運行時 `@TypeAdapter` 並不起作用，導致 gson 不知如何解析某個類型。然而，我在 `--jars` 參數後指定的 gson 確實是程序依賴的版本，不應該不識別。

我猜測原因是 Spark 運行環境中，在加載 `--jars` 指定的 jar 之前，已加載過老版本的 gson。然而 Spark 尚不支持優先加載用戶指定的 jar，所以无法替換運行環境中已存在的老版本依賴。幸好解決依賴衝突常用的 [maven-shade-plugin][] 有個 [relocate][] 功能可以幫助我繞過這個問題。

relocate 可以將一個 package 改名成另一個，並修改程序中所有使用這個 package 的地方使用改名後的 package。比如，如下配置將 gson 包含在生成的 jar 中，並將 `com.google.gson` / `com.google.gson.*` 改名為 `shaded.com.google.gson` / `shaded.com.google.gson.*`：

```xml
<plugin>
    <artifactId>maven-shade-plugin</artifactId>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>shade</goal>
            </goals>
            <configuration>
                <artifactSet>
                    <includes>
                        <include>com.google.code.gson:gson</include>
                    </includes>
                </artifactSet>
                <relocations>
                    <relocation>
                        <pattern>com.google.gson</pattern>
                        <shadedPattern>shaded.com.google.gson</shadedPattern>
                    </relocation>
                </relocations>
            </configuration>
        </execution>
    </executions>
</plugin>
```

這個方法有一個副作用，當 gson 抛出異常時，stacktrace 會顯示新的 package 名稱，而非原来的。

```
shaded.com.google.gson.JsonSyntaxException: java.lang.IllegalStateException: Expected BEGIN_OBJECT but was STRING at line 1 column 105 path $.timestamp
        at shaded.com.google.gson.internal.bind.ReflectiveTypeAdapterFactory$Adapter.read(ReflectiveTypeAdapterFactory.java:220)
        at shaded.com.google.gson.internal.bind.ReflectiveTypeAdapterFactory$1.read(ReflectiveTypeAdapterFactory.java:116)
        at shaded.com.google.gson.internal.bind.ReflectiveTypeAdapterFactory$Adapter.read(ReflectiveTypeAdapterFactory.java:216)
        at shaded.com.google.gson.Gson.fromJson(Gson.java:879)
        at shaded.com.google.gson.Gson.fromJson(Gson.java:844)
        at shaded.com.google.gson.Gson.fromJson(Gson.java:793)
        at shaded.com.google.gson.Gson.fromJson(Gson.java:765)
        ...
```

[maven-shade-plugin]: https://maven.apache.org/plugins/maven-shade-plugin/
[relocate]: https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html
