# **File Systems Implementation**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. Allocation of File Data in Disk**

### **1-1. Contiguous Allocation**

* 장점
	
    * Fast I/O
		
        * 한번의 seek/rotation으로 많은 바이트 transfer
		
        * Realtime file용으로, 또는 이미 run 중이던 process의 swapping용
	
    * Direct access(=random access) 가능

* 단점
	
    * External fragmentation
	
    * File grow가 어려움
		
        * File 생성 시 얼마나 큰 hole을 배당할 것인가?
		
        * Grow 가능 vs 낭비 (internal fragmentation)

![](https://images.velog.io/images/dogfootbirdfoot/post/da14fbfc-95b4-47a5-b0e4-8308d07b2956/11-1.png)

### **1-2. Linked Allocation**

* 장점
	
    * External fragmentation 발생 안함

* 단점
	
    * No random access
	
    * Reliability 문제
		
        * 한 sector가 고장나 pointer가 유실되면 많은 부분을 잃음
	
    * Pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨림
		
        * 512 bytes/sector, 4 bytes/pointer

* 변형
	
    * <U>File-allocation table (FAT)</U>
		
        * 포인터를 별도의 위치에 보관하여 reliability와 공간 효율성 문제 해결

![](https://images.velog.io/images/dogfootbirdfoot/post/1b965e6f-5120-416f-9192-b0aa8a5febb6/11-2.png)

### **1-3. Indexed Allocation**

* 장점
	
    * External fragmentation 발생 안함
	
    * Direct access 가능

* 단점
	
    * Small file의 경우 공간 낭비 (실제로 많은 file들이 small)
	
    * Too large file의 경우 하나의 block으로 index를 저장하기에 부족
		
        * 해결 방안
			
            * linked-scheme
			
            * multil-level index

![](https://images.velog.io/images/dogfootbirdfoot/post/b55274f6-2253-47eb-b03d-b24eaef2cde0/11-3.png)

&nbsp;

## **2. UNIX 파일 시스템의 구조**

![](https://images.velog.io/images/dogfootbirdfoot/post/933af031-1fc3-4a34-b62c-7f03bb2a1295/11-4.png)

* Boot block
	
    * 부팅에 필요한 정보 (bootstrap loader)

* Super block
	
    * 파일 시스템에 관한 총체적인 정보

* Inode
	
    * 파일 이름을 제외한 파일의 모든 메타 데이터 저장

* Data block
	
    * 파일의 실제 내용 보관

![](https://images.velog.io/images/dogfootbirdfoot/post/ab7202b8-5477-4470-9ef1-ef8b9c805f3b/11-5.png)

&nbsp;

## **3. FAT 파일 시스템의 구조**

![](https://images.velog.io/images/dogfootbirdfoot/post/2386c9cb-27ad-47b5-b15b-8da0ce195618/11-6.png)

* Boot block에는 어떤 파일 시스템과 마찬가지로 부팅에 필요한 정보를 담고있다.

* 메타데이터의 일부(위치정보만)를 FAT에 보관하고 있다.

* 나머지 메타데이터는 디렉토리에 담겨있다.

* 직접 접근이 가능하다.

* 포인터 하나가 유실되더라도(bad sector) FAT에 내용이 있기 때문에(Data block과 FAT block의 분리) reliability 개선

* Linked Allocation의 단점 모두 극복

&nbsp;

## **4. Free-Space Management**

* <U>Bit map or Bit vector</U>
	
    * Bit map은 부가적인 공간을 필요로 함
	
    * 연속적인 n개의 free block을 찾는데 효과적

![](https://images.velog.io/images/dogfootbirdfoot/post/eedb5cce-f4d7-406d-9a97-474802f39fd1/11-7.png)

* <U>Linked list</U>
	
    * 모든 free block들이 링크로 연결 (free list)
	
    * 연속적인 가용공간을 찾는 것은 쉽지 않다.
	
    * 공간의 낭비가 없다.

![](https://images.velog.io/images/dogfootbirdfoot/post/edc5d437-83b7-4c39-9981-07ad753f1bf5/11-8.png)

* <U>Grouping</U>
	
    * linked list 방법의 변형
	
    * 첫 번째 free block이 n개의 pointer를 가짐
		
        * n - 1 pointer는 free data block을 가리킴
		
        * 마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐

![](https://images.velog.io/images/dogfootbirdfoot/post/1eb49fea-4f80-45bf-b504-20eaa4f3beb0/11-9.png)

* <U>Counting</U>
	
    * 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질에 착안
	
    * (first free block, # of contiguous free blocks)을 유지

&nbsp;

## **5. Directory Implementation**

* Linear list
	
    * <file name, file의 metadata>의 list
	
    * 구현이 간단
	
    * 디릭토리 내에 파일이 있는지 찾기 위해서는 linear search 필요 (time-consuming)

* Hash Table
	
    * Linear list + Hashing
	
    * Hash table은 file name을 이 파일의 linear list의 위치로 바꾸어줌
	
    * Search time을 없앰
	
    * Collision 발생 가능

![](https://images.velog.io/images/dogfootbirdfoot/post/64afb8a2-57f9-40ef-8ad4-979669b75529/11-10.png)

* File의 metadata의 보관 위치
	
    * 디렉토리 내에 직접 보관
	
    * 디렉토리에는 포인터를 두고 다른 곳에 보관
		
        * inode, FAT 등

* Long file name의 지원
	
    * <file name, file의 metadata>의 list에서 각 entry는 일반적으로 고정
	
    * file name이 고정 크기의 entry 길이보다 길어지는 경우 entry의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
	
    * 이름의 나머지 부분은 동일한 directory file의 일부에 존재

![](https://images.velog.io/images/dogfootbirdfoot/post/6668e70e-5b58-43cf-84ff-621bb9464ae9/11-11.png)

&nbsp;

## **6. VFS and NFS**

* <U>Virtual File System (VFS)</U>
	
    * 서로 다른 다양한 file system에 대해 동일한 시스템 콜 인터페이스(API)를 통해 접근할 수 있게 해주는 OS의 layer

* <U>Network File System (NFS)</U>
	
    * 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
	
    * NFS는 분산 환경에서의 대표적인 파일 공유 방법임

![](https://images.velog.io/images/dogfootbirdfoot/post/f2ac5a0a-47eb-4ab5-9e48-3c95910decd4/11-12.png)

&nbsp;

## **7. Page Cache and Buffer Cache**

* <U>Page Cache</U>
	
    * Virtual memory의 paging system에서 사용하는 page frame을 caching의 관점에서 설명하는 용어
	
    * Memory-Mapped I/O를 쓰는 경우 file의 I/O에서도 page cache 사용

* <U>Memory-Mapping I/O</U>
	
    * File의 일부를 virtual memory에 mapping 시킴
	
    * 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함

* <U>Buffer Cache</U>
	
    * 파일시스템을 통한 I/O 연산은 메모리의 특정 영역인 buffer cache 사용
	
    * File 사용의 locality 활용
		
        * 한번 읽어온 block에 대한 후속 요청 시 buffer cache에서 즉시 전달
	
    * 모든 프로세스가 공용으로 사용
	
    * Replacement algorithm 필요 (LRU, LFU 등)

* <U>Unified Buffer Cache</U>
	
    * 최근의 OS에서는 기존의 buffer cache가 page cache에 통합됨

![](https://images.velog.io/images/dogfootbirdfoot/post/07267c8f-d6cf-4ea8-8db7-470aff3b3ba6/11-13.png)

![](https://images.velog.io/images/dogfootbirdfoot/post/93a54e2d-5077-420e-944d-9e15a51c9ccd/11-14.png)

사용자 프로그램이 file을 접근하는 방식은 인터페이스가 2개 있다 :

* Read-Write System Call
	
    * cache에 있는 내용을 copy해서 process 공간에 전달
	
    * 운영체제의 중재

* Memory-Mapped I/O
	
    * copy가 아니라 mapping
	
    * memory에 올라온 file의 내용은 시스템콜을 하지 않고 자신이 CPU를 가지고 있으면서 직접 접근할 수 있기 때문에 빠르다는 장점이 있다.
	
    * buffer cache를 mapping하기 때문에 또 다른 mapping이 들어와서 share하게 되면 일관성 문제가 생긴다는 단점이 있다.
