## ThreadLocal类

它是一个线程容器，它只能取自己线程的东西。常用的方法有`set`和`get`，它的get方法是不带参数的，因为它只存放一个数据。

```java
ThreadLocal<Integer> threadLocal = new ThreadLocal<>();	//创建一个ThreadLocal对象，类型为Integer
		threadLocal.set(1);
		// 创建10个线程
		for (int i = 0; i < 10; i++) {
			new Thread(new Runnable() {
				
				@Override
				public void run() {
					synchronized (threadLocal) {
						Integer result = threadLocal.get();
						System.out.println(result);
						if(result == null ) {
							result = 0;
						}
						result += 1;
						threadLocal.set(result);
						System.out.println(threadLocal.get());
					}
				}
			}).start();
		}
		while(Thread.activeCount()>1) {
			
		}
		System.out.println(threadLocal.get());
```

上面的代码输出的是1，因为ThreadLocal它只能放取自己线程的东西。



## 事务

### 概念

它是数据库操作的一个单元。一组要么同时执行，要么同时失败的sql语句。

### 如何使用

- 开始之前要把Connection对象的自动提交设置为false。`con.setAutoCommit(false)`
- 所有操作都执行完一起提交。`con.commit()`。
- 如果中间出现任何问题就要在catch中回滚 `con.rollback`。

### 特征

事务的特征（ACID）如下：

- Atomicity  原子性	同生共死
- Consistency 一致性    一个失败，就会回滚
- Isolation  隔离性    两个事务是隔离的
- Durability 持久性    事务完成之后，它对系统的影响是持久的

## ORM

ORM（Object Relation Mapping）一种模式，用于指导数据持久层。即它以面向对象的观点来操作数据库。

