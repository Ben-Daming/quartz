# SHELL脚本
#shell [[MS L1 Basic Shell usage]] [[MS Linux 杂项]] [[Missing semeter]] 
## shell语言的基础语法:
- $:取某变量的值
- =:赋值符号
- 空格十分关键,是用于分隔参数的标识符号,形如
	foo = bar
 的命令不会起作用,shell会将foo认作命令,"=" 和 "bar" 当作foo的参数
 >[!info] 定义字符串:纯文本字符串中"" 和''等效,但对于操作语法不一样
	 $ bar=foo
	 $ echo $bar
	 foo
	 $ echo "Value is $foo"
	 Value is bar
	 $ echo 'Value is $foo'
	 Value is $foo
by instance,可以看出''是机械引用,而""会识别字符串中语法符(可通过zsh中高亮颜色判断)
## shell脚本的基础构建
- 文件后缀名: **.sh
>[!info] 不学习直接在shell中编写.sh脚本,而是采用editor来写
	~$ code mcd.sh
		(vscode界面)
		mcd(){
			mkdir "$1"
			cd "$1"
		} //定义一个mcd函数,功能是先创建一个目录,然后直接cd到该新目录
	~$ source mcd.sh
	~$ mcd test
	~/test$
如上,我们通过mcd操作,创建并进入了test目录
- 保留关键字: \$0表示脚本的名称,\$1~\$9表示脚本所接收的1~9个参数,\$@表示所有参数
- \$\_表示上一个命令的最后一个参数,\$?表示上一个命令的错误代码
>[!info] 补充信息:stream中不仅有标准输入/出流,还有标准错误流,用于在程序编写中输出错误信息.它与标准输出流分开,以避免污染标准输出
下面这个例子是标准错误流的应用
	$ echo "HELLO"
	HELLO
	$ echo $?
	0    //打印$?输出了0,这是因为一切顺利,与C语言中return 0相同
	$ grep HELLO mcd.sh
	$ echo $?
	1    //打印$?输出了1,代表发生了一个错误,因为grep并没有在mcd.sh中搜索到HELLO

## 逻辑表达式
- "&&"和"||"和C语言中意思相同(废话,shell就是用C写的)
- true是一个空指令,不会执行任何事情,但它的\$?总是0;同理,false的\$?总是1
>[!info] 使用与/或连接两个命令时,作用与c语言中表面上有歧义,实际上还是根据第一个命令的true/false来决定是否接着执行第二条命令,考虑命令的错误流与true还是false等价
>	$ grep HELLO mcd.sh || echo "UNDONE"
>	UNDONE
>	$ grep mcd() mcd.sh && echo "DONE" 
>	DONE
>显然,SHELL与C的&&和||都是从左到右运行,且"是否继续"的标准一致.

## 命令替换
>[!info] \$()可以修改某命令的输出流,让它的输出以变量的形式存储
>	$ foo=$(echo HELLO)
>	$ echo $foo
>	HELLO

>[!info] 命令替换是一个常见的终端技术,用于在一个命令的输出中嵌套另一个命令
>	$ echo "We are in $(pwd)"
>	We are in /Users/Ben-Daming

>[!info] 这段instance信息量比较大,旨在快速过一遍.sh基础语言
>	checkit(){
>	echo "Start programme at $(date)" #date will be subtituted
>	echo "Running Programme $0 with $# arguments with pid $$"
>	# $#表示参数数目,$$表示该命令的进程id(pid)
>	for file in $@; do
>	# shell版for循环,创建file并将参数的值依次赋值给file
>	grep it $file > /dev/null 2> /dev/null
>	# 在file中搜索it
>	# >:redirect stdout 2>: redirect stderr
>	# /dev/null 是黑洞文件,自动将写入的数据丢弃
>	if [ $? != 0 ]; then #注意if与[之间必须有空格!!!
>	echo "File $file doesn't have any hi,adding one"
>	echo "# hi" >> $file
>	fi
>	done
>	}