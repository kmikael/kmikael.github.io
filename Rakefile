task :default => [:serve]

desc 'Build and watch the site'
task :build do
  system 'jekyll build --safe --watch'
end

desc 'Serve and watch the site'
task :serve do
  system 'jekyll serve --safe --watch'
end

desc 'Create a new post'
task :post do
  date = Time.new
  title = STDIN.gets.chomp.strip
  
  content = <<-EOS.gsub(/^ +/, '')
    ---
    layout: post
    date: #{date.strftime('%Y-%m-%d %H:%M:%S')}
    title: #{title}
    ---
    
  EOS
  
  date_slug = date.strftime('%Y-%m-%d')
  title_slug = title.downcase.tr_s('^A-Za-z0-9', '-').chomp('-')
  filename = "_posts/#{date_slug}-#{title_slug}.md"
  
  File.write filename, content
end
