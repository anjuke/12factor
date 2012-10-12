## VIII. 并发
### 通过进程模型进行扩展

任何计算机程序，一旦启动，就会生成一个或多个进程。互联网应用采用多种进程运行方式。例如，PHP进程作为Apache的子进程存在，随请求按需启动。Java进程则采取了相反的方式，在程序启动之初JVM就提供了一个超级进程储备了大量的系统资源(CPU和内存)，并通过多线程实现内部的并发管理。上述2个例子中，进程是开发人员可以操作的最小单位。

![扩展表现为运行中的进程，工作多样性表现为进程类型。](/images/process-types.png)

**在12-factor应用中，进程是一等公民。** 12-factor应用的进程主要借鉴于 [unix进程模型](http://adam.heroku.com/past/2011/5/9/applying_the_unix_process_model_to_web_apps/) 。开发人员可以运用这个模型去设计应用架构，将不同的工作分配给不同的 *进程类型* 。例如，HTTP请求可以交给web进程来处理，而常驻的后台工作则交由worker进程负责。

这并不表示应用不能通过单个进程来处理并发，如使用VM运行时的线程机制，或是由[EventMachine](http://rubyeventmachine.com/), [Twisted](http://twistedmatrix.com/trac/),  [Node.js](http://nodejs.org/) 等工具提供的异步/事件驱动模型。但是，单个VM的垂直扩展能力是有限的，所以应用必须能够扩展到多台物理机器上运行。

在需要对系统进行扩展时，进程模型的作用会大放异彩。 [12-factor应用的进程所具备的无共享，水平分区的特性](/processes) 意味着增加并发处理能力会是一项简单而稳妥的操作。这些进程的类型以及每个类型中进程的数量就被称作 *进程构成* 。

12-factor应用 [不需要作为守护进程启动](http://dustin.github.com/2010/02/28/running-processes.html) 或是写入PID文件。相反的，应该借助操作系统的进程管理器(例如 [Upstart](http://upstart.ubuntu.com/) ，分布式的进程管理云平台，或在开发环境中使用类似 [Foreman](http://blog.daviddollar.org/2011/05/06/introducing-foreman.html) 的工具)，来管理 [输出流](/logs) ，应对进程崩溃，以及处理用户触发的重启和关闭操作。
