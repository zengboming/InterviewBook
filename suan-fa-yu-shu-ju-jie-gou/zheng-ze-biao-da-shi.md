# **正则表达式**

**次数符号**

```
* 0或多次
+ 1或多次
？0或1次
{n} 恰n次
{n,m} 从n到m次

```

**其他符号**

符号 等价形式

```
\d		[0-9]
\D      [^0-9]  
\w 		[a-zA-Z_0-9]
\W 		[^a-zA-Z_0-9]
\s 		[\t\n\r\f]
\S 		[^\t\n\r\f]
. 		任何字符

```

**边界匹配器**

行开头 ^ 行结尾 $ 单词边界 \b

**贪婪模式**:最大长度匹配 非贪婪模式:匹配到结果就好,最短匹配

**环视**

```
字符 				描述 					匹配对象
.					单个任意字符			
[...] 				字符组 					列出的任意字符
[^...] 										未列出的任意字符
^ 					caret 					行的起始位置
$     				dollar 					行的结束位置
\
<
   										单词的起始位置
\
>
 											单词的结束位置
\b   				单词边界
\B 					非单词边界
(?=Expression)		顺序肯定环视			成功,如果右边能够匹配
(?!Expression)		顺序否定环视			成功,如果右边不能够匹配
(?
<
=Expression)		逆序肯定环视			成功,如果左边能够匹配
(?
<
!Expression) 	逆序否定环视			成功,如果左边不能够匹配

```

举例:北京市\(海淀区\)\(朝阳区\)\(西城区\)

Regex: .\*\(?=\\(\)

**模式和匹配器的典型调用次序**

1. 把正则表达式编译到模式中 Pattern p = Pattern.compile\("a\*b"\);
2. 创建给定输入与此模式的匹配器 Matcher m = p.matcher\("aaab"\);
3. 尝试将整个区域与此模式匹配 boolean b = m.matches\(\);



