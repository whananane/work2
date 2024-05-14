# 作业
## nosync-ex.c文件
### 代码
#include <stdio.h>
#include <pthread.h>

int sum = 0;
pthread_mutex_t mutex;

void* thread(void* arg) {  
    int i;  
    for (i = 0; i < 1000000; i++) {  
        pthread_mutex_lock(&mutex);  
        sum += 1;  
        pthread_mutex_unlock(&mutex);  
    }  
    return NULL;  
}  

int main(void) {  
    pthread_t tid1, tid2;  

    // 初始化互斥锁  
    
   pthread_mutex_init(&mutex, NULL); 

    // 创建线程
   pthread_create(&tid1, NULL, thread, NULL);
   pthread_create(&tid2, NULL, thread, NULL);

    // 等待线程结束
   pthread_join(tid1, NULL);
   pthread_join(tid2, NULL);

    // 销毁互斥锁
   pthread_mutex_destroy(&mutex);

   printf("1000000 + 1000000 = %d\n", sum);
   return 0;
}
### 结果截图
![这是图片]( https://github.com/whananane/work2/blob/main/nosync.png "nosync")
## mutex-ex
### 代码
#include <stdio.h>  
#include <pthread.h>  
int sum = 0;  
pthread_mutex_t mutex;  
void thread(void) {  
    int i;  
    for (i = 0; i< 1000000; i++) {  
    pthread_mutex_lock(&mutex);  
    sum += 1;  
    pthread_mutex_unlock(&mutex);  
    }  
}  
int main(void) {  
    pthread_t tid1, tid2;  
    pthread_mutex_init(&mutex, NULL);  
    pthread_create(&tid1, NULL, thread, NULL);  
    pthread_create(&tid2, NULL, thread, NULL);  
    pthread_join(tid1, NULL);  
    pthread_join(tid2, NULL);  
    printf("1000000 + 1000000 = %d\n", sum);  
    return (0);  
}
### 结果截图
![这是图片](https://github.com/whananane/work2/blob/main/mutex.png" "mutex")  
## sem-ex
### 代码
#include <stdio.h>  
#include <pthread.h>  
#include <semaphore.h>  
int sum = 0;  
sem_t sem;  
void thread(void) {  
    int i;  
    for (i = 0; i< 1000000; i++) {  
    sem_wait(&sem);  
    sum += 1;  
    sem_post(&sem);  
    }  
}  
int main(void) {  
    pthread_t tid1, tid2;  
    sem_init(&sem, 0, 1);  
    pthread_create(&tid1, NULL, thread, NULL);  
    pthread_create(&tid2, NULL, thread, NULL); 
    pthread_join(tid1, NULL);  
    pthread_join(tid2, NULL);  
    printf ("1000000 + 1000000 = %d\n", sum);   
    return (0);  
}  
### 结果截图
![这是图片](https://github.com/whananane/work2/blob/main/sem-ex.png "sem-ex") 
## 生产者消费者问题
#include <stdio.h>  
#include <stdlib.h>  
#include <pthread.h>  
#include <unistd.h>  

#define BUFFER_SIZE 10  // 缓冲区大小  
 
int buffer[BUFFER_SIZE];  // 缓冲区  
int count = 0;  // 缓冲区中的数据计数  

pthread_mutex_t mutex;  
pthread_cond_t cond_producer;  
pthread_cond_t cond_consumer;  

// 生产者线程函数  
void* producer(void* arg) {  
    int i;  
    for (i = 0; i < 20; i++) {  // 生产20个数据  
        pthread_mutex_lock(&mutex);  
        
        // 如果缓冲区已满，等待消费者消耗数据     
        
   while (count == BUFFER_SIZE) {  
          pthread_cond_wait(&cond_producer, &mutex);  
        }  

        // 生产数据    
   buffer[count] = i;  
        count++;  
        printf("Produced: %d\n", i);  

        // 通知消费者可以消费数据  
   pthread_cond_signal(&cond_consumer);  
        pthread_mutex_unlock(&mutex);  

        // 模拟生产时间  
   sleep(rand() % 2);  
    }  
    return NULL;   
}  

// 消费者线程函数  
void* consumer(void* arg) {  
    int i, data;  
    for (i = 0; i < 20; i++) {  // 消费20个数据  
        pthread_mutex_lock(&mutex); 

        // 如果缓冲区为空，等待生产者生产数据  
   while (count == 0) {  
            pthread_cond_wait(&cond_consumer, &mutex);  
        }  

        // 消费数据  
   data = buffer[--count];  
        printf("Consumed: %d\n", data);  

        // 通知生产者可以生产数据  
   pthread_cond_signal(&cond_producer);  
        pthread_mutex_unlock(&mutex);  

        // 模拟消费时间  
   sleep(rand() % 2);  
    }  
    return NULL;  
}  

int main() {  
    pthread_t tid_producer, tid_consumer;  

    // 初始化互斥锁和条件变量  
   pthread_mutex_init(&mutex, NULL);  
    pthread_cond_init(&cond_producer, NULL);  
    pthread_cond_init(&cond_consumer, NULL);  

    // 创建生产者和消费者线程  
   pthread_create(&tid_producer, NULL, producer, NULL);  
    pthread_create(&tid_consumer, NULL, consumer, NULL);  

    // 等待线程结束  
   pthread_join(tid_producer, NULL);  
    pthread_join(tid_consumer, NULL);  

    // 销毁互斥锁和条件变量  
   pthread_mutex_destroy(&mutex);  
    pthread_cond_destroy(&cond_producer);  
    pthread_cond_destroy(&cond_consumer);  

   return 0;  
}  
### 结果截图
![这是图片](https://github.com/whananane/work2/blob/main/inducer2.png "inducer1")
![这是图片](https://github.com/whananane/work2/blob/main/inducer1.png "inducer2") 
