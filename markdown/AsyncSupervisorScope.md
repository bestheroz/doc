# async와 supervisorScope

### async 예외 전파 특성

```kotlin
// ⚠️ async는 예외가 await() 시점에 전파됨
val deferred = async {
    throw RuntimeException("API 호출 실패")  // 여기서 발생
}

// ... 다른 작업 수행 ...

val result = deferred.await()  // ← 여기서 예외가 터짐!
```

### 문제 상황: 하나가 실패하면 전체 취소

```kotlin
// ❌ 문제: 하나의 실패가 다른 작업도 취소시킴
coroutineScope {
    val api1 = async { callApi1() }  // 성공
    val api2 = async { callApi2() }  // 실패!
    val api3 = async { callApi3() }  // api2 실패로 취소됨
    
    // api2 실패 시 → 전체 coroutineScope 취소
    // api1, api3도 함께 취소됨
}
```

### 해결책 1: supervisorScope 사용

```kotlin
// ✅ supervisorScope: 자식의 실패가 다른 자식에게 전파되지 않음
supervisorScope {
    val api1 = async { callApi1() }  // 성공 → 정상 완료
    val api2 = async { callApi2() }  // 실패!
    val api3 = async { callApi3() }  // api2 실패와 무관하게 실행
    
    // 각각 독립적으로 처리
    val result1 = runCatching { api1.await() }.getOrNull()
    val result2 = runCatching { api2.await() }.getOrNull()  // null
    val result3 = runCatching { api3.await() }.getOrNull()
}
```

### 해결책 2: 개별 예외 처리

```kotlin
// ✅ 각 async 내부에서 예외 처리
coroutineScope {
    val api1 = async {
        runCatching { callApi1() }
            .getOrElse { ApiResponse.empty() }
    }
    
    val api2 = async {
        runCatching { callApi2() }
            .getOrElse { ApiResponse.empty() }
    }
    
    // 실패해도 기본값 반환, 전체 취소 없음
    val results = listOf(api1.await(), api2.await())
}
```

### 실무 패턴: 보험 코어 연동 예시

```kotlin
suspend fun getCustomerInfo(customerId: String): CustomerInfoResponse {
    return supervisorScope {
        // 3개의 코어 시스템 동시 호출
        val contractDeferred = async(Dispatchers.IO) { 
            coreApi.getContracts(customerId) 
        }
        val paymentDeferred = async(Dispatchers.IO) { 
            coreApi.getPayments(customerId) 
        }
        val claimDeferred = async(Dispatchers.IO) { 
            coreApi.getClaims(customerId) 
        }
        
        // 개별 실패 허용, 성공한 것만 조합
        CustomerInfoResponse(
            contracts = runCatching { contractDeferred.await() }
                .getOrElse { emptyList() },
            payments = runCatching { paymentDeferred.await() }
                .getOrElse { emptyList() },
            claims = runCatching { claimDeferred.await() }
                .getOrElse { emptyList() }
        )
    }
}
```

### 면접 답변 예시

> "async 사용 시 예외는 await() 호출 시점에 전파됩니다. 기본 coroutineScope에서는 하나의 자식 코루틴이 실패하면 다른 자식들도 함께 취소되는데, 저희 시스템에서는 여러 코어 API를 동시 호출하면서 일부 실패해도 나머지는 정상 처리해야 했기 때문에 supervisorScope를 사용했습니다. 그리고 각 await 시점에서 runCatching으로 감싸서 부분 실패를 허용하도록 구현했습니다."

---

## 요약 비교표

| 구분 | coroutineScope | supervisorScope |
|------|----------------|-----------------|
| 자식 실패 시 | 다른 자식도 취소 | 다른 자식 영향 없음 |
| 사용 케이스 | 전부 성공해야 의미 있는 작업 | 부분 실패 허용 가능한 작업 |
| 예시 | 트랜잭션 처리 | 여러 API 병렬 호출 |
