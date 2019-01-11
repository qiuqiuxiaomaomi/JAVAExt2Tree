# JAVAExt2Tree
Java高级基础技术研究


<pre>
Java中如何正确的终止线程：

      1）Thread.stop()
         一个被废弃了的方法，不被推荐使用的原因是stop方法太过于暴力，强行把执行到一半的线
         程终止，并且会立即释放这个线程所有的锁，会破坏了线程中引用对象的一致性。

         例如在数据库中维护着一张用户表，记录了用户ID和用户名，使用Thread多线程写入两条记录:

             如果在记录1写到一半的时候被stop结束了，就可能出现各种奇怪的现象：

             记录1被记录2覆盖，没有任何数据留下。

             记录1只有一半，即只有ID，而NAME为空。

      使用判断标志位的方法中断线程：
          那如果的确有中断线程的需求,我们需要怎么做呢？一般我们马上就会想到设置标志位的方
          法,即在线程中执行每一个步骤之前都判断一下是否需要退出线程:
          
          其实Thread类早就帮我们实现了这个中断标志了。与Thread中断相关的方法有下面三个:
               public void Thread.interrupt() //线程中断

               public native boolean Thread.isInterrupted() //判断是否被中断

               public static native boolean Thread.interrupted() //判断是否中断,并清除当前中断状态

          需要提醒一下的是Thread.interrupt()方法并不会像Thread.stop()方法一样立即结束
          线程,它只是设置了一个中断标志,需要在代码实现中去手动判断这个标志并且推出。

          Thread.interrupt的优点
              使用Thread.interrupt去中断线程除了可以免去自己实现标志位的烦恼之外,还可以中断sleep和wait中的线程。

              需要注意的是如果sleep方法由于中断而抛出异常，此时，它会清除中断标记。所以在
              catch到这个异常的时候需要再次设置中断标记：
</pre>