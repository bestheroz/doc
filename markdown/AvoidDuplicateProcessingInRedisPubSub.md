# Redis Pub/Sub 메시지 중복 처리를 방지하는 방법

Redis Pub/Sub은 메시지를 발행(publish)하면 모든 구독자(subscriber)에게 메시지가 전달되는 방식으로 동작합니다. 따라서 기본적으로 여러 구독자가 동일한 메시지를 받게 되어 중복 처리가 발생할 수 있습니다. 중복 처리를 예방하기 위한 몇 가지 방안을 소개하겠습니다:

### 1. Redis Streams와 Consumer Groups 사용하기
Redis Pub/Sub은 브로드캐스트 방식이기 때문에 메시지 처리를 단일 소비자에게 할당하는 데 한계가 있습니다. 대신, **Redis Streams**와 **Consumer Groups**을 사용하는 것이 효과적입니다. Redis Streams는 메시지를 로그 형태로 저장하며, Consumer Groups를 통해 여러 소비자가 메시지를 나누어 처리할 수 있습니다. 각 메시지는 한 번만 하나의 소비자에게 전달되므로 중복 처리를 방지할 수 있습니다.

**장점:**
- 메시지의 지속성 보장
- 메시지 재처리 가능
- 소비자 그룹을 통한 메시지 분배

**사용 예시:**
```bash
# 메시지 추가
XADD my_stream * field1 value1 field2 value2

# Consumer Group 생성
XGROUP CREATE my_stream my_group $ MKSTREAM

# 메시지 읽기
XREADGROUP GROUP my_group consumer1 COUNT 1 STREAMS my_stream >
```

### 2. 메시지 ID를 활용한 중복 처리 방지
Pub/Sub을 계속 사용해야 하는 경우, 각 메시지에 고유한 ID를 부여하고, 구독자가 메시지를 처리하기 전에 해당 ID가 이미 처리되었는지 확인하는 방법을 사용할 수 있습니다. 이를 위해 Redis의 **SET** 자료구조를 활용하여 처리된 메시지 ID를 저장하고, 새로운 메시지가 들어올 때마다 중복 여부를 체크합니다.

**단계:**
1. 메시지에 고유한 ID 추가
2. 구독자가 메시지를 받을 때, 해당 ID가 SET에 존재하는지 확인
3. 존재하지 않으면 메시지 처리 후 SET에 ID 추가
4. 일정 시간 후 SET에서 ID 삭제 (TTL 설정)

**예시 코드 (Python):**
```python
import redis
import uuid

r = redis.Redis()

def handle_message(message):
    message_id = message['id']
    if r.sadd('processed_ids', message_id):
        # 메시지 처리 로직
        print(f"Processing message: {message['data']}")
        # ID에 TTL 설정 (예: 1시간)
        r.expire('processed_ids', 3600)
    else:
        print(f"Duplicate message ignored: {message['data']}")

# 구독자 설정 및 메시지 처리
pubsub = r.pubsub()
pubsub.subscribe('my_channel')

for msg in pubsub.listen():
    if msg['type'] == 'message':
        handle_message({'id': msg['id'], 'data': msg['data']})
```

### 3. 분산 락(Distibuted Lock) 사용하기
여러 구독자가 동일한 메시지를 처리하려 할 때, **분산 락**을 사용하여 한 번에 하나의 구독자만 메시지를 처리하도록 할 수 있습니다. Redis의 `SETNX` 명령어나 Redlock 알고리즘을 활용하여 락을 구현할 수 있습니다.

**단계:**
1. 메시지 수신 시 고유한 락 키 생성
2. 락 획득 시에만 메시지 처리
3. 처리 완료 후 락 해제

**주의사항:**
- 락의 TTL(Time To Live)을 설정하여 데드락 방지
- 락 획득 실패 시 메시지 무시 또는 재시도 로직 구현

### 4. 메시지 브로커 전환 고려
Redis Pub/Sub이 아닌, **RabbitMQ**, **Kafka** 등의 전문 메시지 브로커를 사용하는 것도 중복 처리 방지를 위한 좋은 방법입니다. 이러한 시스템은 메시지의 지속성, 소비자 그룹 관리, 메시지 재처리 등의 기능을 기본적으로 제공하여 중복 처리 문제를 효과적으로 해결할 수 있습니다.

### 결론
Redis Pub/Sub은 간단하고 빠른 메시지 브로드캐스팅에 적합하지만, 중복 처리를 방지하는 데는 한계가 있습니다. 중복 처리가 중요한 경우 Redis Streams와 Consumer Groups를 사용하는 것이 권장되며, 필요에 따라 메시지 ID 관리나 분산 락 등의 추가적인 방법을 도입할 수 있습니다. 더 복잡한 메시징 요구사항이 있다면 전문 메시지 브로커로의 전환을 고려해보는 것도 좋은 선택입니다.