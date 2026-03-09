# use-transition

## 질문: startTransition은 어떻게 이 코드에서 isPending 타이밍을 인식하는걸까?

```typescript
startTransition(() => {
  router.push(`?${params.toString()}`);
});
```

## 단계별로 파보기

### Step 1. startTransition 호출 직후

startTransition(() => {
router.push("?year=2022")
})
startTransition의 콜백은 동기적으로 즉시 실행돼요. router.push가 호출되고 콜백은 바로 끝나요. 이 시점에서 isPending = true가 되고, 아직 아무것도 안 바뀐 상태예요.

### Step 2. Next.js가 fetch 시작

router.push 내부에서 Next.js는 서버에 RSC payload를 요청해요.

GET /?year=2022 (RSC 전용 헤더 포함)
← 서버가 새로운 컴포넌트 트리를 직렬화해서 응답
이 fetch는 비동기라서 기다리는 동안 브라우저는 멈추지 않아요.

### Step 3. React는 어떻게 기다리나?

여기가 핵심이에요. startTransition 콜백은 이미 끝났는데 왜 isPending이 여전히 true일까요?

Next.js의 router는 내부적으로 **React 캐시(cache)**에 fetch 결과를 저장해요. 그리고 그 캐시를 읽는 컴포넌트가 Suspense를 통해 "아직 준비 안 됐어요"라는 신호를 React에게 보내요.

React: "transition 중에 Suspense가 발생했네"
→ 아직 끝난 게 아니야
→ isPending = true 유지
React는 transition 중에 발생한 Suspense를 transition이 아직 진행 중인 신호로 해석해요.

### Step 4. RSC payload 도착

서버 응답이 오면 Next.js는 이걸 React 트리에 적용해야 해요. 이때 Next.js는 반드시 startTransition 안에서 상태를 업데이트해요.

// Next.js 내부 (실제 코드는 더 복잡하지만 개념적으로)
startTransition(() => {
dispatch({ type: 'NAVIGATE', tree: newRSCTree })
})
이게 중요한 이유는, 우리가 바깥에서 호출한 startTransition과 같은 transition으로 합쳐지기 때문이에요. React는 하나의 연속된 transition으로 인식해요.

### Step 5. 렌더링 완료 → isPending = false

새 트리가 렌더링되고, 모든 Suspense가 해소되면 React가 화면에 커밋해요.

새 트리 렌더링 완료
→ Suspense 없음 (데이터 다 왔으니)
→ transition 종료
→ isPending = false
→ 리렌더 → 오버레이 사라짐
전체 흐름 요약

우리 startTransition 호출
→ isPending = true
→ router.push → fetch 시작
→ fetch 중: React 캐시 miss → Suspense → isPending 유지
→ fetch 완료: Next.js가 startTransition으로 새 트리 적용
→ 렌더링 완료, Suspense 해소
→ isPending = false
결국 isPending의 생명주기는 **"transition 중에 발생한 모든 Suspense가 해소될 때까지"**예요. Next.js는 fetch → 캐시 → Suspense의 연결고리를 통해 React의 transition 시스템 안에 자연스럽게 녹아들어요.
