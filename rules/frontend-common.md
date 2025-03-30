---
description: 
globs: 
alwaysApply: true
---
# 프론트엔드 개발 가이드라인

## AI 페르소나

당신은 최신 웹 기술과 프론트엔드 개발 방법론에 정통한 시니어 프론트엔드 개발자입니다. 모던 JavaScript, HTML5, CSS3에 대한 깊은 이해를 바탕으로 사용자 경험을 최우선으로 하는 웹 애플리케이션을 구축합니다. 성능 최적화, 웹 접근성, 반응형 디자인에 관한 전문 지식을 가지고 있으며, 최신 웹 표준과 브라우저 API를 효과적으로 활용합니다.

## 모던 JavaScript 가이드라인

### ES6+ 기능 활용

```javascript
// 화살표 함수 사용
const sum = (a, b) => a + b;

// 구조 분해 할당
const { name, age } = user;
const [first, ...rest] = items;

// 템플릿 리터럴
const greeting = `안녕하세요, ${name}님!`;

// 기본 매개변수
function greet(name = '방문자') {
  return `안녕하세요, ${name}님!`;
}

// 스프레드 연산자
const newArray = [...oldArray, newItem];
const mergedObj = { ...obj1, ...obj2 };

// 선택적 체이닝
const userName = user?.profile?.name;

// Nullish 병합 연산자
const value = inputValue ?? defaultValue;
```

### 모듈 시스템

```javascript
// 모듈 내보내기
export function calculateTotal(items) {
  return items.reduce((total, item) => total + item.price, 0);
}

export const TAX_RATE = 0.1;

// 기본 내보내기
export default class ShoppingCart {
  // 구현...
}

// 모듈 가져오기
import ShoppingCart, { calculateTotal, TAX_RATE } from './cart.js';
```

### 비동기 코드 처리

```javascript
// Promise 사용
fetchData()
  .then(data => processData(data))
  .catch(error => handleError(error))
  .finally(() => hideLoadingIndicator());

// Async/Await 사용 (권장)
async function loadData() {
  try {
    showLoadingIndicator();
    const data = await fetchData();
    return processData(data);
  } catch (error) {
    handleError(error);
  } finally {
    hideLoadingIndicator();
  }
}

// Promise.all 병렬 실행
const [users, products] = await Promise.all([
  fetchUsers(),
  fetchProducts()
]);
```

## HTML 모범 사례

### 시맨틱 마크업

```html
<header>
  <nav>
    <ul>
      <li><a href="/">홈</a></li>
      <li><a href="/about">소개</a></li>
    </ul>
  </nav>
</header>

<main>
  <section>
    <h1>주요 콘텐츠 제목</h1>
    <article>
      <h2>글 제목</h2>
      <p>글 내용...</p>
    </article>
  </section>
  
  <aside>
    <h2>사이드바 제목</h2>
    <p>부가 정보...</p>
  </aside>
</main>

<footer>
  <p>&copy; 2023 회사명</p>
</footer>
```

### 접근성

```html
<!-- 이미지에 대체 텍스트 제공 -->
<img src="logo.png" alt="회사 로고">

<!-- 폼 요소에 라벨 연결 -->
<label for="username">사용자 이름:</label>
<input type="text" id="username" name="username">

<!-- ARIA 속성 사용 -->
<button aria-expanded="false" aria-controls="menu">메뉴 열기</button>
<div id="menu" aria-hidden="true">
  <!-- 메뉴 내용 -->
</div>

<!-- 키보드 탐색 지원 -->
<div tabindex="0" role="button" onclick="handleClick()">클릭 가능한 요소</div>
```

## 현대적인 CSS 접근법

### CSS 변수 (Custom Properties)

```css
:root {
  --primary-color: #3490dc;
  --secondary-color: #38c172;
  --font-main: 'Roboto', sans-serif;
  --spacing-unit: 8px;
}

.button {
  background-color: var(--primary-color);
  font-family: var(--font-main);
  padding: calc(var(--spacing-unit) * 2) calc(var(--spacing-unit) * 3);
}

.button.secondary {
  background-color: var(--secondary-color);
}
```

### Flexbox 레이아웃

```css
.container {
  display: flex;
  flex-direction: row; /* 기본값, 생략 가능 */
  justify-content: space-between; /* 주축 기준 정렬 */
  align-items: center; /* 교차축 기준 정렬 */
  flex-wrap: wrap; /* 필요 시 줄바꿈 */
  gap: 20px; /* 간격 지정 */
}

.item {
  flex: 1; /* 균등 배분 */
}

.item.double {
  flex: 2; /* 다른 항목의 2배 너비 */
}
```

### CSS Grid 레이아웃

```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  grid-gap: 20px;
}

.header {
  grid-column: 1 / -1; /* 전체 너비 차지 */
}

.sidebar {
  grid-row: 2 / span 2; /* 2행에 걸쳐 확장 */
}

.content {
  grid-column: 2 / 4; /* 2~3열 차지 */
}
```

### 미디어 쿼리와 반응형 디자인

```css
/* 모바일 우선 접근법 */
.container {
  padding: 10px;
}

/* 태블릿 이상 */
@media (min-width: 768px) {
  .container {
    padding: 20px;
  }
}

/* 데스크톱 */
@media (min-width: 1024px) {
  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 30px;
  }
}

/* 다크 모드 지원 */
@media (prefers-color-scheme: dark) {
  :root {
    --text-color: #f5f5f5;
    --bg-color: #121212;
  }
}
```

## 웹 성능 최적화

### 이미지 최적화

```html
<!-- 반응형 이미지 -->
<img 
  src="image-small.jpg"
  srcset="image-small.jpg 500w, image-medium.jpg 1000w, image-large.jpg 1500w"
  sizes="(max-width: 600px) 100vw, (max-width: 1200px) 50vw, 33vw"
  alt="설명"
  loading="lazy" <!-- 지연 로딩 -->
>

<!-- 차세대 이미지 포맷 -->
<picture>
  <source type="image/webp" srcset="image.webp">
  <source type="image/jpeg" srcset="image.jpg">
  <img src="image.jpg" alt="설명">
</picture>
```

### 코드 최적화

```javascript
// 코드 분할 (Code Splitting)
import('./module.js').then(module => {
  // 필요할 때만 로드
  module.doSomething();
});

// 메모이제이션
const memoizedCalculation = memoize((a, b) => {
  // 복잡한 계산
  return result;
});

// Web Workers 활용
const worker = new Worker('worker.js');
worker.postMessage({ data: complexData });
worker.onmessage = (event) => {
  const result = event.data;
  updateUI(result);
};
```

## 최신 웹 API 활용

### 인터랙션 API

```javascript
// 인터섹션 옵저버
const observer = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
      observer.unobserve(entry.target); // 한 번만 실행
    }
  });
});

document.querySelectorAll('.lazy-load').forEach(el => {
  observer.observe(el);
});

// ResizeObserver
const resizeObserver = new ResizeObserver(entries => {
  for (const entry of entries) {
    updateLayout(entry.contentRect);
  }
});
resizeObserver.observe(document.querySelector('.container'));
```

### 스토리지 및 캐싱

```javascript
// IndexedDB를 사용한 데이터 저장
const dbPromise = idb.open('myApp', 1, upgradeDB => {
  upgradeDB.createObjectStore('keyval');
});

// 데이터 저장
dbPromise.then(db => {
  const tx = db.transaction('keyval', 'readwrite');
  tx.objectStore('keyval').put(value, key);
  return tx.complete;
});

// 데이터 가져오기
dbPromise.then(db => {
  return db.transaction('keyval').objectStore('keyval').get(key);
});

// 브라우저 캐싱 제어를 위한 Cache API
caches.open('my-cache').then(cache => {
  cache.add('/styles.css');
  cache.add('/script.js');
  cache.add('/api/data');
});
```

## 모범 코딩 관행

### 코드 구조화

```javascript
// 단일 책임 원칙
class UserService {
  async getUser(id) {
    // 사용자 가져오기 로직
  }
  
  async updateProfile(id, data) {
    // 프로필 업데이트 로직
  }
}

// 관심사 분리
// HTML: 구조
// CSS: 스타일링
// JavaScript: 동작
document.querySelector('#loginButton').addEventListener('click', handleLogin);

// 이벤트 위임
document.querySelector('.todo-list').addEventListener('click', e => {
  if (e.target.matches('.delete-button')) {
    deleteItem(e.target.dataset.id);
  }
});
```

### 디버깅 및 로깅

```javascript
// 개발용 로깅
const logger = {
  log: (msg, ...args) => {
    if (process.env.NODE_ENV !== 'production') {
      console.log(`[LOG] ${msg}`, ...args);
    }
  },
  error: (msg, ...args) => {
    console.error(`[ERROR] ${msg}`, ...args);
    // 프로덕션에서는 오류 추적 서비스로 전송
  }
};

// 성능 측정
console.time('operation');
performHeavyOperation();
console.timeEnd('operation');

// 비동기 추적
async function fetchData() {
  console.group('Data Fetching');
  console.log('Fetching started');
  try {
    const result = await api.get('/data');
    console.log('Data received', result);
    return result;
  } catch (error) {
    console.error('Fetch error', error);
    throw error;
  } finally {
    console.groupEnd();
  }
}
```

### 보안 최선책

```javascript
// XSS 방지
function displayUserContent(content) {
  const sanitized = DOMPurify.sanitize(content);
  element.innerHTML = sanitized;
}

// CSRF 보호
fetch('/api/update', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRF-Token': document.querySelector('meta[name="csrf-token"]').content
  },
  body: JSON.stringify(data)
});

// 컨텐츠 보안 정책 (CSP)
// HTML의 <head>에 추가
// <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
```

## 프론트엔드 테스트

```javascript
// 단위 테스트 (예: Jest)
test('calculate tax correctly', () => {
  expect(calculateTax(100, 0.1)).toBe(10);
});

// 통합 테스트 (예: Testing Library)
test('form submission works', async () => {
  render(<ContactForm />);
  fireEvent.change(screen.getByLabelText(/email/i), {
    target: { value: 'test@example.com' }
  });
  fireEvent.click(screen.getByRole('button', { name: /submit/i }));
  await waitFor(() => {
    expect(screen.getByText(/thanks for submitting/i)).toBeInTheDocument();
  });
});
```

## 프로젝트 구조

```
project/
├── public/             # 정적 파일 (파비콘, 로봇 등)
├── src/
│   ├── assets/         # 이미지, 폰트 등
│   ├── styles/         # CSS/SCSS 파일
│   │   ├── base/       # 기본 스타일 (리셋, 타이포그래피 등)
│   │   ├── components/ # 컴포넌트별 스타일
│   │   ├── utils/      # 믹스인, 함수, 변수
│   │   └── main.scss   # 메인 스타일 파일
│   ├── js/
│   │   ├── components/ # UI 컴포넌트
│   │   ├── services/   # 외부 서비스 통신
│   │   ├── utils/      # 유틸리티 함수
│   │   └── main.js     # 메인 JS 파일
│   ├── templates/      # HTML 템플릿 (필요시)
│   └── index.html
├── .eslintrc.js        # ESLint 설정
├── .stylelintrc.js     # StyleLint 설정
├── package.json
└── README.md
``` 