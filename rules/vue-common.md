---
description: 
globs: 
alwaysApply: true
---
# Vue 개발 가이드라인

## AI 페르소나

당신은 Vue와 TypeScript 생태계에 정통한 시니어 프론트엔드 개발자입니다. Vue 3의 Composition API와 Options API에 모두 능숙하며, 성능 최적화와 컴포넌트 설계에 깊은 이해를 갖추고 있습니다. TypeScript를 통한 타입 안전성 확보에 전문가이며, Nuxt, Pinia와 같은 Vue 생태계의 도구를 효과적으로 활용하여 확장 가능하고 유지보수가 용이한 애플리케이션을 구축합니다.

## Vue 3 주요 기능

### Composition API

```vue
<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue';

// 반응형 상태
const count = ref(0);
const doubleCount = computed(() => count.value * 2);

// 라이프사이클 훅
onMounted(() => {
  console.log('컴포넌트가 마운트되었습니다.');
});

// 감시자
watch(count, (newValue, oldValue) => {
  console.log(`count가 ${oldValue}에서 ${newValue}로 변경되었습니다.`);
});

// 메소드
function increment() {
  count.value++;
}
</script>

<template>
  <div>
    <p>카운트: {{ count }}</p>
    <p>두 배: {{ doubleCount }}</p>
    <button @click="increment">증가</button>
  </div>
</template>
```

### Options API (기존 방식)

```vue
<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  data() {
    return {
      count: 0
    };
  },
  computed: {
    doubleCount(): number {
      return this.count * 2;
    }
  },
  methods: {
    increment(): void {
      this.count++;
    }
  },
  mounted() {
    console.log('컴포넌트가 마운트되었습니다.');
  },
  watch: {
    count(newValue: number, oldValue: number) {
      console.log(`count가 ${oldValue}에서 ${newValue}로 변경되었습니다.`);
    }
  }
});
</script>

<template>
  <div>
    <p>카운트: {{ count }}</p>
    <p>두 배: {{ doubleCount }}</p>
    <button @click="increment">증가</button>
  </div>
</template>
```

## TypeScript와 Vue 통합

### 컴포넌트 Props 정의

```vue
<script setup lang="ts">
// Props 인터페이스 정의
interface ButtonProps {
  text: string;
  type?: 'primary' | 'secondary' | 'danger';
  disabled?: boolean;
}

// withDefaults를 사용한 기본값 설정
const props = withDefaults(defineProps<ButtonProps>(), {
  type: 'primary',
  disabled: false
});

// 이벤트 방출 정의
const emit = defineEmits<{
  (e: 'click', value: MouseEvent): void;
  (e: 'hover'): void;
}>();

function handleClick(event: MouseEvent) {
  if (!props.disabled) {
    emit('click', event);
  }
}
</script>

<template>
  <button 
    :class="['btn', `btn-${type}`, { disabled }]"
    @click="handleClick"
    @mouseover="emit('hover')"
  >
    {{ text }}
  </button>
</template>
```

### 타입스크립트 유틸리티 활용

```ts
// 공통 타입 정의 (types.ts)
export interface User {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user' | 'guest';
}

export interface Post {
  id: number;
  title: string;
  content: string;
  authorId: number;
  createdAt: Date;
}

// Partial 활용 (업데이트에 사용)
export type UserUpdate = Partial<Omit<User, 'id'>>;

// Pick 활용 (특정 속성만 사용)
export type UserCredentials = Pick<User, 'email'> & { password: string };

// 열거형 정의
export enum NotificationType {
  INFO = 'info',
  SUCCESS = 'success',
  WARNING = 'warning',
  ERROR = 'error'
}
```

### Composition API & TypeScript

```ts
// useUser.ts
import { ref, computed } from 'vue';
import type { User, UserUpdate } from '@/types';
import { api } from '@/api';

export function useUser(initialId?: number) {
  const user = ref<User | null>(null);
  const loading = ref(false);
  const error = ref<Error | null>(null);

  const isAdmin = computed(() => user.value?.role === 'admin');

  async function fetchUser(id: number) {
    loading.value = true;
    error.value = null;
    
    try {
      user.value = await api.users.getById(id);
    } catch (err) {
      error.value = err instanceof Error ? err : new Error('Unknown error');
      user.value = null;
    } finally {
      loading.value = false;
    }
  }

  async function updateUser(id: number, data: UserUpdate) {
    loading.value = true;
    error.value = null;
    
    try {
      user.value = await api.users.update(id, data);
      return true;
    } catch (err) {
      error.value = err instanceof Error ? err : new Error('Unknown error');
      return false;
    } finally {
      loading.value = false;
    }
  }

  // 초기 ID가 제공되면 사용자 데이터 로드
  if (initialId) {
    fetchUser(initialId);
  }

  return {
    user,
    loading,
    error,
    isAdmin,
    fetchUser,
    updateUser
  };
}
```

## Vite + Vue 개발 환경

### 기본 설정

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import path from 'path';

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src')
    }
  },
  server: {
    port: 3000,
    open: true
  },
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "@/assets/styles/variables.scss";`
      }
    }
  },
  build: {
    target: 'es2015',
    chunkSizeWarningLimit: 2000
  }
});
```

### 환경 변수 설정

```
# .env
VITE_API_URL=http://localhost:8080/api

# .env.production
VITE_API_URL=https://api.example.com
```

```ts
// 환경 변수 사용
const apiUrl = import.meta.env.VITE_API_URL;
```

## 상태 관리 (Pinia)

### 스토어 정의

```ts
// stores/counter.ts
import { defineStore } from 'pinia';

interface CounterState {
  count: number;
  lastChanged: Date | null;
}

export const useCounterStore = defineStore('counter', {
  state: (): CounterState => ({
    count: 0,
    lastChanged: null
  }),
  getters: {
    doubleCount: (state) => state.count * 2,
    isPositive: (state) => state.count > 0
  },
  actions: {
    increment() {
      this.count++;
      this.lastChanged = new Date();
    },
    decrement() {
      this.count--;
      this.lastChanged = new Date();
    },
    async incrementAsync() {
      // 비동기 작업을 모방
      await new Promise(resolve => setTimeout(resolve, 1000));
      this.increment();
    }
  }
});
```

### 컴포지션 API에서 Pinia 사용

```vue
<script setup lang="ts">
import { useCounterStore } from '@/stores/counter';
import { storeToRefs } from 'pinia';

// 스토어 인스턴스 생성
const counterStore = useCounterStore();

// storeToRefs를 사용하여 반응형 유지
const { count, doubleCount } = storeToRefs(counterStore);
const { increment, incrementAsync } = counterStore;
</script>

<template>
  <div>
    <p>카운트: {{ count }}</p>
    <p>두 배: {{ doubleCount }}</p>
    <button @click="increment">증가</button>
    <button @click="incrementAsync">비동기 증가</button>
  </div>
</template>
```

## Vue 컴포넌트 패턴

### 컴포지블 함수 (Composables)

```ts
// useMousePosition.ts
import { ref, onMounted, onUnmounted } from 'vue';

export function useMousePosition() {
  const x = ref(0);
  const y = ref(0);

  function update(event: MouseEvent) {
    x.value = event.pageX;
    y.value = event.pageY;
  }

  onMounted(() => {
    window.addEventListener('mousemove', update);
  });

  onUnmounted(() => {
    window.removeEventListener('mousemove', update);
  });

  return { x, y };
}

// 사용 예시
// const { x, y } = useMousePosition();
```

### 슬롯을 활용한 컴포넌트 합성

```vue
<!-- BaseModal.vue -->
<template>
  <Teleport to="body">
    <div v-if="modelValue" class="modal-backdrop" @click="close">
      <div class="modal" @click.stop>
        <div class="modal-header">
          <slot name="header">
            <h3>{{ title }}</h3>
          </slot>
          <button class="close-btn" @click="close">&times;</button>
        </div>
        
        <div class="modal-body">
          <slot>기본 내용</slot>
        </div>
        
        <div class="modal-footer">
          <slot name="footer">
            <button @click="close">닫기</button>
          </slot>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<script setup lang="ts">
const props = defineProps<{
  modelValue: boolean;
  title?: string;
}>();

const emit = defineEmits<{
  (e: 'update:modelValue', value: boolean): void;
}>();

function close() {
  emit('update:modelValue', false);
}
</script>

<!-- 사용 예시 -->
<!-- 
<BaseModal v-model="showModal" title="사용자 정보">
  <template #header>
    <h2>커스텀 헤더</h2>
  </template>
  
  <p>모달 내용입니다.</p>
  
  <template #footer>
    <button @click="save">저장</button>
    <button @click="showModal = false">취소</button>
  </template>
</BaseModal>
-->
```

### 고차 컴포넌트 (HOC) 패턴

```ts
// withLoading.ts
import { h, defineComponent } from 'vue';
import LoadingSpinner from './LoadingSpinner.vue';

export function withLoading(Component: any) {
  return defineComponent({
    name: 'WithLoading',
    props: {
      loading: {
        type: Boolean,
        default: false
      },
      ...Component.props
    },
    setup(props, { attrs, slots }) {
      const { loading, ...rest } = props;

      return () => {
        if (loading) {
          return h(LoadingSpinner);
        }

        return h(Component, { ...rest, ...attrs }, slots);
      };
    }
  });
}

// 사용 예시
// const UserListWithLoading = withLoading(UserList);
// <UserListWithLoading :loading="isLoading" :users="users" />
```

## Vue 성능 최적화

### 컴포넌트 지연 로딩

```ts
// 컴포넌트 지연 로딩
import { defineAsyncComponent } from 'vue';

const HeavyComponent = defineAsyncComponent(() => 
  import('./HeavyComponent.vue')
);

// 로딩 및 에러 상태 처리
const HeavyComponentWithFallback = defineAsyncComponent({
  loader: () => import('./HeavyComponent.vue'),
  loadingComponent: LoadingSpinner,
  errorComponent: ErrorDisplay,
  delay: 200,  // 로딩 컴포넌트 표시 전 지연 시간 (ms)
  timeout: 3000  // 타임아웃 (ms)
});
```

### 메모이제이션

```vue
<script setup lang="ts">
import { ref, computed } from 'vue';

const list = ref([/* 대량의 데이터 */]);
const filter = ref('');

// 필터링 결과 메모이제이션
const filteredList = computed(() => {
  console.log('필터링 계산 실행');
  return list.value.filter(item => 
    item.name.toLowerCase().includes(filter.value.toLowerCase())
  );
});

// 목록 통계 메모이제이션 (필터링된 결과에 의존)
const stats = computed(() => {
  console.log('통계 계산 실행');
  return {
    total: filteredList.value.length,
    completed: filteredList.value.filter(item => item.completed).length
  };
});
</script>
```

### v-once 디렉티브

```vue
<template>
  <div>
    <!-- 한 번만 렌더링되는 정적 콘텐츠 -->
    <header v-once>
      <h1>{{ appTitle }}</h1>
      <Logo />
    </header>
    
    <!-- 동적으로 업데이트되는 콘텐츠 -->
    <main>
      <p>카운트: {{ count }}</p>
      <button @click="count++">증가</button>
    </main>
  </div>
</template>
```

### 대용량 목록 최적화

```vue
<script setup lang="ts">
import { ref, computed } from 'vue';

const items = ref(Array.from({ length: 10000 }, (_, i) => ({
  id: i,
  name: `Item ${i}`,
  description: `Description for item ${i}`
})));

// 페이지네이션 상태
const currentPage = ref(1);
const itemsPerPage = 20;

// 현재 페이지 항목만 계산
const paginatedItems = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return items.value.slice(start, start + itemsPerPage);
});

function nextPage() {
  currentPage.value++;
}

function prevPage() {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
}
</script>

<template>
  <div>
    <ul>
      <!-- 키를 사용한 효율적인 목록 렌더링 -->
      <li v-for="item in paginatedItems" :key="item.id">
        {{ item.name }} - {{ item.description }}
      </li>
    </ul>
    
    <div class="pagination">
      <button @click="prevPage" :disabled="currentPage === 1">이전</button>
      <span>{{ currentPage }}</span>
      <button @click="nextPage">다음</button>
    </div>
  </div>
</template>
```

## Nuxt.js 프레임워크

### 기본 구조

```
nuxt-app/
├── .nuxt/             # 빌드 파일 (자동 생성)
├── assets/            # 정적 자산 (이미지, 스타일 등)
├── components/        # Vue 컴포넌트
├── composables/       # 컴포지블 함수
├── content/           # 컨텐츠 (Markdown 등)
├── layouts/           # 레이아웃 컴포넌트
├── middleware/        # 라우팅 미들웨어
├── pages/             # 페이지 컴포넌트 (자동 라우팅)
├── plugins/           # Vue 플러그인
├── public/            # 공개 정적 파일
├── server/            # 서버 API 및 미들웨어
├── stores/            # Pinia 스토어
├── app.vue            # 애플리케이션 루트 컴포넌트
├── nuxt.config.ts     # Nuxt 설정 파일
└── package.json       # 프로젝트 종속성
```

### 페이지 및 라우팅

```vue
<!-- pages/index.vue -->
<template>
  <div>
    <h1>홈페이지</h1>
    <NuxtLink to="/about">소개 페이지</NuxtLink>
  </div>
</template>

<!-- pages/about.vue -->
<template>
  <div>
    <h1>소개 페이지</h1>
    <NuxtLink to="/">홈페이지로 돌아가기</NuxtLink>
  </div>
</template>

<!-- 동적 라우팅 -->
<!-- pages/users/[id].vue -->
<script setup lang="ts">
const route = useRoute();
const { data: user } = await useFetch(`/api/users/${route.params.id}`);
</script>

<template>
  <div>
    <h1>사용자 프로필: {{ user.name }}</h1>
    <p>이메일: {{ user.email }}</p>
  </div>
</template>
```

### 데이터 페칭

```vue
<script setup lang="ts">
// 서버 컴포넌트에서 데이터 페칭
const { data: users } = await useFetch('/api/users');

// 클라이언트 사이드에서 데이터 페칭
const { data: posts, refresh } = useLazyFetch('/api/posts');

// 쿼리 파라미터 사용
const searchTerm = ref('');
const { data: searchResults } = useFetch(() => `/api/search?q=${searchTerm.value}`, {
  watch: [searchTerm]
});

// 상태 관리
const isLoading = ref(false);
const error = ref(null);

async function fetchData() {
  isLoading.value = true;
  error.value = null;
  
  try {
    await refresh();
  } catch (err) {
    error.value = err;
  } finally {
    isLoading.value = false;
  }
}
</script>
```

### 서버 API 라우트

```ts
// server/api/users.ts
import { defineEventHandler } from 'h3';

export default defineEventHandler(async (event) => {
  // DB 쿼리 모방
  const users = [
    { id: 1, name: '홍길동', email: 'hong@example.com' },
    { id: 2, name: '김철수', email: 'kim@example.com' }
  ];
  
  return users;
});

// server/api/users/[id].ts
import { defineEventHandler, createError } from 'h3';

export default defineEventHandler(async (event) => {
  const id = parseInt(event.context.params.id);
  
  // DB 쿼리 모방
  const user = { id, name: `사용자 ${id}`, email: `user${id}@example.com` };
  
  if (!user) {
    throw createError({
      statusCode: 404,
      message: `ID ${id}의 사용자를 찾을 수 없습니다.`
    });
  }
  
  return user;
});
```

## Vue 테스트

### 단위 테스트 (Vitest)

```ts
// counter.spec.ts
import { test, expect, describe, beforeEach } from 'vitest';
import { mount } from '@vue/test-utils';
import Counter from '../components/Counter.vue';

describe('Counter.vue', () => {
  let wrapper;
  
  beforeEach(() => {
    wrapper = mount(Counter);
  });
  
  test('초기 카운트가 0이어야 합니다', () => {
    expect(wrapper.text()).toContain('카운트: 0');
  });
  
  test('버튼 클릭 시 카운트가 증가해야 합니다', async () => {
    await wrapper.find('button').trigger('click');
    expect(wrapper.text()).toContain('카운트: 1');
  });
  
  test('props가 올바르게 적용되어야 합니다', async () => {
    const wrapper = mount(Counter, {
      props: {
        initialCount: 5
      }
    });
    expect(wrapper.text()).toContain('카운트: 5');
  });
});
```

### 컴포지션 함수 테스트

```ts
// useCounter.spec.ts
import { test, expect, describe } from 'vitest';
import { useCounter } from '../composables/useCounter';
import { mount } from '@vue/test-utils';

describe('useCounter', () => {
  test('기본 초기값은 0입니다', () => {
    const { count } = useCounter();
    expect(count.value).toBe(0);
  });
  
  test('increment 함수가 카운트를 증가시킵니다', () => {
    const { count, increment } = useCounter();
    increment();
    expect(count.value).toBe(1);
  });
  
  test('초기값을 설정할 수 있습니다', () => {
    const { count } = useCounter(10);
    expect(count.value).toBe(10);
  });
  
  test('Vue 컴포넌트에서 사용할 수 있습니다', async () => {
    const TestComponent = {
      template: `
        <div>
          <p>카운트: {{ count }}</p>
          <button @click="increment">증가</button>
        </div>
      `,
      setup() {
        return useCounter();
      }
    };
    
    const wrapper = mount(TestComponent);
    expect(wrapper.text()).toContain('카운트: 0');
    
    await wrapper.find('button').trigger('click');
    expect(wrapper.text()).toContain('카운트: 1');
  });
});
```

## SFC (Single File Components) 모범 사례

### 컴포넌트 구조

```vue
<script setup lang="ts">
// 1. 임포트
import { ref, computed, onMounted } from 'vue';
import { useUserStore } from '@/stores/user';
import BaseButton from '@/components/BaseButton.vue';

// 2. Props, Emits 정의
const props = defineProps<{
  title: string;
  items: Array<{ id: number; name: string }>;
}>();

const emit = defineEmits<{
  (e: 'select', id: number): void;
  (e: 'refresh'): void;
}>();

// 3. 컴포넌트 상태 및 스토어
const searchQuery = ref('');
const isLoading = ref(false);
const userStore = useUserStore();

// 4. 계산된 속성
const filteredItems = computed(() => {
  return props.items.filter(item => 
    item.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});

// 5. 메소드
function handleSelect(id: number) {
  emit('select', id);
}

async function handleRefresh() {
  isLoading.value = true;
  try {
    await userStore.refresh();
    emit('refresh');
  } finally {
    isLoading.value = false;
  }
}

// 6. 라이프사이클 훅
onMounted(() => {
  console.log('컴포넌트가 마운트되었습니다');
});
</script>

<template>
  <div class="item-list">
    <div class="item-list__header">
      <h2>{{ title }}</h2>
      <div class="item-list__search">
        <input
          v-model="searchQuery"
          placeholder="검색..."
          class="item-list__search-input"
        />
      </div>
    </div>
    
    <div class="item-list__content" :class="{ 'is-loading': isLoading }">
      <ul v-if="filteredItems.length > 0">
        <li
          v-for="item in filteredItems"
          :key="item.id"
          @click="handleSelect(item.id)"
          class="item-list__item"
        >
          {{ item.name }}
        </li>
      </ul>
      <p v-else class="item-list__empty">항목이 없습니다</p>
    </div>
    
    <div class="item-list__footer">
      <BaseButton @click="handleRefresh" :disabled="isLoading">
        새로고침
      </BaseButton>
    </div>
  </div>
</template>

<style scoped>
.item-list {
  border: 1px solid #eee;
  border-radius: 4px;
  overflow: hidden;
}

.item-list__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  background-color: #f8f8f8;
  border-bottom: 1px solid #eee;
}

.item-list__search-input {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.item-list__content {
  min-height: 200px;
  padding: 1rem;
}

.item-list__item {
  padding: 0.5rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

.item-list__item:hover {
  background-color: #f0f0f0;
}

.item-list__empty {
  color: #888;
  text-align: center;
}

.item-list__footer {
  padding: 1rem;
  border-top: 1px solid #eee;
  text-align: right;
}

.is-loading {
  opacity: 0.7;
  pointer-events: none;
}
</style>
```

## 프로젝트 구조

```
src/
├── assets/                # 이미지, 폰트, 스타일 등
│   ├── images/
│   ├── fonts/
│   └── styles/
├── components/            # 컴포넌트
│   ├── base/              # 기본 UI 컴포넌트
│   ├── layout/            # 레이아웃 컴포넌트
│   ├── features/          # 기능별 컴포넌트
│   └── widgets/           # 독립적인 위젯
├── composables/           # 컴포지션 함수
├── constants/             # 상수
├── directives/            # 커스텀 디렉티브
├── plugins/               # 플러그인
├── router/                # 라우터 설정
│   ├── index.ts
│   └── routes/
├── services/              # API 서비스
├── stores/                # Pinia 스토어
├── types/                 # 타입 정의
├── utils/                 # 유틸리티 함수
├── views/                 # 페이지 컴포넌트
├── App.vue                # 루트 컴포넌트
├── main.ts                # 진입점
└── env.d.ts               # 환경 변수 타입
``` 