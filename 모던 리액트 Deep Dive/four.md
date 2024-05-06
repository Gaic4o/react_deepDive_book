# 서버 사이드 렌터링

이번편에서는 Next.js 가 어떤 식으로 동작하는지, 또 고려해야 할 것은 무엇인지 더 쉽게 이해할 수 있을 것 입니다.

## Next.js 톺아보기

### Next.js 란?

Vercel 미국 스타트업에서 개발된 React 기반 프레임워크입니다.
이 프레임워크는 풀스택 웹 애플리케이션 구축을 위해 설계되었으며, 서버 사이드 렌더링(SSR), 정적 사이트 생성(SSG), 그리고 클라이언트 사이드 렌더링을 포괄적으로 지원합니다.

#### 배경

Next.js 개발 이전에 페이스북 팀은 React 페이지를 서버나 클라이언트에서 더 쉽게 사용할 수 있도록 하는 프로젝트인 `react-page`를 고려했습니다.
이 프로젝트는 서버에서의 렌더링을 용이하게 하는 미들웨어를 포함하고 있었습니다.
Next.js는 이와 같은 초기 아이디어에 영감을 받아 설계됐습니다.

#### Next.js의 장점

- 다재다능성 : 리액트 기반의 서버 사이드 프로젝트에 적합하며, 서버 렌더링과 정적 사이트 생성 모두를 지원합니다.
- 널리 사용됨 : 사용자 기반과 커뮤니티가 크며, 다른 리액트 SSR 프레임워크인 Remix나 Hydrgen에 비해 더 널리 사용됩니다.
- 강력한 지원 : 모기업인 Vercel의 지속적인 지원과 개발로 기능이 지속적으로 추가되고 있습니다.

#### Next.js 시작하기

Next.js는 Vercel에서 개발한 React 기반의 프레임워크로, `create-react-app`을 통해 프로젝트를 쉽게 시작할 수 있습니다.
이 도구는 필요한 설정 파일과 기본 페이지 템플릿을 자동으로 생성하여 개발자가 즉시 시작할 수 있도록 합니다.

#### 기본 설정 파일: next.config.js

`next.config.js`는 프로젝트 환경 설정을 관리하는 파일입니다.
이 파일을 통해 React의 엄격 모드 활성화, SWC 최소화 옵션 등을 최소화할 수 있습니다.

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
};
module.exports = nextConfig;
```

### 주요 페이지 구성

#### 1. pages/\_app.tsx

- 모든 페이지에 공통적으로 적용되는 설정을 구성하는 시작점입니다. 여기서 전역 CSS, 에러 바운더리 설정, 공통 데이터 제공 등을 설정할 수 있습니다.

#### 2. pages/\_document.sx

- 서버에서만 실행되고, 애플리케이션의 HTML을 초기화하는 역할을 합니다. `<html>`과 `<body>`에 추가 속성을 적용할 수 있습니다.

```javascript
import { Html, Head, Main, NextScript } from "next/document";
export default function Document() {
  return (
    <Html lang="ko">
      <Head />
      <body className="body">
        <Main />
        <NextScript />
      </body>
    </Html>
  );
}
```

#### 3. pages/\_error.tsx

- 서버 또는 클라이언트에서 발생한 에러를 처리합니다. 개발 모드에서는 Next.js의 에러 팝업이 표시됩니다.

```javascript
function Error({ statusCode }: { statusCode: number }) {
  return (
    <p>
      {statusCode ? `서버에서 ${statusCode}` : `클라이언트에서`} 에러가
      발생했습니다.
    </p>
  );
}

Error.getInitialProps = ({ res, err }: NextPageContext) => {
  const statusCode = res ? res.statusCode : err ? err.statusCode : "";
  return { statusCode };
};
export default Error;
```

#### 4. pages/404.tsx

- 사용자가 존재하지 않는 페이지에 접근했을 떄 표시되는 404 에러 페이지를 정의합니다.

#### 5. pages/500.tsx

- 서버에서 발생하는 에러를 처리하는 500 에러 페이지입니다. \_error.tsx와 함께 있을 경우, 500.tsx가 우선적으로 사용됩니다.

#### 6. pages/index.tsx

\_app.tsx, \_error.tsx, \_document.tsx, 404.tsx, 500.tsx 제공하는 예약어로 관리되는 페이지라면, 이제부터는 개발자가 자유롭게 명칭을 정의할 수 있는 페이지입니다.
라우팅이 파일명으로 이어지는 구조가 바로 react-pages 에서 처음 만들어졌습니다.
Next.js 에서 현재까지 이어지고 있음.

### 서버 라우팅과 클라이언트 라우팅의 차이

#### 서버 라우팅 (Server Routing):

- 초기 로드 시 : Next.js는 사용자가 최초 페이지 요청 시 서버에서 해당 페이지를 렌더링하고 전달합니다. 이를 통해 페이지 로드 시 빠르게 컨텐츠를 볼 수 있게 하여, SEO 최적화에 유리합니다.
- 사용 예시 : `getServerSideProps` 함수는 서버에서 실행되어, 페이지의 필요한 데이터를 사전에 불러온 후 props로 페이지 컴포넌트에 전달합니다. 서버에서 실행되므로 `window` 객체가 `undefined` 로 나타나게 됩니다.

#### 클라이언트 라우팅 (Client Routing):

- 네비게이션 시 : 사용자가 사이트 내에 다른 페이지로 이동할 떄, 클라이언트 라우팅은 페이지 전체를 새로 불러오지 않고 필요한 데이터만 불러와 뷰를 업데이트합니다. 이는 더 빠른 페이지 전환과 부드러운 사용자 경험을 제공해 줍니다.
- 사용 예시 : `<Link>` 컴포넌트를 사용하면 Next.js 는 페이지 전환 시 서버에 요청하지 않고 필요한 자바스크립트 및 데이터만을 클라이언트에서 가져와 처리합니다.

#### <Link>와 <a>의 차이

- `<a>` 태그는 전통적인 방식으로 페이지를 새로 불러오며, 이는 브라우저가 서버에 페이지 파일을 요청하는 과정을 포함합니다.
- `<Link>` 클라이언트 사이드 라우팅을 활용, Next.js는 필요한 자원만을 동적으로 불러와 빠르게 페이지를 전환합니다.

#### getServerSideProps의 역향

`getServerSideProps` 는 서버 사이드 렌더링의 핵심 요소, 이 함수를 사용하면 각 페이지 요청에 따라 서버에서 데이터를 불러와 렌더링할 수 있으며, 이를 통해 동적 데이터 처리가 가능합니다. 만약 이 함수를 제거하면 Next.js 는 해당 페이지를 정적 페이지로 처리하게 되며, 모든 사용자가 HTML을 제공하게 됩니다. 이는 서버 처리 부하를 줄일 수 있으나, 동적인 데이터 요구가 있는 페이지에는 적합하지 않을 수 있습니다.

#### /pages/api 디렉토리

Next.js에서 `/pages/api` 디렉토리는 API 엔드포인트를 구현하는 곳입니다.
이 디렉토리 내의 파일들은 서버 사이드에서만 실행되며, 클라이언트 사이드에서는 접근할 수 없습니다.
이를 통해 서버 로직을 분리하고, 필요한 API를 통해 데이터를 처리 및 제공할 수 있습니다.

#### 데이터 패칭 전략

Next.js는 다양한 데이터 페칭 전략을 제공합니다:

- `getStaticProps`: 빌드 시 데이터를 불러와 정적인 페이지를 생성합니다.
- `getServerSideProps`: 각 요청마다 서버에서 데이터를 불러와 페이지를 렌더링합니다.
- `getStaticPaths`: 동적 라우트를 사용하는 페이지의 경로를 미리 정의하고 정적으로 생성합니다.

#### 페이지에서 getServerSideProps 를 제거하면 어떻게 될까?

/pages/hello 예제에서 getServerSideProps 가 아무것도 하지 않고 있음에도 추가돼 있다는 것이 의아하게 느껴질 수 있다.
만약 이 예제 파일에서 해당 부분을 제거하면 어떻게 작동할까?

즉, /pages/api/hello.ts 는 /api/hello 로 호출할 수 있으며, 이 주소는 다른 pages 파일과 다르게 HTML 요청을 하는 게 아니라 단순히 서버 요청을 주고받게 된다. pages/api/hello.ts 를 살펴보자.

```javascript
import type { NextApiRequest, NextApiResponse } from "next";

type Data = {
  name: string
};

export default function handler(
    req: NextApiRequest,
    res: NextApiResponse<Data>,
) (
    res.status(200).json({ name: 'John Doe' })
)
```

프런트엔드 프로젝트를 만든다면 /api를 작성할 일이 거의 없지만, 서버에서 내려주는 데이터를 조합해 BFF(backend-for-frontend) 형태로 활용하거나, 완전한 풀스택 애플리케이션을 구축하고 싶을 떄, CORS(Cross-Origin Resource Sharing) 문제를 우회하기 위해 사용될 수 있습니다.

/pages/hello 예제에서 getServerSideProps 가 아무것도 하지 않고 있음에도 추가돼 있다는 것이 의아하게 느껴질 수 있다.
만약 이 예제 파일에서 해당 부분을 제거하면 어떻게 작동할까?

```javascript
// pages/hello.tsx
export default function Hello() {
  console.log(typeof window === "undefined" ? "서버" : "클라이언트");
  return <>hello</>;
}
```

빌드 한 뒤 실행해 보면, 어떠한 방식으로 접근해도 <a/>, <Link/> 에 상관없이 서버에 로그가 남지 않는 것을 확인할 수 있습니다.
그 이유는 빌드 결과물에 확인할 수 있습니다.

#### getServerSideProps가 있는 빌드

/hello에 getServerSideProps 가 있는 채로 빌드한 결과, 서버 사이드 런타임 체크가 되어 있는 것을 볼 수 있음.

#### getServerSideProps가 없는 빌드

/hello에 getServerSideProps 가 없는 채로 빌드한 결과, 빌드 크기도 약간 줄었으면 서버 사이드 렌더링이 필요없는 정적인 페이지로 분류된 것을 알 수 있습니다.

#### Data Fetching

Next.js 에서 서버 사이드 렌더링 지원을 위한 몇 가지 데이터를 불러오기 전략이 있습니다.
이를 Next.js 에서는 Data Fetching 이라고 합니다.

이 함수는 pages/ 폴더에 있는 라우팅이 되는 파일에서만 사용 가능하며,
예약어로 지정되어 반드시 정해진 함수명으로 export를 사용해 함수를 파일 외부로 내보내야 합니다.

이를 활용하면 서버에서 미리 필요한 페이지를 만들어서 제공하거나 해당 페이지에 요청이 있을 떄마다 서버에서 데이터를 조회해서 미리 페이지를 만들어서 제공할 수 있습니다.

### Next.js 데이터 페칭 방법: getStaticPaths, getStaticProps, getServerSideProps, getInitialProps

#### getStaticPaths와 getStaticProps

- `getStaticPaths`와 `getStaticProps` 는 정적 사이트 생성(SSG)을 위해 사용됩니다. 이 방법은 블로그, 게시판, CMS와 같은 정적 데이터를 기반으로 하는 사이드에 적합합니다.
  - getStaticPaths는 동적 라우팅을 사용하는 페이지에 접근 가능한 경로에 사전 정의합니다. 예를 들어 `/post[id]` 와 같은 경로가 있을 떄, 어떤 `id` 값들이 유효한지 사전에 지정 가능합니다. 이 함수를 통해 `/post/1`, `/post/2` 등 특정 ID에 대한 페이지만 생성 가능하고, 나머지는 404 페이지를 반환하거나 필요에 따라 실시간으로 생성 가능합니다.
  - getStaticProps는 각 미리 정의된 경로에 대해 필요한 데이터를 불러와 렌더링하는 데 사용됩니다. 이 함수는 `getStaticPaths` 에서 정의된 각 경로에 대해 실행되어, 각 페이지의 초기 프로퍼티를 설정합니다.

`fallback` 옵션을 통해 `getStaticPaths` 에서 정의되지 않은 경로의 요청 처리 방식을 설정 가능합니다.
`false`일 경우 정의되지 않는 경로는 모두 404를 반환하고, `true`또는 `blocking` 일 경우 요청 시 해당 페이지를 실시간으로 생성하여 제공합니다.

```javascript
import { GetStaticPaths, GetStaticProps } from "next";

export const getStaticPaths: GetStaticPaths = async () => {
    return {
        paths: [{ params: { id: '1' }, { params: { id: '2' }}}],
        fallback: false,
    }
}

export const getStaticProps: GetStaticProps = async ({ params }) => {
    const { id } = params

    const post = await fetchPost(id)

    return {
        props: { post },
    }
}

export default function Post({ post }: { post: Post }) {
    // post로 페이지를 렌더링합니다.
}
```

#### getServerSideProps

`getServerSideProps` 서버 사이드 렌더링 (SSG)을 위해 사용됩니다.
이 함수는 페이지 요청이 있을 때마다 실행되어 필요한 데이터를 불러오고, 이를 페이지 컴포넌트에 전달합니다.
이 방식은 사용자나 세션별로 동적 데이터가 필요할 떄 유용합니다.

`getServerSideProps` 는 요청마다 서버에서 실행되어 결과를 HTML형태로 클라이언트에 전달합니다. 따라 사용자 인터렉션에 따라 변경되는 데이터를 실시간으로 처리 가능합니다.
`redirect` 옵션을 통해 특정 조건에 따라 다른 페이지로 리다이렉션을 할 수 있습니다. 예를 들어, 요청된 포스트가 없을 경우 사용자를 404페이지로 안내 가능합니다.

```javascript
import type { GetServerSideProps } from "next";

export default function Post({ post }: { post: Post }) {
  // 렌더링
}

export const getServerSideProps: GetServerSideProps = async (context) => {
  const {
    query: { id = "" },
  } = context;
  const post = await fetchPost(id.toString());
  return {
    props: { post },
  };
};
```

```javascript
export const getServerSideProps: GetServerSideProps = async (context) => {
  const {
    query: { id = "" },
  } = context;
  const post = await fetchPost(id.toString());

  if (!post) {
    redirect: {
      destination: "/404";
    }
  }
  return {
    props: { post },
  };
};
```

#### getInitialProps

`getInitialProps`는 Next.js의 초기 버전에서 제공된 데이터 페칭 방법입니다.
현재는 `getStaticProps`나 `getServerSideProps` 사용을 권장하고 있으며, `getInitialProps` 는 특수한 경우에 한해 제한적으로 사용됩니다.
`getInitialProps`는 클라이언트 사이드와 서버 사이드 모두에서 실행될 수 있지만, 최신 함수들이 제공하는 최적화된 성능과 구분되는 빌드 전략을 제공하지는 않습니다.

#### 전역 스타일 적용하기

전역 스타일은 애플리케이션 전체에 공통적으로 적용되는 스타일입니다.
이를 적용하기 위해 `_app.tsx` 파일을 사용하며, 여기에 필요한 style sheet를 직접 import 할 수 있습니다.
예를 들어, CSS reset을 위한 `normalize.css`나 기타 전역 스타일 시트를 추가할 수 있습니다.

```javascript
// _app.tsx
import type { AppProps } from "next/app";
import "../styles.css";
import "normalize.css/normalize.css";

export default function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />;
}
```

#### 컴포넌트 레벨 CSS

컴포너트별로 독립적인 스타일을 적용하고 싶다면, CSS 모듈을 사용하는 것이 좋습니다.
CSS 모듈은 고유한 Class 이름을 자동으로 생성하여 스타일 충동을 방지합니다.

```css
/* button.module.css */
.alert {
  color: red;
  font-size: 16px;
}
```

```javascript
import styles from "./Button.module.css";

export function Button() {
  return (
    <button type="button" className={styles.alert}>
      경고!
    </button>
  );
}
```

#### SCSS와 SASS

SCSS와 SASS는 CSS의 확장 버전으로, 변수, 중첩, 믹스인 등 추가적인 기능을 제공합니다.
Next.js에서는 `.scss`, `.sass` 파일을 CSS 모듈과 같은 방식으로 사용 가능.

```css
/* styles.scss */
$primary: blue;

:export {
  primary: $primary;
}
```

```javascript
import styles from "./Button.module.scss";

export function Button() {
  return <span style={{ color: styles.primary }}>안녕하세요.</span>;
}
```

#### CSS-in-JS

CSS-in-JS 스타일을 Javascript로 작성하는 방법입니다.
Next.js는 여러 CSS-in-JS 라이브러리를 지원하지만 설정이 필요할 수 있습니다.
예를 들어, styled-components 를 사용할 떄는, `_document.tsx` 에 특정 설정을 추가하여 서버 사이드 렌더링 시 스타일을 적절히 적용하도록 해야 합니다.

```javascript
import { ServerStyleSheet } from "styled-components";

MyDocument.getInitialProps = async (ctx) => {
  const sheet = new ServerStyleSheet();
  const originalRenderPage = ctx.renderPage;

  try {
    ctx.renderPage = () =>
      originalRenderPage({
        enhanceApp: (App) => (props) => sheet.collectStyle(<App {...props} />),
      });

    const initialProps = await Document.getInitialProps(ctx);
    return {
      ...initialProps,
      styles: (
        <>
          {initialProps.styles}
          {sheet.getStyleElement()}
        </>
      ),
    };
  } finally {
    sheet.seal();
  }
};
```

이 설정은 서버 사이드에서 생성된 스타일을 클라이언트에도 적용할 수 있게 FOUC를 방지합니다.
