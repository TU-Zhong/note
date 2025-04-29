官方解析：yield和sleep是Java中常用的线程控制方法,让我们逐个分析各选项的正误:  
  
B选项正确:sleep方法会导致当前线程暂停指定时间,在这段时间内线程会释放CPU资源,不会消耗CPU时间片。  
  
A选项错误:yield方法调用后,只是让当前线程让出CPU执行权,但不一定会发生线程切换。如果没有其他相同优先级的线程在等待CPU资源,该线程可能会继续执行。  
  
C选项错误:yield方法并不会导致线程暂停指定时间,它只是一个提示性的方法,建议让出CPU时间片。yield之后线程会直接进入就绪状态,随时可能再次获得CPU执行权。  
  
D选项错误:yield方法执行后,线程从running状态转为ready(就绪)状态,而不是waiting状态。这是一个重要的状态转换概念。  
  
主要区别:  
1. sleep会使线程进入计时等待状态,yield只是让线程回到就绪状态  
2. sleep<mark style="background: #FFF3A3A6;">一定会发生线程切换</mark>,而yield不一定  
3. sleep会暂停指定时间,而yield只是建议性让出CPU  
4. sleep会使线程进入TIMED_WAITING状态,而yield会使线程进入READY状态