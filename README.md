改造普通灯为WIFI灯
------------------------------------------------------------------------------------------------------------------------
一、项目目的
1.通过购买设备将一个普通的USB灯管改造为通过连接网络来用手机控制灯的开和关。
二、项目环境
1.ESP8266串口WiFi模块一块
2.高低电平继电器模块一个
3.普通USB灯一个
4.电源模块（可转换电压至11V/3V/5V,若是DC-DC口，需自备DC转USB线）
5.杜邦线若干（三种类型均备为好）
6.手机一台
7.Arduino IDE开发环境
8.VS code开发环境
三、项目原理
3. 1继电器工作原理
继电器（英文名称：relay）是一种电控制器件，是当输入量（激励量）的变化达到规定要求时，在电气输出电路中使被控量发生预定的阶跃变化的一种电器。它具有控制系统（又称输入回路）和被控制系统（又称输出回路）之间的互动关系。通常应用于自动化的控制电路中，它实际上是用小电流去控制大电流运作的一种“自动开关”，以保证人们不会在高压电路中因为某种原因发生危险。故在电路中起着自动调节、安全保护、转换电路等作用。

上面的是一个最普通的继电器模块引脚图，（引脚是连接到元件外的连接线）VCC表示电源正极，GND为电源负极，IN是通断信号的输入引脚；另一边的NC及常闭端，COM为公共端，NO为常开端。通过中学物理我们可以知道，开路即两端没有电流通过，闭合是指开关闭合，也就是说，在没有任何电之类的动作时，NC和COM端相当于已经连通，这种继电器模块的连线一般为

因为在一般情况下NC是闭合的，所以我们要接到NO接口，然后信号输入引脚即IN引脚给低电平，NC端断开，NO端闭合，负载端电路形成闭合回路，开始工作，当IN端给高电平时，NO端断开，负载即停止工作。如果想一直工作，给信号停止工作，即把NO端的连线接在NC端就行了。（切记：继电器的GND要和单片机的GND接在一起共地，也可以使用同一个电源来达到共地的效果）。
3.2 ESP8266-Node MCU模块原理
ESP8266一款低功耗WiFi芯片，集成完整的TCP/IP协议栈和MCU。它仅需要通过串口使用AT指令控制，就能满足大部分的网络功能需求。ESP8266是一块芯片，而Node MCU是以ESP8266为核心的开发板。

 Node MCU的两排插针和ESP8266芯片引脚相连，方便实验使用，同时，NodeMCU开发板上还配有USB接口以及电压转换电路。我们只需要利用一根USB数据线即可对开发板进行操作。
ESP8266 支持 softAP 模式，station 模式softAP + station 共存模式三种。
利用 ESP8266-Node MCU模块可以实现十分灵活的组网方式和网络拓扑。Soft AP即无线接入点，是一个无线网络的中心节点。通常使用的无线路由器就是一个无线接入点；Station即无线终端，是一个无线网络的终端端，SoftAP+Station共存模式：ESP8266 支持 softAP+station 共存的模式，用户设备、手机等可以作为station 连入 ESP8266 的 softAP 接口，同时，可以控制 ESP8266 的 station接口通过路由器（AP）连入 internet，这也是此次实验该模块的主要作用。实验中利用Arduino IDE开发环境，直接编写固件，编译之后上传到ESP8266中，实现物联网功能。
3.3 HTTP/HTTPS服务器程序原理
超文本传输协议（HTTP：Hypertext TransportProtocol）是万维网应用层的协议，通过两个程序实现：一个是客户端程序（各种浏览器），另一个是服务器（常称Web服务器）。这两个通常运行在不同的主机上，通过交换报文来完成网页请求和响应，报文可简单分为请求报文和响应报文。
客户机与服务器建立连接后，浏览器可以向web服务器发送请求并显示收到的网页，当用户在浏览器地址栏中输入一个URL或点击一个超连接时，浏览器就向服务器发出了HTTP请求，请求方式的格式为：统一资源标识符、协议版本号，后边是MIME（Multipurpose Internet  Mail Extensions）信息包括请求修饰符、客户机信息和可能的内容。该请求被送往由URL指定的WEB服务器，WEB服务器接收到请求后，进行相应反映，其格式为：一个状态行包括信息的协议版本号、一个成功或错误的代码，后边服务器信息、实体信息和可能的内容。即以HTTP规定的格式送回所要求的文件或其他相关信息，再由用户计算机上的浏览器负责解释和显示。
在本次实验中，客户端ESP8266将会实时检测板上的按键状态，并且把按键状态发送给服务器。服务器在接收到客户端按键状态后，可以根据客户端按键状态来控制服务器端板上的LED点亮和熄灭。最终实现的效果是，我们可以通过客户端ESP8266开发板上的按键来“遥控”服务器上的LED点亮和熄灭。
四、项目步骤与结果
4.1 安装驱动模块
   到网上查找关于ESP8266模块连接电脑的驱动进行安装，打开下面两个exe程序进行驱动的安装，提示出现启动已安装即安装驱动成功，然后查看设备管理器中的端口查看，显示CH340端口即表示成功连接。


4.2 根据电路原理图，连接硬件设备

USB灯截断，剪开里面两条线，不传输数据的数据线只有两条线，分别为红色和黑色，红色为正极，黑色为负极，将红色端的线与电源正极用杜邦线连接起来，黑色端的与继电器的公共端连接起来，开发板连接继电器的触发正负极，完成整个电路的连通。


4.3 配置继电器
继电器模式选择延时模式，并设置编号0000，设置继电器为高电平接通，低电平断开。通过继电器的1号按键设F--0模式为--00，F--1模式下为0000。


4.4 通过Arduino为NodeMCU编写程序
（1）到Arduino官网下载合适自己版本的Arduino IDE，下载完毕后，在页面点击工具栏选择连接CH340的串口Com 7（不同设备端口号可能不同）。
（2）设置首选项，在软件中点击文件进入首选项，将带有ESP8266库的网址输入附加开发管理器网址中，然后在工具栏进入开发管理器安装esp8266库。


（3）下载Arduino Uno WiFi Dev Ed Library库，并安装
（4） Arduino代码页面引入库
    \#include \<ESP8266WiFi.h\> // 本程序使用 ESP8266WiFi库

    \#include \<ESP8266WiFiMulti.h\> // ESP8266WiFiMulti库

    \#include \<ESP8266WebServer.h\> // ESP8266WebServer库

    ESP8266WiFiMulti wifiMulti; //
    建立ESP8266WiFiMulti对象,对象名称是'wifiMulti'

    ESP8266WebServer esp8266_server(80);

    // 建立SP8266WebServer对象，对象名称为esp8266_server

    // 括号中的数字是网路服务器响应http请求的端口号

    // 网络服务器标准http端口号为80，使用80为端口号
（5）设置初始化
    // 开灯
    String openh= “xxx”;
 // 关灯
    String closeh= “xxx”;
    void setup(void)
    {
    Serial.begin(115200); // 启动串口通讯，串口监视器的频率也要相同，不然会出现乱码
    pinMode(05, OUTPUT); // D1
    digitalWrite(05, HIGH);//本来就是high，其他两个是相反的
    //通过addAp函数存储 WiFi名称 WiFi密码
    wifiMulti.addAP("HONOR V30", "11111111");
    // 这里可以存入手机热点名及密码
    }
    digitalWrite(2, HIGH)
    while (wifiMulti.run() != WL_CONNECTED)
    {
    //
    此处的wifiMulti.run()是重点。通过wifiMulti.run()，NodeMCU将会在当前环境中搜索addAP函数所存储的WiFi。如果搜到多个存储的WiFi那么NodeMCU将会连接信号最强的那一个WiFi信号。
    delay(1000);
    Serial.print(i++);
    Serial.print(' ');
    }
    //
    一旦连接WiFI成功，wifiMulti.run()将会返回“WL_CONNECTED”。这也是digitalWrite(2, LOW); // 此处while循环判断是否跳出循环的条件。
（6）启动网络服务功能
    esp8266_server.begin();
    esp8266_server.on("/", handleRoot);
    //设置服务器根目录为/ 访问时调用handleroot
    esp8266_server.on("/pin", HTTP_GET, pin);
    //访问pin时 模式为传参模式 调用pin
    esp8266_server.onNotFound(handleNotFound);
    //处理访问不存在位置时 调用Notfound
     （7）处理网站根目录“/”的访问请求
void handleRoot()
{                                                //处理网站根目录“/”的访问请求
    esp8266_server.send(200, "text/html", openh); // NodeMCU将调用此函数。
}

// 设置处理404情况的函数'handleNotFound'
void handleNotFound()
{                                                             // 当浏览器请求的网络资源无法在服务器找到时，
    esp8266_server.send(404, "text/plain", "404: Not found"); // NodeMCU将调用此函数。
}
（8）设置访问端口
    void handleLEN()//访问pin
{
    if (esp8266_server.arg("light1") == "1")
    {
digitalWrite(05, LOW); // D1 ，05对应开发板中的D1插针           
    
        esp8266_server.send(200, "text/html", closeh);
    } // 2端口
    else if (esp8266_server.arg("light1") == "0")
    {
        digitalWrite(05, HIGH);        
        esp8266_server.send(200, "text/html", openh);
    }
}
（9）运行程序
将代码编写无误后，点击编译项目并上传无误后，打开，串口监视器，查看连接成功后得到的wifi的IP地址及产生的网址，进去网址即可控制灯的开启和关闭。



详细代码链接：
演示视频链接：https://www.bilibili.com/video/BV1So4y1L7Rm/?buvid=XYF38DAFAEF5528C434EE9830E48D662C334D&is_story_h5=false&mid=sGLMJWY2e6AnwPsMW1Cv3Q%3D%3D&plat_id=462&share_from=ugc&share_medium=android&share_plat=android&share_source=WEIXIN&share_tag=s_i&timestamp=1681658188&unique_k=i6zcA17&up_id=688171816&vd_source=a366456f0bf551dc545ed991d6a7ddb5

五、项目总结
5.1 实验结果评价
   本次实验涉及的物联网技术并没有怎么接触过，对于相关技术没有什么知识基础，但通过在网上进行学习搜索相关文章，从网上学习一些样例，对这些内容融会贯通，最终完成了该项目，实验中完成了搭建服务器，响应网页，并且构建出了基本的网页交互效果，通过高电平低电平响应完成了wifi灯的开与关操作，完成了实验的要求。
5.2 实验中遇到的问题的分析与处理
1.电路连接
物理知识尚缺，并且对相关技术了解很少，通过在网上查找该项目相关的文章，才完成电路的连接。
2.Arduino库的引入
     一开始去官网下载了最新版的Arduino，然后根据网上教程下一些相关库文件，然而在附加网页地址输入esp8266的文件地址后，在管理库中installesp8266时一直显示在下载但是一直卡着，然后在在代码中导入相关库尝试运行，发现根本没有ESPWIFI这个库文件。然后线是将软件卸载后下载3.0之前的版本，并且，在下载过程中如果有报错哪个下载步骤出错就单独去下载这个文件在将其导入到相关文件夹中即可。
3.控制开灯的方式
本次实验采用的是连接同一WiFi，连接手机的移动热点，使其处于同一网络下实现浏览器访问网页进行控制。也可采用websocket连接电脑与esp8266模块，将ESP8266作为服务器，电脑为client，连接AP模式发出的WiFi，在同一WLAN下向ESP8266模块发送指令控制开关灯，但此种方法需要编写APP，较为困难，且每次连接都需下载APP或打开idex.html文件，所以并未采用。
