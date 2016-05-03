title: Linux下常用软件列表
date: 2016-05-03 16:41:31
tags: 推荐
---

## 一般源码安装方法
0. configure
    可能换成cmake
    这里有很大自定义性，可以修改编译参数，也可以定义安装位置。另外，如果发现某些功能没及时打开时，可以重新configure然后编译。
    可能出现找不到系统变量的问题，所以这里的调整性也挺大的。如果怕麻烦，写个脚本解决。
1. make
    需要重新编译的话一般可以用make clean先清空旧的。
    奇葩问题比较多，手动修改源码也有可能。
2. make install
    如果是安装在root权限目录的话，需要sudo或者管理员安装。
    安装过程一般没问题，问题出现在configure和make过程，缺包或者编译出错都有可能。需要重新安装时，删掉重来，这可跟windows有很大差别。
    一般装完后还有许多环境设置。

## 系统工具
0. terminator
    增强型终端，可以代替原来的terminal
1. sudo
    修改权限的重要工具
2. ssh
    远程连接电脑必须品
3. vnc
    远程linux桌面，类似于ssh，也需要开启服务。包括vncserver、vncviewer
4. scp
    传送文件命令，更多功能自己探究
5. zsh
    可以换掉默认的bash！自己选择，配置有这个: [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)。可能不兼容bash，所以可以结合terminator使用。
6. dconf-editor
    配置工具
7. gnome-tweak-tool 
    桌面配置
8. ranger
9. gparted
    注意小心使用，毕竟是系统操作。注意拼写，别打错软件名字。

## 网络浏览
0. chrome
    如果不习惯Firefox的话，chrome是个好选择。
1. jre
    一般情况下很少用到，不同于jdk，jre是允许网页运行小java程序的java环境，以一个插件形式出现。
    可以利用这个测试：http://www.csie.ntu.edu.tw/~cjlin/libsvm/
2. goagent
    不翻墙也可以不装，其实也有别的翻墙工具。
3. flash
    安装方法各异，自行查找。
4. wget
    命令行下载工具
5. skype
6. astrill

## 开发工具
0. vim
    这个的重要性就不讨论了。如果想支持系统粘贴板，还要装一下这个：vim-gui-common
1. gcc-gdb
    写c程序必备，如果有钱，也可以买icc玩一下。
    debian系统下可以用装build-essential，安装gcc等一些开发的软件。
2. cmake
    安装某些应用程序的时候需要
3. cuda
    安装cuda，同时最大的问题是N卡驱动。
4. jdk
    可以安装官方版本，也可以安装开源版本。
5. eclipse
    不解释。
6. git
    不解释，版本控制工具，而且不仅如此简单。
7. xampp
    仅仅是为了开发环境，生产环境还需要自己另外调优，并独立安装。
8. subversion
    另一个版本控制工具。
9. understand(scitools)
    代码查看工具，需要破解。
10. openmpi
    偏向性地选择这个mpi，性能应该是intel mpi与mpich之间，据说最好是intel mpi。
11. code::blocks
    C/C++ IDE，取代VS。
12. geany
13. sublime
14. meld
15. umbrello、Dia
16. GanttProject
17. PhpStorm
18. Spyder
19. tau
    源码级别分析工具

## 办公程序
0. wine、PlayOnLinux
    作为模拟器，还是有点差别的，不建议使用。
1. unrar、unzip
    小工具，系统可能自带，也可能没有，主要也是增强对windows转移过来的文件的支持。
2. fcitx、fcitx-pinyin
    fcitx貌似比ibus更好
3. shutter、scrot
    截图工具，shutter还自带编辑！
4. clamAV
    Linux下的开源防病毒软件，`-_-|||`。
    可能需要安装clamav-update、以及clamtk，还需要自己手动编辑/etc/freshclam.conf以启用更新
5. TeamViewer
    远程控制电脑，包括手机。windows、linux、android通用！
6. stardict
    星际译王
7. mendeley
    文献管理工具！
8. FileZilla
9. LaTeX
    texlive、lyx
10. VirtualBox
    最好通过官网的方法安装，源里面的版本可能太低，有较多bug。
11. FoxitReader
    替代系统的pdf reader。
12. WPS
    ubuntu下表现良好，装上微软字体后支持不错。
13. ReText
    桌面markdown编辑工具，支持实时显示效果，ctrl+l，或者ctrl+e，快捷键显示所见即所得编辑。
14. Kazam
    屏幕录像
15. dropbox
16. RealVNC

## 娱乐工具
0. smplayer、mplayer
    貌似比VLC好多了，但VLC在ubuntu下表现良好。貌似smplayer很棒！
1. audacious
    比较好的音乐播放器，可能需要安装插件audacious-plugins-freeworld
2. eog
    看图工具！
3. Gwenview
     
## 美化工具
0. conky
    配置丰富，可以用lua做。
1. gimp
    linux下的PS
2. inkscape
    linux下的AI