# **Deadlocks**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. The Deadlock Problem**

![](https://images.velog.io/images/dogfootbirdfoot/post/ceb952cd-b662-4003-94f3-5dcd65ff8341/7-1.png)

**Deadlock (교착상태)**

* 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

**Resource (자원)**

* 하드웨어, 소프트웨어 등을 포함하는 개념

* (예) I/O device, CPU cycle, memory space, semaphore 등

* 프로세스가 자원을 사용하는 절차
	
    * Request ➔ Allocate ➔ Use ➔ Release

**Deadlock Example 1:**

* 시스템에 2개의 tape drive가 있다.

* 프로세스 P1과 P2가 각각 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.

**Deadlock Example 2:**

* Binary semaphore A and B

|P0|P1|
|:--:|:--:|
|P(A);|P(B);|
|P(B);|P(A);|

&nbsp;

## **2. Deadlock 발생의 4가지 조건**

* <U>Mutual exclusion</U> (상호 배제)
	
    * 매 순간 하나의 프로세스만이 자원을 사용할 수 있음

* <U>No preemption</U> (비선점)
	
    * 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음

* <U>Hold and wait</U> (보유대기)
	
    * 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음

* <U>Circular wait</U> (순환대기)
	
    * 자원을 기다리는 프로세스 간에 사이클이 형성되어야 함
	
    * 프로세스 P0, P1, ..., Pn이 있을 때
		
        * P0은 P1이 가진 자원을 기다림
		
        * P1은 P2이 가진 자원을 기다림
		
        * Pn-1은 Pn이 가진 자원을 기다림
		
        * Pn은 P0이 가진 자원을 기다림

&nbsp;

## **3. Resource-Allocation Graph (자원할당그래프)**

![](https://images.velog.io/images/dogfootbirdfoot/post/75a8999c-b47b-462e-a0ab-b2631e398b4d/7-2.png)

(deadlock X)

* Vertex
	
    * Process P = {P1, P2, ..., Pn}
	
    * Resource R = {R1, R2, ..., Rn}

* Edge
	
    * request edge = Pi ➔ Rj
	
    * assignment edge = Rj ➔ Pi

![](https://images.velog.io/images/dogfootbirdfoot/post/91135d13-4eae-495f-abf6-f83c920bbdf3/7-3.png)

(deadlock)

![](https://images.velog.io/images/dogfootbirdfoot/post/6e854557-9273-4d22-b5a1-c210b48e2371/7-4.png)

(deadlock X)

* 그래프에 cycle이 없으면 deadlock이 아니다.

* 그래프에 cycle이 있으면
	
    * if only one instance(검은색 점) per resource type, then deadlock
	
    * if serveral instances per resource type, possibility of deadlock

&nbsp;

## **4. Deadlock의 처리 방법**

* <U>Deadlock Prevention</U>
	
    * 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

* <U>Deadlock Avoidance</U>
	
    * 자원 요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당
	
    * 시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당

* <U>Deadlock Detection and Recovery</U>
	
    * Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견 시 recover

* <U>Deadlock Ignorance</U>
	
    * Deadlock을 시스템이 책임지지 않음
	
    * UNIX를 포함한 대부분의 OS가 채택
	
    * 현대 가장 많이 채택하는 방법으로 deadlock은 빈번히 발생하는 문제가 아니기 때문에 미연에 방지하기 위해서 훨씬 더 많은 오버헤드를 들이는 것이 비효율적

> 위로 갈 수록 더 강력한 방법  
> 위 2가지 방법은 deadlock이 생기기 전 미연에 방지  
> 아래 2가지 방법은 일단 deadlock이 생기게 놔둠

### **4-1. Deadlock Prevention**

Deadlock이 발생하는 4가지 조건 중 하나를 원천적으로 차단해서 deadlock에 들어가지 못 하게 하는 방법

* <U>Mutual exclusion</U>
	
    * 공유해서는 안되는 자원의 경우 반드시 성립해야 한다.

* <U>Hold and wait</U>
	
    * 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
	
    * 방법 1 : 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법 (비효율적)
	
    * 방법 2 : 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청

* <U>No preemption</U>
	
    * Process가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점된다.
	
    * 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
	
    * State를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용 (CPU, memory)

* <U>Circular wait</U>
	
    * 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
	
    * 예를 들어 순서가 3인 자원 Ri를 보유 중인 프로세스가 순서가 1인 자원 Rj를 할당받기 위해서는 우선 Rj를 release해야 한다.

> utilization 저하, throughput 감소, starvation 문제  
> 생기지도 않을 deadlock을 미리 생각해서 제약조건을 많이 달아놓기 때문에 비효율적

### **4-2. Deadlock Avoidance**

* 자원 요청에 대한 부가정보를 이용해서 자원 할당이 deadlock으로부터 안전(safe)한지를 동적으로 조사해서 안전한 경우에만 할당

* 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법

* <U>safe state</U>
	
    * 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태

* <U>safe sequence</U>
	
    * 프로세스의 sequence <P1, P2, ..., Pn>이 safe하려면 Pi(1 ≤ i ≤ n)의 자원 요청이 `가용 자원 + 모든 Pj(j < i)의 보유 자원`에 의해 충족되어야 한다.
	
    * 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
		
        * Pi의 자원 요청이 즉시 충족될 수 없으면 모든 Pj(j < i)가 종료될 때까지 기다린다.
		
        * Pi-1이 종료되면 Pi의 자원요청을 만족시켜 수행한다.

![](https://images.velog.io/images/dogfootbirdfoot/post/e55926cb-7885-429e-a2a0-5c49c61e3ac9/7-5.png)

* 시스템이 safe state에 있으면 ➔ no deadlock

* 시스템이 unsafe state에 있으면 ➔ possibility of deadlock

* Deadlock Avoidance
	
    * 시스템이 unsafe state에 들어가지 않는 것을 보장
	
    * 2가지 경우의 avoidance 알고리즘
		
        * Single instance per resource types
			
            * `Resource Allocation Graph algorithm` 사용
		
        * Multiple instances per resource types
			
            * `Banker's Algorithm` 사용

#### **4-2-1. Resource Allocation Graph algorithm**

* <U>Claim edge</U> Pi ➔ Rj
	
    * 프로세스 Pi가 자원 Rj를 미래에 요청할 수 있음을 뜻함 (점선)
	
    * 프로세스가 해당 자원 요청 시 request edge로 바뀜 (실선)
	
    * Rj가 release되면 assignment edge는 다시 claim edge로 바뀜

* request edge의 assignment edge 변경 시 (점선을 포함하여) cycle이 생기지 않는 경우에만 요청 자원을 할당한다.

* cycle 생성 여부 조사 시 프로세스의 수가 n일 때 O(n^2) 시간이 걸린다.

![](https://images.velog.io/images/dogfootbirdfoot/post/7c2a88f7-6a90-46f8-9712-bc3748301d31/7-6.png)

#### **4-2-2. Banker's Algorithm**

* 가정
	
    * 모든 프로세스는 자원의 최대 사용량을 미리 명시
	
    * 프로세스가 요청 자원을 모두 할당받은 경우 유한 시간 안에 이들 자원을 다시 반납한다.

* 방법
	
    * 기본 개념 : 자원 요청 시 safe 상태를 유지할 경우에만 할당
	
    * 총 요청 자원의 수가 가용 자원의 수보다 적은 프로세스를 선택 (그런 프로세스가 없으면 unsafe 상태)
	
    * 그런 프로세스가 있으면 그 프로세스에게 자원을 할당
	
    * 할당받은 프로세스가 종료되면 모든 자원을 반납
	
    * 모든 프로세스가 종료될 때까지 이러한 과정 반복

**Example :**

![](https://images.velog.io/images/dogfootbirdfoot/post/9147a999-beb7-47cf-9dfd-1c5397388a79/7-7.png)

![](https://images.velog.io/images/dogfootbirdfoot/post/969c0e21-2ba5-4ee4-ace8-8540ebb478eb/7-8.png)

### **4-3. Deadlock Detection and Recovery**

#### **4-3-1. Deadlock Detection**

* Resource type 당 single instance인 경우
	
    * 자원할당 그래프에서의 cycle이 곧 deadlock을 의미
* Resource type 당 multiple instance인 경우
	
    * Banker's algorithm과 유사한 방법 활용

* Wait-for graph 알고리즘
	
    * Resource type 당 single instance인 경우
	
    * Wait-for graph
		
        * 자원할당 그래프의 변형
		
        * 프로세스만으로 node 구성
		
        * Pj가 가지고 있는 자원을 Pk가 기다리는 경우 Pk ➔ Pj
	
    * Algorithm
		
        * Wait-for graph에 사이클이 존재하는지를 주기적으로 조사
		
        * O(n^2)

![](https://images.velog.io/images/dogfootbirdfoot/post/938897ed-38ac-4ede-bd20-11da2f37065d/7-9.png)

![](https://images.velog.io/images/dogfootbirdfoot/post/131b5d64-03e2-4fd9-8cd0-018e4f3a4ea7/7-10.png)

![](https://images.velog.io/images/dogfootbirdfoot/post/01944fe3-5683-492a-bb12-c3b1c53dd597/7-11.png)

#### **4-3-2. Recovery**

* <U>Process termination</U>
	
    * Abort all deadlocked processes (모든 프로세스 죽임)
	
    * Abort one process at a time until the deadlock cycle is eliminated (하나씩 죽여서 deadlock이 풀리는지 확인)

* <U>Resource Preemption</U>
	
    * 비용을 최소화할 victim의 선정
	
    * safe state로 rollback하여 process를 restart
	
    * starvation 문제
		
        * 동일한 프로세스가 계속해서 victim으로 선정되는 경우
		
        * cost factor에 rollback 횟수도 같이 고려

### **4-4. Deadlock Ignorance**

Deadlock이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음

* Deadlock이 매우 드물게 발생하므로 deadlock에 대한 조치 자체가 더 큰 overhead일 수 있음
* 만약, 시스템에 deadlock이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 process를 죽이는 등의 방법으로 대처
* UNIX, Windows 등 대부분의 범용 OS가 채택

