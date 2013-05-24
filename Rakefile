task :default => [:serve]

desc 'Build and watch the site'
task :build do
  system 'jekyll build --watch'
end

desc 'Serve and watch the site'
task :serve do
  system 'jekyll serve --watch'
end

desc 'Create a new post'
task :post do
  date = Time.new
  title = STDIN.gets.chomp
  
  content = <<-EOS.gsub(/^ +/, '')
    ---
    layout: post
    date: #{date.strftime('%Y-%m-%d %H:%M:%S')}
    title: #{title}
    ---
    
  EOS
  
  datef = date.strftime('%Y-%m-%d')
  titlef = title.downcase.gsub(' ', '-')
  filename = "_posts/#{datef}-#{titlef}.md"
  
  File.write filename, content
end
