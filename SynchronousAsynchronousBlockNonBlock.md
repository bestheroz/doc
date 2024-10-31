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

관계:
- 대부분의 비동기 작업은 논블럭킹 방식으로 구현됨
- 하지만 모든 논블럭킹 작업이 비동기인 것은 아님
- 둘 다 시스템의 효율적인 자원 활용을 위해 사용됨

정리하면:
- 비동기(Asynchronous): 작업의 완료를 기다리지 않고 다른 작업을 수행하며, 작업 완료 시 결과를 처리하는 방식.
  - 비동기는 "언제 끝날지 모르는 작업의 완료 시점과 결과 처리"에 초점
- 논블로킹(Non-Blocking): 작업이 즉시 완료되지 않아도 호출이 즉시 반환되어 다른 작업을 수행할 수 있는 방식.
  - 논블럭킹은 "작업 수행 중에도 다른 작업을 할 수 있는가"에 초점

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

