# MVVM
MVVM通常是通过数据改变来驱动的，这样就需要进行数据的双向绑定。一般若要根据View层的变化来改变Model，是通过一些特殊元素（例如：input、select、textarea等元素）的onchange事件来触发修改JavaScript中的ViewModel对象数据的内容来实现的，这一点比较容易理解。另一方面是ViewModel修改，如何触发View自动更新或重渲染呢？

这种根据数据的变化来自动触发其他操作的机制就是我们说的数据变更检测，实现数据变更检测的办法主要有手动触发绑定、脏数据检测、对象劫持、Proxy等。

# 数据变更检测示例
## 手动触发绑定
主要思路是在数据对象上定义set()方法和get()方法，调用时手动触发get()或set()函数来获取、修改数据，改变数据后主动触发get()和set()函数中View层的重新渲染功能。


## 脏检测机制
以典型的MVVM框架Angularjs为例，Angularjs是通过检查脏数据来进行View层操作更新的。

脏检测的基本原理是在ViewModel对象的某个属性值发生变化时找到与这个属性值相关的所有元素，然后再比较数据变化，如果变化则进行Directive指令调用，对这个元素进行重新扫描渲染。
## 前端数据对象劫持
数据劫持是目前使用比较广泛的方式。其基本思路是使用Object.defineProperty和Object.defineProperties对ViewModel数据对象进行属性get()和set()的监听，当有数据读取和赋值时则扫描元素节点，运行指定对应节点的Directive指令，这样ViewModel使用通用的等号赋值就可以了。
## Proxy
Proxy可以用于再已有对象基础上重新定义一个对象，并重新定义对下个原型上的方法，包括get()和set()方法。