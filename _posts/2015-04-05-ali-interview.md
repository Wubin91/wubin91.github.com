---
layout: post
category: Interview
title: 2015阿里实习生C++研发笔试附加题
tags: [Interview,C++,algorithm]
tagline: by wubin
---

4 月 2 日晚 7 点，阿里 2015 实习生招聘线上笔试。由于自己的疏忽，没有投简历，所以只能从同学朋友圈盗笔试题的截图啦（感谢巨哥~），下次要引以为戒，提前做好准备。

这是这次 C++ 开发岗位笔试题的三道附加题，具体如下：

<!--more-->

**一、**分布式系统中的 RPC 请求经常出现乱序的情况。

写一个算法来将一个乱序的序列保序输出。例如：假设起始序号是 1，对于（1, 2, 5, 8, 10, 4, 3, 6, 9, 7）这个序列，输出是：

    1
    2
    3, 4, 5
    6
    7, 8, 9, 10

上述例子中，3 到来的时候会发现 4, 5 已经在了。因此将已经满足顺序的整个序列（3, 4, 5）输出为一行。

要求：

1. 写一个高效的算法完成上述功能，实现要尽可能的健壮、易于维护
2. 为该算法设计并实现单元测试

分析：在输出 1, 2 后，我们需要找到 3，但是在 3 之前已经存在 5, 4，即在找到所需的 x 之前，将位于 x 之前的 x + 1, x + 2, ... 排好序保存下来，这一功能可以借助于`“优先队列（priority queue）”`，紧接着，输出 3, 4, 5 之后，我们需要从 3 的位置开始往后找到 6，以此类推...

代码如下：

    #include<iostream>
    #include<vector>
    #include<queue>

    using namespace std;

    void sorted_out(int unsorted_array[], unsigned size);

    int main() {
        const unsigned size = 1;
        int unsorted_array[size] = {2};
        sorted_out(unsorted_array, size);
        return 0;
    }

    void sorted_out(int unsorted_array[], unsigned size) {
        int start = 1;
        priority_queue<int, vector<int>, greater<int> > pq;
        for (int i = 0; i < size; i++) {
            if (unsorted_array[i] == start) {
                cout<<unsorted_array[i];
                start++;
                while (!pq.empty() && pq.top() == start) {
                    cout<<','<<pq.top();
                    pq.pop();
                    start++;
                }
                cout<<endl;
            }
            else {
                pq.push(unsorted_array[i]);
            }
        }
    }

如果不太了解 priority_queue，请移步[priority_queue用法](http://blog.chinaunix.net/uid-533684-id-2100009.html)。

另附 priority 提供的一些函数：

    头文件:<queue>
    priority_queue();   //默认构造函数，生成一个空的排序队列
    priority_queue(const queue&);   //拷贝构造函数
    priority_queue& operator=(const priority_queue &);    //赋值运算符重载
    priority_queue 的私有成员：
    value_type;    //priority_queue中存放的对象类型，它和priority_queue中的T类型相同
    priority_queue(const Compare& comp);    //构造生成一个空的priority_queue对象，使用comp作为priority_queue的comparison
    priority_queue(const value_type* first, const value_type* last);    //带有两个参数的构造 函数，使用默认的Comparison作为第三个参数
    size_type;    //正整数类型，和Sequence::size_type类型一样。
    bool empty() const;    //判断优先级队列是否为空，为空返回true，否则返回false
    size_type size() const;    //返回优先级队列中的元素个数
    const value_type& top() const();    //返回优先级队列中第一个元素的参考值。
    void push(const value_type& x);    //把元素x插入到优先级队列的尾部，队列的长度加1
    void pop();    //删除优先级队列的第一个值，前提是队列非空，删除后队列长度减1

-------------------------------

**二、**常常会有频繁申请、释放内存的需求，比如在发送网络报文时，每次都要分配内存以存储报文，等报文发送完成后要需要删除报文。为了避免频繁的 new/delete 对系统带来的开销，需要实现一个通用的 FreeList 机制。使用者总是从 free list 中分配内存，如果存在没有使用的内存块就直接摘出来使用，如果没有的话再从系统中分配。使用完毕后并不去直接 delete 该内存块，而是交给 FreeList 保管。

要求：

1. 实现一个对固定大小内存块进行管理的通用 FreeList 类，给出定义和实现。要求不能使用 STL 中的容器类。定义类的接口和实现时注意通用性、健壮性和可测试性。
2. 如果该类的对象可能会被多个 thread 同时访问，请描述如何保证线程安全。有没有办法在保证线程安全的同时尽可能增大并发度？如果有也请描述你的思路。

分析：这道题的思路是先给 FreeList 分配一个大的内存空间，接下来使用的内存，都从该 FreeList 的空闲内存中获取，而 FreeList 需记录好哪些内存已被使用，哪些未被使用。`注意：此时的 FreeList 最好使用单例类，确保这个大的内存空间唯一。`

代码：

    #include<iostream>
    using namespace std;

    const int PointerArraySize = 1000;

    class FreeList {
    private:
        void *p;
        bool unused[PointerArraySize];
        FreeList() {
            // 构造函数是私有的
            p = (void *)new int[PointerArraySize];
            for(int i = 0; i < PointerArraySize; i++) {
                unused[i] = true;
            }
        }
        
    public:
        static FreeList *sharedInstance() {    // FreeList 为单例类
            static FreeList *freeList;
            if (freeList == NULL)       // 判读是否第一次调用
            freeList = new FreeList();
            return freeList;
        }

        void *getMemory(unsigned size) {
            for (int i = 0; i < PointerArraySize; i++) {    //遍历寻找是否有满足条件的内存块
                int cnt = 0;
                int start_i = i;
                while (unused[i] && i < PointerArraySize) {
                    cnt++;
                    i++;
                }
                if (cnt * PointerArraySize * sizeof(int) >= size) { //如果有，则拿来用
                    int tmp_start_i = start_i;
                    while (tmp_start_i < i) {
                        unused[i] = false;
                        tmp_start_i++;
                    }
                    return p + start_i; //返回分配的内存的起始地址
                }
            }
            void *q = (void *)new int[size / sizeof(int) + 1];  //如果没有满足条件的内存块，则由系统分配
            return q;
        }

        void delMemory(void *&del_p, unsigned size) {
            if(del_p - p < 1000 && del_p - p >= 0) {    //如果要回收的内存在 FreeList 内
                int length = size / sizeof(int) + 1;
                for(int i = del_p - p; i < length; i++) {
                    unused[i] = true;
                }
            }
            else {              //否则直接调用系统的回收内存函数
                delete[] del_p;
            }
            del_p = NULL;
        }
    };

如果该类的对象被多个 thread 同时访问，可以在 FreeList 设置一个信号量，它的值为 1。当进来一个 thread 需要 FreeList 分配内存时，我们首先查询该信号量是否为 1，如果为 1，那么将其减 1 后置为 0，为该 thread 分配内存。处理完后再将该值加 1 置为 1。如果 thread 查询到信号量为 0，那么分配内存的请求进入等待状态，直到其它 thread 结束后信号量的值恢复为 1。

此外，如果需要在保证线程安全的同时尽可能增大并发度，则可以设置多个 FreeList（例如 6 个），每个 FreeList 大小相同但相互独立。在这 6 个 FreeList 之上设置一个管理类（同样也是单例类）对它们进行管理，该管理类实时统计着每个 FreeList 中的剩余空间量，为用户每次内存分配请求决策出究竟使用哪一个 FreeList。同时，每一个 FreeList 都有它自己的信号量来处理在它上面内存分配的请求。

-------------------------------

**三、**假设这样一个场景：当很多用户并发获取服务，server 端资源不足时，希望用户能够按照预先分配的配额来使用资源。

比如预先定义好 user1，user2，user3 的配额是 20%，30%，50%，资源争抢时希望服务器有 20% 的服务能力分配给 user1，30% 给 user2，50% 给 user3。

但是如果某个时刻只有 user1 的请求，server 还是要把 100% 的服务能力分配给 user1 以充分利用资源；又如某个时间段只有 user2/user3 在访问服务，则按照 30%:50% 的比率来分配资源。

需要通过一个类似于队列的 ManagementQueue 类来封装上述功能。

入队的时候需要提供 user id（32位正整数）以及用户的任务（Task）。我们假设系统的用户数是有上限的，不会超过 10 个。

当队列中各个用户的请求均非空时，要求出队的 Task 分布符合用户的配额设置。延续上面的例子如果连续出队 100 次，要求 user1 的 Task 有 20 个左右，user2 的 Task 30 个左右，user3 的 50 个左右。

这里出队的 Task 恰好能对应服务器的服务能力。

要求：

1. 给出关键数据结构以及 ManagementQueue 的定义。用户任务 Task 可以认为是一个已经实现的类来使用。可以使用 STL 容器类。
2. 实现出队方法 Dequeue()，请尽可能写健壮的代码

注意：这里并不要求精确的按照比例分配任务，只要统计意义上满足预定义的配额比例就可以了。

分析：题目中假设系统的用户数是有上限的，不会超过 10 个。那么我们可以建立 10 条 Task 队列，每条队列中保存一个 user 的 Task。每次调度时产生一个随机数，通过随机数的大小来决定从哪条队列中选取一个 Task 进行出队操作。

例如：现在有 user1: 20%，user2: 30%，user3: 50%，那么我们有三条队列，分别保存这三个用户的 Task。每次调度时产生区间 [0, 10) 的随机数 x，若 0 <= x < 2 则调度 user1；若 2 <= x < 5 则调度 user2；若 5 <= x < 10 则调度 user3。

代码如下：

    #include<iostream>
    #include<vector>
    #include<queue>
    #include<set>
    #include<ctime>
    using namespace std;

    struct cmp {
        bool operator() (const int &u1_id, const int &u2_id) {
            return u1_id < u2_id;
        }
    };

    class ManagementQueue {
    private:
        vector<queue<Task> > queue_array(10);   //保持10个task队列
        set<int, cmp> userSet;
        const float proportion[10] = {0.8, 0.6, 1.0, 1.2, 0.4, 0.8, 0.6, 1.0, 1.2, 0.4};    //各个User的配额
        float all_proportion;       //当前队列中已有的User的配额和
        ManagementQueue() {
            //构造函数是私有的
            all_proportion = 0;
        }

    public:
        static ManagementQueue *sharedInstance() {    // FreeList 为单例类
            static ManagementQueue *m_queue;
            if (m_queue == NULL)       // 判读是否第一次调用
            m_queue = new ManagementQueue();
            return m_queue;
        }

        void inQueue(Task &t) {             //task入队函数
            queue_array[ut.user_id].push(t.user_id);
            set<User>::iterator find_iter = userSet.find(t.user_id);    如果集合中未有该User，则添加并刷新all_proportion
            if(find_iter == userSet.end()) {
                userSet.insert(t.user_id);
                all_proportion += proportion[t.user_id];
            }
        }

        void DeQueue() {

            srand((unsigned)time(NULL));
            int random_num = rand() % 10;   //产生一个10以内的随机数
            float start = 0;
            for(set<int>::iterator iter = userSet.begin(); iter != userSet.end(); iter++) {
                if(start <=  random_num && random_num < proportion[*iter] / all_proportion * 10) {  //判断随机数落入的区间
                    queue_array[*iter].pop();           //出队
                    if(queue_array[*iter].empty()) {
                        userSet.erase(*iter);
                        all_proportion -= proportion[*iter];
                    }
                    break;
                }
                start = proportion[*iter] / all_proportion * 10;
            }
        }
    };

