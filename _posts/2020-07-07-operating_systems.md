---
title: "Operating Systems"
toc: true
toc_sticky: true
categories:
  - CS
---

## Process와 Thread

### Process (프로세스)

<center> <img src='../../assets/images/os_review/process.png' width="500"> </center>

- 실행 중인 프로그램
- Code, data, stack, heap의 구조로 이루어진 독립된 메모리 영역을 할당받음
- 기본적으로 하나의 프로세스에는 최소 1개의 스레드 (메인 스레드)를 가지고 있음
- IPC (Inter-process communication)로 프로세스 간의 통신

### Thread (스레드)

<center> <img src='../../assets/images/os_review/thread.png' width="500"> </center>

- 프로세스 내에 실행되는 실행 흐름의 단위
- 프로세스 내에서 stack만 각각 따로 할당받고 code, data, heap 영역은 공유함
- 각각의 스레드는 별도의 레지스터와 stack을 갖고, 다른 영역들은 서로 읽고 씀
- 공유 변수 수정 등 자유롭게 다른 스레드와 통신

---

## Multi-processing과 Multi-threading

<center> <img src='../../assets/images/os_review/multi.png' width="600"> </center>

### Multi-processing (멀티 프로세싱)

<center> <img src='../../assets/images/os_review/multiprocessing.jpg' width="450"> </center>

- 하나의 메모리를 공유하여 CPU (프로세서)를 여러 개 (정확히는 CPU 속에 multi-core) 이용해 벙렬로 실행. <U>각각의 CPU에서는 여러 개의 프로세스들을 병렬로 실행</U> (multi-tasking)
- **프로세스 간의 context switching 시에 공유된 메모리 만큼의 시간과 자원 손실이 생김** (단순히 CPU 레지스터 교체 뿐만이 아니라 RAM과 CPU 사이의 캐쉬메모리에 대한 데이터까지 초기화되므로 상당한 부담이 발생함)

### Multi-threading (멀티 스레딩)

<center> <img src='../../assets/images/os_review/multithreading.jpg' width="450"> </center>

- 하나의 프로세스 안에서 여러 개의 스레드들을 병렬로 실행
- 하나의 프로세스 내에서 stack만 각각 따로 할당받고 code, data, heap 영역은 공유하기 때문에 복잡한 과정을 거치지 않고 **자원을 효율적으로 관리하고 운영**할 수 있음

### 추가 1) Multi-tasking (멀티 테스킹)

- 하나의 CPU 상에서 운영 체제의 스케줄링에 따라 여러 개의 프로세스들을 병렬로 실행
- 사용자에게는 동시에 실행되는 것처럼 보이지만 실제로는 병렬로 실행됨
- Context switching 시에 multi-processing과 같은 문제가 생김
- 밑에 있는 멀티 프로그래밍과는 다르게 <U>그저 여러 개의 프로세스들을 병렬로 실행하는 것</U> (이유가 자원 낭비를 막기 위함이 아님)

> 여기서 task란 운영 체제에서 최초에는 작업의 최소 단위인 process로 불리었으나, thread 기술이 개발된 이후로는 thread와 동일한 것으로 바뀌었다. 즉, 운영 체제에서의 task는 process 또는 thread로 불려진다.

### 추가 2) Multi-programming (멀티 프로그래밍)

- 멀티 태스킹과 의미는 같지만 관점이 다르다.
- 하나의 CPU 상에서 여러 개의 프로그램들을 병럴로 실행
- CPU가 하나의 프로그램에서 입출력 작업의 종료를 대기할 동안 다른 프로그램을 수행할 수 있도록 하는 것
- **CPU의 자원 낭비를 막기 위해 생김**
- 사용자에게는 모든 프로그램이 마치 동시에 수행되는 것처럼 보임

---

## Memory Structure (Code, Data, Stack, Heap)

- Code (전체 코드)
  - 실행할 프로그램의 코드가 저장되는 영역
  - 컴파일 시 크기가 결정됨
- Data (전역, 정적)
  - 전역 (global) 변수, 정적 (static) 변수, 정적 배열, 정적 구조체 등이 있는 영역
  - 컴파일 시 크기가 결정됨
- Stack (지역, 매개)
  - 지역 (local) 변수, 매개변수, 리턴 값과 같이 임시로 사용하는 것들에 대한 영역
  - 런타임 시 크기가 결정됨
- Heap (동적)
  - New, malloc 등의 동적 할당 객체에 대한 영역
  - 런타임 시 크기가 결정됨

> Heap과 stack 영역은 같은 공간을 공유한다. Heap이 메모리 위쪽 주소부터 할당되면 stack은 아래쪽부터 할당되는 식이다. 그래서 각 영역이 상대 공간을 침범하는 일이 발생할 수 있는데 이를 각각 heap overflow, stack overflow라고 말한다. Stack 영역이 크면 클 수록 heap 영역이 작아지고, heap 영역이 크면 클수록 stack 영역이 작아진다.

---

## Context Switch

- 하나의 프로세스가 CPU를 사용 중인 상태에서 다른 프로세스가 CPU를 사용하도록 하는 것
- 하나의 프로세스를 진행하다가 I/O, event, system call 등이 발생했을 때 현재 진행하고 있던 CPU data (context)를 PCB (Process Control Block)에 담고 메모리에 저장하고 난 뒤 다른 프로세스를 불러와서 진행하는 것

> Malloc할 때 느린 이유는?

> 커널 모드에 진입해서 memory allocation을 하고 다시 유저 모드로 빠져나올 때 일어나는 일을 살펴보자. 먼저 커널 모드에 진입할 때 context switch가 일어난다. 그러면 현재 진행하고 있던 data들을 다 PCB에 담고 메모리에 올려놨다가 커널 모드에 있는 프로세스들을 불러서 수행하고 다 끝나면 다시 원래 있던 대로 돌아와서 메모리에서 PCB에 있는 data들을 CPU에 올려놓는다.

---

## Scheduling (FCFS, SJF, HRRN, SRTF, RR)

- **Multi-programming**을 가능하게 하는 운영 체제의 동작 기법
- 하나의 CPU를 여러 프로세스로 나눠쓰고 싶을 때 최대한 효율적으로 하는 방법
- CPU의 자원 낭비를 줄이기 위해 (또는 idle time을 줄이기 위해) 사용
- Context switch의 상위 개념

### Non-preemptive Scheduling과 Preemptive Scheduling

- Non-preemptive Scheduling (비선점형 스케줄링)
  - 어떤 프로세스가 CPU를 할당 받으면 그 프로세스가 종료되거나 입출력 요구가 발생하여 자발적으로 중지될 때까지 계속 실행되도록 보장
- Preemptive Scheduling (선점형 스케줄링)
  - 어떤 프로세스가 CPU를 할당받아 실행 중에 있어도 다른 프로세스가 실행 중인 프로세스를 중지하고 **CPU를 강제로 점유**할 수 있음
  - 모든 프로세스에게 CPU 사용 시간을 동일하게 부여할 수 있음

### First Come First Served (FCFS)

- 먼저 자원 사용을 요청한 프로세스에게 자원을 할당해주는 방식

### Shortest Job First (SJF)

- CPU 점유 시간이 가장 짧은 프로세스에 CPU를 먼저 할당해주는 방식

### Highest Response Ratio Next (HRRN)

- 프로세스 처리의 우선 순위 (priority)를 CPU 처리 기간과 해당 프로세스의 대기 시간을 동시에 고려해 선정하는 방식

$$Priority = \frac{waiting \,\, time + estimated \,\, run \,\, time}{estimated \,\, run \,\, time}$$

(waiting time: 대기 시간, estimated run time: 처리 시간)

### Shortest Remaining-Time First (SRTF)

- SJF 스케줄링을 비선점에서 선점 형태로 수정한 형태

### Round Robin Scheduling (RR)

- 프로세스들 사이에 우선순위를 두지 않고, 순서대로 time slice를 이용하여 CPU를 할당하는 방식
- Time slice란 미리 정의된 CPU time
- 적당한 크기의 time slice를 찾는 것이 어려움. 너무 크게 잡으면 FCFS, 너무 작게 잡으면 context switch 시에 overhead가 높아짐

---

## Process Synchronization

- 협력하는 프로세스들 사이에서 실행 순서 규칙을 정하여 공유된 자원의 일관성 (consistency)을 보장하는 것
- 하나의 프로세스가 공유된 자원에 접근하고 난 뒤에 다른 프로세스가 쓰도록 만들어주어야 함

### Critical Section (Shared Resource)

<center> <img src='../../assets/images/os_review/criticalsection.png' width="600"> </center>

$n$개의 프로세스들이 있을 때, 이 프로세스들은 공유된 자원을 사용하기 위해 경쟁한다 (race condition). 각각의 프로세스에는 "code segment"라는 것이 있는데 일반적으로 "Critical Section (CS)"이라고 불린다. CS을 통해 공유된 자원에 접근할 수 있다.

> CS resource that "allows only one user at a time".

- 위의 문장과 같이 한 번에 하나의 프로세스만 허용
- Race condition이 일어나지 않기 위해 사용

### Solutions for CS problem

- Mutual exclusion (상호 배제)
  - 하나의 프로세스가 CS 내에서 실행 중일 때 다른 프로세스는 CS으로 진입할 수 없음
- Progress (진행)
  - CS에서 실행 중인 프로세스가 없다면 CS으로 진입하려는 프로세스들 중 하나는 반드시 유한한 시간 내에 진입해야함
- Bounded waiting (한정된 대기)
  - 다른 프로세스의 starvation (기아)를 방지하기 위해 CS에 한 번 접근했던 프로세스는 다시 CS에 들어갈 때 제한을 둠
  - 즉, CS에 대한 진입 요청 후 무한히 기다리지 않도록 함

---

## Mutex와 Semaphore

- Synchronization 방법 중 여러 프로세스나 스레드가 공유 자원에 접근하는 것을 제어하기 위한 방법들

### Mutex

<center> <img src='../../assets/images/os_review/mutex.png' width="500"> </center>

- “Mutual Exclusion"의 약자로서 CS를 가진 프로세스 (or 스레드)들에 의해 소유될 수 있는 key와 lock, unlock을 기반으로 한 상호 배제 기법
- 오직 1개만의 프로세스 (or 스레드)만 접근 가능
- Mutex는 semaphore가 될 수 없음
- Mutex 구현의 문제: starvation (기아) - 무한 대기
  - 높은 우선순위의 프로세스가 계속 들어오면 무한히 대기만 하는 상황이 발생
  - 시간이 지날 수록 우선순위를 높여주는 방법으로 해결 (aging)

### Semaphore

<center> <img src='../../assets/images/os_review/semaphore.png' width="500"> </center>

- Signaling mechanism. 현재 공유 자원에 접근할 수 있는 프로세스 (or 스레드)의 수를 나타내는 값을 두어 상호 배제를 달성하는 기법
- Semaphore의 정수형 변수인 S 만큼 프로세스 (or 스레드)가 접근할 수 있음
- Semaphore는 mutex가 될 수 있음
- Semaphore 구현의 문제: busy waiting
  -  Lock의 해제를 기다리는 동안 빈 반복문을 계속 도는 것을 반복하기 때문에 context switching이 발생
  -  따라서 block()을 통해서 프로세스를 대기 상태로 전환하고 CPU 스케줄링을 실행

### Reference

- [https://worthpreading.tistory.com/90](https://worthpreading.tistory.com/90)

---

## Deadlock

<center> <img src='../../assets/images/os_review/deadlock.png' width="600"> </center>

- 두 개 이상의 프로세스가 서로 lock을 해제하기만을 기다리는 상황으로써 서로의 작업이 끝나기만을 기다리고 있는 상태. 결과적으로 아무것도 완료되지 못하는 상태
- CS에 아무것도 들어있지 않은데 deadlock이 걸려서 아무도 들어갈 수 없는 상태
- Multi-programming에서 흔히 발생할 수 있는 문제

### Deadlock의 발생 조건

- Mutual exclusion (상호 배제): 자원은 한 번에 하나의 프로세스만이 사용할 수 있어야 함
- Hold and wait (점유 대기): 하나의 프로세스가 최소한 하나의 자원을 점유하고 있으면서도 다른 프로세스에 할당되어 있는 자원을 추가로 점유하기 위해 대기하고 있어야 함
- No preemption (비선점): 다른 프로세스에 할당된 자원은 사용이 끝날 때까지 강제로 빼앗을 수 없음
- Circular wait (순환 대기): 프로세스의 집합 ${P_0, P_1}$이 있다고 했을 때, $P_0$의 프로세스는 $P_1$의 프로세스가 점유한 자원을 대기하고, $P_1$의 프로세스는 $P_0$의 프로세스가 점유한 자원을 대기해야 함

---

## Synchronous와 Asynchronous

### Synchronous (동시에 일어나는)

<center> <img src='../../assets/images/os_review/synchronous.png' width="600"> </center>

- 요청을 하면 시간이 얼마가 걸리던지 요청한 자리에서 결과가 주어져야 함
- 설계가 매우 간단하고 직관적
- 결과가 주어질 때까지 아무것도 못하고 대기해야 함
- System call이 끝날 때까지 기다리고 결과물을 가져옴

### Asynchronous (동시에 일어나지 않는)

<center> <img src='../../assets/images/os_review/asynchronous.png' width="600"> </center>

- 요청과 결과가 동시에 일어나지 않음
- 동기보다 복잡
- 기다리는 시간동안 다른 작업을 할 수 있으므로 자원을 효율적으로 사용
- System call이 완료되지 않아도 나중에 완료가 되면 그 때 결과물을 가져옴

---

## User-level Thread와 Kernel-level Thread

<center> <img src='../../assets/images/os_review/user_kernel1.png' width="700"> </center>

- 사용자 레벨 스레드는 말그대로 우리가 `#include <thread>` 혹은 import를 통해 스레드를 이용하는 것
- 커널 레벨 스레드는 커널 내에 있는 스레드를 의미하게 되고 위와 같이 3가지 방법으로 나뉨

<center> <img src='../../assets/images/os_review/user_kernel2.png' width="450"> </center>

- 스케줄러가 컨텍스트 스위칭 하는 단위는 커널 스레드
- 컨텍스트 스위칭을 당하며 저장되는 정보가 PCB
- 어떤 인터럽트가 들어오면 OS의 스케줄러에 의해
  - 커널 레벨 스레드가 컨텍스트 스위칭을 당하면서 TCB (Thead Control Block)가 저장한 뒤 스왑
  - 프로세스 자체가 컨텍스트 스위칭될 때는 PCB가 저장한 뒤 스왑

### Reference

- [https://www.crocus.co.kr/1255](https://www.crocus.co.kr/1255)
- [https://www.crocus.co.kr/1404](https://www.crocus.co.kr/1404)
