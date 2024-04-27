## SSR, SSR, SSG, ISR의 Next에서의 사용법을 정리합니다.

### 목차

1. [CSR](#CSR)
2. [SSR](#SSR)
3. [SSG](#SSG)
4. [ISR](#ISR)

## CSR

CSR (Client Side Rendering)은 클라이언트측 렌더링이라고 부릅니다.
브라우저는 페이지를 만드는데 필요한 최소한의 HTML 페이지와 JavaScript를 다운로드 합니다. 그런 다음 JavaScript를 이용하여 DOM을 업데이트 하고 렌더링 합니다.

처음 페이지를 로드할 경우모든 JavaScript가 다운로드, 구문분석이 될 때까지 렌더링이 되지 않기 때문에 오래걸릴 수 있습니다. 하지만 이렇게 한 번 모든 데이터를 받아온 이후에는 필요한 데이터만 가져오면 되고 이미 다운로드한 JavaScript를 이용하여 모든 페이지를 새로 고칠 필요 없이 일부를 다시 렌더링 할 수 있으므로 동일한 웹 사이트의 다른 페이지로 이동하는 것이 빠릅니다.

NextJS에는 CSR을 구현하는 방법이 두 가지가 있습니다.

1. `useEffect()` hook을 사용하여 `getStaticProps()`나 `getServerSideProps()` 같은 메소드를 사용하지 않고 데이터를 받아와서 사용합니다.

```JavaScript
import React, { useState, useEffect } from 'react'

export function Page() {
  const [data, setData] = useState(null)

  useEffect(() => {
    const fetchData = async () => {
      const response = await fetch('https://api.example.com/data')
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`)
      }
      const result = await response.json()
      setData(result)
    }

    fetchData().catch((e) => {
      // handle the error as needed
      console.error('An error occurred while fetching the data: ', e)
    })
  }, [])

  return <p>{data ? `Your data: ${data}` : 'Loading...'}</p>
}
```

이러한 패턴은 React에서 볼 수 있던 패턴이지만 더 나은 성능이나 캐싱, 낙관적 업데이트를 위해서는 API 호출 라이브러리 (tanstack query, **SWR**) 등을 사용하는 것을 추천합니다. 특히 SWR은 같은 Vercel에서 만들었기 때문에 추천합니다.

SWR을 사용하면 다음과 같이 미리 사용해볼 수 있습니다.

```JavaScript
import useSWR from 'swr'

export function Page() {
  const { data, error, isLoading } = useSWR(
    'https://api.example.com/data',
    fetcher
  )

  if (error) return <p>Failed to load.</p>
  if (isLoading) return <p>Loading...</p>

  return <p>Your Data: {data}</p>
}
```

## SSR

SSR (Server Side Rendering)은 동적 렌더링이라고도 부릅니다.
페이지에서 SSR(동적 렌더링)을 사용하는 경우 각 요청마다 페이지 HTML이 생성이 됩니다.

> 전통적인 렌더링 방식으로 이해하면 됩니다.

Next 13 이전의 버전에서는 `getServerSideProps()` 함수가 필요합니다. 해당 함수는 매번 페이지를 요청합니다.

다음과 같이 작성할 수 있습니다.

```JavaScript
export default function Page({ data }) {
  // Render data...
}

export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // Pass data to the page via props
  return { props: { data } }
}
```

getServerSideProps는 getStaticProps와 비슷하지만 빌드 시간이 아닌 모든 요청에 대해 실행한다는 차이점이 있습니다.

## SSG

SSG (Static Site Generation)은 정적 렌더링이라고 합니다.
이 방법의 특징은 페이지를 호출하거나 한 번에 다운로드 받는게 아니라 빌드가 될 때 HTML이 생성이 됩니다.

### 동적인 데이터가 없는 정적 페이지를 생성하는 방법

동적인 데이터가 없는 일반 페이지를 생성하는 법은 원래 사용하던 코드와 다르지 않습니다.

```JavaScript
function About() {
  return <div>About</div>
}

export default About
```

해당 페이지는 사전 렌더링을 하기 위해 필요한 데이터가 없으므로 빌드 시간 동안 단일 HTML 파일을 미리 생성을 할 수 있습니다.

### 동적인 데이터가 있는 정적 페이지를 생성하는 방법

몇몇의 페이지에서는 페이지 렌더링을 위해서 외부 데이터를 필요로 할 수 있습니다.

시나리오 1. 페이지의 콘텐츠는 외부 데이터를 따르는 경우

페이지의 콘텐츠가 외부 데이터를 따르는 경우 `getStaticProps()`를 사용합니다.

```JavaScript
export default function Blog({ posts }) {
  // Render posts...
}

// This function gets called at build time
export async function getStaticProps() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // By returning { props: { posts } }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  }
}
```

시나리오 2. 페이지의 경로는 외부 데이터에 따라는 경우
페이지의 경로가 외부 데이터를 필요로 하는경우 `getStaticPaths()`와 일반적으로 `getStaticProps()`를 같이 사용합니다. (ex: pages/posts/[id].js 페이지의 id에 들어갈 값들을 먼저 받아와야 하는 경우)

Next.js를 사용할 경우 동적 페이지에서 호출되는 함수를 외부에서 사용할 수 있습니다.

동적 라우팅에 대한 정보는 다음 [사이트](https://nextjs.org/docs/pages/building-your-application/data-fetching/get-static-props)에 있습니다.

```JavaScript
// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false }
}
```

또한 게시물에 대한 데이터를 가져오고 사전 렌더링을 한다면 `getStaticProps()`를 사용하면 됩니다.

데이터 가져오기에 대한 정보는 다음 [사이트](https://nextjs.org/docs/pages/building-your-application/data-fetching/get-static-paths)에 있습니다.

만약 페이지가 동적 라우팅을 사용하고 `getStaticProps()`를 사용한다면 해당 경로들을 사전에 정의를 해줄 필요가 있습니다. `getStaticPaths()`를 사용하면 Next.js에서는 지정한 모든 경로를 사전에 렌더링을 합니다.

`fallback`을 `false`로 설정을 할 경우 다른 라우트들은 404 처리가 됩니다.

```typeScript
// /page/repo/[name].tsx

import type {
  InferGetStaticPropsType,
  GetStaticProps,
  GetStaticPaths,
} from 'next'

type Repo = {
  name: string
  stargazers_count: number
}

export const getStaticPaths = (async () => {
  return {
    paths: [
      {
        params: {
          name: 'next.js',
        },
      }, // See the "paths" section below
    ],
    fallback: true, // false or "blocking"
  }
}) satisfies GetStaticPaths

export const getStaticProps = (async (context) => {
  const res = await fetch('https://api.github.com/repos/vercel/next.js')
  const repo = await res.json()
  return { props: { repo } }
}) satisfies GetStaticProps<{
  repo: Repo
}>

export default function Page({
  repo,
}: InferGetStaticPropsType<typeof getStaticProps>) {
  return repo.stargazers_count
}
```

예제 코드는 다음과 같이 실행이 되고 있습니다.

1. `getStaticProps()`를 사용하는 페이지는 빌드시에 미리 정적인 HTML 파일로 생성이 됩니다. 이 때, 특정 경로의 페이지를 미리 렌더링하여 미리 생성된 HTML 파일로 제공하게 됩니다. 또한 `getStaticProps()`를 통해 반환하는 데이터는 해당 페이지 컴포넌트의 props로 미리 전달이 되어 페이지가 렌더링 될 때 사용이 됩니다.

2. `getStaticPaths()`함수는 동적 경로에 대한 페이지의 경로를 빌드시에 어떻게 생성할 정의를 합니다.

```javascript
export const getStaticPaths = async () => {
  // 비동기적으로 동적 경로를 생성하는 로직
  const paths = [
    { params: { id: '1' } },
    { params: { id: '2' } },
    // ...
  ];

  return {
    paths,
    fallback: false, // 또는 true 또는 'blocking'
  };
};
```

리턴 값은 다음과 같습니다.

paths: 정적으로 생성될 페이지의 경로를 배열로 반환합니다. 배열의 각 요소는 params 속성을 가지며, 동적으로 생성될 경로의 파라미터를 정의합니다.

fallback: 미리 정의된 경로 이외의 경로로 접근할 때 어떻게 처리할지를 정의합니다. false인 경우 404 페이지를 반환하고, true나 'blocking'인 경우 요청 시에 해당 경로에 대한 페이지를 생성합니다.

이 두 함수를 함께 사용할 때, getStaticPaths에서 반환한 경로에 대한 페이지가 getStaticProps에서 반환한 데이터로 미리 빌드되어 정적 파일로 저장됩니다. 이후에 해당 경로에 대한 요청이 들어오면 정적 파일이 제공되어 렌더링 성능을 향상시킵니다.

<details>
<summary>SSG 예제 코드</summary>
<div markdown="1">

```javascript
// /pages/user/[userId].tsx
import type {
  InferGetStaticPropsType,
  GetStaticProps,
  GetStaticPaths,
} from 'next';

type User = {
  userId: number;
  name: string;
  // other user information
};

export const getStaticPaths: GetStaticPaths = async () => {
  // Assuming you have a function to fetch user information from the backend
  const users = await fetchUserInformationFromBackend();

  // Generate paths based on user information
  const paths = users.map((user) => ({
    params: {
      userId: user.userId.toString(),
    },
  }));

  return {
    paths,
    fallback: false,
  };
};

// getStaticPaths() 함수는 백엔드에서 유저 정보를 가져와 해당 유저들의 'userId'를 기반으로 동적 경로를 생성합니다.

export const getStaticProps: GetStaticProps<{ user: User }> = async (context) => {
  const userId = context.params.userId as string;
  // Fetch user information based on the userId
  const user = await fetchUserInformationFromBackendById(parseInt(userId, 10));

// getStaticProps()에서는 각 유저의 'userId'를 기반으로 해당 유저의 정보를 가져와서 페이지에 전달합니다.

  return {
    props: {
      user,
    },
  };
};

export default function UserPage({
  user,
}: InferGetStaticPropsType<typeof getStaticProps>) {
  return (
    <div>
      <h1>{user.name}</h1>
      {/* Render other user information */}
    </div>
  );
}
```

이렇게 설정하면 빌드시에 유저에 대한 페이지가 미리 생성될 클라이언트에 제공이 됩니다.

</div>
</details>

## ISR

ISR (Incremental Static Regeneration)은 증분 정적 재생성이라고 부릅니다. 해당 렌더링 방식을 사용할 경우 사이트를 구축한 후에도 페이지를 생성하거나 업데이트를 할 수 있습니다.

사용하는 방법은 쉽습니다. 위에서 사용했던 `getStaticProps()`에 `revalidate` 항목만 추가를 하면 사용할 수 있습니다.

<details>
<summary>ISR 예제 코드</summary>
<div markdown="1">

```javascript
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}

// This function gets called at build time on server-side.
// It may be called again, on a serverless function, if
// revalidation is enabled and a new request comes in
export async function getStaticProps() {
  const res = await fetch('https://.../posts');
  const posts = await res.json();

  return {
    props: {
      posts,
    },
    // Next.js will attempt to re-generate the page:
    // - When a request comes in
    // - At most once every 10 seconds
    revalidate: 10, // In seconds
  };
}

// This function gets called at build time on server-side.
// It may be called again, on a serverless function, if
// the path has not been generated.
export async function getStaticPaths() {
  const res = await fetch('https://.../posts');
  const posts = await res.json();

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }));

  // We'll pre-render only these paths at build time.
  // { fallback: 'blocking' } will server-render pages
  // on-demand if the path doesn't exist.
  return { paths, fallback: 'blocking' };
}

export default Blog;
```

이렇게 설정하면 초기 방문시부터 10초간 페이지에 대한 모든 요청과 데이터가 즉시 캐싱이 됩니다. 10초 동안 모든 사이트 방문자는 동일한 페이지를 볼 수 있습니다. 캐싱을 무효화 하는 방법은 10초 이후에 누군가가 해당 페이지로 접속을 하는 것 뿐입니다.

</div>
</details>

## Next13 이후의 변경사항

위의 메소드들이 Next 13이후의 `fetch()` api로 변경이 되었습니다. 이제 외울 필요가 없다고 생각할 수 도 있지만 레거시 코드로 남아 있을수도 있기 때문에 잊어서는 안됩니다.

### cache: force-cache

SSG의 `getStaticProps()`와 비슷합니다. 빌드 시점에 fetch가 됩니다. 해당 값은 cache의 default 속성이기 때문에 생략이 가능합니다.

```JavaScript
fetch(URL, { cache: 'force-cache' });
```

### cache: no-store

CSR의 `getServerSideProps()`와 비슷합니다. 해당 옵션으로 설정을 해둘경우 매번 요청을 할 때마다 refetch를 하게 됩니다.

```javascript
const userRequest = await fetch('https://topdragon.co.kr/api/user/info', { cache: 'force-cache' });

const userData = await userRequest.json();
```

### next: { revalidate: n }

ISR과 비슷합니다. 해당 옵션을 사용할 경우 n 만큼 캐싱을 하고있으므로 다른 사용자의 요청시에도 똑같은 페이지가 그려지게 됩니다.

```JavaScript
const userRequest = await fetch('https://topdragon.co.kr/api/user/info', { next: { revalidate: 10 } });

const userData = await userRequest.json();
```
