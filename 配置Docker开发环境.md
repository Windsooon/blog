---
title: 配置Docker开发环境
date: 2017-05-27 11:28:44
categories: EngineGirls
tags: 基础 EngineGirls
---


我们开始安装一些工具来方便我们学习编程，一般初接触编程的同学使用的都是Windows系统或者macOS系统。从这节课开始我们将使用Linux系统来学习编程，不同于Windows以及macOS系统个，Linux系统是一个开源(open source)系统，也就是说它的所有源代码都可以让大家浏览以及审核，提出修改。如何在Windows系统或者macOS系统上运行Linux系统呢？我们可以使用[Docker](https://www.docker.com/)来在本地使用Linux系统

###macOS系统安装docker教程
(官方英文教程)[https://docs.docker.com/toolbox/toolbox_install_mac/]

- System Requirements: Docker for Mac will launch only if all of these requirements are met.
    
    - Mac must be a 2010 or newer model, with Intel’s hardware support for memory management unit (MMU) virtualization; i.e., Extended Page Tables (EPT) and Unrestricted Mode.
    - OS X El Capitan 10.11 and newer macOS releases are supported. At a minimum, Docker for Mac requires macOS Yosemite 10.10.3 or newer, with the caveat that going forward 10.10.x is a use-at-your-own risk proposition.
    - Starting with Docker for Mac Stable release 1.13 (upcoming), and concurrent Edge releases, we will no longer address issues specific to OS X Yosemite 10.10. In future releases, Docker for Mac could stop working on OS X Yosemite 10.10 due to the deprecated status of this OS X version. We recommend upgrading to the latest version of macOS.
At least 4GB of RAM
    - VirtualBox prior to version 4.3.30 must NOT be installed (it is incompatible with Docker for Mac). If you have a newer version of VirtualBox installed, it’s fine.

- 系统必须满足以下全部要求：
    - 2010后的苹果电脑，支持英特尔MMU内存虚拟
    - 10.10.3或者更新的苹果系统
    - 从Docker1.13版本（准备推出）之后不再支持10.10版本（OS X 10.10 Yosemite）
    - 不能安装4.3.30版本之前的VirtualBox

1. 下载软件[Docker for macOS](https://download.docker.com/mac/stable/Docker.dmg)
2. 双击Docker.dmg安装

    ![docker-app-drap](https://github.com/EngineGirl/basic-tutorial/blob/master/imgs/install_docker/docker-app-drag.png?raw=true)
3. 双击应用程序中的Docker.app来启动Docker

    ![docker-app-inapp](https://github.com/EngineGirl/basic-tutorial/blob/master/imgs/install_docker/docker-app-in-apps.png?raw=true)
4. 系统会请求你输入密码来启动Docker，Docker需要特殊权限来安装网络组件
5. 然后可以在屏幕右上角看到Docker的标志

    ![docker-app-inapp](https://github.com/EngineGirl/basic-tutorial/blob/master/imgs/install_docker/whale-in-menu-bar.png?raw=true)
6. 然后可以点击这个标志修改设置

    ![docker-app-inapp](https://github.com/EngineGirl/basic-tutorial/blob/master/imgs/install_docker/docker-menu.png?raw=true)

7. 如此简单就安装成功啦，可以吃点巧克力奖励下自己了。:D
