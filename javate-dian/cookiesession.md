# Cookie

# Session

#### Cookie和Session区别

1）cookie保存在客户端，session保存在服务器端；

2）cookie可以跟踪会话，也可以保存用户喜好或者保存用户名密码；session用来跟踪会话。

3）cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑到安全应当使用session。

4）session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，考虑到减轻服务器性能方面，应当使用cookie。

#### 服务端session是如何标识已登录的用户的？

http是无状态的会话，需要基于http协议支持会话状态的机制。这时候引入session，在会话开始时，分配一个唯一的会话标识（sessionId），通过cookie把这个标识告诉浏览器，以后每次请求的时候，浏览器的cookie都会带上这个会话ID来告诉web服务器请求是属于哪个会话的。这也是为什么禁用了cookie之后，每次操作都需要先登录。在web服务器上各个会话有独立的存储，保存不同会话的信息。

**对于上万个用于已经登录了，服务端是怎么分辨每个用户的？**

1. 服务器在响应头内加上”set-Cookie:XXXXXXXXXXXXX“\(相当于一个唯一的ID符\)，此信息是服务器随机生成的，放在服务器内存里，不会重复,这就是sessionid。
2. 当浏览器得到这个sessionId会将它放在自己的进程内存里,.然后你继续发请求给这个网站的时候,浏览器就会把这个sessionId放在请求头里发送给该服务器了,这样服务器得到sessionId后再和自己内存里存放的sessionid对比锁定客户端,从而区分不同客户端,完成会话。
3. 关闭浏览器结束进程,则这个sessionid将消失,如果用户又打开浏览器想继续这次会话的时候,就会因为发送的请求中没有这个sessionid，而使服务器无法辨别请求身份。

  
@font-face{  
font-family:"Times New Roman";  
}  
  
@font-face{  
font-family:"宋体";  
}  
  
@font-face{  
font-family:"等线";  
}  
  
@font-face{  
font-family:"微软雅黑";  
}  
  
p.MsoNormal{  
mso-style-name:正文;  
mso-style-parent:"";  
margin:0pt;  
margin-bottom:.0001pt;  
font-family:'Times New Roman';  
mso-fareast-font-family:微软雅黑;  
font-size:10.5000pt;  
}  
  
h3{  
mso-style-name:"标题 3";  
mso-style-noshow:yes;  
mso-style-next:正文;  
margin-top:13.0000pt;  
margin-bottom:13.0000pt;  
page-break-after:avoid;  
mso-pagination:lines-together;  
mso-outline-level:3;  
font-family:'Times New Roman';  
mso-fareast-font-family:微软雅黑;  
font-weight:bold;  
font-size:15.0000pt;  
}  
  
p.MsoFooter{  
mso-style-name:页脚;  
mso-style-noshow:yes;  
margin:0pt;  
margin-bottom:.0001pt;  
font-family:'Times New Roman';  
mso-fareast-font-family:微软雅黑;  
font-size:10.5000pt;  
}  
  
span.msoIns{  
mso-style-type:export-only;  
mso-style-name:"";  
text-decoration:underline;  
text-underline:single;  
color:blue;  
}  
  
span.msoDel{  
mso-style-type:export-only;  
mso-style-name:"";  
text-decoration:line-through;  
color:red;  
}  
@page{mso-page-border-surround-header:no;  
	mso-page-border-surround-footer:no;}@page Section0{  
}  
div.Section0{page:Section0;}

### **session是存储在什么地方，以什么形式存储的？**

1）session变量保存在web服务器中，你不能直接修改，当然，调用程序中的setAttribute\(\)方法当然可以了。cookie存储的不是具体的数据，要不岂不是太不安全了，谁都可以修改session变量了，网站也毫无安全性可言。实际，在cookie中，存储的是一个sessionId，它标示了一个服务器中的session变量，通过这种方式，服务器就知道你到底是那个session了。所以，记住客户端只存储session标识，实际内容在网页服务器中。

2）以键值对的方式存储的

