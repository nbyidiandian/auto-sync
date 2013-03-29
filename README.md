# auto-sync for emacs

### 概述
该脚本用于在emacs里编辑文件保存后，自动将该文件所在项目同步到另外一台机器。

### 工作原理
首先用户需要定义一个项目目录，使用auto-sync就会在用户每次使用emacs修改后，自动将修改同步到另外一台机器的指定目录。

当用户使用C-X C-S时，触发save-buffer命令。在save-buffer完成后，会调用auto-sync。

auto-sync首先在当前目录查找名为.sync的文件，如果找不到则在当前目录上一层（父类目）里找，一直递归。当找到.sync文件时，则把.sync文件所在目录作为项目目录，同时执行.sync文件（要求.sync需要有可执行权限）。

用户在.sync里可以使用rsync等工具来做同步并定义目标机器和目标目录。

### 应用场景
本地开发，同步到服务器编译。本地和服务器上都会有一份最新的代码。编辑完成后切换到服务器上编译。当服务器出故障时，仍然可以在本地进行编辑2. 

### 使用说明

<li> 将auto-sync目录copy至~/.emacs.d/plugins目录
<li> 将以下代码增加到~/.emacs文件里

```
;; auto sync project
(add-to-list 'load-path "~/.emacs.d/plugins/auto-sync/")
(require 'auto-sync)
(auto-sync-after-save-buffer-done)
```
<li> 定义一个项目目录，在项目目录下touch一个.sync文件，并写入以下内容

```
#!/bin/sh
cd `dirname $0`
# 将下面的$TARGET_HOST替换为同步的目标机器，$TARGET_DIRECTORY替换为同步的目标目录
rsync -arvzc ./ $TARGET_HOST:$TARGET_DIRECTORY/
```
