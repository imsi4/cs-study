# chap5. CPU scheduling

## 5.3 Scheduling Algorithms

> CPU 스케줄링은 준비 완료 큐에 있는 어느 프로세스에게 CPU를 할당할 것인지를 결정하는 문제를 다룬다.

### 5.3.1 선입 선처리 알고리즘(FCFS, First Come First Served-scheduling)

> 가장 간단한 CPU 스케줄링 알고리즘이다.

| process | Burst Time |
| ------- | ---------- |
| P1      | 24         |
| P2      | 3          |
| p3      | 3          |

![image-20200801214923944](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20200801214923944.png)

평균 대기 시간 = (0 + 24 + 27) /3 = 17ms

하지만, 순서를 2->3->1 으로 바꾸면 평균 대기시간은 (6 + 0 + 3)/3 = 3ms이다. 따라서 FCFS상에서 평균 대기 시간은 일반적으로 최소가 아니며, 프로세스 CPU Burst time이 크게 변할 경우에는 평균 대기 시간도 상당히 변할 수 있다.

비선점형...

모든 다른 프로세스들이 하나의 긴 프로세스가 CPU를 양도하기를 기다리는 것을 convoy effect라고 한다. 이는 짧은 프로세스들이 먼저 처리 되도록 허용될 때보다 CPU와 장치 이용률이 저하되는 결과를 낳는다.

### 5.3.2 최단 작업 우선 스케줄링(Shortest-Job-First Scheduling)

> 말 그대로 죄단 작업을 우선적으로 실행하는 알고리즘

| process | Burst Time |
| ------- | ---------- |
| p1      | 6          |
| p2      | 8          |
| p3      | 7          |
| p4      | 3          |

![image-20200801220208882](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20200801220208882.png)

평균 대기 시간 = (3 + 16 + 9 + 0) / 4 = 7ms

FCFS 평균 대기 시간 = (0 + 6 + 14 + 21) / 4 = 10.25ms

따라서, SJF 알고리즘은 주어진 프로세스 집합에 대해 최소의 평군 대기 시간을 가진다.

SJF 알고리즘의 실제 어려움은 다음 CPU 요청의 길이를 파악하는 것이다. 일괄처리 시스템에서 장기 스케줄링을 위해서는 사용자가 작업을 제출할 때 지정한 처리 시간 제한을 이용할 수 있다.

이러한 경우 사용자들이 처리 시간 제한을 정확하게 예상하도록 하는 동기가 된다. 시간이 짧을 수록 신속한 응답을 기대할 수 있지만 너무 짧으면 시간초과 오류가 발생하게 되어 사용자는 작업을 다시 제출해야 하기 때문이다. 따라서 SJF 스케줄링은 장기 스케줄링에서 자주 사용된다.

SJF 알고리즘의 어려운 점은 다음 CPU 요청의 길이를 아는것이다.

하지만 CPU burst time은 I/O request가 나올때 까지 알 수 없다. 따라서 CPU burst time은 미리 정확하게 측정할 수 없다.

따라서, 데이터를 토대로 예측을 하려고 시도했다. 결과를 먼저 얘기하자면 예측은 잘 안되고 일관성도 없고 그렇기 때문에 좋지 못했다.

예측 방법은 지수 평균(exponential average)를 통해 예측했다. 가장 최근의 history가 영퍙을 미칠 것이라는 생각에서 시작.

![image-20200801223547747](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20200801223547747.png)



| process | Arrive Time | Burst Time |
| ------- | ----------- | ---------- |
| p1      | 0           | 8          |
| p2      | 1           | 4          |
| p3      | 2           | 9          |
| p4      | 3           | 5          |

![image-20200801223807698](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20200801223807698.png)

- 선점(SRTF)

  평균 대기 시간 = ((10 - 1) + (1 - 1) + (17 - 2) + (5 - 3)) / 4 = 6.5ms

- 비선점(SJF)

  평균 대기 시간  = (0 + (8 - 1) + (17 - 2) + (12 - 3)) / 4 = 7.75ms

### 5.3.3 Round-Robin Scheduling

> A small unit of time, called a time quantum or time slice, is defined.

time quantum or time slice은 일반적으로 10에서 100ms의 길이를 갖는다.(context switching time은 보통 10마이크로s 미만이다.)

ready queue는 round queue로 작동된다. 

컴퓨터 운영에서, 컴퓨터 자원을 사용할 수 있는 기회를 프로그램 프로세스들에게 공정할게 부여하기 위한 방법으로서, 각 프로세스에 일정 시간을 할당하고, 할당된 시간이 지나면 그 프로세스는 잠시 보류한 뒤 다른 프로세스에게 기회를 주고, 또 다음 프로세스에게 하는 식으로, 돌아가며 기회를 부여하는 운영방식이다.

프로세스가 엄청 많고 엄청 긴 프로세스가 포함 되어 있으면 무한 연기의 문제가 발생할 수 있다. 이를 방지 하는 것을 Aging기법이라고 한다. '우선순위 = (대기시간 + 서비스시간) / 서비스 시간'

| process | Burst time |
| ------- | ---------- |
| p1      | 24         |
| p2      | 3          |
| p3      | 3          |

![image-20200801231713572](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20200801231713572.png)

평균 대기 시간 = ((10 - 4) + 4 +7) / 3 = 5.66ms

적당한 time quantum을 정하는게 관건. 따라서 우리는 시간 할당량이 문맥 교환시간에 비해 더 클 것을 원한다. 문맥 교환시간이 시간 할당량의 10%에 근접하면, CPU 시간의 약 10%는 문맥 교환에 사용될 것이다. 또한 CPU 버스트의 80%는 시간 할당량보다 짧아야 한다.

### 5.3.4 우선순위 스케줄링(priority Scheduling)

> CPU는 가장 높은 우선순위를 가진 프로세스에게 할당된다. 우선순위가 같은 프로세스들은 FCFS 순서로 스케줄 된다.

우선순위는 일반적으로 일정 범위의 수가 사용된다. 하지만 숫자가 낮을 수록 높은 우선순위인지 숫자가 높을 수록 높은 우선순위인지에 대해선 일반적인 합의가 없다.

| process | Burst Time | priority |
| ------- | ---------- | -------- |
| p1      | 10         | 3        |
| p2      | 1          | 1        |
| p3      | 2          | 4        |
| p4      | 1          | 5        |
| p5      | 5          | 2        |

![image-20200801232355723](C:\Users\multicampus\AppData\Roaming\Typora\typora-user-images\image-20200801232355723.png)

평균 대기 시간 = (0 + 1 + 6 + 16 + 18) / 5 = 8.2ms

우선 순위 알고리즘의 주요한 문제는 indefinite blocking or starvation이다.(무한히 기다리는 것)

1. 실행 준비는 되어 있으나 CPU를 사용하지 못하는 프로세스는 CPU를 기다리면서 blocking 된 것으로 간주 될 수 있다. 앞이 끝날때 까지 무한히 기다리기..
2. 부하가 과중한 컴퓨터 시스템에서는 높은 우선순위의 프로세스들이 꾸준히 들어와서 낮은 우선순위의 프로세스들이 CPU를 얻지 못하게 될 수도 있다. 앞에 높은 우선순위 프로세스들이 끼어들기..

이를 해결하기위한 방법이 Aging기법이다.