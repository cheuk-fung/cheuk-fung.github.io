desc 'create a new post'
task :new, :title do |task, args|
  title = args[:title] || 'untitled'
  date_str = Time.now.strftime('%Y-%m-%d')
  path = File.dirname(__FILE__) + "/_posts/#{date_str}-#{title}.md"

  File.open path, 'w+' do |post|
    post.write <<-END.strip.split("\n").map { |s| s.strip }.join("\n")
      ---
      layout: post
      title: #{title}
      ---
    END
  end

  puts path
end

desc 'get the last modified post'
task :last do
  puts Dir['_posts/*.md'].sort_by { |f| File.mtime f }.last
end
