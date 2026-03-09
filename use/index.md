# use() hook — Client에서 Promise unwrap

```tsx
import { use } from 'react';

// 서버에서 Promise를 만들어서 내려줌
export default function Page() {
  const dataPromise = fetchData(); // async 함수 (await 안 함)
  return (
    }>


  );
}

// Client Component에서 use()로 unwrap
'use client';
function ClientComponent({ promise }: { promise: Promise }) {
  const data = use(promise); // Suspense와 함께 동작
  return {data.title};
}
```
