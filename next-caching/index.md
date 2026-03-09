# Next.js 캐싱 4단계

Next.js는 캐싱이 4개 레이어로 구성된다. 이걸 설명할 수 있으면 확실히 다르다.

```
요청
 │
 ▼
① Request Memoization      — 같은 요청 URL, 렌더링 1회 내 중복 제거 (자동)
 │
 ▼
② Data Cache               — fetch() 결과를 서버에 영구 저장 (revalidate로 제어)
 │
 ▼
③ Full Route Cache         — 빌드 시 정적 페이지를 서버에 저장
 │
 ▼
④ Router Cache             — 클라이언트 브라우저의 방문한 라우트 캐시
```

## 캐시 무효화

```tsx
// Server Action 안에서 특정 경로 또는 태그 무효화
import { revalidatePath, revalidateTag } from 'next/cache';

async function updateUser(formData: FormData) {
  'use server';
  await db.user.update(...);

  revalidatePath('/dashboard');        // 특정 경로 무효화
  revalidateTag('user-profile');       // 태그 기반 무효화
}

// fetch에 태그 붙이기
const data = await fetch('/api/user', {
  next: { tags: ['user-profile'] }
});
```
