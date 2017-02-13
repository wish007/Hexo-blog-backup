---
title: Windows下Python2和Python3共存的设置方法
categories:
  - Python
tags:
  - pip
  - easy_install
date: 2017-02-01 20:06:10
---



# 前言

之前在自己电脑上写代码、跑程序用的都是 Python3，基本不会出现必须用 Python2 的情况。然而，最近 clone 了别人的项目下来学习，发现必须用 Py2 才能正常运行，否则不断报错，而且是外部模块报的错误，调试起来实在无力，遂决定在电脑上同时装上 Py2 和Py3，顺便把过程记录下来。





# 安装Python2&3

- 分别下载 Python2 和 Python3 安装包，安装顺序没有要求，安装时选上加入`系统变量`中的`Path`选项。都安装好后检查`系统变量`中的`Path`中有没加入以下内容；没有加入，就手动添加，记得每一项都必须用 `; `分隔

  ```bash
  C:\Program Files (x86)\Python27\
  C:\Program Files (x86)\Python27\Scripts  #此目录暂时没有，安装完pip后才生成
  C:\Program Files (x86)\Python34\
  C:\Program Files (x86)\Python34\Scripts
  ```


<!--more-->


- 打开 Python2 和 Python3 的安装目录，确保`PythonX\`目录下没有同名的`python.exe`文件，可以参考以下修改

  Python27\

  ```bash
  python.exe  -->  python2.exe
  pythonw.exe  -->  pythonw2.exe
  ```

  Python34\

  ```bash
  python.exe  -->  不用修改
  pythonw.exe  -->  不用修改

  当然你也可以相应修改为 python3.exe 和 pythonw3.exe，因为我一般用 Python3，为了方便就保留默认
  ```

  这样修改后，在`cmd`中就可以直接用 `python2`运行`python2`，用 `python`运行`python3`了。

  ​


# 安装pip和easy_install


- Python3 安装包中已经包含安装 pip 的选项了，安装时选上就能自动安装好`pip`和`easy_install`，它们的运行程序保存目录在`PythonX\Scripts\ `，如果安装时忘了选上，可以参照下面 Python2 安装 pip 的方法

  ```bash
  python get-pip.py
  ```


- Python2 安装包没有包含安装 pip 的选项，可以在打开 [https://bootstrap.pypa.io/get-pip.py](https://bootstrap.pypa.io/get-pip.py)将程序右键另存为`get-pip.py`文件，记得使用管理员身份运行

  ```bash
  python2 get-pip.py
  ```


- 最后，打开`Python2X\Scripts\`和`Python3X\Scripts\`目录，确保两个目录下没有同名的`pip.exe`和`easy_install.exe`文件，可以参考以下修改

  Python27\Scripts\

  ```bash
  pip.exe  -->  pip2.exe
  easy_install.exe  -->  easy_install2.exe
  ```

  Python34\Scripts\

  ```bash
  pip.exe  -->  不用修改
  easy_install.exe  -->  不用修改

  同上，你也可以相应修改为 pip3.exe 和 easy_install3.exe，因为我一般用 Python3，为了方便就保留默认
  ```

  这样修改后，在`cmd`中就可以直接用 `pip2`运行`python2`中的`pip`，用  `pip`运行`python3`中的`pip`；`easy_install`同理。



# 安装虚拟环境virtualenv

- Python3

  ```bash
  pip install virtualenv
  ```

- Python2

  ```bash
  pip2 install virtualenv
  ```

- 因为默认安装好的`virtualenv`程序文件名为`virtualenv.exe`，显然 Python2 和 Python3 不能有同名的`virtualenv.exe`，于是可以参考以下修改

  ```bash
  Python2:
  virtualenv.exe  -->  virtualenv2.exe

  Python3:
  virtualenv.exe  -->  不用修改
  ```

  ​

# 小结

> 关键的步骤是系统变量中设置的目录下不能有同名的文件，否则系统怎么知道你要调用哪个文件，改掉同名的文件就行了：)

经过上面的设置，就可以在使用命令行的时候方便的区分 Python2 还是 Python3 了，有些项目只支持 Python2，可以创建 Python2 的虚拟环境，方便本地调试，更重要的是可以少踩一些坑(╯‵□′)╯︵┻━┻