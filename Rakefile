require 'rake'
require 'erb'

DATE = Time.now.strftime("%Y-%m-%d")
TIME = Time.now.strftime("%H:%M:%S")
POST_TIME = DATE + ' ' + TIME

task :default => :test

desc "simple demo task"
task :test do |t, args|
  p 'this is just a test'
end

desc "Create a post in _posts[tech,life,read,listen,watch,play]"
task :new_post, :title, :category do |t, args|
  template = File.read('./templates/post.erb')
  render = ERB.new(template)

  params_loader = PostTemplateParamsLoader.new(args)
  file_path = params_loader.file_path

  post_content = render.result(params_loader.params)

  exit(-1) if File.exist?(file_path)
  File.write(file_path, post_content)
  system("echo '#{file_path}'| xclip -selection clipboard")
end

class PostTemplateParamsLoader
  attr_reader :author,:title,:category,:tags,:now,:file_path

  def initialize(args)
    directories = {
        tech: '挖坑',
        life: '扯淡',
        read: '我读',
        listen: '我听',
        watch: '我看',
        play: '我玩'
    }

    @author = 'Makefile君'
    @title = args[:title]
    @now = DATE + ' ' + TIME
    @category = directories[args[:category].to_sym]
    exit(-1) if @category.nil?
    @tags = args.extras.join(' ')
    @file_path = './_posts/'+@category+'/'+DATE+'-'+standard_filename(@title, 'md')
  end

  def params
    binding
  end

  def standard_filename(title, extension)
    characters = /("|'|!|\?|:|\s\z|　\z)/
    whitespace = /\s|　/
    "#{title.gsub(characters, "").gsub(whitespace, "-").downcase}.#{extension}"
  end
end
