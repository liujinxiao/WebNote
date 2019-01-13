## 浏览器中的Event loop 与 Node的 Event loop##
#####参考链接[https://yuchengkai.cn/docs/frontend/browser.html#poll](https://yuchengkai.cn/docs/frontend/browser.html#poll "参考链接")
###浏览器
1.  JS 是门非阻塞单线程语言，因为在最初 JS 就是为了和浏览器交互而诞生的。如果 JS 是门多线程的语言话，我们在多个线程中处理 DOM 就可能会发生问题。
2.  HTML5 标准规定setTimeout这个函数第二个参数不得小于 4 毫秒，不足会自动增加。
3.  浏览器正确的一次 Event loop 顺序是这样的
	> 执行同步代码，这属于宏任务。
	> 
	> 执行栈为空，查询是否有微任务需要执行
	> 
	> 执行所有微任务
	> 
	> 必要的话渲染 UI
	> 
	> 然后开始下一轮 Event loop，执行宏任务中的异步代码
### Node
![Node Event Loop](https://i.imgur.com/GbGfiH6.png)

#### timer
timers 阶段会执行 setTimeout 和 setInterval

一个 timer 指定的时间并不是准确时间，而是在达到这个时间后尽快执行回调，可能会因为系统正在执行别的事务而延迟。

下限的时间有一个范围：[1, 2147483647] ，如果设定的时间不在这个范围，将被设置为 1。

#### I/O
I/O 阶段会执行除了 close 事件，定时器和 setImmediate 的回调

#### idle, prepare
idle, prepare 阶段内部实现

#### poll
poll 阶段很重要，这一阶段中，系统会做两件事情

执行到点的定时器
执行 poll 队列中的事件
并且当 poll 中没有定时器的情况下，会发现以下两件事情

如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者系统限制
如果 poll 队列为空，会有两件事发生
如果有 setImmediate 需要执行，poll 阶段会停止并且进入到 check 阶段执行 setImmediate
如果没有 setImmediate 需要执行，会等待回调被加入到队列中并立即执行回调
如果有别的定时器需要被执行，会回到 timer 阶段执行回调。

#### check
check 阶段执行 setImmediate

#### close callbacks
close callbacks 阶段执行 close 事件

并且在 Node 中，有些情况下的定时器执行顺序是随机的