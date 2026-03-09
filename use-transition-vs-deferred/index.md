# useTransition vs useDeferredValue

둘 다 React 동시성(Concurrent) 기능이지만 **적용 대상이 다르다.**

|              | `useTransition`                    | `useDeferredValue`           |
| ------------ | ---------------------------------- | ---------------------------- |
| 대상         | 내가 직접 트리거하는 상태 업데이트 | 외부에서 오는 값             |
| 사용 시점    | 버튼 클릭, 라우트 이동 등          | props나 상위 state를 받을 때 |
| pending 상태 | `isPending` 제공                   | 없음 (이전 값을 그대로 유지) |

### useTransition

```tsx
// Server Component 네비게이션의 pending 상태를 볼 수 있다
const [isPending, startTransition] = useTransition();

<button
  onClick={() => startTransition(() => router.push('/dashboard'))}
  disabled={isPending}
>
  {isPending ? '로딩 중...' : '이동'}

```

> **핵심:** `router.push()` 같은 Server Component fetching도 `useTransition`으로 감싸면 isPending을 통해 pending 상태를 알 수 있다.

### useDeferredValue

```tsx
// 부모에서 받은 query가 빠르게 바뀌어도, 무거운 필터링은 여유있게
function SearchResults({ query }: { query: string }) {
  const deferredQuery = useDeferredValue(query);
  const results = expensiveFilter(deferredQuery); // 이전 값으로 먼저 렌더

  return {results.map(...)};
}
```

## 데모 주제

실시간 검색 필터 (리스트 100개 이상)
