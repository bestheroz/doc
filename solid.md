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