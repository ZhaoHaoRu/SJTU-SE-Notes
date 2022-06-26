# 并行编程

### 共享变量存在数据竞争的原因

- 数据竞争产生的原因是由于对于共享变量操作的“非原子性”。非原子性操作是指，这样的操作在执行过程中是可能被其他线程打断的。
- **<atomic>**除非操作本身是原子的（即不可打断的，C++11中也提供库，该标准库中提供了一系列原子操作），否则对于共享变量（全局变量、静态变量、共享指针）的操作就会产生数据竞争的现象。此外函数内部的非静态局部变量则不会产生数据竞争现象，因为它们是**不共享**的。

### 并行编程的一些函数

- mutex mtx;
    - **lock_guard<mutex> lck (mtx);**
    - **mtx.lock();**
    - **mtx.unlock();**
- std::mutex mutex;
std::condition_variable cv;
    
    // 条件变量与临界区有关，用来获取和释放一个锁，因此通常会和mutex联用。
    std::unique_lock lock(mutex);
    // 此处会释放lock，然后在cv上等待，直到其它线程通过cv.notify_xxx来唤醒当前线程，cv被唤醒后会再次对lock进行上锁，然后wait函数才会返回。
    // wait返回后可以安全的使用mutex保护的临界区内的数据。此时mutex仍为上锁状态
    cv.wait(lock)
    
    // 所有等待在cv变量上的线程都会被唤醒。但直到lock释放了mutex，被唤醒的线
    程才会从wait返回。
    cv.notify_all(lock)
    

### cuckoo hash中的关键函数

- 并行get

```cpp
std::vector<std::thread> threads;
threads.clear();
for(int i = 1; i <= TOTAL; ++i){
	threads.emplace_back([&](int thread_id){
	printf("thread: %d get %d\n", thread_id, test.get(thread_id));
	}, i); 
}
for(int i = 0; i < TOTAL; ++i){
	threads[i].join(); 
}
return 0;
```

- kick

```cpp
KeyType evicted = key;
// determine which pos hash1 or hash2 to put key
// 0 is hash1, 1 is hash2
int which = 0;
// first evict key in hash1
int idx = hash1(evicted);
// != 0 means place has been occupied
// if there is a cycle, maybe cannot terminate
int pre_pos = -1;
while(T[idx] != 0){
	printf("evicted key %d from %d to %d\n", evicted, pre_pos, idx);
	swap(&evicted, &T[idx]);
	pre_pos = idx;
	which = 1 - which;
	idx = (which == 0)?hash1(evicted):hash2(evicted); 
}
	printf("evicted key %d from %d to %d\n", evicted, pre_pos, idx);
	T[idx] = evicted;
}

```

- kick基于回溯的实现

```cpp
void Cuckoo::bt_evict(const KeyType &key, int which, int pre_pos){
	int idx = (which == 0)?hash1(key):hash2(key);
	// basic case: find a empty pos for the last evicted element
	if(T[idx] == 0){
		printf("evicted key %d from %d to %d\n", key, pre_pos, idx);
		T[idx] = key;
		return; 
	}
	printf("evicted key %d from %d to %d\n", key, pre_pos, idx);
	KeyType cur = T[idx];
	// first evict latter elements
	bt_evict(cur, 1 - which, idx);
	T[idx] = key; 
}
```

- rehash

```cpp
void hash_table::rehash(){
        tab *temp1=new tab[size];
        tab *temp2=new tab[size];
        for(int j=0;j<size;++j)
            temp1[j]=table0[j];
        for(int j=0;j<size;++j)
            temp2[j]=table1[j];
        table0=new tab[size*2];
        table1=new tab[size*2];
        size=size*2;
        //int j=0;
        for(int i=0;i<size/2;++i){
            if(temp1[i].val!=-1)
                h_insert(temp1[i].key,temp1[i].val);
        }
        for(int i=0;i<size/2;++i){
            if(temp2[i].val!=-1)
                h_insert(temp2[i].key,temp2[i].val);
        }
        delete []temp1;
        delete []temp2;
    }
```

### 最小生成树

- 时间复杂度
    - **Kruskal算法 $O(m log m)$**
    - Prim算法$**O(n^2)**$
    

### pageRank

- 在pageRank矩阵中，列为出度，行为入度
    - 如：
    
    ![Untitled](%E5%B9%B6%E8%A1%8C%E7%BC%96%E7%A8%8B%202f5fad0582ac47fbb0cdfd7ba0469f1d/Untitled.png)
    

![Untitled](%E5%B9%B6%E8%A1%8C%E7%BC%96%E7%A8%8B%202f5fad0582ac47fbb0cdfd7ba0469f1d/Untitled%201.png)

- 一个并行编程分组的函数示例

```cpp
std::pair<int, int> get_range(int sub_id, int partitions, int total_len){
	assert(partitions > 0 && total_len > 0);
	int each_siz = total_len / partitions;
	int start = sub_id * each_siz;
	// > total_len is impossible
	int end = (sub_id + 1) * each_siz;
	// special case is the last partition
	end = (sub_id == partitions - 1)?total_len:end;
	return std::make_pair(start, end); 
}
```