# 코틀린+코루틴에서의 컨텍스트 오염 방지

Kotlin + Coroutine 환경에서 주로 사용한 Thread Local 에 대해 멀티 스레드환경에서 안전한지, 코루틴환경에서 안전한지 검증해보았습니다.

```Kotlin
class RequestContextElement(
    private val attributes: RequestAttributes
) : AbstractCoroutineContextElement(Key),
    ThreadContextElement<RequestAttributes?> {
    companion object Key : CoroutineContext.Key<RequestContextElement>

    override fun updateThreadContext(context: CoroutineContext): RequestAttributes? {
        val oldState = RequestContextHolder.getRequestAttributes()
        RequestContextHolder.setRequestAttributes(attributes)
        return oldState
    }

    override fun restoreThreadContext(
        context: CoroutineContext,
        oldState: RequestAttributes?
    ) {
        RequestContextHolder.setRequestAttributes(oldState)
    }
}

fun requestContextElement(): RequestContextElement {
    val attributes = RequestContextHolder.currentRequestAttributes()
    return RequestContextElement(attributes)
}

class SecurityContextElement(
    private val securityContext: SecurityContext
) : AbstractCoroutineContextElement(Key),
    ThreadContextElement<SecurityContext?> {
    companion object Key : CoroutineContext.Key<SecurityContextElement>

    override fun updateThreadContext(context: CoroutineContext): SecurityContext? {
        val oldContext = SecurityContextHolder.getContext()
        SecurityContextHolder.setContext(securityContext)
        return oldContext
    }

    override fun restoreThreadContext(context: CoroutineContext, oldState: SecurityContext?) {
        SecurityContextHolder.setContext(oldState)
    }
}

fun securityContextElement(): SecurityContextElement {
    val securityContext = SecurityContextHolder.getContext()
    return SecurityContextElement(securityContext)
}
```

## 멀티 스레드 환경에서 안전한가?

-> 결론: **안전하다.**

## 기본 설명

1. ThreadContextElement의 역할
- 코루틴이 다른 스레드로 전환될 때마다 ThreadLocal 값을 자동으로 전파해줍니다
- updateThreadContext(): 새로운 스레드로 전환 시 ThreadLocal 값을 설정
- restoreThreadContext(): 실행 완료 후 이전 ThreadLocal 값을 복원
2. 안전성 보장 메커니즘
```Kotlin
runBlocking(requestContextElement() + securityContextElement()) {
    // 이 블록 내에서는
    // 1. RequestContextHolder의 값
    // 2. SecurityContextHolder의 값
    // 이 항상 올바르게 유지됩니다
}
```
- 코루틴이 시작될 때 현재 스레드의 컨텍스트 값을 캡처
- 다른 스레드로 전환되어도 캡처된 컨텍스트 값이 새 스레드에 자동 설정
- 코루틴 실행이 완료되면 원래 값으로 자동 복원
3. 실제 동작 과정
```Kotlin
// 1. 코루틴 시작 시
val originalRequestContext = RequestContextHolder.getRequestAttributes()
val originalSecurityContext = SecurityContextHolder.getContext()

// 2. 새로운 스레드로 전환 시 자동으로 수행
RequestContextHolder.setRequestAttributes(capturedRequestContext)
SecurityContextHolder.setContext(capturedSecurityContext)

// 3. 작업 완료 후 자동으로 수행
RequestContextHolder.setRequestAttributes(originalRequestContext)
SecurityContextHolder.setContext(originalSecurityContext)
```

### 상세 설명

1. 구조 분석
```Kotlin
class RequestContextElement(
    // attributes는 불변(immutable) 객체로 생성 시점에 캡처됩니다
    private val attributes: RequestAttributes 
)
```

2. 스레드 전환 시 동작 과정
```Kotlin
override fun updateThreadContext(context: CoroutineContext): RequestAttributes? {
    // 1. 현재 스레드의 상태를 먼저 저장
    val oldState = RequestContextHolder.getRequestAttributes()
    
    // 2. 새로운 스레드에 우리가 가지고 있던 attributes를 설정
    RequestContextHolder.setRequestAttributes(attributes)
    
    // 3. 이전 상태를 반환하여 나중에 복원할 수 있게 함
    return oldState
}
```

3. 작업 완료 후 복원 과정
```Kotlin
override fun restoreThreadContext(
    context: CoroutineContext,
    oldState: RequestAttributes?
) {
    // 이전 상태로 복원
    RequestContextHolder.setRequestAttributes(oldState)
}
```

4. 안전성이 보장되는 이유:

    1. 동시성 제어
        - 각 스레드는 독립적인 ThreadLocal 저장소를 가짐
        - ThreadLocal 접근은 스레드 안전성이 보장됨
        - setRequestAttributes()와 getRequestAttributes() 메서드는 ThreadLocal 연산이므로 원자적(atomic)으로 동작
    2. 상태 격리
        ```Kotlin
        // 스레드 A
        val oldStateA = RequestContextHolder.getRequestAttributes() // 스레드 A의 상태
        RequestContextHolder.setRequestAttributes(attributes)       // 스레드 A에만 영향
        
        // 동시에 스레드 B
        val oldStateB = RequestContextHolder.getRequestAttributes() // 스레드 B의 상태
        RequestContextHolder.setRequestAttributes(attributes)       // 스레드 B에만 영향
        ```
     3. 생명주기 관리
        ```Kotlin
        // 1. 코루틴 시작
        val element = RequestContextElement(originalAttributes)
        
        // 2. 스레드 전환 발생
        oldState = element.updateThreadContext(context) // 현재 상태 저장 & 새 상태 설정
        
        // 3. 작업 수행
        // ... 비즈니스 로직 ...
        
        // 4. 작업 완료
        element.restoreThreadContext(context, oldState) // 원래 상태로 복원
        ```

5. 실제 사용 예시에서의 안전성:
```Kotlin
runBlocking(RequestContextElement(currentAttributes)) {
    // 스레드 1
    launch {
        // RequestAttributes는 스레드 1에 올바르게 설정됨
        println(RequestContextHolder.getRequestAttributes())
    }
    
    // 스레드 2
    launch {
        // RequestAttributes는 스레드 2에도 올바르게 설정됨
        println(RequestContextHolder.getRequestAttributes())
    }
}
// 모든 작업이 완료되면 원래 상태로 자동 복원
```

6. 안전성을 보장하는 핵심 요소:
   - ThreadLocal의 스레드 격리성
   - 상태 저장 및 복원의 원자성
   - 코루틴 컨텍스트를 통한 체계적인 생명주기 관리
   - 불변 객체를 통한 상태 관리

**이러한 메커니즘들이 조합되어 멀티 스레드 환경에서도 각 스레드의 RequestAttributes가 독립적이고 안전하게 관리됨**

## 하나의 스레드에서 여러 요청의 코루틴이 동시에 처리될때 안전한가?

-> 결론: **컨텍스트가 오염되는 이슈가 발생함**

```Kotlin
// 스레드 1에서 실행
val dispatcher = Dispatchers.Default // 혹은 Custom Dispatcher

// 요청 1
launch(dispatcher) {
    runBlocking(RequestContextElement(request1Attributes)) {
        // 여기서 스레드 1의 ThreadLocal 값이 request1Attributes로 설정됨
        delay(100) // 다른 작업으로 전환될 수 있는 지점
        
        // 다시 돌아왔을 때 이 스레드의 ThreadLocal 값이
        // 다른 요청에 의해 변경되었을 수 있음!
        val attributes = RequestContextHolder.getRequestAttributes() 
        // attributes가 request1Attributes가 아닐 수 있음!
    }
}

// 요청 2 (동시에 실행)
launch(dispatcher) {
    runBlocking(RequestContextElement(request2Attributes)) {
        // 같은 스레드 1을 사용하게 되면
        // ThreadLocal 값이 request2Attributes로 덮어씌워짐
        val attributes = RequestContextHolder.getRequestAttributes()
    }
}
```
문제점:
- 하나의 스레드가 여러 코루틴을 처리할 때 ThreadLocal 값이 덮어씌워질 수 있습니다
- 코루틴이 중단되었다가 재개될 때 ThreadLocal 값의 일관성이 깨질 수 있습니다
- 동일 스레드에서 실행되는 다른 코루틴의 ThreadLocal 값에 영향을 줄 수 있습니다

해결 방안:
1. 코루틴 단위로 컨텍스트를 격리
```Kotlin
// 각 요청마다 새로운 코루틴 컨텍스트를 생성
class IsolatedRequestContext(
    private val attributes: RequestAttributes,
    private val coroutineContext: CoroutineContext
) {
    suspend fun <T> run(block: suspend () -> T): T {
        return withContext(coroutineContext) {
            withContext(RequestContextElement(attributes)) {
                block()
            }
        }
    }
}

// 사용 예시
val isolatedContext = IsolatedRequestContext(
    attributes = requestAttributes,
    coroutineContext = Dispatchers.Default
)

isolatedContext.run {
    // 이 블록 내에서는 RequestAttributes가 안전하게 유지됨
    val attributes = RequestContextHolder.getRequestAttributes()
}
```
설명:
- IsolatedRequestContext 클래스를 통해 각 코루틴마다 별도의 컨텍스트를 생성하고 격리합니다.
- withContext를 중첩하여 coroutineContext와 RequestContextElement를 설정합니다.
- 이를 통해 코루틴 내에서 RequestAttributes를 안전하게 사용할 수 있습니다.

장점:
- 코루틴마다 컨텍스트가 격리되어 상태 오염을 방지합니다.
- 기존 코드 구조를 크게 변경하지 않고 적용할 수 있습니다.
- 재사용 가능한 구조로 만들어 여러 곳에서 활용할 수 있습니다.

단점:
- withContext를 두 번 중첩하여 사용하므로 코드가 다소 복잡해질 수 있습니다.
- IsolatedRequestContext 클래스를 매번 생성해야 하므로 약간의 오버헤드가 발생할 수 있습니다

2. 전용 Dispathcer 사용
```Kotlin
// 요청당 하나의 스레드를 사용하는 Dispatcher
val dedicatedDispatcher = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors())
    .asCoroutineDispatcher()

runBlocking(RequestContextElement(attributes) + dedicatedDispatcher) {
    // 이 블록은 전용 스레드에서 실행되어 다른 요청과 격리됨
} 
```
설명:
- Executors.newFixedThreadPool을 사용하여 고정된 스레드 풀을 생성하고, 이를 asCoroutineDispatcher()로 변환하여 전용 디스패처를 만듭니다.
- runBlocking이나 launch 등의 코루틴 빌더에서 이 디스패처를 사용하여 코루틴을 실행합니다.
- 각 코루틴이 고유한 스레드에서 실행되므로 스레드 로컬 변수의 충돌을 방지합니다.

장점:
- 스레드 로컬 변수를 그대로 사용할 수 있으므로 기존 코드를 크게 변경하지 않아도 됩니다.
- 코루틴 간의 스레드 로컬 변수 충돌을 완벽히 방지합니다.

단점:
- 스레드 수가 고정되어 있으므로 많은 요청이 들어올 경우 스레드 풀이 포화될 수 있습니다.
- 코루틴의 장점인 경량 쓰레딩을 활용하지 못하고, 스레드 기반으로 동작하므로 리소스 효율성이 떨어집니다.
- 스레드 관리와 관련된 추가적인 복잡성이 생길 수 있습니다.

3. 컨텍스트 전파 방식 변경
```Kotlin
class CoroutineSafeRequestContext {
    private val contextFlow = MutableStateFlow<RequestAttributes?>(null)
    
    suspend fun withRequestAttributes(attributes: RequestAttributes, block: suspend () -> Unit) {
        contextFlow.value = attributes
        try {
            block()
        } finally {
            contextFlow.value = null
        }
    }
    
    suspend fun getRequestAttributes(): RequestAttributes? {
        return contextFlow.value
    }
}
```

GPT o1 preview 의 추천 순서
1. **1번 방법 (코루틴 단위로 컨텍스트를 격리)**
- 코루틴 컨텍스트를 활용하여 상태를 안전하게 관리할 수 있습니다.
- 재사용성과 확장성이 높으며, 코루틴의 철학에 부합합니다.
- 코드의 복잡성을 줄이기 위해 유틸리티 함수를 활용하거나, 컨텍스트 요소를 간소화하는 방법을 고려할 수 있습니다.

2. 3번 방법 (컨텍스트 전파 방식 변경)
- 코루틴 친화적인 방식으로 상태를 관리하지만, 여러 코루틴 간의 상태 공유에 주의해야 합니다.
- 적절한 스코프 관리와 인스턴스 생성을 통해 문제를 완화할 수 있습니다.

3. 2번 방법 (전용 Dispatcher 사용)
- 기존 코드를 크게 변경하지 않고 적용할 수 있지만, 리소스 효율성이 떨어집니다.
- 고성능이 요구되는 환경에서는 부적합할 수 있습니다.

### 결론

최선의 해결책은 코루틴 컨텍스트를 활용하여 각 코루틴마다 독립된 상태를 유지하는 1번 방법입니다. 이는 코루틴의 특성을 잘 활용하면서도 스레드 안전성을 확보할 수 있는 방법입니다.

코드를 간소화하고 가독성을 높이기 위해 다음과 같이 헬퍼 함수를 사용할 수 있습니다:

```Kotlin
suspend fun <T> withRequestContext(
    attributes: RequestAttributes,
    block: suspend () -> T
): T {
    return withContext(RequestContextElement(attributes)) {
        block()
    }
}

// 사용 예시
withRequestContext(requestAttributes) {
    // 안전하게 RequestAttributes 사용
}
```
이를 통해 코드의 복잡성을 줄이고 유지 보수를 용이하게 할 수 있습니다.

코루틴의 특성을 고려하여 컨텍스트를 안전하게 전파하는 방법을 선택하는 것이 중요합니다.


## 최종 적용 코드
1. RequestContextElement와 SecurityContextElement 수정

먼저, 기존에 ThreadContextElement를 구현하여 스레드 로컬 변수를 관리하던 방식을 변경합니다. 대신, 코루틴 컨텍스트에 데이터를 저장하는 간단한 컨텍스트 요소로 변경합니다.

```Kotlin
// 컨텍스트 요소 정의
class RequestContextElement(
    val requestAttributes: RequestAttributes
) : CoroutineContext.Element {
    companion object Key : CoroutineContext.Key<RequestContextElement>
    override val key: CoroutineContext.Key<RequestContextElement> = Key
}

fun requestContextElement(): RequestContextElement = RequestContextElement(
    RequestContextHolder.getRequestAttributes()
        ?: throw IllegalStateException("RequestAttributes not found in context")
)

// 확장 함수로 컨텍스트 접근 제공
fun CoroutineContext.requestAttributes(): RequestAttributes? =
    this[RequestContextElement]?.requestAttributes

class SecurityContextElement(
    val securityContext: SecurityContext
) : CoroutineContext.Element {
    companion object Key : CoroutineContext.Key<SecurityContextElement>
    override val key: CoroutineContext.Key<SecurityContextElement> = Key
}

fun securityContextElement(): SecurityContextElement = SecurityContextElement(SecurityContextHolder.getContext())

fun CoroutineContext.securityContext(): SecurityContext? =
    this[SecurityContextElement]?.securityContext

```

2. 컨트롤러 수정

컨트롤러에서 runBlocking이나 launch를 사용할 때, 코루틴 컨텍스트에 RequestContextElement와 SecurityContextElement를 추가합니다.

```Kotlin
// Controller.kt
fun SomeMethod(): ResponseEntity<SomeMethodResponse> = runBlocking(requestContextElement() + securityContextElement()) {
    // 코루틴 컨텍스트에서 데이터 가져오기
    val currentAttributes = coroutineContext.requestAttributes()
        ?: throw IllegalStateException("RequestAttributes not found in coroutine context")
    val currentSecurityContext = coroutineContext.securityContext()
        ?: throw IllegalStateException("SecurityContext not found in coroutine context")
    // 필요한 로직 수행
}

```

3. 필요한 곳에서 코루틴 컨텍스트 사용

만약 다른 서비스나 컴포넌트에서 RequestAttributes나 SecurityContext가 필요하다면, 코루틴 컨텍스트에서 가져옵니다.

```Kotlin
// SomeService.kt
class SomeService {
    suspend fun performAction() {
        val attributes = coroutineContext.requestAttributes()
            ?: throw IllegalStateException("RequestAttributes not found in coroutine context")
        val securityContext = coroutineContext.securityContext()
            ?: throw IllegalStateException("SecurityContext not found in coroutine context")
        
        // 로직 수행
    }
}

```

4. `runBlocking` 중첩 호출
```Kotlin
runBlocking(requestContextElement() + securityContextElement()) {
    // 이 내부에서는 requestContextElement와 securityContextElement가 포함된 컨텍스트를 사용합니다.

    runBlocking {
        // 별도의 컨텍스트 요소를 지정하지 않았으므로, 상위의 컨텍스트를 상속받습니다.
        // 따라서 여기서도 requestContextElement와 securityContextElement를 사용할 수 있습니다.

        runBlocking {
            // 마찬가지로 상위 컨텍스트를 그대로 상속받습니다.
        }
    }
}
```
하지만, 만약 하위 코루틴에서 컨텍스트 요소를 재정의하면 상황이 달라집니다
```Kotlin
runBlocking(requestContextElement() + securityContextElement()) {
    runBlocking(requestContextElement()) {
        // 여기서는 securityContextElement가 상위에서 설정되었지만,
        // 하위에서 requestContextElement를 다시 지정했기 때문에
        // 상위의 requestContextElement를 덮어쓰게 됩니다.
        // securityContextElement는 상속되므로 그대로 사용할 수 있습니다.
    }
}
```




**이 구현의 장점:**

1. 각 코루틴이 독립된 컨텍스트를 가짐
2. 컨텍스트 전환이 안전하게 관리됨
3. 자원이 적절히 정리됨
4. 중첩된 코루틴에서도 안전하게 동작
5. 테스트하기 쉬움

주의사항:
1. 항상 `runBlocking(requestContextElement() + securityContextElement()) { }` 블록 내에서 실행해야 함
2. 컨텍스트가 필요한 모든 서비스는 코루틴 컨텍스트를 통해 접근해야 함
3. 예외 발생 시에도 컨텍스트가 원래대로 복원되도록 주의
