# Operating-System Structures Q&A

### 1. GUI가 무엇인가?

- Graphical User Interface의 약자로서 사용자 친화적인 방식을 의미한다. 데스크탑이라고 불리는 마우스를 기반으로 하는 윈도우 메뉴 시스템을 예시로 들 수 있다.

### 2. System Call이 무엇인가?

- OS에 의해 이용가능하게 만들어진 서비스를 제공한다. 
- OS를 운용하는데 있어서 필요한 기능을 호출 할때 방생하는 신호라 할 수 있다.

### 3. 시스템 서비스(시스템 프로그램)엔 무엇이 있는가?

- 시스템 유틸리티로도 알려진, 프로그램 개발과 실행을 위해 보다 편리한 환경을 제공한다.
- 파일관리, 상태 정보, 파일 변경, 프로그래밍 언어지원, 프로그램 적재와 수행, 통신, 백그라운드 서비스 등을 제공한다.

### 4. Linker와 Loader의 차이는?

- Linker는 컴퓨터가 읽을 수 있는 이진 파일로 결합하는 역할을 하며 Loader는 CPU core 실행을 위해 이진 파일을 메모리에 적재하는 것을 의미한다.

### 5. CLI를 많이 사용하는 경우는?

- 컴퓨터의 관리자나 시스템의 깊은 지식을 갖고 있는 Power user의 경우 CLI를 많이 사용한다.
- 왜냐하면 그것들을 더 효율적이고 빠르게 실행에 접근 할 수 있기 때문에, 보통 반복적으로 해야하는 행동에 대해 CLI를 많이 이용한다.

### 6. 운영체제가 매개변수를 처리하기 위한 방법을 설명하라

- 가장 간단한 방법은 매개변수를 레지스터 내에 전달하는 것
- 레지스터보다 더 많은 매개 변수가 존재할 시 블록이나 테이블에 저장되고, 블록의 주소가 레지스터 내에 매개변수로 전달된다.
- 매개변수는 프로그램에 의해 스택(Stack)에 push할 수 있고, 운영체제에 의해 pop off할 수 있다. 

### 7. 사용자가 운영체제와 접촉하는 두가지 기본 방식은?

- 명령 해석기 : 명령어 해것기의 중요한 기능은 사용자가 지정한 명령을 가져와서 그것을 수행하는 것이다. 대화형 시스템 상에서 명령을 입력함으로서 운영체제 동작을 한다.
- GUI : 사용자 친화적인 접촉 방식으로 데스크탑이라고 불리는 마우스를 기반으로 하는 윈도우 메뉴 시스템을 사용하는 것으로 마우스 포인터의 위치와 클릭등을 이용하여 os에 접촉할 수 있다.

### 8. 실제 시스템 호출을 부르지 않고 API에 따라 프로그래밍 하는 것을 선호하는 이유는?

- 프로그램의 호환성 관련 : API에 따라 프로그램을 설계하는 응용 프로그래머는 자신의 프로그램이 같은 API를 지원하는 어느 시스템에서건 컴파일 되고 실행된다는 것을 기대 할 수 있다.
- 실제 시스템 호출은 애매모호 하지만(자세한 설명 및 이해가 필요),  API 함수 호출은 쉽다. 그럼에도 불구하고 API 함수를 호출하는 것과 커널의 관련된 시스템 호출을 호출한 것에는  강한 상관관계가 있다.