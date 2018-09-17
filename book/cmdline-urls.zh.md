
## 网址

卷曲被称为卷曲,因为它的名称中的子字符串是URL(统一资源定位器).它运行在URL上.URL是我们随意用于web地址字符串的名称,就像我们通常看到的以http\://或以www开头的前缀.

严格来说,URL是前者的名称.URI(统一资源标识符)是它们更为现代和正确的名称.它们的语法被定义为[RFC 3986](https://www.ietf.org/rfc/rfc3986.txt).

如果卷曲接受"URL"作为输入,那么它实际上是一个"URI".curl理解的大多数协议还具有相应的URI语法文档,该文档描述了特定URI格式的工作原理.

curl假设您给它一个有效的URL,并且它只对格式进行有限的检查,以便提取它认为执行操作所必需的信息.例如,您可以在URL中传递非法字符,而不需要卷发注意或关心,并且它只会传递它们.

### 方案

URL从"方案"开始,这是"http\://]部分的正式名称.它告诉URL使用哪个协议.该方案必须是已知的,这个版本的卷曲支持或将显示一个错误信息和停止.此外,该方案既不能启动也不包含任何空白.

### 方案分离器

方案标识符由"//"序列与URL的其余部分分隔开.这是一个冒号和两个前斜杠.存在只有一个斜杠的URL格式,但CURL不支持其中任何一个.关于斜杠的数量还有两个需要注意的事项:

curl允许一些非法的语法,并试图在内部对其进行纠正;因此它也可以理解并接受具有一个或三个斜杠的URL,即使它们实际上不是正确格式的URL.CURL之所以这样做,是因为浏览器开始了这个练习,所以它经常导致这样的URL在野外被使用.

`file://`URL被写为`file://<hostname>/<path>`但是唯一可以使用的主机名是`localhost`,`127.0.0.1`或者一个空白(什么都没有):

```
file://localhost/path/to/file
file://127.0.0.1/path/to/file
file:///path/to/file
```

插入任何其他主机名将使CURL的最新版本返回错误.

特别注意上面的第三个例子(`file:///path/to/file`)那就是*三*路径前的斜杠.这也是一个常见的错误区域,浏览器允许用户使用错误的语法,作为特殊例外,Windows上的curl也允许这种不正确的格式:

```
file://X:/path/to/file
```

…x是Windows样式的驱动器字母.

### 无计划

为方便起见,CURL还允许用户从URL中删除方案部分.然后根据主机名的第一部分猜测使用哪个协议.这种猜测是非常基本的,因为它只是检查主机名的第一部分是否与一组协议之一匹配,并假设您打算使用该协议.这种启发式是基于这样一个事实,即服务器通常被命名为这样.以这种方式检测的协议是FTP、DICT、LDAP、IMAP、SMTP和POP3.在无方案URL中的任何其他主机名都会使CURL默认为HTTP.

您可以将默认协议修改为HTTP以外的其他内容.`--proto-default`选择权.

### 姓名和密码

在该方案之后,可以嵌入一个可能的用户名和密码.这种语法的使用通常在这些日子里是不赞成的,因为您很容易在脚本或其他脚本中泄漏这些信息.例如,使用给定的名称和密码列出FTP服务器的目录:

```
curl ftp://user:password@example.com/
```

URL中的用户名和密码的存在完全是可选的.CURL还允许在URL之外提供正常命令行选项的信息.

### 主机名或地址

URL的主机名部分,当然,只是一个名字,可以解析为数字IP地址,或数值地址本身.在指定数字地址时,使用点式版本的IPv4地址:

```
curl http://127.0.0.1/
```

对于IPv6地址,数字版本需要在方括号内:

```
curl http://[::1]/
```

当使用主机名时,通常使用系统的解析器函数完成名称到IP地址的转换.通常允许系统管理员在本地提供本地名称查找.`/etc/hosts`文件(或等效文件).

### 端口号

每个协议都有一个CURL将使用的"默认端口",除非指定了端口号.可选的端口号可以是主机名的一部分后,在提供的网址,如结肠癌和写在小数的端口号.例如,在端口8080上请求HTTP文档:

```
curl http://example.com:8080/
```

名称指定为IPv4地址:

```
curl http://127.0.0.1:8080/
```

以IPv6地址命名:

```
curl http://[fdea::1]:8080/
```

### 路径

每个URL都包含一个路径.如果没有给出,则暗示"/".路径被发送到指定的服务器,以准确地确定请求或将提供哪些资源.

路径的精确使用是依赖于协议的.例如,从默认的匿名用户从FTP服务器获取文件ReMe:

```
curl ftp://ftp.example.com/README
```

对于具有目录概念的协议,用尾随斜杠结束URL意味着它是目录而不是文件.因此,从FTP服务器请求目录列表是用这样的斜线来暗示的:

```
curl ftp://ftp.example.com/tmp/
```

### FTP型

这不是一个被广泛使用的特征.

标识FTP服务器上的文件的URL有一个特殊的特性,允许您还告诉客户端(本例中为curl)资源是哪种文件类型.这是因为FTP有点特殊,可以改变传输的模式,因此处理文件的方式不同于使用其他模式.

通过向URL追加";type =",告诉CURL FTP资源是ASCII类型.使用ASCII从示例.com的根目录获取"Foo"文件,然后可以用:

```
curl "ftp://example.com/foo;type=A"
```

虽然卷曲默认为FTP的二进制传输,但URL格式也允许您指定类型为i=i的二进制类型:

```
curl "ftp://example.com/foo;type=I"
```

最后,您可以告诉CURL,如果您传递的类型是D,则标识的资源是目录:

```
curl "ftp://example.com/foo;type=D"
```

……然后,它可以作为另一种格式工作,而不是用上面提到的尾随斜线来结束路径.

### 破片

URL提供了一个"片段部分".这通常被看作是一个哈希符号(α)和一个特定名称的名字在一个网页中的浏览器.当URL被传递给curl时,curl可以很好地支持片段,但是片段部分从来没有通过连线发送,因此不管是否存在,它对curl的操作没有影响.

### 浏览器的"地址栏"

重要的是要认识到,当你使用现代网络浏览器时,他们往往在主窗口顶部的"地址栏"没有使用"URL"甚至"URI".实际上,它们主要使用IRI,它是URI的超集,允许国际化,比如非拉丁符号等等,但它通常也超出了这个范围,因为它们倾向于处理空间,并以上述规范中没有提到的方式对百分比编码进行神奇的处理.客户应该这么做.

地址栏是一个简单的界面,供人输入并查看URI类字符串.

有时候,你在浏览器地址栏中看到的和你可以传递给卷发的区别是很重要的.

## 许多选项和URL

如上所述,CURL支持数以百计的命令行选项,并且它还支持无限数量的URL.如果你的shell或命令行系统支持它,那么你可以把命令行传递给CURL的时间是没有限制的.

curl将首先解析整个命令行,应用所使用的命令行选项的愿望,然后逐个遍历URL(按照从左到右的顺序)来执行操作.

对于一些选项(例如)`-o`或`-O`它告诉CURL在哪里存储传输,您可能想为命令行上的每个URL指定一个选项.

CURL将返回其在最后一个URL上的操作的退出代码.相反,如果希望CURL退出失败的集合中的第一个URL上的错误,请使用`--fail-early`选择权.

## 每个URL的独立选项

在前面的小节中,我们描述了curl如何总是解析整个命令行中的所有选项,并将这些选项应用于它传输的所有URL.

这是一个简化:curl还提供了一个选项(-;,--next),该选项在一组选项和URL之间插入某种边界,用于应用这些选项.当命令行解析器找到下一个选项时,它会将下列选项应用到下一组URL.下一个选项因此起作用.*除法器*在一组选项和URL之间.你可以尽可能多地使用下一个选项.

作为一个示例,我们对一个URL执行HTTP GET并遵循重定向,然后对另一个URL执行第二个HTTP POST,然后用HEAD请求将其循环到第三个URL.所有在一条命令行中:

```
curl --location http://example.com/1 --next
  --data sendthis http://example.com/2 --next
  --head http://example.com/3
```

尝试这样的事情*没有*命令行上的--下一个选项将生成一个非法命令行,因为curl将尝试组合POST和HEAD:

```
Warning: You can only select one HTTP request method! You asked for both POST
Warning: (-d, --data) and HEAD (-I, --head).
```

## 连接重用

建立TCP连接,尤其是TLS连接可能是一个缓慢的过程,即使在高带宽网络上也是如此.

记住curl在内部有一个连接池,它使之前使用的连接保持活跃,并在使用连接之后保持一段时间,以便对相同主机的后续请求可以重用已经建立的连接,这一点是很有用的.

当然,它们只能在运行curl工具时保持活动状态,但是尝试在同一命令行内完成几个传输而不是运行几个独立的curl命令行调用,这是一个很好的理由.