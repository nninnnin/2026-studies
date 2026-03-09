# `revalidatePath` vs `revalidateTag`

```typescript
// Server Action 안에서
revalidatePath("/dashboard");
// 또는 태그 기반
revalidateTag("user-data");
```

이걸 모르면 mutation 후 stale 데이터를 보게 됨

**Parallel Routes + Intercepting Routes**

````
app/
  @modal/
    (.)photo/[id]/  ← 같은 페이지에서 모달로 intercept
    ```
````
