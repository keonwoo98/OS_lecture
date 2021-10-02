# **Introduction to Operaing Systems**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. 운영체제(Operating System, OS)란 무엇인가?**

컴퓨터 하드웨어 바로 위에 설치되어 사용자 및 다른 모든 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층

![](https://images.velog.io/images/dogfootbirdfoot/post/37e70368-9cdc-4a2e-af6a-3679e921d656/1.png)

좁은 의미의 운영체제(커널)
* 운영체제의 핵심 부분으로 메모리에 상주하는 부분

넓은 의미의 운영체제
* 커널 뿐 아니라 각종 주변 시스템 유틸리티를 포함한 개념

&nbsp;

## **2. 운영체제의 목표**

컴퓨터 시스템을 **`편리하게 사용할 수 있는 환경을 제공`**
* 운영체제는 동시 사용자 / 프로그램들이 각각 독자적 컴퓨터에서 수행되는 것 같은 환상을 제공
* 하드웨어를 직접 다루는 복잡한 부분을 운영체제가 대행

![](https://images.velog.io/images/dogfootbirdfoot/post/f646d11c-c778-43e2-984c-b92fecc0eb4f/2.png)

컴퓨터 시스템의 **`자원을 효율적으로 관리`**
* 프로세서, 기억장치, 입출력 장치등의 효율적 관리
	* 사용자간의 형평성 있는 자원 분배
	* 주어진 자원으로 최대한의 성능을 내도록
* 사용자 및 운영체제 자신의 보호
* 프로세스, 파일, 메시지 등을 관리

![](https://images.velog.io/images/dogfootbirdfoot/post/abbac9dc-1655-46cd-a764-79ac01cbe3f2/3.png)

&nbsp;

## **3. 운영체제의 분류**

### **3-1. 동시 작업 가능 여부에 따른 분류**

* 단일 작업 (single tasking)
	* 한 번에 하나의 작업만 처리
	* 예) MS-DOS 프롬프트 상에서는 한 명령의 수행을 끝내기 전에 다른 명령을 수행시킬 수 없음

* 다중 작업 (multi tasking)
	* 동시에 두 개 이상의 작업 처리
	* 예) UNIX, MS Windows 등에서는 한 명령의 수행이 끝나기 전에 다른 명령이나 프로그램을 수행할 수 있음


### **3-2. 사용자 수에 따른 분류**

* 단일 사용자 (single user)
	* 예) MS-DOS, MS Windows

* 다중 사용자 (multi user)
	* 예) UNIX, NT server

### **3-3. 처리 방식에 따른 분류**

* 일괄 처리 (batch processing)
	* 작업 요청의 일정량 모아서 한꺼번에 처리
	* 작업이 완전 종료될 때까지 기다려야 함
	* 예) 초기 Punch Card 처리 시스템

![](https://images.velog.io/images/dogfootbirdfoot/post/141086cc-0173-462c-836d-f4635e6d5d0e/4.png)

* 시분할 (time sharing)
	* 여러 작업을 수행할 때 컴퓨터 처리 능력을 일정한 시간 단위로 분할하여 사용
	* 일괄 처리 시스템에 비해 짧은 응답 시간을 가짐
		* 예) UNIX
	* interactive한 방식

![](https://images.velog.io/images/dogfootbirdfoot/post/9636904e-7316-4d02-ab88-11a505db0363/5.png)

* 실시간 (Realtime OS)
	* 정해진 시간 안에 어떠한 일이 반드시 종료됨이 보장되어야 하는 실시간 시스템을 위한 OS
	* 예) 원자로 / 공장 제어, 미사일 제어, 반도체 장비, 로보트 제어

* 실시간 시스템의 개념 확장
	* Hard realtime system (경성 실시간 시스템)
	* Soft realtime system (연성 실시간 시스템)

&nbsp;

## **4. 유사한 의미의 몇 가지 용어**

아래 용어들은 컴퓨터에서 여러 작업을 동시에 수행하는 것을 뜻한다.
* Multitasking
* Multiprogramming : 여러 프로그램이 메모리에 올라가 있음을 강조
* Time sharing : CPU의 시간을 분할하여 나누어 쓴다는 의미를 강조
* Multiprocess

> Multiprocessor : 하나의 컴퓨터에 CPU (processor)가 여러 개 붙어 있음을 의미


## **4. 운영체제의 예**

### **유닉스**

* 코드의 대부분을 C언어로 작성 (C언어는 유닉스를 만들기 위해 만들어짐)
* 높은 이식성
* 최소한의 커널 구조
* 복잡한 시스템에 맞게 확장 용이
* 소스 코드 공개
* 프로그램 개발에 용이
* 다양한 버전
	* System V, FreeBSD, SunOS, Solaris
	* Linux

### **DOS (Disk Operating System)**

* MS사에서 1981년 IBM-PC를 위해 개발
* 단일 사용자용 운영체제, 메모리 관리 능력의 한계 (주 기억 장치 : 640KB)

### **MS Windows**

* MS사의 다중 작업용 GUI 기반 운영 체제
* Plug and Play, 네트워트 환경 강화
* DOS용 응용 프로그램과 호환성 제공
* 불안정성
* 풍부한 지원 소프트웨어

### **Handheld device를 위한 OS**

* PalmOS, Pocket PC (WinCE), Tiny OS

&nbsp;

## **5. 운영체제의 구조**

![](https://images.velog.io/images/dogfootbirdfoot/post/994f4ad6-d692-4e2e-93b4-523097d531fc/6.png)

&nbsp;

## **6. 운영체제 과목의 수강 태도**

본 과목은 OS 사용자 관점이 아니라 OS 개발자 관점에서 수강해야 한다.
* 대부분의 알고리즘은 OS 프로그램 자체의 내용
* 인간의 신체가 뇌의 통제를 받듯 컴퓨터 하드웨어는 운영체제의 통제를 받으며 그 운영체제는 사람이 프로그래밍하는 것이다.
* 본인을 Windows XP나 Linux 같은 운영체제라고 생각하고 본인의 할 일이 무엇인지를 생각해보면 본 과목에서 배울 내용이 무엇인지 명확히 알 수 있다.
