# 应用和请求上下文

Flask从客户端接受到请求时，要让视图函数能访问一些对象，这样才能处理请求，**请求对象**就是一个很好的例子，它封装了客户端发送的HTTP请求。

## 请求对象

我们知道，Flask通过上下文变量request对外开放请求对象。包含了客户端发送的HTTP请求的全部信息。Flask请求对象中最常用的属性和方法如下表所示：

**Flask请求对象**

|  属性或方法  |                             说明                             |
| :----------: | :----------------------------------------------------------: |
|     form     |               一个字典，存储提交的所有表单字段               |
|     args     |          一个字典，存储URL查询字符串传递的所有参数           |
|    values    |                  一个字典，form和args的合集                  |
|   cookies    |                一个字典，存储请求的所有cookie                |
|   headers    |               一个字典，存储请求的所有HTTP首部               |
|    files     |               一个字典，存储请求上传的所有文件               |
|  get_data()  |                    返回请求主体缓冲的数据                    |
|  get_json()  |      返回一个Python字典，包含解析请求主体后的得到的json      |
|  blueprint   |                    处理请求flask蓝本名称                     |
|   endpoint   | 处理请求的flask端点名称，Flask把视图函数名称用作路由端点名称 |
|    method    |                 HTTP请求方法，例如GET与POST                  |
|    scheme    |                    URL方案（HTTP与HTTPS）                    |
| is_secure()  |           通过安全的链接(HTTPS)发送请求时返回True            |
|     host     |     请求定义的主机名，如果客户定义了端口号，还包括端口号     |
|     path     |                         URL路径部分                          |
| query_string |            URL的查询字符串部分，返回原始二进制值             |
|  full_path   |                  URL的路径和查询字符串部分                   |
|     url      |                     客户端请求的完整URL                      |
|   base_url   |                 同url，但没有查询字符串部分                  |
| remote_addr  |                        客户端的ip地址                        |
|   enviran    |                    请求的原始WSG环境字典                     |

## 使用请求对象的方法与属性

在上面的表格中，向大家展示了request的方法与属性，那么接下来，就带领大家共同来使用这些方法与属性。当然，我肯定不能全部都为大家展示出来，主要介绍我们现在要用到的方法与属性。

首先大家必须要明确的是请求对象是基于request的。

具体代码如下所示：

app.py

```python
from flask import Flask, request
import settings


app = Flask(__name__)
app.config.from_object(settings)


@app.route('/index')
def index():
	print(request.headers)
	print(request.path)
	print(request.url)
	return 'Hello World'


if __name__=='__main__':
	app.run()
```

settings.py

```python
ENV = 'development'
DEBUG = True
```

运行结果：

```
Host: 127.0.0.1:5000
Connection: keep-alive
Sec-Ch-Ua: " Not A;Brand";v="99", "Chromium";v="90", "Google Chrome";v="90"
Sec-Ch-Ua-Mobile: ?0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9


/index
http://127.0.0.1:5000/index
```

接下来，我将对上面的代码进行简单描述。

首先来说一下，settings.py是怎么回事。

其实就是修改配置文件，在定义app这个变量之后，可以在后面添加一行代码就可以看到配置文件啦，**在这里我得先说明一下，默认情况下运行环境不是开发者模式，而是生产者模式，因此，ENV的默认值是production**，如下：

```python
print(type(app.config))
print(app.config)
```

运行结果，如下：

```
<class 'flask.config.Config'>

<Config {'ENV': 'development', 'DEBUG': True, 'TESTING': False, 'PROPAGATE_EXCEPTIONS': None, 'PRESERVE_CONTEXT_ON_EXCEPTION': None, 'SECRET_KEY': None, 'PERMANENT_SESSION_LIFETIME': datetime.timedelta(days=31), 'USE_X_SENDFILE': False, 'SERVER_NAME': None, 'APPLICATION_ROOT': '/', 'SESSION_COOKIE_NAME': 'session', 'SESSION_COOKIE_DOMAIN': None, 'SESSION_COOKIE_PATH': None, 'SESSION_COOKIE_HTTPONLY': True, 'SESSION_COOKIE_SECURE': False, 'SESSION_COOKIE_SAMESITE': None, 'SESSION_REFRESH_EACH_REQUEST': True, 'MAX_CONTENT_LENGTH': None, 'SEND_FILE_MAX_AGE_DEFAULT': datetime.timedelta(seconds=43200), 'TRAP_BAD_REQUEST_ERRORS': None, 'TRAP_HTTP_EXCEPTIONS': False, 'EXPLAIN_TEMPLATE_LOADING': False, 'PREFERRED_URL_SCHEME': 'http', 'JSON_AS_ASCII': True, 'JSON_SORT_KEYS': True, 'JSONIFY_PRETTYPRINT_REGULAR': False, 'JSONIFY_MIMETYPE': 'application/json', 'TEMPLATES_AUTO_RELOAD': None, 'MAX_COOKIE_SIZE': 4093}>
```

返回的类型是：*<class 'flask.config.Config'>*，有没有发现，这种类型的数据很像是字典，其实你也可以像操作字典一样去操作它们。

例如：

```python
print(app.config.get('ENV'))
```

运行结果：

```
development
```

在settings文件中，将运行环境ENV修改为development，将调试模式的布尔值改为True。

这样做的好处就是，在开发过程中当我们修改了代码，终端也会实时进行更新并加载，是不是很方便呢？

- **request.headers**：获取请求的HTTP首部，其实就是请求头的部分。

就像是去请求百度首页，打开network，你就会看到请求头数据。

![image-20210507062223011](https://routing-ospf.oss-cn-beijing.aliyuncs.com/image-20210507062223011.png)

- **request.path**

获取URL的路径部分，**/** 后面的内容称为路径。

- **request.url**

获取完整的URL路径。

## full_path

下面我要详细讲一下**full_path**，这个属性。在上面的表格上也详细的说明了该属性是用来获取URL的路径和查询字符串的部分。

路径我们好理解，就是**/** 后面的内容。但是查询的字符串是什么意思呢？

在这里问一下大家，之前是不是写过百度爬虫，在输入框内输入“美女”

```
https://www.baidu.com/s?wd=美女&rsv_spt=1&rsv_iqid=0x9745989700088f2f&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&tn=baiduhome_pg&rsv_enter=1&rsv_dl=tb&rsv_sug3=3&rsv_sug1=2&rsv_sug7=101&rsv_sug2=0&rsv_btype=i&prefixsug=%25E7%25BE%258E%25E5%25A5%25B3&rsp=5&inputT=1500&rsv_sug4=2839
```

问号(?)后面的内容就是查询的字符串。

在这里我举个例子，就拿简单的用户注册作为例子，先写一个register.html，代码如下所示：

register.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>注册</title>
</head>
<body>
	<form action="/register2" method="get">
		<p><input type="text" name="username" placeholder="用户名"></p>
		<p><input type="text" name="password" placeholder="密码"></p>
		<p><input type="submit" name="" value="提交"></p>
	</form>
</body>
</html>
```

再写一个app.py。

app.py

```python
from flask import Flask, request
import settings


app = Flask(__name__)
app.config.from_object(settings)


@app.route('/register')
def register():
	res = '''
	<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>注册</title>
</head>
<body>
	<form action="/register2" method="get">
		<p><input type="text" name="username" placeholder="用户名"></p>
		<p><input type="text" name="password" placeholder="密码"></p>
		<p><input type="submit" name="" value="提交"></p>
	</form>
</body>
</html>
	
	'''
	return res


@app.route('/register2')
def register2():
	print(request.full_path)
	print(request.path)
	print(request.args)
	print(request.args.get('username'))	
	print(request.args.get('password'))	
	return '注册成功'


if __name__ == '__main__':
	app.run()
	
	
```

现在我将对上面的代码进行简单的讲解说明。

首先，我创建了一个HTML文件，里面有一个form表单，当我点击提交时，将执行action里面的动作。将路径跳转至**/register2**， 因此，我在app.py中创建了**register2()**这个函数，用来处理路径为**/register2**的页面。

- **request.args**

获取查询字符串的参数。返回的结果也是一个字典，因此也可以使用字典的方式去获取里面的值。

注意：这里涉及到路由规则问题，可以在入口函数中，添加一行代码：

```python
print(app.url_map)
```

输出结果，如下：

```
Map([<Rule '/register2' ( HEAD, GET, OPTIONS) -> register2>,
 <Rule '/register' (HEAD, GET, OPTIONS) -> register>,
 <Rule '/static/<filename>' (HEAD, GET, OPTIONS) -> static>])
```

## post请求

从上面的运行结果可以发现，视图函数默认支持的HTTP请求方法有三个：HEAD, GET, OPTIONS。

因此，在我们的HTML表单中，如果将**method**属性修改为**post**将不被允许访问。

![](https://routing-ospf.oss-cn-beijing.aliyuncs.com/image-20210507060335928.png)

那么你就会问了，一般来说注册这种隐秘性极高的内容，肯定是不能使用get方法请求吧。

一定要使用post方法请求，那如果我们将HTML中的method属性修改为post之后，那视图函数应该怎么样修改呢？

只需要对register2的装饰器进行修改即可。

具体代码如下所示：

```python
@app.route('/register2', methods=[ 'POST'])
```

添加一个methods属性，注意这是个列表。将post方法添加进去即可。

## request.form

**request.args**只是获取查询的字符串参数。当我们将请求方法改为post之后，请求参数将会被加密，不再出现在URL中。

然而**request.form**的作用是获取提交的所有表单字段，它也是一个字典。

因此代码可以这样写：

app.py

```python
@app.route('/register2', methods=['POST'])
def register2():
	print(request.full_path)
	print(request.path)
	print(request.args)	# 返回值为空
	print(request.form)
	print(request.form.get('username'))
	print(request.form.get('password'))
	return '注册成功'
```

## render_template

```
flask.render_template(template_name_or_list, **context)
Renders a template from the template folder with the given context.

参数:	
template_name_or_list – the name of the template to be rendered, or an iterable with template names the first one existing will be rendered
context – the variables that should be available in the context of the template.
```

从上面的说明中可以看出render_template也是属于Flask模块下的。

它的作用是，渲染HTML文件，这个HTML文件的默认路径是在templates文件夹下。

如果你是直接从pycharm专业版去创建Flask项目的话，你就会发现，它已经帮我们创建好了这些文件夹。

**注意：如果你写的HTML文件要被渲染，就必须将HTML文件放在templates文件夹下。**

那么问题来了，知道为什么要这种方式去做渲染吗？而不是和前面一样，把HTML代码当作字符，写入视图函数中，然后再返回？

首先，现在是一个前后端分离的时代。一个网站中的HTML页面只有一页吗？可能是几十页、几百页，那你也是把这些HTML代码写入Python文件中吗？

肯定不是。

因此，你现在明白了吗？

那具体代码应该怎么样写呢？

app.py

```python
from flask import Flask, request, render_template
import settings


app = Flask(__name__)
app.config.from_object(settings)


@app.route('/register')
def register():
	res = render_template('register.html')	# 模板渲染
	return res


@app.route('/register2', methods=['POST'])
# 不能使用post函数的原因
def register2():
	print(request.full_path)
	print(request.path)
	print(request.args)
	# print(request.args.get('username'))	# post请求无法获取参数值
	# print(request.args.get('password'))	# 使用request.form.get('username')
	print(request.form)
	print(request.form.get('username'))
	print(request.form.get('password'))
	return '注册成功'


if __name__ == '__main__':
	app.run()
	
	
```

这样看，代码是不是比之前的代码要简单明了很多呢？

# 最后

**啃书君说**：

文章的每一个字都是我用心敲出来的，只希望对得起每一位关注我的人。

为啃书君点个**[在看]**与【**赞**】，让我知道，你们也在为自己的学习拼搏和努力。

**路漫漫其修远兮，吾将上下而求索**。

我是**啃书君**，一个专注于学习的人，**你懂的越多，你不懂的越多**。更多精彩内容，我们下期再见！