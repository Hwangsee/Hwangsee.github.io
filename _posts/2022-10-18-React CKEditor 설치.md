---
title: 221018 TIL | React CKEditor 설치
tags: [React, Library, TIL]
---

# CKEditor4와 CKEditor5 라이브러리 설치 방법

> 개요.  
> CKEditor5를 설치하였으나 모듈에서 엑셀 붙여넣기에 인라인 CSS를 적용하는 기능을 미지원하여 CKEditor4로 다운그레이드 설치를 하게 되었다.


## CKEditor5 설치 
> 리액트 컴포넌트 생성 방법 문서  
> [React component - CKEditor 5 Documentation](https://ckeditor.com/docs/ckeditor5/latest/installation/getting-started/frameworks/react.html)

### (1) CKEditor5 Classic 추가

`yarn add @ckeditor/ckeditor5-react @ckeditor/ckeditor5-build-classic`

### (2) CKEditor5 any 타입 지정

- CKEditor5에서 typescript 미지원
- 루트 폴더에 해당 모듈을 any 타입으로 지정

```tsx
// ./types/ckedtior/index.d.ts
declare module '@ckeditor/ckeditor5-build-classic';
declare module '@ckeditor/ckeditor5-react';
```

### (3) 리액트 컴포넌트로 불러오기

```tsx
import React from 'react';
import { CKEditor } from '@ckeditor/ckeditor5-react';
import ClassicEditor from '@ckeditor/ckeditor5-build-classic';

function CKEditorTemplate():JSX.Element {
    console.log(`editor:${ClassicEditor}`);
    return (
        <CKEditor
            editor={ClassicEditor}
            data="<p>Hello from CKEditor 5!</p>"
            onReady={(editor:any) => {
                console.log( 'Editor is ready to use!', editor );
            }}
            onChange={(event: any, editor: any) => {
                const data = editor.getData();
                console.log({ event, editor, data });
            }}
            onBlur={(event: any, editor: any) => {
                console.log('Blur.', editor);
            }}
            onFocus={(event: any, editor: any) => {
                console.log('Focus.', editor);
            }}
        />
    );
};

export default CKEditorTemplate;
```

### (4) 엑셀 붙여넣기 기능 확인 중 (CK Editor5 지원안함)

엑셀 붙여넣기 관련 이슈

[https://github.com/ckeditor/ckeditor5/issues/2513](https://github.com/ckeditor/ckeditor5/issues/2513)

- 엑셀 붙여넣기 표 합치기는 지원하니 css 적용이 안 됨

[Paste from Word - CKEditor 5 Documentation](https://ckeditor.com/docs/ckeditor5/latest/features/pasting/paste-from-word.html)

- 워드에서 붙여넣는 기능은 있음, 단 패키지 라이브러리 설치 필요

---

## CKEditor4 라이브러리 설치

### (1) Editor 설치

`yarn add ckeditor4-react`

### (2) 에디터 컴포넌트 생성

```tsx
// ../ApprovalFormEditContentDialog

import React from 'react';
import { CKEditor } from 'ckeditor4-react';

const CKEditor4 = React.forwardRef((props, ref: any) => {
  return <CKEditor initData="Hello from CKEditor 4!" />;
});

export default CKEditor4;
```

```tsx
// component/editor/CKEditor4

<div className="edit-content">
		<CKEditor4 />
</div>
```

### CKEditor Component API

### config 설정

[Class Config (CKEDITOR.config) - CKEditor 4 API docs](https://ckeditor.com/docs/ckeditor4/latest/api/CKEDITOR_config.html#cfg-height)

### 에디터에 본문 내용 불러오기

- config에 `allowedContent=true` 설정 시 본문 내에 모든 태그 사용 가능

### 본문 내용 가져오기
```tsx
onChange={(event) => {
    console.log(`CKEditor4 loaded data : `, event.editor.getData());
}}
```
- change 이벤트 발생 시 콘솔 창에 본문 출력되도록 함.
