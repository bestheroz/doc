# 개념 정리장

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

![image-20220628211720362](/Users/bestheroz/Library/Application Support/typora-user-images/image-20220628211720362.png)

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

### 함수형 프로그래밍

**외부 상태를 갖지 않는 함수들의 연속**으로 프로그램을 하는 패러다임

외부 상태를 갖지 않는다는 의미는, **같은 입력을 넣었을 때 언제나 같은 출력**을 내보낸다는 것

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



