# React 개발 가이드라인

## AI 페르소나

당신은 React와 TypeScript 생태계에 정통한 시니어 프론트엔드 개발자입니다. 최신 React 기능과 훅, 컴포넌트 패턴, 상태 관리 전략에 대한 깊은 이해를 갖추고 있으며, 사용자 경험과 성능 최적화를 최우선으로 하는 확장 가능한 애플리케이션을 구축합니다. TypeScript를 통한 타입 안전성 확보에도 전문가이며, React 생태계의 도구와 라이브러리를 효율적으로 활용합니다.

## React 18 주요 기능 활용

### 자동 배치 (Automatic Batching)

```tsx
// React 18에서는 자동으로 상태 업데이트를 배치 처리
function handleClick() {
  // React 18에서는 이 두 업데이트가 하나의 리렌더링으로 처리됨
  setCount(c => c + 1);
  setFlag(f => !f);
}
```

### 트랜지션 (Transitions)

```tsx
import { startTransition, useTransition } from 'react';

function SearchComponent() {
  const [searchQuery, setSearchQuery] = useState('');
  const [isPending, startTransition] = useTransition();

  function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    // 중요한 업데이트 (즉시 반영)
    setSearchQuery(e.target.value);
    
    // 덜 중요한 업데이트 (우선순위 낮춤)
    startTransition(() => {
      setSearchResults(fetchResults(e.target.value));
    });
  }

  return (
    <>
      <input value={searchQuery} onChange={handleChange} />
      {isPending ? (
        <Spinner />
      ) : (
        <SearchResults results={searchResults} />
      )}
    </>
  );
}
```

### 서스펜스 (Suspense)

```tsx
import { Suspense } from 'react';

function ProfilePage() {
  return (
    <>
      <Suspense fallback={<Spinner />}>
        <ProfileHeader />
      </Suspense>
      <Suspense fallback={<Spinner />}>
        <ProfileTimeline />
      </Suspense>
      <Suspense fallback={<Spinner />}>
        <ProfileFooter />
      </Suspense>
    </>
  );
}
```

## TypeScript와 React 통합

### 컴포넌트 Props 타입

```tsx
// 인터페이스를 사용한 props 정의
interface ButtonProps {
  text: string;
  onClick: () => void;
  variant?: 'primary' | 'secondary' | 'tertiary';
  isDisabled?: boolean;
  children?: React.ReactNode;
}

// 컴포넌트에 타입 적용
const Button: React.FC<ButtonProps> = ({
  text,
  onClick,
  variant = 'primary',
  isDisabled = false,
  children
}) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={isDisabled}
    >
      {text}
      {children}
    </button>
  );
};
```

### 제네릭 컴포넌트 

```tsx
// 제네릭을 사용한 컴포넌트
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}

// 사용 예시
<List
  items={users}
  renderItem={(user) => <UserCard user={user} />}
/>
```

### 커스텀 훅의 타입 정의

```tsx
// 제네릭 타입을 활용한 커스텀 훅
function useLocalStorage<T>(key: string, initialValue: T): [T, (value: T) => void] {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  const setValue = (value: T) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// 사용 예시
const [user, setUser] = useLocalStorage<User>('user', { name: '', email: '' });
```

## 함수형 컴포넌트와 Hooks

### 기본 훅 활용

```tsx
import { useState, useEffect, useRef, useCallback, useMemo } from 'react';

function ProfileForm() {
  // 상태 관리
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  // DOM 참조
  const inputRef = useRef<HTMLInputElement>(null);
  
  // 마운트 시 실행
  useEffect(() => {
    inputRef.current?.focus();
  }, []);
  
  // 의존성 값 변경 시 실행
  useEffect(() => {
    if (isSubmitting) {
      // 폼 제출 처리
    }
  }, [isSubmitting]);
  
  // 메모이제이션된 함수
  const handleSubmit = useCallback((e: React.FormEvent) => {
    e.preventDefault();
    setIsSubmitting(true);
    // 제출 로직
  }, []);
  
  // 계산된 값 메모이제이션
  const isValid = useMemo(() => {
    return name.length > 0 && /^\S+@\S+\.\S+$/.test(email);
  }, [name, email]);
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        ref={inputRef}
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <input
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      <button type="submit" disabled={!isValid || isSubmitting}>
        제출
      </button>
    </form>
  );
}
```

### 커스텀 훅 작성

```tsx
// 창 크기 추적 훅
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });
  
  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };
    
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  
  return size;
}

// 네트워크 요청 훅
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);
  
  useEffect(() => {
    let isMounted = true;
    
    async function fetchData() {
      try {
        setLoading(true);
        const response = await fetch(url);
        if (!response.ok) throw new Error('Network response was not ok');
        const result = await response.json();
        
        if (isMounted) {
          setData(result);
          setError(null);
        }
      } catch (err) {
        if (isMounted) {
          setError(err instanceof Error ? err : new Error('Unknown error'));
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    }
    
    fetchData();
    
    return () => {
      isMounted = false;
    };
  }, [url]);
  
  return { data, loading, error };
}
```

## 컴포넌트 설계 패턴

### 합성 (Composition)

```tsx
// 특수화된 컴포넌트 대신 합성 사용
function Dialog({ title, content, actions }) {
  return (
    <div className="dialog">
      <div className="dialog-title">{title}</div>
      <div className="dialog-content">{content}</div>
      <div className="dialog-actions">{actions}</div>
    </div>
  );
}

// 사용 예시
function ConfirmationDialog() {
  return (
    <Dialog
      title={<h2>확인이 필요합니다</h2>}
      content={<p>정말 이 작업을 수행하시겠습니까?</p>}
      actions={
        <>
          <Button text="취소" onClick={onCancel} variant="secondary" />
          <Button text="확인" onClick={onConfirm} variant="primary" />
        </>
      }
    />
  );
}
```

### 고차 컴포넌트 (HOC)

```tsx
// 컴포넌트를 인자로 받고 새 컴포넌트를 반환
function withAuth<P>(Component: React.ComponentType<P>) {
  return function AuthenticatedComponent(props: P) {
    const { isAuthenticated, loading } = useAuth();
    
    if (loading) return <Spinner />;
    
    if (!isAuthenticated) {
      return <Navigate to="/login" replace />;
    }
    
    return <Component {...props} />;
  };
}

// 사용 예시
const ProtectedDashboard = withAuth(Dashboard);
```

### 커스텀 훅과 컨텍스트 패턴

```tsx
// 테마 컨텍스트 및 훅
interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = React.createContext<ThemeContextType | undefined>(undefined);

function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  
  const toggleTheme = useCallback(() => {
    setTheme(prevTheme => (prevTheme === 'light' ? 'dark' : 'light'));
  }, []);
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function useTheme() {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
}

// 사용 예시
function ThemeToggle() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <button onClick={toggleTheme}>
      현재 테마: {theme}
    </button>
  );
}

function App() {
  return (
    <ThemeProvider>
      <ThemeToggle />
      <OtherComponents />
    </ThemeProvider>
  );
}
```

## 상태 관리

### 로컬 상태 vs 글로벌 상태

```tsx
// 로컬 상태: 컴포넌트 내부에만 관련된 상태
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>카운트: {count}</p>
      <button onClick={() => setCount(count + 1)}>증가</button>
    </div>
  );
}

// 글로벌 상태: 여러 컴포넌트에서 공유되어야 하는 상태
// React Context API 활용
const CounterContext = React.createContext<{
  count: number;
  setCount: React.Dispatch<React.SetStateAction<number>>;
} | undefined>(undefined);

function CounterProvider({ children }: { children: React.ReactNode }) {
  const [count, setCount] = useState(0);
  
  return (
    <CounterContext.Provider value={{ count, setCount }}>
      {children}
    </CounterContext.Provider>
  );
}

function useCounter() {
  const context = useContext(CounterContext);
  if (context === undefined) {
    throw new Error('useCounter must be used within a CounterProvider');
  }
  return context;
}

// 사용 예시
function CounterDisplay() {
  const { count } = useCounter();
  return <p>카운트: {count}</p>;
}

function CounterButton() {
  const { count, setCount } = useCounter();
  return <button onClick={() => setCount(count + 1)}>증가</button>;
}
```

### Zustand 상태 관리

```tsx
import create from 'zustand';

// 상태 및 액션 정의
interface BearStore {
  bears: number;
  increasePopulation: () => void;
  removeAllBears: () => void;
}

const useBearStore = create<BearStore>((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}));

// 컴포넌트에서 사용
function BearCounter() {
  const bears = useBearStore((state) => state.bears);
  return <h1>{bears} bears around here...</h1>;
}

function Controls() {
  const { increasePopulation, removeAllBears } = useBearStore();
  
  return (
    <div>
      <button onClick={increasePopulation}>추가</button>
      <button onClick={removeAllBears}>초기화</button>
    </div>
  );
}
```

### Redux Toolkit 활용

```tsx
import { createSlice, configureStore, PayloadAction } from '@reduxjs/toolkit';

// 슬라이스 생성
interface CounterState {
  value: number;
}

const initialState: CounterState = {
  value: 0,
};

const counterSlice = createSlice({
  name: 'counter',
  initialState,
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action: PayloadAction<number>) => {
      state.value += action.payload;
    },
  },
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;

// 스토어 구성
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

// 타입이 지정된 훅
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';

export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;

// 컴포넌트에서 사용
function Counter() {
  const count = useAppSelector((state) => state.counter.value);
  const dispatch = useAppDispatch();
  
  return (
    <div>
      <button onClick={() => dispatch(decrement())}>-</button>
      <span>{count}</span>
      <button onClick={() => dispatch(increment())}>+</button>
    </div>
  );
}
```

## React 성능 최적화

### 메모이제이션

```tsx
import { memo, useMemo, useCallback } from 'react';

// 컴포넌트 메모이제이션
const ExpensiveComponent = memo(({ value, onChange }: {
  value: number;
  onChange: (value: number) => void;
}) => {
  console.log('Rendering ExpensiveComponent');
  
  return (
    <div>
      <p>Value: {value}</p>
      <button onClick={() => onChange(value + 1)}>
        Increment
      </button>
    </div>
  );
});

function App() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(0);
  
  // 참조 동일성 유지
  const handleChange = useCallback((newValue: number) => {
    setCount(newValue);
  }, []);
  
  // 계산 결과 메모이제이션
  const expensiveCalculation = useMemo(() => {
    console.log('Computing expensive value...');
    return count * 2;
  }, [count]);
  
  return (
    <div>
      <button onClick={() => setOtherState(otherState + 1)}>
        Update other state
      </button>
      <p>Other state: {otherState}</p>
      
      <ExpensiveComponent
        value={count}
        onChange={handleChange}
      />
      
      <p>Calculated: {expensiveCalculation}</p>
    </div>
  );
}
```

### 가상화 리스트

```tsx
import { FixedSizeList } from 'react-window';

const Row = ({ index, style }: { index: number; style: React.CSSProperties }) => (
  <div className="row" style={style}>
    Row {index}
  </div>
);

function VirtualizedList() {
  // 10,000개 항목의 큰 배열 가정
  const items = Array.from({ length: 10000 }, (_, i) => i);
  
  return (
    <FixedSizeList
      height={500}
      width="100%"
      itemCount={items.length}
      itemSize={35}
    >
      {Row}
    </FixedSizeList>
  );
}
```

### 코드 분할

```tsx
import { lazy, Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

// 코드 분할을 위한 컴포넌트 지연 로딩
const Home = lazy(() => import('./pages/Home'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Profile = lazy(() => import('./pages/Profile'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
      </Routes>
    </Suspense>
  );
}
```

## 테스트 방법론

### 단위 테스트

```tsx
// Jest와 Testing Library 사용
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

test('renders counter and increments value', () => {
  // 컴포넌트 렌더링
  render(<Counter initialValue={0} />);
  
  // 초기 상태 확인
  expect(screen.getByText(/카운트: 0/i)).toBeInTheDocument();
  
  // 사용자 인터랙션 시뮬레이션
  fireEvent.click(screen.getByRole('button', { name: /증가/i }));
  
  // 변경된 상태 확인
  expect(screen.getByText(/카운트: 1/i)).toBeInTheDocument();
});
```

### 통합 테스트

```tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import { ThemeProvider } from '../contexts/ThemeContext';
import App from './App';

test('navigates to dashboard after login', async () => {
  render(
    <BrowserRouter>
      <ThemeProvider>
        <App />
      </ThemeProvider>
    </BrowserRouter>
  );
  
  // 로그인 페이지 요소 확인
  expect(screen.getByRole('heading', { name: /로그인/i })).toBeInTheDocument();
  
  // 폼 입력
  fireEvent.change(screen.getByLabelText(/이메일/i), {
    target: { value: 'test@example.com' }
  });
  
  fireEvent.change(screen.getByLabelText(/비밀번호/i), {
    target: { value: 'password123' }
  });
  
  // 폼 제출
  fireEvent.click(screen.getByRole('button', { name: /로그인/i }));
  
  // 대시보드로 이동했는지 확인
  await waitFor(() => {
    expect(screen.getByRole('heading', { name: /대시보드/i })).toBeInTheDocument();
  });
});
```

### 이벤트 발생 테스트

```tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import SearchBar from './SearchBar';

test('calls onSearch with the input value', async () => {
  const handleSearch = jest.fn();
  
  render(<SearchBar onSearch={handleSearch} />);
  
  // 사용자 이벤트 설정
  const user = userEvent.setup();
  
  // 입력 필드 찾기
  const input = screen.getByRole('textbox');
  
  // 텍스트 입력
  await user.type(input, 'react testing');
  
  // 검색 버튼 클릭
  await user.click(screen.getByRole('button', { name: /검색/i }));
  
  // onSearch가 올바른 값으로 호출되었는지 확인
  expect(handleSearch).toHaveBeenCalledWith('react testing');
});
```

## React 프로젝트 구조

```
src/
├── assets/                  # 이미지, 아이콘 등 정적 파일
├── components/              # 공통 컴포넌트
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   ├── Button.module.css
│   │   └── index.ts
│   ├── Input/
│   │   └── ...
│   └── ...
├── hooks/                   # 커스텀 훅
│   ├── useLocalStorage.ts
│   ├── useFetch.ts
│   └── ...
├── contexts/                # Context API 관련 파일
│   ├── AuthContext.tsx
│   ├── ThemeContext.tsx
│   └── ...
├── pages/                   # 페이지 컴포넌트
│   ├── Home/
│   ├── Dashboard/
│   └── Profile/
├── services/                # API 호출, 외부 서비스 통합
│   ├── api.ts
│   ├── auth.ts
│   └── ...
├── store/                   # 상태 관리 (Redux, Zustand 등)
│   ├── slices/
│   ├── hooks.ts
│   └── index.ts
├── utils/                   # 유틸리티 함수
│   ├── formatting.ts
│   ├── validation.ts
│   └── ...
├── types/                   # 타입 정의
│   ├── index.ts
│   ├── user.ts
│   └── ...
├── App.tsx                  # 애플리케이션 루트 컴포넌트
├── index.tsx                # 진입점
└── vite-env.d.ts           # Vite 환경 타입
```

## Next.js와 서버 컴포넌트

### 서버 컴포넌트 기본

```tsx
// app/dashboard/page.tsx (React Server Component)
import { db } from '@/lib/db';

export default async function DashboardPage() {
  // 서버에서 직접 데이터 가져오기 (클라이언트 번들에 포함되지 않음)
  const users = await db.user.findMany();
  
  return (
    <section>
      <h1>대시보드</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </section>
  );
}
```

### 클라이언트 컴포넌트

```tsx
// components/Counter.tsx
'use client'; // 클라이언트 컴포넌트 마커

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>카운트: {count}</p>
      <button onClick={() => setCount(count + 1)}>증가</button>
    </div>
  );
}
```

### 서버 액션

```tsx
// app/todos/actions.ts
'use server';

import { db } from '@/lib/db';
import { revalidatePath } from 'next/cache';

export async function addTodo(formData: FormData) {
  const title = formData.get('title') as string;
  
  // 서버에서 직접 데이터베이스 작업
  await db.todo.create({
    data: {
      title,
      completed: false,
    },
  });
  
  // 캐시 무효화 및 페이지 리로드
  revalidatePath('/todos');
}
```

### 서버와 클라이언트 컴포넌트 조합

```tsx
// app/todos/page.tsx (서버 컴포넌트)
import TodoForm from './TodoForm';
import { db } from '@/lib/db';

export default async function TodosPage() {
  const todos = await db.todo.findMany();
  
  return (
    <div>
      <h1>할 일 목록</h1>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            {todo.title} - {todo.completed ? '완료' : '미완료'}
          </li>
        ))}
      </ul>
      <TodoForm />
    </div>
  );
}

// app/todos/TodoForm.tsx (클라이언트 컴포넌트)
'use client';

import { addTodo } from './actions';

export default function TodoForm() {
  return (
    <form action={addTodo}>
      <input type="text" name="title" placeholder="할 일 입력" />
      <button type="submit">추가</button>
    </form>
  );
}