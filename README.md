# 개념 정리장

> 개발하면서 알아간 내용들을 정리하였습니다.

## Duck Typing(덕 타이핑)

> 만약 어떤 새가 오리처럼 걷고, 헤엄치고, 꽥꽥거리는 소리를 낸다면 나는 그 새를 오리라고 부를 것

- 덕 타이핑은 객체가 어떤 타입에 걸맞은 변수와 메소드를 지니면 객체를 해당 타입에 속하는 것으로 간주함
- 타입에 대한 검사가 유연해졌다고 볼 수 있음
- 취향이나 환경에 따라, 명시적으로 타입을 주입해 이러한 경우를 제약하거나 테스트를 통해 신경쓸 필요가 있음

## 사이드카(Sidecar) 패턴

![img](https://image.samsungsds.com/kr/insights/mesh_img03.jpg?queryString=20220820100250)

- 모든 응용 프로그램 컨테이너에 사이드카 컨테이너를 추가하여 배포
- 서비스에 들어오거나 나가는 모든 네트워크 트래픽을 처리
- 가장 큰 특징은 비즈니스 로직이 포함된 실제 서비스와 사이드카가 병렬로 배치되기 때문에 서비스가 타 서비스를 직접 호출하는 것이 아니라 프록시(proxy)를 통해서 호출한다는 점
- 따라서 대규모의 마이크로서비스 환경이더라도 별도 작업 없이 서비스 연결뿐만 아니라 로깅, 모니터링, 보안, 트래픽 제어가 가능

![img](https://www.redhat.com/cms/managed-files/service-mesh-1680.png)



## CQS

**C**ommand and **Q**uery **S**eparation. 

CQS는 명령과 조회를 연산 수준에서 분리

## CQRS패턴

**C**ommand **Q**uery **R**esponsibility **S**egregation, 명령 조회 책임 분리 패턴(읽기와 쓰기 분리)

- CQRS는 CQS 원리에 기원
- 사실 CQRS는 처음엔 CQS의 확장으로 얘기되었음
- CQS는 명령과 조회를 연산 수준에서 분리하는 반면 CQRS는 개체(object)나 시스템(혹은 하위 시스템) 수준에서 분리

![쓰기와 읽기의 분리 과정](https://engineering-skcc.github.io/assets/images/msa/MSA3.14.png)

- 윗 그림에서 Write Data Store 과 Read Data Store 은 결과적 일관성(Eventual Consistency)추구



## 동기와 비동기, 블락과 논블락

### 동기(Synchronous)

**동기**방식은 요청한 작업에 대해 관심을 가지고 기다리는 방식

요청을 했을 때 시간이 많이 걸리더라도 결과를 기다려야 함

### 비동기(Asynchronous)

**비동기**방식은 요청한 작업에 대해 관심을 버리고 기다리지 않는 방식

요청을 하고 다른 일을 처리

![동기(Sync) 와 비동기(ASync)](https://t1.daumcdn.net/cfile/tistory/2776293757C7D0C522)

### 블락(Block)

일반적으로 함수 A가 함수 B를 호출하면, 프로세스의 제어권은 함수 B로 넘어감
함수 B가 프로세스의 제어권을 가지고 있는 동안 함수 A는 아무것도 하지 않게 되는데, 이 상태를 **블락**이라고 함
또 이런 함수 B를 블락킹 함수라고 함
함수 B가 모두 실행되고, 프로세스의 제어권이 다시 함수 A로 오게 되면 함수 A의 **블락** 상태는 해제됨

### 논블락(Nonblock)

함수 A에서 함수 B를 스레드로 생성하는 함수를 호출했을 때 스레드를 생성하는 함수는 함수 B를 별도의 스레드로 생성하고, 특정 객체를 바로 리턴함
함수 A가 있는 스레드는 함수 호출 이후의 일을 계속해서 하게됨
이 과정에서 함수 A는 **블락**상태를 가지지 않으며, 이 상태를 **논블락**이라고 함
또 이런 함수 B를 논블락킹 함수라고 함

블락/논블락을 접하는 가장 대표적인 사례가 I/O 관련 코드를 작성할 때임

### 동기/비동기 vs 블락/논블락의 차이

![추가적인 Blocking / Non-Blocking의 개념에 대해서 - 인프런 | 강의 공지사항](https://cdn.inflearn.com/public/comments/08f0d98d-5a36-4701-880e-e3ca0189d2a3/%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5.png)

동기/비동기는 한 작업에서 다른 작업의 작업완료 여부에 관심이 있느냐/없느냐에 있음

- 관심이 있다면 **동기**
- 관심이 없다면 **비동기**

블락/논블락은 프로세스 제어권을 뺏기는 상태에 대한 내용

- 함수 A가 함수 B를 호출하고 B가 실행되는 동안 프로세스 제어권을 뺏겨 본인 로직을 실행하지 못하는 경우 **블락**
- 반면 프로세스 제어권을 뺏기지 않고 바로 리턴 받아 본인의 로직을 실행하는 **논블락**

일반적으로 **동기/블락** 형태와 **비동기/논블락** 방식이 쓰입니다.

- **동기/블락** 방식은 이해하기 쉽고 직관적이지만 일반적으로 **느림**
- **비동기/논블락** 방식은 이해하기 어렵고, 프로그램 흐름도 어려워지지만 일반적으로 **빠름**

##### 동기 / 블락

```python
# sync / block
import time

def a():
    print("start in a()")
    time.sleep(2)
    print("finished in a()")

def b():
    print("start in b()")
    time.sleep(2)
    print("finished in b()")

def task():
    print("start in task()")
    a()
    b()
    print("finished in task()")

task()

# 실행결과
start in task()
start in a()
finished in a()
start in b()
finished in b()
finished in task()
```

##### 비동기 / 논블락

```python
# async / non-block
import asyncio

async def a():
    print("start in a()")
    await asyncio.sleep(2)
    print("finished in a()")

async def b():
    print("start in b()")
    await asyncio.sleep(2)
    print("finished in b()")

async def task():
    print("start in task()")
    asyncio.create_task(a())
    asyncio.create_task(b())
    print("finished in task()")
    await asyncio.sleep(3)

async def main():
    await task()

asyncio.run(main())

# 실행결과
start in task()
finished in task()
start in a()
start in b()
finished in a()
finished in b()
```

##### 동기 / 논블락

흔한 경우는 아니지만 종종 쓰임

```python
# sync / non-block
import asyncio

global a_task_success
a_task_success = False

async def a():
    print("doing ... in a()")
    await asyncio.sleep(3)
    print("finished a")
    global a_task_success
    a_task_success = True

async def task():
    print("doing task ...")
    asyncio.create_task(a())
    print("doing something ...")
    global a_task_success
    while a_task_success is False:
        print("waiting a to be finished ...")
        await asyncio.sleep(1)

    print("finished task")

asyncio.run(task())

# 실행결과
doing task ...
doing something ...
waiting a to be finished ...
doing ... in a()
waiting a to be finished ...
waiting a to be finished ...
finished a
finished task
```

##### 비동기/블락

```python
# async / block
import asyncio

async def a():
    print("start in a()")
    await asyncio.sleep(3)
    print("finished in a()")

async def task():
    print("doing task ...")
    value = await a()
    print("doing something ... in task()")
    print("finished in task()")

asyncio.run(task())

# 실행 결과
doing task ...
start in a()
finished in a()
doing something ... in task()
finished in task()
```



## 병렬성과 동시성

CPU가 쉴 틈 없이 한 번에 주어진 테스크들을 빠르게 처리되다 보니 컴퓨터 사용자는 사실상 모든 프로세스의 명령이 "동시에" 처리된다고 느끼게 됨, 이것을 동시성(Concurrency)이라고 함

- 동시성이라는 개념은 물리적으로 CPU 1개의 코어에서만 동작하는 개념은 아님

- 제한된 자원에서 여러 작업을 한번에 실행시키려는 논리적인 개념

### 동시성(Concurrency)

![img](https://velog.velcdn.com/images%2Fcha-suyeon%2Fpost%2Fe13b6da0-c211-44d6-a8bf-a7dee3d539b3%2Fimage.png)

### 병렬성(Parallelism)

![img](https://velog.velcdn.com/images%2Fcha-suyeon%2Fpost%2Fd7ddc0d2-23b6-41fe-b406-3c1284634e22%2Fimage.png)

- **동시성**은 실제로는 하나의 명령을 빠르게 수행하지만 처리속도가 매우 빨라 여러 작업이 동시에 진행되는 것처럼 "느껴지게" 해줌

- **병렬성**은 "실제로" 여러 개의 명령어를 동시에 실행하는 것

### 병렬 동시 실행

![멀티태스킹(1) - 동시성, 병렬성(Concurrency, Parallelism)](https://velog.velcdn.com/images%2Fcha-suyeon%2Fpost%2F16ef40eb-e9b8-45cb-9a7e-00a08d946907%2Fimage.png)



## 아키텍처란

소프트웨어의 전체적인 구조를 잡아주는 설계도

예) 레이어드 아키텍처, MVC 패턴 등

##### 아키텍처가 있다면

- 소프트웨어의 큰 그림을 이해하기 쉬움
  - 코드를 다 확인하지 않아도, 일관된 코드 구조로 흐름을 쉽게 유추할 수 있음
    - 개발의 생산성 향상
    - 개발자의 커뮤니케이션 비용을 줄일 수 있음
- 트레이드 오프
  - 코드를 작성하거나 읽어야 할 때 알아야 될 규칙이 늘어남
    - 팀원 전체가 프로젝트의 아키텍처 패턴에 익숙해야 함
    - 전반적으로 작성해야하는 코드의 양이 늘어남

##### 아키텍처가 없다면

- 코드를 어디에 두어야 할지 모름
  - 또한 내가 생각한 기준과 팀원이 생각한 기준이 다를 수 있기에 output 이 일관성이 없음
- 팀에 새로운 사람이 들어와 프로젝트 코드를 보면, 어디서부터 어떻게 봐야 할지 감이 오지 않음

##### 아키텍처를 처음에 쉽게 이해하려면

- **의존성** 관계에 집중
  - 컴포넌트별로 역할을 명확하게 나누고 의존 관계를 분명히 하는 것이 핵심

- 본인이 자주 사용하는 언어 프레임워크와 아키텍처 패턴을 구글링해서 다른 사람들은 어떻게 구현하는지 알아보고 적용

**아키텍처에 정답은 없음**

- 아무리 유명한 아키텍처라고 하더라도, 당장 상황에 맞지 않은 아키텍처는 좋은 아키텍처가 아님
- 완벽한 아키텍처는 존재하지 않음

- 상황에 따라 레이어 개수나 레이어별 의미가 달라질 수 있음
- 중요한 것은 레이어를 잘 나눌 수 있도록 경계를 설정할 수 있어야 함
  - 개발자가 어떤 모듈을 어디에 두어야 할지에 대한 고민을 줄여줘야 함
- 반드시 의존 흐름을 바깥에서 안쪽으로 가져가야 함

### 레이어드 아키텍처(Layered architecture)

- 레이어를 분리하여 레이어 마다 해야 할 역할을 정의해놓은 구조(**의존성 방향**)

![Layered Architecture](https://images.velog.io/images/hanblueblue/post/c2555af2-4fc5-4e64-9a3a-2314b2239941/layered-architecture-overview.png)

- Presentation Layer
  - 인터페이스와 애플리케이션이 연결되는 곳 (예: controller, router)
- Application Layer
  - 소프트웨어가 제공하는 주요 기능을 구현하는 코드가 모이는 곳
- Domain Layer
  - 도메인 모델, 도메인 서비스 등 도메인 문제를 코드로 풀어내는 일을 담당
- Infrastructure Layer
  - DB와의 연결, ORM 객체, 메시지 큐 등 애플리케이션 외적인 인프라들과의 어댑터 역할

##### 장점

- 정해진 역할이 분명하여 유지보수 및 코드 관리가 용이
  - 의존성 방향이 동일하여 테스트 코드 짜기도 수월
  - 새로운 기능을 개발할 때 통일된 흐름에 맞게 빠르게 개발 가능

##### 단점

- 소프트웨어가 최종적으로 Infrastructere Layer 에 의존성을 갖음
  - 소프트웨어에서 가장 중요한 부분은 비지니스 로직을 처리하는 ""Application Layer"와 "Domain Layer"
  - Domain Layer 가 Infrastructure Layer, 특히 DB에 의존하게 됨
  - **소프트웨어의 모든 구조가 DB 중심의 설계가 됨**
    - **소프트웨어는 그저 DB에 데이터를 옮겨주는 역할이 됨**
  - **애플리케이션 설계에 앞서 데이터베이스를 먼저 설계하게 됨**
  - 객체지향에서 추구하는 "Action" 중심이 아니라 "State" 중심으로 설계하게 됨
    - 점점 객체지향에서 벗어나는 코드를 작성하게 됨

##### 소프트웨어 아키텍처의 지향점

- 소프트웨어의 중심은 DB와 같은 외부 시스템이 아니라 애플리케이션이 중심

- 애플리케이션을 중심으로 보고 DB, 웹 프레임워크 등은 모두 애플리케이션이 사용하는 부품으로 봐야함

### 헥사고날 아키텍처(Hexagonal architecture)

애플리키에션과 바깥 모듈을 자유롭게 탈부착

- 예: 게임기 본체 (엑스박스, 플레이스테이션, 닌텐도) 와 주변기기 (입력패드, 모니터, 주변기기)
  - 핵심은 게임기 본체 => 애플리케이션
  - 주변기기는 취향에 따라 변경 => 바깥 모듈
  - **포트 앤 어댑터 패턴**(Ports and Adapters)

**의존성 흐름**

**Adapter -> Application -> Domain**

![헥사고날(Hexagonal) 아키텍처 in 메쉬코리아:: MESH KOREA | VROONG 테크 블로그](https://mesh.dev/static/5218ecc01e2106b794a5831fda5baca3/2b72d/03.png)

- Domain
  - 소프트웨어의 핵심이 되는 도메인
  - Layered architecture의 Domain Layer와 같음
- Application
  - Domain을 이용하여 비지니스를 제공
  - 관용적으로 Service 라고 표현
  - 포트를 가지고 있음
    - 포트는 외부의 어댑터를 끼울 수 있는 인터페이스
- Adapter
  - Application 의 포트 규격에 맞게 구현한 구현체
    - Application 의 포트를 나타내는 인터페이스를 상속받아 구현
  - DB는 Out bound adapter
  - Browser, App 은 In bound adapter

##### 코드 변화

AS-IS

```java
public class AdminService {
  private final AdminRepository adminRepository;

  public AdminDTO remove(final Long id) {
    return this.adminRepository
        .findById(id)
        .map(admin -> new AdminDTO(admin.remove()))
        .orElseThrow(() -> new BusinessException(ExceptionCode.FAIL_NO_DATA_SUCCESS));
  }
}
```

TO-BE

```java
public class AdminService {
  public AdminDTO remove(final Long id, final AdminRepository adminRepository) {
    return adminRepository
        .findById(id)
        .map(admin -> new AdminDTO(admin.remove()))
        .orElseThrow(() -> new BusinessException(ExceptionCode.FAIL_NO_DATA_SUCCESS));
  }
}
```

##### 트레이드오프

어댑터, 포트 등 알아야 할 개념과 보일러 플레이트 코드가 늘어날 수 있음

### 클린 아키텍처

당연한 아키텍처 설계

- 세부 사항(DB, 프레임워크)가 정책(업무 규칙)에 영향을 주면 안 됨
- 계층별로 관심사를 명확하게 분리하여 변경이 필요할 때 영향을 주는 부분을 최소화
- 내부 계층이 외부 계층을 의존하지 않아야 함
- **의존 방향 규칙** (저수준 -> 고수준)

![Clean Architecture는 모바일 개발을 어떻게 도와주는가? - (1) 경계선: 계층 나누기 | by Saryong Kang  | Medium](https://miro.medium.com/max/1400/1*_HxTmyiDQNCfACBZle67rw.jpeg)

### 모놀리스 아키텍처

일반적이고 우리 상식에 들어맞는 아키텍처 패턴

##### 장점

- 디버깅이나 비교적 장애 대응이 비교적 쉬움

##### 단점

- 최신 기술 스택이 나와도 쉽게 도입하기 어려움

### 마이크로서비스 아키텍처

소프트웨어를 구성하는 각각의 컴포넌트들을 독립적인 프로젝트들로 분리하는 것

일반적으로 서비스가 성장하고 프로젝트 규모가 커질 때 모놀리식에서 마이크로서비스로 전환함

![img](https://www.redhat.com/cms/managed-files/microservices-1680.png)

![마이크로 서비스 아키텍처 & 모놀리틱 아키텍처](https://images.velog.io/images/dsunni/post/b61ec810-1347-4022-91e6-2b8c139d3d06/assets_-LE8_fwLnI2gUuguYTDU_-LH6l3KoYH7hthyWqBvj_-LH6l4H0EWQvNC1mq4_P_monolithic-vs-microservice.png)

- 서로의 데이터처리가 필요한 경우 서로간의 HTTP 통신

##### 장점

- 서비스 개발과 배포를 빠르게 진행
- 마이크로서비스 마다 프로그래밍 언어나 데이터베이스에 대한 기술 스택을 더 자유롭게 가져갈 수 있음
- 하나의 마이크로서비스에서 장애가 발생하더라도 다른 마이크로서비스는 정상 동작 가능

##### 단점

- 테스트 난이도가 굉장히 높음
- DDD, EDA 의 개념을 이해하는 난이도가 굉장히 높음
- 처음에 어떤 기준으로 마이크로서비스를 나눌지 결정이 어려움

## 프로그래밍 패러다임

C언어 - 절차지향 언어

Java - 객체지향 언어

Python - 두 패러다임을 모두 수용하는 멀티 패러다임 언어

No silver bullet... 비판적인 관점에서 프로그래밍 패러다임을 바라보는 것이 중요

### 절차지향 프로그래밍

**함수적 호출 과정**을 중심으로 바라보고 설계

![Programming Paradigm](https://images.velog.io/images/roo333/post/addd7947-5980-4fe5-a135-3ed4e6c317d6/15084493877125_C03p01ch03-procedural-vs-oop.png)

**데이터가 중앙 집중식 관리**

**직관적** (쉬움)

우리가 프로그래밍을 처음 배우면 보통 절차지향으로 배우는 이유

### 객체지향 프로그래밍

객체라고 하는 단위에 책임을 명확히 하고 서로 협력하도록 프로그래밍 하는 패러다임

**모든 것을 객체로 나누어 생각**, 필요할때 객체들을 활용하여 서로 협력하여 일을 수행

상태를 가지고 있기 때문에 함수에 같은 입력을 넣었더라도 언제나 같은 출력이 보장되지 않음

#### SOLID

- Single Responsibility Principle(단일 책임 원칙)

객체는 하나의 책임만을 지녀야 한다는 원칙

as-is

```python
# 하나의 클래스(객체)가 여러 책임을 가지고 있음
class Employee:
    def coding(self):
        print("코딩을 합니다.")

    def design(self):
        print("디자인을 합니다")

    def analyze(self):
        print("분석을 합니다.")
```

to-be

```python
# 각 객체는 역할을 나눠서 가지고 있음
class Developer:
    def coding(self):
        print("코딩을 합니다.")

class Designer:
    def design(self):
        print("디자인을 합니다")

class Analyst:
    def analyze(self):
        print("분석을 합니다.")
```

- Open Closed(개방 폐쇄 원칙)

객체의 확장에는 열려있고, 수정에는 닫혀있게 해야한다는 원칙

as-is

```python
class Developer:
    def coding(self):
        print("코딩을 합니다.")

class Designer:
    def design(self):
        print("디자인을 합니다")

class Analyst:
    def analyze(self):
        print("분석을 합니다.")

class Company:
    def __init__(self, employees):
        self.employees = employees

    # employee 가 다양해 질수록 코드를 계속 변경해야 한다.
    def make_work(self):
        for employee in self.employees:
            if type(employee) == Developer:
                employee.coding()
            elif type(employee) == Designer:
                employee.design()
            elif type(employee) == Analyst:
                employee.analyze()

```

to-be

```python
# 각 객체들의 역할을 아우르는 추상 클래스(고수준)을 생성합니다.
import abc
from typing import List

class Employee(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def work(self):
        ...

class Developer(Employee):
    def work(self):
        print("코딩을 합니다.")

class Designer(Employee):
    def work(self):
        print("디자인을 합니다")

class Analyst(Employee):
    def work(self):
        print("분석을 합니다.")

# 상속을 통해 쉽게 구현이 가능함 -> 확장이 열려있다.
class Manager(Employee):
    def work(self):
        print("매니징을 합니다.")

class Company:
    def __init__(self, employees: List[Employee]):
        self.employees = employees

    # employee 가 늘어나더라도 변경에는 닫혀있다.
    def make_work(self):
        for employee in self.employees:
            employee.work()
```

- Liskov Substitution Priciple(리스코브 치환 원칙)

부모 객체의 역할은 자식 객체도 할 수 있어야 된다는 원칙

 ```python
# 위반 사례1
class Employee(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def work(self):
        ...

class Developer(Employee):
    def work(self):
        print("코딩을 합니다.")
        return ["if...", "for..."]

class FrontEndDeveloper(Developer):
    def work(self):
        print("프론트엔드 개발을 합니다")
        # 결과를 반환하지 않음

if __name__ == "__main__":

    def make_code(developer: Developer):
        code = developer.work()
        print(f"총 {len(code)}줄의 코드를 작성하였습니다.")

    make_code(Developer())
    make_code(FrontEndDeveloper())
 ```

자식 객체가 부모 객체를 상속해야 하는지를 반드시 확인

```python
# 위반 사례2
# 유명한 직사각형, 정사각형 사례
# 일반적으로 정사각형은 직사각형입니다. 즉 정사각형 is 직사각형의 관계이며, 이는 상속이 가능합니다.
# 직사각형
class Rectangle:
    def get_width(self):
        return self.width

    def get_height(self):
        return self.height

    def set_width(self, width):
        self.width = width

    def set_height(self, height):
        self.height = height

# 정사각형
class Square(Rectangle):
    def set_width(self, width):
        self.width = width
        self.height = width

    def set_height(self, height):
        self.width = height
        self.height = height

if __name__ == "__main__":
    square = Square()
    square.set_width(20)
    square.set_height(30)
    check = square.get_width() == 20 and square.get_height() == 30
```

- Interface Segregation (인터페이스 분리 원칙)

클라이언트가 자신이 이용하지 않는 메서드는 의존하지 않아야 한다는 원칙

인테페이스가 하나의 책임만을 가져야 함

as-is

```python
import abc

class Smartphone(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def call(self):
        ...

    @abc.abstractmethod
    def send_message(self):
        ...

    @abc.abstractmethod
    def see_youtube(self):
        ...

    @abc.abstractmethod
    def take_picture(self):
        ...

# 카메라가 없는 클래스에서 take_picture는 불필요한 메서드가 된다.
class PhoneWithNoCamera(Smartphone):
    ...
```

to-be

```python
import abc

class Telephone(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def call(self):
        ...

    @abc.abstractmethod
    def send_message(self):
        ...

class Camera(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def take_picture(self):
        ...

class Application(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def see_youtube(self):
        ...

class PhoneWithNoCamera(Telephone, Application):
    ...
```

- Dependency Inversion(의존성 역전 원칙)

의존성을 항상 고수준으로 향하게 하여 예측할 수 없는 의존성의 변화를 줄이자는 원칙

일반적으로 의존성을 가지는 대상이 변경되면 의존하는 주체도 함께 변경됨
만약 자주 바뀌는 구현체(저수준)를 의존하게 된다면 코드의 변경이 잦을 것이며 버그와 사이드 이펙트가 날 확률이 높아짐
이때 코드가 덜 바뀌는 인터페이스나 추상 클래스(고수준)를 의존한다면 상태적으로 안정적인 코드를 작성할 수 있음

as-is

```python
class App:
    def __init__(self):
        self.inmemory_db = InMemoryDatabase()  # 구현체에 의존하고 있음

    def save_user(self, data):
        self.inmemory_db.store_data(data)

if __name__ == "__main__":
    app = App()
    app.save_user({"id": 1, "name": "grab"})
```

to-be

```python
class App:
    def __init__(self, database: Database):  # 고수준에 의존
        self.database = database

    def save_user(self, data):
        self.database.store_data(data)

if __name__ == "__main__":
    inmemory_db = InmemoryDatabase()
    app = App(inmemory_db)
    app.save_user({"id": 1, "name": "grab"})
```

의존성 주입을 해주기 위해선 결국 이를 사용하는 클라이언트에서 의존성들을 일일이 넣어줘야 함
만약 잘못 코드를 작성하면 의존성 관계가 복잡해질 수 있음
그래서 보통 의존성 주입을 별도로 관리해주는 라이브러리나 프레임워크를 사용



### 함수형 프로그래밍

**외부 상태를 갖지 않는 함수들의 연속**으로 프로그램을 하는 패러다임

외부 상태를 갖지 않는다는 의미는, **같은 입력을 넣었을 때 언제나 같은 출력**을 내보낸다는 것(부수 효과(side effect)가 전혀 없다는 것)

한번 초기화한 변수는 변하지 않음, 이런 특성을 **불변성**이라고 하는데, 이 **불변성**을 통해 안전성을 얻을 수 있음

```python
c = 1

def func(a: int, b: int) -> int:
  return a + b + c # c 라는 외부 값, 상태가 포함되어 있기에 함수형이 아님
```

함수형

```python
def func(a: int, b: int, c: int) -> int:
  return a + b + c # 주어진 파라미터만 사용, 별도의 상태가 없음
```

수학에서 `f(x) = y` 라는 함수가 있을 때 `f`에 `x`를 입력주면 항상 `y`라는 출력을 얻음, 이처럼 함수형 프로그래밍은 같은 입력을 주었을 때 항상 같은 값을 출력하도록 하는 것

함수형 프로그래밍의 중심인 함수는 입력(parameter)과 출력(result)이 될 수도 있기에, 함수를 결합하고 조합하기 쉬워져 간결한 코드 작성이 가능

## 클린코드

#### 함수

1. 함수의 역할은 하나만 할 수 있도록 하자(**SRP**: Single Responsibility Principle)

- as-is

```python
def create_user(email, password):
  if '@' not in email or len(password) < 6 :
    raise Exception("유저 정보를 제대로 입력하세요")
   	
  user = {"email": email, "password": password}
  
  database = Database("mysql")
  database.add(user)
  
  email_client = EmailClient()
  email_client.set_config(...)
  email_client.send(email, "회원가입을 축하합니다")
  
  return True
```

- to-be

```python
def create_user(email, password):
  validate_create_user(email, password)
  
  user = build_user(email, password)
  
  save_user(user)
  send_email(email)
  
  return True

def validate_create_user(email, password):
  if '@' not in email or len(password) < 6 :
    raise Exception("유저 정보를 제대로 입력하세요")

def build_user(email, password):
  return {
    "email": email, 
    "password": password
  }
  
def save_user(user):
  database = Database("mysql")
  database.add(user)
  
def send_email(email):
  email_client = EmailClient()
  email_client.set_config(...)
  email_client.send(email, "회원가입을 축하합니다")
```

2. 반복하지 말자(**DRY**: Do not Repeat Yourself)

- as-is

```python
def create_user(email, password):
  if '@' not in email or len(password) < 6 :
    raise Exception("유저 정보를 제대로 입력하세요")
  
  ...
  
def update_user(email, password):
  if '@' not in email or len(password) < 6 :
    raise Exception("유저 정보를 제대로 입력하세요")
    
  ...
```

- to-be

```python
def validate_create_user(email, password):
  if '@' not in email or len(password) < 6 :
    raise Exception("유저 정보를 제대로 입력하세요")
    
def create_user(email, password):
  validate_create_user(email, password)
  ...
  
def update_user(email, password):
  validate_create_user(email, password)
  ...
```

3. 파라미터 수는 적게 유지하자

```python
#as-is
def save_user(user_name, email, password, created):
  ...
  
#to-be
def save_user(user: User):
  ...
```

4. 사이드 이팩트를 잘 핸들링하자

```python
# 사이드 이팩트가 없음
def get_user_instance(email, password):
  user = User(email, password)
  return user

# 사이드 이팩트가 있음
def update_user_instance(user):
  user.email = "new email" # 인자로 받은 user 객체를 업데이트 함
  ...
  
 # 사이드 이팩트가 있음
def create_user(email, password):
  user = User(email, password)
  start_db_session() # 외부의 DB Session 변화를 줄 수 있음
  ...
```

##### 잘 핸들링 하는 방법

1. 코드를 통해 충분히 예측할 수 있도록 네이밍을 잘하는 것이 중요

- `update`, `change`,  `set` 같은 직관적인 prefix 를 붙여서 사이드 이펙트가 있음을 암시

2. 함수의 사이드 이팩트가 있는 부분과 없는 부분을 잘 나눠서 관리

- 명령(side effect 있음)과 조회(side effect 없음)를 분리하는 `CQRS` 방식을 사용

3. 일반적으로 update 를 남발하기 보단 **순수 함수 형태**로 사용하는 것이 더 직관적이고 에러를 방지 할 수 있음

- as-is

```python
carts = []

# 사이드 이팩트를 발생시킴
def add_cart(product):
  carts.append(product)
  
product = Product(...)
add_cart(product)
```

- to-be

```python
carts = []

# 사이드 이팩트가 없는 순수함수
def get_added_cart(product):
  return [...carts, product]
  
product = Product(...)
carts = get_added_cart(product)
```

# 자주 사용하는 용어

#### 트레이드오프

하나가 증가하면 다른 하나는 무조건 감소한다는 것

둘 중 하나를 반드시 선택해야 하는 관계

#### 보일러 플레이트  (코드)

최소한의 변경으로 여러곳에서 재사용되며, 반복적으로 비슷한 형태를 띄는 코드

#### 순수 함수

어떤 함수에 동일한 인자를 주었을 때 항상 같은 값을 리턴하는 함수  + 외부의 상태를 변경하지 않는 함수

#### No Silver Bullet

**만병 통치약**은 없다.

1986년 프레드릭 브룩스가 만든 소프트웨어 공학 논문에 나오는 내용

Silver Bullet은 원래 흡혈귀나 늑대인간 등의 전설에서 은으로 만든 탄환을 쏘아서 심장 부위를 맞추면 일거에 죽일 수 있다는 전설에서 유래된 말

silver bullet 이란 비장의 무기, 만반의 대책, 묘책 등의 의미
