---
layout: post
author: Makefile君
title: 写个raketask生成jekyll模板吧
category: 挖坑
tags: ruby erb jekyll
date: 2015-04-26 23:46:06
keywords:
description:
---

# 写个raketask生成jekyll模板吧

首先，先来回顾下Raketask的写法，新建一个名为Rakefile的文件

```Ruby
require 'rake'

task :default => :test

desc "simple demo task"
task :test do |t, args|
  p 'this is just a test'
end

```

然后执行`rake test`,即会自动执行该任务

当然执行`rake -T`或`rake -D`会列出所有的tasks

下面列出自动生成新post的文件，比较有意思的是erb的binding部分，快十二点了，先放在这里，以后有时间再写。

```Ruby
desc "Create a post in _posts"
task :new_post, :title, :category do |t, args|
  template = File.read('./templates/post.erb')
  render = ERB.new(template)

  params_loader = PostTemplateParamsLoader.new(args)
  file_path = params_loader.file_path

  post_content = render.result(params_loader.params)

  exit(-1) if File.exist?(file_path)
  File.write(file_path, post_content)
  system("echo '#{file_path}'| pbcopy")
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
```