# 	          CompletableFuture并发编程



### 一、前言 函数式编程

在java1.8中有一个不得不说的特性,函数式编程,虽然说这个好像违背了java的oop思想,但是终究抵不过真香定律,尤其是各种集合操作,本次主要记录下函数式编程在并发编程中的应用

#### 函数式接口

先看一段代码

```
public class TestFunction {

    public static void main(String[] args) {
    //调用方法
        String result = testExecute(() -> "函数式编程");
        System.out.println(result);
    }
	// 接口作为参数值
    private static String testExecute(MyInterface myInterface) {
        return myInterface.execute();
    }
}

//函数式接口
@FunctionalInterface
interface MyInterface{
    public String execute();
}
```

当一个方法的入参直接是一个接口,那么问题来了,这个需要怎么去执行,是没有方法体的啊

所以调用的时候,我们传入的是一个lamda表达式,作为接口的实现类来执行代码逻辑,类似的有Runnable接口   Callable接口,Comparator接口

函数式接口的要求函数式接口**有且只能有一个抽象方法**,使用@FunctionalInterface来进行标示。



#### 函数式接口的使用

假如我们每个接口都去写,好像也太麻烦了,每次还需要些不同的函数式接口来进行调用,这个问题官方肯定早就想到了,所以提供了大量的函数式接口供我们调用.

```
java.util.function  包下面就有大量的函数式接口
```

这里就大概看下几个常用的吧

##### Runnable

Runnable，无参数，无返回值

```
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

##### Function

Function<T, R> 接受一个参数，并且有返回值

```
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```

##### Consumer

Consumer接受一个参数，没有返回值

```
@FunctionalInterface
public interface Consumer<T> {   
    void accept(T t);
}
```

##### Supplier

Supplier没有参数，有一个返回值

```
@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```

##### BiConsumer

BiConsumer<T, U> 接受两个参数（Bi， 英文单词词根，代表两个的意思），没有返回值

```
@FunctionalInterface
public interface BiConsumer<T, U> {
    void accept(T t, U u);
```



### 二、CompletableFuture入门

之前的文章有讲到关于java并发编程,我们通过juc的工具类来控制代码的同步和协作问题,但是那些 线程工具类还是有点晦涩,使用起来远不如CompletableFuture的语义清晰易懂

```
public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
	......
}
```

可以清晰的看到实现了Future和CompletionStage

##### Future

![1597585928166](C:\Users\tyrion\AppData\Roaming\Typora\typora-user-images\1597585928166.png)

##### CompletionStage

​			

![1597586006162](C:\Users\tyrion\AppData\Roaming\Typora\typora-user-images\1597586006162.png)

接口的参数就是我们上面提到的jdk所提供的函数式接口,所以类的具体执行还是我们传入的代码逻辑(接口的实现)

在之前的文章里面有提到 **并发编程的核心就是:   分工,同步,互斥**来控制线程的协作

用一个简单的示意图模拟一下异步任务

![1597586428428](C:\Users\tyrion\AppData\Roaming\Typora\typora-user-images\1597586428428.png)

任务1和任务2都有各自的串行任务,1和2之间属于并行关系,在任务1和任务2完成之后,任务三才能开始执行,如果使用juc下的工具类也能实现,但是现在有了CompletableFuture之后,我们可以轻而易举的实现各种串行并行关系



#### 串行关系

参数的接口含义看第一段

```
Runnable，无参数，无返回值
// 下一步执行,同步操作,仍然是当前的线程
CompletablFuture<Void> thenRun(Runnable action)
// 下一步改为异步   Executor executor是传入的执行线程池,  不传的话默认是ForkJoinPool
CompletableFuture<Void> thenRunAsync(Runnable action)
CompletableFuture<Void> thenRunAsync(Runnable action, Executor executor)
  
 
  //含义类似,参数不一样
  //Function<T, R> 接受一个参数，并且有返回值
<U> CompletableFuture<U> thenApply(Function<? super T,? extends U> fn)
<U> CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn)
<U> CompletableFuture<U> thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)

//Consumer接受一个参数，没有返回值 
CompletableFuture<Void> thenAccept(Consumer<? super T> action) 
CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action)
CompletableFuture<Void> thenAcceptAsync(Consumer<? super T> action, Executor executor)

//这个和thenApply的代码比较像,后面用代码来演示区别
<U> CompletableFuture<U> thenCompose(Function<? super T, ? extends CompletionStage<U>> fn)  
<U> CompletableFuture<U> thenComposeAsync(Function<? super T, ? extends CompletionStage<U>> fn)
<U> CompletableFuture<U> thenComposeAsync(Function<? super T, ? extends CompletionStage<U>> fn, Executor executor)
```



##### 串行的示例代码

```
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> {
           return "Hello";
        }).thenApply(string -> { // 同步执行
            printThread();
            return string + " world1";
        }).thenApplyAsync(string -> { // 异步执行
            printThread();
            return string + " world2";
        });
        System.out.println(completableFuture.get());
    }

    private static void printThread() {
        System.out.println(Thread.currentThread().getName());
    }
```

输出结果

main   说明是同步执行
ForkJoinPool.commonPool-worker-9   //代码的内置线程池,流式编程的.parallel()之后,并发流的执行线程池的执行者,线程数默认为服务器的逻辑核心数,可以人为设置  

Hello world1 world2   输出的结果,每一步的入参都是上一步的返回结果

其他的串行方法都是类似的,需要注意的就是thenApply()和thenCompose的区别

###### 两种方法的区别

```
CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> "Hello").thenApply(string -> string + " world");
接收上一步过程的结果作为参数来进行执行

CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> "Hello").thenCompose(string -> CompletableFuture.supplyAsync(() -> string + " world"))   //接收上一步过程,利用过程来作为参数  

看起来好像区别不大,业务场景没遇到过,个人感觉喜欢上面的写法,清晰明了一点

```



#### 聚合 And 关系

```
<U,V> CompletableFuture<V> thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)
<U,V> CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)
<U,V> CompletableFuture<V> thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn, Executor executor)

<U> CompletableFuture<Void> thenAcceptBoth(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action)
<U> CompletableFuture<Void> thenAcceptBothAsync(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action)
<U> CompletableFuture<Void> thenAcceptBothAsync( CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action, Executor executor)
  
CompletableFuture<Void> runAfterBoth(CompletionStage<?> other, Runnable action)
CompletableFuture<Void> runAfterBothAsync(CompletionStage<?> other, Runnable action)
CompletableFuture<Void> runAfterBothAsync(CompletionStage<?> other, Runnable action, Executor executor)
```



#### 聚合 Or 关系

```
<U> CompletableFuture<U> applyToEither(CompletionStage<? extends T> other, Function<? super T, U> fn)
<U> CompletableFuture<U> applyToEitherAsync(、CompletionStage<? extends T> other, Function<? super T, U> fn)
<U> CompletableFuture<U> applyToEitherAsync(CompletionStage<? extends T> other, Function<? super T, U> fn, Executor executor)

CompletableFuture<Void> acceptEither(CompletionStage<? extends T> other, Consumer<? super T> action)
CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action)
CompletableFuture<Void> acceptEitherAsync(CompletionStage<? extends T> other, Consumer<? super T> action, Executor executor)

CompletableFuture<Void> runAfterEither(CompletionStage<?> other, Runnable action)
CompletableFuture<Void> runAfterEitherAsync(CompletionStage<?> other, Runnable action)
CompletableFuture<Void> runAfterEitherAsync(CompletionStage<?> other, Runnable action, Executor executor)
```



#### 异常处理   

传统的多线程异常在线程池中是无法捕获的,该问题在该类中得到了解决

```
CompletableFuture<T> exceptionally(Function<Throwable, ? extends T> fn)
CompletableFuture<T> exceptionallyAsync(Function<Throwable, ? extends T> fn)
CompletableFuture<T> exceptionallyAsync(Function<Throwable, ? extends T> fn, Executor executor)
        
CompletableFuture<T> whenComplete(BiConsumer<? super T, ? super Throwable> action)
CompletableFuture<T> whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action)
CompletableFuture<T> whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action, Executor executor)
          
<U> CompletableFuture<U> handle(BiFunction<? super T, Throwable, ? extends U> fn)
<U> CompletableFuture<U> handleAsync(BiFunction<? super T, Throwable, ? extends U> fn)
<U> CompletableFuture<U> handleAsync(BiFunction<? super T, Throwable, ? extends U> fn, Executor executor)
```





### 三、CompletableFuture使用

上面的烧水泡茶过程我们使用上面的方法来实现一遍

```
//任务1：洗⽔壶->烧开⽔
CompletableFuture<Void> f1 =
        CompletableFuture.runAsync(() -> {
            System.out.println("T1:洗⽔壶...");
            sleep(1, TimeUnit.SECONDS);
            System.out.println("T1:烧开⽔...");
            sleep(15, TimeUnit.SECONDS);
        });
        
//任务2：洗茶壶->洗茶杯->拿茶叶
CompletableFuture<String> f2 =
        CompletableFuture.supplyAsync(() -> {
            System.out.println("T2:洗茶壶...");
            sleep(1, TimeUnit.SECONDS);
            System.out.println("T2:洗茶杯...");
            sleep(2, TimeUnit.SECONDS);
            System.out.println("T2:拿茶叶...");
            sleep(1, TimeUnit.SECONDS);
            return "⻰井";
        });
        
//任务3：任务1和任务2完成后执⾏：AND的并行关系
CompletableFuture<String> f3 =
        f1.thenCombine(f2, (__, tf) -> {
            System.out.println("T1:拿到茶叶:" + tf);
            System.out.println("T1:泡茶...");
            return "上茶:" + tf;
        });
        
//等待任务3执⾏结果
System.out.println(f3.join());
```