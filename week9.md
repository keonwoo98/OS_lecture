# **Virtual Memory**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. Demand Paging**

* 실제로 필요할 때 page를 메모리에 올리는 것
	
    * I/O 양의 감소
	
    * Memory 사용량 감소
	
    * 빠른 응답 시간
	
    * 더 많은 사용자 수용

* Valid / Invalid bit의 사용
	
    * Invalid의 의미
		
        * 사용되지 않는 주소 영역인 경우
		
        * 페이지가 물리적 메모리에 없는 경우
	
    * 처음에는 모든 page entry가 invalid로 초기화
	
    * address translation 시에 invalid bit이 set되어 있으면 ➔ `page fault`

**Memory에 없는 Page의 Page Table**

![](https://images.velog.io/images/dogfootbirdfoot/post/a294d725-d1b3-47fd-b431-5396bd88805b/9-1.png)

### **1-1. Page Fault**

* Invalid page를 접근하면 MMU가 trap을 발생시킴 (page fault trap)

* Kernel mode로 들어가서 page fault handler가 invoke됨

* 다음과 같은 순서로 page fault를 처리한다 :
1. Invalid reference? (ex. bad address, protection violation) ➔ abort process
2. Get an empty page frame (없으면 뺏어온다: replace)
3. 해당 페이지를 disk에서 memory로 읽어온다.
	1. disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt 당함 (block)
	2. Disk read가 끝나면 page tables entry 기록, valid / invalid bit = "valid"
	3. ready queue에 process를 insert ➔ dispatch later
4. 이 프로세스가 CPU를 잡고 다시 running
5. 아까 중단되었던 instruction을 재개

**Steps in Handling a Page Fault**

![](https://images.velog.io/images/dogfootbirdfoot/post/6ba517af-6f99-4b66-b961-8d02a2940f77/9-2.png)

### **1-2. Performance of Demand Paging**

* Page Fault Rate `0 ≤ p ≤ 1.0`
	
    * if `p = 0` no page faults
	
    * if `p = 1` every reference is a fault

* Effective Access Time  
	= (1 - p) x memory access  
		+ p (OS & HW page fault overhead  
			+ [swap page out if needed]  
			+ swap page in  
			+ OS & HW restart overhead)


### **1-3. Free frame이 없는 경우**

* Page replacement
	
    * 어떤 frame을 빼앗아올지 결정해야 함
	
    * 곧바로 사용되지 않을 page를 쫓아내는 것이 좋음
	
    * 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음

* Replacement Algorithm
	
    * page-fault rate을 최소화하는 것이 목표
	
    * 알고리즘 평가
		
        * 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사
	
    * reference string의 예
		
        * 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5

**Page Replacement**

![](https://images.velog.io/images/dogfootbirdfoot/post/eebfd94d-baff-4b8a-85f6-39c0c8818e98/9-3.png)

&nbsp;

## **2. Replacement Algorithm**

### **2-1. Optimal Algorithm**

* MIN (OPT) : 가장 먼 미래에 참조되는 page를 replace

* 미래를 예측하는 것을 기반으로 하기 때문에 현실에서는 사실상 불가능 (이 알고리즘을 모델로 알고리즘을 만듦)

* 4 frame example :

![](https://images.velog.io/images/dogfootbirdfoot/post/ecf0f46a-68b3-4fe5-994d-163f2ca5c479/9-4.png)

* 미래의 참조를 어떻게 아는가?
	
    * Offline algorithm

* 실제로는 미래를 알 수 없기 때문에 과거를 참조하여 예측할 수 밖에 없다.

* 다른 알고리즘의 성능에 대한 upper bound 제공
	
    * Belady's optimal algorithm, MIN, OPT 등으로 불림

### **2-2. FIFO(First In First Out) Algorithm**

* FIFO : 먼저 들어온 것을 먼저 내쫓음

![](https://images.velog.io/images/dogfootbirdfoot/post/fc66e7d4-0d49-4f0c-a41f-53ffeee0c574/9-5.png)

* FIFO Anomaly (Belady's Anomaly)
	
    * more frames ➔ less page faults
	
    * 메모리 frame을 늘려줬는데 성능은 저하

### **2-3. LRU(Least Recently Used) Algorithm**

* LRU : 가장 오래 전에 참조된 것을 지움

![](https://images.velog.io/images/dogfootbirdfoot/post/5465b80d-0438-4bd4-9419-b0f18250742b/9-6.png)

### **2-4. LFU(Least Frequently Used) Algorithm**

* LFU : 참조 횟수(reference count)가 가정 적은 페이지를 지움

* 최저 참조 횟수인 page가 여럿 있는 경우
	
    * LFU 알고리즘 자체에서는 여러 page 중 임의로 선정한다.
	
    * 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 구현할 수도 있다.

* 장단점
	
    * LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음
	
    * 참조 시점의 최근성을 반영하지 못함
	
    * LRU보다 구현이 복잡함

### **2-5. LRU와 LFU Algorithm 비교**

**예제 :**

![](https://images.velog.io/images/dogfootbirdfoot/post/d5177c8e-3c7d-42d9-a464-cc2bf3df6e59/9-7.png)

**구현 :**

![](https://images.velog.io/images/dogfootbirdfoot/post/6a61c10e-28cd-49d4-8ec6-331a1226f495/9-8.png)

### **2-6. Paging System에서 LRU, LFU 가능한가?**

![](https://images.velog.io/images/dogfootbirdfoot/post/9da8c8f7-bca7-4c67-ae07-7ec8f9c4e137/9-9.png)

사실상 LRU, LFU Algorithm은 메모리에 이미 있는 page에 대해서는 운영체제한테 CPU가 넘어오지 않기 때문에 운영체제는 어떤 page가 가장 최근에 참조되었는지 어떤 page가 가장 많이 참조되었는지 등을 알 수 없다. 따라서 LRU, LFU Algorithm은 paging system 즉, virtual memory system에서는 사용을 할 수가 없다. 대신 Buffer caching이나 Web caching 같은 곳에서는 활용이 될 수가 있다. 그래서 paging system에서는 어떤 것을 쫓아내야할지 결정하기 위해서 다음 알고리즘을 사용한다.

### **2-7. Clock Algorithm**

* LRU의 근사(approximation) 알고리즘

* 여러 명칭으로 불림
	
    * Second chance algorithm
	
    * NUR(Not Used Recently) 또는 NRU(Not Recently Used)

* Reference bit을 사용해서 교체 대상 페이지 선정 (circular list)

* Reference bit가 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동

* 포인터 이동하는 중에 reference bit 1은 모두 0으로 바꿈

* Reference bit이 0인 것을 찾으면 그 페이지를 교체

* 한 바퀴 되돌아와서도(=second chance) 0이면 그때에는 replace 당함

* 자주 사용되는 페이지라면 second chance가 올 때 1

* Clock algorithm의 개선
	
    * Reference bit과 modified bit(dirty bit)을 함께 사용
	
    * Reference bit = 1 : 최근에 참조된 페이지
	
    * Reference bit = 1 : 최근에 변경된 페이지 (I/O를 동반하는 페이지)

![](https://images.velog.io/images/dogfootbirdfoot/post/fc4e8c7b-b986-4205-b22e-56a15dcf93b8/9-10.png)

&nbsp;

## **3. 다양한 캐슁 환경**

* 캐슁 기법
	
    * 한정된 빠른 공간(=캐쉬)에 요청된 데이터를 저정해 두었다가 후속 요청 시 캐쉬로부터 직접 서비스하는 방식
	
    * Paging system 외에도 cache memory, buffer memory, Web caching 등 다양한 분야에서 사용

* 캐쉬 운영의 시간 제약
	
    * 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음
	
    * Buffer caching이나 Web caching의 경우
		
        * O(1)에서 O(log n) 정도까지 허용
	
    * Paging system인 경우
		
        * page fault인 경우에만 OS가 관여함
		
        * 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
		
        * O(1)인 LRU의 list 조작조차 불가능

&nbsp;

## **4. Page Frame의 Allocation**

* Allocation Problem
	
    * 각 process에 얼마만큼의 page frame을 할당할 것인가?

* Allocation의 필요성
	
    * 메모리 참조 명령어 수행 시 명령어, 데이터 등 여러 페이지 동시 참조
		
        * 명령어 수행을 위해 최소한 할당되어야 하는 frame의 수치가 있음
	
    * Loop를 구성하는 page들은 한꺼번에 allocate 되는 것이 유리함
		
        * 최소한의 allocation이 없으면 매 loop 마다 page fault

* Allocation Scheme
	
    * <U>Equal allocation</U> : 모든 프로세스에 똑같은 갯수 할당
	
    * <U>Proportional allocation</U> : 프로세스 크기에 비례하여 할당
	
    * <U>Priority allocation</U> : 프로세스의 priority에 따라 다르게 할당

### **4-1. Global vs Local Replacement**

* <U>Global replacement</U>
	
    * Replace 시 다른 process에 할당된 frame을 빼앗아 올 수 있음
	
    * Process 별 할당량을 조절하는 또 다른 방법임
	
    * FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용 시에 해당
	
    * Working set, PFF 알고리즘 사용

* <U>Local replacement</U>
	
    * 자신에게 할당된 frame 내에서만 replacement
	
    * FIFO, LRU, LFU 등의 알고리즘을 process 별로 운영 시

&nbsp;

## **5. Thrashing**

* 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당 받지 못한 경우 발생

* Page fault rate이 매우 높아짐

* CPU utilization이 낮아짐

* OS는 MPD(Multiprogramming degree)를 높여야 한다고 판단

* 또 다른 프로세스가 시스템에 추가됨 (higher MPD)

* 프로세스 당 할당된 frame의 수가 더욱 감소

* 프로세스는 page의 swap in / swap out으로 매우 바쁨

* 대부분의 시간에 CPU는 한가함

* low throughput

**Thrashing Diagram**

![](https://images.velog.io/images/dogfootbirdfoot/post/ec3412a7-72dd-4d61-b9d6-f60dba304d6b/9-11.png)

Thrashing 현상을 막기 위해서는 multiprogramming degree(동시에 올라가있는 프로세스의 갯수)를 조절해 주어야 한다. 다음에 나오는 알고리즘은 이것을 가능하게 해주는 알고리즘이다.

### **5-1. Working-Set Model**

* Locality of reference
	
    * 프로세스는 특정 시간 동안 일정 장소들만을 집중적으로 참조한다.
	
    * 집중적으로 참조되는 해당 page들의 집합을 locality set이라고 한다.

* Working-Set Model
	
    * Locality에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page들의 집합을 Working set이라 정의한다.
	
    * Working Set Model에서는 process의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 frame을 반납한 후 swap out (suspend)
	
    * Thrashing을 방지함
	
    * Multiprogramming degree를 결정함

### **5-2. Working-Set Algorithm**

* Working set의 결정
	
    * Working set window를 통해 알아냄 (과거 참조)
	
    * Window size가 Δ인 경우
		
        * 시각 ti에서의 working set WS (ti)
			
            * Time interval [ti - Δ, ti2] 사이에 참조된 서로 다른 페이지들의 집합
		
        * Working set에 속한 page는 메모리에 유지, 속하지 않는 것은 버린다. 즉, 참조된 후 Δ시간 동안 해당 page를 메모리에 유지한 후 버린다.

![](https://images.velog.io/images/dogfootbirdfoot/post/a6209470-0a40-41e0-87b9-6501bc57fa0e/9-12.png)

* Working-Set Algorithm
	
    * Process들의 working set size의 합이 page frame의 수보다 큰 경우 일부 process를 swap out시켜 남은 process의 working set을 우선적으로 충족시켜준다. (MPD를 줄임)
	
    * Working set을 다 할당하고도 page frame이 남는 경우 swap out 되었던 process에게 working set을 할당한다. (MPD를 키움)

* Window size Δ
	
    * Working set을 제대로 탐지하기 위해서는 window size를 잘 결정해야 함
	
    * Δ 값이 너무 작으면 locality set을 모두 수용하지 못할 우려
	
    * Δ 값이 크면 여러 규모의 locality set 수용
	
    * Δ 값이 ∞이면 전체 프로그램을 구성하는 page를 working set으로 간주

### **5-3. PFF(Page-Fault Frequency) Scheme**

![](https://images.velog.io/images/dogfootbirdfoot/post/743ec3dd-dc6d-46b0-a5e4-01ab87b5af1c/9-13.png)

* Page-fault rate의 상한값과 하한값을 둔다.
	
    * Page fault rate이 상한값을 넘으면 frame을 더 할당한다.
	
    * Page fault rate이 하한값 이하이면 할당 frame 수를 줄인다.

* 빈 frame이 없으면 일부 process를 swap out

&nbsp;

## **6. Page Size의 결정**

* Page size를 감소시키면
	
    * 페이지 수 증가
	
    * 페이지 테이블 크기 증가
	
    * Internal fragmentation 감소
	
    * Disk transfer의 효율성 감소
		
        * Seek/Rotation vs Transfer
	
    * 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
		
        * Locality의 활용 측면에서는 좋지 않음

* 최신 trend
	
    * Larger page size
