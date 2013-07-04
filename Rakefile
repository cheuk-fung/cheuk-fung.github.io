task :setup do
  system "git clone . _deploy"
  Dir.chdir("_deploy") { system "git checkout master" }
end

task :deploy do
  system "jekyll build -s source"
  Dir.chdir("_deploy") do
    system "rm -rf *"
    system "cp -r ../_site/* ."
    system "git add -A"
    system "git commit -m 'Site update at #{Time.now.utc}'"
    system "git push origin master"
  end
  system "git push origin master"
end
