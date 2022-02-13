# 						 							OTA测试自动化



背景: 当前TBOX的供应商分为3家,有为,南斗,电驱动

仅仅针对到技术中心的回归验收项,以cs2.0为例,每次的修改后需要回归测试

流程如下:

1. 网联科测试30%    1400*30% = 420
2. 测试部门验收还需要 100 条左右
3. 供应商部分的全量功能测试



会有以下的痛点:

1. 人力成本极大, 目前的网联科测试420条需要22人/天
2. 在项目节点的压力下,供应商是否会进行全量的测试
3. 人员操作的时候会有规范化的问题,过程不可控
4. 测试人员的工作极度枯燥,会让人进入疲乏状态, 影响人员稳定性



考虑到以上的情况:

目前是有考虑将功能测试尽可能实现自动化,后续直接输出相应的结果

好处在于,

1. 案例编写之后可以实现复用
2. 测试的成本降低,测试的效率提升给项目进度加快
3. 提升人员的技术水平,可以将该部分能力拓展到其他的业务上



## 1、基于python-can模拟交互

### 发送消息

```python
import ics
device = ics.find_devices()[0]
ics.open_device(device)

msg = ics.SpyMessage() # Setup the message
msg.ArbIDOrHeader = 0x411  # CAN ID
msg.NetworkID = ics.NETID_HSCAN # Channel 1 on the ValueCAN
msg.Data = (0x12, 0x34, 0x56, 0x78, 0x0, 0x0, 0x0, 0x0) # CAN Data field
msg.is_extended_id=true

ics.transmit_messages(device, msg) # Transmit the message
```



### 接收消息

```
import ics
if __name__ == "__main__":
    bus = can.interface.Bus(bustype='neovi', channel='1', bitrate=250000)
    bus2 = can.interface.Bus(bustype='neovi', channel='2', bitrate=500000)
    logger = can.Logger("logfile.csv")
    # logger = can.Logger("logfile.csv")  # 保存数据到asc文件
    def print_message(msg):
        print(msg)
    listeners = [print_message, logger, ]
    listeners2 = [print_message, logger, ]
    notifier = can.Notifier(bus, listeners)
    notifier2 = can.Notifier(bus2, listeners2)
```



### 模拟总线

1. 我们创建了一个发送(bus_tx)，一个接收(bus_rx)两个节点，并都连接到virtual_ch上
2. 我们创建了一个名为logger的日志记录器，该日志记录器会将受到的信息写入到logfile.asc文件，记录器会根据文件的后缀来决定写入的格式，因此，它将会生成一个asc格式的日志，如果这里后缀为csv，blf或者其它，则会生成相应格式的文件。
3. 日志记录器logger被放置到了一个listeners的列表中，里边还包含一个print_message的回调函数，这个是我们自己实现的函数，只是简单的将输入参数msg打印出来。
4. listeners列表通过Notifier绑定到接收节点（bus_rx），这样只要有新的消息在接收节点上收到，这两个listener就会被执行，print_message会将消息打印到终端，而logger会将消息写入到logfile.asc文件。

```python
import random
import threading
import time

import can

class tx_thread_cl:
    def __init__(self, bus):
        self.bus = bus
        self.running = True
        self.thread = threading.Thread(target=self.tx_callback, args=(self.bus,))
        self.finished = False

    def start(self):
        self.thread.start()

    def tx_callback(self, bus):
        while self.running:
            data = [random.randint(0, 15) for i in range(0, 8)]
            msg = can.Message(is_extended_id=False, arbitration_id=0x7E0, data=data)
            bus.send(msg)
            time.sleep(0.5)
        self.finished = True

    def stop(self):
        self.running = False

def print_message(name):
    print(name)

if __name__ == "__main__":
    # RX part
    bus_rx = can.interface.Bus('virtual_ch', bustype='virtual')
    logger = can.Logger("logfile.csv")  # save log to asc file
    listeners = [
        print_message,  # Callback function, print the received messages
        logger,  # save received messages to asc file
    ]
    notifier = can.Notifier(bus_rx, listeners)
    # TX part
    bus_tx = can.interface.Bus('virtual_ch', bustype='virtual')
    tx_service = tx_thread_cl(bus_tx)
    tx_service.start()
    running = True
    while running:
        input()
        running = False

    while not tx_service.finished:
        tx_service.stop()
    # It's important to stop the notifier in order to finish the writting of asc file
    notifier.stop()
    # stops the bus
    bus_tx.shutdown()
    bus_rx.shutdown()
```





## 2、基于OTA平台,实现任务创建的自动化

### 获取登录token

 

### 创建父任务



### TSP下发指令



```

```



 关于自动化测试项目



## 3、技术框架选型

基础功能如何设计

平台业务架构





## 4、协议解析

实现当前已有的报文解析





## 5、前端项目初始化

考虑学习成本和当前概况,考虑基于vue来实现当前的业务

**前置条件是需要本地已经安装了node环境和npm的环境**



#### 1.webpack安装

```
npm install webpack -g
```

安装之后,如果提示安装webpack-cli的话,直接安装即可

#### 2.脚手架安装

```
npm install --g vue-cli
```

#### 3、初始化项目

进入到对应的文件夹下面,执行一下命令

```
vue init webpack my-project[]    项目的名称
```

​	

**项目的配置项, 示例**



? Project name value_can                      项目名称
? Project description auto_test       项目描述
? Author ray <623888433@qq.com>     项目作者
? Vue build standalone         项目类型   单体
? Install vue-router? Yes     vue的路由,建议安装
? Use ESLint to lint your code? Yes          使用eslint规范编程
? Pick an ESLint preset Standard         eslint配置
? Set up unit tests Yes     单元测试,我也不知道了
? Pick a test runner noTest
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) npm       选择包管理工具



#### 4、项目本地启动

使用vscode打开对应的项目,进入项目目录下,启动项目

npm run dev 即可启动本地项目



#### 5、element-ui安装

element-ui是饿了么的vue框架,目前vue已知的使用较多的组件框架还有ant-design

安装命令

```
npm i element-ui -S
```



需要安装发送请求的工具包

```
npm install axios --save
```

后续需要相应的安装包可以继续安装即可, 方式和上面一样



#### 6、项目中引入axios和element

将element的部分单独抽出来维护管理





## 6、基于文件的方式远程执行python文件



#### 1、远程脚本的维护

出于过程追踪和版本管理的便利性, 建议使用代码托管平台来维护脚本文件

本次预研使用gitee作为尝试, 后续实施会将代码托管到我们的内网平台



ps:  打开文件之后选择row，获取到对应的链接地址，后续就可以将这部分的代码进行调用

**git**

https://raw.githubusercontent.com/toxicray/python/master/bysms/manage.py

**gitee**

https://gitee.com/toxicray/python-demo/raw/master/remote.py



#### 2、远程脚本的执行

方法函数

```python
def load_module(url):
    u = urllib.request.urlopen(url)
    source = u.read().decode('utf-8')
    mod = sys.modules.setdefault(url, imp.new_module(url))
    code = compile(source, url, 'exec')
    mod.__file__ = url
    mod.__package__ = ''
    exec(code, mod.__dict__)
    return mod
```



验证运行环境和数据写入

基于pymysql库来执行,已确认可行,远程加载的脚本运行环境还是来自于当前的项目环境

```
def operatedb():
    conn = pymysql.connect(host='localhost',
                           user='root',
                           password='root',
                           db='test',
                           charset='utf8')
    # 创建一个游标
    cur = conn.cursor()
    # 插入一条数据
    sqli = "insert into student values(%s,%s,%s,%s)"
    cur.execute(sqli, ('3', 'Huhu', '2 year 1 class', '7'))
    cur.close()
    conn.commit()
    conn.close()
```





## 7、python工具

Jupyter本地的两种安装办法：
1.安装Anconda。conda包含科学计算的几乎所有包，包含jupyter。
2.仅安装了Python。可以pip install ipython, jupyter。安装即可。
命令行启动jupyter：jupyter notebook



我这边安装报错  应该是版本问题,目前是32位的



## 8、继承抽象类的写法

```python
class MyListener(Listener):

    def on_message_received(self, msg):
        print(msg)
```





## 9、can发送消息



消息发送注意事项, 发送的数据默认是10进制,  我们系统使用的默认为16进制,需要注意转换一下



```
# hex2dec
# 十六进制 to 十进制
def hex2dec(string_num):
    return int(string_num.upper(), 16)
```



```
import can
import numutil


bus = can.Bus(bustype='neovi', channel='1', bitrate=250000,
              receive_own_messages=True)
msg = can.Message(arbitration_id=0xc0ffee,
                      data=[0, numutil.hex2dec('ff'), 0, 1, 3, 1, 4, 1],
                      is_extended_id=True)
bus.send(msg)
```





## 10、数据转msg(TODO)

字符串切分



## 11、第一个案例



```
ID: 07e0    S                DLC:  8    03 22 fc 70 ff ff ff ff     Channel: 2
ID: 07e8    S                DLC:  8    10 14 62 fc 70 33 36 31     Channel: 2
ID: 07e0    S                DLC:  8    30 00 05 00 00 00 00 00     Channel: 2
ID: 07e8    S                DLC:  8    21 30 38 31 31 2d 45 34     Channel: 2
ID: 07e8    S                DLC:  8    22 39 30 31 30 39 30 30     Channel: 2
ID: 07e0    S                DLC:  8    03 22 fc 50 ff ff ff ff     Channel: 2
ID: 07e8    S                DLC:  8    10 10 62 fc 50 42 30 48     Channel: 2
ID: 07e0    S                DLC:  8    30 00 05 00 00 00 00 00     Channel: 2
ID: 07e8    S                DLC:  8    21 34 20 20 20 20 20 20     Channel: 2
ID: 07e8    S                DLC:  8    22 20 20 20 ff ff ff ff     Channel: 2
ID: 07e0    S                DLC:  8    03 22 f1 8a ff ff ff ff     Channel: 2
ID: 07e8    S                DLC:  8    10 0b 62 f1 8a 43 30 20     Channel: 2
ID: 07e0    S                DLC:  8    30 00 05 00 00 00 00 00     Channel: 2
ID: 07e8    S                DLC:  8    21 20 20 20 20 20 ff ff     Channel: 2
ID: 07e0    S                DLC:  8    03 22 fc 51 ff ff ff ff     Channel: 2
ID: 07e8    S                DLC:  8    10 0b 62 fc 51 42 30 48     Channel: 2
ID: 07e0    S                DLC:  8    30 00 05 00 00 00 00 00     Channel: 2
ID: 07e8    S                DLC:  8    21 34 20 20 20 20 ff ff     Channel: 2
```



## 12、串口通信

​		通过串口和tbox就可以进行交互



## 13、python的str转化

bytes转string

```python
string=str(b,'utf-8')

string=b.decode()

```

string转bytes

```python
b=bytes(string,"utf-8")

b=string.encode()
```



```python
bytes和str转化为bytearray都依赖于bytearray函数

bytes——》bytearray

bytes=b"aabbcc"

byarray=bytearray(bytes)

print(byarray)

bytearray(b'aabbcc')

str——》bytearray (encoding)

str="aabbcc"

byarray=bytearray(str)

print(byarray)

bytearray(b'aabbcc')
```

