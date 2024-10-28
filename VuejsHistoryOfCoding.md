# Vuejs

#### 코딩의 변화

1. Vue.js 2 + native javascript
2. Vue.js 2 + Typescript
3. Vue.js 2 + Typescript + decorator ( based on Class )
4. Vue.js 2 + Typescript + Composition API
5. Vue.js 3 + Typescript + Composition API
6. Vue.js 3 + Typescript + script setup ( based on Composition API )



#### 1. Vue.js 2 + native javascript

```html
<script>
export default {
  components: {
    HelloWorld
  },
  data() {
    listValue: []
  },
  mounted() {
    console.log("onMounted");
  },
  watch: {
    listValue: {
      handler(val, oldVal) {
        console.log('listValue changed')
      },
      immediate: true
    },
  },
  computed: {
    filterListValue: function () {
      return this.listValue.filter(v=>!!v);
    },
    envs: function() {
      return envs;
    },
  },
  methods: {
    method1() {
      this.$refs.refHelloWorld.methodA();
    },
  },
}
</script>
```

#### 2. Vue.js 2 + Typescript

```html
<script lang="ts">
export default {
  components: {
    HelloWorld
  },
  data: {
    listValue: [] as string[],
  },
  mounted() {
    console.log("onMounted");
  },
  watch: {
    listValue: {
      handler(val: string[], oldVal: string[] | undefined) {
        console.log('listValue changed')
      },
      immediate: true
    },
  },
  computed: {
    filterListValue: function (): string[] {
      return this.listValue.filter(v => !!v);
    },
    envs: function(): typeof envs {
      return envs;
    },
  },
  methods: {
    method1(): void {
      this.$refs.refHelloWorld.methodA();
    },
  },
}
</script>
```



#### 3. Vue.js 2 + Typescript + decorator ( based on Class )

```html
<script lang="ts">
import { Component, Ref, Vue, Watch } from "vue-property-decorator";

@Component({
  name: "MyComponent1",
  components: { HelloWorld },
})
export default class MyComponent1 extends Vue {
  @Ref("refHelloWorld") readonly refHelloWorld: HelloWorld;
  readonly envs: typeof envs = envs;
  listValue = [] as string[];
  mounted(): void {
    console.log("onMounted");
  }
  @Watch("listValue", { immediate: true })
  watchListValue(val: string[], oldVal: string[] | undefined) {
    console.log("listValue changed");
  }
  get filterListValue(): string[] {
    return this.listValue.filter((v) => !!v);
  }
  protected methods1(): void {
    this.refHelloWorld.methodsA();
  }
}
</script>
```

##### 장점

1. 클래스 기반의 코드들을 작성할 때와 같은 익숙함을 제공 
1. 가독성이 높음

##### 단점

1. 더이상 library 업데이트 되지 않음
2. Vue3에서 지원하지 않아 Migration 할 수 없음

#### 4. Vue.js 2 + Typescript + Composition API

```html
<script lang="ts">
import {computed, onMounted, reactive, ref, toRefs, watch} from "@vue/composition-api";

export default {
  components: { HelloWorld },
  setup() {
    const refHelloWorld = ref<null | InstanceType<typeof HelloWorld>>();
    const state = reactive({ listValue: [] as string[] });
    const dialog = ref(false); // dialog.value === false
    onMounted(() => {
      console.log("onMounted");
    });
    watch(
      () => state.listValue.value,
      () => {
        console.log("listValue changed");
      },
      { immediate: true },
    );
    const filterListValue = computed(() => state.listValue.value.filter((v) => !!v));
    const methods1 = (): void => {
      refHelloWorld.value?.methodsA();
    };
    return {
      refHelloWorld,
      ...toRefs(state),
      dialog,
      filterListValue,
      methods1,
      envs,
    };
  },
};
</script>
```

##### 장점

1. 애매한 표현인 this 를 제거하여 명확하게 표현
1. 매번 작성해야 하는 코드를 composition api 를 통해 가져올 수 있으므로 코드가 가장 간결하다.

##### 단점

1. method 를 제외한 모든 값은 Ref 형태로 상태변화를 감지하기 위해 `.value` 를 통해 접근하여야 한다. 

#### 5. Vue.js 3 + Typescript + Composition API

```html
<script lang="ts">
import {computed, onMounted, reactive, ref, toRefs, watch} from "@vue/composition-api";

export default {
  components: { HelloWorld },
  setup() {
    const refHelloWorld = ref(); // diff from vue2
    const state = reactive({ listValue: [] as string[] });
    onMounted(() => {
      console.log("onMounted");
    });
    watch(
      () => state.listValue,
      () => {
        console.log("listValue changed");
      },
      { immediate: true },
    );
    const filterListValue = computed(() => state.listValue.filter((v) => !!v));
    const methods1 = (): void => {
      refHelloWorld.value?.methodsA();
    };
    return {
      refHelloWorld,
      ...toRefs(state),
      filterListValue,
      methods1,
      envs,
    };
  },
};
</script>
```



##### Vue2 -> Vue3 

- 가상 DOM 최적화
  - 렌더링을 위해 템플릿 구문을 가상 DOM 트리로 반환한 후 실제로 DOM의 어떤 영역이 업데이트되어야 하는지 재귀적으로 탐색하는 방식
  - 변경 사항을 파악하기 위해 전체 DOM 트리를 재귀적으로 탐색해야만 하는 상황이 발생할 수 있음
  - Vue3에서는 이같이 불필요한 탐색을 위한 코드를 제거하여 렌더링 성능을 향상

- Tree shaking 강화
  - 나무를 흔들어 잎을 떨어트리듯 모듈을 번들링하는 과정에서 사용하지 않는 코드를 제거하여 파일 크기를 줄여 최적화
  - Vue.js 3.0 개발팀은 릴리즈 노트에서 가상 DOM 최적화, 트리쉐이킹 강화 등을 통해 이전 버전에 비해 번들 크기가 최대 41% 감소하였고, 초기 렌더링은 최대 55% 빠르며 업데이트와 메모리 사용량은 최대 133% 향상되었다고 밝혔습니다
- Composition API
  - 인스턴스의 특정 기능 단위로 모듈화된 로직을 여러 컴포넌트에서 재사용할 수 있음
- 완벽해진 Typescript 지원
  - Vue3에서는 코드베이스를 타입스크립트로 새로 작성
    - Vuex ($store) 타입 지원
    - Refs ($refs) 타입 지원
- 여러 루트 노드를 가질 수 있습니다.
  - 여러 루트 노드를 가지려고 루트 노드에  `<div>` 를 선언하지 않아도 된다.
- `<style>` 내에 `v-bind`를 이용하여 변수 처리

```javascript
<script setup>
const theme = {
  color: 'red'
}
</script>

<template>
  <p>hello</p>
</template>

<style scoped>
p {
  color: v-bind('theme.color');
}
</style>
```

- IE는 지원하지 않음(레가시 지원 코드를 제거 & 경량화)



#### 6. Vue.js 3 + Typescript + script setup ( based on Composition API )

```html
<script setup lang="ts">
import { computed, onMounted, ref, watch } from "@vue/composition-api";
import { HelloWorld } from ".HelloWorld.vue";
import { envs } from "../envs";

const refHelloWorld = ref();
const listValue = ref([] as string[]);
onMounted(() => {
  console.log("onMounted");
});
watch(
  () => listValue.value,
  () => {
    console.log("listValue changed");
  },
  { immediate: true },
);
const filterListValue = computed(() => listValue.value.filter((v) => !!v));
function methods1(): void {
  refHelloWorld.value?.methodsA();
};
</script>
```

##### 장점

1. 더 적은 상용구로 더 간결한 코드
2. 순수 TypeScript를 사용하여 `props` 및 `emitted event`를 선언하는 기능
3. 더 나은 런타임 성능(템플릿은 중간 프록시 없이 동일한 범위의 렌더 함수로 컴파일됨)
4. 더 나은 IDE 유형 추론 성능(언어 서버가 코드에서 유형을 추출하는 작업 감소)
5. 사용하지 않는 리소스를 즉시 확인 및 제거가능하다.

##### 단점

1. 있었나???