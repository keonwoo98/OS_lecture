# **Process Management**

> 본 글은 다음 강의를 들으며 정리한 내용입니다.  
> 강의 정보 : 운영체제 / 이화여대 반효경  
> [강의 링크](http://kocw.or.kr/home/cview.do?cid=3646706b4347ef09)

&nbsp;

## **1. 프로세스의 생성 (Process Creation)**

* Copy-on-write (COW) 기법

* 부모 프로세스(Parent process)가 자식 프로세스(children process) 생성

* 프로세스의 트리(계층 구조) 형성

* 프로세스는 자원을 필요로 함
	
    * 운영체제로부터 받는다
	
    * 부모와 공유한다

* 자원의 공유
	
    * 부모와 자식이 모든 자원을 공유하는 모델
	
    * 일부를 공유하는 모델
	
    * 전혀 공유하지 않는 모델

* 수행 (Execution)
	
    * 부모와 자식은 공존하며 수행되는 모델
	
    * 자식이 종료(terminate)될 때까지 부모가 기다리는(wait) 모델

* 주소 공간 (Address space)
	
    * 자식은 부모의 공간을 복사함 (binary and OS data)
	
    * 자식은 그 공간에 새로운 프로그램을 올림

* 유닉스의 예
	
    * `fork()` 시스템 콜이 새로운 프로세스를 생성
		
        * 부모를 그대로 복사 (OS data except PID + binary)
		
        
        * 주소 공간 할당
	
    * `fork` 다음에 이어지는 `exec()` 시스템 콜을 통해 새로운 프로그램을 메모리에 올림

* 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (`exit`)
	
    * 자식이 부모에게 output data를 보냄 (via `wait`)
	
    * 프로세스의 각종 자원들이 운영체제에게 반납됨

* 부모 프로세스가 자식의 수행을 종료시킴 (`abort`)
	
    * 자식이 할당 자원을 한계치를 넘어섬
	
    * 자식에게 할당된 태스크가 더 이상 필요하지 않음
	
    * 부모가 종료(exit)하는 경우
		
        * 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않는다.
		
        * 단계적인 종료

&nbsp;

## **2. fork() 시스템 콜**

A Process is created by the `fork()` system call.

* creates a new address space that is a duplicate of the caller.

```c
int	
	main()
{
	int		pid;

	pid = fork();
	if (pid == 0)
		printf("Hello, I am child!\n");
	else if (pid > 0)
		printf("Hello, I am parent!\n");
}
```

&nbsp;

## **3. exec() 시스템 콜**

A process can execute a different program by the `exec()` system call.

* replace the memory image of the caller with a new program.

```c
int
	main()
{
	int		pid;

	pid = fork();
	if (pid == 0)
	{
		printf("Hello, I am child! Now I'll run date.\n");
		execlp("/bin/date", "/bin/date", (char *)0);
	}
	else if (pid > 0)
		printf("Hello, I am parent!\n");	
}
```

&nbsp;

## **4. wait() 시스템 콜**

프로세스 A가 wait() 시스템 콜을 호출하면

* 커널은 child가 종료될 때까지 프로세스 A를 sleep시킨다. (block 상태)

* Child process가 종료되면 커널은 프로세스 A를 깨운다. (ready 상태)

![](https://images.velog.io/images/dogfootbirdfoot/post/d6c87fb0-9d86-4812-b086-cdadc04830c6/4-1.png)

&nbsp;

## **5. exit() 시스템 콜**

프로세스의 종료

### **5-1. 자발적 종료**

* 마지막 statement 수행 후 exit() 시스템 콜을 통해

* 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌

### **5-2. 비자발적 종료**

* 부모 프로세스가 자식 프로세스를 강제 종료시킴
	
    * 자식 프로세스가 한계치를 넘어서는 자원 요청
	
    * 자식에게 할당된 태스크가 더 이상 필요하지 않음

* 키보드로 kill, break 등을 친 경우

* 부모가 종료하는 경우
	
    * 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨

&nbsp;

## **6. 프로세스와 관련한 시스템 콜**

* `fork()` : create a child (copy)

* `exec()` : overlay new image

* `wait()` : sleep until child is done

* `exit()` : frees all the resources, notify parent

&nbsp;

## **7. 프로세스 간 협력**

### **7-1. 독립적 프로세스 (Independent process)**

* 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함

### **7-2. 협력 프로세스 (Cooperating process)**

* 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음

### **7-3. 프로세스 간 협력 메커니즘 (IPC : Interprocess Communication)**

* 메시지를 전달하는 방법
	
    * message passing : 커널을 통해 메시지 전달

* 주소 공간을 공유하는 방법
	
    * shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
	
    * tread : tread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 프로세스를 구성하는 thread들 간에는 주소 공간을 공유하므로 협력이 가능

&nbsp;

## **8. Message Passing**

* Message system
	
    * 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 통신하는 시스템

* Direct Communication
	
    * 통신하려는 프로세스의 이름을 명시적으로 표시

![](https://images.velog.io/images/dogfootbirdfoot/post/8bfc46eb-2248-4d52-8cdc-6ec90f59e98a/4-2.png)

* Indirect Communication
	
    * mailbox(또는 port)를 통해 메시지를 간접 전달

![](https://images.velog.io/images/dogfootbirdfoot/post/bb32fb63-2cbc-4e3a-86a2-1574cdc12473/4-3.png)

&nbsp;

## **9. Interprocess Communication**

![](https://images.velog.io/images/dogfootbirdfoot/post/5049070a-4acd-4e04-9246-1377ae18dc76/4-4.png)