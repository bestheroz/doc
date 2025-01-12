# 코틀린 블록 함수 (Kotlin Block Functions)

코틀린에서 **블럭 함수**란 주로 람다식(코드 블럭)을 인자로 받아 실행하는 **고차 함수(higher-order function)** 를 의미합니다. 이러한 함수들은 코드의 재사용성을 높이고, 가독성을 개선하며, 도메인 특정 언어(DSL)를 구현하는 데에도 유용하게 사용됩니다.

### 1. 고차 함수와 람다식
고차 함수는 함수를 인자로 받거나, 함수를 반환하는 함수입니다. 코틀린에서는 람다식을 이용해 블럭 함수를 간편하게 사용할 수 있습니다.

예를 들어, 다음과 같이 람다식을 인자로 받는 함수를 정의할 수 있습니다:

```kotlin
fun doSomething(block: () -> Unit) {
    // 함수 내부에서 전달된 블럭 실행
    block()
}
```

이 함수를 호출할 때는 다음과 같이 람다 블럭을 넘겨줍니다:

```kotlin
doSomething {
    println("Hello, Kotlin!")
}
```

위 예제에서 중괄호 `{ ... }` 안에 있는 코드가 `block` 파라미터로 전달되어 `doSomething` 함수 내에서 실행됩니다.

### 2. 스코프 함수(scope functions)
코틀린 표준 라이브러리에는 블럭 함수를 활용하는 대표적인 함수들이 있습니다. 이들은 객체의 컨텍스트 내에서 특정 작업을 수행하고 결과를 반환하거나, 객체 자신을 반환하는 등 다양한 용도로 쓰입니다. 대표적인 스코프 함수는 다음과 같습니다:

- **let** (it 참조, 람다 결과 반환): 객체를 람다의 인자로 넘겨 처리하며, 람다의 마지막 식 결과를 반환합니다. null 체크, 지역 변수 제한, 체이닝 필요시 사용 합니다.
    ```kotlin
    // 1. Null 체크와 함께 사용할 때
    val email: String? = user.email
    email?.let { userEmail ->
        sendEmail(userEmail)
        updateLastEmailSent(userEmail)
    }
    
    // 2. 결과값을 변수의 스코프를 제한하여 사용할 때
    val length = str?.let { 
        println("처리 문자열: $it")
        it.length // 마지막 식이 반환값
    }
    
    // 3. 체이닝을 사용할 때
    findUser(userId)
        ?.let { user -> validateUser(user) }
        ?.let { validUser -> processUser(validUser) }
    ```

- **run** (this 참조, 람다 결과 반환): 람다식 내에서 여러 작업을 수행하고, 마지막 식의 결과를 반환합니다. 객체 컨텍스트 없이 사용할 수도 있고, 객체의 확장 함수 형태로도 사용됩니다. 객체 초기화와 계산 결과가 필요할 때 사용합니다.
    ```kotlin
    // 1. 객체 초기화와 계산을 동시에 할 때
    val result = user.run {
    name = "새이름"
    age = 25
    calculateInsurance() // 마지막 계산 결과 반환
    }
    
    // 2. 여러 작업을 그룹화하고 결과를 반환할 때
    val isValid = inputData.run {
    validate()
    transform()
    save() // 저장 성공 여부 반환
    }
    
    // 3. 객체 생성 없이 블록 실행이 필요할 때
    val result = run {
    val temp = complexCalculation()
    temp * 2
    }
    ```

- **with** (this 참조, 람다 결과 반환, 확장함수 아님): 수신 객체와 함께 블럭을 실행하고, 결과를 반환합니다. 반복적인 객체 참조가 필요할 때 사용합니다.
    ```kotlin
    // 1. 객체의 함수를 여러번 호출할 때
    with(user) {
        println("이름: $name")
        println("나이: $age")
        println("이메일: $email")
    }
    
    // 2. 객체 설정을 그룹화할 때
    with(retrofitBuilder) {
        baseUrl("https://api.example.com")
        client(okHttpClient)
        addConverterFactory(GsonConverterFactory.create())
    }
    
    // 3. 특정 객체를 반복적으로 사용할 때
    with(database.userDao()) {
        deleteAll()
        insert(newUsers)
        update(modifiedUsers)
    }
    ```

- **apply** (this 참조, 객체 자신 반환): 객체를 확장 함수의 수신 객체로 받아 블럭 내에서 초기화 작업을 수행한 후, 원본 객체 자체를 반환합니다. 객체 초기화와 자신을 반환해야 할 때 사용합니다.
    ```kotlin
    // 1. 객체 초기화에 사용
    val user = User().apply {
        name = "김철수"
        age = 30
        email = "test@test.com"
    }
    
    // 2. 빌더 패턴 구현
    data class RequestBuilder(
        var url: String = "",
        var method: String = "GET",
        var headers: MutableMap<String, String> = mutableMapOf()
    ) {
        fun build() = Request().apply {
            this.url = this@RequestBuilder.url
            this.method = this@RequestBuilder.method
            this.headers.putAll(this@RequestBuilder.headers)
        }
    }
    
    // 3. 테스트 데이터 생성
    fun createTestUser() = User().apply {
        name = "테스트유저"
        email = "test@test.com"
        role = Role.USER
    }
    ```

- **also** (it 참조, 객체 자신 반환): 객체를 인자로 받아 추가 작업을 수행하고, 원본 객체를 그대로 반환합니다. 주로 부수 효과(side effect)를 위해 사용됩니다. 부수 작업, 로깅, 유효성 검사 등이 필요할 때 사용합니다.
    ```kotlin
    // 1. 부수 작업이나 로깅
    val user = User().apply {
        name = "김철수"
        age = 30
    }.also { user ->
        logger.info("사용자 생성됨: ${user.name}")
    }
    
    // 2. 유효성 검사
    fun saveUser(user: User) = user.also {
        require(it.name.isNotBlank()) { "이름은 필수입니다" }
        require(it.age >= 0) { "나이는 0보다 작을 수 없습니다" }
    }
    
    // 3. 디버깅이나 로깅 체인
    data class Transaction(val id: String, var amount: Int)
    
    fun processTransaction(transaction: Transaction) = transaction
        .also { log.debug("처리 전 트랜잭션: $it") }
        .apply { amount *= 2 }
        .also { log.debug("처리 후 트랜잭션: $it") }
    ```


### 3. 함수 리터럴과 리시버(Function Literals with Receiver)
코틀린에서는 함수 리터럴에 수신 객체(receiver)를 지정할 수 있는데, 이를 통해 DSL을 구현하거나 더 직관적인 코드를 작성할 수 있습니다.

```kotlin
fun buildString(block: StringBuilder.() -> Unit): String {
    val sb = StringBuilder()
    sb.block()  // 여기서 block은 StringBuilder의 컨텍스트 내에서 실행됨
    return sb.toString()
}

val result = buildString {
    append("Hello, ")
    append("Kotlin!")
}
println(result)  // "Hello, Kotlin!" 출력
```

위 예제에서 `block: StringBuilder.() -> Unit`는 수신 객체 타입이 `StringBuilder`인 람다를 받는다는 의미입니다. 따라서 람다 내부에서는 `this`가 `StringBuilder` 객체를 가리키며, 바로 `append` 메서드를 호출할 수 있습니다.

### 결론
코틀린의 블럭 함수는 람다식을 활용해 코드를 모듈화하고, 객체를 다루는 로직을 간결하게 만드는 강력한 도구입니다. 이를 활용하면 가독성 높은 DSL을 구현하거나, 객체 초기화, 자원 관리 등의 다양한 패턴을 쉽게 적용할 수 있습니다.