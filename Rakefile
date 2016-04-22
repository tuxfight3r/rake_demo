require 'yaml'
require 'pp'

#Global tasks
desc "default task"
task :default =>[:test]

task :test do
  puts "default task"
end

desc "list files from ~"
task :list do
  files = Dir["file*"].sort
  puts files
end

desc "Restart WebServer"
task :restart do
  touch "restart.txt"
end

#namespaces to contain tasks
namespace :server do
  desc "start webserver"
  task :start do
  puts "starting webserver"
  end

  desc "stop webserver"
  task :stop do
  puts "stop webserver"
  end
  
  desc "status webserver"
  task :status do
  puts "status info"
  end

  #single dependency
  desc "detailed status webserver"
  task :dstatus => :status do
  puts "detailed info status"
  end

  #multiple dependency
  desc "Restart webserver"
  task :restart => [:stop, :start]  do
  puts "restarting web server"
  end
end


#second name space define
namespace :blog do
  desc "set_title" 
  task :set_title do
  title = ENV['TITLE'] || "Blog"
  puts "Setting the Title: #{title}"
  end
end

#rake with arguments
desc "rake task with arguments"
task :name, [:first_name, :last_name] do |t, args|
puts "First name is #{args.first_name}"
puts "Last name is #{args.last_name}"
end

#creating task for list of hosts
HOSTS = File.join(".", "hosts.txt") # List of all hosts
hosts=File.foreach(ENV["HOSTS"] || HOSTS).map { |line| line.strip }
#puts hosts.class()


#for i in {1..10}; do echo "Content from file${i}" > file${i};done
namespace :check do
  namespace :host do
    desc "Run test on all hosts "
    task :all => hosts.map { |h| :"#{h}" }
  # Per server tasks
    hosts.each do |host_name|
      desc "Run test on hosts #{host_name}"
      task "#{host_name}" do 
        puts "checking server #{host_name}"    
        file_no="#{host_name}".match(/host(\d+)/)[1]
        sh "cat file#{file_no}"
      end
    end
  end
end

#loop through a yaml file
properties=YAML.load_file('properties.yml')

desc "loop through yaml and print"
task :yaml do
  if properties.key?('env')
    properties['env'].each do |pversion|
      _,peversion=pversion.split("=")
          puts "PUPPET VERSION: #{peversion}"
    end
    puts ""
  end
end

#file operations in rake
desc "rename settings.yaml to settings.yml"
file ':settings.yaml' do
  mv 'settings.yaml', 'settings.yml'
end

#file operations with pandoc and markdown rendering
namespace :renderblog do
  desc "renders markdown to create blog index"
  task :default => 'blog.html'
  articles = ['article1', 'article2']
  articles.each do |article|
    file "#{article}.html" => "#{article}.md" do
    sh "pandoc -s #{article}.md -o #{article}.html"
    end
  end

  file 'blog.html' => articles.map { |a| "#{a}.html" } do
    File.open('blog.html', 'w') do |f|
      article_links = articles.map do |article|
        <<-EOS
        <a href='#{article}.html'>
        Article #{article.match(/\d+$/)}
        </a>
        EOS
      end
      html = <<-EOS
        <!DOCTYPE html>
        <html>
        <head>
        <title>Rake essential</title>
        </head>
        <body>
        #{article_links.join('<br />')}
        </body>
        </html>
        EOS
      f.write(html)
    end
  end
end
#  vim: set ts=4 sw=2 tw=0 sts=2 noet :
