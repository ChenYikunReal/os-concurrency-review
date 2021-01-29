# “生产者-消费者”问题
```java
semaphore mutex = 1;
semaphore empty = n;
semaphore full = 0;

producer() {
    while(1) {
        // TODO 生产数据
        P(empty);   // 获取空缓存区单元
        P(mmutex);  // 进入临界区
        // TODO 将数据放入缓存区
        V(mutex);   // 离开临界区，释放互斥信号量
        V(full);    // 满缓存区数+1
    }
}

consumer() {
    while(1) {
        P(full);    // 获取满缓冲区单元
        P(mutex);   // 进入临界区
        // TODO 从缓冲区中取出数据
        V(mutex);   // 离开临界区，释放互斥信号量
        V(empty);   // 空缓存区数+1
        // TODO 消费数据
    }
}
```

# “Reader-Writer”问题
```java
int count = 0;  // 记录当前读者数量

semaphore mutex = 1;  // 用于保护更新count变量时的互斥
semaphore rw = 1;  // 用于保护读者和写者互斥地访问文件
semaphore w = 1;    // 用于实现写优先

writer() {
    while(1) {
        P(w);   // 在无写进程请求时进入
        P(rw);  // 互斥访问文件
        // TODO 写入
        V(rw);  // 释放共享文件
        V(w);   // 恢复对共享文件的访问
    }
}

reader() {
    while(1) {
        P(w);   // 在无写进程请求时进入
        P(mutex);  // 互斥访问count变量
        if (count == 0) {  // 当第一个读者进程读取共享变量时
            P(rw);  // 阻止写进程写入
        }
        V(mutex);  // 释放互斥变量mutex
        V(w);   // 恢复对共享文件的访问
        // TODO 读取共享文件内容
        P(mutex);  // 互斥访问count变量
        count--;  // 读者计数器-1
        if (count == 0) {  // 当最后一个读者进程读完共享文件
            V(rw);  // 允许写进程写入
        }
        V(mutex);  // 释放互斥变量count
    }
}
```

# “哲学家就餐”问题
```java
semaphore mutex = 1;    // 设置取筷子的信号量
semaphore chopsticks[5] = {1, 1, 1, 1, 1};  // 定义信号量数组chopsticks[5]并初始化

Pi() {
    while(1) {
        P(mutex);   // 取筷子前获得互斥量
        P(chopsticks[i]);   // 取左边筷子
        P(chopsticks[(i+1)%5]);   // 取右边筷子
        V(mutex);   // 取完筷子释放信号量
        // TODO 进餐
        P(chopsticks[i]);   // 放回左边筷子
        P(chopsticks[(i+1)%5]);   // 放回右边筷子
        // TODO 思考哲♂学
    }
}
```

# “吸烟者”问题

```java
int random; // 存储随机数

semaphore offer1 = 0;    // 定义信号量对应烟草和纸组合的资源
semaphore offer2 = 0;    // 定义信号量对应烟草和胶水组合的资源
semaphore offer3 = 0;    // 定义信号量对应纸和胶水组合的资源
semaphore finish = 0;    // 定义信号量表示抽烟是否完成

P1() {
    while(1) {
        // TODO 随机化整数变量random∈[0,1,2]
        if (random == 0) {
            V(offer1);  // 提供烟草和纸
        } else if (random == 1) {
            V(offer2);  // 提供烟草和胶水
        } else {
            V(offer3);  // 提供纸和胶水
        }
        // TODO 任意两种材料放到桌子上
        P(finish);
    }
}

P2() {
    while(1) {
        P(offer3);
        // TODO 拿纸和胶水，卷成烟，抽掉
        V(finish);
    }
}

P3() {
    while(1) {
        P(offer2);
        // TODO 拿烟草和胶水，卷成烟，抽掉
        V(finish);
    }
}

P4() {
    while(1) {
        P(offer1);
        // TODO 拿烟草和纸，卷成烟，抽掉
        V(finish);
    }
}
```