# 4. loading.tsx vs Suspense

|           | `loading.tsx`        | `<Suspense>`  |
| --------- | -------------------- | ------------- |
| 범위      | 라우트 전체          | 컴포넌트 단위 |
| 선언 위치 | 파일 시스템          | JSX 안        |
| 사용 시점 | 페이지 전체 스켈레톤 | 부분 로딩 UI  |

```
app/
  dashboard/
    loading.tsx     ← 대시보드 전체 로딩 UI
    page.tsx
```

```tsx
// page.tsx 안에서 Suspense로 세분화
export default function Dashboard() {
  return (

        {/* 즉시 렌더 */}
      }>
          {/* 차트만 따로 로딩 */}

      }>
          {/* 테이블은 또 따로 */}


  );
}
```

> **기준:** 페이지 전체가 데이터 로딩 전에 의미없으면 `loading.tsx`, 부분적으로 먼저 보여줄 수 있으면 `Suspense`로 세분화.
