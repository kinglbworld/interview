虽然CGI早被业界主流淘汰，但通过学习它，可以加深WebServer通信、HTTP协议的深层认识，也算有所收获。



CGI 即 **Common Gateway Interface**，译作“**通用网关接口**”。初次听闻，略感疑惑，实则每个字眼都值得玩味。

## 1. Common

​       **通用**，是一个显著特征。虽然我们听说过Java的Servlet，Python的WSGI。但其实Java、Python都是支持CGI的，不仅如此，其他我们所熟知的语言大都也都支持。理论上来说，所有支持标准输出，支持获取环境变量的编程语言都能用来编写CGI程序。

​       在HTML诞生之后，随着网络规模的发展，动态网站成为人们的主要诉求。彼时，CGI应运而生。第一个版本的CGI由Perl语言编写的脚本，因此通常称之为“CGI脚本”。直至今日，把CGI一词丢入谷歌的搜索框，搜到的一大把都是Perl相关的内容。

*其实呢，脚本(script)并不一定就是脚本语言编写的。脚本描述的是一类程序的特征：为了完成某一任务，用程序实现批量执行一组常用逻辑的组合。凡是符合这一特征的程序都可称作脚本。当然，时至今日，脚本语言所编写的程序都可以称之为脚本程序，并且其内部逻辑也早已变得并不简单。*

如今呢，上古巫术Perl早已淡出人们视野，CGI这技术虽然通用，但是实则真正仍在应用的恐怕只有C/C++了。



## 2. Gateway

​       **网关**，网关。各位看官，不用好奇，不用疑惑。随便打开一本HTTP的书，都会提到网关的概念。通常意识中，网关一词更多的是硬件层面的概念，但其实与CGI的网关二字之含义也是不谋而合的。称CGI为软件网关也不为过。

网关，更形象的叫法是“**协议翻译机**”。通常与网关输入输出两端通信使用的是不同的协议。即一方是HTTP协议，另一方可能是其他协议，比如企业内部的自定义协议。CGI程序既是如此。

![img](https://pic1.zhimg.com/80/v2-146dd92968211c1f8ae96ad4178fa66c_720w.jpg)

​                                                 

CGI程序通常部署到Web服务器（如Apache）上，Web服务器然后调用CGI程序，关于CGI程序到底如何从Web服务器中获得输入，请继续阅读下一节 Interface。

请注意区分Web Server和后台Server。



## 3. Interface

**Interface**：Come On。又是一个被国人翻译烂掉的词汇。API（应用程序接口）的I是它，被译作“接口”。UI（用户界面）的I也是它，被译作“界面”。实际他们是同样的意思。我更喜欢“接口”一词。

## 3.1 接口协议

​       **接口**，确切而言是“接口协议”，熟悉网络的同学们，肯定都明白“**协议**”是什么。所谓协议，既是通信双方或多方都共识并遵守的一套规则。就像红灯停，绿灯行，上下车道靠右行。只有全国人民都遵守这个规则，交通才不会出什么乱子。实际上规定绿灯停，红灯行，上下车道靠左行可不可以呢？当然可以，英国日本都是靠左行的，关键是全国人民的认知要一致，这个**共识**很重要。

​       举几个栗子：TCP/IP这类二进制协议，协议内容的描述是某某字节是干嘛滴，其取值范围是什么，不同取值又是什么含义。HTTP协议是字符协议，这类协议内容的描述就是第一行是啥，第二行是啥……，应该出现啥单词，表示啥意思。

​       其实不管是TCP/IP或是HTTP，都可以统称为“网络协议”，粗浅一点理解就是“**描述报文内容详细语义的协议**”。而“接口协议”却不然，他不会定义哪些字节该写什么，也不会定义字符的内容规范。CGI其实是构架在HTTP协议之上的。它描述的是**另一个维度的共识标准**。

## 3.2 输入，输出

回顾上一节的图。Web服务器在接收到用户浏览器的HTTP请求，比如请求如下URL：

```text
http://guodongxiaren.me/cgi-bin/helloworld.cgi
```

此时在Web服务器调用helloworld.cgi之前，会把各类HTTP请求中的信息以**环境变量**的方式写入OS。CGI程序本质是OS上一个普通的可执行程序，它通过语言本身库函数来获取环境变量，从而获得数据输入。

除环境变量外，另外一个CGI程序获取数据的方式就是**标准输入（stdin）**。如post请求一个CGI的URL，那么POST的数据，CGI是通过标准输入来获取到的。



而CGI如何构造出数据（比如HTML页面）返回给浏览器呢？其实CGI本身只要向标准输出去写入数据即可。比如printf、cout，比如System.out.println，又比如print、echo等。因为Web服务器已经做了重定向，将标准输出重定向给Web服务器的与浏览器连接的socket。

此时要注意的是，不要以为返回HTML页面，那么直接输出一段HTML代码就OK，注意。此时CGI的输出承担的是HTTP协议的响应部分，因此HTTP响应报头也要自己标准输出出来。比如：

```cpp
cout<<"Content-Type:text/html\n\n"<<endl;  
```

注意最后要有两个换行符啊。不要问为什么。因为HTTP协议本身就是一个字符协议。它需要通过一个空行来区分哪里是报头，哪里是实体。在两个换行符（即一个空行）之后就可以愉快的输出HTML（即实体部分）了。熟悉PHP的同学，肯定echo过HTML代码，没错，这很像。

CGI编写Web程序虽然看似解析组装等操作十分繁琐，但其实都有很多第三方的封装来简化这些操作，高级语言的标准库基本都已经做了封装，而针对C++则有一个还不错的第三方库Cgicc。

## 4. 硬伤

既然有这么多现成的库做了封装，那么理应用CGI编写Web的也不少才对。其实不然，**这是因为CGI有一大硬伤：**

每次HTTP请求CGI，Web服务器都有启动一个新的进程去执行这个CGI程序，即颇具Unix特色的fork-and-execute。当用户请求量大的时候，这个fork-and-execute的操作会严重拖慢Web服务器的性能。

时势造英雄，FastCGI（简称FCGI）技术应运而生。简单来说，其本质就是一个常驻内存的进程池技术，由调度器负责将传递过来的CGI请求发送给处理CGI的handler进程来处理。在一个请求处理完成之后，该处理进程不销毁，继续等待下一个请求的到来。

当然FCGI其实也并不是什么惊世骇俗的创意，很容易联想到的解决思路。**资源池是后台性能优化中的常见套路**。Java发明的Servlet技术也是一种常驻内存的网关通信技术，只不过它采用的是多线程而非进程。



## 5. 争议

CGI程序有一不大不小的缺陷，缺乏URL路由的功能，基本上一个CGI都是独立提供给外界访问，一个CGI就是独立的可执行程序。因此**不仅CGI的URL比较丑陋，而且容易暴露真实路径**。

CGI一般只做上层目录的路由，而这只能交给Web服务器去配置。比如：[http://app/hello.cgi](https://link.zhihu.com/?target=http%3A//app/hello.cgi) 的app目录在物理OS的什么位置可通过Apache去配置。虽然理论上讲CGI程序也可以实现[http://app/hello.cgi](https://link.zhihu.com/?target=http%3A//app/hello.cgi)/abc/def 这种形式的路由。但是基本上没人这样做。



## 6. 展望

​       我们知道，CGI可以直接吐出一个html网页，也可以进行各种计算、逻辑处理任务。但随着各类web前后端技术的发展，以及大数据、高并发的Server使用场景越来越多。现代的CGI的用法，在发生变化。越来越多的任务从后端转移到前端，前端页面利用强大的JS承担起更多的责任。

​       CGI一般不再用于直接返回html页面，同时将复杂的计算、IO任务下沉到后端（后端可以进一步进行路由转发，实现负载均衡）。使CGI作为前后端之间的中间层。彼时CGI的职能是完成基本的**鉴权**以及**数据交换。**

​       Restful风格API的出现，让CGI获得了**续命**。CGI解析前端请求，再转发给对应后端；然后从后端取回数据，给前端返回XML或JSON。然后前端JS利用XML/JSON中的数据来进行填充。可以绘制出丰富的界面或用作他用。JS可以使用Ajax技术来向后台CGI发起数据请求。Ajax完成的是不需要刷新整个页面就可以加载后端数据（比如从数据库中取出）。

*当然实际工程应用中很少严格遵守Restful的学院派定义，比如URL资源使用名词，然后使用各个HTTP的方法（GET、POST、PUT、DELETE等）表示动词。* 

除了FCGI外，还有SCGI（Simple CGI），也是作为CGI的替代协议而产生的，但他与FCGI更像，另外呢，SCGI在每次完成HTTP应答之后都立即关闭HTTP连接，有点张小龙“**用完即走**”的意思。这个简单的协议更符合Restful API的理念需要。但是名气不大哈。



## 7. 万法归宗

​       CGI三个单词，我个人认为核心点在G（Gateway，网关）上面。在了解了CGI的基础知识之后，你会发现其他语言中都有类似概念，比如Servlet、WSGI。这些概念其实都滥觞于CGI，但是又完成了各自的进化。后续专栏文章会带你走进Servlet以及WSGI等内容。