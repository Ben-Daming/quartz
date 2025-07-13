tip: vim的核心设计哲学 [[Missing semeter]]
# model editor
vim has basic operating modes
1. normal mode "Esc"
2. insect mode "i"/"a"
3. replace mode "r"
4. visual mode "v"
5. command line mode ":"

## Vim's buffer,tabs,windows
- buffer (缓冲区)是vim打开文件的术语.
- tabs > windows,这一点与tmux相反(只是术语)
- special:缓冲区和窗口之间不一定一一对应.可以用多个窗口打开同一个文件,同步过程.便于看大文件的多个不同部分
- "q"只是关闭当前窗口,所有窗口关闭时vim才关闭使用"qa"(quit all)关闭整个tab
## Vim's movement operator(normal)
可以批处理
	- "w""b" 在单词之间快速移动(到单词首字母处,b back)
	- "e"end 到单词末尾处
	- ^u 向上滚动 ^d 向下滚动
- - "0" 行首 "$" 行尾 "^" 行的第一个非空字符 ^ac0987
- "G"移动到最末尾 gg:到最开头
- "H"highest "M" middle "L" lowest 可以翻到窗口上中下
- 查找:当前行用f +要制定的内容,全文用/,并且n是next u是up(上一个)
## Vim's combination ooperator
- d delete
- c change delete and enter editor mode
- c\d 按两次,在行单位起效

- 单个字符操作:
	- r + 字母 替换光标到字母
	- x
## Vim's edit operator
- u撤销命令 undo
	- undo 撤销上一次命令 一次insert模式被视为一次命令
- ^R redo u的逆
- 重复上一次操作: "."
- y yank=copy复制
- p paste粘贴
	- y\p 可以接入移动命令,如yy pp对行单位操作
	- 复制语法块:涉及可视模式
- visual mode
	- 按v进入可视模式,然后抓取语块
## 特殊交互:和括号交互
- i\[ inside\[ ],i(,i{同理,一律记为(
- eg: ci( 删改()内部内容 chang in ()
- a( 包括括号 all ()
- 使用%在光标所指的()左右来回跳转