---
layout: post
title:  "ctf-Web_php_unserialize反序列化漏洞"
categories: [ctf]
tags: [php]
---



# 一.根据题目猜测和反序列化漏洞有关

　　**1.思路：**

　　进入实验环境后，发现是一段php代码，通过接受get请求方式将提交过来的var的值，

　　先进行base64解码，再进行正则的判断，如果不匹配成功就会反序列化输入的参数值。

　　所以这里我们目的就是为了绕过正则表达式，让其反序列化出我们要的fl4g.php，再通过析构函数，高亮出fl4g文件中的内容。

　　2.步骤：

　　通过代码中的注释我们发现flag在fl4g.php这个文件里面，所以我们要将其序列化

　　![img](https://img-blog.csdnimg.cn/img_convert/4c2d979433165720387874bdc1560154.png)

 

 　编写一段代码先实例化我们要序列化的类，这里吧fl4g.php传进去，然后通过构造方法将其赋值给$file，

　　然后我们输出序列化的结果，这里为了绕过正则可以采用将  **:4:**  替换为 **:+4:,**

　　在 PHP5 < 5.6.25， PHP7 < 7.0.10 的版本存在wakeup的漏洞。当反序列化中object的个数和之前的个数不等时，wakeup就会被绕过。**
**

　　所以我再将它的  **:1:**  替换为一个大点的值 比如 **:2:   ,**

　　因为在接受到var值后会对其base64解码，所以这里我们再将其base64编码，然后输出即得到我们要输入的参数值。

　　TzorNDoiRGVtbyI6Mjp7czoxMDoiAERlbW8AZmlsZSI7czo4OiJmbDRnLnBocCI7fQ== ，得到flag。

　　**这里我们要注意：这里的file变量为私有变量（protected变量应该也会），所以序列化之后的字符串开头结尾各有一个空白字符（即%00），

　　字符串长度也比实际长度大2，如果将序列化结果复制到在线的base64网站进行编码可能就会丢掉空白字符，从而得到错误的编码值

　　如下图，通过strlen求到序列化的长度为48，但实际我数了一下只有46。

　　![img](https://img-blog.csdnimg.cn/img_convert/581ebf1ba84cb4f66df8eaa80838e5ea.png)

 

 

　　![img](https://img-blog.csdnimg.cn/img_convert/ec318421d9626cc2eed22926f1910c5b.png)

 



 

 

 

 

　　![img](https://img-blog.csdnimg.cn/img_convert/19a2e728cdb843b413b7ab3aa97360a2.png)