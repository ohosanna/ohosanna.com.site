---
layout: post
title: Gentoo下用Nginx+thin构建rails环境
created: 2011-12-20 16:45:20.000000000 +08:00
tags: web linux rails
---

Thin是一个ruby的轻量级的web server，根据它官网的提供的对比可以看到它在100个并发连接的情况下性能还是不错的。  
![Benchmarks](http://chart.apis.google.com/chart?cht=bvg&chd=t:14.98,54.8723076923077,48.9184615384615,79.9276923076923|14.8692307692308,65.0615384615385,70.4446153846154,89.5553846153846|14.9476923076923,35.1123076923077,70.18,88.6769230769231&chbh=16&chs=350x150&chl=WEBrick|Mongrel|Evented%20M.|Thin&chco=000000,666666,cccccc&chdl=1%20c%20req.|10%20c%20req.|100%20c%20req.)

<!-- more -->

要使用Thin作为你的Rails项目的服务器的话，只需在项目Gemfile里加入

```
gem 'thin'
```

然后转到项目目录下运行:  

```
bundle install
```

Thin安装完之后，可以通过在项目目录下的lib/tasks下建立一个任务脚本来控制Thin集群。比如添加一个lib/tasks/thin.rake文件，内容如下:

```ruby
namespace :thin do
  namespace :cluster do
  desc 'Start thin cluster'
    task :start => :environment do
      Dir.chdir(Rails.root)
      port_range = Rails.env == 'development' ? 3 : 8
      (ENV['SIZE'] ? ENV['SIZE'].to_i : 4).times do |i|
        Thread.new do
          `sleep 1`
          port = ENV['PORT'] ? ENV['PORT'].to_i + i : ("#{port_range}%03d" % i)
          str  = "thin start -d -p#{port} -Ptmp/pids/thin-#{port}.pid"
          str += " -e#{Rails.env}"
          #puts "Running #{str} \n"
          puts "Starting server on port #{port}... \n"
          `#{str}`
        end
      end
    end
desc 'Stop all thin clusters'
    task :stop => :environment do
      Dir.chdir(Rails.root)
      Dir.new("#{Rails.root}/tmp/pids").each do |file|
        Thread.new do
          `sleep 1`
          if file.starts_with?("thin-")
            str  = "thin stop -Ptmp/pids/#{file}"
            #puts "Running #{str} \n"
            puts "Stopping server on port #{file[/\d+/]}... \n"
            `#{str}`
          end
        end
      end
    end
  end
end
```

之后就可以用:

```bash
$ rake thin:cluster:start RAILS_ENV=production SIZE=3 PORT=3100
$ rake thin:cluster:stop
```

来启动|停止Thin集群了。

接下来就是配置Nginx了。在Nginx的配置文件里添加一个虚拟主机：

```
upstream thin {
  server 127.0.0.1:3100;
  server 127.0.0.1:3101;
  server 127.0.0.1:3102;
}

server {
  listen 80;
  server_name blog.keep-coding.com;
  access_log  /home/nofool/project/myblog/log/blog.keep-coding.log;
  root /home/nofool/project/myblog/public/;
  
  location / {
    proxy_set_header  X-Real-IP  $remote_addr;
    proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header   X-Sendfile-Type  X-Accel-Redirect;
    proxy_redirect off;

    if (-f $request_filename/index.html) {
      rewrite (.*) $1/index.html break;
    }

    if (-f $request_filename.html) {
      rewrite (.*) $1.html break;
    }

    if (!-f $request_filename) {
      proxy_pass http://thin;
      break;
    }
  }
}
```

如果你的Rails是3.1+版本，需要把项目config/environments/production.rb文件里的:  

```
config.action_dispatch.x_sendfile_header = "X-Sendfile"
```

改为：

```
config.action_dispatch.x_sendfile_header = "X-Accel-Redirect"
```

否则会出现访问图片提示‘The image “...” cannot be displayed because it contains errors’而无法显示的问题。如果修改之后还出现同样的问题，那就要检查一下Nginx是否添加了“nginx_modules_http_headers_more”这个Use的支持。
