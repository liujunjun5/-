## 什么是CSP
通信共享数据，利用channel进行数据共享
## channel的底层原理是什么
### 数据结构
- cap
- size
- 环形数组
- 起始指针
- sendq
- recvq

## 向Channel发送数据的流程
检查是否有接收的节点，有直接给它  
检查是否有缓存的位置，有直接添加，没有放入sendq

## 向Channel读取数据的流程

## 从一个已经关闭的channel可以读取数据吗
可以，只要是ok=false就是有效的，channel关闭以后对发送队列的直接返回panic，防止阻塞。
对接收队列的返回值，如果没有发送的则是零值
## channel在什么情况下会内存泄漏
不关闭chanel，而且有发送的或者接收的阻塞，那么会出现g的泄漏
## select的作用
为channel服务，用来监听case后的每一个channel，如果有数据则执行对于的流程，可以用来多路复用、超时控制、非阻塞操作
## select的原理
编译时注册为case数组
快速路径：检查是否有直接运行的流程  没有在每一个channle中注册，gopark
