# **CPU Scheduling**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. CPU and I/O Bursts in Program Execution**

![](https://images.velog.io/images/dogfootbirdfoot/post/d097cd1b-d8c4-4f4c-b109-ce173688c8f0/5-1.jpeg)

&nbsp;

## **2. CPU-burst Time의 분포**

![](https://images.velog.io/images/dogfootbirdfoot/post/047a7b74-5084-4878-9642-1dc652ff68f0/5-2.jpeg)

여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요하다.

* Interactive job에게 적절한 response 제공 요망

* CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

&nbsp;

## **3. 프로세스의 특성 분류**

프로세스는 그 특성에 따라 다음 두 가지로 나눔

* I/O-bound process
	
    * CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
	
    * (many short CPU bursts)

* CPU-bound process
	
    * 계산 위주의 job
	
    * (few very long CPU bursts)

&nbsp;

## **4. CPU Scheduler & Dispatcher**

### **4-1. CPU Scheduler**

* Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.

### **4-2. Dispatcher**

* CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다.

* 이 과정을 context switch(문맥 교환)라고 한다.

CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.

1. Running ➔ Blocked (예: I/O 요청하는 시스템 콜)
2. Running ➔ Ready (예: 할당시간만료로 timer interrupt)
3. Blocked ➔ Ready (예: I/O 완료 후 인터럽트)
4. Terminate

> 1, 4에서의 스케줄링은 nonpreemptive (비선점형) (=강제로 빼앗지 않고 자진 반납)  
> 2, 3에서의 스케줄링은 preemptive (선점형) (=강제로 빼앗음)

&nbsp;

## **5. Scheduling Criteria (Performance Measure, 성능 척도)**

* CPU utilization (이용률)
	
    * keep the CPU as busy as possible

* Throughput (처리량)
	
    * `#` of processess that complete their execution per time unit

> 위 2개는 시스템 입장에서의 성능 척도

* Turnaround time (소요 시간, 반환 시간)
	
    * amount of time to execute a particuler process

* Waiting time (대기 시간)
	
    * amount of time a process has been waiting in the ready queue

* Response time (응답 시간)
	
    * amount of time it takes from when a request was submitted until the first response is produced, not output  (for time-sharing environment)

> 위 3개는 프로세스(고객) 입장에서의 성능 척도

&nbsp;

## **6. Scheduling Algorithms**

* FCFS (First-Come-First-Served)

* SJF (Shortest-Job-First)

* SRTF (Shortest-Remaining-Time-First)

* Priority Scheduling

* RR (Round Robin)

* Multilevel Queue

* Multilevel Feedback Queue

&nbsp;

## **7. FCFS (First-Come-First-Served)**

FCFS 는 비선점형 스케줄링

**Example 1 :**

|Process|Burst Time|
|:--:|:--:|
|P1|24|
|P2|3|
|P3|3|

프로세스의 도착 순서 : P1, P2, P3

![](https://images.velog.io/images/dogfootbirdfoot/post/26b8c04e-bad9-4f0c-b828-03b99838df0d/5-3.jpeg)

* Waiting time for P1 = 0; P2 = 24; P3 = 27

* Average waiting time : (0 + 24 + 27) / 3 = 17

그리 효율적이진 않음

**Example 2 :**

프로세스의 도착 순서 : P2, P3, P1

![](https://images.velog.io/images/dogfootbirdfoot/post/d7899df0-29ed-45d3-9e2c-5acebffe54b2/5-3-1.png)

* Waiting time for P1 = 6; P2 = 0; P3 = 3

* Average waiting time : (6 + 0 + 3) / 3 = 3

* Much better than previous case

* <U>Convoy effect</U> : short process behind long process (짧은 프로세스들이 지나치게 오래 기다려야 하는 현상)

&nbsp;

## **8. SJF (Shortest-Job-First)**

* 각 프로세스의 다음번 CPU burst time을 가지고 스케줄링에 활용

* CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄

* Two schemes
    * Nonpreemptive
    	* 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점(preemption) 당하지 않음
	
    * Preemptive
    	
        * 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
		
        * 이 방법을 Shortest-Remaining-Time-First (SRTF)이라고 부른다.

* SJF is optimal
	
    * 주어진 프로세스들에 대해 minimum average waiting time을 보장

**Example of Non-Preemptive SJF :**

|Process|Arrival Time|Burst Time|
|:--:|:--:|:--:|
|P1|0|7|
|P2|2|4|
|P3|4|1|
|P4|5|4|

프로세스의 도착 순서 : P1, P2, P3, P4

![](https://images.velog.io/images/dogfootbirdfoot/post/5980985a-3bd8-4909-86b1-59aae26033b0/5-4.jpeg)

* Waiting time for P1 = 0; P2 = 6; P3 = 3; P4 = 7

* Average waiting time : (0 + 6 + 3 + 7) / 4 = 4

**Example of Preemptive SJF :**

![](https://images.velog.io/images/dogfootbirdfoot/post/224d6679-c745-4986-9653-1190018719f4/5-4-1.png)

* Waiting time for P1 = 9; P2 = 1; P3 = 0; P4 = 2

* Average waiting time : (9 + 1 + 0 + 2) / 4 = 3

&nbsp;

## **9. Priority Scheduling**

* A priority number (integer) is associated with each process

* highest priority를 가진 프로세스에게 CPU 할당 (smallest integer = highest priority)
	
    * Preemptive
	
    * Nonpreemptive

* SJF는 일종의 priority scheduling이다.
	
    * priority = predicted next CPU burst time

* Problem (SJF의 문제점)
	
    * <U>Starvation</U> (기아 현상) : low priority processes may never execute
	
    * CPU 사용시간을 정확하게 예측할 수 없다. (과거 사용이력으로 대략적으로는 예측 가능)

* Solution
	
    * <U>Aging</U> (노화) : as time progresses increase the priority of the process

&nbsp;

## **10. 다음 CPU Burst Time의 예측**

* 다음번 CPU burst time을 어떻게 알 수 있는가? (영향을 주는 요소로 input data, branch, user...)

* 추정(estimate)만이 가능하다.

* 과거의 CPU burst time을 이용해서 추정 (`exponential averaging`)

그림 5-3

점화식을 풀면 타우n+1은 실제로 수행된 프로세스의 CPU burst time 값들로 나타낼 수 있다.

&nbsp;

## **11. Round Robin (RR)**

* 현대적인 컴퓨터 시스템에서 사용하는 CPU Scheduling

* Response time(응답 시간)이 빠르다. (예측할 필요 X)

* 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐 (일반적으로 10 ~ 100 milliseconds)

* 할당 시간이 지나면 프로세스는 선점(preempted)당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다.

* n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻는다. (➔ 어떤 프로세스도 (n - 1)q time unit 이상 기다리지 않음)

* Performance (극단적인 상황)
	
    * q large ➔ FCFS
	
    * q small ➔ context switch 오버헤드가 커짐

**Example : RR with Time Quantum = 20**

|Process|Burst Time|
|:--:|:--:|
|P1|53|
|P2|17|
|P3|68|
|P4|24|

![](https://images.velog.io/images/dogfootbirdfoot/post/b57b1720-6853-463c-846c-ada6c83dd052/5-5.jpeg)

일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧다.

CPU burst time이 모두 동일한 프로세스(특수한 경우)가 아주 짧은 time quantum으로 잘게 나뉠 경우는 average turnaround time이 너무 길어지기 때문에 비효율적일 수 있다.

&nbsp;

## **12. Multilevel Queue**

* Ready queue를 여러 개로 분할
	
    * <U>foreground</U> (interactive)
	
    * <U>background</U> (batch - no human interaction)

* 각 큐는 독립적인 스케줄링 알고리즘을 가짐
	
    * <U>foreground</U> (RR)
	
    * <U>background</U> (FCFS)

* 큐에 대한 스케줄링이 필요
	
    * Fixed priority scheduling
		
        * serve all from foreground then from background
		
        * Possibility of starvation
	
    * Time slice
		
        * 각 큐에 CPU time을 적절한 비율로 할당
		
        * ex) 80% to foreground in RR, 20% to background in FCFS

![](https://images.velog.io/images/dogfootbirdfoot/post/cbe96ffd-6589-43cf-a4ee-7ac0f48dd69e/5-6.jpeg)

&nbsp;

## **13. Multilevel Feedback Queue**

* 프로세스가 다른 큐로 이동 가능

* Aging을 이와 같은 방식으로 구현할 수 있다.

* Multilevel-feedback-queue scheduler를 정의하는 parameter들
	
    * Queue의 수
	
    * 각 큐의 scheduling algorithm
	
    * Process를 상위 큐로 보내는 기준
	
    * Process를 하위 큐로 내쫓는 기준
	
    * 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

**Example of Multilevel Feedback Queue**

![](https://images.velog.io/images/dogfootbirdfoot/post/7136ce95-b255-49df-9ee0-fc1fef766225/5-7.jpeg)

* Three queues :
	
    * Q0 - time quantum 8 milliseconds
	
    * Q1 - time quantum 16 milliseconds
	
    * Q2 - FCFS

* Scheduling
	
    * new job이 queue Q0로 들어감
	
    * CPU를 잡아서 할당 시간 8 milliseconds 동안 수행됨
	
    * 8 milliseconds 동안 다 끝내지 못했으면 queue Q1으로 내려감
	
    * Q1에 줄서서 기다렸다가 CPU를 잡아서 16 ms 동안 수행됨
	
    * 16 ms에 끝내지 못한 경우 queue Q2로 쫓겨남

&nbsp;

## **14. Multiple-Processor Scheduling**

* CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐

* Homogeneous processor인 경우
	
    * Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
	
    * 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐

* Load sharing
	
    * 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
	
    * 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법

* Symmetric Multiprocessing (SMP)
	
    * 각 프로세서가 각자 알아서 스케줄링 결정

* Asymmetric Multiprocessing
	
    * 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

&nbsp;

## **15. Real-Time Scheduling**

**<U>Hard real-time systems</U>**

* 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함

**<U>Soft real-time systems</U>**

* 일반 프로세스에 비해 높은 priority를 갖도록 해야 함

&nbsp;

## **16. Thread Scheduling**

**<U>Local Scheduling</U>**

* User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
	
    * OS가 하는 것이 아니고(thread의 존재를 모르기 때문) 사용자 프로세스가 결정

**<U>Global Scheduling</U>**

* Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널 단기 스케줄러가 어떤 thread를 스케줄할지 결정
	
    * OS가 thread의 존재를 알고 있기 때문에 프로세스 스케줄링 하듯이 OS가 어떤 알고리즘에 근거해서 결정

&nbsp;

## **17. Algorithm Evaluation**

**<U>Queueing models</U>**

* 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산

![](https://images.velog.io/images/dogfootbirdfoot/post/11089333-b992-453e-b10f-82437a69cdff/5-8.jpeg)

**<U>Implementation & Measurement</U> (구현 & 성능 측정)**

* 실제 시스템에 알고리즘을 구현하여 실제 작업(workload)에 대해서 성능을 측정 비교

**<U>Simulation</U> (모의 실험)**

* 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교

