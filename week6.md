# **Process Synchronization**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. 데이터의 접근**

![](https://images.velog.io/images/dogfootbirdfoot/post/cbc003ef-c951-4330-a5d6-6208b2d21803/6-1.png)

**Example :**

||E-box|S-box|
|:--:|:--:|:--:|
|1|CPU|Memory|
|2|컴퓨터 내부|디스크|
|3|프로세스|그 프로세스의 주소 공간|

&nbsp;

## **2. Race Condition**

![](https://images.velog.io/images/dogfootbirdfoot/post/60651059-f3c6-470d-b327-7f0ec7842b5f/6-2.png)

OS에서 race condition은 언제 발생하는가?

1. kernel 수행 중 인터럽트 발생 시
2. Process가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
3. Multiprocessor에서 shared memory 내의 kernel data

### **2-1. OS에서의 race condition 1**

**interrupt handler vs kernel**

![](https://images.velog.io/images/dogfootbirdfoot/post/c0d777e4-b74b-41b5-a5e0-4686a4a27e4a/6-3.png)

* 커널모드 running 중 interrupt가 발생하여 인터럽트 처리루틴이 수행
	
    * 양쪽 다 커널 모드이므로 kernel address space 공유

### **2-2. OS에서의 race condition 2**

**Preempt a process running in kernel?**

![](https://images.velog.io/images/dogfootbirdfoot/post/a185096c-95b8-43fb-9448-7ba95b1d4ec4/6-4.png)

* 두 프로세스의 address space 간에는 data sharing이 없음

* 그러나 system call을 하는 동안에는 kernel address space의 data를 access하게 됨 (share)

* 이 작업 중간에 CPU를 preempt 해가면 race condition 발생

**If you preempt CPU while in kernel mode ...**

![](https://images.velog.io/images/dogfootbirdfoot/post/0e70f70c-e278-459f-a2bf-e86c7f9d7f4f/6-5.png)

해결책

* 커널 모드에서 수행 중일 때는 CPU를 preempt하지 않음

* 커널 모드에서 사용자 모드로 돌아갈 때 preempt

### **2-3. OS에서의 race condition 3**

**multiprocessor**

![](https://images.velog.io/images/dogfootbirdfoot/post/fcdc7c9f-e6d6-4407-b174-d7fd97641417/6-6.png)

어떤 CPU가 마지막으로 count를 store했는가? ➔ race condition

multiprocessor의 경우 interrupt enable/disable로 해결되지 않음

방법 1

* 한번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법

방법 2

* 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock / unlock을 하는 방법

&nbsp;

## **3. Process Synchronization 문제**

* 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다.

* 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process) 간의 실행 순서(orderly execution)를 정해주는 메커니즘 필요

* Race condition
	
    * 여러 프로세서들이 동시에 공유 데이터를 접근하는 상황
	
    * 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐

* race condition을 막기 위해서는 concurrent process는 동기화(synchronize)되어야 한다.

**Example of a Race Condition**

![](https://images.velog.io/images/dogfootbirdfoot/post/cadbb7b1-8ce1-470d-b9a8-7ede36616103/6-7.png)

사용자 프로세스 P1 수행중 timer interrupt가 발생해서 context switch가 일어나서 P2가 CPU를 잡으면? ➔ 보통은 문제가 안생김

&nbsp;

## **4. The Critical-Section(임계 구역) Problem**

* n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우

* 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section이 존재

* Problem
	
    * 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다.

![](https://images.velog.io/images/dogfootbirdfoot/post/783e9dcc-0cec-45ac-b9eb-d39f3e0e70ab/6-8.png)

&nbsp;

## **5. Initial Attempts to Solve Problem**

* 두 개의 프로세스가 있다고 가정 : P0, P1

* 프로세스들의 일반적인 구조

```c
do {
	entry section
	critical section
	exit section
	remainer section
} while (1);
```

* 프로세스들은 수행의 동기화(synchronize)를 위해 몇몇 변수를 공유할 수 있다 ➔ `synchronization variable`

### **5-1. 프로그램적 해결법의 충족 요건**

* <U>Mutal Exclusion</U> (상호 배제)
	
    * 프로세스 Pi가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안 된다.

* <U>Progress</U> (진행)
	
    * 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야 한다.

* <U>Bounded Waiting</U> (유한 대기)
	
    * 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.

* 가정
	
    * 모든 프로세스의 수행 속도는 0보다 크다.
	
    * 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.


### **5-2. Algorithm 1**

**Synchronization variable :**

```c
int turn;
initially turn = 0;		// Pi can enter its critical section if (turn == i)
```

**Process P0 :**

```c
do {
	while (turn != 0);	// My turn?
	critical section
	turn = 1;
	remainder section	// Now it's your turn
} while (1);
```

* Satisfies mutual exclusion(상호 배제), but not progress(진행)

* 즉, 과잉양보 :
	
    * 반드시 한번씩 교대로 들어가야만 함 (swap-turn)
	
    * 그가 turn을 내 값으로 바꿔줘야만 내가 들어갈 수 있음
	
    * 특정 프로세스가 더 빈번히 critical section을 들어가야 한다면?

### **5-3. Algorithm 2**

**Synchronization variable :**

```c
boolean flag[2];
initially flag[모두] = false;	// no one is in CS
```

```
Pi ready to enter its critical section if (flag[i] == true)
```

**Process Pi :**

```c
do {
	flag[i] = true;		// Pretend I am in
	while (flag[j]);	// Is he also in? then wait
	critical section
	flag[i] = false;	// I am out now
	remainder section
} while (1);
```

* Satisfies mutual exclusion(상호 배제), but not progress requirement(진행)

* 둘 다 2행까지 수행 후 끊임 없이 양보하는 상황 발생 가능

### **5-4. Algorithm 3 (Peterson's Algorithm)**

Combined synchronization variables of algorithm 1 and 2

**Process Pi :**

```c
do {
	flag[i] = true;		// My intention is to enter...
	turn = j;			// Set to his turn
	while (flag[j] && turn ==j);	// wait only if...
	critical section
	flag[i] = false;
	remainder section
} while(1);
```

* Meets all three requirements; solves the critical section problem for two processes

* Busy Waiting(= spin lock)! (계속 CPU와 memory를 쓰면서 wait)

### **5-5. Synchronization Hardware**

하드웨어적으로 Test & modify를 atomic하게 수행할 수 있도록 지원하는 경우 앞의 문제는 간단히 해결

![](https://images.velog.io/images/dogfootbirdfoot/post/d4911c3e-f35f-434f-bb0d-78c35b1f8f69/6-9.png)

#### **5-5-1. Mutual Exclusion with Test & Set**

**Synchronization variable :**

```c
boolean lock = false;
```

**Process Pi :**

```c
do {
	while (Test_and_Set(lock));
	critical section
	lock = false;
	remainder section
}
```

&nbsp;

## **6. Semaphores**

앞의 방식들을 추상화시킴

### **6-1. Semaphore S**

* integer variable

* 아래의 두 가지 atomic 연산에 의해서만 접근 가능

![](https://images.velog.io/images/dogfootbirdfoot/post/d9a3b6ba-4189-4c07-89cd-fab3425a3fb3/6-10.png)

> **P 연산** : 공유 데이터를 획득하는 과정  
> **V 연산** : 사용 후 반납하는 과정  
> **S** : 자원의 갯수

### **6-2. Critical Section of n Processes**

**Synchronization variable :**

```c
semaphore mutex;	// initially 1 : 1개가 CS에 들어갈 수 있다
```

**Process Pi :**

```c
do {
	P(mutex);	// If positive, dec-&-enter, otherwise wait
	critical section
	V(mutex);	// Increment semaphore
	remainder section
} while (1);
```

* busy-wait는 효율적이지 못함 (= spin lock) (위 방식)

* Block & Wakeup 방식의 구현 (= sleep lock) (아래 방식)

### **6-3. Block & Wakeup Implementation**

**Semaphore를 다음과 같이 정의 :**

```c
typedef struct
{
	int value;				// semaphore
	struct process *L;		// process wait queue
} semaphore;
```

**block과 wakeup을 다음과 같이 가정 :**

* block
	
    * 커널은 block을 호출한 프로세스를 suspend시킴
	
    * 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음

* wakeup(P)
	
    * block된 프로세스 P를 wakeup시킴
	
    * 이 프로세스의 PCB를 ready queue로 옮김

![](https://images.velog.io/images/dogfootbirdfoot/post/612393da-ff42-459d-873a-37c4f04e8654/6-11.png)	

### **6-4. Block & Wakeup version of P() & V()**

**Semaphore 연산이 이제 다음과 같이 정의됨 :**

**P(S) :**

```c
S.value--;			// prepare to enter
if (S.value < 0)	// Oops, negative, I cannot enter
{
	add this process to S.L;
	block();
}
```

**V(S) :**

```c
S.value++;
if (S.value <= 0)
{
	remove a process P form S.L;
	wakeup(P);
}
```

### **6-5. Which is better? (Busy-wait vs Block & Wakeup)**

Block & Wakeup overhead vs Critical section 길이

* Critical section의 길이가 긴 경우 Block & Wakeup이 적당

* Critical section의 길이가 매우 짧은 경우 Block & Wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음

* 일반적으로는 Block & Wakeup 방식이 더 좋음

### **6-6. Two Types of Semaphores**

* <U>Counting semaphore</U>
	
    * 도메인이 0 이상인 임의의 정수값
	
    * 주로 resource counting에 사용

* <U>Binary semaphore</U> (=mutex)
	
    * 0 또는 1 값만 가질 수 있는 semaphore
	
    * 주로 mutual exclusion (lock / unlock)에 사용

&nbsp;

## **7. Deadlock and Starvation**

* <U>Deadlock</U>
	
    * 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상

![](https://images.velog.io/images/dogfootbirdfoot/post/d164895b-7662-4697-90e7-5ac31693dd55/6-12.png)

* <U>Starvation</U>
	
    * indefinite blocking : 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상

&nbsp;

## **8. Classical Problems of Synchronization**

### **8-1. Bounded-Buffer Problem (Producer-Consumber Problem)**

![](https://images.velog.io/images/dogfootbirdfoot/post/aa26ef3f-5fa6-4e51-ab38-ec16bf97c1c6/6-13.png)

* Shared data
	
    * buffer 자체 및 buffer 조작 변수 (empty/full buffer의 시작 위치)

* Synchronization variables
	
    * mutual exclusion ➔ Need binary semaphore (shared data의 mutual exclusion을 위해)
	
    * resource count ➔ Need integer semaphore (남은 full/empty buffer의 수 표시)

![](https://images.velog.io/images/dogfootbirdfoot/post/07451245-1519-463e-b2ac-58fb7c596bed/6-14.png)

### **8-2. Readers and Writers Problem**

* 한 process가 DB에 write 중일 때 다른 process가 접근하면 안됨

* read는 동시에 여럿이 해도 됨

* solution
	
    * Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 Reader들을 다 DB에 접근하게 해준다.
	
    * Writer는 대기 중인 Reader가 하나도 없을 때 DB 접근이 허용된다.
	
    * 일단 Writer가 DB에 접근 중이면 Reader들은 접근이 금지된다.
	
    * Writer가 DB에서 빠져나가야만 Reader의 접근이 허용된다.

* Shared data
	
    * DB 자체
	
    * readcount ➔ 현재 DB에 접근 중인 Reader의 수

* Synchronization variables
	
    * mutax ➔ 공유 변수 readcount를 접근하는 코드(critical section)의 mutual exclusion 보장을 위해 사용
	
    * db ➔ Reader와 Writer가 공유 DB 자체를 올바르게 접근하게 하는 역할

![](https://images.velog.io/images/dogfootbirdfoot/post/bd1ca2f8-5ca4-46d7-8c49-67b9ac186c8e/6-15.png)

### **8-3. Dining-Philosophers Problem**

![](https://images.velog.io/images/dogfootbirdfoot/post/8e918d34-2efa-4d4c-bce3-87cca66f3a4a/6-16.png)

위 그림 solution의 문제점

* Deadlock 가능성이 있다.

* 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 집어버린 경우

해결 방안

* 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다.

* 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 한다. (아래 그림)

* 비대칭 : 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 집도록 한다.

![](https://images.velog.io/images/dogfootbirdfoot/post/b597bca0-2d73-4264-91b1-aec97b5760f9/6-17.png)

&nbsp;

## **9. Monitor**

Semaphore의 문제점

* 코딩하기 힘들다.

* 정확성(correctness)의 입증이 어렵다.

* 자발적 협력(voluntary cooperation)이 필요하다.

* 한번의 실수가 모든 시스템에 치명적 영항을 미친다.

**Example :**

![](https://images.velog.io/images/dogfootbirdfoot/post/2748a0d2-0461-4bdf-b3e6-c843b4d27cc9/6-18.png)

**Monitor** : 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct (Semaphore와 다르게 lock을 할 필요 X)

![](https://images.velog.io/images/dogfootbirdfoot/post/04d7ee7a-3358-4ab7-b6f2-4b2e53d41d45/6-19.png)

![](https://images.velog.io/images/dogfootbirdfoot/post/c9d660fa-d865-4525-b8e9-7d03ac6c3cf1/6-20.png)

* 모니터 내에서는 한번에 하나의 프로세스만이 활동 가능

* 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요없음

* 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 `condition variable` 사용

```c
condition x, y;
```

* `condition variable`은 `wait`와 `signal` 연산에 의해서만 접근 가능

```c
x.wait();
x.signal();
```

* `x.wait()`을 invoke한 프로세스는 다른 프로세스가 `x.signal()`을 invoke하기 전까지 suspend된다.

* `x.signal()`은 정확하게 하나의 suspend된 프로세스를 resume한다. 

* suspend된 프로세스가 없으면 아무 일도 일어나지 않는다.

### **9-1. Bounded-Buffer Problem**

![](https://images.velog.io/images/dogfootbirdfoot/post/c3237e28-b8d0-41d9-945f-bdf2c6706d66/6-21.png)

### **9-2. Dining-Philosophers Problem**

![](https://images.velog.io/images/dogfootbirdfoot/post/c7fc3f6d-21c9-469c-bcc2-82051187106c/6-22.png)