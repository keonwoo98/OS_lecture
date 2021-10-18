# **File Systems**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. File and File System**

* <U>File</U>
	
    * A named collection of related information (이름을 가지고 저장)
	
    * 일반적으로 비휘발성의 보조기억장치에 저장
	
    * 운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해 줌
	
    * Operation (연산)
		
        * create, read, write, reposition(lseek), delete, open, close 등

* <U>File attribute</U> (metadata)
	
    * 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
		
        * 파일 이름, 유형, 저장된 위치, 파일 사이즈
		
        * 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

* <U>File system</U>
	
    * 운영체제에서 파일을 관리하는 부분
	
    * 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
	
    * 파일의 저장 방법 결정
	
    * 파일 보호 등

&nbsp;

## **2. Directory and Logical Disk**

* <U>Directory</U>
	
    * 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
	
    * 그 디렉토리에 속한 파일 이름 및 파일 atrribute들
	
    * Operation
		
        * search for a file, create a file, delete a file
		
        * list a directory, rename a file, traverse the file system

* <U>Partition</U> (= Logical Disk)
	
    * 하나의 (물리적)디스크 안에 여러 파티션을 두는게 일반적
	
    * 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
	
    * (물리적)디스크를 파티션으로 구성한 뒤 각각의 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있음

&nbsp;

## **3. open()**

**open("/a/b/c")**

* 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴

* 이를 위하여 directory path를 search
	
    * 루트 디렉토리 `/`를 open하고 그 안에서 파일 `a`의 위치 획득
	
    * 파일 `a`를 open한 후 read하여 그 안에서 파일 `b`의 위치 획득
	
    * 파일 `b`를 open한 후 read하여 그 안에서 파일 `c`의 위치 획득
	
    * 파일 `c`를 open

* Directory path의 search에 너무 많은 시간 소요
	
    * Open을 read/write와 별도로 두는 이유임
	
    * 한번 open한 파일은 read/write 시 directory search 불필요

* Open file table
	
    * 현재 open된 파일들의 메타데이터 보관소 (in memory)
	
    * 디스크의 메타데이터보다 몇 가지 정보가 추가
		
        * Open한 프로세스의 수
		
        * File offset : 파일 어느 위치 접근 중인지 표시 (별도 테이블 필요)

* File descriptor (file handle, file control block)
	
    * Open file table에 대한 위치 정보 (프로세스 별)

![](https://images.velog.io/images/dogfootbirdfoot/post/6f10ff62-eb6e-43c4-bd21-76b00ee59d0f/10-1.png)

![](https://images.velog.io/images/dogfootbirdfoot/post/48f3ae06-27de-458c-a6ff-8693b246da94/10-2.png)

&nbsp;

## **4. File Protection**

각 파일에 대해 누구에게 어떤 유형의 접근(read/write/execution)을 허락할 것인가?

**Access Control 방법**

* <U>Access control Matrix</U>
	
    * Access control list : 파일 별로 누구에게 어떤 접근 권한이 있는지 표시
	
    * Capability : 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한 표시

![](https://images.velog.io/images/dogfootbirdfoot/post/12100c62-b594-4405-860e-4db255aca1b0/10-3.jpg)

* <U>Grouping</U>
	
    * 전체 user를 owner, group, public의 세 그룹으로 구분
	
    * 각 파일에 대해 세 그룹의 접근 권한(rwx)을 3비트씩으로 표시
	
    * 예 : UNIX

|owner|group|other|
|:--:|:--:|:--:|
|rwx|r--|r--

* <U>Password</U>
	
    * 파일마다 password를 두는 방법 (디렉토리 파일에 두는 방법도 가능)
	
    * 모든 접근 권한에 대해 하나의 password : all-or-nothing
	
    * 접근 권한별 password : 암기 문제, 관리 문제

&nbsp;

## **5. File System의 Mounting**

![](https://images.velog.io/images/dogfootbirdfoot/post/3455086e-f308-4cfe-b5f5-2b66216a1d43/10-4.png)

![](https://images.velog.io/images/dogfootbirdfoot/post/1a9f2097-f6ea-4402-869c-82c9aad35b9d/10-5.png)

&nbsp;

## **6. Access Methods**

시스템이 제공하는 파일 정보의 접근 방식

* 순차 접근 (sequential access)
	
    * 카세트 테이프를 사용하는 방식처럼 접근
	
    * 읽거나 쓰면 offset은 자동적으로 증가

* 직접 접근 (direct access, random access)
	
    * LP 레코드 판과 같이 접근하도록 함
	
* 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음

