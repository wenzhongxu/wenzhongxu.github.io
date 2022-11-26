# Wenzhong Blog

> This is the blog of <a href="https://wenzhongxu.github.io" target="_blank">Wenzhong's Blog</a>
> Thanks the provider of the theme **<a href="https://github.com/Simpleyyt/jekyll-theme-next" target="_blank">jekyll-theme-next</a>**

### 搭建指南
具体搭建个人博客的过程可参考<a href="http://theme-next.simpleyyt.com/">NexT主题</a>的文档

### 本地运行
#### 安装ruby
1. 下载[ruby](https://rubyinstaller.org/downloads/)并安装
    ![ruby-installed](/img/tools/ruby-jekyll/ruby-installed.png)

2. 安装jekyll
    ```shell
    gem install jekyll bundler
    ```

3. 进入.github.io目录
    ```shell
    bundle install # 安装依赖
    bundle exec jekyll serve # 启动服务
    ```
    ![server-start](/img/tools/ruby-jekyll/server-start.png)



### 可能遇到的问题
1. 本地运行网站时，报You have already activated X, but your Gemfile requires Y的错误时，一般是安装的依赖包版本不对，可以删除Gemfile.lock文件，重新执行 bundle install

2. 本地运行网站时，打开中文文件名的博客，出现404时，是因为Jekyll无法正常解析中文文件名。可参考<a href="https://blog.csdn.net/yinaoxiong/article/details/54025482?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs&dist_request_id=&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs">解决方案</a>。即 修改安装目录\Ruby22-x64\lib\ruby\2.2.0\webrick\httpservlet下的filehandler.rb文件
找到下列两处，添加一句（+的一行为添加部分）
    ```shell
    path = req.path_info.dup.force_encoding(Encoding.find("filesystem"))
    + path.force_encoding("UTF-8") # 加入编码
    if trailing_pathsep?(req.path_info)
    ```
    ```shell
    break if base == "/"
    + base.force_encoding("UTF-8") #加入編碼
    break unless File.directory?(File.expand_path(res.filename + base))
    ```

3. 本地运行网站时，如果ruby的版本高于3.0.0，启动服务失败，则需要添加webrick依赖
    ![serve-error-start](/img/tools/ruby-jekyll/serve-error-start.png)
    ```shell
    bundle add webrick
    ```