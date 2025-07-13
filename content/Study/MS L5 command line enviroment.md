2024-11-24 11:35

Tags:更聪明地使用shell [[Missing semeter]] [[MS Linux 杂项]]
## shell环境的信号
### type "^C"时,终端向程序发送了"SIGINT"信号
该信号表示"SIGnal INTerupt" 信号中断
### 其他需要了解的信号:
- SIGQUIT 终端上退出程序信号 "^\\"来发送 
- SIGTERM 在大多数时候等于SIGINT SIGQUIT,但不通过终端发送
- SIGNUP 挂起信号(hangUP) 当关闭正在运行进程的终端,终端会发送此信号来杀死所有进程
- SIGKILL
以上是"终止"信号
- SIGSTOP 暂停程序并放到后台 \^Z
- SIGCONT CONTinue 让暂停的程序重新执行 \
如果程序中有写处理相应信号的"办法",会优先运行程序中的method
无法被捕获:SIGKILL
SIGKILL的强制是瞬时的,这意味着主进程被kill时来不及终止子进程,从而产生孤儿进程
## 在shell中实现后台进程
- 使用jobs命令查看后台进程或者已经暂停的进程
- 使用bg %+想继续的进程序号,以在后台continue进程(background),在前台则是fg
- 在命令前写nohup (no hang up,把输出定向到nohup.out以防污染终端),再在命令后加个 &,以标注程序后台运行.这样可以实现程序的静默运行
>[!info] 这时通过简单的\^键已经无法向后台进程传信号了,使用kill可以向指定对象发送所有的信号
>	kill -STOP %1  # 把进程放到后台并暂停进程
>	bg %1 # 在后台继续运行被暂停的进程
>	fg %1 # 在前台恢复进程 foreground 
>	kill -HUP %1 # hang up第一个进程 # hang up "挂断",终止的意思
>	kill -KILL %1 # 瞬时杀进程
## 终端复用器(tmux)
### tmux的主要思想
每次运行tmux功能,先敲功能键
(tmux的功能键^B太反人类,自己改成了^X)
 三个结构层次:
1. session
	2. windows
		3. pane(面板)
### tmux多层次的使用
在原始终端输入tmux,进入一个无名session,此时与普通终端无异
命令行
- 终端中tmux new -n "name" 建立一个有名字的session
- 终端中tmux a (attach) 连接detach的session,后接 -t 指定要连的session
- 任意区tmux ls 查看已有session和window
功能键
- 功能+(下略)d(divide)分离session,回到原本终端,但session仍在运行
	- c(create)在当前session创建新window
	- p(previous) n(netxt) 数字 切换到对应窗口
		- % 纵切面板 " 横切面板
		- 箭头 在面板间移动
		- Space 将当前pane等距分布
		- 按住功能键 箭头 调整pane宽度
		- z "zoom" 扩张 当前pane扩到整个屏幕,再按一下恢复
## 别名持久化
### .file
历史原因,很多shell程序使用.xx作为配置文本
- 配置文件放在~目录中,就可以对用户生效
- 配置好后source指令加载或者重启
### 符号链接
1. 概念:配置文件全放在home下不好,需要符号链接,作用是转移读取和写入请求到另外一个文件,这样一个子目录下的文件可以遥控主目录
2. 本质:UNix创造一个空文件,这个空文件只是一个指针,向读写请求链接到真正文件
3. 语法:
- ln -s指定符号链接(symbolic link)
- ln -s \[源文件或目录] \[目标链接]
