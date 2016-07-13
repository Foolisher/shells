# 《ForkJoin》

## 1.并发的目的

java7开始提供的forkjoin并发模型是一种任务分解并发执行模型，把大任务分解\(Fork\)为一个个的小任务\(Task\)，然后将小任务的结果合并\(join\)而逐层地返回回去。我们常见提高并发的手段有**减小任务粒度**，**降低锁竞争**，在该种并发模式中由于独自线程处理独自的任务，避免了对任务获取的锁竞争，同时通过分解任务让这些任务并发地执行，提高执行效率。

该种并发模型适用于大任务的并发执行，并且各个子任务之间互不干扰，该模型还支持工作窃取算法，某线程做完了自己的任务队列，还可以从其他队列里获取任务来执行，这样均衡了任务调度，避免任务倾斜，使并发度更加平衡。工作窃取算法还有一个有趣的特点是它会从别的线程的任务的另一端获取任务，这样若他们获取的不是同一个任务的话就可以避免任务队列的锁竞争了。

## 2.Fork\/Join模式的注意点

**但是**为了保证一个任务的子任务都必须执行完成才算该任务是完成的，例如如下图若需执行到【任务1结果】那么必须等待【子任务1.1】+【子任务1.2】都执行完成即代码中 `task_1_1.join(); task_1_2.join()` 执行完毕能算【子任务1】执行完成，才能进行下一步，但是问题来了：若【子任务1.1】耗时很长，甚至一直执行不完成，那么即使【子任务1.2】执行完成了，`task_1_2.join()` 线程将一直等待状态，此时即使【子任务2】、【子任务3】都执行完成了，但由于【子任务1】里面有个任务还未执行完成，它们也必须等待，此时整个ForkJoinPool为当前工作的线程将处于hang状态，若此时还有客户端提交了一个任务也将因为线程池线程限制无法继续执行，当然若我们把线程池设置的很大的话，又有造成线程泄露的风险。

通过以上的描述我们可得知，在执行时间不可控的任务使用ForkJoin是有风险的，该中并发模型必须要保证每个任务是可终止的，并且各个任务间的空间粒度与时间粒度应该是相近的，例如子任务设置相应的超时机制，若发现异常就快速失败

ForkJoin并发模式应当尽量避免在任务里面有锁竞争，鉴于该并发模型的设计初衷就是为了划分任务边界，减少锁竞争，若我们再在任务里面加入一些资源锁，那样有造成死锁的风险，当然只是有可能，这就需要开发者自己把握了。



![](http://fuxiao.oss-cn-shanghai.aliyuncs.com/book/Forkjoin.png)



## 实践编程

1. ddd

2. dd
3. dd
4. 

```java


        class AddTask extends RecursiveTask<Integer> {

            private final Task[] arr;
            private final int    start;
            private final int    end;

            public AddTask(Task arr[], int start, int end) {
                this.arr = arr;
                this.start = start;
                this.end = end;
            }

            protected Integer compute() {
                if (end - start <= 50) {
                    int a = 0;
                    for (int i = start; i <= end; i++) {
                        a += arr[i].doSomeThing(1);
//                        a += i == 100 ? arr[i].doSomeThing(300000000) : arr[i].doSomeThing(1);
                    }
                    return a;
                }
                AddTask addTask1 = new AddTask(arr, start, (end - start) / 2 + start);
                AddTask addTask2 = new AddTask(arr, (end - start) / 2 + 1 + start, end);

                ForkJoinTask<Integer> fork1 = addTask1.fork();
                ForkJoinTask<Integer> fork2 = addTask2.fork();

                return fork1.join() + fork2.join();
            }
        }


        ForkJoinPool forkJoinPool = new ForkJoinPool(10);
        int          len          = 5000;
        Task[]       arr          = new Task[len];
        for (int i = 0; i < len; i++) {
            arr[i] = (t) -> {
                try {
                    Thread.sleep(t);
                } catch (InterruptedException e) {
                }
                return t;
            };
        }

        Stopwatch             started = Stopwatch.createStarted();
        ForkJoinTask<Integer> submit  = forkJoinPool.submit(new AddTask(arr, 0, arr.length - 1));

        forkJoinPool.submit(new AddTask(arr, 0, arr.length - 1));

        submit.get();
        System.out.println("并发耗时:   " + started.elapsed(TimeUnit.MILLISECONDS) + " 毫秒");

        Stopwatch start = started.reset().start();
        for (Task task : arr) {
            task.doSomeThing(1);
        }

        System.out.println("串行耗时:   " + start.elapsed(TimeUnit.MILLISECONDS) + " 毫秒");


        start = started.reset().start();
        Arrays.stream(arr).parallel().forEach((action) -> {
            action.doSomeThing(1);
        });
        System.out.println("并行流耗时: " + start.elapsed(TimeUnit.MILLISECONDS) + " 毫秒");


并发耗时:   1311 毫秒
串行耗时:   6170 毫秒
并行流耗时: 1525 毫秒

```

## 1.几种并发模型

### 1.1.裸线程模型

自己创建线程，实现了异步，将主线程与异步任务在执行方式上解耦

### 1.2.JDK Executors

线程的规划，例如线程池，执行调度，结果获取，异常处理都交由一个成熟稳定的框架来管理，这样实现了在算法上的解耦，我们不关注线程框架内部细节，只管提供任务给它就行了

### 1.3.Forkjoin 分治策略

目的是划分细粒度线程，划分任务边界，避免锁竞争，通过线程独占任务队列减少锁竞争，实现了并发任务执行空间上的解耦

### 1.4.Actor 基于事件模型的并发

异步消息概念，基于事件模型的并发，但它的内部实现也是基于ForkJoin的，但是Actor的优点是支持基于RMI的分布式并发，这在分布式并发计算场景非常实用，例如Spark的分布式计算就是实用该模型，当然Actor模型在ForkJoin中存在的问题有着更严重的体现，由于是基于消息对时空解耦，这在调试，问题排查上就会比较困难，而且需要保证避免全局变量与锁竞争

## 2.ForkJoin 实现原理

### 2.1.工作队列

### 2.2.Fork

### 2.3.Join

