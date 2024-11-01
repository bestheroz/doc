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

# 