---
title: '프론트엔드 성능 최적화 완벽 가이드'
description: '웹 성능 지표(Core Web Vitals)부터 이미지 최적화, 번들 최적화, 크리티컬 렌더링 패스까지. 프론트엔드 성능 최적화에 필요한 모든 것을 정리했습니다.'
pubDate: '2026-02-16'
heroImage: '../../assets/blog-placeholder-4.jpg'
--- 

프론트엔드 성능 최적화 강의를 들으며 정리한 내용입니다. 웹 성능의 주요 지표부터 실전 최적화 기법까지 다룹니다.

---

## 1. 웹 성능의 두 가지 축

웹 성능은 크게 **로딩 성능**과 **렌더링 성능**으로 나뉩니다.

| 구분 | 설명 | 관련 요소 |
|------|------|-----------|
| **로딩 성능** | 리소스를 다운로드하는 속도 | 네트워크, 서버 응답 시간, 파일 크기 |
| **렌더링 성능** | 다운로드한 리소스를 화면에 그리는 속도 | DOM 처리, CSS 계산, JavaScript 실행 |

---

## 2. 주요 성능 지표

### FP (First Paint)
화면에 **무언가 하나라도 처음 그려지는 시점**입니다. 배경색이 바뀌는 것만으로도 측정됩니다. 사용자에게 "페이지가 로딩 중"이라는 시각적 신호를 제공하는 데 의미가 있습니다.

### FCP (First Contentful Paint)
텍스트, 이미지 등 **실제 콘텐츠가 처음 렌더링되는 시점**입니다. 다만 "의미 있는 콘텐츠"의 기준이 모호하여 참고 지표로 활용합니다.

### ⭐ LCP (Largest Contentful Paint)
뷰포트에서 **가장 큰 콘텐츠 요소가 렌더링되는 시점**입니다. 보통 히어로 이미지, 대형 텍스트 블록이 해당합니다. Core Web Vitals 핵심 지표입니다.

```
좋음: 2.5s 이하
개선 필요: 2.5s ~ 4.0s
나쁨: 4.0s 초과
```

> **참고:** Skeleton UI를 사용하는 경우, Skeleton 자체가 LCP로 측정될 수 있어 정확한 측정이 어려울 수 있습니다. Skeleton이 실제 콘텐츠로 교체되는 시점이 진짜 LCP이지만, 브라우저는 가장 큰 요소의 렌더링 시점으로 판단합니다.

### ⭐ CLS (Cumulative Layout Shift)
페이지 로딩 중 **레이아웃이 얼마나 밀리는지** 측정합니다. 예를 들어, 이미지가 늦게 로딩되면서 텍스트가 아래로 밀리는 현상이 대표적입니다.

```
좋음: 0.1 이하
개선 필요: 0.1 ~ 0.25
나쁨: 0.25 초과
```

### FID (First Input Delay) → INP로 대체됨
사용자의 **첫 번째 인터랙션에 대한 응답 지연 시간**입니다. 2024년 3월부터 INP로 대체되었습니다.

### ⭐ INP (Interaction to Next Paint)
FID를 대체한 지표로, **모든 인터랙션의 응답성**을 측정합니다.

| 비교 | FID | INP |
|------|-----|-----|
| 측정 범위 | 최초 1회 인터랙션 | 모든 인터랙션 |
| 측정 구간 | 입력 지연(Input Delay)만 | 입력 지연 + 처리 시간 + 다음 Paint까지 |

```
좋음: 200ms 이하
개선 필요: 200ms ~ 500ms
나쁨: 500ms 초과
```

> **출처:** [web.dev - INP](https://web.dev/articles/inp)

### 기타 지표
- **TTI (Time to Interactive):** 페이지가 완전히 상호작용 가능해지는 시점
- **TBT (Total Blocking Time):** 메인 스레드가 블로킹되는 총 시간
- **SI (Speed Index):** 콘텐츠가 점진적으로 표시되는 속도 (한 번에 팍 뜨는지 vs 점진적으로 뜨는지)

### Core Web Vitals

Google이 정의한 핵심 사용자 경험 지표입니다:

| 지표 | 측정 대상 |
|------|-----------|
| **LCP** | 로딩 성능 (Loading) |
| **INP** | 상호작용성 (Interactivity) |
| **CLS** | 시각적 안정성 (Visual Stability) |

> **출처:** [web.dev - Core Web Vitals](https://web.dev/articles/vitals)

---

## 3. 성능 측정 방법

### Lighthouse

Chrome DevTools에 내장된 성능 측정 도구입니다. 세 가지 측정 모드가 있습니다:

| 모드 | 설명 | 사용 시점 |
|------|------|-----------|
| **Navigation** | 페이지 로딩 전체 과정 측정 | 초기 로딩 성능 확인 |
| **Timespan** | 특정 시간 구간 측정 | 사용자 인터랙션 성능 확인 |
| **Snapshot** | 현재 상태 스냅샷 | 접근성, SEO 등 정적 분석 |

**Diagnostics** 섹션에서 구체적인 성능 개선 가이드를 제공합니다.

#### ⚠️ Dev 환경에서 Lighthouse 사용 시 주의

개발 환경에서는 JS minify, gzip 압축, 번들링이 적용되지 않은 상태입니다. 따라서 다음 항목들은 **dev 환경에서 무시**해도 됩니다:

- Reduce JS execution time
- Enable text compression
- Minify JS
- Reduce unused JS

**정확한 측정을 위해서는 `npm run build && npm run preview`로 프로덕션 빌드를 확인하세요.**

### Chrome Performance 탭

병목 지점과 메모리 누수를 찾을 수 있습니다.

**병목 지점 찾기:**
1. Performance 탭 열기 (F12 → Performance)
2. 녹화 시작 후 인터랙션 수행
3. Frames 섹션 확인: 🟢 초록색 = 정상, 🟡 노란색 = Frame Drop

**메모리 누수 확인:**
1. Performance 탭에서 **Memory** 체크박스 활성화
2. 녹화 후 JS Heap 그래프 확인
3. Heap이 지속적으로 상승하면 메모리 누수 의심

### React Profiler (React DevTools)

React 앱에서는 React DevTools의 **Profiler** 탭에서 각 컴포넌트의 렌더링 원인과 소요 시간을 확인할 수 있습니다. 설정에서 "Why did this render?" 옵션을 활성화하면 리렌더링 원인을 직접 추적할 수 있습니다.

---

## 4. Throttle과 Debounce

검색창에서 입력할 때마다 API를 호출하면 불필요한 네트워크 요청이 폭증합니다. **Debounce**로 해결할 수 있습니다.

### Throttle
일정 시간 간격(delay)마다 **최대 1회 실행**합니다.

```javascript
function throttle(fn, delay) {
  let lastCall = 0;
  return (...args) => {
    const now = Date.now();
    if (now - lastCall >= delay) {
      lastCall = now;
      fn(...args);
    }
  };
}

// 사용 예: 스크롤 이벤트
window.addEventListener('scroll', throttle(() => {
  console.log('스크롤 위치:', window.scrollY);
}, 200));
```

**적합한 상황:** scroll 이벤트 처리, window resize 감지, mouse move 추적

### Debounce
마지막 호출 후 delay만큼 **대기한 뒤 1회 실행**합니다.

```javascript
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

// 사용 예: 검색어 자동완성
const searchInput = document.getElementById('search');
searchInput.addEventListener('input', debounce((e) => {
  fetchSearchResults(e.target.value);
}, 300));
```

**적합한 상황:** 검색어 자동완성, 폼 유효성 검사, 윈도우 리사이즈 최종 처리

### 한눈에 비교

| | Throttle | Debounce |
|---|----------|----------|
| 실행 시점 | 일정 간격마다 | 마지막 이벤트 후 대기 |
| 실행 보장 | 주기적 실행 보장 | 연속 입력 시 마지막만 실행 |
| 대표 사용처 | 스크롤, 마우스 이동 | 검색, 폼 입력 |

---

## 5. 이미지 최적화

**성능 최적화에서 가장 쉽고 효과가 큰 방법**이 이미지 최적화입니다.

### WebP 포맷 사용

WebP는 JPEG 대비 약 25~35% 용량이 작으면서 품질은 유사합니다.

**변환 도구:** [Squoosh](https://squoosh.app/) — Google에서 만든 무료 이미지 압축 도구

**브라우저 호환성 대응 (`<picture>` 태그):**
```html
<picture>
  <source srcset="image.webp" type="image/webp" />
  <source srcset="image.jpg" type="image/jpeg" />
  <img src="image.jpg" alt="설명" />
</picture>
```
WebP를 지원하지 않는 브라우저에서는 자동으로 JPEG로 폴백됩니다.

### 반응형 이미지 (srcset)

사용자의 화면 크기와 DPR(Device Pixel Ratio)에 따라 적절한 이미지를 제공합니다.

```html
<img
  srcset="image-420w.jpg 420w,
          image-768w.jpg 768w,
          image-1024w.jpg 1024w"
  sizes="(max-width: 420px) 420px,
         (max-width: 768px) 768px,
         1024px"
  src="image-1024w.jpg"
  alt="반응형 이미지"
/>
```

**DPR(Device Pixel Ratio)이란?**
- 일반 모니터: 1x (논리 1px = 물리 1px)
- Retina 디스플레이: 2x (논리 1px = 물리 2px)
- 고밀도 모바일: 3x (논리 1px = 물리 3px)

따라서 Retina에서 선명한 이미지를 보여주려면 **렌더링 크기 × DPR 배수**의 이미지가 필요합니다.

> **참고:** 브라우저는 화면이 커지면 더 큰 이미지를 가져오지만, 이미 큰 이미지를 로드한 상태에서 화면이 작아지면 다시 작은 이미지를 가져오지 않습니다. 이미 캐시된 큰 이미지가 충분하기 때문입니다.

> **출처:** [MDN - srcset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/srcset)

### Progressive JPEG

일반 JPEG는 위에서 아래로 순차적으로 로딩되지만, Progressive JPEG는 **전체 이미지가 흐릿하게 먼저 보이고 점차 선명해집니다.** 체감 로딩 속도가 빨라지는 효과가 있습니다.

---

## 6. 스크롤 성능 최적화

### Performance 탭에서 스크롤 분석

1. Performance 탭에서 녹화 시작
2. 스크롤 인터랙션 수행 후 녹화 중지
3. **Frames** 섹션 확인:
   - 🟢 초록색: 정상 프레임
   - 🟡 노란색: Frame Drop 발생

Frame Drop의 주요 원인은 **메인 스레드가 바빠서** 원하는 타이밍에 화면을 그리지 못하는 것입니다.

---

## 7. 애니메이션 최적화

### Reflow를 피하라

```css
/* ❌ 나쁜 예: width 변경 → Reflow 발생 */
.box {
  transition: width 0.3s;
}
.box:hover {
  width: 200px;
}

/* ✅ 좋은 예: transform 사용 → Reflow/Repaint 생략 */
.box {
  transition: transform 0.3s;
}
.box:hover {
  transform: scaleX(1.5);
}
```

### requestAnimationFrame

JavaScript로 애니메이션을 구현할 때는 `setInterval` 대신 `requestAnimationFrame`을 사용합니다:

```javascript
// ❌ 나쁜 예
setInterval(() => {
  element.style.transform = `translateX(${x++}px)`;
}, 16);

// ✅ 좋은 예
function animate() {
  element.style.transform = `translateX(${x++}px)`;
  requestAnimationFrame(animate);
}
requestAnimationFrame(animate);
```

`requestAnimationFrame`은 **브라우저의 렌더링 주기(보통 60fps)에 맞춰 콜백을 실행**하므로, Frame Drop 없이 부드러운 애니메이션을 구현할 수 있습니다.

> **출처:** [MDN - requestAnimationFrame](https://developer.mozilla.org/ko/docs/Web/API/Window/requestAnimationFrame)

---

## 8. 불필요한 렌더링 제거 (React)

### 문제: useEffect 남용

```javascript
// ❌ 나쁜 예: 동기적으로 계산 가능한 값을 useEffect로 처리
const [users, setUsers] = useState([]);
const [filtered, setFiltered] = useState([]);

useEffect(() => {
  setFiltered(users.filter(u => u.active));
}, [users]);
// → 렌더링 2번 발생: users 변경 → 리렌더링 → filtered 변경 → 다시 리렌더링

// ✅ 좋은 예: useMemo로 직접 계산
const [users, setUsers] = useState([]);
const filtered = useMemo(() =>
  users.filter(u => u.active), [users]
);
// → 렌더링 1번으로 충분
```

### key를 활용한 컴포넌트 초기화

`initState`가 prop으로 전달되어 변경될 때 useEffect를 사용하면 2번 렌더링됩니다:

```jsx
// ❌ 나쁜 예
function Form({ initValue }) {
  const [value, setValue] = useState(initValue);
  useEffect(() => {
    setValue(initValue);
  }, [initValue]);
  return <input value={value} onChange={e => setValue(e.target.value)} />;
}

// ✅ 좋은 예: key를 변경하여 컴포넌트 자체를 재생성
<Form key={selectedId} initValue={initialData} />
```

key가 변경되면 React가 컴포넌트를 완전히 새로 마운트하므로, useEffect 없이도 초기값이 올바르게 설정됩니다.

> **출처:** [React 공식 문서 - You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)

---

## 9. Table Windowing (Virtualization)

수천 개의 행이 있는 테이블을 한 번에 렌더링하면 성능이 급격히 저하됩니다. **Virtualization**은 뷰포트에 보이는 행만 렌더링하고, 스크롤 시 동적으로 교체합니다.

```
전체 데이터: 10,000행
실제 DOM에 렌더링: 약 20~30행 (화면에 보이는 만큼만)
```

**대표 라이브러리:**
- [TanStack Virtual](https://tanstack.com/virtual/latest) — 프레임워크 독립적, 가볍고 유연
- [react-window](https://github.com/bvaughn/react-window) — React 전용, react-virtualized의 경량화 버전

---

## 10. React 동시성 (Concurrent Features)

React 18에서 도입된 동시성 기능은 **DOM을 그리는 중간에 더 중요한 이벤트가 끼어들 수 있게** 해줍니다.

### useTransition

긴급하지 않은 상태 업데이트를 저우선순위로 처리합니다:

```jsx
function SearchResults() {
  const [query, setQuery] = useState('');
  const [isPending, startTransition] = useTransition();

  function handleChange(e) {
    // 입력값 업데이트는 즉시 (긴급)
    setQuery(e.target.value);

    // 검색 결과 필터링은 저우선순위
    startTransition(() => {
      setFilteredResults(filterData(e.target.value));
    });
  }

  return (
    <>
      <input value={query} onChange={handleChange} />
      {isPending ? <Spinner /> : <ResultList />}
    </>
  );
}
```

### useDeferredValue

값의 업데이트를 지연시켜 UI 응답성을 유지합니다:

```jsx
function SearchList({ query }) {
  const deferredQuery = useDeferredValue(query);

  // query는 즉시 반영, deferredQuery는 여유가 있을 때 반영
  const results = useMemo(() =>
    filterData(deferredQuery), [deferredQuery]
  );

  return <ul>{results.map(item => <li key={item.id}>{item.name}</li>)}</ul>;
}
```

> **출처:** [React 공식 문서 - useTransition](https://react.dev/reference/react/useTransition)

---

## 11. requestIdleCallback

브라우저가 **유휴 상태(idle)**일 때 콜백을 실행합니다. 우선순위가 낮은 작업(분석 데이터 전송, 프리로딩 등)에 적합합니다.

```javascript
// 브라우저가 한가할 때 실행
requestIdleCallback((deadline) => {
  // deadline.timeRemaining(): 남은 유휴 시간 (ms)
  while (deadline.timeRemaining() > 0) {
    doLowPriorityWork();
  }
});
```

> **출처:** [MDN - requestIdleCallback](https://developer.mozilla.org/ko/docs/Web/API/Window/requestIdleCallback)

---

## 12. Tree Shaking

사용하지 않는 코드를 번들에서 제거하는 기법입니다.

### lodash 주의

```javascript
// ❌ 나쁜 예: lodash 전체를 가져옴 (약 70KB gzipped)
import _ from 'lodash';
_.debounce(fn, 300);

// ✅ 좋은 예: 필요한 함수만 가져옴
import debounce from 'lodash/debounce';
debounce(fn, 300);

// ✅ 또는: lodash-es 사용 (ESM 지원 → 자동 Tree Shaking)
import { debounce } from 'lodash-es';
```

**핵심 원리:** **ESM(ES Modules)** 형식으로 작성된 라이브러리는 Webpack/Vite 등의 번들러가 자동으로 Tree Shaking합니다. CommonJS(`require`)로 작성된 라이브러리는 Tree Shaking이 어렵습니다.

---

## 13. 번들 최적화

### 번들 크기 분석

**[vite-bundle-analyzer](https://github.com/KusStar/vite-bundle-visualizer)** 를 사용하면 번들 구성을 시각적으로 확인할 수 있습니다.

```bash
npm install -D vite-bundle-visualizer
```

```javascript
// vite.config.js
import { visualizer } from 'vite-bundle-visualizer';

export default {
  plugins: [visualizer({ open: true })],
};
```

`npm run build` 후 자동으로 번들 리포트가 열립니다. Gzip 적용 후 크기도 확인 가능합니다.

### 불필요한 라이브러리 발견 시

1. **package.json 확인:** 직접 설치한 적 없는 라이브러리 확인
2. **package-lock.json 확인:** 어떤 패키지의 의존성인지 추적
3. **기회비용 판단:** 라이브러리의 번들 크기 vs 제공하는 기능의 가치

### Code Splitting + Lazy Loading

큰 라이브러리를 사용하는 컴포넌트를 분리 로딩합니다:

```jsx
import { lazy, Suspense } from 'react';

// Three.js를 사용하는 3D 뷰어를 Lazy Loading
const ThreeDViewer = lazy(() => import('./ThreeDViewer'));

function App() {
  return (
    <Suspense fallback={<div>로딩 중...</div>}>
      <ThreeDViewer />
    </Suspense>
  );
}
```

> **Suspense를 사용하는 이유:** Lazy Loading된 컴포넌트가 로딩 중일 때 보여줄 폴백 UI를 지정하기 위해 필요합니다. Suspense 없이 lazy를 사용하면 에러가 발생합니다.

#### ⚠️ Lazy Loading 주의사항

- **과도한 Lazy Loading은 역효과:** 모든 컴포넌트를 lazy하면 렌더링 후 체이닝이 발생하여 오히려 느려질 수 있습니다.
- **주요 컴포넌트는 즉시 로딩:** 초기 화면에 필요한 핵심 컴포넌트는 lazy하지 않습니다.
- **페이지 단위로 판단:** Coverage Tab(CT)을 활용해 페이지별로 사용률을 확인하고 결정합니다.

#### Lazy Loading의 단점과 해결: Component Preloading

```javascript
// Lazy Loading의 단점:
// 클릭 → 컴포넌트 로드 → 스크립트 평가 → 렌더링 (느림)

// 해결: 미리 Preload
const LazyComponent = lazy(() => import('./HeavyComponent'));

// hover 시 미리 로드
function handleMouseEnter() {
  import('./HeavyComponent'); // 미리 다운로드 + 평가
}

// 또는 requestIdleCallback으로 브라우저가 한가할 때 미리 로드
requestIdleCallback(() => {
  import('./HeavyComponent');
});
```

---

## 14. Script 로딩 전략: async vs defer

HTML 파싱 중 `<script>` 태그를 만나면 **파싱이 중단(blocking)**됩니다. 스크립트가 `document.write()` 등으로 HTML을 변경할 수 있기 때문입니다.

```html
<!-- 기본: HTML 파싱 중단 → 스크립트 다운로드 → 실행 → 파싱 재개 -->
<script src="app.js"></script>

<!-- async: 다운로드는 병렬, 완료 즉시 실행 (실행 순서 보장 안 됨) -->
<script async src="analytics.js"></script>

<!-- defer: 다운로드는 병렬, HTML 파싱 완료 후 실행 (순서 보장) -->
<script defer src="app.js"></script>
```

| 속성 | 다운로드 | 실행 시점 | 순서 보장 |
|------|----------|-----------|-----------|
| 없음 | 파싱 중단 후 | 즉시 | ✅ |
| `async` | 병렬 | 다운로드 완료 즉시 | ❌ |
| `defer` | 병렬 | HTML 파싱 완료 후 | ✅ |

#### defer 사용 시 주의

외부 라이브러리(예: 카카오맵 SDK)에 `defer`를 적용할 때, 해당 라이브러리 내부에서 `document.write()`를 사용하는 경우 문제가 발생합니다. `defer`된 스크립트에서 `document.write()`를 호출하면 브라우저가 이를 무시하거나 에러를 발생시킵니다. 이런 경우 해당 라이브러리의 코드를 직접 가져와서 `document.write()` 없이 동작하도록 수정이 필요합니다.

---

## 15. 웹 폰트 최적화

### 문제: FOUT과 FOIT

| 현상 | 설명 |
|------|------|
| **FOUT** (Flash of Unstyled Text) | 기본 폰트가 먼저 보이다가 웹폰트로 교체되면서 깜빡임 |
| **FOIT** (Flash of Invisible Text) | 웹폰트가 로드될 때까지 텍스트가 안 보임 |

### 해결 1: font-display 속성

```css
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/MyFont.woff2') format('woff2');
  font-display: fallback; /* 추천 */
}
```

| 값 | 동작 |
|----|------|
| `auto` | 브라우저 기본 동작 |
| `block` | 최대 3초간 텍스트 미표시 (FOIT) |
| `swap` | 즉시 기본 폰트 표시, 이후 교체 (FOUT) |
| `fallback` | 100ms 대기 후 기본 폰트 표시, 3초 내 로드 안 되면 기본 폰트 유지 |
| `optional` | 100ms 내 로드되면 적용, 아니면 기본 폰트 유지 |

> **참고:** `fallback`을 사용해도 깜빡임이 보이는 것은 100ms 대기 시간 내에 로드되지 않아 기본 폰트 → 웹폰트로 전환되기 때문입니다.

### 해결 2: 폰트 파일 크기 줄이기

**① WOFF2 포맷 사용**
```css
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/MyFont.woff2') format('woff2'),
       url('/fonts/MyFont.woff') format('woff'); /* 폴백 */
}
```

변환 도구: [Transfonter](https://transfonter.org/)

**② Local Font 우선 사용**
```css
@font-face {
  font-family: 'MyFont';
  src: local('MyFont'),  /* 사용자 PC에 설치된 폰트 우선 사용 */
       url('/fonts/MyFont.woff2') format('woff2');
}
```

**③ Subset 폰트**

한글 폰트의 용량이 큰 이유는 한글 모든 글자(11,172자)를 포함하기 때문입니다. 실제로 사용하는 글자만 추출하면 용량을 크게 줄일 수 있습니다.

**④ Unicode Range**
```css
/* 한글 서브셋 */
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/MyFont-korean.woff2') format('woff2');
  unicode-range: U+AC00-D7AF; /* 한글 음절 범위 */
}

/* 영문 서브셋 */
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/MyFont-latin.woff2') format('woff2');
  unicode-range: U+0020-007E; /* 기본 라틴 문자 */
}
```

`unicode-range`를 지정하면 **해당 범위의 글자가 페이지에 사용될 때만** 폰트 파일을 다운로드합니다.

**⑤ Data URI 변환**

4KB 이하의 작은 폰트 파일은 Vite가 자동으로 Base64로 인라인합니다. 별도의 네트워크 요청 없이 CSS에 포함됩니다.

### 해결 3: 폰트 Preloading

```html
<link
  rel="preload"
  href="/fonts/MyFont.woff2"
  as="font"
  type="font/woff2"
  crossorigin
/>
```

HTML을 파싱하는 시점에 폰트를 미리 다운로드하여 FOUT/FOIT를 최소화합니다.

Vite 프로젝트에서는 **[unplugin-inject-preload](https://github.com/AiHiPUnmworworker/unplugin-inject-preload)** 플러그인을 사용해 빌드 시 자동으로 preload 태그를 삽입할 수 있습니다.

> **참고:** 이 플러그인은 dev 모드에서는 동작하지 않습니다. `npm run preview`로 확인하세요.

---

## 16. 크리티컬 렌더링 패스 (Critical Rendering Path)

### 렌더링 파이프라인

```
DOM → CSSOM → Render Tree → Layout → Paint → Composite
                                                  ↑
                                            (GPU 처리)
```

| 단계 | 역할 |
|------|------|
| **DOM** | HTML을 파싱하여 DOM 트리 생성 |
| **CSSOM** | CSS를 파싱하여 스타일 트리 생성 |
| **Render Tree** | DOM + CSSOM을 결합하여 렌더 트리 생성 |
| **Layout** | 각 요소의 위치와 크기 계산 |
| **Paint** | 색상, 그림자 등 시각적 스타일 채우기 |
| **Composite** | 각 레이어를 합성하여 최종 화면 표시 (GPU 사용) |

### 속성 변경에 따른 렌더링 비용

```
width, height, margin, padding 변경
 → Reflow (Layout부터 다시) → 비용 큼

color, background-color, box-shadow 변경
 → Repaint (Layout 생략, Paint부터) → 비용 중간

transform, opacity, filter 변경
 → Composite만 (Layout + Paint 생략) → 비용 적음 ✅
```

> **참고:** Performance 탭에서 초록색 커밋은 Composite 단계에 해당합니다. parseHTML은 진한 파란색으로 표시됩니다.

### Jank 현상

보통 디스플레이는 **초당 60프레임(60fps)**을 렌더링합니다. 즉, 각 프레임은 약 **16.67ms** 안에 처리되어야 합니다. 렌더링 파이프라인이 16ms를 넘기면 프레임이 드랍되어 **버벅거림(Jank)**이 발생합니다.

---

## 17. CLS 개선: Layout Shift 방지

### 이미지 영역 사전 예약

이미지가 로딩되기 전에 영역을 확보하여 레이아웃 밀림을 방지합니다:

```html
<!-- 접근성 가이드라인 권장: img에 width/height 명시 -->
<img src="banner.jpg" width="1200" height="400" alt="배너" />
```

```css
/* 반응형 대응: 컨테이너에 고정 높이 또는 aspect-ratio 사용 */
.banner-container {
  width: 100%;
  aspect-ratio: 3 / 1;
  overflow: hidden;
}

.banner-container img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

---

## 18. LCP 개선: Preload 활용

### 문제: 리소스 체이닝

```
체이닝 발생 시:
HTML → JS → JSON → Image (순차 로딩, 느림)

Preload 적용 시:
HTML → JS / JSON / Image (병렬 로딩, 빠름)
```

### 해결: `<link rel="preload">`

```html
<head>
  <!-- 핵심 이미지를 HTML 파싱 시점에 미리 로드 -->
  <link rel="preload" href="/banner.jpg" as="image" />

  <!-- 핵심 스크립트 preload -->
  <link rel="modulepreload" href="/src/main.js" />
</head>
```

> **preload vs modulepreload:**
> - `preload`: 리소스를 미리 다운로드
> - `modulepreload`: ES 모듈을 미리 다운로드 **+** 파싱/컴파일까지 수행

> **출처:** [MDN - rel="preload"](https://developer.mozilla.org/ko/docs/Web/HTML/Reference/Attributes/rel/preload)

### Vite 프로젝트에서 Preload 자동화

**unplugin-inject-preload** 플러그인을 사용하면 빌드 시 자동으로 preload 태그를 삽입합니다. `npm run preview`에서 프로덕션 빌드 결과를 확인할 수 있습니다.

> **주의:** `index.js`와 같은 메인 번들과 함께 불러오는 모듈을 modulepreload하면 오히려 요청이 중복될 수 있습니다. 메인 번들과 별도로 로딩되는 모듈에 적용하는 것이 효과적입니다.

---

## 19. CDN (Content Delivery Network)

### CDN이란?

사용자와 **물리적으로 가까운 서버**에서 콘텐츠를 제공하여 응답 시간을 단축하는 기술입니다.

```
서울 사용자 → 서울 CDN 엣지 서버 → 빠른 응답 ✅
서울 사용자 → 미국 원본 서버 → 느린 응답 ❌
```

### Image CDN

이미지 처리에 특화된 CDN으로, 원본 이미지를 서버에서 자동으로 변환/최적화하여 전달합니다.

```
원본 이미지 (JPG/PNG)
    ↓
Image CDN (리사이즈, WebP 변환, 압축)
    ↓
최적화된 이미지 → 사용자
```

URL 파라미터로 이미지 변환을 요청할 수 있습니다:

```
https://cdn.example.com/image.jpg?w=800&h=600&format=webp&quality=80
```

**대표 서비스:** Cloudinary, imgix, Cloudflare Images

> **예시:** Medium 블로그의 이미지 URL을 보면 CDN을 통해 다양한 크기로 자동 변환됩니다.

---

## 📚 참고 자료

- [web.dev - Core Web Vitals](https://web.dev/articles/vitals)
- [web.dev - INP](https://web.dev/articles/inp)
- [MDN - srcset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/srcset)
- [MDN - requestAnimationFrame](https://developer.mozilla.org/ko/docs/Web/API/Window/requestAnimationFrame)
- [MDN - requestIdleCallback](https://developer.mozilla.org/ko/docs/Web/API/Window/requestIdleCallback)
- [MDN - rel="preload"](https://developer.mozilla.org/ko/docs/Web/HTML/Reference/Attributes/rel/preload)
- [React 공식 문서 - You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
- [React 공식 문서 - useTransition](https://react.dev/reference/react/useTransition)
- [Squoosh - 이미지 압축](https://squoosh.app/)
- [Transfonter - 폰트 변환](https://transfonter.org/)
