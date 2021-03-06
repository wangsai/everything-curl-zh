
# HTTP重定向

"重定向"是HTTP协议的基础部分.这个概念是存在的,并且已经在1996年发布的第一个规范(RFC 1945)中有了文档,从那时起它一直被很好地使用.

重定向就是它听起来的样子.它是服务器向客户端发送指令,而不是返回客户想要的内容.服务器基本上说:"去看看."*在这里*而不是你所要求的.

但并非所有重定向都是相同的.重定向有多持久?客户端在下一个请求中应该使用什么请求方法?

所有重定向也需要发送回A.`Location:`用新URI请求的头,它可以是绝对的或相对的.

## 永久的和暂时的

重定向是否意味着持续或现在仍然有效?如果你想让用户永久地用另一个GET将用户重定向到资源B,请发送一个301.它还意味着用户代理(浏览器)应该缓存它,并且从现在起在请求原始URI时继续使用新的URI.

临时替代方案是302.现在,服务器希望客户端向B发送GET请求,但是它不应该缓存它,而是在下一次指向它时继续尝试原始URI.

注意,301和302都将使浏览器在下一个请求中执行GET,这可能意味着如果方法以POST开始(并且仅当POST开始)则更改该方法.这种将HTTP方法更改为GET以用于301和302的响应据说是"出于历史原因",但是浏览器仍然这样做,所以大多数公共web将采用这种方式.

在实践中,303代码非常类似于302.它不会被缓存,它会使客户端在下一个请求中发出一个get.302和303之间的区别是微妙的,但是303似乎更适合于对原始请求的"间接响应",而不仅仅是重定向.

这三个代码是HTTP/1规范中唯一的重定向代码.

但是,curl根本不记得或缓存任何重定向,所以对于它,永久重定向和临时重定向之间实际上没有区别.

## 告诉CURL遵循重定向

在CURL只做基础知识的传统中,除非你用不同的方式进行说明,否则它不遵循默认的HTTP重定向.使用`-L, --location`告诉它做那件事.

当启用重定向后,CURL将默认遵循50重定向.最大限度的限制主要是为了避免陷入无穷无尽的循环中.如果50对您来说不够,则可以更改重定向的最大数目以跟随`--max-redirs`选择权.

## 得到还是邮寄?

所有这些响应代码301和302/303都假定客户端发送GET以获得新的URI,即使客户端在第一个请求中可能已经发送了POST.这是非常重要的,至少如果你做一些不使用GET的事情.

相反,如果服务器希望将客户端重定向到一个新的URI,并且希望它在第二个请求中发送与第一个请求相同的方法,就像如果它第一次发送POST它希望它在下一个请求中再次发送POST一样,服务器将使用不同的响应代码.

为了告诉客户端"您发送了POST的URI被永久重定向到B,您现在和将来都应该发送POST",服务器用308进行响应.为了使问题复杂化,308代码只是最近定义的.[规格](https://tools.ietf.org/html/rfc7238#section-3)在2014年6月出版,所以老客户可能不会正确对待它!如果是这样,那么留给你的唯一回应代码是…

(旧的)响应代码,告诉客户端在下一个请求中发送一个POST,但临时为307.但是,该重定向不会被客户端缓存,因此它将再次发布到请求的IF中.307代码在HTTP/1.1中引入.

哦,在HTTP/2中用同样的方式重定向工作,就像HTTP/1.1中那样.

|        | 永久的  | 暂时的     |
| ------ | ---- | ------- |
| 切换到    | 三百零一 | 302和303 |
| 保持原有方法 | 三百零八 | 三百零七    |

### 决定在重定向中使用什么方法

事实证明,世界上有Web服务希望将POST发送到原始URL,但是使用301、302或303响应代码的HTTP重定向进行响应,并且*仍然*希望HTTP客户端发送下一个请求作为POST.正如上面所解释的,浏览器不会这样做,默认情况下也不会cURL.

由于这些设置存在,而且它们实际上并不罕见,所以CURL提供了改变其行为的选项.

您可以通过使用专用选项来告诉CURL不改变非get请求方法以获得30X响应.`--post301`,`--post302`和`--post303`. 如果您正在编写基于LyCURL的应用程序,则用`CURLOPT_POSTREDIR`选择权.

## 重定向到其他主机名

当您使用curl时,您可以为特定站点提供诸如用户名和密码之类的凭据,但是由于HTTP重定向很可能会移到不同的主机,所以在同一个"传输"中,HTTP重定向会限制它向其他主机发送的内容,而不是向原始主机发送的内容.

因此,如果您希望凭据也被发送到以下主机名,即使它们与原始主机名不同,可能是因为您信任它们,并且知道这样做没有害处,那么可以使用`--location-trusted`选择权.

# 非HTTP重定向

由于curl不支持或识别这些方法,浏览器支持更多的重定向方法,有时这会使curl用户的生活变得复杂.

## HTML重定向

如果上述还不够,Web世界也提供了一种用普通HTML重定向浏览器的方法.参见示例`<meta>`下面的标签.这与CURL有点复杂,因为CURL从不解析HTML,因此不知道这些重定向.

```
<meta http-equiv="refresh" content="0; url=http://example.com/">
```

## JavaScript重定向

现代Web充满了JavaScript,正如您所知,JavaScript是一种语言,并且是允许在访问网站时在浏览器中执行代码的完整运行时间.

JavaScript还提供了一种方法来指导浏览器移动到另一个站点,如果你愿意,可以重定向.
