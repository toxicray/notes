# 								Hystrix学习





## 一, 启动创建项目

创建空的工程

创建eureka注册中心

创建服务实例

可以将idea的地址切换为 https://start.aliyun.com/    解决创建慢的问题

使用resttemplate进行服务间调用

使用eureka的ip加端口就能看到当前的eureka的服务情况

 指定调用方的 fallbackMethod  或者指定超时的时间





## 二,Hystrix的原理

我们对方法添加对应的注解, 该部分方法就会被切面进行处理

该部分的方法会被线程池进行执行

ps: 默认的线程是10, 可能会存在一种情况, 线程会被占用



##### 这种会有可能的问题, 

所有带@HystrixCommand的方法使用的是同一个线程池, 可能会有任务需要排队的问题



解决方案:

使用线程池来进行业务的隔离, 也就是叫做舱壁模型



## 三, Hystrix的工作流程

服务最好不要被压垮, 请求数并不会去增加服务端的处理能力, 毫无意义的压垮



#### 1,当调用出错,

开启一个时间窗 默认10s



#### 2,如果达到了最小请求数,统计失败的请求数是否达到阈值

1. 达到了  跳闸

2. 没有达到,重新进行开启统计,回到第一步

   

#### 3,跳闸之后:

​	每隔5s让一个请求通过

- 成功   重置断路器
- 失败 回到第三步



##### 参数:

所有参数来源于 HystrixCommandProperties类

```java
@HystrixCommand(
        commandProperties = {
                @HystrixProperty( value = "3000",name = "metrics.rollingStats.timeInMilliseconds"),
                @HystrixProperty( value = "2",name = "circuitBreaker.requestVolumeThreshold"),
                @HystrixProperty( value = "50",name = "circuitBreaker.errorThresholdPercentage"),
                @HystrixProperty( value = "5",name = "circuitBreaker.sleepWindowInMilliseconds"),
        }
)
```



## 四, 服务间调用的最佳实践

##### 使用RestTemplate的问题

1. 服务地址都是使用字符串,如果错误的话,那么只有到了测试的那一步才能发现

2. 一旦接口发生了变动,调用方的所有代码都得去修改

3. 请求参数的类型和返回值的类型都是消费者自己进行实现,容易出错

   我们希望的是



##### 使用CompnentScan   加Meta-inf来配置包扫描   详见autoconfiguration包





## 五, Feign和Hystrix的结合使用







## 六, 监控Hystrix来监控服务间的调用问题