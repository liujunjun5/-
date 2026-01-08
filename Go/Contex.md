## Contex是什么？作用
Go用于上下文传递的数据结构，比如web应用的请求传递，超时控制。
实现使用了组合模式
## 种类
Empty、Cancel、timeout、value
### empty
实现的都是返回nil
是一切的根本
### cancel
带有取消功能的contex，contex作为组合节点
cancel对done channel struct{} 关闭
另外对children中的每一个子节点关闭
### timeout
分为timeout和deadline
前者是相对时间，后者是绝对时间
本质上是调用一个计时器，到了时间以后使用cancel
### value 
key、value结构
创建时放入值，查找时如果当前节点没有对应key，则递归往父节点查找
## 接口
Contex、cancer
done、timeout、value
## 和gortinue关系
gourtine负责使用和监听contex
select 
