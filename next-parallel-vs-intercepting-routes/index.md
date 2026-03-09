# Parallel Routes + Intercepting Routes

## 개념

같은 URL이지만 **진입 경로에 따라 다른 UI를 보여주는 패턴.**

- 피드에서 사진 클릭 → 모달로 열림 (URL: `/photo/123`)
- 해당 URL 직접 접근 → 전체 페이지로 열림 (URL: `/photo/123`)

## 파일 구조

```
app/
  layout.tsx
  page.tsx                    ← 피드 페이지
  @modal/                     ← Parallel Route (모달 슬롯)
    (.)photo/
      [id]/
        page.tsx              ← 모달로 intercept
  photo/
    [id]/
      page.tsx                ← 직접 접근 시 전체 페이지
```

## 구현

```tsx
// app/layout.tsx — modal 슬롯을 받아서 렌더
export default function Layout({
  children,
  modal,
}: {
  children: React.ReactNode;
  modal: React.ReactNode;
}) {
  return (


        {children}
        {modal}  {/* 평소엔 null, intercept 시 모달 렌더 */}


  );
}
```

```tsx
// app/@modal/(.)photo/[id]/page.tsx — 모달 UI
export default function PhotoModal({ params }: { params: { id: string } }) {
  return;
}
```

## Intercepting Routes 경로 규칙

| 표기    | 의미                 |
| ------- | -------------------- |
| `(.)`   | 같은 레벨 intercept  |
| `(..)`  | 한 레벨 위 intercept |
| `(...)` | 루트에서 intercept   |

## 데모 주제

갤러리 → 사진 모달 (인스타그램 스타일)
