# Ch 5.5

## 5.5 Multi-Processor Scheduling

- 여러 개의 CPU가 있으면 여러 개의 스레드가 병렬로 실행될 수 있는 **load sharing**이 가능해지는 장점이 있는 반면, 스케줄링은 더욱 복잡해진다.
- **multiprocessor**의 의미
  - 원래는 하나의 코어를 가진 CPU로 이루어진 프로세서가 여러개 있는 시스템을 지칭했지만, 현대에는 다음과 같은 시스템 구조들을 의미한다.
    - 멀티코어 CPU
    - 멀티스레드 코어
    - NUMA(non-uniform memory access) 시스템
    - heterogeneous multiprocessing



#### 5.5.1 Approaches to Multiple-Processor Scheduling

- **asymmetric multiprocessing** 
  - 하나의 **master server 프로세서**가 모든 스케줄링 결정을 하는 구조로, master 프로세서가 혼자서 모든 스케줄링 결정, I/O 프로세싱, 그리고 기타 시스템 활동들을 담당한다. 하지만 이 접근법의 단점은 master server에 **병목 현상**이 일어나면서 퍼포먼스가 떨어질 수 있다는 것이다. 
- **symmetric multiprocessing (SMP)**
  - 각 프로세서가 **각자 스스로 스케줄링**을 하는 구조이다. 보통 대부분의 시스템은 이 접근법을 따른다 (Windows, Linux, macOS, Android 등). 각 프로세서의 스케줄러가 ready queue에서 다음으로 실행될 스레드를 고르는 방식이다.
  - 실행되어야 할 스레드들을 순서대로 정리하기 위한 두 가지 방법
    - 모든 스레드들을 하나의 **common ready queue**에 넣기
      - 문제점: 두 개의 **서로 다른 프로세서가 같은 스레드를 선택하지 않도록** 유의해야한다. 이를 막기 위해 locking을 이용할 수 있으나 이렇게 하면 퍼포먼스 병목 현상이 나타날 수도 있다.
    - 각 프로세서가 각자의 **private ready queue**를 가지기
      - SMP를 따르는 대부분의 시스템들이 사용하는 방법이다.
      - 문제점: 프로세서 간의 **workload가 불균형** 할 수 있다. 이는 balancing algorithm들을 이용해서 해결할 수 있다.



#### 5.5.2 Multicore Processors

- **multicore processor**는 하나의 프로세서 칩에 여러 개의 코어가 돌아가는 구조이다. 운영체제의 입장에서는 **각 코어가 별개의 논리적 CPU로 인식**되어진다.



- **chip multithreading (CMT) / hyper-threading / simultaneous multithreading (SMT)**

  - 프로세서가 메모리보다 훨씬 더 빠른 탓에 프로세서가 메모리를 접근해서 필요한 데이터를 가져오는데에 많은 시간을 보내기 때문에 (**memory stall**), 최근에 많은 하드웨어들은 **multicore processor에서 하나의 코어 당 2개 이상의 hardware thread**를 가지도록 하는 경우가 많다. 이러한 구조를 가지면 하나의 스레드에서 메모리에서 데이터를 가져오기 위해 기다리는 동안 코어는 다른 스레드로 스위치할 수 있다. 
  - 각 hardware thread는 instruction pointer와 register set 등을 갖추었기 때문에 **운영체제의 입장에서는 software thread를 실행 시킬 수 있는 하나의 논리적 CPU로 인식**되어진다.
  - 하지만 cache나 pipeline과 같은 코어의 물리적 자원들은 코어 내의 스레드들 간에 공유되기 때문에 **하나의 코어는 한번에 하나의 스레드만을 실행** 시킬 수 있다.

  

- 코어를 multithread하기 위한 두 가지 방법

  - **coarse-grained multithreading**
    - 하나의 스레드를 실행하다 memory stall과 같은 긴 latency를 가진 일이 생겼을 때 다른 스레드로 바꾸는 방법
    - thread switching의 cost가 높다
  - **fine-grained multithreading**
    - coarse-grained보다 더 자잘자잘한 경계에서 thread switching이 일어나는 방법으로, 보통 하나의 instruction cycle이 끝나면 코어가 다른 스레드로 switch한다.
    - thread switching의 cost가 비교적 작다



- multithreaded, multicore processor에서 일어나는 **두 가지 단계의 스케줄링**
  - 1단계: **어떤 software thread가 어떤 hardware thread에 돌아갈지** (운영체제에 의해 결정)
    - 5.3에 나온 알고리즘들을 사용
  - 2단계: **코어 내에서 어떤 hardware thread를 선택해서 실행할지** (각 코어 내에서 결정)
    - round-robin algorithm
    - 각 hardware thread에 urgency(priority)를 부여해서 thread switch 상황이 오게되면 각 스레드의 중요도를 비교해서 현재 실행 중인 스레드를 다른 스레드로 바꿀지 말지 선택



#### 5.5.3 Load Balancing

- SMP 구조에서 multiprocessor의 장점을 최대화하려면 각 프로세서의 workload가 항상 비슷하도록 유지해야한다. 이를 **load balancing**이라 한다.
  - **push migration**: 각 프로세서의 workload를 수시로 체크해서 만약 불균형적으로 분배되어있으면 바쁜 프로세서에서 한가로운 프로세서에게 thread들을 이동(push)하는 방법
  - **pull migration**: 한가로운 프로세서가 바쁜 프로세서에 대기 중인 task를 가져오는(pull) 방법



#### 5.5.4 Processor Affinity

- **warm cache**
  - 하나의 프로세서에서 실행되고 있는 스레드가 가장 최근에 접근한 데이터들은 프로세서의 캐시에 저장이 되어있다. 따라서, 이후에도 똑같은 데이터들이 필요한 경우에는 바로 캐시에서 접근할 수 있으며, 이를 **warm cache**라고 한다.
- **processor affinity**
  - 만약 load balancing과 같은 이유로 위에서 언급한 스레드가 다른 프로세서로 옮겨지게 된다면, **기존의 프로세서의 캐시는 지워지고 옮겨진 프로세서의 캐시에 스레드가 필요한 데이터를 다시 새롭게 적재**해야할 것이다. 이러한 과정은 costly하기 때문에, SMP 구조를 가진 대부분의 운영체제들은 웬만하면 스레드가 다른 프로세서로 옮겨지지 않고 기존의 프로세서에서 계속 실행될 수 있게 한다. 이처럼 프로세스가 현재 실행되고 있는 프로세서에 대한 친밀(애착?)을 가지는 것을 **processor affinity**라고 한다.
  - 따라서 **load balancing과 processor affinity는 항상 상충**된다.
  - 위에서 언급한 common ready queue와 private ready queue의 구조들을 processor affinity의 관점에서 비교해보면, **common ready queue**에서는 하나의 스레드가 모든 프로세서에 의해 선택될 수 있기 때문에 만약 기존의 프로세서와 다른 프로세서에 의해 선택되면 캐시를 다시 적재해야한다. 반면에 **private ready queue**에서는 항상 같은 프로세서에서 실행되는 것이 보장되기 때문에 processor affinity가 보장된다고 할 수 있다.
- processor affinity의 종류
  - **soft affinity**
    - 운영체제가 웬만하면 하나의 프로세스가 계속 같은 프로세서에서 실행되도록 노력하지만 항상 그럴 것이라는 보장은 없다.
  - **hard affinity**
    - 하나의 스레드가 실행될 수 있는 프로세서들의 부분집합을 정의해서 항상 그 프로세서들에서만 실행되도록 한다.
  - 많은 운영체제들은 soft와 hard affinity 둘 다 제공한다.



#### 5.5.5 Heterogeneous Multiprocessing

- **heterogeneous multiprocessing (HMP)**
  - 다양한 사양(clock speed, power management 등)의 코어들을 가지는 구조로, 각 task가 요구하는 사양에 따라 그에 맞는 코어에 배치함으로써 power consumption을 효율적으로 관리하는 방법이다.

