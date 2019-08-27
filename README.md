# Queue定义

 ***Queue： 基本上，一个队列就是一个先入先出（FIFO）的数据结构。***<br>
 ***Queue接口与List、Set同一级别，都是继承了Collection接口。LinkedList实现了Deque接 口。***<br>
 
 ---------------------------------------------------------------------------------------------------
## Queue类型

 ArrayBlockingQueue ：一个由数组支持的有界队列。<br>
 LinkedBlockingQueue ：一个由链接节点支持的可选有界队列。<br>
 PriorityBlockingQueue ：一个由优先级堆支持的无界优先级队列。<br>
 DelayQueue ：一个由优先级堆支持的、基于时间的调度队列。<br>
 SynchronousQueue ：一个利用 BlockingQueue 接口的简单聚集（rendezvous）机制<br>
 LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。<br>
 
 ---------------------------------------------------------------------------------------------------
## Queue的操作

  add(E)        增加一个元索                     如果队列已满，则抛出一个IIIegaISlabEepeplian异常   <br>
  remove()      移除并返回队列头部的元素          如果队列为空，则抛出一个NoSuchElementException异常  <br>
  element()     返回队列头部的元素               如果队列为空，则抛出一个NoSuchElementException异常 <br>
  offer(E)      添加一个元素并返回true           如果队列已满，则返回false          <br>              
  poll()        移除并返问队列头部的元素          如果队列为空，则返回null       <br>                   
  peek()        返回队列头部的元素               如果队列为空，则返回null      <br>                    
  put()         添加一个元素                     如果队列满，则阻塞          <br>                      
  take()        移除并返回队列头部的元素          如果队列为空，则阻塞     <br>
  
---------------------------------------------------------------------------------------------------
### Queue与Thread搭配(顺序执行，执行完移除)

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        LinkedBlockingQueue<Thread> queue = new LinkedBlockingQueue();
        MyThread m1 = new MyThread(1);
        MyThread m2 = new MyThread(2);
        MyThread m3 = new MyThread(3);
        MyThread m4 = new MyThread(4);
        MyThread m5 = new MyThread(5);
        MyThread m6 = new MyThread(6);
        queue.offer(m1); //添加元素，add put 都走offer
        queue.offer(m1);
        queue.offer(m2);
        queue.offer(m3);
        queue.offer(m3);
        queue.offer(m4);
        queue.offer(m5);
        queue.offer(m6);
        queue.offer(m6);
        queue.offer(m6);
        queue.offer(m6);
        queue.offer(m6);
        int size = queue.size();
        for (int i = 0; i < size; i++) {
            queue.peek().start(); //顶项执行,peek减少异常为空的情况，比element()安全
            queue.poll(); //去掉顶项 take ,remove
        }
     }    
```
----------------------------------------------------------
```
  结果显示：  
  2019-08-27 14:11:29.261 9559-9574/? D/lylog: 任务1<br>
  2019-08-27 14:11:29.261 9559-9575/? D/lylog: 任务1<br>
  2019-08-27 14:11:29.262 9559-9576/? D/lylog: 任务2<br>
  2019-08-27 14:11:29.263 9559-9577/? D/lylog: 任务3<br>
  2019-08-27 14:11:29.263 9559-9578/? D/lylog: 任务3<br>
  2019-08-27 14:11:29.264 9559-9579/? D/lylog: 任务4<br>
  2019-08-27 14:11:29.264 9559-9580/? D/lylog: 任务5<br>
  2019-08-27 14:11:29.265 9559-9582/? D/lylog: 任务6<br>
  2019-08-27 14:11:29.265 9559-9581/? D/lylog: 任务6<br>
  2019-08-27 14:11:29.266 9559-9583/? D/lylog: 任务6<br>
  2019-08-27 14:11:29.266 9559-9584/? D/lylog: 任务6<br>
  2019-08-27 14:11:29.266 9559-9585/? D/lylog: 任务6<br> 
```
---------------------------------------------------------- 
                                 
  __LinkedBlockingDeque 和 LinkedBlockingQueue 没固定队列大小，执行添加完成的元素为止，改成ArrayBlockingQueue，需要添加数目大小，当越界时候，抛出异常。__

-----------------------------------------------------------
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        PriorityBlockingQueue<Thread> queue = new PriorityBlockingQueue();
        MyThread m1 = new MyThread(1);
        MyThread m2 = new MyThread(2);
        MyThread m3 = new MyThread(3);
        MyThread m4 = new MyThread(4);
        MyThread m5 = new MyThread(5);
        MyThread m6 = new MyThread(6);
        queue.offer(m1);
        queue.offer(m1);
        queue.offer(m2);
        queue.offer(m3);
        queue.offer(m3);
        queue.offer(m4);
        queue.offer(m5);
        queue.offer(m6);
        queue.offer(m6);
        queue.offer(m6);
        queue.offer(m6);
        queue.offer(m6);
        int size = queue.size();
        for (int i = 0; i < size; i++) {
            queue.peek().start();
            queue.poll();
        }
    }
}
class MyThread extends Thread implements Comparable<MyThread>{
    int i = 0;

    public MyThread(int i) {
        this.i = i;
    }

    @Override
    public void run() {
        super.run();
        Log.d("lylog", "任务" + i);

    }

    @Override
    public int compareTo(MyThread o) {
        if(o.i == 6){
            return 1; //优先
        }else{
            return -1;
        }
    }
}
```
-------------------------------------------------------------
```
  结果显示：  
  2019-08-27 14:35:38.160 11616-11631/com.ly.workqueue D/lylog: 任务6<br>
  2019-08-27 14:35:38.160 11616-11632/com.ly.workqueue D/lylog: 任务6<br>
  2019-08-27 14:35:38.160 11616-11633/com.ly.workqueue D/lylog: 任务6<br>
  2019-08-27 14:35:38.161 11616-11634/com.ly.workqueue D/lylog: 任务6<br>
  2019-08-27 14:35:38.161 11616-11635/com.ly.workqueue D/lylog: 任务6<br>
  2019-08-27 14:35:38.161 11616-11636/com.ly.workqueue D/lylog: 任务1<br>
  2019-08-27 14:35:38.162 11616-11637/com.ly.workqueue D/lylog: 任务3<br>
  2019-08-27 14:35:38.162 11616-11638/com.ly.workqueue D/lylog: 任务4<br>
  2019-08-27 14:35:38.162 11616-11639/com.ly.workqueue D/lylog: 任务2<br>
  2019-08-27 14:35:38.162 11616-11640/com.ly.workqueue D/lylog: 任务5<br>
  2019-08-27 14:35:38.163 11616-11641/com.ly.workqueue D/lylog: 任务1<br>
  2019-08-27 14:35:38.163 11616-11642/com.ly.workqueue D/lylog: 任务3<br>
```
-------------------------------------------------------------
