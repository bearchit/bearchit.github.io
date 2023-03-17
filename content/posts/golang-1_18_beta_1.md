---
title: "Go 1.18 Beta 1"
date: 2021-12-18T18:46:57+09:00
tags: ["go", "generic", "1.18"]
---

Go 1.18 Beta 1 버전이 릴리즈 되었습니다. 1.18의 가장 큰 특징으로는 (**드디어!!**) generic이 정식으로 추가되었다는 것입니다.

Go Blog에 소개된 자료를 바탕으로 간단하게 체험해 보도록 하겠습니다.

## 설치

`Go`가 이미 설치되어 있다면 아래 명령어를 통해 1.18 beta1을 설치할 수 있습니다.

```sh
go install golang.org/dl/go1.18beta1@latest
```

아래 명령어를 통해 업데이트를 설치합니다.

```sh
go1.18beta1 download
```

업데이트가 완료되면 `go1.18beta1` 명령어를 통해 1.18 beta 1 버전의 Go를 사용할 수 있습니다.

```sh
go1.18beta1 version
```

alias를 통해 `go1.18beta1` 명령어 대신에 `go` 명령어로 축약해서 사용할 수 있습니다.

```sh
alias go=go1.18beta1
go version
```

## Generic 예제

```go
package main

import "fmt"

type Number interface {
    int64 | float64
}

func main() {
    ints := map[string]int64{
        "first": 34,
        "second": 12,
    }

    floats := map[string]float64{
        "first": 35.98,
        "second": 26.99,
    }

    fmt.Printf("Generic Sums: %v and %v\n",
        SumNumbers(ints),
        SumNumbers(floats),
    )
}

func SumNumbers[K comparable, V Number](m map[K]V) V {
    var s V
    for _, v := range m {
        s += v
    }
    return s
}
```

기존처럼 타입별로 여러 개의 함수를 작성하지 않고, Generic을 사용하여 함수 하나로 임의의 타입에 대응이 가능해 보입니다.

## 출처

- [https://go.dev/doc/tutorial/generics](https://go.dev/doc/tutorial/generics)
