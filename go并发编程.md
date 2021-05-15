# 								golang并发编程

## 概述

​		并发编程本身应该是一个脱离语言层面的技术点, 为了充分发挥多核cpu的性能,也就是为了极限压榨CPU的性能, 而go语言从语言层面来支持并发编程,采用协程的方式调度,调度处理器需要了解下

​		go的并发编程和java对比的话, java的线程是和内核的线程一一对应,属于重量级的线程,资源消耗比较严重,而go的协程比较轻量级,并发性能更为良好.



## 一、golang的互斥锁

​		互斥锁的本质可以理解为将某段代码改为单行道,这段代码(资源)同一时间只可以被一个线程来进行访问,这样就可以避免由于线程调度带来的执行结果不确定性.

​		

```
func testMutex() {
   count := 0
   var wg sync.WaitGroup
   wg.Add(10)
   for i := 0; i < 10; i++ {
      go func() {
         defer wg.Done()
         for i := 0; i < 10000; i++ {
            count++
         }
      }()
   }
   wg.Wait()
   fmt.Print(count)
}
// 如果不加锁的时候,那么输出结果就是不确定的值
```



```
func testMutex1() {
   count := 0
   var wg sync.WaitGroup
   var mu sync.Mutex
   wg.Add(10)
   for i := 0; i < 10; i++ {
      go func() {
         defer wg.Done()// 函数执行完了之后会去执行
         for i := 0; i < 10000; i++ {
            mu.Lock()
            count++
            mu.Unlock()
         }
      }()
   }
   wg.Wait()
   fmt.Print(count)
}
```

```
type Counter struct {
	count uint64
	mu sync.Mutex
}


func testMutex2() {
   var counter Counter
   var wg sync.WaitGroup
   wg.Add(10)
   for i := 0; i < 10; i++ {
      go func() {
         defer wg.Done()
         for i := 0; i < 10000; i++ {
            counter.mu.Lock()
            counter.count++
            counter.mu.Unlock()
         }
      }()
   }
   wg.Wait()
   fmt.Print(counter.count)
   fmt.Print(counter)
}
```