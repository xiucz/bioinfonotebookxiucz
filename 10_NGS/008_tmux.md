输入 Tmux 前缀和一个冒号呼出命令提示行

## session
```
# 列出所有会话：
tmux ls
# 新建一个无名称的会话
tmux
# 新建一个名称为demo的会话
tmux new -s demo
# 断开当前会话，会话在后台运行
tmux detach
# 默认进入第一个会话
tmux a 
# 进入到名称为demo的会话
tmux a -t demo
 # 关闭demo会话
tmux kill-session -t demo
# 关闭服务器，所有的会话都将关闭
tmux kill-server

## window
c  创建新窗口
w  列出所有窗口
n  后一个窗口
p  前一个窗口
f  查找窗口
,  重命名当前窗口
&  关闭当前窗口

%  垂直分割
"  水平分割
o  交换窗格
x  关闭窗格
⍽  左边这个符号代表空格键 - 切换布局
q 显示每个窗格是第几个，当数字出现的时候按数字几就选中第几个窗格
{ 与上一个窗格交换位置
} 与下一个窗格交换位置
z 切换窗格最大化/最小化


输入 Tmux 前缀和一个冒号呼出命令提示行

## session

# 列出所有会话：
tmux ls
# 新建一个无名称的会话
tmux
# 新建一个名称为demo的会话
tmux new -s demo
# 断开当前会话，会话在后台运行
tmux detach
# 默认进入第一个会话
tmux a 
# 进入到名称为demo的会话
tmux a -t demo
 # 关闭demo会话
tmux kill-session -t demo
# 关闭服务器，所有的会话都将关闭
tmux kill-server

## window
# 创建新窗口
prefix + c
# 列出所有窗口 
prefix + w
# 后一个窗口
n
#前一个窗口  
p  
#重命名当前窗口
, 
关闭当前窗口 
&  

%  垂直分割
"  水平分割
o  交换窗格
x  关闭窗格
⍽  左边这个符号代表空格键 - 切换布局
q 显示每个窗格是第几个，当数字出现的时候按数字几就选中第几个窗格
{ 与上一个窗格交换位置
} 与下一个窗格交换位置
z 切换窗格最大化/最小化
```