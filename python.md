# python notes



[TOC]



## pycharm tutorial

参看官网链接:

https://www.jetbrains.com/help/pycharm/2017.2/meet-pycharm.html?utm_medium=help_link&utm_source=from_product&utm_campaign=PC&utm_content=2017.2

## yaml

## os



## logging

基本的logging使用：http://yu-liang.logdown.com/posts/195882/python-logging-module

Python logging模块详解：http://blog.csdn.net/zyz511919766/article/details/25136485/

python logging模块使用教程：http://www.jianshu.com/p/feb86c06c4f4

## cPickle

在python中，一般可以使用cPickle类来进行python对象的序列化，而cPickle提供了一个更快速简单的接口。cPickle可以对任意一种类型的python对象进行序列化操作，比如list，dict，甚至是一个类的对象等。而所谓的序列化，我的粗浅的理解就是为了能够完整的保存并能够完全可逆的恢复。在cPickle中，主要有四个函数可以做这一工作，下面使用例子来介绍。

* dump： 将python对象序列化保存到本地的文件。

  ~~~python
  import cPickle
  data = range(1000)
  cPickle.dump(data,open("test\\data.pkl","wb")) 
  ~~~

* load：载入本地文件，恢复python对象

  ~~~python
  data = cPickle.load(open("test\\data.pkl","rb"))
  ~~~

## argparse

* 基本使用

  ~~~python
  import argparse
  #创建一个解析器对象
  parser=argparse.ArgumentParser()
  #增加一个命令行参数
  parser.add_argument("echo",help="echo the string")
  #读取命令行参数
  args=parser.parse_args()
  print args.echo
  ~~~

* 高级使用

## argparse命令含参数模块 

参考链接：http://blog.chinaunix.net/uid-28437434-id-4542541.html

## 程序运行过程中询问是否继续

~~~python
if not raw_input('Start testing? (y/n)') == 'y':
	self.logger.info('Aborted!')
	exit()
~~~

##	os.path.join()使用

~~~python
#在拼接路径的时候用的。举个例子，
os.path.join("home","me","mywork")
#在Linux系统上会返回：“home/me/mywork"
~~~

## pycharm debug raw_input()

将PyCharm->Properties->Build->Console>"Always show debug console"打勾即可

## 属性`__dict__`

参看链接：

http://www.cnblogs.com/duanv/p/5947525.html



自己的理解，类变量：类可以直接访问，实例也可以访问，但是只有通过类才能改变这个属性值，而不能通过实例来改变这个值。

## python多进程

http://www.cnblogs.com/duanv/p/5947525.html

参考链接：http://cuiqingcai.com/3335.html

multiprocessing支持子进程、通信和共享数据、执行不同形式的同步，提供了Process、Queue、Pipe、Lock等组件。本节主要介绍Process。

* 基本使用

  在multiprocessing中，每一个进程都用一个Process类来表示。首先看下它的API。

  > Process([group [, target [, name [, args [, kwargs]]]]])

  - target表示调用对象，你可以传入方法的名字

  - args表示被调用对象的位置参数元组，比如target是函数a，他有两个参数m，n，那么args就传入(m, n)即可

  - kwargs表示调用对象的字典

  - name是别名，相当于给这个进程取一个名字

  - group分组，实际上不使用

一个简单的例子：

  ~~~python

  import multiprocessing

  def process(num):
      print 'Process:', num

  if __name__ == '__main__':
      for i in range(5):
          p = multiprocessing.Process(target=process, args=(i,))
          p.start()
  ~~~

最简单的创建Process的过程如上所示，target传入函数名，args是函数的参数，是元组的形式，如果只有一个参数，那就是长度为1的元组。然后调用start()方法即可启动多个进程了。

* 自定义类

  另外你还可以继承Process类，自定义进程类，实现run方法即可。用一个实例来感受一下：

~~~python

from multiprocessing import Process
import time

class MyProcess(Process):
    def __init__(self, loop):
        Process.__init__(self)
        self.loop = loop

    def run(self):
        for count in range(self.loop):
            time.sleep(1)
            print('Pid: ' + str(self.pid) + ' LoopCount: ' + str(count))

if __name__ == '__main__':
    for i in range(2, 5):
        p = MyProcess(i)
        p.start()
~~~

运行结果

~~~python
Pid: 28116 LoopCount: 0
Pid: 28117 LoopCount: 0
Pid: 28118 LoopCount: 0
Pid: 28116 LoopCount: 1
Pid: 28117 LoopCount: 1
Pid: 28118 LoopCount: 1
Pid: 28117 LoopCount: 2
Pid: 28118 LoopCount: 2
Pid: 28118 LoopCount: 3
~~~

* **deamon**属性

  在这里介绍一个属性，叫做deamon。每个线程都可以单独设置它的属性，如果设置为True，当父进程结束后，子进程会自动被终止。用一个实例来感受一下，还是原来的例子，增加了deamon属性：

  ~~~python

  class MyProcess(Process):
      def __init__(self, loop):
          Process.__init__(self)
          self.loop = loop
   
      def run(self):
          for count in range(self.loop):
              time.sleep(1)
              print('Pid: ' + str(self.pid) + ' LoopCount: ' + str(count))
   
   
  if __name__ == '__main__':
      for i in range(2, 5):
          p = MyProcess(i)
          p.daemon = True
          p.start()
      print 'Main process Ended!'
  ~~~

  在这里，调用的时候增加了设置deamon，最后的主进程（即父进程）打印输出了一句话。

  运行结果：

  ~~~python
  Main process Ended!
  ~~~

  结果很简单，因为主进程没有做任何事情，直接输出一句话结束，所以在这时也直接终止了子进程的运行。这样可以有效防止无控制地生成子进程。如果这样写了，你在关闭这个主程序运行时，就无需额外担心子进程有没有被关闭了。

* **join()方法**

  不过这样并不是我们想要达到的效果呀，能不能让所有子进程都执行完了然后再结束呢？那当然是可以的，只需要加入join()方法即可。在这里，每个子进程都调用了join()方法，这样父进程（主进程）就会等待子进程执行完毕。运行结果：

  ~~~python
  Pid: 29902 LoopCount: 0
  Pid: 29902 LoopCount: 1
  Pid: 29905 LoopCount: 0
  Pid: 29905 LoopCount: 1
  Pid: 29905 LoopCount: 2
  Pid: 29912 LoopCount: 0
  Pid: 29912 LoopCount: 1
  Pid: 29912 LoopCount: 2
  Pid: 29912 LoopCount: 3
  Main process Ended!
  ~~~

##python面向对象

* 可以直接给对象赋予属性，比如`dog.name="stone"`，也可以直接读取`print dog.name`。

* 访问权限问题

  > name：可以直接通过对象访问
  >
  > _name：可以通过对象直接访问，但是不建议这么做，暗示了是私有变量
  >
  > __name：不能通过对象直接访问，确定性的私有变量
  >
  > __name--（两个下划线无法打出来）：特殊变量，对象可以直接访问

* 使用`dir()`方法

  如果要获得一个对象的所有属性和方法，可以使用`dir()`函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法：

  ~~~python
  >>> dir('ABC')
  ['__add__', '__class__', '__contains__', '__delattr__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__getslice__', '__gt__', '__hash__', '__init__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_formatter_field_name_split', '_formatter_parser', 'capitalize', 'center', 'count', 'decode', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'index', 'isalnum', 'isalpha', 'isdigit', 'islower', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
  ~~~

  类似`__xxx__`的属性和方法在Python中都是有特殊用途的，比如`__len__`方法返回长度。在Python中，如果你调用`len()`函数试图获取一个对象的长度，实际上，在`len()`函数内部，它自动去调用该对象的`__len__()`方法，所以，下面的代码是等价的：

  ~~~python
  >>> len('ABC')
  3
  >>> 'ABC'.__len__()
  3
  ~~~

  剩下的都是普通属性或方法，比如`lower()`返回小写的字符串：

  ~~~python
  >>> 'ABC'.lower()
  'abc'
  ~~~

  ​

## 继承__init__函数和super

自己的理解。有两种方法可以调用父类的`__init__()`方法

1.通过`Father.__init__()`来调用父类的方法

2.通过super来调用父类被覆盖的方法

其他人的理解，参考以下两个链接。

参考链接1：http://www.cnblogs.com/encode/p/6048321.html

参考链接2： http://blog.csdn.net/abe_abd/article/details/52450354

## 装饰器

1.容易理解的版本：http://python.jobbole.com/82344/



装饰模式有很多经典的使用场景，例如插入日志、性能测试、事务处理等等，有了装饰器，就可以提取大量函数中与本身功能无关的类似代码，从而达到代码重用的目的。下面就一步步看看Python中的装饰器。

一个简单的需求



自己写的代码

~~~python
import time
def deco1(func):
    begin=time.time()
    func()
    end=time.time()
    print "Time eclipsed:",end-begin

def deco2(func):
    def wrapper(*args,**kwargs):
        begin=time.time()
        #!
        func(*args,**kwargs)
        end=time.time()
        print "Time eclipsed:", end - begin
    return wrapper

def deco3(flag):
    if flag:
        def wrapper_father(func):
            def wrapper_son(*args, **kwargs):
                begin = time.time()
                # !
                func(*args, **kwargs)
                end = time.time()
                print "Time eclipsed:", end - begin

            return wrapper_son
    else:
        def wrapper_father(func):
            return func
    return wrapper_father

def getsum1():
    n=100
    s=0
    for i in range(1,n+1):
        s+=i
    return s

def getsum2(n):
    s=0
    for i in range(1,n+1):
        s+=i
    return s

@deco2#===>deco2(getsum3)(n)
def getsum3(n):
    s = 0
    for i in range(1, n + 1):
        s += i
    return s

@deco3(False)
def getsum4(n):
    s = 0
    for i in range(1, n + 1):
        s += i
    return s

if __name__ == '__main__':
    print "-------------part1----------"
    deco1(getsum1)
    print "-------------part2-----------"
    deco2(getsum2)(2)
    print "-------------part3------------"
    getsum3(100)
    print "-------------part4------------"
    getsum4(100)
~~~





## 静态方法staticmethod和classmethod的区别

参看链接：http://blog.csdn.net/GeekLeee/article/details/52624742

参看知乎链接：https://www.zhihu.com/question/20021164

## 静态成员

实际上没有静态成员这个说法，静态成员就是类成员。类成员只能通过类对象来调用。不能通过对象实例来调用。



## socket网络编程

简单版本的介绍：http://blog.csdn.net/rebelqsp/article/details/22109925

简要的代码：

server

~~~python
# coding=utf-8
import socket  # socket模块
import commands  # 执行系统命令模块

HOST = '127.0.0.1'
PORT = 50007
# 定义socket类型，网络通信，TCP
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 套接字绑定的IP与端口
s.bind((HOST, PORT))
# 开始TCP监听
s.listen(1)
print 'listenning...'
while 1:
    # 接受TCP连接，并返回新的套接字与IP地址
    conn, addr = s.accept()
    # 输出客户端的IP地址
    print'Connected by', addr
    while 1:
        data = conn.recv(1024)  # 把接收的数据实例化
        # commands.getstatusoutput执行系统命令（即shell命令），返回两个结果，第一个是状态，成功则为0，第二个是执行成功或失败的输出信息
        cmd_status, cmd_result = commands.getstatusoutput(data)
        # 如果输出结果长度为0，则告诉客户端完成。此用法针对于创建文件或目录，创建成功不会有输出信息
        if len(cmd_result.strip()) == 0:
            conn.sendall('Done.')
        else:
            # 否则就把结果发给对端（即客户端）
            conn.sendall(cmd_result)  
    # 关闭连接
    conn.close()

~~~

client

~~~python
# coding=utf-8
import socket

HOST = '127.0.0.1'
PORT = 50007
# 定义socket类型，网络通信，TCP
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 要连接的IP与端口
s.connect((HOST, PORT))
while 1:
    # 与人交互，输入命令
    cmd = raw_input("Please input cmd:")
    # 把命令发送给对端
    s.sendall(cmd)
    # 把接收的数据定义为变量
    data = s.recv(1024)
    # 输出变量
    print data
# 关闭连接
s.close()
~~~



## select 模块

## 关键字参数

参考链接：http://www.jianshu.com/p/98f7e34845b5



## python定义caffe python层

参考链接：http://blog.csdn.net/liuheng0111/article/details/53090473



## Matplotlib使用

1.colormap的种类参考这里：

https://www.programcreek.com/python/example/61463/matplotlib.cm.hot

2.矩阵画图

~~~python
def plot_mat(mat):
    plt.figure()
    plt.imshow(mat, cmap=matplotlib.cm.jet)
    #plt.xticks([0, 1, 2, 3, 4, 5], ['x', 'y', 'z', 'u', 'v', 'w'])
    plt.colorbar()
    plt.show()
~~~



2.直方图看这里

https://stackoverflow.com/questions/5328556/histogram-matplotlib



### `__enter__`和`__exit__`

~~~python
with Session() as sess:
    pass
#这句话将Session()复制给sess,同时调用了__enter__方法
#进入主体之后,执行一些操作,操作之后会调用__exit__方法,这个方法可以关闭文件,清理资源等操作,也可以将错误信息传递到__exit__方法之中.
~~~

参考链接:http://www.cnblogs.com/lipijin/p/4460487.html