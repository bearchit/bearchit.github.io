---
title: "Go Sloppylen"
date: 2022-02-06T18:25:45+09:00
draft: true
tags: ["TIL", "go"]
---

# Sloppylen

```go
a := []int{1, 2, 3}
if len(a) <= 0 {
    ...
}
```

[go-critic](https://github.com/go-critic/go-critic)을 통해 lint를 수행하면 `a >= 0` 으로 비교하는 부분에서 아래와 같은 경고가 나타납니다.

> sloppyLen: len(a) <= 0 can be len(a) == 0

slice의 크기를 비교하는 구문에서 크기가 0보다 작은 경우를 비교하는 것은 쓸모없다고 알려주고 있습니다. C 와 마찬가지로 Go의 int 타입도 overflow가 발생할 수 있습니다. 이 경우 음수의 값을 가지게 됩니다. Go의 `len` 함수는 int 타입을 리턴합니다. 그렇다면 이 역시 overflow가 발생 할 수 있습니다. 다만 그렇게 큰 크기의 slice를 생성 할 수 있는지는 확인해봐야 합니다.

이 경우 배열의 크기가 비정상적인 경우를 체크하기 위해 음수 영역까지 비교해야 하는게 안전하지 않을까 하는 의문이 남네요. `gocritic` linter는 왜 이 경우를 쓸모없는 비교라고 판단하고 체크하고 있을까요?

https://github.com/go-critic/go-critic/issues/418
