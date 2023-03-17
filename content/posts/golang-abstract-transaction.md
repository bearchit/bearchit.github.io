---
title: "2021 07 31"
date: 2021-07-31T00:15:35+09:00
draft: true
toc: true
---

### sql.DB 트랜잭션 추상화

Golang의 표준 라이브러리 `sql.DB` 에서 트랜잭션을 사용하기 위해서는 `Begin` 함수를 통해 `Tx`를 얻어내야 한다.

```go
tx, err := db.Begin()
if err != nil {
  return fmt.Errorf("failed to begin a transaction: %w", err)
}
```
