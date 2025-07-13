---
title: '"makefile"'
draft: false
tags:
  - basicTools
---
 
The rest of your content lives here. You can use **Markdown** here :)
# make工作流与原理
## 前言:compose && link
C语言: file.c->file.o(Unix)/file.obj(windows)->file.exe
前一个过程即编译*compose*,后一个过程即链接*link*
- compose涉及语言的转换,要求.c语法正确,变量和函数声明正确.
- link链接各个.o的函数和全局变量,需要找到有且只有一个同名函数的实现
>[!info] compose和link命令分别是:
>	gcc -c file1.o file1.c
>	gcc -o file file1.o file2.o file3.o
  
## 1.1 makefile规则
make执行时需要一个makefile
makefile规则:
	 target ... : prerequisites ...
	 recipe
- target:可以是个.o,.exe,label
- prerequisites:依赖项
- recipe:任意shell命令
功能表述:依赖项中任意一个文件改变,recipe执行
>[!info] clean作为target无依赖项
## 1.2 makefile工作流
1. make 会在当前目录下找名字叫“Makefile”或“makefile”的文件。 
2. 找到“可执行”的文件，并把这个文件作为最先执行的target。
3. 如果 可执行文件不存在，或是其依赖项的 date 要比 edit 这个文件新， 就recipe
4.  make 会在当前文件中可执行文件的依赖项作为target,，如果找到则再根据上一条规则生成 .o 文件。（递归）
5. make生成 .o 文件，再用 .o 文件生成 make 的终极任务，也就是可执行文件。
由上面的工作原理可知,makefile只靠起始点(可执行文件)和其依赖项,通过递归的方式构建项目.
因此,没有任何依赖项,也不是make起始点的clean在make过程中不会执行
## 1.3 makefile隐式规则
- 前提:compose过程一一对应,且.o名字与.c相同
- 自动推导:makefile遍历到一个whatever.o target,就可以自动推导出它的一个依赖项whatever.c,并且自动推导出recipe:cc -c whatever.c
- 这两项因此是可省略的,我们因此简化了makefile的写法
- **注意:如果我们这么写了,实际上启用了隐式规则,记得写一行 .PHONY : clean来表示clean是个伪文件** ^2898b1
## 1.4 makefile文件关系
- make默认找GNUmakefile,makefile和Makefile.其中最好用Makefile,因为其排序最靠近README等重要文件.GNUmakefile只能用GNU make识别
- 如果使用别的文件名写makefile,使用make -f \<name\>
- 使用include指令去包含别的Makefile,作用和C中是一样的,就是复制粘贴.语法:
	- include \<name\>
	- e.g. include foo.make \*.mk  $(bar)
## 1.5 make工作流
上文讲过makefile部分工作流细节,以下是make工作的执行步骤
第一阶段:
1. 读入所有makefile
2. include其他makefile
3. 初始化makefile变量
4. 推导隐式规则
5. 创建依赖关系链
第二阶段如上[[makefile#1.2 makefile工作流]]
# make的书写规则
## 2.1 约定习惯
- 必须要写clean,以便维持文件清洁
- 开头写可执行文件规则
- 结尾写clean
- recipe必须以tab键开始
## 2.2 书写规则
- 规则,即依赖关系(target + prerequisites)+recipe
- Makefile规则从上往下,第一个会被决定为最终目标,所以在开头写可执行文件
- 如果想把recipe和依赖关系写在一行,使用" ; "隔开
- 如果想换行,使用反斜杠"\\"作为换行符
## 2.3 通配符
make支持"\*,?,~"通配符.(shell中通配符)
- clean recipe写"rm \*.o,即可删除所有.o文件
## 2.4 文件搜寻
- Makefile最优先搜索的永远是当前目录
- Makefile中特殊变量VPATH.当没有指明VPATH时Makefile只会在当前目录找file
	 VPATH = src:../headers
- 上面的定义指定两个目录,分别是src和../headers.make按顺序搜索.目录按":"分割
- make关键字vpath是更灵活的功能
	vpath %.h ../headers
	- 表述:对于以.h结尾的文件(%:匹配或若干字符),在../headers中找(如果当前目录中找不到)
	vpath %.c foo
	vpath % blish
	vpath %.c bar
	- 表述:对于.c文件,查找顺序:当前目录->foo->blish->bar
	vpath %.c foo:bar
	vpath % blish
	- 表述:对于.c文件,查找顺序:当前->foo->bar->blish
## 2.5 伪目标应用
见上文[[makefile#^2898b1]].
伪目标不是一个file,所以make无法生成它的依赖关系和是否要执行.
.PHONY : clean声明伪目标
- 伪目标为开头:
	- 终极目标特性:总是被执行的
	 all : prog1 prog2 prog3
	- 该终极目标不生成文件,但会使得make递归运行prog1\2\3的目标,从而实现一个make启动了三个makefile进程
- 伪目标为依赖:
	- 伪目标和一般target一样可以作为依赖
		 .PHONY : cleanall cleanobj cleandiff
		 cleanall : cleanobj cleandiff
	如此,运行make cleanall.同时执行了cleanobj和cleandiff 