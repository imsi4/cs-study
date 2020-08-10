# Ch 6.1 - 6.3



## 6.1 Background

- **Interprocess Communication (IPC)** (ch 3.4 참고)
  - **cooperating process**: 다른 프로세스들과 데이터를 공유하며 실행되고 있는 다른 프로세스들에 의해 영향을 받거나 다른 프로세스들에게 영향을 줄 수 있는 프로세스. 이를 위해서는 IPC가 필요하다.
  - IPC 모델들
    - **shared-memory model**
      - 협력하는 프로세스들 모두가 접근할 수 있는 공유 메모리가 생긴다.
      - message-passing model보다 더 빠르다.
    - **message-passing model**
      - 협력하는 프로세스들 간 통신이 메세지 교환으로 이루어진다.
      - 작은 양의 데이터를 주고받는데에 좋다.



- **producer-consumer problem** (ch 3.5 참고)
  - a **producer process** produces information that is consumed by a **consumer process**
  - ex) a compiler (producer) produces assembly code that is consumed by an assembler (consumer)
  - producer-consumer problem을 해결하기 위해서 **shared memory를 사용**하는데, 이때 producer process와 consumer process가 **병행으로 실행될 수 있도록 buffer를 사용**한다.



- **buffer** (ch 3.5 참고)
  - a region of a physical memory storage used to **temporarily store data while it is being moved** from one place to another
  - 2가지 종류의 buffer
    - **unbounded buffer**: buffer의 크기에 제한이 없다.
    - **bounded buffer**: buffer의 크기가 고정되어 있다.



- **race condition (경합 조건)**
  - 다중 프로그래밍 시스템에서 **두 명령어가 동시에 같은 기억 장소를 접근**할 때 그들 사이의 경쟁에 의해 수행 결과를 예측할 수 없게 되는 것이다.
  - 이 상황이 일어나지 않도록 하기 위해서 **process synchronization (처리 동기화)**, 즉 처리의 실행에서 시간에 따른 순서를 맞추는 것이 필요하다.
  - [참고](https://blog.naver.com/asqw887/221993414832)



## 6.2 The Critical-Section Problem

- **critical-section problem (임계 구역 문제)**
  - 모든 프로세스는 **critical section (임계 구역)**을 가지고 있는데, 이는 다른 프로세스와 공유하는 변수를 변경하거나, 테이블을 갱신하거나 파일을 쓰거나 하는 작업이 수행되는 구간이다. 즉, 다른 프로세스와 공유하는 데이터를 접근하고 수정하는 작업이 일어나는 모든 구간을 critical section이라고 한다. **한 프로세스가 자신의 critical section에서 수행하는 동안에는 다른 프로세스들은 그들의 critical section으로 들어갈 수 없다.**
  - critical-section problem을 해결하기 위해서는 **프로세스들이 synchronization(동기화)를 통해 협력적으로 데이터를 공유할 수 있도록 하는 protocol을 디자인**해야한다.
  - 코드의 구성
    - **entry section (진입 구역)**: 임계 구역으로 진입하기 위한 허가 요청을 구현하는 코드 부분
    - **critical section**
    - **exit section (퇴출 구역)**: 임계 구역 뒤를 따르는 코드 부분
    - **remainder section (나머지 구역)**: 코드의 나머지 부분들



- **critical-section problem에 대한 해결안**이 충족해야하는 조건
  - **mutual exclusion (상호배제)**
    - 프로세스 $P_i$가 자신의 critical section에서 실행되고 있으면, **다른 프로세스들은 그들의 critical section에서 실행될 수 없다.**
  - **progress (진행)**
    - 현재 자신의 critical section에서 실행되고 있는 프로세스가 없는 상태에서 자신의 critical section으로 진입하고자 하는 프로세스들이 존재할 때, **remainder section에서 실행 중이지 않은 프로세스들만 누가 다음으로 critical section으로 진입할 수 있는지를 결정하는데에 참여** 할 수 있으며, 이 선택은 무한정 연기 될 수 없다.
  - **bounded waiting (한정된 대기)**
    - **한 프로세스가 자신의 critical section에 진입하려는 요청을 한 후부터 그 요청이 허용될 때까지의 기간 사이**에 다른 프로세스들이 그들 자신의 critical section에 진입되도록 허용되는 횟수에 제한이 있어야한다.



- 운영 체제에서 critical section들을 다루기 위한 2가지 접근법
  - **preemptive kernels**
  - **nonpreemptive kernels**



## 6.3 Peterson's Solution

- **Peterson's solution**은 critical section problem을 **소프트웨어 기반으로 해결하는 방법**으로, **프로세스가 2개인 경우**에만 적용할 수 있다.

  - **$P_i$ 와  $P_j$ 라는 2개의 프로세스**가 있다고 가정하자

  - $P_i$ 프로세스의 구조

    ```c
    // 두 프로세스는 다음 2개의 데이터를 공유함
    int turn;
    boolean flag[2];
    
    while (true) {
      flag[i] = true;		// 프로세스 i가 critical section에 진입하고자한다.
      turn = j;		// 누가 진입할 차례인지를 말해준다. j가 진입하고자하면 양보를 해준다.
      while (flag[j] && turn == j)
        ;
      
      /* critical section */
    
      flag[i] = false;		// critical section을 다 실행하고 빠져나간다.
    
      /* remainder section */
    }
    ```

  - `turn`은 이번에 **어느 프로세스가 critical section에 진입할 차례**인지를 나타내는 변수이다.

  - `flag`는 **해당 프로세스가 critical section에 진입할 준비가 되어있는지**를 나타내는 배열이다.



- Peterson's solution이 위에서 언급한 **3개의 조건을 충족하는가?**
  - 충족한다! 설명은 [이 블로그 설명](https://blog.naver.com/asqw887/221993464902) 참고
  - 3개의 조건
    - mutual exclusion
    - progress
    - bounded waiting







