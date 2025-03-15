---
title: "[자바] 자바 Thread"
excerpt: "Thread 의 동작과 제어"

categories:
  - java
tags:
  - [tag1, tag2]

permalink: /java/post-name-here-4/

toc: true
toc_sticky: true

date: 2025-03-15
last_modified_at: 2025-03-15
---

## 🦥 본문

# Thread 의 동작과 제어

### Process 와 Thread
* process 란 ?
  * 어떠한 주체에 의해 작성된 프로그램이 메모리를 할당받아 실행 중인 것
* thread 란 ?
  * process가 실행되는 주체
  * 하나의 process는 한 개 이상의 thread가 존재함 (Multi thread)
---
### thread 생성 및 호출 방법
Thread 클래스로부터 구현 객체를 갖는 생성자를 호출하면 됨.

1. Thread Class로 부터 Runnable 구현 객체를 매개값으로 하는 생성자 호출

```java
// 생성자를 통한 호출
Thread thread = new Thread(Runnable target);

// 익명 구현 객체를 통한 생성 방법
Thread thread = new Thread(new Runnable() {
  @Override
  public void run() {
    // 실행 코드
  }
});
```

2. Thread 자식 클래스로 생성
```java
public class WorkerThread extends Thread {
    @Override
    public void run(){
        // 실행 코드
    }
    Thread thread = new WorkerThread();
}
```
---
### Thread 상태
* Thread 를 생성하고 start() 메소드를 호출했을때 바로 실행되는 것이 아닌 _실행 대기 상태(RUNNABLE)_가 된다
* RUNNABLE 상태의 Thread는 CPU 스케쥴링에 따라 CPU를 점유하고 run() 메소드를 실행하여 실행 상태(RUNNING)가 된다. 
* run() 메소드가 종료되면 Thread는 실행을 멈추고 종료 상태(TERMINATED)가 된다.

#### Thread Method
* Java에서는 Thread 제어를 위한 여러가지 메서드를 제공한다.

<table>
  <tr>
    <td>구분</td>
    <td>메소드</td>
    <td>설명</td>
  </tr>
  <tr>
    <td rowspan="3">일시 정지로 보냄</td>
    <td>sleep(long millls)</td>
    <td>주어진 시간 동안 스레드를 일시 정지 상태로 만든다. 주어진 시간이 지나면 자동적으로 실행 대기 상태가 된다</td>
  </tr>
  <tr>
    <td>join()</td>
    <td>join()메소드를 호출한 스레드는 일시 정지 상태가 된다. 실행 대기 상태가 되려면, join() 메소드를 가진 스레드가 종료되어야함.</td>
  </tr>
  <tr>
    <td>wait()</td>
    <td>동기화 블록 내에서 스레드를 일시 정지 상태로 만듬</td>
  </tr>

  <tr>
    <td rowspan="2">일시 정지에서 벗어남</td>
    <td>interrupt()</td>
    <td>일시 정지 상태일 경우, InterruptedException을 발생시켜 실행 대기 상태 또는 종료 상태로 만듬</td>
  </tr>
  <tr>
    <td>notify(), notifyAll()</td>
    <td>wait() 메소드로 인해 일시 정지 상태인 스래드를 실행 대기 상태로 만듬</td>
  </tr>

  <tr>
    <td>실행 대기로 보냄</td>
    <td>yield()</td>
    <td>실행 상태에서 다른 스레드에게 실행을 양보하고 실행 대기 상태가 됨</td>
  </tr>
  
</table>

### Thread 동기화
* Multi Thread의 경우 객체를 공유해서 작업할 수 있다, 이 경우 다른 Thread에 의해 객체의 내용이 변경 될 수 있다
* 다른 Thread에 의해 내용이 변경되지 않으려면 객체에 잠금을 걸면된다
* Java는 이러한 문제를 해결하기 위해 동기화 메소드와 블록을 제공한다

#### 동기화 메소드 및 블록
* synchronized 키워드를 통해 동기화를 제공한다

#### 동기화 메소드
```java
package thread;

public class Calculator{
    private int memory;

    public int getMemory() {
        return memory;
    }


    // 동기화 메소드
    public synchronized void setMemory1(int memory) {
        this.memory = memory;
        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {

        }
        System.out.println(Thread.currentThread().getName() + ":" + this.memory);
    }

    public void setMemory2(int memory) {
        // 동기화 블록
        synchronized (this) {
            this.memory = memory;
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + ":" + this.memory);
            }
        }
    }
}

```

### Thread 안전 종료
* Java에서는 stop() 메소드를 통해 Thread의 종료 기능을 제공한다, 그러나 Thread를 갑작스럽게 종료하게 되면 리소스가 남아 불완전한 상태로 남겨진다
* Thread를 안전종료하기 위해선 조건을 통해 run() 메소드를 빨리 종료하거나 interrupt() 메소드를 사용한다

### ThreadPool
* 병렬 작업 처리가 많아지면 스레드 개수가 폭증하여 애플리케이션 성능이 저하된다, 스레드의 폭증을 막으려면 ThreadPoll을 사용하는 것이 좋다.
* 쓰레드풀은 작업 처리에 사용되는 스레드를 제한된 개수만큼 초기화 하고 Queue에 들어오는 작업을 스레드가 1개씩 처리하는 방식이다.

#### ThreadPool 생성 및 종료

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

ExecutorService threadPool = new ThreadPoolExecutor(
        3,                          // 코어 스레드 개수
        100,                        // 최대 스래드 개수
        120L,                       // 놀고 있는 시간
        TimeUnit.SECONDS,           // 놀고 있는 시간 단위
        new SynchronousQueue<>()    // 작업 큐
);
// 종료
threadPool.shutdown();
```

### ThreadPool 작업 생성과 처리 요청
* 작업은 Runnable 또는 Callable 구현 객처로 표현한다
* Runnable은 작업결과를 리턴하지 않고, Callable은 Future 타입을 리턴

