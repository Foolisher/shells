

 ForkJoin
==

java7开始提供的forkjoin模式是一种任务分解并发执行模型，把大任务分解(Fork)为一个个的小任务(Task)，然后将小人物的结果合并(join)回来在逐层地返回回去，我们常见提供并发的手段有分解任务，减低锁竞争，在该种并发模式中由于独自线程处理独自的任务，没有了锁竞争，同时分解任务，让多线程可以在这些任务上执行，对空间进行裁剪，对时间也相应的缩小。

这是在java5-6版本之后一种较为新的并发模型，该种并发模型适用于大任务的并发执行，并且各个子任务之间互不干扰，该模型还支持工作窃取算法，某线程做完了自己的任务队列，还可以从其他队列里获取任务来执行，这样均衡了任务调度，避免任务倾斜，使并发度更加平衡

为了保证一个任务的子任务都必须执行完成才算该任务是完成的，例如如下图若需执行到【任务1结果】那么必须等待【子任务1.1】+【子任务1.2】都执行完成即代码中 `task_1_1.join(); task_1_2.join()` 执行完毕能算【子任务1】执行完成，才能进行下一步，但是问题来了：若【子任务1.1】耗时很长，甚至一直执行不完成，那么即使【子任务1.2】执行完成了，`task_1_2.join()` 线程将一直等待状态，此时即使【子任务2】、【子任务3】都执行完成了，但由于【子任务1】里面有个任务还未执行完成，它们也必须等待，此时整个ForkJoinPool为当前工作的线程将处于hang状态，若此时还有客户端提交了一个任务也将因为线程池线程限制无法继续执行，当然若我们把线程池设置的很大的话，又有造成线程泄露的风险。

通过以上的描述我们可得知，在执行时间不可控的任务使用ForkJoin是有风险的，该中并发模型必须要保证每个任务是可终止的，并且各个任务的空间粒度与时间粒度应该是相近的，例如子任务设置相应的超时机制，若发现异常就快速失败

ForkJoin并发模式应当尽量避免在任务里面有锁竞争，鉴于该并发模型的设计初衷就是为了划分任务边界，减少锁竞争，若我们再在任务里面加入一些资源锁，那样有造成死锁的风险，当然只是有可能，这就需要开发者自己把握了。




![](http://cdn2.infoqstatic.com/statics_s1_20160622-0236/resource/articles/fork-join-introduction/zh/resources/21.png)


```
		class AddTask extends RecursiveTask<Integer> {

			private final int[] arr;
			private final int   start;
			private final int   end;

			public AddTask(int arr[], int start, int end) {
				this.arr = arr;
				this.start = start;
				this.end = end;
			}

			protected Integer compute() {
				if (end - start <= 1) {
					return start == end ? arr[start] : arr[end] + arr[start];
				}
				AddTask addTask1 = new AddTask(arr, start, (end - start) / 2);
				AddTask addTask2 = new AddTask(arr, (end - start) / 2 + 1, end);

				ForkJoinTask<Integer> fork1 = addTask1.fork();
				ForkJoinTask<Integer> fork2 = addTask2.fork();

				return fork1.join() + fork2.join();
			}
		}


		ForkJoinPool forkJoinPool = new ForkJoinPool(4);
		int[]        arr          = new int[100];
		for (int i = 0; i < 100; i++) {
			arr[i] = i + 1;
		}
		forkJoinPool.submit(new AddTask(arr, 0, arr.length-1));

```


