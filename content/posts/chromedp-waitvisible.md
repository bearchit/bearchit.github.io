---
title: "chromedp Wait Visible"
date: 2021-09-04T22:35:15+09:00
draft: true
---

**chromedp**의 **WaitVisible** 함수의 동작에 대해서 정리한다.

## WaitVisible

우선 함수의 정의를 살펴보도록 하자.

> WaitVisible is an element query action that waits until the element matching the selector is visible. [[link](https://pkg.go.dev/github.com/chromedp/chromedp#WaitVisible)]

```go
func WaitVisible(sel interface{}, opts ...QueryOption) QueryAction
```

**WaitVisible** 함수는 **selector**에 해당하는 요소가 표시될 때까지 기다리도록 하는 역할을 한다. 여기서 '기다리다' 라는 표현이 다소 혼란스러울 수 있는데, **chromedp**는 일련의 명령을 지정하고 한 번에 실행시키는 형식의 API를 가지고 있다. 따라서 **WaitVisible**이 실행되게 되면 다음 명령을 수행하기 전에 지정한 요소가 표시될 때까지 명령 수행을 멈췄다가 해당 요소가 표시되면 다음 명령으로 제어권을 넘긴다.

## 표시될 때 까지?

그럼 **WaitVisible** 함수가 어떻게 DOM 요소의 표시 여부를 판단하는지 알아보도록 하자.

**chromedp**는 기본적으로 [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)을 통해 크롬 브라우저 프로세스와 통신한다. 이 프로토콜에서는 크롬의 다양한 개발자 API 들을 지원해주며, **chromedp**는 이 API를 사용한다. **Chrome DevTools Protocol**과의 통신을 정의한 부분은 [cdproto](https://github.com/chromedp/cdproto)라는 별도의 패키지로 관리하고 있다.

**WaitVisible** 함수의 구현을 따라가보면 **chromedp**에서 어떻게 DOM 요소의 표시 여부를 판단하는지 알 수 있다. ([Source](https://github.com/chromedp/chromedp/blob/b04145ce79fe1919930a241b98a21f872bedb3d1/query.go#L450))

### BoxModel

먼저 해당 DOM 노드(이하 '노드')의 **BoxModel**을 쿼리한다. 만약 **BoxModel**을 획득하는데 실패하면 노드가 아직 표시되지 않았다고 판단 한다.

```go
_, err := dom.GetBoxModel().WithNodeID(n.NodeID).Do(ctx)
if err != nil {
	if isCouldNotComputeBoxModelError(err) {
		return ErrNotVisible
	}

	return err
}
```

**BoxModel**이 생성되었다면 해당 노드를 대상으로 짧은 Javascript를 실행 시킨다. 이 Javascript 코드는 해당 노드의 표시 정보를 쿼리하여 그 값들이 유효한지 검사한다. 간단하게 브라우저에 해당 노드가 표시되었는지 확인 하는 과정이다.

```jsx
function visible() {
  return Boolean(
    this.offsetWidth || this.offsetHeight || this.getClientRects().length
  );
}
```

위의 **visible** 함수가 **true를** 반환하면 해당 요소가 브라우저에 표시되었다고 판단한다.

브라우저의 동작 과정상 **offsetWidth**, **offsetHeight** 와 같은 정보들이 구축되었다는 것은 DOM 분석과 배치가 완료되었다는 의미이기 떄문에 해당 요소가 표시 되었다고 판단 할 수 있다.
