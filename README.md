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

<pre>
内连接 左连接 右连接 全连接

      left join:  返回包括左表中的所有记录和右表中连接字段相等的记录
      right join :返回右表中的所有记录和左表中和连接字段相等的记录
      inner join: 只返回两个表中连接字段相等的行
      full join: 返回左右表中所有的记录和左右表中连接字段相等的记录
</pre>

![](https://i.imgur.com/AZNAwBy.png)

![](https://i.imgur.com/Z6ZQYN9.png)

<pre>
SQL注入的原理

      解决方法：
              1）永远不要信任用户的输入，要对用户的输入进行校验，能够通过正则表达式，或
                 限制长度，对单引号和双-进行转换等。
              2）永远不要使用动态拼装SQL,能够使用参数化的SQL或者直接使用存储过程进行数据
                 查询存取
              3）永远不要使用管理员权限的数据库连接，为每一个应用使用单独的权限有限的数据库连接。
              5）不要把机密信息明文存放，请加密或者hash掉password和敏感的信息
              6）应用的异常信息应该给出尽可能少的提示，最好使用自己定义的错误信息对原始
                 错误信息进行包装，把异常的信息存放在独立的表中。

      但使用正則表達式仅仅能防范一些常见或已知SQL Injection方式，并且每当发现有新的攻击方
      式时，都要对正則表達式进行改动，这但是吃力不讨好的工作。
  
      可以通过參数化存储过程进行数据查询存取。

      大家看到当我们试图在URL中嵌入恶意的SQL语句时，參数化存储过程已经帮我们校验出传递给数
      据库的变量不是整形，并且使用存储过程的优点是我们还能够非常方便地控制用户权限，我们能
      够给用户分配仅仅读或可读写权限。

      但我们想想真的有必要每一个数据库操作都定义成存储过程吗？并且那么多的存储过程也不利于
      日常的维护。           
</pre>

<pre>
10万并发的服务器应该怎么构建？

      首先要确认你的10w并发服务器，是长连接型的，如即时通讯；还是短连接型的，如网站？

      如果是短连接型，首推仍然是线程池；因为这种情况下连接会很多、切换很快，所以没必要去维护
      连接，想想银行大厅的前台窗口排队叫号，窗口忙得很如果还同时照顾几个客户那不疯了？

      如果是长连接型，首推是NIO模型；因为这种情况下保持连接很重要，但线程并不忙，想想医院住
      院部的病人，大部分时间躺在床上就行了，一个护士就可以搞定好多病人了；mina提供了比较好
      的封装。
</pre>

<pre>
缓存击穿有哪些方案解决?
</pre>

<pre>
dubbo如何一条链接并发多个调用
</pre>

<pre>
Override和Overload的区别，分别用在什么场景

      override是方法覆盖，用在父子类中，是方法名字相同，参数列表也相同，声明形式都相同，但
      是子类方法的权限不允许小于父类，不允许抛出比父类更多的异常。调用子类的方法与父类的同名
      方法无关，在子类中完全覆盖了父类的方法。

      overload是方法重载，用在同一个类中，是几个方法的名字相同，返回值相同，但是参数列表不
      同，举例来说就像构造函数，可以有多个构造函数，并且每个的参数列表都不同，这样可以用多
      种方式构造对象。
</pre>

<pre>
类序列化时类的版本号的用途，如果没有指定一个版本号，系统是怎么处理的？如果加了字段会怎么样？

      Java的序列化机制是通过在运行时判断类的serialVersionUID来验证版本一致性的。在进行反
      序列化时，JVM会把传来的字节流中的serialVersionUID与本地相应实体（类）的
      serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序
      列化版本不一致的异常。

      serialVersionUID 用来表明类的不同版本间的兼容性。有两种生成方式： 
          1) 一个是默认的1L；
          2) 另一种是根据类名、接口名、成员方法及属性等来生成一个64位的哈希字段 。 在JDK
          中，可以利用JDK的bin目录下的serialver.exe工具产生这个serialVersionUID 的值

          对于Test.class，
               执行命令： 
                       serialver Test   
               这时JVM（java虚拟机）会生成一个哈希字段。
</pre>

<pre>
InnoDB读不加锁
</pre>

<pre>
mybatis如何映射表结构
</pre>

<pre>
static加锁
</pre>

<pre>
sql语句各种条件的执行顺序，如select， where， order by， group by
</pre>

<pre>
mysql是如何实现事务的
</pre>

<pre>
hashmap如果只有一个写其他全读会出什么问题
</pre>

<pre>
如何手动触发全量回收垃圾，如何立即触发垃圾回收
</pre>

<pre>
ConcurrentHashmap的锁是如何加的？是不是分段越多越好
</pre>

<pre>
mysql的行级锁加在哪个位置
</pre>

<pre>
遍历hashmap的三种方式
</pre>

<pre>
定时器用什么做的
</pre>

<pre>
用过spring的线程池还是java的线程池？
</pre>

<pre>
IO会阻塞吗？readLine是不是阻塞的
</pre>

<pre>
spring的监听器。
</pre>

<pre>
spring的bean配置的几种方式
</pre>

<pre>
aop的底层实现，动态代理是如何动态，假如有100个对象，如何动态的为这100个对象代理
</pre>

<pre>
一万个人抢100个红包，如何实现（不用队列），如何保证2个人不能抢到同一个红包，可用分布式锁
</pre>

<pre>
线程的阻塞的方式
</pre>

<pre>
用hashmap实现redis有什么问题（死锁，死循环，可用ConcurrentHashmap）
</pre>

<pre>
nginx的请求转发算法，如何配置根据权重转发
</pre>

![](https://i.imgur.com/EMANTt2.png)

<pre>
junit用法，before,beforeClass,after, afterClass的执行顺序

     JUnit4使用Java5中的注解（annotation），以下是JUnit4常用的几个annotation： 
         @Before：初始化方法   对于每一个测试方法都要执行一次（注意与BeforeClass区别，
                 后者是对于所有方法执行一次）
         @After：释放资源  对于每一个测试方法都要执行一次（注意与AfterClass区别，后者
                 是对于所有方法执行一次）
         @Test：测试方法，在这里可以测试期望异常和超时时间 
         @Test(expected=ArithmeticException.class)检查被测方法是否抛出
              ArithmeticException异常 
         @Ignore：忽略的测试方法 
         @BeforeClass：针对所有测试，只执行一次，且必须为static void 
         @AfterClass：针对所有测试，只执行一次，且必须为static void 
          
      一个JUnit4的单元测试用例执行顺序为： 
           @BeforeClass -> @Before -> @Test -> @After -> @AfterClass; 
      每一个测试方法的调用顺序为： 
           @Before -> @Test -> @After; 
</pre>

<pre>
Mysql的三大存储引擎
</pre>

![](https://i.imgur.com/MshlYam.png)

![](https://i.imgur.com/oDCIr0U.png)

![](https://i.imgur.com/HGUsOYI.png)

![](https://i.imgur.com/cpK8UNJ.png)

![](https://i.imgur.com/ZmQi99k.png)

<pre>
redis的hash算法用的是啥？

      redis使用的consistent hashing(一致性hash算法)
</pre>

<pre>
nosql为啥比sql快？

      1) 扩展困难：由于存在类似Join这样多表查询机制，使得数据库在扩展方面很艰难;

      2) 读写慢：这种情况主要发生在数据量达到一定规模时由于关系型数据库的系统逻辑非常复杂，
                使得其非常容易发生死锁等的并发问题，所以导致其读写速度下滑非常严重;

      3) 成本高：企业级数据库的License价格很惊人，并且随着系统的规模，而不断上升;

      5) 有限的支撑容量：现有关系型解决方案还无法支撑Google这样海量的数据存储;
</pre>

<pre>
Java事件机制包括哪三个部分？
</pre>

<pre>
反射机制会不会有性能问题？

      反射大概比直接调用慢50~100倍，但是需要你在执行100万遍的时候才会有所感觉
      判断一个函数的性能，你需要把这个函数执行100万遍甚至1000万遍
      如果你只是偶尔调用一下反射，请忘记反射带来的性能影响
      如果你需要大量调用反射，请考虑缓存。
      你的编程的思想才是限制你程序性能的最主要的因素
</pre>

![](https://i.imgur.com/z7Fl5N3.png)

![](https://i.imgur.com/EhbASc2.png)

<pre>
说说http协议的工作流程。

      1 . 地址解析
       HTTP协议是通过标准URL来请求指定的服务器中指定服务的 . 一个标的 URL 如下 :
           http://www.baidu.com:80/index.html?name=tom&age=18.
 
       下面来解析一下 URL , 看看这些组成都是做什么的 :
       (1) http: 协议类型 . 这里指的是要发送的是什么协议，还可以是FTP等其他协议 . 而这
                 里请求的是服务器中的网页，所以使用的是常见的HTTP协议 .
       (2) www.baidu.com：主机名 . 通过主机名，可以准确定位到要访问的那台服务器 . 而在
             前面说的网络通信中，IP是可以唯一表示服务器地址的，但IP烦琐复杂，很难记忆，所
             以人们就想了个办法，通过熟悉的英文、数字等来表示一台服务器的地址，称为域名。这
             样就需要一个文件（作为一个数据仓库）把IP和域名一一对应起来 . 在很早的时候，
             我们确实是这么做的，不过随着IP越来越多，文件也变得越来越大，不堪负重 . 于是
             人们就想到了把这些一一对应的关系都放到一台统一的服务器上，这台服务器被称为
             DNS域名解析系统，它会把域名解析成对应的IP .
       (3) 80：端口号 . 用户已经可以通过域名或者IP访问到一台服务器了，但是一台服务器里有
             那么多的服务和应用，怎样才能准确找到用户需要访问的那个服务或应用呢？在服务器
             中，每个服务和应用都会开启一个进程，都会有一个进程号（PID），如果对外提供服
             务，则还会有一个唯一的端口号，这让外部应用可以直接通过这个端口号访问到指定的
             服务和应用。端口号的范围是0～65 535，一些常用的服务和应用都有默认的端口号，
             一般不能轻易更改，比如Web服务器的80端口、远程连接SSH服务的22端口、数据库
             MySQL的3306端口等等。因为80端口是Web服务器的默认端口，所以在写HTTP请求的
             URL的时候，80端口一般是省略的 .
       (4) index.html：请求的文件名 . 用户通过域名和端口号已经能访问到Web服务器了，接下
             来就可以通过文件名来访问指定的文件了 . Web服务器一般都做好了路由，不同的路由
             所提供的访问文件的形式可能不一样，但核心都是一样的 .
       (5)  ?name=tom&age=18：请求参数 . 即使同一个网页，可能针对不同的用户，服务器要返
             回给客户端的信息也是不一样的 . 而服务器就是通过URL中“?”后面携带的参数不同来
             响应不同的用户或者同一个用户的不同请求的 .
      2 . 封装HTTP 请求
          这一步把上面写的 URL 以及本机的一些信息封装成一个 HTTP 请求数据包
      3 . 封装 TCP 包
          第三步就是封装 TCP 包 , 建立 TCP 连接 , 也就是常说的"三次握手" . 由于HTTP位
          于最上层的应用层 , 所以HTTP在工作之前要由 TCP 和 IP 协议建立网络连接 , 这就是
          TCP/IP协议族 , 因此互联网又称为 TCP/IP 网络 .
          这里就少TCP/IP协议的"三次握手" , 首先由客户端发送建立连接的请求 , 客户端发送一
          个 syn 包 , 等待服务端的响应 ; 服务端收到 syn 包之后 , 返回给客户端一个表示确
          认的 syn 包 和 ack 包 , 最后客户端收到之后向服务端发送 ACK 包 , 发送完之后开
          始建立连接 , 如下图所示

      4 . 客户端发送请求命令
          第四步就是在连接建立之后 , 客户端发送 HTTP 请求到服务端与请求相关的信息都会包含在
          请求头和请求体中发送给服务器端 .
      5 . 服务器端响应
          服务器端在收到请求之后  , 根据客户端的请求发送给客户端相应的信息 , 相关的响应信息
          都会放在响应头和响应体中 .

    关闭连接
       服务器端在发送完响应之后 , 就会关闭连接 , 如果过客户端的请求的头部信息中有 
       Connection-alive , 那么客户端在响应完这个请求之后不会关闭连接 , 知道客户端的所有请
       求都响应完毕 , 才会关闭连接 , 这样大大节省了带宽和 IO 资源 .


    请求和响应
       HTTP请求室友两部分组成 : HTTP消息头和 HTTP消息体 . 消息头告诉服务器该请求是做什么的,
       消息体高速服务器怎么做 , 比如访问一个网页 , 头部信息可以到浏览器中的调试中心看 , 而消
       息体需要用户单机鼠标右键查看源码 , 那些HTML代码就是服务器返回给客户端的消息体 .

        1) 请求行
          请求的第一行是请求行 , 里面有请求方法 , URL , 协议版本等 , 比如上图中 , 请求的方
          法是 GET , 请求的URL是/ , 协议版本是HTTP/1.1 .

          常见的请求方式有GET 和 POST , GET 方式主要用于请求网络资源 , POST 方式主要用于表
          单提交 , 由于GET方式的参数是在地址栏中的  , 所以总是可见的 , 不是很安全 , 而且长度
          也有限制 (2048个字符) , 而 POST 方式的参数是封装成实体之后发送给服务器的 , 是不可
          见的 , 相对比较安全 , 用户的敏感信息一般采用post  方式提交 .

        2) 请求头
          每个头域都由一个头域名 , 冒号和值域组成 , 下面介绍一些最常见的头域 :
             (1) Connection : 表示是否需要持久连接 , 如果服务器看到它的值为 keep-alive ,
             或者请求协议使用的是HTTP/1.1(默认使用持久连接) , 同一个页面如果包含多个资源 ,
             则只会使用一个连接 , 如Connection : keep-alive . 如果设置了 

                 Connetion:close , 则每一个请求结束都会关闭连接 , 新的请求又会重新建立连
                           接 , 一个网页至少有几十个资源请求 , 这样会浪费带宽和时间 .
             (2) Host : 这个是必需的 , 表示请求的服务器地址是什么 , 是从URL 中提取出来的 .
                    比如 http://www.baidu.com/ 的host 就是 www.baidu.com , 这里是80端
                    口 , 默认省略 ; 如果是其他端口 , 比如 http://www.baidu.com:8080 , 
                    则Host是 www.baidu.com:8080 .
             (3) Accept : 浏览器可以接受的媒体类型(MIME) , 如 Accept:text/html 代表可
                     以接受HTML文档 , " * "代表接受任何类型 , 如 Accept:/ .
             (4) Accept-Encoding : 浏览器申明自己接受的编码方法 , 通常指定压缩方法 , 是
                     否支持压缩 , 支持什么格式的压缩 .
         3) 请求正文
            也叫请求数据 , 在使用post请求表单数据的时候 , 这些表单数据就会被放在 HTTP 请求
            的请求正文中 , 以加密的形式向服务器传输 .
</pre>

<pre>
说说Java虚拟机的生命周期及体系结构。
</pre>

<pre>
分布式系统你会考虑哪些方面？

     1) 异构性
        分布式系统由于基于不同的网络，操作系统，计算机硬件和编程语言来构造，必须要考虑一种通用
        的网络协议来屏蔽异构系统之间的差异，一般交由中间件来处理这些差异。
     2）缺乏全球时钟
        在程序需要协作时，他们通过交换消息来协调他们的动作，紧密的协调经常依赖于对程序动作发生
        时间的共识，但是实际上网络上计算机同步时钟的准确性受到极大的限制，即没有一个正确时间的
        全局概念，这是通过网络发送消息作为唯一的通信方式这一事实带来的直接结果。
     3）一致性
        数据被分散或者复制到不同的机器上，如果保证各台主机之间数据的一致性将成为一个难点。
     5）故障的独立性
        任何计算机都有可能发生故障且各种总故障不尽相同，他们之间出现故障的时间也是相互独立的，
        一般分布式系统要设计成允许部分故障，而不影响整个系统的正常使用。
     6）并发
        分布式系统的目的是更好的共享资源，那么系统中的每个资源都必须被设计成在并发环境中安全的。
     7）透明性
        分布式系统中任何组件的故障，或者主机的升级和迁移对于用户来说是透明的，不可见的。
     8）开放性
        分布式系统由不同的程序员来编写不同的组件，组件最终要集成为一个系统，那么组件锁发布的接
        口必须遵守一定的规范且能够被相互理解。
     9）安全性
        在网络上所有传递的敏感信息都要加密，拒绝服务攻击任然是一个有待解决的问题。
     10）可扩展性
        系统要设计成随着业务量的增加，相应的系统也必须要扩展来提高对应的服务。
</pre>

<pre>
什么是检查异常，不受检查异常，运行时异常？并分别举例说明。
</pre>

这是因为这两个例子都在try代码块之前就结束了temp()方法，try代码块并没有得到执行，
所以finally中的代码块也不会得到相应的执行。只有在try代码块得到执行的情况下，finally代码
块才会得到执行。
![](https://i.imgur.com/0X18WVG.png)

执行结果为：可以看到只打印了try代码块中的语句，finally代码块中的语句并没有打印，这是因
为System.exit(0);它表示退出当前Java虚拟机，一旦退出Java虚拟机，任何代码都不会再执行。
![](https://i.imgur.com/nURYpzW.png)


写程序一般都不会写System.exit(0)；来退出Java虚拟机，那么是不是不写System.exit(0)；
finally代码块就一定会得到执行呢？
可能还是会让你们失望了，答案时不一定的，这又是为什么？来看这段文字，估计你应该会明白一些。
如果当一个线程在执行 try 语句块或者 catch 语句块时被打断（interrupted）或者被终止
（killed），与其相对应的 finally 语句块可能不会执行。还有更极端的情况，就是在线程运行 
try 语句块或者 catch 语句块时，突然死机或者断电，finally 语句块肯定不会执行了。


<pre>
finally块一定会执行吗？
</pre>

<pre>
Java虚拟机中，数据类型可以分为哪几类？
      1）基本类类型 byte,short,int,long,char,float,double,Boolean,returnAddress
      2）引用类型  类类型，接口类型和数组。
</pre>

![](https://i.imgur.com/LYql4Wx.png)

<pre>
在Java中，什么是是栈的起始点，同是也是程序的起始点？

      程序要运行总是有一个起点的。同C语言一样，java中的Main就是那个起点。无论什么java
      程序，找到main就找到了程序执行的入口。
</pre>

<pre>
为什么要把堆和栈区分出来呢？栈中不是也可以存储数据吗？

      1）从软件设计的角度看，栈代表了处理逻辑，而堆代表了数据，这样分开，使得处理逻辑
         更为信息，分而治之的思想，这种隔离，模块化的思想在软件设计的方方面面都有体现。
      2）堆与栈的分离，使得堆中的内容可以被多个栈共享，这种共享的收益是很多的，一方面
         这种共享提供了一种有效的数据交互方式，另一方面，堆中的共享常量和缓存可以被所有
         线程栈访问，节省了空间。
      3）栈因为运行时的需要，比如保存系统运行的上下文，需要进行地址段的划分，由于栈
         只能向上增长，因此就会限制栈内存内容的能力，而堆不同，堆中的对象是可以根据需要
         动态增长的，因此栈和堆的拆分，使得动态增长成为可能,相应栈中只需要记录堆中的一个
         地址即可。
      5）面向对象就是堆和栈的完美结合，其实，面向对象方式的程序与以前结构化的程序在执行
         上没有任何区别，对象的数据属性存放在堆中，对象的存取逻辑放在栈中。
</pre>

<pre>
Java中的参数传递时传值呢？还是传引用？

      （1）基本数据类型传值，对形参的修改不会影响实参；
      （2）引用类型传引用，形参和实参指向同一个内存地址（同一个对象），所以对参数的修改
           会影响到实际的对象；
      （3）String, Integer, Double等immutable的类型特殊处理，可以理解为传值，最后
          的操作不会修改实参对象。
</pre>

<pre>
Java中，栈的大小通过什么参数来设置？
</pre>

<pre>
一个空Object对象的占多大空间？
</pre>

<pre>
如何解决JVM内存碎片的问题？
</pre>

<pre>
如何解决同时存在的对象创建和对象回收问题？
</pre>

<pre>
JVM中最大堆大小有没有限制
</pre>

<pre>
如何进行JVM调优？有哪些方法？
</pre>

<pre>
如何理解内存泄漏问题？有哪些情况会导致内存泄露？如何解决？
</pre>

<pre>
从分布式系统部署角度考虑，分哪几层？
</pre>

<pre>
什么是著名的拜占庭将军问题？
</pre>

<pre>
为什么说TCP/IP协议是不可靠的？
</pre>

<pre>
怎么理解强一致性、单调一致性和最终一致性？
</pre>

<pre>
分布式系统设计你会考虑哪些策略？
</pre>

<pre>
什么是Lease机制？
</pre>

