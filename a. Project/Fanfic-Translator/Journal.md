팬픽 번역기를 만들어보자..

앱이 아니라 웹앱으로 진행하는 게 더 쉽다고 하니 웹앱으로 만들어보자.

일단 Node.js를 깔았다.
그다음 터미널에 다음과 같이 입력해서 프로젝트를 본격적으로 시작해보자.

#### 프로젝트 만들기
```bash
npx create-next-app fanfic-translator
cd fanfic-translator
```

#### 서버 실행하기
```bash
npm run dev
```

그럼 다음과 같이 화면이 뜬다.
![](../../z.%20Docs/img/Pasted%20image%2020250214132600.png)

그런 다음 브라우저에 ``http://localhost:3000``를 치면
![](../../z.%20Docs/img/Pasted%20image%2020250214132708.png)
오.. 뭔가 떴다.

보니까 `src/app/page.tsx`를 수정하여 시작하라는 것 같다.
ChatGPT가 알려준 다음 코드로 `function Home()`을 대체해보자.
```tsx
export default function Home() {
  const title: string = "웹 팬픽 번역기!";
  return (
    <div>
      <h1>{title}</h1>
      <input type="text" placeholder="팬픽 URL 입력" />
      <button>번역 시작</button>
    </div>
  );
}
```
웹페이지로 들어가보니 디자인이 아주 단순한 조잡한 디자인의 URL 박스와 변역 시작 박스가 생성되었다.
CSS를 만져서 디자인을 좀 더 상향시켜 보자.

#### TSX란?
TypeScript + JSX의 조합이다.
간단히 말해, TypeScript 문법을 사용하면서 React 컴포넌트를 만들 수 있는 파일 형식이다.
현재 가장 많이 사용되고 있는 트렌드라고 하는 모양이다.
JavaScript 문법에 타입만 추가한 것이므로 천천히 익숙해지면 된다.

다음과 같이 코드를 수정하였다.
뭔지는 모르고 ChatGPT가 시키는 대로 할 뿐이다. `...`
```jsx
"use client";

import { useState } from 'react';

export default function Home() {
  // 팬픽 URL 상태 관리
  const [url, setUrl] = useState<string>(''); 

  // URL 입력 핸들러 (타입 정의 추가)
  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    setUrl(event.target.value);
  };

  // 번역 시작 핸들러 (타입 정의 추가)
  const handleTranslate = () => {
    if (url) {
      alert(`번역할 URL: ${url}`);
    } else {
      alert('URL을 입력해주세요!');
    }
  };

  return (
    <div className="min-h-screen flex flex-col items-center justify-center bg-gray-100">
      <h1 className="text-4xl font-bold mb-4">웹 팬픽 번역기 MVP</h1>
      <input 
        className="border p-2 mb-4" 
        type="text" 
        placeholder="팬픽 URL 입력" 
        value={url}
        onChange={handleChange}
      />
      <button 
        className="bg-blue-500 text-white p-2 rounded hover:bg-blue-600 transition"
        onClick={handleTranslate}
      >
        번역 시작
      </button>
    </div>
  );
}

```

![](../../z.%20Docs/img/Pasted%20image%2020250215201859.png)
이제 대충 ~~많이 허접한~~ 인터페이스는 만들었으니 본격적으로 번역을 하는 기능을 추가해보자.

OpenAI 계정을 만들고 거기서 API Key를 발급 받으란다..
```
보안상 생략
```

발급 받았다.
옵션은 뭔지 모르겠어서 그냥 다 Default로 했다.

API Key는 코드에 직접 넣으면 보안에 위험하니, 프로젝트 추트에 `.env.local` 파일을 만들어 따로 관리한다.
```env
NEXT_PUBLIC_OPENAI_API_KEY=발급받은_API_Key
```
다음과 같이 코드를 추가하였다.
이제 `NEXT_PUBLIC_`으로 시작하면 클라이언트에서 접근 가능하다. `아직 뭔 소린지 모르겠다`

Next.js 서버를 재시작 해보자..

OpenAI API 호출을 위해 **axios**라는 것이 필요한 모양이다.
터미널에서 다음과 같이 설치해보자.
```bash
npm install axios
```