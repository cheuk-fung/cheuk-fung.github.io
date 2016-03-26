---
layout: post
title: GoF 閱讀筆記（Structural Patterns）
---

這一章討論類和對象的組織結構。有意思的是，有些模式之間的差異很微妙。

### Adapter 適配器

適配器將一個接口轉換為用戶期望的另一個接口，解決接口不匹配的問題。JDBC 和 ActiveRecord 都是適配器的典型應用，它們設計了一套統一的關係型數據庫接口，使用不同的適配器將對應數據庫的接口轉換為這套統一的接口。

### Bridge 橋接

橋接模式用於解耦抽象概念和具體實現，讓抽象層級和實現層級可以獨自發展。比如，在概念上，Window 可以有 IconWindow、TransientWindow 等各種不同的 Window；而在實現上，GNOME 和 KDE 等平臺的 Window 有不同的實現方式。使用橋接可以避免將這兩種層級組合在一起而需要實現諸如 GNOMEIconWindow、KDEIconWindow 的類。

適配器和橋接在使用上有許多相似的地方，容易搞混。它們的主要區別是適配器解決的是兩個獨立接口不兼容的問題，往往是在使用時這兩個接口**已經存在**了；而橋接是在**設計之初**就考慮到要將抽象与實現分離，橋接所關聯的兩個接口之間的聯繫較之適配器的要更緊密些。

### Composite 組合

組合模式中，一個組合類將其父類的多個對象組合在一起。由於接口一致，在使用上組合類與其它子類沒差。一個簡單的 Java 例子：

```java
class Graphic {
    public abstract void draw();
}

class Picture extends Graphic {
    private List<Graphic> graphics;

    @Override
    public void draw() {
        for (Graphic graphic : graphics) {
            graphic.draw();
        }
    }
}
```

### Decorator 修飾

修飾模式指一個類中包含其父類的一個對象並為它提供額外的功能。由於接口一致，在使用上修飾類與其它子類沒差。如：

```java
class BorderedGraphic extends Graphic {
    private Graphic graphic;

    @Override
    public void draw() {
        drawBorder();
        graphic.draw();
    }
}
```

組合和修飾也是一組類似的模式，它們都包含了別的對象又提供與被包含對象一致的接口。也許會有人誤認為修飾是組合退化為只包含一個對象，實際上它們的意圖不同。組合強調結構上**組織多個對象**，並不關心功能上的擴展；而修飾的目的恰恰在於**提供額外功能**。

### Facade 外觀

外觀模式在子系統中的一組接口上再抽象出一個統一的高層接口来負責子系統之間的溝通，使用時只需調用這個高層接口即可。書中以編譯器為例，`Compiler.compile()` 接口封裝了 `Scanner`、`Parser`、`ProgramNode` 等組件。

### Flyweight 享元

享元模式比較簡單，也很常用，它指通過共享一些**簡單**且**不變**的對象來節約內存使用。

### Proxy 代理

代理模式的組織方式和修飾模式一樣，但其目的並不是要提供额外功能，而是為不方便或無法直接訪問的對象提供一個幫助訪問的替身。Lazy loading 是代理模式的一種常見應用。

```java
class LazyImageProxy extends Graphic {
    private Graphic image = null;

    private Image getImage() {
        if (image == null) {
            image = loadImage();
        }
        return image;
    }

    @Override
    public void draw() {
        getImage().draw();
    }
}
```
