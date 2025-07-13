# lecture1
#shell [[MS L2]] [[Missing semeter]]
## shell is the main textual interface you have to your computer.
- command:命令
- argument:参数
__argument is separated by space, when you have a space in your argument, use""or''

当你键入一个command,shell通过环境变量寻找并执行对应的程序
环境变量:系统级别的全局变量
## 路径(PATH)变量:

本质上,你每次键入程序名称,shell在计算机上遍历PATH列表,寻找目标程序或文件名称

>echo $PATH//查看路径列表
>which echo//查看echo程序所在路径

- 绝对路径:确切路径 
>pwd:print working directary 打印当前工作目录所在路径
- 相对路径:" . " 代表当前路径 " .. "代表当前路径的父路径
- " ~ "代表home目录 "/"代表根目录
- "-"代表之前所在目录,便于在两个目录中不停切换
## 标志选项
- 同一个程序往往有大量的flag和option,使用chatgpt替代help(逃)
## 学会看ls -l
ls -l 会弹出当前目录文件的具体信息,形如
![[Pasted image 20241120212911.png]]

| 权限               | 对象            | 补充信息          |     |
| :--------------- | :------------ | :------------ | --- |
| d:direction 代表目录 | 大抵分为root和其他用户 | 占用内存大小 最晚改动时间 |     |
| r:read           | "1"代表所有对象     |               |     |
| w:write          |               |               |     |
| x:execute        |               |               |     |
 _linux归一化地将所有数据看作文件,目录也是文件,存储目录中文件的信息_

**目录的权限含义与文件的权限含义不一致

对于文件,x代表可执行,r代表可读,w代表可修改
对于direction
- X:搜索权限,你若想访问某文件/目录,必须有它以及所有父目录的x权限
- r:允许看到该目录下的文件
- w:在该目录中rename,delete,create文件

## 操作文件
- mv命令有两个argument,第一个是原文件,第二个是改动后文件.mv既可以重命名,也可以移动文件路径 e.g. mv mynote.md ../food.md 将mynote重命名为food 并移动到父目录
- cp:复制粘贴目录. e.g. cp mynote.md ../food.md 在父目录中创建一个food.md,该文件内容与mynote.md一致
- rm:删除文件
- rmdir只有是空目录的时候才会执行. 使用 rmdir -r 进行递归删除
- xdg-open 采用适当的程序打开目标文件
>[!info] 当你跟着note操作到现在时,命令行已经被填满了.使用clear命令或者ctrl+l 快速清除
## Stream
- 每个程序有两个stream:输入流输出流
- 使用"<"改变输入流 ">"改变输出流(和C++)相反
>e.g. echo hello > hello.txt

改输出流进hello.txt,echo就在hello.txt中(没有会自动创建)打印hello而非命令行
">>"代表追加
## 管道
pipe就是"|"符号,它的意思是:
**取前一个命令的输出,作为后一个命令的输入**
通过管道可以实现数据流的流动传输
>tee:T形管,一个进两个出,既是argument又是command, 有两个 argument
>tee的用法很值得学习.E.g echo "hello world" | tee hello.txt
>这里的tee只有一个参数,另外一个参数默认设置为终端界面上,这样可以输入进txt的同时把结果打到终端上

## Root用户(0号用户)
root:ID=0,super user.很多时候需要超级权限
sudo:do as super
