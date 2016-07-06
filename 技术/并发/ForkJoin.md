

 ForkJoin
==

java7开始提供的forkjoin模式是一种任务分解并发执行模型，把大任务分解(Fork)为一个个的小任务(Task)，然后将小人物的结果合并(join)回来在逐层地返回回去


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


