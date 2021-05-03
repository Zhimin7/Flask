# 路由

一般我们设置web应用程序的URL，需要设置有意义的URL来帮助用户记忆。比如说，登录界面的URL可以设置为`/login`，而登出界面的URL可以设置为`/logout`。而不是设置一堆没有任何意义的字符串，那样只会恶心用户。

使用`route`装饰器将功能绑定到URL中。

```python
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello, World'
```

你也可以做更多，使部分URL动态化，并将多个规则附加到一个函数上。

大家还需要知道一个内容就是路由函数与装饰器

```python
from flask import Flask

# 路由函数：add_url_rule与装饰器
app = Flask(__name__)

@app.route('/')
def hello():
	return 'Hello world'

def index():
	return 'hello everyone'

app.add_url_rule('/index', view_func=index)

if __name__ == '__main__':
	app.run()
```

通过`add_url_rule`这个函数将我们要访问的路径告诉服务器，那么服务器就会跳转到指定页面。

# 变量规则

我们可以使用变量标记各个部分，将动态部分添加到URL中，标记使用的是`<>`(一对尖括号)。

```python
from flask import Flask
import settings

app = Flask(__name__)
app.config.from_object(settings)

data = {'a':'北京', 'b':'上海','c':'广州','d':'深圳'}

@app.route('/getcity/<key>')
def getcity(key):
	return data.get(key, '走丢了')
	
@app.route('/add/<int:num>')
def add(num):
	result = str(num + 10)
	return result

@app.route('/path/<path:subpath>')
def get_path(subpath):
	return subpath

@app.route('/uuid/<uuid:my_uuid>')
def get_uuid(my_uuid):
    return f'得到的uuid：{my_uuid}'


if __name__=='__main__':
	app.run()
```

除了字符串默认是str类型，其他整型、浮点型等都需要标记`int`和`float`。

要注意返回的值只能是字符串。

## path

在上面的代码中你会发现有一个path类型的标记。那什么时候需要用到呢？

当后面的路径是一种动态变化的，那我们就可以通过这种方式来获取变化的URL。

## uuid

uuid是一种通用的唯一识别码的缩写，是一种软件构建的标准，在这样的情况下，就不需要考虑数据库创建时名称重复的问题。

uuid是一个128bit的数值，这个数值可以通过一定的算法计算出来。uuid用来识别属性类型，在所有空间和时间上被视为唯一标识。

uuid的一般格式为xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx(8-4-4-12)。

同时，我们可以使用Python来生成uuid格式的数据

```python
import uuid

uid = uuid.uuid4()
print(uid)
```

注意：若没有写出正确的uuid格式，访问该URL时会出现空白。

除了字符串和数字类型的数据，其他数据类型使用的比较少。

# / 的重要性

```python
# 以下两个规则的不同之处在于是否使用尾部斜杠


from flask import Flask
import settings


app = Flask(__name__)
app.config.from_object(settings)


@app.route('/project/')
def projects():
	return 'The project page'


@app.route('/about')
def about():
	return 'The about page'

if __name__ == '__main__':
	app.run()
```

上面的代码中有两个路径，第一个为`/project/`，第二个路径为`/about`。

第一个路径的尾部是加了一条斜杠，而第二个路径的尾部是没有加斜杠的。

当我访问：

```
http://localhost:5000/project/
```

想必你应该很明确，返回的值肯定是`The project page`

当我访问的是：

```
http://localhost:5000/project
```

它会进行页面的跳转，然后再访问

```
http://localhost:5000/project/
```

project的URL是中规中矩的，尾部有一个斜杠，看起来就像一个文件夹。访问一个没有斜杠的URL结尾时，Flask会自动进行重定向，并在尾部加上一个斜杠。

about的URL尾部没有斜杠，因此其行为表现与文件类似，如果访问URL添加斜杠就会得到一个404错误。这样可以保持URL唯一，并帮助搜索引擎避免重复索引同一个页面。

# Response对象

## 返回普通的字符串

flask视图函数的return可以不做转化的返回：字符串

```python
app = Flask(__name__)
@app.route('/')
def index():
    return 'Hello World'
```

其实上面的返回是默认使用Response转换过来的，它和下面代码是一样的。

```python
from flask import Flask, Response

app = Flask(__name__)
@app.route('/')
def index():
    return Response('Hello World')
```

## json数据

如果你想要返回列表，字典之类的数据，就需要先转换为json数据返回。

```python
from flask import Flask, jsonify

app = Flask(__name__)
@app.route('/')
def index():
    dict_data = {
        'name': '啃书君',
        'age': 20
    }
    return jsonify(dict_data)
```

## 返回元组

返回元组是有要求的，元组内容包括三个参数，response（响应体）、status_code(状态码，可以自定义)、headers（响应头）

```python
from flask import Flask
import json

app = Flask(__name__)
@app.route('/')
def user():
    json_dict = {
        'name': '啃书君',
        'user_info': {'age': 20}
    }
    data = json.dumps(json_data)
    return data, 200, {"ContentType":"application/json"}
```

在这里headers是可以不写的，系统有默认的响应头。

## 获取该对象的基本信息

```python
from flask import Flask, Response
import settings


app = Flask(__name__)
app.config.from_object(settings)

@app.route('/index')
def index():
	response = Response('<h1>大家好，今天中午吃什么</h1>')
	print(response.content_type)
	print(response.headers)
	print(response.status_code)
	print(response.status)
	return response


if __name__=='__main__':
	app.run()
```

更多的基本信息与操作可以查询flask官网。

# 最后

**啃书君说**：

文章的每一个字都是我用心敲出来的，只希望对得起每一位关注我的人。

点个**[在看]**与**【点赞】**，让我知道，你们也在为自己的学习拼搏和努力。

**路漫漫其修远兮，吾将上下而求索**。

我是**啃书君**，一个专注于学习的人，**你懂的越多，你不懂的越多**。更多精彩内容，我们下期再见！