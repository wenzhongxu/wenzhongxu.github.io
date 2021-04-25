# Wenzhong Blog

> This is the blog of [Wenzhong's Blog](https://wenzhongxu.github.io)
 Thanks the provider of the theme **[hexo-theme-snippet](https://github.com/shenliyang/hexo-theme-snippet)**

### 搭建指南

#### 1.环境要求
- [x] node.js
- [x] git

#### 2.搭建步骤
1. 安装Hexo
```js
npm install hexo
```
2. 使用hexo命令创建项目
```js
// npx hexo <command>
// npx hexo init <folder> // folder必须是空文件夹
npx hexo init F:\MyProject\myblog\myblogs
```
3. 下载theme主题
```shell
git clone git://github.com/shenliyang/hexo-theme-snippet.git themes/hexo-theme-snippet
```
4. 安装依赖包
```js
npm i hexo-renderer-ejs hexo-renderer-less hexo-deployer-git -S
```
5. 本地启动项目
```js
npx hexo server
```

#### 错误解决
1. 解决REFERENCEERROR: primordials is not defined
使用最新的glup相关包，并重新npm i
2. Unable to call `the return value of (posts["first"])["updated"]["toISOString"]`, which is undefined or falsey
卸载hexo-generator-sitemap, hexo-generator-feed两个插件
参考 http://mixoo.cn/2020/02/08/hexo-tempalte-render-error/
npm uninstall hexo-generator-sitemap
npm uninstall hexo-generator-feed
3. AssertionError [ERR_ASSERTION]: Task function must be specified
https://blog.csdn.net/qq_32705087/article/details/86076828