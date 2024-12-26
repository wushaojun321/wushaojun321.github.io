---
title: "Python发布自己的包到pypi"
date: 2024-12-26T11:40:26+08:00
tags:
  - Python
featured_image: ""
description: ""
---

## 为什么要打包发布

我们在日常开发中需要用到各种第三方库，帮助我们来提高开发效率，而不是重复的造轮子，并且，我们在使用第三方库的时候可能会遇到一些依赖呀，环境等问题，熟悉打包发布的流程对于我们解决这类问题很有帮助，下面我们来屡一下打包发布的流程。

## 第一版

项目结构如下：

```cmd
./
└── foo.py
```

只有一个foo.py,我们在其中实现了一个函数

```python
def hello(name):
    return "Hello %s" % name

```

我们想让老王也用我这个功能，我们可以把这个foo.py发给老王，老王把foo.py放在自己的项目中，在其他文件中直接执行

```python
from foo import hello
```

就可以使用我们的功能，但是这样的话，老王的项目中就包含了我们项目的代码，显得有些混乱，如果把我们的代码当成一个第三方库，让老王来用，就不会存在这样的问题了，python可以完成这样的操作

1. 使用setuptools打包我们的项目，

    1. 在项目根目录下创建setup.py

       ```python
       from distutils.core import setup
       
       setup(
           name='foo',
           version='1.0',
           py_modules=['foo'],
       )  
       
       ```

    2. 执行打包命令

       ```shell
       python setup.py sdist
       ```

       生成dist文件夹，文件夹中包含foo-1.0.tar.gz,我们将这个文件发送给老王

2. 安装

    1. 解压

       ```python
       tar -zxvf foo-1.0.tar.gz
       ```

    2. 执行安装命令

       ```shell
       cd foo-1.0
       python setup.py install
       ```

这样，foo模块就安装在了python专门用来存放第三方库的路径：/lib/python3.6/site-packages，当我们import foo的时候，python会在sys.path中按照顺序去搜索，而我们这个路径是在sys.path中的，所以会正常引入。所以python setup.py install 的作用就是将python包放入到site-packages中



## 第二版

第一版中存在的问题

- 我们需要将打包后的文件发送给老王，相当于只能给认识的人用，如果想让互联网上不认识的人使用，怎么办呢？

  在互联网上建立一个包管理平台，大家把包上传到这个里面，供其他人下载安装，这个平台就是pypi，我们也可以建立自己的包管理平台。

- 按照上面的搞法就实现了包的发布，下载安装，但是直接去下载，下载到本地解压，解压完之后安装，这些步骤是有些繁琐的，为了解决这个问题，就出现了用于安装第三方库的工具：easy_install,pip;还有用于发布的工具:poetry，twine

下面我们使用setuptool、twine、pip来发布一个我们自己的包

1. 创建项目，项目结构

   ```shell
   ./
   ├── evansjwu
   │   ├── __init__.py # 为空
   │   └── work.py
   └── setup.py
   ```

   setup.py内容如下

   ```python
   from setuptools import setup
   
   setup(
       name="evansjwu",
       version="0.0.1"
     	packages=["evansjwu"]
   )
   ```

2. 打包，下面命令生成的是tar.gz压缩文件，也可以指定生成其他类型的文件

   ```shell
   python setup.py sdist
   ```

3. 上传,这里我们使用pypi提供的测试环境完成，需要在test.pypi.org注册账号

   ```
   ➜  roo python -m twine upload --repository-url https://test.pypi.org/legacy/ dist/*
   Uploading distributions to https://test.pypi.org/legacy/
   Enter your username: wushaojun321
   Enter your password:
   Uploading evansjwu-0.0.1.macosx-10.9-x86_64.tar.gz
   100%|█████████████████████████████████████████████████████████████| 3.24k/3.24k [00:08<00:00, 403B/s]
   
   View at:
   https://test.pypi.org/project/evansjwu/0.0.1/
   ```

4. 在其他虚拟环境中安装、测试

   ```
   pip install -i https://test.pypi.org/simple/ evansjwu
   ```

   安装完成之后就可以在自己的项目中from evansjwu import work 使用了

至此，我们就完成了一套简单的打包，发布流程，如果要发布到正式的pypi环境，只需要替换上面的url即可。上面只是为了便于理解，简化版的，实际的工作中。遇到的远比上面的情况复杂，比如，需要指定依赖的包，需要附带一些静态的数据文件等等，这些都可以在setup.py中配置。

以下是使用setuptool.setup函数之后，打包的一些可选参数

```shell
Standard commands:
  build             build everything needed to install
  build_py          "build" pure Python modules (copy to build directory)
  build_ext         build C/C++ extensions (compile/link to build directory)
  build_clib        build C/C++ libraries used by Python extensions
  build_scripts     "build" scripts (copy and fixup #! line)
  clean             clean up temporary files from 'build' command
  install           install everything from build directory
  install_lib       install all Python modules (extensions and pure Python)
  install_headers   install C/C++ header files
  install_scripts   install scripts (Python or otherwise)
  install_data      install data files
  sdist             create a source distribution (tarball, zip file, etc.)
  register          register the distribution with the Python package index
  bdist             create a built (binary) distribution
  bdist_dumb        create a "dumb" built distribution
  bdist_rpm         create an RPM distribution
  bdist_wininst     create an executable installer for MS Windows
  check             perform some checks on the package
  upload            upload binary package to PyPI

Extra commands:
  bdist_wheel       create a wheel distribution
  alias             define a shortcut to invoke one or more commands
  bdist_egg         create an "egg" distribution
  develop           install package in 'development mode'
  dist_info         create a .dist-info directory
  easy_install      Find/get/install Python packages
  egg_info          create a distribution's .egg-info directory
  install_egg_info  Install an .egg-info directory for the package
  rotate            delete older distributions, keeping N newest files
  saveopts          save supplied options to setup.cfg or other config file
  setopt            set an option in setup.cfg or another config file
  test              run unit tests after in-place build (deprecated)
  upload_docs       Upload documentation to PyPI
  flake8            Run Flake8 on modules registered in setup.py
```

以下是setuptools.setup函数的一些可配置项

```shell
--name 包名称------------生成的egg名称
--version (-V) 包版本----生成egg包的版本号
--author 程序的作者------包的制作者名字
--author_email 程序的作者的邮箱地址
--maintainer 维护者
--maintainer_email 维护者的邮箱地址
--url 程序的官网地址
--license 程序的授权信息
--description 程序的简单描述-------程序的概要介绍
--long_description 程序的详细描述---程序的详细描述
--platforms 程序适用的软件平台列表
--classifiers 程序的所属分类列表
--keywords 程序的关键字列表
--packages 需要处理的包目录（包含__init__.py的文件夹）-------和setup.py同一目录下搜索各个含有 init.py的包
--py_modules 需要打包的python文件列表
--download_url 程序的下载地址
--cmdclass
--data_files 打包时需要打包的数据文件，如图片，配置文件等
--scripts 安装时需要执行的脚步列表
--package_dir 告诉setuptools哪些目录下的文件被映射到哪个源码包。一个例子：package_dir = {'': 'lib'}，表示“root package”中的模块都在lib 目录中。
--requires 定义依赖哪些模块
--provides定义可以为哪些模块提供依赖
--find_packages() 对于简单工程来说，手动增加packages参数很容易，刚刚我们用到了这个函数，它默认在和setup.py同一目录下搜索各个含有 init.py的包。
其实我们可以将包统一放在一个src目录中，另外，这个包内可能还有aaa.txt文件和data数据文件夹。另外，也可以排除一些特定的包
find_packages(exclude=[".tests", ".tests.", "tests.", "tests"])
--install_requires = ["requests"] 需要安装的依赖包
--entry_points 动态发现服务和插件
```

## python包管理工具的演化之路

以下工具按照时间顺序排序

1. distutils:2000年发布，能够进行python模块的安装和发布

2. setuptools和distribute，增强版的distutils，里面包含easy_install，其中distribute是setuptools的一个分支，其实是一个东西

3. eggs：一种setuptools可以识别的格式，使用setuptools可以解析，安装它

4. pip：2008年发布，easy_install 的替代品，支持更多格式的安装，包括

    - 从pypi安装，默认方式

    - 源码压缩包的方式：python setup.py sdist 产生的
    - whl格式
    - egg格式

发展趋势是：客户端使用pip，发布使用whl格式

## 总结

万变不离其宗，这东西的原理就是将一个python项目打包，发布到服务器上，其他人从服务器上下载安装使用，为了提升效率，人们开发出来一些工具，来简化中间这个操作，之前提倡eggs+easy_install,现在是pip+whl，之后可能会出现更便利的工具。
