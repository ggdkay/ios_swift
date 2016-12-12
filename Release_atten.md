#1 remove lib source code --> get lib/Product/
#2 Embedded Binaries/ + framework
#3 will show on Linked Frameworks and Libraries

##hide file 
mac系统如何显示和隐藏文件
> **1 苹果Mac OS X操作系统**下

> **2 隐藏Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles  NO**

> **3 输完单击Enter键，退出终端，重新启动Finder就可以了**

> **4 重启Finder**

> **5 进入文件夹查看**,删除一些文件路径（.git/.svn)

隐藏文件是否显示有很多种设置方法，最简单的要算在Mac终端输入命令。显示/隐藏Mac隐藏文件命令如下(注意其中的空格并且区分大小写)：
显示Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles -bool true

隐藏Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles -bool false


或者

显示Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles  YES

隐藏Mac隐藏文件的命令：defaults write com.apple.finder AppleShowAllFiles  NO

输完单击Enter键，退出终端，重新启动Finder就可以了

重启Finder：鼠标单击窗口左上角的苹果标志-->强制退出-->Finder-->重新启动

# OR : [http://jingyan.baidu.com/article/86fae346947c453c48121a66.html]
