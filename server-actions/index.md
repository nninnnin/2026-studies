# Server Actions 패턴

## 학습

### form과 직접 연결

```tsx
// app/actions.ts
"use server";

import { z } from "zod";
import { revalidatePath } from "next/cache";

const schema = z.object({
  title: z.string().min(1),
});

export async function createPost(formData: FormData) {
  const parsed = schema.safeParse({
    title: formData.get("title"),
  });

  if (!parsed.success) {
    return { error: parsed.error.flatten() };
  }

  await db.post.create({ data: parsed.data });
  revalidatePath("/posts");
}
```

```tsx
// app/new-post/page.tsx
import { createPost } from "../actions";

export default function NewPost() {
  return 작성;
}
```

### useFormState로 에러 처리

```tsx
'use client';
import { useFormState } from 'react-dom';
import { createPost } from '../actions';

export default function NewPostForm() {
  const [state, action] = useFormState(createPost, null);

  return (


      {state?.error && {state.error.title}}
      작성

  );
}
```

## 데모 주제

게시글 작성 폼 (유효성 검사 + revalidate)
