# WebSocket协议

> WebSocket协议是基于TCP的一种新的网络协议。它实现了浏览器与服务器全双工（full-duplex）通信，即允许服务器主动发送信息给客户端。因此，在WebSocket中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输，客户端和服务器之间的数据交换变得更加简单。

WebSocket是一种与[HTTP](https://zh.m.wikipedia.org/wiki/超文本传输协议)不同的协议。两者都位于[OSI模型](https://zh.m.wikipedia.org/wiki/OSI模型)的[应用层](https://zh.m.wikipedia.org/wiki/应用层)，并且都依赖于[传输层](https://zh.m.wikipedia.org/wiki/传输层)的TCP协议。 虽然它们不同，但是RFC 6455中规定：`it is designed to work over HTTP ports 80 and 443 as well as to support HTTP proxies and intermediaries`（WebSocket通过HTTP端口80和443进行工作，并支持HTTP代理和中介），从而使其与HTTP协议兼容。 为了实现兼容性，WebSocket握手使用HTTP Upgrade头从HTTP协议更改为WebSocket协议。

Web Sockets以最小的开销高效地提供了Web连接。相较于经常需要使用推送实时数据到客户端甚至通过维护两个HTTP连接来模拟全双工连接的旧的轮询或长轮询（Comet）来说，这就极大的减少了不必要的网络流量与延迟。

要使用HTML5 Web Sockets从一个Web客户端连接到一个远程端点，你要创建一个新的WebSocket实例并为之提供一个URL来表示你想要连接到的远程端点。该规范定义了ws://以及wss://模式来分别表示WebSocket和安全WebSocket连接,这就跟http:// 以及https:// 的区别是差不多的。

一个WebSocket连接是在客户端与服务器之间HTTP协议的初始握手阶段将其升级到Web Socket协议来建立的，其底层仍是TCP/IP连接。

## websocket和http的区别与联系

websocket是和http并存的两种应用层协议。
HTTP协议是非持久化的，单向的网络协议，在建立连接后只允许浏览器向服务器发出请求后，服务器才能返回相应的数据。当需要即时通讯时，通过轮询在特定的时间间隔（如1秒），由浏览器向服务器发送Request请求，然后将最新的数据返回给浏览器。

![](https://github.com/SocialistYouth/Test/blob/main/http%E8%BF%9E%E6%8E%A5.png?raw=true)

**websocket** **和** **http** **的区别**：

> 1、WebSocket是双向通信协议，模拟Socket API，可以双向发送或接受信息，而HTTP是单向的；
>
> 2、WebSocket是需要浏览器和服务器握手进行建立连接的，而http是浏览器发起向服务器的连接

**websocket 和 http 的联系：**WebSocket在建立握手时，数据是通过HTTP传输的。但是建立之后，在真正传输时候是不需要HTTP协议的。单独建立一条TCP的通信通道进行数据的传送。



> **websocket 和 socket 的关系**
> Socket其实并不是一个协议，而是为了方便使用TCP或UDP而抽象出来的一层，是位于应用层和传输控制层之间的一组接口。
> “Socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口，提供一套调用TCP/IP协议的API。在设计模式中，Socket其实就是一个门面模式，它把复杂的TCP/IP协议族隐藏在Socket接口后面，对用户来说，一组简单的接口就是全部，让Socket去组织数据，以符合指定的协议。” 
> 当两台主机通信时，必须通过Socket连接，Socket则利用TCP/IP协议建立TCP连接。TCP连接则更依靠于底层的IP协议，IP协议的连接则依赖于链路层等更低层次。
WebSocket就像HTTP一样，则是一个典型的应用层协议。
**Socket是传输控制层接口，WebSocket是应用层协议。**

## Web端即时通讯

### 1.ajax短轮询

短轮询的基本思路就是浏览器每隔一段时间向浏览器发送**http**请求，服务器端在收到请求后，不论是否有数据更新，都直接进行响应。这种方式实现的即时通信，本质上还是浏览器发送请求，服务器接受请求的一个过程，通过让客户端不断的进行请求，使得客户端能够模拟实时地收到服务器端的数据的变化。

- 优点：

	简单，易于理解。实现没有什么技术难点

- 缺点：

	需要不断的建立http连接，需要不断的发送请求，而且通常HTTP request的Header是非常长的，为了传输一个很小的数据 需要付出巨大的代价，是很不合算的，占用了很多的宽带。

**因此短轮询不适用于那些同时在线用户数量比较大，并且很注重性能的Web应用。**

### 2.长轮询(comet)

> comet: 一种基于http长连接的“服务器推”技术

长轮询是在打开一条连接以后保持，等待服务器推送来数据再关闭的方式。本质上也是轮询，但与短轮询区别在于：

当服务器收到客户端发来的请求后,服务器端不会直接进行响应，而是先将这个请求挂起，然后判断服务器端数据是否有更新。如果有更新，则进行响应，如果一直没有数据，则到达一定的时间限制(服务器端设置)才返回。

- 优点：和短轮询比起来，明显减少了很多不必要的http请求次数，相比之下节约了资源。
- 缺点：连接挂起也会导致资源的浪费

### 3.长连接(SSE)

### 4.WebSocket

> 如果说Ajax的出现是互联网发展的必然，那么Comet技术的出现则更多透露出一种无奈，仅仅作为一种hack技术，因为没有更好的解决方案。Comet解决的问题应该由谁来解决才是合理的呢？浏览器，html标准，还是http标准？主角应该是谁呢？本质上讲，这涉及到数据传输方式，http协议应首当其冲，是时候改变一下这个懒惰的协议的请求/响应模式了。

## WebSocket 的优势

现在，很多网站为了实现推送技术，所用的技术都是Ajax轮询。轮询是在特定的时间间隔（如每1秒），由浏览器对服务器发出HTTP请求，然后由服务器返回最新的数据给客户端的浏览器。

> Ajax :  "Asynchronous Javascript And XML”(异步JavaScript和XML)
>
> Ajax能够在不刷新整个页面的情况下，跟后端的服务器进行通信，从而获得当前页面请求的某些内容，然后对页面进行部分更新。因为一张网页的大部分地方在加载之后是不需要更新的，这种方式减少了很多没有意义的数据传输，可以提高效率，也能提升用户体验。一句话概括，Ajax能完成的就是一件事，进行更有效的前后端数据交互。
>
> 从具体的实现上来说，现代的浏览器通过一个JavaScript的内置对象，也就是出现频率很高的XMLHttpRequest对象来实现这种效果。

这种传统的模式带来很明显的缺点，即浏览器需要不断的向服务器发出请求。然而HTTP请求可能包含较长的头部，其中真正有效的数据可能只是很小的一部分，显然这样会浪费很多的带宽等资源。

HTML5定义的WebSocket协议优势如下：

- 小Header：互相沟通的Header非常小，只有2Bytes左右。

- 服务器不再被动接收到浏览器的请求之后才返回数据，而是在有新数据时就主动推送给浏览器。

- WebSocket协议能更好的节省服务器资源和带宽，并且能够更实时地进行通讯。

## WebSocket 的特点

websocket最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。

其他特点包括：
	
（1）建立在 TCP 协议之上，服务器端的实现比较容易。
	
（2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
	
（3）数据格式比较轻量，性能开销小，通信高效。
	
（4）可以发送文本，也可以发送二进制数据。
	
（5）没有同源限制，客户端可以与任意服务器通信。
	
（6）协议标识符是`ws`（如果加密，则为`wss`），服务器网址就是 URL。

## WebSocket  Client API

#### 1. WebSocket Constructor

`var ws = new WebSocket('ws://localhost:8080');`

客户端与服务器进行连接。

#### 2. webSocket.readyState

`readyState`属性返回实例对象的当前状态，共有四种。

> - CONNECTING：值为0，表示正在连接。
> - OPEN：值为1，表示连接成功，可以通信了。
> - CLOSING：值为2，表示连接正在关闭。
> - CLOSED：值为3，表示连接已经关闭，或者打开连接失败。

#### 3. webSocket.onopen

实例对象的`onopen`属性，用于指定连接成功后的回调函数。

#### 4. webSocket.onclose

实例对象的`onclose`属性，用于指定连接关闭后的回调函数。

#### 5. webSocket.onmessage

实例对象的`onmessage`属性，用于指定收到服务器数据后的回调函数。

#### 6. webSocket.send()

实例对象的`send()`方法用于向服务器发送数据。

#### 7. webSocket.onerror

实例对象的`onerror`属性，用于指定报错时的回调函数。

# Django Channels Tutorial

> Channels是Django团队研发的一个给Django提供websocket支持的框架，Channels 允许您在 Django 站点中使用 Websockets 和其他非 HTTP 协议。例如, 您可能希望 Websockets 允许网站上的页面立即从 Django 服务器接收更新, 而无需使用 HTTP 长轮询或其他昂贵的技术。使用channels可以让Django应用拥有实时通讯和给用户主动推送信息的功能。

**在Django项目里面，我们并不是直接使用websocket协议，因为原生django不支持websocket，仅有Django Channels库支持WebSocket。**

**Channels由几个包组成:**

- Channels, Django集成层
- Daphne， HTTP和WebSocket终端服务器
- ASGIref，基于ASGI库
- channel_redis, the Redis channel layer后端（可选）



在本教程中, 我们将建立一个简单的聊天服务器, 在那里你可以加入一个在线房间, 张贴消息到房间, 并让其他人在同一房间看到这些消息立即。

## Tutorial Part 1: 基本设置

在本教程中, 我们将构建一个简单的聊天服务器。它将有两个页面:

> - index 视图，用于输入要加入的聊天室的名称。
> - room 视图，可以查看在特定聊天室中发送的消息。
>	- room 视图将使用 WebSocket 与 Django 服务器进行通信, 并监听任何发送出来的消息。

假设您已经熟悉构建django站点的基本概念：

1. 假设您已经安装了Django，通过在shell提示符下运行以下命令来查看您安装的Django版本（3.2.8）：

	```shell
	$ python3 -m django --version
	```

2. 假设您已经安装了channels，通过在shell提示符下运行以下命令来查看您安装的Channels版本（3.0.4）：

	```shell
	$ python3 -c 'import channels; print(channels.__version__)'
	```

### 1. 创建一个项目

```shell
$ django-admin startproject mysite
```

这将在当前目录中创建一个 mysite 目录, 其中有以下内容:

```
mysite/
    manage.py
    mysite/
        __init__.py
        asgi.py
        settings.py
        urls.py
        wsgi.py
```

### 2. 创建聊天应用

我们将聊天服务器的代码放在其自己的应用程序中。确保您与`manage.py`位于同一目录中，然后键入以下命令：

```shell
$ python3 manage.py startapp chat	
```

这将创建一个目录`chat`，其布局如下：

```
chat/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

我们需要告诉我们的项目，该应用程序已安装。编辑文件`mysite/mysite/settings.py`并添加`chat`到**INSTALLED_APPS**设置。它看起来像这样：

```
# mysite/settings.py
INSTALLED_APPS = [
    'chat',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

### 3. 添加index视图

在 `chat` 目录中创建 `templates` 目录。在刚刚创建的 `templates` 目录中, 创建另一个名为 `chat` 的目录, 并在其中创建一个名为 `index.html` 的文件。

将以下代码放入：`chat/templates/chat/index.html`

```HTML
<!-- chat/templates/chat/index.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <title>Chat Rooms</title>
</head>
<body>
    What chat room would you like to enter?<br>
    <input id="room-name-input" type="text" size="100"><br>
    <input id="room-name-submit" type="button" value="Enter">

    <script>
        document.querySelector('#room-name-input').focus();
        document.querySelector('#room-name-input').onkeyup = function(e) {
            if (e.keyCode === 13) {  // enter, return
                document.querySelector('#room-name-submit').click();
            }
        };

        document.querySelector('#room-name-submit').onclick = function(e) {
            var roomName = document.querySelector('#room-name-input').value;
            window.location.pathname = '/chat/' + roomName + '/';
        };
    </script>
</body>
</html>
```

#### 3.2 为index视图创建视图函数

将以下代码放入`chat/views.py`：

```python
# chat/views.py
from django.shortcuts import render

def index(request):
    return render(request, 'chat/index.html')
```

要调用视图，我们需要将其映射到 URL - 为此，我们需要一个 URLconf。

#### 3.3  在`chat`目录下创建URLconf

要在`chat`目录中创建 URLconf，请创建一个名为`urls.py`的文件。文件中包含以下代码：

```python
# chat/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

#### 3.4 将根URLconf指向chat.urls模块

在 `mysite/urls.py `中, 导入 django.urls.include模块，并在 urlpatterns 列表中插入一个 include() 函数, 因此您需要写入以下代码:

```python
# mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('chat/', include('chat.urls')),
    path('admin/', admin.site.urls),
]
```

#### 3.5  验证索引视图是否正常工作

运行以下命令：

```shell
$ python3 manage.py runserver
```

您将在命令行上看到以下输出：

```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
October 21, 2020 - 18:49:39
Django version 3.1.2, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

忽略有关未应用数据库迁移的警告。我们将不会在本教程中使用数据库。

> 在浏览器中转到 http://127.0.0.1:8000/chat/, 您应该看到文本 "What chat room would you like to enter?" 以及一个用于输入房间名字的文本输入框。
>
> **注意**：如果是在本地开发，键入URL http://127.0.0.1:8000/chat/ 可以看到刚刚开发的站点，但在公网服务器上开发，且用其他网络设备查看该URL，应该做以下几步：
>
> 1.在`mysite/settings.py`中`ALLOWED_HOSTS`加入该公网IP，看起来应该像这样：
>
> ```python
> ALLOWED_HOSTS = ["123.57.187.239"]
> ```
>
> 2.运行以下命令：
>
> ```
> $ python3 manage.py runserver 0.0.0.0:8000
> ```
>
> 3.在浏览器中转到 http://123.57.187.239:8000/chat/

输入 "lobby" 作为房间名称, 然后按 enter 键。你应该被重定向到 http://123.57.187.239:8000/chat/lobby/的房间视图, 但我们还没有写的房间视图, 所以你会得到一个 "页面找不到" 错误页面。

转到运行 runserver 命令的终端, 然后按下 Control+C 以停止服务器。

### 4. 集成 Channels 库

到目前为止, 我们刚刚创建了一个常规的 Django 应用程序;我们根本就没有使用 Channels 库。现在是时候集成 Channels 库了。

让我们首先为 Channels 创建**根路由配置**。Channels 路由配置类似于 Django URLconf的 ASGI 应用程序，它会告诉 Channels 当收到由 Channels 服务器发过来的 HTTP 请求时，应该执行什么代码。

首先调整文件`mysite/asgi.py`以包含以下代码：

```python
# mysite/asgi.py
import os

import django
from channels.http import AsgiHandler
from channels.routing import ProtocolTypeRouter

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mysite.settings')
django.setup()

application = ProtocolTypeRouter({
  "http": AsgiHandler(),
  # Just HTTP for now. (We can add other protocols later.)
})
```

现在, 将 Channels 库添加到已安装的应用程序列表中。编辑 `mysite/settings.py` 文件并将 'channels' 添加到 `INSTALLED_APPS` 设置。它看起来像这样:

```python
# mysite/settings.py
INSTALLED_APPS = [
    'channels',
    'chat',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

您同样需要在根路由配置中指向 Channels。再次编辑 `mysite/settings.py `文件, 并将以下内容添加到底部:

```python
# mysite/settings.py
# Channels
ASGI_APPLICATION = 'mysite.asgi.application'
```

**现在已安装的应用程序中有 Channels, 它将控制 runserver 命令, 用 Channels 开发服务器替换标准的 Django 开发服务器。**

让我们确保通道开发服务器正常工作。运行以下命令：

```shell
$ python3 manage.py runserver 0.0.0.0:8000
```

您将在命令行上看到以下输出：

```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
October 21, 2020 - 19:08:48
Django version 3.1.2, using settings 'mysite.settings'
Starting ASGI/Channels version 3.0.0 development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

请注意以 开头的行。这表明Channels开发服务器已经从Django开发服务器接管了。

`Starting ASGI/Channels version 3.0.0 development server at http://127.0.0.1:8000/`

转到[浏览器中的  http://123.57.187.239:8000/chat/，您仍应看到我们之前创建的索引页。

## Tutorial Part 2:实现聊天服务器

### 1. 添加room视图

创建新的文件 chat/templates/chat/room.html，添加以下代码：

```html
<!-- chat/templates/chat/room.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <title>Chat Room</title>
</head>
<body>
    <textarea id="chat-log" cols="100" rows="20"></textarea><br>
    <input id="chat-message-input" type="text" size="100"><br>
    <input id="chat-message-submit" type="button" value="Send">
    {{ room_name|json_script:"room-name" }}
    <script>
        const roomName = JSON.parse(document.getElementById('room-name').textContent);

        const chatSocket = new WebSocket(
            'ws://'
            + window.location.host
            + '/ws/chat/'
            + roomName
            + '/'
        );

        chatSocket.onmessage = function(e) {
            const data = JSON.parse(e.data);
            document.querySelector('#chat-log').value += (data.message + '\n');
        };

        chatSocket.onclose = function(e) {
            console.error('Chat socket closed unexpectedly');
        };

        document.querySelector('#chat-message-input').focus();
        document.querySelector('#chat-message-input').onkeyup = function(e) {
            if (e.keyCode === 13) {  // enter, return
                document.querySelector('#chat-message-submit').click();
            }
        };

        document.querySelector('#chat-message-submit').onclick = function(e) {
            const messageInputDom = document.querySelector('#chat-message-input');
            const message = messageInputDom.value;
            chatSocket.send(JSON.stringify({
                'message': message
            }));
            messageInputDom.value = '';
        };
    </script>
</body>
</html>
```

#### 1.2 为room视图创建视图函数

在`chat/views.py`加入以下代码：

```python
# chat/views.py
from django.shortcuts import render

def index(request):
    return render(request, 'chat/index.html', {})

def room(request, room_name):
    return render(request, 'chat/room.html', {
        'room_name': room_name
    })
```

#### 1.3 在`chat/urls.py`创建房间视图的路径

```python
# chat/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('<str:room_name>/', views.room, name='room'),
]
```

#### 1.4 启动 Channels 开发服务器:

```shell
$ python3 manage.py runserver 0.0.0.0:8000
```

转到[浏览器中的  http://123.57.187.239:8000/chat/并查看索引页。

输入“lobby”作为房间名称，然后按回车键。您应该被重定向到  http://123.57.187.239:8000/chat/lobby/ 的房间页面，该页面现在显示一个空的聊天记录。

键入消息 "hello", 然后按 enter 键。什么也没有发生。尤其是，消息并不会出现在聊天日志中。为什么？

房间视图试图打开一个 WebSocket 连接到 URL ws://127.0.0.1:8000/ws/chat/lobby/，但我们还没有创建一个接受 WebSocket 连接的 consumer。如果打开浏览器的 JavaScript 控制台, 您应该会看到如下所示的错误:

```
WebSocket connection to 'ws://127.0.0.1:8000/ws/chat/lobby/' failed: Unexpected response code: 500
```

### 2. 编写您的第一个 consumer

>  当 Django 接受 HTTP 请求时, 它会根据根 URLconf 以查找视图函数, 然后调用视图函数来处理请求。同样, 当 Channels 接受 WebSocket 连接时, 它会根据根路由配置以查找对应的 consumer, 然后调用 consumer 上的各种函数来处理来自这个连接的事件。

我们将编写一个简单的 consumer, 它会在路径 /ws/chat/ROOM_NAME/ 接收 WebSocket 连接，然后把接收任意的消息, 回送给同一个 WebSocket 连接。

![attention](https://img2022.cnblogs.com/blog/2858391/202209/2858391-20220902224907888-1913676344.png)

创建新文件 chat/consumers.py， 写入以下代码：

```python
# chat/consumers.py
import json
from channels.generic.websocket import WebsocketConsumer

class ChatConsumer(WebsocketConsumer):
    def connect(self):
        self.accept()

    def disconnect(self, close_code):
        pass

    def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json['message']

        self.send(text_data=json.dumps({
            'message': message
        }))
```

这是一个同步 WebSocket 使用者，它接受所有连接，从其客户端接收消息，并将这些消息回显到同一客户端。目前，它不会向同一房间中的其他客户端广播消息。

> **注意：**
>
> Channels 还支持编写异步 consumers 以提高性能。但是, 任何异步 consumers 都必须小心, 避免直接执行阻塞操作, 例如访问 Django 的 model。

#### 2.2 编写routing.py文件

我们需要为 chat app 创建一个路由配置, 它有一个通往 consumer 的路由（这一部分的作用相当于http的urls）。创建新文件 chat/routing.py。将以下代码放入：

```python
# chat/routing.py
from django.urls import re_path

from . import consumers

websocket_urlpatterns = [
    re_path(r'ws/chat/(?P<room_name>\w+)/$', consumers.ChatConsumer.as_asgi()),
]
```

我们调用classmethod`as_asgi()`是为了获得一个ASGI应用程序，该应用程序将为每个用户连接实例化我们的consumer的实例.    这类似于 Django 的`as_view()`,它对每个请求的 Django 视图实例扮演着相同的角色。

#### 2.3 将根路由配置指向 **chat.routing** 模块

在`mysite/asgi.py` 中, 导入 AuthMiddlewareStack、URLRouter 和 chat.routing ;并在 ProtocolTypeRouter 列表中插入一个 "websocket" 键, 格式如下:

```python
# mysite/asgi.py
import os

from channels.auth import AuthMiddlewareStack
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.security.websocket import AllowedHostsOriginValidator
from django.core.asgi import get_asgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")
# Initialize Django ASGI application early to ensure the AppRegistry
# is populated before importing code that may import ORM models.
django_asgi_app = get_asgi_application()

import chat.routing

application = ProtocolTypeRouter({
  "http": django_asgi_app,
  "websocket": AllowedHostsOriginValidator(
        AuthMiddlewareStack(URLRouter(chat.routing.websocket_urlpatterns))
    ),
})
```

这个根路由配置指定，当与 Channels 开发服务器建立连接的时候, ProtocolTypeRouter 将首先检查连接的类型。如果是 WebSocket 连接 (ws://或 wss://), 则连接会交给 AuthMiddlewareStack。

根据提供的 url 模式, URLRouter 将检查连接的 HTTP 路径, 以将其路由指定到到特定的 consumer。

#### 2.4 验证Channels开发服务器

让我们验证路径的使用者是否正常工作。运行迁移以应用数据库更改（Django的会话框架需要数据库），然后启动Channels开发服务器：`/ws/chat/ROOM_NAME/`

```shell
$ python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying sessions.0001_initial... OK
$ python3 manage.py runserver 0.0.0.0:8000
```

转到  http://123.57.187.239:8000/chat/lobby/ 中的房间页面，该页面现在显示空的聊天记录。

键入消息“你好”，然后按回车键。您现在应该看到聊天记录中回显的“hello”。

但是，如果您在  http://123.57.187.239:8000/chat/lobby/ 打开同一房间页面的第二个浏览器选项卡并键入消息，则该消息将不会显示在第一个选项卡中。为此，我们需要有多个相同的`ChatConsumer`实例能够相互通信。Channels提供了一个**channel layer**抽象，可实现consumers之间的这种通信。

### 3. 启动channel layer

channel layer 是一种通信系统。他允许多个consumer实例相互通信，并与Django的其他部分通信。

channel layer 提供以下抽象：

- **channel** 是可以发送message 的邮箱。每个channel都有一个名称。任何拥有channel名称的人都可以向channel发送消息。
- **group**是一组相关的channels。一个group有一个名字。任何拥有group名称的人都可以按名称向group添加/删除channel，并向group中所有channel发送消息。**无法枚举特定group中的channels**

每个consumer实例都有一个自动生成的唯一channel name，因此可以通过channel layer进行通信。

在我们的聊天应用程序中，我们希望`ChatConsumer`同一个房间中的多个实例相互通信。为此，我们将让每个 ChatConsumer 将其channel添加到名称基于房间名称的组中。这将允许 ChatConsumers 将消息传输到同一房间中的所有其他 ChatConsumer。

我们将使用一个使用 Redis 作为其后备存储的channel layer。要在端口 6379 上启动 Redis 服务器，请运行以下命令：

```shell
$ docker run -p 6379:6379 -d redis:5
```

#### 3.2 安装channels_redis

我们需要安装 channels_redis 以便 Channels 知道如何与 Redis 交互。运行以下命令：

```shell
pip install channels_redis
```

在我们能使用channel layer后，我们必须对其进行配置。编辑`mysite/settings.py`文件并将`CHANNEL_LAYERS `设置添加到底部。它应该看起来像：

```python
# mysite/settings.py
# Channels
ASGI_APPLICATION = 'mysite.asgi.application'
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {
            "hosts": [('127.0.0.1', 6379)],
        },
    },
}
```

让我们确保channel layer可以与 Redis 通信。打开 Django shell 并运行以下命令：

```django
$ python3 manage.py shell
>>> import channels.layers
>>> channel_layer = channels.layers.get_channel_layer()
>>> from asgiref.sync import async_to_sync
>>> async_to_sync(channel_layer.send)('test_channel', {'type': 'hello'})
>>> async_to_sync(channel_layer.receive)('test_channel')
{'type': 'hello'}
```

> 键入 Ctrl + D 退出

#### 3.3 修改cousumer代码

现在我们有了一个channel layer，让我们在`ChatConsumer`. 将以下代码放入`chat/consumers.py`，替换旧代码：

```python
# chat/consumers.py
import json
from asgiref.sync import async_to_sync
from channels.generic.websocket import WebsocketConsumer

class ChatConsumer(WebsocketConsumer):
    def connect(self):
        self.room_name = self.scope['url_route']['kwargs']['room_name']
        self.room_group_name = 'chat_%s' % self.room_name

        # Join room group
        async_to_sync(self.channel_layer.group_add)(
            self.room_group_name,
            self.channel_name
        )

        self.accept()

    def disconnect(self, close_code):
        # Leave room group
        async_to_sync(self.channel_layer.group_discard)(
            self.room_group_name,
            self.channel_name
        )

    # Receive message from WebSocket
    def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json['message']

        # Send message to room group
        async_to_sync(self.channel_layer.group_send)(
            self.room_group_name,
            {
                'type': 'chat_message',
                'message': message
            }
        )

    # Receive message from room group
    def chat_message(self, event):
        message = event['message']

        # Send message to WebSocket
        self.send(text_data=json.dumps({
            'message': message
        }))
```

>  当用户发布消息时，JavaScript 函数将通过 WebSocket 将消息传输到 ChatConsumer。ChatConsumer 将收到该消息并将其转发到与房间名称对应的组。同一组（因此在同一个房间）中的每个 ChatConsumer 将接收来自该组的消息，并通过 WebSocket 将其转发回 JavaScript，在那里它将被附加到聊天日志中。

**新代码的几个部分`ChatConsumer`值得进一步解释**：

- `self.scope['url_route']['kwargs']['room_name']`

	- 从`chat/routing.py`中打开到cousumer的WebSocket连接的URL路由中获取“room_name”参数。
	- 每个消费者都有一个[范围](https://channels.readthedocs.io/en/stable/topics/consumers.html#scope)，其中包含有关其连接的信息，尤其包括来自 URL 路由的任何位置或关键字参数以及当前经过身份验证的用户（如果有）。

- `self.room_group_name = 'chat_%s' % self.room_name`

	- 直接从用户指定的房间名称构造一个 Channels 组名称，没有任何引用或转义。
	- 组名只能包含字母数字、连字符、下划线或句点。因此，此示例代码将在包含其他字符的房间名称上失败。

- `async_to_sync(self.channel_layer.group_add)(...)`

	- 加入一个群组。
	- async_to_sync(…) 包装器是必需的，因为 ChatConsumer 是一个同步的 WebsocketConsumer，但它正在调用异步channel layer方法。（所有channel layer方法都是异步的。）
	- 组名称仅限于 ASCII 字母数字、连字符和句点。由于此代码直接从房间名称构造组名称，因此如果房间名称包含任何在组名称中无效的字符，它将失败。

- `self.accept()`

	- 接受 WebSocket 连接。

	- 如果您没有在 connect() 方法中调用 accept()，那么连接将被拒绝并关闭。例如，您可能希望拒绝连接，因为请求用户无权执行请求的操作。如果您选择接受连接，建议将 accept() 作为 connect() 中的*最后一个操作调用。*

- `async_to_sync(self.channel_layer.group_discard)(...)`

	- 离开一组。

- `async_to_sync(self.channel_layer.group_send)`

	- 向组发送事件。
	- 一个事件有一个特殊的`'type'`键，对应于接收事件的消费者应该调用的方法的名称。

#### 3.4 重启Channels开发服务器

请运行以下命令：

```django
$ python3 manage.py runserver 0.0.0.0:8000
```

在 http://123.57.187.239:8000/chat/lobby/打开一个浏览器选项卡到房间页面。打开第二个浏览器选项卡到同一房间页面。

在第二个浏览器选项卡中，键入消息“hello”，然后按 Enter。您现在应该在第二个浏览器选项卡和第一个浏览器选项卡中的聊天日志中看到回显“hello”。

您现在拥有一个基本的全功能聊天服务器！

## Tutorial Part 3:将聊天服务器重写为异步

> 将消费者代码重写为异步， 而不是同步，以提高其性能。

`ChatConsumer`我们所写的当前是同步的。同步消费者很方便，因为它们可以调用常规的同步 I/O 函数，例如访问 Django 模型的函数，而无需编写特殊代码。然而，异步消费者可以提供更高级别的性能，因为他们在处理请求时不需要创建额外的线程。

`ChatConsumer`仅使用异步原生库（通道和通道层），特别是它不访问同步 Django 模型。因此，它可以被重写为异步而没有复杂性。

> **Note:**
>
> 即使`ChatConsumer` *确实*访问了 Django 模型或其他同步代码，仍然可以将其重写为异步。[asgiref.sync.sync_to_async](https://channels.readthedocs.io/en/stable/topics/consumers.html#sync-to-async)和 [channels.db.database_sync_to_async](https://channels.readthedocs.io/en/stable/topics/databases.html)等实用程序 可用于从异步使用者调用同步代码。然而，性能提升将低于仅使用异步原生库的情况。

### 1. 重写consumers

让我们重写`ChatConsumer`为异步。将以下代码放入`chat/consumers.py`：

```python
# chat/consumers.py
import json
from channels.generic.websocket import AsyncWebsocketConsumer

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope['url_route']['kwargs']['room_name']
        self.room_group_name = 'chat_%s' % self.room_name

        # Join room group
        await self.channel_layer.group_add(
            self.room_group_name,
            self.channel_name
        )

        await self.accept()

    async def disconnect(self, close_code):
        # Leave room group
        await self.channel_layer.group_discard(
            self.room_group_name,
            self.channel_name
        )

    # Receive message from WebSocket
    async def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json['message']

        # Send message to room group
        await self.channel_layer.group_send(
            self.room_group_name,
            {
                'type': 'chat_message',
                'message': message
            }
        )

    # Receive message from room group
    async def chat_message(self, event):
        message = event['message']

        # Send message to WebSocket
        await self.send(text_data=json.dumps({
            'message': message
        }))
```

**ChatConsumer 的新代码与原始代码非常相似，但有以下区别：**

- `ChatConsumer`现在继承自`AsyncWebsocketConsumer`而不是 `WebsocketConsumer`.
- 所有的方法是`async def`而不仅仅是`def`.
- `await`用于调用执行 I/O 的异步函数。
- `async_to_sync`在channel layer调用方法时不再需要。

### 2. 重启channel开发服务器

让我们验证`/ws/chat/ROOM_NAME/`路径的消费者是否仍然有效。要启动 Channels 开发服务器，请运行以下命令：

```
$ python3 manage.py runserver 0.0.0.0:8000
```

在 http://123.57.187.239:8000/chat/lobby/打开房间页面的浏览器选项卡。打开第二个浏览器选项卡到同一房间页面。

在第二个浏览器选项卡中，键入消息“hello”，然后按 Enter。您现在应该在第二个浏览器选项卡和第一个浏览器选项卡中的聊天日志中看到回显“hello”。

现在您的聊天服务器是完全异步的！

## Tutorial Part 4:自动化测试

