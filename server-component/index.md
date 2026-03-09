# Server Component

### async/await 직접 사용

```tsx
// app/page.tsx — useEffect 없이 서버에서 바로 fetch
export default async function Page() {
  const data = await fetch("https://api.example.com/items", {
    next: { revalidate: 60 }, // 60초마다 재검증
  });
  const items = await data.json();

  return;
}
```

> **흔한 실수:** 여전히 Client Component에서 `useEffect + useState`로 fetching하는 패턴. Server Component에서 직접 fetch하면 waterfall도 없고 번들 크기도 줄어든다.

## References

- https://www.joshwcomeau.com/react/server-components/
- https://github.com/reactjs/server-components-demo
