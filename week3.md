# **Process**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. 프로세스의 개념**

`Process is a program in execution`

### **1-1. 프로세스의 문맥 (context)**

* CPU 수행 상태를 나타내는 하드웨어 문맥
	
    * Program Counter
    
    * 각종 register

* 프로세스의 주소 공간
	
    * code, data, stack

* 프로세스 관련 커널 자료 구조
	
    * PCB (Process Control Block)
    
    * Kernel stack

![](https://images.velog.io/images/dogfootbirdfoot/post/3a993de6-5c72-4da0-b4c2-07254de2d68c/3-1.png)

&nbsp;

## **2. 프로세스의 상태 (Process State)**

프로세스는 상태(state)가 변경되며 수행된다.

* <U>Running</U>
	
    * CPU를 잡고 instruction을 수행중인 상태

* <U>Ready</U>
	
    * CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족하고)

* <U>Blocked</U> (wait, sleep)
	
    * CPU를 주어도 당장 instruction을 수행할 수 없는 상태
	
    * Process 자신이 요청한 event(예: I/O)가 즉시 만족되지 않아 이를 기다리는 상태
	
    * (예) 디스크에서 file을 읽어와야 하는 경우

* New
	
    * 프로세스가 생성중인 상태

* Terminated
	
    *  수행(execution)이 끝난 상태

**프로세스 상태도**
![](https://images.velog.io/images/dogfootbirdfoot/post/dcd4dc46-7c53-493d-b27a-3a971e3d6407/3-2.png)
![](https://images.velog.io/images/dogfootbirdfoot/post/ea89689a-2c0b-4fc6-9853-4df26db19c49/3-3.png)
![](https://images.velog.io/images/dogfootbirdfoot/post/beb597c3-fca2-4a35-804b-b478904f2c1c/3-4.png)

&nbsp;

## **3. Process Control Block (PCB)**

운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보

다음의 구성 요소를 가진다 (구조체로 유지)

* (1) OS가 관리상 사용하는 정보
	
    * Process state, Process ID
	
    * scheduling information, priority

* (2) CPU 수행 관련 하드웨어 값
	
    * Program counter, registers

* (3) 메모리 관련
	
    * code, data, stack의 위치 정보

* (4) 파일 관련
	
    * Open file descriptors...

![](https://images.velog.io/images/dogfootbirdfoot/post/b1848124-61a1-4be1-b5f2-d9e8797b2571/3-5.png)

&nbsp;

## **4. 문맥 교환 (Context Switch)**

CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정

CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행

* CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장

* CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

![](https://images.velog.io/images/dogfootbirdfoot/post/33a83e59-9028-4c1a-9bab-f6393ec9e5c3/3-6.png)

System call이나 Interrupt 발생 시 반드시 context switch가 일어나는 것은 아님

![](https://images.velog.io/images/dogfootbirdfoot/post/277a961f-38fc-441c-9a45-68959591543c/3-7.png)

(1)의 경우에도 CPU 수행 정보 등 context의 일부를 PCB에 save해야 하지만 문맥교환을 하는 (2)의 경우 그 부담이 훨씬 큼 (ex. cache memory flush)

&nbsp;

## **5. 프로세스를 스케줄링하기 위한 큐**

* Job queue
	
    * 현재 시스템 내에 있는 모든 프로세스의 집합

* Ready queue
	
    * 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합

* Device queues
	
    * I/O device의 처리를 기다리는 프로세스의 집합

프로세스들은 각 큐들을 오가며 수행된다.

### **5-1. Ready Queue와 다양한 Device Queue**

![](https://images.velog.io/images/dogfootbirdfoot/post/f9b48c00-6c65-4019-8efa-6740d730bf0e/3-8.png)

### **5-2. 프로세스 스케줄링 큐의 모습**

![](https://images.velog.io/images/dogfootbirdfoot/post/6649752f-252b-41f7-9813-5031849c4c9b/3-9.png)

&nbsp;

## **6. 스케줄러 (Scheduler)**

* <U>Long-term scheduler</U> (장기 스케줄러 / job scheduler)
	
    * 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
	
    * 프로세스에 memory(및 각종 자원)을 주는 문제
	
    * degree of Multiprogramming을 제어
	
    * time sharing system에는 보통 장기 스케줄러가 없음 (무조건 ready)

* <U>Short-term scheduler</U> (단기 스케줄러 / CPU scheduler)
	
    * 어떤 프로세스를 다음번에 running시킬지 결정
	
    * 프로세스에 CPU를 주는 문제
	
    * 충분히 빨라야 함 (millisecond 단위)

* <U>Medium-Term Scheduler</U> (중기 스케줄러 / Swapper)
	
    * 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
	
    * 프로세스에게서 memory를 뺏는 문제
	
    * degree of Multiprogramming을 제어

프로세스 상태 (suspended 포함)

* <U>Running</U>
	
    * CPU를 잡고 instruction을 수행중인 상태

* <U>Ready</U>
	
    * CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족하고)

* <U>Blocked</U> (wait, sleep)
	
    * CPU를 주어도 당장 instruction을 수행할 수 없는 상태
	
    * Process 자신이 요청한 event(예: I/O)가 즉시 만족되지 않아 이를 기다리는 상태
	
    * (예) 디스크에서 file을 읽어와야 하는 경우

* <U>Suspended</U> (stopped) (중기 스케줄러에 의함)
	
    * 외부적인 이유로 프로세스의 수행이 정지된 상태
	
    * 프로세스는 통째로 디스크에 swap out 된다.
	
    * (예) 사용자가 프로그램을 일시 정지시킨 경우(break key) 시스템이 여러 이유로 프로세스를 잠시 중단시킨 (메모리에 너무 많은 프로세스가 올라와 있을 때)

> Blocked와 Suspended 차이점  
> Blocked : 자신이 요청한 event가 만족되면 Ready  
> Suspended : 외부에서 resume해 주어야 Active

**프로세스 상태도 (suspended 포함)**
![](https://images.velog.io/images/dogfootbirdfoot/post/cbba7285-fb7f-4d85-9620-0b04f9a108bf/3-10.png)

&nbsp;

## **7. Thread**

`A thread (or lightweight process) is a basic unit of CPU utilization`

### **7-1. Tread의 구성**

* program counter

* register set

* stack space

### **7-2. Tread가 동료 thread와 공유하는 부분 (=task)**

* code section

* data section

* OS resources

전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.

![](https://images.velog.io/images/dogfootbirdfoot/post/788c6500-b577-4b3c-815b-14fc3633ca10/3-11.png)

### **7-3. Tread의 장점**

* 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked (waiting) 상태인 동안에도 동일한 테스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다.

* 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.

* 스레드를 사용하면 병렬성을 높일 수 있다. (CPU가 여러개 달린 컴퓨터에서만 가능)

![](https://images.velog.io/images/dogfootbirdfoot/post/d2822107-ba81-4b32-bc20-91acc2637b14/3-12.png)

### **7-4. Single and Multithreaded Processes**

![](https://images.velog.io/images/dogfootbirdfoot/post/e21ab737-4616-4ee9-9762-1a30364852b5/3-13.png)

### **7-5. Thread의 장점2**

* Responsiveness (응답성)
	
    * (예) multi-threaded Web - if one thread is blocked(ex. network) another thread continues(ex. display) (일종의 비동기식 방법)

* Resource Sharing (자원 공유)
	
    * n threads can share binary code, data, resource of the process

* Economy (경제성)
	
    * creating & CPU switching thread (rather than a process)
		
        * process를 하나 만드는 것은 overhead가 상당히 크지만 process 안에 thread를 하나 추가하는 것은 overhead가 그리 크지 않음
	
    * Solaris의 경우 위 두 가지 overhead가 각각 30배, 5배
		
        * 하나의 process에서 다른 process로 넘어가는 것 즉, 문맥 교환은 overhead가 상당히 크지만 process 내부에서 thread간의 CPU switch는 비교적 간단함

* Utilization of MP(multi-processor) Architectures (CPU가 여러 개 있을 때 얻을 수 있는 장점)
	
    * each thread may be running in parallel on a different processor

### **7-6. Implemetation of Threads (스레드를 구현할 수 있는 방법)**

* Some are supported by kernel ➔ Kernel Threads (운영체제 커널이 여러 개의 스레드가 존재한다는 사실을 알고 있음)
	
    * Windows 95/98/NT
	
    * Solaris
	
    * Digital UNIX, Mach

* Others are supported by library ➔ User Threads (프로세스 안에 여러 개의 스레드가 존재한다는 사실을 운영체제가 모름)
	
    * POSIX Pthreads
	
    * Mach C-threads
	
    * Solaris threads

* Some are real-time threads