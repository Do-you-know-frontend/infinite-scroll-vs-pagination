# 무한스크롤

Date: January 11, 2023 4:03 PM
Status: Need to Learn
Tags: React

# 무한 스크롤이란?

트위터와 같은 SNS에서 자주 쓰이는 방법으로 스크롤을 내리는 행위를 통해 추가적인 데이터를 불러오는 기법입니다.정확히는 스크롤을 끝까지 내렸을 때 다음 데이터를 가져오는식으로 계속해서 스크롤을 내릴 수 있게 합니다.

## 무한스크롤의 장점

**1. 페이지를 따로 넘기지 않고 (방해 요소 없이) 컨텐츠를 보여줄 수 있습니다**

무한스크롤의 장점은 **자연스러움**입니다 => 유저에게 더 많은 컨텐츠를 보도록 유도 가능

**2. 모바일 환경에 적합합니다.**

모바일 환경에서 가장 기본이 되는 **상하 스크롤링으로만 데이터를 계속해서 접할 수 있다**는 점을 말합니다.

## 무한스크롤의 단점

무한스크롤의 단점은 **원하는 데이터의 위치를 기억하기 어렵다**는 점입니다.

무한스크롤은 **원하는 정보가 어디에 위치하는지를 기억할 수 없습니다.**

대략적으로 이 정도 스크롤 내리면 나왔던 것 같은데? 와 같은 부정확한 직감에 의존하게 됩니다.

무한스크롤은 특정 데이터를 찾고, 기억해야하는 서비스에서는 좋지 않습니다.

### 데이터 10,000개를 가지고 무한 스크롤 구현시에 가장 중요하게 고려해야 할점은?

1. 기존의 컴포넌트가 리사이징될 때, 렌더링이 매우 느려진다.

---

1. 스크롤을 할 때마다 랙이 걸린다.

---

1. 수천개의 컴포넌트로 가득찬 DOM은 브라우저가 버티지 못할 수 있다.

대부분의 디바이스는 프레임을 초당 60번의 속도로 새로고침합니다. 하나의 프레임이 사용할수 있는 예산은 (1s/60 = 16.66ms) 정도가 된다는 것인데 어떤 프레임이 예산을 초과해서 사용한다면, 제 때에 프레임을 갱신할 수 없게 되면 화면 끊김현상이 발생합니다.

컴포넌트의 재사용, 이미지 리사이즈, `Intersection Observer`를 통한 뷰포트 로딩, 스켈레톤을 통한 사용자 경험 개선. 레이지로딩을 통한 초기 렌더링 속도 감소 등이 고려되어야합니다.

### 레이지로딩

![https://velog.velcdn.com/images%2Frladpwl0512%2Fpost%2F52db0f55-adad-4ec6-b39c-a64c27c50faf%2Fimage.png](https://velog.velcdn.com/images%2Frladpwl0512%2Fpost%2F52db0f55-adad-4ec6-b39c-a64c27c50faf%2Fimage.png)

**`lazy loading`이란 페이지가 처음 렌더링 되는 시점에서 필요하지 않는 리소스들은 최대한 미뤄서 나중에 로딩하는 기술이다.**

그렇다면, lazy loading은 언제 발생시키는 것이 좋을까?`lazy loading`이 발생하는 순간은 개발자가 지정할 수 있지만, 위 사진과 같이 scroll바가 가장 아래에 내려왔을 때 컨텐츠를 로드하는 경우가 일반적으로 많이 사용된다. (무한 스크롤! 스크롤이 계속 내려가면서, 컨텐츠가 새로 로딩된다.)

- `lazy loading`은 성능향상, 비용 감소, 사용자 경험 향상의 장점들이 있기 때문에 안 쓸 이유가 없다!
- 이러한 lazy loading의 예 중 하나가, 스크롤을 내리면 계속 새로운 컨텐츠가 로딩되는 무한 스크롤이다.
- 무한 스크롤은 `scroll` 이벤트, `Intersection observer API`를 통해 구현할 수 있다. `scroll` 이벤트는 스크롤을 내릴 때마다 발생되는 단점이 있고, 이를 개선하기 위해서는 `debounce`, `throttle`를 사용해야하는 번거로움이 있었다.하지만, `scroll` 이벤트의 단점을 개선한 `Intersection observer API`가 등장하면서, 무한 스크롤을 구현하기가 좋아졌다.

<aside>
☑️ 중요하게 고려해야할 점

</aside>

중요하게 고려해야 할 점은, 무한스크롤은 트위터나 페이스북 같은 단발성 메시지가 주가 되는 SNS에서는, 최근 글에만 신경쓰게 할 수 있지만, 과거 데이터를 보고 싶은 사람들이 많은 사이트라면, 무한스크롤 구현을 하기에 적합하지 않다는 것을 고려해야 합니다.

> 그럼 뭘 해야되나요?

**페이지네이션**

# 무한 스크롤 구현 방법

무한 스크롤을 구현하기 위한 방법은 크게 2가지 방법이 있다.
1. `scroll listener` (스크롤 리스너)
2. `intersection observers` (교차로 관찰자)

- `scroll listener` 방법은 스크롤이 아주 미세하게 움직여도 수많은 트리거가 발생하게 되고 이는 성능 저하의 주된 원인이 된다.
- 하지만 특정 상황에서는 스크롤 리스너가 더 효율적일 수 있다.
- `intersection observer`는 특히 앱에서(모바일) 더 나은 성능을 제공한다.

`intersection observer`가 항상 효율적인 건 아니지만 모바일 같이 스크롤 사용이 빈번한 환경에서는 더 좋은 선택이 될 수 있다

## **scroll event로 무한스크롤 구현(feat. throttle)**

스크롤이 페이지 끝에 닿는 것을 감지하여 추가 데이터를 받아오는 방식입니다.

`전체 페이지 높이 <= 지금까지 스크롤한 길이 + 현재 보이는 부분`이면 끝에 닿았다는 것을 인지하여 데이터 요청을 한다.

![https://velog.velcdn.com/images/sjoleee_/post/87d6cf95-1621-4c91-ac48-4fc97a8883cf/image.jpg](https://velog.velcdn.com/images/sjoleee_/post/87d6cf95-1621-4c91-ac48-4fc97a8883cf/image.jpg)

- `scrollTop` : 맨 위 ~ 현재 보이는 부분까지
- `scrollHeight` : 맨 위 ~ 맨 아래
- `clientHeight` : 현재 보이는 부분(border 제외)
- `offsetHeight` : 현재 보이는 부분(border 포함)

scroll 이벤트가 발생할 때마다, `scrollHeight`와 `scrollTop + offsetHeight`를 비교하면 페이지의 끝에 닿았는지를 알 수 있겠습니다.

하지만, **"scroll 이벤트가 발생할 때마다"**와 같은 과도한 이벤트는 **성능 저하**로 이어질 수 있습니다다.성능 개선을 위해서는 `throttle`을 적용할 수 있습니다.

<aside>
☑️ throttle

</aside>

> Throttle이란?
> 

마찬가지로, **과도한 요청, 처리를 수행하게 될 경우 발생할 수 있는 성능 저하를 막기 위해 이를 효과적으로 제어하여 성능을 개선하는 방법 중 하나!**

동작하는 방식이 debounce와 조금 다른데, **마지막 함수가 호출된 후 일정 시간이 지나기 전에는 다시 호출되지 않도록 한다.**

무한 스크롤을 구현한다고 생각해보자.

페이지의 하단(대충 하단이라고 표현하겠다.) 부근까지 스크롤이 내려가면 추가로 데이터를 불러와야해서 스크롤이 어디까지 내려갔는지를 감지해야하는 상황이다.

이때, `throttle`을 설정해두지 않는다면 스크롤이 내려가는 내내 스크롤 이벤트가 발생하여 성능에 문제를 일으킬 수 있다.

이를 일정 시간동안 한 번만 스크롤을 감지하도록 하여 과도한 이벤트를 제어한다.

왜 `debounce`를 쓰지 않고 `throttle`을 사용하냐면, `debounce`를 사용했다면 사용자가 페이지를 쭉~ 내려서 페이지 끝에 닿고 스크롤 이벤트가 더 이상 발생하지 않을 때가 되어서야 추가로 데이터를 불러오게 된다.

페이지 끝에 닿기 전에 추가로 데이터를 불러와서 무한한 스크롤을 만드는 것이 목적이라면 throttle이 더욱 적합하다.

### 구현 방법

```tsx
const onScroll = useCallback(() => {
    if (!loadMore) return;
    clearTimeout(timeoutId);
    if (isEnd || isLoading) return;
    timeoutId = setTimeout(() => {
      if (
        document.documentElement.scrollTop +
          document.documentElement.clientHeight >=
        document.documentElement.scrollHeight - 250
      ) {
        loadMore();
      }
    }, 300);
  }, [isEnd, isLoading, timeoutId, loadMore]);

  useEffect(() => {
    window.addEventListener('scroll', onScroll);
    return () => {
      window.removeEventListener('scroll', onScroll);
    };
  }, []);
```

## ****Intersection Observer API를 사용한 무한 스크롤****

<aside>
☑️ Intersection observer

</aside>

[Intersection observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)는 기본적으로 브라우저 뷰포트(Viewport)와 설정한 요소(Element)의 교차점을 관찰하며, `Target Element` 가 화면에 **노출**되었는지 여부를 간단하게 구독할 수 있는 `API`이다. 더 쉽게는 사용자 화면에 지금 보이는 요소인지 아닌지를 구별하는 기능을 제공합니다.

![https://velog.velcdn.com/images/sjoleee_/post/1fa4a9a8-7e99-421b-ac31-b25a710cd341/image.jpeg](https://velog.velcdn.com/images/sjoleee_/post/1fa4a9a8-7e99-421b-ac31-b25a710cd341/image.jpeg)

스크롤을 내리다 뷰포트에 타겟 요소가 들어오게 되면 데이터를 불러온다.불러온 데이터는 기존 데이터와 타겟 요소 사이에 추가한다.

다시 스크롤을 내리다 뷰포트에 타겟 요소가 들어오게 되면 데이터를 불러온다.불러온 데이터는 기존 데이터와 타겟 요소 사이에 추가한다.

`Intersection Observer API`를 사용하여 구현하는 방법은 고전적으로 사용하는 `scroll` 이벤트를 걸어서 스크롤할때마다 함수가 실행되는 스크롤 이벤트보다 성능적으로도 우월하고 사용하기도 편합니다.

### API 메소드, 옵션

```jsx
// ✅ 관측에 적용할 수 있는 옵션입니다.
let options = {
  root: null, // 타켓 요소가 "어디에" 들어왔을때 콜백함수를 실행할 것인지 결정합니다. null이면 viewport가 root로 지정됩니다.
  //root: document.querySelector('#scrollArea'), => 특정 요소를 선택할 수도 있습니다.
  rootMargin: '0px', // root에 마진값을 주어 범위를 확장 가능합니다.
  threshold: 1.0 // 타겟 요소가 얼마나 들어왔을때 백함수를 실행할 것인지 결정합니다. 1이면 타겟 요소 전체가 들어와야 합니다.
}

// ✅ 관측되었을 경우 실행할 콜백함수입니다.
let callback = () => {
  console.log('관측되었습니다.')
}

// ✅ observer를 선언합니다.
// 첫 번째 인자로 관측되었을 경우 실행할 콜백함수를 넣습니다.
// 두 번째 인자로 관측에 대한 옵션을 지정합니다.
let observer = new IntersectionObserver(callback, options);

// ✅ 타겟 요소를 지정합니다.
// React에서는 useRef를 활용하여 DOM을 선택합니다.
let target = useRef();

observer.observe(target); // ✅ 타겟 요소 관측 시작
observer.unobserve(target); // ✅ 타겟 요소 관측 종료
```

<aside>
☑️ isIntersecting

</aside>

관찰 대상이 루트 요소와 교차 상태로 들어가거나(**`true`**) 교차 상태에서 나가는지(**`false`**) 여부를 나타내는 값(`Boolean`)입니다.

### 구현 방법

```jsx
import { useState, useRef, useEffect } from 'react';
import ProductCard from 'components/ProductCard';

const ProductMain = ({ list, params }) => {
	const [bottom, setBottom] = useState(null);
	const bottomObserver = useRef(null);
	useEffect(() => {
		const observer = new IntersectionObserver(
			entries => {
				if (entries[0].isIntersecting) {
					const { page, totalElement, limit } = params.pageData;
					if (totalElement < limit * (page - 1)) {
						return;
					}
					params.getProductList({ page: page + 1 });
				}
			},
			{ threshold: 0.25, rootMargin: '80px' },
		);
		bottomObserver.current = observer;
	}, []);

	useEffect(() => {
		const observer = bottomObserver.current;
		if (bottom) {
			observer.observe(bottom);
		}
		return () => {
			if (bottom) {
				observer.unobserve(bottom);
			}
		};
	}, [bottom]);

	return (
		<>
			{list.map(card => (
				<ProductCard key={card.id} data={card} />
			))}
			<div ref={setBottom} />
		</>
	);
};

export default ProductMain;
```

`observe`메서드는 대상 요소의 관찰을 시작하게 됩니다. 
그에 반해 `unobserve`는 대상 요소의 관찰을 중지하게 됩니다. 
`disconnect`는 인스턴스가 관찰하는 모든 요소의 관찰을 중지하게 됩니다.

`observer`가 `bottom`을 감지하고 `bottom`이 `true`가 되면 새로운 리스트를 불러오는 방식

스크롤 이벤트 또는 observer로 무한 스크롤을 구현하면 치명적인 단점이 있습니다. 스크롤을 계속 밑으로 내려서 데이터가 100개 1000개 쌓이게 되면 브라우저 dom은 100개 또는 1000개가 추가 되어 브라우저는 버벅이게 되고 사용자의 ux가 나빠집니다.

- 훅으로 만들어서 사용(TypeScript)
    
    ```jsx
    import { useEffect, useState } from "react";
    
    interface useIntersectionObserverProps {
      root?: null;
      rootMargin?: string;
      threshold?: number;
      onIntersect: IntersectionObserverCallback;
    }
    
    const useIntersectionObserver = ({
      root,
      rootMargin = "0px",
      threshold = 0,
      onIntersect
    }: useIntersectionObserverProps) => {
      const [target, setTarget] = useState<HTMLElement | null | undefined>(null);
    
      useEffect(() => {
        if (!target) return;
    
        const observer: IntersectionObserver = new IntersectionObserver(
          onIntersect,
          { root, rootMargin, threshold }
        );
        observer.observe(target);
    
        return () => observer.unobserve(target);
      }, [onIntersect, root, rootMargin, target, threshold]);
    
      return { setTarget };
    };
    
    export default useIntersectionObserver;
    ```
    

## React query에서 무한스크롤 구현하기

무한스크롤을 구현하기 전에 `react-query`가 쉽게 이전, 다음 페이지의 여부를 알 수 있도록 백엔드 API세팅이 필요하며, 이때 다음페이지 번호나 페이지 존재 여부, 다음페이지 URL 등 다음페이지를 확인 할 수 있다면 뭐든지 와도 상관없다.

```jsx
{
  "isLast": false, // 페이지 존재 여부
  "posts": [ ...postData ]
}
```

`isLast`라는 `boolean`값을 받아 현재 페이지가 마지막페이지 인지를 알 수 있다.

<aside>
☑️ useInfiniteQuery

</aside>

`useInfiniteQuery`도 `useQuery`와 같이 인자로 쿼리키, 쿼리함수, 옵션을 받으며 useQuery와 다른점으로 이 쿼리함수는 객체 매개변수를 받으며 프로퍼티로 `pageParam`을 가지고 있다. 

이 `pageParam`의 값에 따라 `useInfiniteQuery`는 다음페이지에 요청을 보낼 수 있고 마지막페이지라면 `pageParam`를 `undefined`로 만들어 요청을 보내지 않게 할 수 있다

- `fetchNextPage` - 다음 페이지를 불러올 때 사용하는 함수
- `hasNextPage` - 다음 페이지가 있는 지 판별하는 함수
- `isFetchingNextPage` - 다음 페이지를 불러오는 동안 로딩 상태

### 구현 방법

```jsx
import React, { useEffect } from "react";
import { useInView } from "react-intersection-observer";
import { PostsContainer } from "styles/Posts/PostStyle";
import PostCard from "pages/Post/PostCard";
import Loading from "components/Loading";
import ErrorPage from "components/ErrorPage";
import { useInfiniteQuery } from "react-query";
import axios from "axios";

const fetchPostList = async (pageParam) => {
  const res = await axios.get(
    `http://localhost:5001/posts?&page=${pageParam}&limit=6`);
  const { posts, isLast } = res.data;
  return { posts, nextPage: pageParam + 1, isLast };
};

function Posts() {
  const { ref, inView } = useInView();
  const { data, status, fetchNextPage, isFetchingNextPage } = useInfiniteQuery(
    "posts",
    ({ pageParam = 1 }) => fetchPostList(pageParam),
    {
      getNextPageParam: (lastPage) =>
        !lastPage.isLast ? lastPage.nextPage : undefined,
    }
  );

  useEffect(() => {
    if (inView) fetchNextPage();
  }, [inView]);

  if (status === "loading") return <Loading />;
  if (status === "error") return <ErrorPage />;

  return (
    <>
      <PostsContainer>
        {data?.pages.map((page, index) => (
          <React.Fragment key={index}>
            {page.posts.map((post) => (
              <PostCard key={post._id} post={post}></PostCard>
            ))}
          </React.Fragment>
        ))}
      </PostsContainer>
      {isFetchingNextPage ? <Loading /> : <div ref={ref}></div>}
    </>
  );
}
export default Posts;
```

`react-intersection-observer` 라이브러리를 사용하였는데 간단하게 설명하자면 DOM 요소에 ref로 모니터링 하고 해당 요소가 화면에 보이는지 안보이는지 여부를 `inView`로 알 수 있다. `react-query` 공식문서의 `useInfiniteQuery` 예제에서도 사용한다.

## 역 방향 스크롤 구현하기(채팅)

```jsx

  const onFetchMessages = useCallback(() => {
    // 1. 스크롤이 최상단에 닿여서 데이터를 불러오기전에 현재 scrollHeight를 저장한다
    // 2. 새로운 데이터를 불러왔을때 scrollHeight에서 저장해둔 scrollHeight를 뺀값이 현재 스크롤 위치
    setPrevScrollHeight(scrollRef.current?.scrollHeight);

    fetchNextPage();
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  const onScrollTo = (y) => {
    scrollRef.current.scrollTop = y;
  };

  useEffect(() => {
    // 화면에 노출되면, 데이터를 불러오는 함수를 실행
    if (inView && messages) {
      onFetchMessages();
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [inView]);

  useEffect(() => {
    if (prevScrollHeight) {
      onScrollTo(scrollRef.current?.scrollHeight - prevScrollHeight);
      return setPrevScrollHeight(null);
    }
    onScrollTo(
      scrollRef.current?.scrollHeight - scrollRef.current?.clientHeight
    );
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [messages?.pages]);

<ChatContainer ref={scrollRef}>
     {isFetchingNextPage ? <div /> : <div ref={ref}></div>}
...

```

# 페이지란?

무한 스크롤 이전에 쓰이던 기법으로 페이징, 페이지네이션 등 용어는 다양할 수 있으나 개수나 카테고리 등 데이터를 일정 기준으로 나눠서 보여주는 것을 페이지 라고 합니다.

기본으로 1페이지를 보여주고, 더 보고싶으면 2페이지나 3페이지를 클릭하면 됩니다.

# UI / UX 측면에서 보는 무한스크롤 VS 페이지네이션

## **무한 스크롤의 장점**

### 1) 사용자 참여 및 콘텐츠 탐색이 쉽습니다.

데이터를 탐색하는 주요 방법으로 무한 스크롤을 사용하는 경우 사용자가 웹페이지에 더 오래 남아있을 수 있어 참여도를 높일 수 있어요. 소셜미디어의 인기 덕분에 데이터 소비량도 증가했는데요. 이런 상황에서 무한 스크롤은 페이지가 로딩되기를 기다리지 않고 정보를 탐색할 수 있는 효율적인 방안입니다. 사용자가 특정 항목을 찾는 것이 아니라면 많은 양의 정보를 탐색한 뒤 원하는 항목을 발견할 수 있으니까요.

[https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdJl0tk%2FbtqwWzZAuyy%2FhbWFiCTpdzC4Z6Eunykr60%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdJl0tk%2FbtqwWzZAuyy%2FhbWFiCTpdzC4Z6Eunykr60%2Fimg.png)

### 2) 무한 스크롤이 클릭하는 것보다 더 나은 사용자 경험을 제공합니다.

사용자에게는 클릭/탭을 하는 것보다 스크롤을 사용할 때의 경험이 더 좋을 수 있어요. 마우스 휠이나 터치스크린으로 스크롤을 활용하면 클릭보다 사용성을 높여줄 수 있다는 이야기죠. 튜토리얼과 같은 긴 콘텐츠의 경우 텍스트를 몇 개의 개별 화면이나 페이지로 자르는 것보다 무한 스크롤을 통해 한 페이지에서 보여주는 것이 더 낫습니다.

[https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F6OfW2%2Fbtqw0a4pq0z%2FhFSqfpxNvMdAq1s5UfPRJ1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F6OfW2%2Fbtqw0a4pq0z%2FhFSqfpxNvMdAq1s5UfPRJ1%2Fimg.png)

클릭 혹은 탭일 경우 각 콘텐츠를 보기 위한 추가 클릭이 필요하고 페이지가 로드될 때까지 기다려야 합니다. 반면 스크롤은 스크롤만 하면 되죠.

### 3) 무한 스크롤은 모바일에 적합합니다.

![https://blog.kakaocdn.net/dn/dwbsue/btqwZYwrynE/1SaJsmlsMmKKYCm8J53Mak/img.gif](https://blog.kakaocdn.net/dn/dwbsue/btqwZYwrynE/1SaJsmlsMmKKYCm8J53Mak/img.gif)

화면이 작을수록 스크롤은 길어져요. 그런데 모바일 검색이 보편화하면서 작은 화면에서 많은 정보를 노출해야 하는 상황이 늘었습니다. 무한 스크롤은 좋은 방법이 될 수 있습니다. 모바일 환경에서 콘텐츠를 보여주기에 가장 직관적이고 사용하기 쉬운 형식이에요.

## **무한 스크롤의 단점**

### 1) 페이지 성능이 느려집니다.

페이지 로딩 속도는 사용자 경험에서 제일 중요한 요소입니다. 로딩 시간이 느리면 사용자가 사이트에서 나가버릴 가능성이 커요. 다시는 들어가지 않기도 하죠. 무한 스크롤을 사용하면 페이지를 스크롤 할수록 더 많은 콘텐츠가 업로드되어야 하는데요. 그러면 페이지 성능은 떨어집니다. 심지어 성능이 좋지 않거나 화면 크기가 작은 사용자 기기라면 문제가 더 심각해집니다. 또 이미지가 많이 실린 무한 스크롤 사이트를 열려면 용량 때문에 느려지기 시작하죠.

### 2) 특정 항목 검색 및 원래 위치로 되돌아오기 힘듭니다.

사용자가 스트림의 특정 지점에 도달했을 때 책갈피 표시를 할 수 없고 나중에 다시 그 지점을 찾아가기가 어렵다는 점 역시 무한 스크롤의 단점이에요. 사용자가 사이트에서 나갔다가 다시 들어와서 그 위치로 가려면 또다시 스크롤을 아래로 계속 내려야 합니다. 사용자는 짜증나기 쉽고 혼란스럽죠. 치명적인 단점이고, 결과적으로 사이트의 사용자 경험을 해치기 쉽습니다.

### 3) 스크롤 막대가 실제 데이터의 양을 반영하지 못합니다.

무한 스크롤의 또 다른 문제점은 스크롤 막대가 실제 사용 가능한 데이터의 양을 반영하지 않는다는 점입니다. 사용자는 페이지 맨 끝단에 곧 다다르리라 생각하며 스크롤을 할 텐데요. 하지만 곧 페이지 끝을 보는 데 기대했던 것의 2배의 시간이 걸린다는 것을 눈치채고 스크롤 막대가 실제 데이터의 양을 반영하지 않는다는 것을 깨닫게 됩니다. 사용성을 해치는 것이죠.

### 4) 푸터를 찾기 어려워집니다. 푸터는 사용자에게 꼭 필요한 콘텐츠를 포함하는데요. 피드를 무한 스크롤 하면 하단에 도달할 때 더 많은 데이터가 로드되기 때문에 푸터는 시야 밖으로 밀려납니다.


## 페이지네이션의 장점

### 1) 사용자 의도에 맞게 페이지를 넘길 수 있습니다.

사용자가 단순히 정보의 흐름만 훑어보고 소비하는 것이 아니라 목록 내에서 특정 콘텐츠를 찾을 때 페이지네이션 방식은 유용합니다.


### 2) 통제감을 느낄 수 있습니다.

사용자가 이용 가능한 결과의 수를 알고 있다면 정보에 근거해 좀 더 나은 결정을 내릴 수 있습니다. ‘인간과 컴퓨터의 상호작용에서의 심리 연구'에 따르면 사용자는 기대하는 시간 안에 예상한 모습으로 종착점에 도달할 때 통제감을 느낀다고 해요. 페이지네이션의 장점입니다. 또 사용자가 검색 결과의 수를 볼 수 있다면 그들이 원하는 항목을 찾을 때 시간이 얼마나 걸릴지 추정할 수 있고, 그들이 찾는 정보가 그 페이지에 있는지 없는지도 쉽게 판단할 수 있어요.

### 3) 특정 항목의 위치를 파악할 수 있습니다.

페이지가 매겨진 링크를 사용하면 더 쉽게 특정한 항목을 찾을 수 있겠죠. 또 무한 스크롤과 달리 페이지네이션을 사용하면 특정 위치를 잘 찾아갈 수 있어요. 어떤 페이지 번호를 클릭해야 하는지 알기 때문입니다. 따라서 전자상거래 사이트, 앱에 잘 어울리는 방식입니다.

## **페이지네이션의 단점**

### 1) 페이지네이션은 추가적인 작업을 필요로 합니다.

사용자는 페이지네이션 방식에서 다음 페이지로 이동하려면 ‘다음'이라는 버튼을 찾아 마우스를 그 위에 올려놓고 클릭한 다음 새 페이지가 로드될 때까지 기다려야 합니다.

2) 페이지네이션은 한 페이지에 매우 제한된 내용만을 보여줍니다.

페이지네이션의 가장 큰 문제는 한 페이지에서 제한된 내용만 보여준다는 점입니다. 원하는 정보를 충분히 얻기 위해서 페이지 번호를 찾아가거나 ‘다음' 버튼을 자주 눌러야 해요.

## **무한 스크롤과 페이지네이션의 용도**

무한 스크롤은 사용자들이 직접 끊임없이 생성하는 콘텐츠가 많은 사이트와 앱에 적합해요. 페이스북, 트위터, 핀터레스트, 인스타그램 등 소셜미디어가 그렇죠. 반면 페이지네이션은 사용자가 가야 할 방향이 뚜렷한, 목표지향적인 사이트와 앱에 적합합니다.

# References

[사용자에게 많은 데이터를 보여주는 방법 | 페이지 | 무한 스크롤](https://velog.io/@dishate/%ED%8E%98%EC%9D%B4%EC%A7%80%EC%97%90-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC-%EB%B3%B4%EC%97%AC%EC%A3%BC%EB%8A%94-%EB%B0%A9%EB%B2%95-%ED%8E%98%EC%9D%B4%EC%A7%80-%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4)

[무한 스크롤 vs 페이지네이션](https://slowalk.com/2596)

[Intersection Observer - 요소의 가시성 관찰](https://heropy.blog/2019/10/27/intersection-observer/)

[[React] 무한 스크롤 적용하기](https://velog.io/@sjoleee_/React-%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4)

[[React] 검색에서 Debounce 적용해보기](https://velog.io/@sjoleee_/debounce-throttle-%EC%A0%95%EB%A6%AC#debounce%EB%9E%80)

[React-Query로 무한스크롤 구현하기](https://velog.io/@handwoong/React-Query%EB%A1%9C-%EB%AC%B4%ED%95%9C%EC%8A%A4%ED%81%AC%EB%A1%A4-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)
