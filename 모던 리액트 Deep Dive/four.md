# 서버 사이드 렌터링

이번편에서는 Next.js 가 어떤 식으로 동작하는지, 또 고려해야 할 것은 무엇인지 더 쉽게 이해할 수 있을 것 입니다.

## Next.js 톺아보기

### Next.js 란?

Vercel 미국 스타트업에서 만든 풀스택 웹 애플리케이션을 구축하기 위한 리액트 기반 프레임워크 입니다.
Next.js 가 대세가 되기 전에 페이스북 팀에서 리액트 기반 사이드 렌더링을 위해 고려했던 프로젝트가 react-page 입니다.

react-page 는 페이지를 서버 또는 클라이언트에서 리액트를 손쉽게 사용할 수 있는 것을 목표로 만들어진 프로젝트.
react-page 내부에 개발해 사용되고 있는 react-page-middlewares 를 보면 실제로 서버에서 렌더링이 가능하도록 코드를 작성해 둔 것을 볼 수 있습니다.

이 프로젝트를 영감을 받아 Next.js 개발된 것으로 볼 수 있음.

리액트 기반 서버 사이드 프로젝트를 한다면 Next.js 를 선택하는 것이 가장 좋다.

- 다른 프레임워크에 비해 사용자도 많고,
- 모기업인 Vercel 에서 지원을 해줍니다.

### Next.js 시작하기

리액트 애플리케이션을 빠르게 만들 수 있는 create-react-app 유사하게 Next.js 는 create-next-app 제공해 개발자가 빠르게 Next.js 기반 프로젝트를 할 수 있습니다.

설치를 하면 next.config.js 라는 파일이 있습니다.
Next.js 프로젝트 환경 설정을 담당합니다.

```javascript
/** @type {import('next').NextConfig} **/
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
};

module.exports = nextConfig;
```

@type 으로 시작하는 주석은 자바스크립트 파일에 타입스크립트 타입 도움을 받기 위해 추가된 코드입니다.

#### pages/\_app.tsx

src에 있거나 혹은 프로젝트 루트에 있어도 동일 동작
\_app.tsx 그리고 내부에 있는 default export 로 내부낸 함수는 애플리케이션의 전체 페이지의 시작점입니다.
페이지의 시작점이라는 특징 떄문에 웹 애플리케이션에서 설정해야 하는 것들을 여기에서 실행할 수 있습니다.

\_app.tsx 에서 할 수 있는 내용들

- 에러 바운더리를 사용해 애플리케이션 전역에서 발생하는 에러 처리
- reset.css 같은 전역 CSS 선언
- 모든 페이지에 공통으로 사용 또는 제공해야 하는 데이터 제공 등

#### pages/\_document.tsx

create-next-app 으로 생성했다면, 해당 페이지가 존재하지 않을 수 있습니다.
\_document.tsx가 없어도 실행에 지장이 없는 파일임을 의미.

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

\_app.tsx 가 애플리케이션 전체를 초기화 하는 곳이라면, \_document.tsx 는 애플리케이션의 HTML을 초기화하는 곳 입니다.
\_app.tsx 에서는 몇 가지 차이점이 있습니다.

- <html>, <body> 에 DOM 속성을 추가하고 싶다면, _document.tsx 를 사용해야 합니다.
- \_app.tsx 렌더링이나 라우팅에 따라 서버나 클라이언트에서 실행될 수 있지만, \_document 는 무조건 서버에서 실행이 됩니다.
- getServerSideProps, getStaticProps 등 서버에서 사용 가능한 데이터 불러오기 함수는 여기에서 사용할 수 없습니다.

#### pages/\_error.tsx

create-next-app 으로 기본적으로 설치해주는 것은 아니며, 없더라도 실행하는 데 지장은 없습니다.

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

클라이언트, 서버에서 에러가 발생하면 500 처리 목적으로 만들어졌습니다. -> Next.js 프로젝트에서 에러를 적절하게 처리하고 싶다면, 이 페이지를 활용하면 됩니다.
단, 개발모드에서 이 페이지에 방문할 수 없고, 에러가 발생하면 Next.js 가 제공하는 개발자 에러 팝업이 나타나게 됩니다.

#### pages/404.tsx

pages/404.tsx 는 이름에서도 알 수 있듯이, 404 페이지를 정의한 것 입니다.

```javascript
export default function My404Page() {
  return <h1>페이지를 찾을 수 없습니다.</h1>;
}
```

#### pages/500.tsx

서버에서 발생하는 에러를 핸들링하는 페이지입니다. \_error.tsx 와 500.tsx 가 모두 있다면 500.tsx 가 우선적으로 실행됩니다.

```javascript
export default function My500Page() {
    return <h1>서버에서 에러가 발생했습니다.<h1/>
}
```

#### pages/index.tsx

이제부터는 개발자가 자유롭게 명칭을 정의할 수 있는 페이지입니다.
라우팅이 파일명으로 이어지는 구조가 바로 react-pages 에서 처음 만들어졌습니다.
Next.js 에서 현재까지 이어지고 있음.

#### 서버 라우팅과 클라이언트 라우팅의 차이

Next.js 는 서버 사이드도 수행하지만, 동시에 싱글 페이지 애플리케이션에서 같이 클라이언트 라우팅도 같이 수행됩니다.
Next.js 서버 사이드 렌더링을 비롯한 사전 렌더링을 지원하기 떄문에 최초 페이지 렌더링이 서버에서 수행된다는 것을 알 수 있습니다.

먼저 Next.js 서버 사이드 렌더링을 비롯한 사전 렌더링을 지원하기 떄문에, 최초 페이지 렌더링이 서버에서 수행된다는 것을 알 수 있을 것이다.
의심스러우면 페이지에 있는 루트 컴포넌트에서 console.log 사용 해 볼 수 있습니다.

```javascript
// pages/hello.tsx
export default function Hello() {
  console.log(typeof window === "undefined" ? "서버" : "클라이언트");
  return <>Hello</>;
}

export const getServerSideProps = () => {
  return {
    props: {},
  };
};
```

window 가 undefined 이기 떄문에 `서버`라는 문자열이 기록될 것 입니다.

#### <Link>

<Link> 와 <a> 가 무슨차이가 있을까요?

<a>로 이동해야 하는 경우에는, 네트워크에는 hello 라는 이름의 문서를 요청하고 있으며, 이후에는 webpack, framework, main, hello 등 페이지를 만드는 데 필요한 모든 리소스를 처음부터 다 가져오는 것을 알 수 있습니다.
또한 렌더링이 어디에서 일어났는지 판단하기 위한 console.log 도 서버와 클라이언트에 각각 동시에 기록되는 것을 알 수 있습니다.
즉, 서버에서 렌더링을 수행하고, 클라이언트에서 hydrate 하는 과정에서 한 번 더 실행됐다는 것을 알 수 있습니다.

next/link 으로 이동하는 경우 서버 사이드 렌더링이 아닌, 클라이언트에서 필요한 자바스크립트만 불러온 뒤 라우팅하는 클라이언트 라우팅/렌더링 방식으로 작동하는 것을 볼 수 있습니다.
-> 즉 Next.js 서버 사이드 장점과, 즉 사용자가 빠르게 볼 수 있는 최초 페이지를 제공해 주는 것과, 싱글 페이지 애플리케이션의 장점인 자연스러운 라우팅 두 가지 장점을 모두 살리기 위해 이러한 방식으로 작동합니다.

#### 페이지에서 getServerSideProps 를 제거하면 어떻게 될까?

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

#### /pages/api/hello.ts

/pages 하단에 api라고 작성된 디렉토리가 보이는데, 이름에서 예상할 수 있는 것처럼 서버의 API를 정의하는 폴더입니다.
기존 디렉터리에 따른 라우팅 구조는 페이지와 동일하되, /pages/api가 /api라는 접두사가 붙는다는 점만 다릅니다.

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

#### Data Fetching

Next.js 에서 서버 사이드 렌더링 지원을 위한 몇 가지 데이터를 불러오기 전략이 있습니다.
이를 Next.js 에서는 Data Fetching 이라고 합니다.

이 함수는 pages/ 폴더에 있는 라우팅이 되는 파일에서만 사용 가능하며,
예약어로 지정되어 반드시 정해진 함수명으로 export를 사용해 함수를 파일 외부로 내보내야 합니다.

이를 활용하면 서버에서 미리 필요한 페이지를 만들어서 제공하거나 해당 페이지에 요청이 있을 떄마다 서버에서 데이터를 조회해서 미리 페이지를 만들어서 제공할 수 있습니다.

#### getStaticPaths와 getStaticProps

이 두 함수는 어떠한 페이지를 CMS(Contents Management System)나 블로그, 게시판과 같이 사용자와 관계없이 정적으로 결정된 페이지를 보여주고자 할 떄 사용되는 함수입니다.

getStaticProps, getStaticPaths 는 반드시 함께 있어야 사용할 수 있습니다.
예를 들어, /pages/post[id] 와 같은 페이지가 있고, 해당 페이지에 다음과 같이 두 함수를 사용했다고 가정 해 봅시다.

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

getStaticPaths는 /pages/post/[id] 가 접근 가능한 주소를 정의하는 함수입니다.
예제에서는 paths 를 배열로 반환하는 것을 볼 수 있습니다.
여기에 params를 키로 하는 함수에 적절 값을 배열로 넘겨주면 해당 페이지에서 접근 가능한 페이지를 정의할 수 있습니다.
이 페이지는 /post/1, /post/2 만 접근 가능함을 의미하며, 이외의 페이지, 예를 들어 /post/3 이나, /post/foo 등은 404를 반환합니다.

getStaticProps는 앞에서 정의한 페이지를 기준으로 해당 페이지로 요청이 왔을 떄 제공할 props를 반환하는 함수 입니다.
예제에서 id가 각각 1과 2로 제한 돼 있기 떄문에 fetchPost(1), fetchPost(2)를 기준으로 각각 함수의 응답 결과를 변수로 가져와 props의 { post }로 반환하게 됩니다. -> 사용자가 접근할 수 있는 페이지를 모조리 빌드해 두고 배포하면 사용자는 굳이 페이지가 렌더링되는 것을 기다릴 필요 없이 이미 완성돼 있는 페이지를 받기만 하면 되므로 굉장히 빠르게 해당 페이지를 확인할 수 있습니다.

사용자가 접근할 수 있는 페이지를 모조리 빌드해 두고 배포하면 사용자는 굳이 페이지가 렌더링되는 것을 기다릴 필요 없이 이미 완성돼 있는 페이지를 받기만 하면 되므로 굉장히 빠르게 해당 페이지를 확인할 수 있습니다.

getStaticPaths 함수의 반환값 중 하나인 fallback 옵션은 이렇게 미리 빌드해야 할 페이지가 너무 많은 경우에만 가능합니다.
paths에 미리 빌드해 둘 몇 개의 페이지만 리스트로 반환하고, true나 "blocking"으로 값을 선언할수 있습니다.

이렇게 하면 next build를 실행할 떄 미리 반환해 둔 paths에 기재돼 있는 페이지만 앞서 마찬가지로 미리 빌드하고, 나머지 페이지의 경우에는 다음과 같이 작동합니다.

```javascript
function Post({ post }: { post: Post }) {
  const router = useRouter();
  if (router.isFallback) {
    return <div>isLoading...</div>;
  }
}
```

getStaticPaths와 getStaticProps는 정적 데이터만 제공하면 되는 사이트, 예를 들어 브로그 글이나 약관 같이 단순 콘텐츠를 빠르게 제공해야 한다면 유용하게 사용 가능합니다.

#### getServerSideProps

앞서 두 함수가 정적 페이지를 제공을 위해 사용됐다면, getServerSideProps는 서버에서 실행되는 함수이며, 해당 함수가 있다면 무조건 페이지 진입 전에 이 함수를 실행해야 합니다.
이 함수는 응답값에 따라 페이지의 루트 컴포넌트에 props를 반환할 수도, 혹은 다른 페이지로 리다이렉트시킬 수도 잇습니다.
이 함수가 있으면 Next.js 에서 서버에서 실행해야 하는 페이지로 분류 해 빌드 시에도 서버용 자바스크립트 파일을 별도로 만들게 됩니다.

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

context.query.id 를 사용하면 /post/[id] 와 같은 경로에 있는 id 값을 접근할 수 있습니다.
이 값을 이용해 props를 제공하면 페이지의 Post 컴포넌트에 해당 값을 제공 해 이 값을 기준으로 렌더링을 수행할 수 있습니다.

```javascript
<!DOCTYPE html>
<html>
  <body>
    <div id="__next" data-reactroot="">
      <h1>안녕하세요<h1>
      <p>반갑습니다.</p>
    </div>
    <!-- 생략 -->
    <script id="__NEXT_DATA__" type="application/json">
      {
        "props": {
          "pageProps": {
            "post": { "title": "안녕하세요", "content": "반갑습니다" }
          },
          "__N_SSP": true
        },
        "page": "post/[id]",
        "query": { "id": "1" },
        "buildId": "development",
        "isFallback": false,
        "gsap": true,
        "scriptLoader": []
      }
    </script>
  </body>
</html>
```

HTML이 getServerSideProps의 반환 값을 기반으로 페이지가 렌더링돼 있음을 알 수 있습니다.
Next.js의 서버 사이드 렌더링은 getServerSideProps의 실행과 함께 이뤄지며, 이 정보를 기반으로 페이지를 렌더링하는 과정이 바로 서버 사이드 렌더리을 나타내는 것임을 알 수 있습니다.

getServerSideProps의 정보인 props뿐만 아니라 현재 페이지 정보, query 등 Next.js 구동에 필요한 다양한 정보들이 담겨 있습니다.
왜 이러한 정보들이 삽입되어 있을까요?

1. 서버에서 fetch 등으로 렌더링에 필요한 정보를 가져옵니다.
2. 1번에서 가져온 정보를 기반으로 HTML을 완성합니다.
3. 2번의 정보를 클라이언트에 제공합니다.
4. 3번의 정보를 바탕으로 클라이언트에서 hydrate 작업을 합니다. 이 작업은 DOM에 리액트 라이프사이클과 이벤트 핸들러를 추가하는 작업입니다.
5. 4번 작업인 hydrate 만든 리액트 컴포넌트 트리와 서버에서 만든 HTML 이 다르다면 불일치 에러가 뱉습니다.
6. 5번 작업도 1번과 마찬가지로 fetch 등을 이용해 정보를 가져와야 합니다.

즉, 1번과 6번 작업 사이에 fetch 시점에 따라 결과물의 불일치가 발생할 수 있으므로, 1번에서 가져온 정보를 결과물인 HTML에 script 형태로 내려주는 것 입니다.
이 작업을 거치면 1번의 작업을 6번에서 반복하지 않아도 되어 불필요한 요청을 막을 수 있을뿐더러 시점 차이로 인한 결과물의 차이도 막을 수 있습니다.

getServerSideProps 은 무조건 클라이언트가 아닌 서버에서만 실행된다는 사실 또한 염두에 두어야 합니다.

- window.document 와 같은 브라우저에서만 접근할 수 있는 개체에는 접근할 수 없습니다.
- API 호출 시 /api/some/path 와 같이 protocol 과 domain 없이 fetch 요청을 할 수 없음.
- 여기에서 에러가 발생한다면 500.tsx 와 같은 미리 정의해둔 에러 페이지로 리다이렉트 합니다.

또한 이 함수는 사용자가 매 페이지를 호출할 떄 마다 실행되고, 이 실행이 끝나기 전까지는 사용자에게 어떠한 HTML도 보여줄 수 없습니다.
꼭 최초에 보여줘야 하는 데이터가 아니라면 `getServerSideProps` 보다는 클라이언트에서 호출하는 것이 더 유리합니다.
`getServerSideProps` 반드시 해당 페이지를 렌더링하는 데 있어 중요한 역할을 하는 데이터만 가져오는 것이 좋습니다.

`getServerSideProps` 에서 어떤 조건에 따라 다른 페이지로 보내고 싶다면 `redirect`를 사용할 수 있습니다.

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

post 를 조회하는 데 실패했다면, /404 페지로 바로 보내도록 할 수 있습니다.
이 경우 클라이언트에 리다이렉트하는 것에 비해서 휠씬 더 자연스럽습니다.

클라이언트에서는 아무리 리다이렉트를 초기화해도 자바스크립트가 어느 정도 로딩된 이후에 실행할 수 밖에 없습니다.
하지만 getServerSideProps 를 사용하면 조건에 따라 사용자에게 미처 해당 페이지를 보여주기도 이전에 원하는 페이지로 보내버릴 수 있어 사용자에게 훨씬 다 자연스럽게 보여줄 수 있습니다.

#### getInitialProps

getInitialProps 는 getStaticProps 나 getServerSideProps 가 나오기 전에 사용할 수 있었던 유일한 페이지 데이터 불러오기 수단입니다.
대부분 경우 getStaticProps 나 getServerSideProps 를 사용하는 것을 권장하며, getInitialProps는 굉장히 제한적인 예시에서만 사용됩니다.

### 스타일 적용하기

Next.js 에서는 다양한 방식의 스타일을 대부분 지원 해 줍니다.

#### 전역 스타일

CSS Reset 불리는, 브라우저 기본으로 제공되고 있는 스타일을 초기화하는 등 애플리케이션 전체에 공통으로 적용하고 싶은 스타일이 있다면 \_app.tsx를 활용하면 된다. \_app.tsx 에 필요한 스타일을 직접 import로 불러오면 애플리케이션 전체에 영향을 미칠 수 있습니다.

```javascript
import type { AppProps } from "next/app";
import "../styles.css";
import "normalize.css/normalize.css";

export default function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />;
}
```

이러한 글로벌 스타일은 다른 페이지나 컴포넌트와 충돌할 수 있으므로 반드시 \_app.tsx에서만 제한적으로 작성해야 한다.

#### 컴포넌트 레벨 CSS

Next.js에서는 컴포넌트 레벨의 CSS를 추가할 수 있음.
[name].module.css와 같은 명명 규칙만 준수하면 되며, 이 컴포넌트 레벨 CSS는 다른 컴포넌트의 클래스명과 겹쳐서 스타일에 충돌이 일어나지 않도록 고유한 클래스명을 제공합니다.

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

```html
<head>
  <!-- 생략 -->
  <!-- 실제 프러덕션 빌드 시에는 스타일 태그가 아닌 별도 CSS 파일을 생성합니다. -->
  <style>
    .Button.alert__62TGU {
      color: red;
      font-size: 16px;
    }
  </style>
</head>
<button type="button" class="Button_alert_62TGU">경고!</button>
```

button에 alert 클래스를 선언해 스타일을 입혔지만, 실제 클래스는 Button_alert\_\_62TGU와 같이 조금 다른 것을 볼 수 있는데,
언급한 컴포넌트별 스타일 충돌을 방지하기 위한 Next.js의 최적화가 잘 작동하고 있는 것임을 확인할 수 있습니다.

#### SCSS와 SASS

sass와 scss도 css를 사용할 떄와 마찬가지로 동일 방식으로 사용합니다.

```javascript
$primary: blue;

:export {
  primary: $primary;
}
import styles from './Button.module.scss';

export function Button() {
  return {
    /* styles.primary 형태로 꺼내올 수 있다. */
    <span style={{color: styles.primary }}>
      안녕하세요.
    </span>
  }
}
```

이 외에도 컴포넌트 레벨 CSS와 코드 작동 방식이 동일합니다.

#### CSS-in-JS

비록 CSS와 비교했을 떄 CSS-in-JS가 코드 작성의 편의성 이외에 실제로 성능 이점을 가지고 있는지는 논쟁거리로 남아있지만,
CSS 구문이 자바스크립트 내부에 있다는 것은 확실히 프론트엔드 개발자에게 직관적이고 편리하게 느껴질 것 이다.

대표적인 라이브러리가 CSS-in-JS -> styles-jsx, styles-component, Emotion 등 여러가지가 있음.
Next.js 에서 style-component을 적용하려면 여러가지 셋팅이 필요합니다.

```javascript
return (
  <Html lang="ko">
    <Head />
    <body>
      <Main />
      <NextScript />
    </body>
  </Html>
);

MyDocument.getInitialProps = async (
  ctx: DocumentContext
): Promise<DocumentInitialProps> => {
  const sheet = new ServerStyleSheet();
  const originalRenderPage = ctx.renderPage;

  console.log(sheet);

  try {
    ctx.renderPage = () =>
      originalRenderPage({
        enhanceApp: (App) => (props) => sheet.collectStyles(<App {...props} />),
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

\_document.tsx 가 앞서 문서를 초기화하기 위한 Next.js의 특별한 페이지라고 했는데,
여기에 서버와 클라이언트 모두에서 작동하는 getInitialProps 를 사용해 무언가 처리하는 것 같다 하나씩 보자.

1. ServerStyleSheet는 style-component의 스타일을 서버에서 초기화해 사용되는 클래스다. 이 클래스를 인스턴스로 초기화하면 서버에서 style-components가 작동하기 위한 다양한 기능을 가지고 있습니다.

2. originalRenderPage는 ctx.renderPage를 담아두고 있다. 즉 기존의 ctx.renderPage가 하는 작업에 추가적으로 styled-components 관련 작업을 하기 위해 별도 변수로 분리했습니다.

3. ctx.renderPage에는 기존에 해야 하는 작업과 함께 enhanceApp, 즉 App을 렌더링할 떄 추가로 수행하고 싶은 작업을 정의했습니다.

4. const initialProps = await Document.getInitialProps(ctx)는 기존 \_document.tsx가 렌더링을 수행할 떄 필요한 getInitialProps를 생성하는 작업을 합니다.

5. 마지막 반환 문구에서 기존에 기본적으로 내려주는 props에 추가적으로 styled-components가 모아둔 자바스크립트 파일 내 스타일을 반환합니다.

내용이 조금 복잡하지만, 이렇게 CSS-in-JS의 스타일을 적용하려면 이 과정을 거쳐야 합니다.
만약에 이런 과정을 거치지 않는다면, 스타일이 브라우저에서 뒤늦게 추가되어 FOUC라는, 스타일이 입혀지지 않은 날것의 HTML을 잠시간 사용자에게 노출하게 된다.

#### \_app.tsx 응용하기

app.tsx 에서 사용자가 처음 서비스에 접근했을 떄 하고 싶은 무언가를 여기에서 처리할 수 있습니다.

#### next.config.js 살펴보기

next.config.js 는 Next.js 실행에 필요한 설정을 추가할 수 있는 파일입니다.
Next.js 실행과 사용자화에 필요한 다양한 설정을 추가할 수 있어서, 어떠한 설정이 직접 소스코드를 통해 확인 해 보는 것이 좋습니다.
