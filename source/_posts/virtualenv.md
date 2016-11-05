---
title: Virtualenv构建Python虚拟环境
date: 2016-11-05 10:10:42
tags:
---

### 为什么要使用virtualenv
当需要在同一台主机上部署多个Python项目，而这些项目所依赖的python版本或第三方模块版本不同时， 就会出现不兼容的问题。 此时我们就可以用virtualenv来为每一个项目构建单独的python虚拟环境， 就像把每个项目分别装进独立的沙箱中， 相互不受干扰。

例如， 你有一个python项目一直跑在python2.6版本上， 突然有一天公司的服务器统一升级到了RHEL7， 它自带的python2.7或者更高级的版本(例如python3）。 为了项目依然能够正确执行， 你需要降级python版本， 但是Linux/Unix上的很多基础组件都依赖python环境， 用yum downgrade很难实现降级python（因为yum本身就依赖python）。 

再举个例子， python中存在一些第三方模块同名的问题， 比如一个项目用到了socketio，另一个项目用到了webpy-socketio， 而他们在/usr/lib/python2.7/site-packages目录中都叫做socketio, 此时就会出现软件包相互覆盖的问题。  

这些情况我们都可以用[virtualenv](https://virtualenv.pypa.io/en/stable/)工具构建独立的python执行环境来解决, 它创建一个目录，  在这个目录中来管理一套独立的python执行环境。 每个项目的python解释器，依赖包等都在独立的目录中进行管理，互不影响。

### virtualenv的安装

可以用pip方便的完成安装
```
pip install virtualenv
```

### virtualenv的使用

使用方法很简单， 只需要在virtualenv命令后跟上要创建的虚拟环境的名称， 它会自动创建一个同名目录，在该目录中初始化好python执行环境。

```
➜  ~ virtualenv venv
New python executable in /Users/gxzhao/venv/bin/python2.7
Also creating executable in /Users/gxzhao/venv/bin/python
Installing setuptools, pip, wheel...done.
```

通过上边的命令， venv这个虚拟环境就构建好了， 我们可以它包含哪些内容：

```
➜  ~ tree -L 2 venv
venv
├── bin
│   ├── activate
│   ├── activate.csh
│   ├── activate.fish
│   ├── activate_this.py
│   ├── easy_install
│   ├── easy_install-2.7
│   ├── pip
│   ├── pip2
│   ├── pip2.7
│   ├── python -> python2.7
│   ├── python-config
│   ├── python2 -> python2.7
│   ├── python2.7
│   └── wheel
├── include
│   └── python2.7 -> /usr/local/Cellar/python/2.7.12_2/Frameworks/Python.framework/Versions/2.7/include/python2.7
└── lib
    └── python2.7
```

bin目录下的activate可以激活虚拟环境， 激活后shell提示符前边会加上当前虚拟环境的名称(venv)标识我们正在使用venv这个虚拟的python环境, 此时执行的命令或项目都只依赖于venv目录下的模块。
```
➜  ~ . venv/bin/activate
(venv) ➜  ~
```

相应的， deactivate命令可以退出虚拟环境
```
(venv) ➜  ~ deactivate
➜  ~
```


### 维护多Python版本: pyenv
