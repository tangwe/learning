## synchronized实现共享变量可见性原理 ##

### 实现共享变量可见性原理 ###

- 线程修改后的共享变量能够及时的从工作内容中刷新到主内存中

- 其他线程能够及时将共享变量的**最新值**从主内存中更新到自己的工作内存中 

### synchronized关键字 ###

代表这个方法加锁,相当于不管哪一个线程A每次运行到这个方法时,都要检查有没有其它正在用这个方法的线程B（或者C D等）,有的话要等正在使用这个方法的线程B（或者C D）运行完这个方法后再运行此线程A,没有的话,直接运行它包括两种用法：synchronized 方法和 synchronized 块。

- 原子性（同步，互斥）
- **可见性**

### JAVA内存模型关于synchronized的两条规定 ###

- 线程解锁前，必须把共享变量的最新值刷新到主内存
- 线程加锁时（进入synchronized代码块），将清空内存中共享变量的值，从而使用共享变量时需要从读取主内存中最新的值

### 线程执行互斥代码过程 ###

1. **获取锁**
2. **清空自己的工作内存**
3. **从主内存中copy共享变量的最新副本到自己的工作内存中**
4. **执行块中代码**
5. **将更改后的共享变量最新的值刷新到主内存中**
6. **最后释放互斥锁**

### code ###

    package cn.synchronizeddemo;

	public class SynchronizedDemo {
	private boolean ready = false;
	private int num = 1;
	private int result = 0;

	private synchronized void write() {
		ready = true;
		num = 2;
	}

	private synchronized void read() {
		if (ready) {
			result = num + 10;
		}
		System.out.println(result);
	}

	private class ReadWriteThread implements Runnable {
		private boolean flag;

		public ReadWriteThread(boolean flag) {
			super();
			this.flag = flag;
		}

		@Override
		public void run() {
			if (flag) {
				write();
			} else {
				read();
			}
		}

	}

	public static void main(String[] args) {
		SynchronizedDemo synchronizedDemo = new SynchronizedDemo();
		new Thread(synchronizedDemo.new ReadWriteThread(true)).start();
		new Thread(synchronizedDemo.new ReadWriteThread(false)).start();
		}
	}
