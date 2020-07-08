# Websocket

**轮询**：在前端通过写js实现。缺点：有延迟、服务器压力大。

```
 就是客户端通过一定的时间间隔以频繁请求的方式向服务器发送请求，来保持客户端和服务器端的数据同步。问题很明显，当客户端以固定频率向服务器端发送请求时，服务器端的数据可能并没有更新，带来很多无谓请求，浪费带宽，效率低下。

```

**长轮询**

```
首先需要为每个用户维护一个队列，用户浏览器会通过js递归向后端自己的队列获取数据，自己队列没有数据，会将请求夯住（去队列中获取数据），夯一段时间之后再返回。
注意：一旦有数据立即获取，获取到数据之后会再发送请求。

```

```
用户发来请求之后，最多会夯住N秒（30s），因为有消息的时候回立即返回，没有消息时才最多夯N秒。

```

### 2.websocket

#### 2.1 原理

- WebSocket 是 HTML5 一种新的协议。它实现了浏览器与服务器全双工通信，能更好的节省服务器资源和带宽并达到实时通讯，它建立在 TCP 之上，同 HTTP 一样通过 TCP 来传输数据，但是它和 HTTP 最大不同是：

- WebSocket 是一种双向通信协议，在建立连接后，WebSocket 服务器和 Browser/Client Agent 都能主动的向对方发送或接收数据，就像 Socket 一样；
- WebSocket 需要类似 TCP 的客户端和服务器端通过握手连接，连接成功后才能相互通信。



#### 2.2具体实现：

- 客户端向服务端发送随机字符串，在http的请求头中 `Sec-WebSocket-Key`

- 服务端接受到到随机字符串

- 随机字符串 + 魔法字符串
- sha1加密
- base64加密
- 放在响应头中给用户返回 `Sec-WebSocket-Accept`


- 客户端浏览器会进行校验，校验不通过：服务端不支持websocket协议。

- 客户端和服务端进行相互收发消息，数据加密和解密。

- 解密细节：

- 获取第二个字节的后7位，payload len

- 判断payload len的值

- = 127

```
2字节  + 8字节  +  4字节 + 数据

```

- = 126

```
2字节  + 2字节  +  4字节 + 数据

```

- <= 125

```
2字节  +  4字节 + 数据

```



- 使用masking key 对数据进行解密




#### 2.3 手动创建支持websocket的服务端（理解）

- 服务端

```
import socket
import hashlib
import base64


def get_headers(data):
    """
    将请求头格式化成字典
    :param data:
    :return:
    """
    header_dict = {}
    data = str(data, encoding='utf-8')
    header, body = data.split('\r\n\r\n', 1)
    header_list = header.split('\r\n')
    for i in range(0, len(header_list)):
        if i == 0:
            if len(header_list[i].split(' ')) == 3:
                header_dict['method'], header_dict['url'], header_dict['protocol'] = header_list[i].split(' ')
        else:
            k, v = header_list[i].split(':', 1)
            header_dict[k] = v.strip()
    return header_dict


def get_data(info):
    """
    对数据进行就解密
    """
    payload_len = info[1] &amp; 127
    if payload_len == 126:
        extend_payload_len = info[2:4]
        mask = info[4:8]
        decoded = info[8:]
    elif payload_len == 127:
        extend_payload_len = info[2:10]
        mask = info[10:14]
        decoded = info[14:]
    else:
        extend_payload_len = None
        mask = info[2:6]
        decoded = info[6:]

    bytes_list = bytearray()
    for i in range(len(decoded)):
        chunk = decoded[i] ^ mask[i % 4]
        bytes_list.append(chunk)
    body = str(bytes_list, encoding='utf-8')
    return body


sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('127.0.0.1', 8002))
sock.listen(5)


# 等待用户连接
conn, address = sock.accept()
# 握手环节
header_dict = get_headers(conn.recv(1024))
# 公认的魔法字符串(固定)
magic_string = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11'

random_string = header_dict['Sec-WebSocket-Key']
value = random_string + magic_string
ac = base64.b64encode(hashlib.sha1(value.encode('utf-8')).digest())
# 给前端发送的响应数据
response = "HTTP/1.1 101 Switching Protocols\r\n" \
      "Upgrade:websocket\r\n" \
      "Connection: Upgrade\r\n" \
      "Sec-WebSocket-Accept: %s\r\n" \
      "WebSocket-Location: ws://127.0.0.1:8002\r\n\r\n"

response = response %ac.decode('utf-8')

conn.send(response.encode('utf-8'))

# 接受数据
while True:
    data = conn.recv(1024)
    msg = get_data(data)
    print(msg)


```

- html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <input type="button" value="开始" onclick="startConnect();">

<script>
    var ws = null;
    function startConnect() {
        // 1. 内部会先发送随机字符串
        // 2. 内部会校验加密字符串
        ws = new WebSocket('ws://127.0.0.1:8002')
    }
</script>
</body>
</html>

```


#### 2.4 企业应用

我们学过django和flask框架，内部基于wsgi做的socket，默认他俩都不支持websocket协议，只支持http协议。

- flask中应用

```
pip3 install gevent-websocket 

```

- django中应用

```
pip3 install channels

```



在基于Django实现的时候因为需要导入`channels`模块，会跟Django中的`wsgiref`发生冲突所以需要在配置文件中添加配置：

```
1.首先是在settings文件中的需要注册这个应用：
	INSTALLED_APPS = ['channels']
    
2.在settings文件中增加一个文件的路径：
	ASGI_APPLICATION = "wechat.routing.application"   
这个文件是用来写前端使用websocket发送数据时指定的接收路径，相当于url；
	

```

- 1v1示例

    


```
from channels.generic.websocket import WebsocketConsumer
# 这里除了 WebsocketConsumer 之外还有
# JsonWebsocketConsumer
# AsyncWebsocketConsumer
# AsyncJsonWebsocketConsumer
# WebsocketConsumer 与 JsonWebsocketConsumer 就是多了一个可以自动处理JSON的方法
# AsyncWebsocketConsumer 与 AsyncJsonWebsocketConsumer 也是多了一个JSON的方法
# AsyncWebsocketConsumer 与 WebsocketConsumer 才是重点
# 看名称似乎理解并不难 Async 无非就是异步带有 async / await
# 是的理解并没有错,但对与我们来说他们唯一不一样的地方,可能就是名字的长短了,用法是一模一样的
# 最夸张的是,基类是同一个,而且这个基类的方法也是Async异步的

class ChatService(WebsocketConsumer):
    # 当Websocket创建连接时
    def connect(self):
        #保持连接状态
        self.accept()
        print('找到女朋友了')
        pass
    
    # 当Websocket接收到消息时
    def receive(self, text_data=None, bytes_data=None):
        #发送接收到的消息
        self.send(text_data)
        print('正在发送消息')
        pass
    
    # 当Websocket发生断开连接时
    def disconnect(self, code):
        print('分手了')
        pass

```

#### 基于vue的websocket客户端

    <template>
        <div>
            <input type="text" v-model="message">
            <p><input type="button" @click="send" value="发送"></p>
            <p><input type="button" @click="close_socket" value="关闭"></p>
        </div>
    </template>
    
    
    <script>
    export default {
        name:'websocket1',
        data() {
            return {
                message:'',
                testsocket:'',
                username:''
            }
        },
        methods:{
            send(){
               
            //    send  发送信息
            //    close 关闭连接
    
                this.testsocket.send(this.message)
                this.testsocket.onmessage = (res) => {
                    console.log("WS的返回结果",res.data);         
                }
    
            },
            close_socket(){
                this.testsocket.close()
            },
            generate_uuid: function() {
                var d = new Date().getTime();
                if (window.performance && typeof window.performance.now === "function") {
                    d += performance.now(); //use high-precision timer if available
                }
                var uuid = "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(
                    /[xy]/g,
                    function(c) {
                    var r = (d + Math.random() * 16) % 16 | 0;
                    d = Math.floor(d / 16);
                    return (c == "x" ? r : (r & 0x3) | 0x8).toString(16);
                    }
                );
                return uuid;
            },
    
        },
        mounted(){
            this.username = this.generate_uuid();
    
            this.testsocket = new WebSocket("ws://127.0.0.1:8000/ws/"+ this.username + "/") 
    
    
            // onopen     定义打开时的函数
            // onclose    定义关闭时的函数
            // onmessage  定义接收数据时候的函数
            this.testsocket.onopen = function(){
                console.log("开始连接socket")
            },
            this.testsocket.onclose = function(){
                console.log("socket连接已经关闭")
            }
            this.testsocket.onmessage = (res) => {
                    console.log("WS的返回结果",res.data);         
            }
        }
    }
    </script>
    


