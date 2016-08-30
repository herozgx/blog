### 下载项目到本地

```bash
git clone https://github.com/herozgx/blog.git
cd blog/theme/clean-blog
git submodule init
git submodule update
# 默认情况下clone会包含子模块的目录， 但是目录是空的， 必须通过 git submodule inist 和 git submodule update 来获取

# 还好以上可以用一条命令搞定：
git clone --recursive https://github.com/herozgx/blog.git
```

### 安装node模块
```
npm install    #根据目录中的package.json
```

### 创建新post

``` bash
$ hexo new "My New Post"
```

### 生成静态文件

``` bash
$ hexo generate
```

### 运行本地服务器

``` bash
$ hexo server
```

### 将静态文件推送到gh-pages分支

``` bash
$ hexo deploy
```
