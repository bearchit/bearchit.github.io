---
title: sqlx Batch Inserts
date: 2021-08-01T23:12:57+09:00
tags: ["go", "sqlx"]
description: sqlx Batch Inserts
draft: true
---

[sqlx](https://github.com/jmoiron/sqlx) 라이브러리를 사용하여 한 번에 여러 개의 데이터를 추가하는 방법에 대해서 설명합니다.

**sqlx**에서는 [Named Queries](https://jmoiron.github.io/sqlx/#namedParams)를 통해 파라미터에 이름을 붙여 쿼리에 적용 될 값을 표시 할 수 있습니다.

**NamedExec** 함수는 map, struct 등의 타입의 멤버들을 추론하여 SQL 쿼리의 인자들에 매핑시켜 줍니다. 간단하게 구조체를 하나 만들고 **NamedExec**에 이 데이터들을 넘겨주면 됩니다. 이 때 해당 데이터의 타입이 slice일 경우 `sqlx`은 slice들의 데이터들을 쿼리로 연결해 줍니다.

```go
db.NamedExec(
		`INSERT INTO tasks (content, completed_at) values (:content, :completed_at)`,
		[]Task{
			{Content: "예제 코드 작성하기", CompletedAt: &completedAt},
			{Content: "Gist 등록하기"},
			{Content: "본문에 Gist 연동하기"},
		})
```

또한 여러개의 Row를 slice에 저장 할 수 있습니다.

```go
var tasks []Task
db.Select(&tasks, `SELECT * FROM tasks`)
```

실행 가능한 전체 코드는 아래와 같습니다.
{{< gist bearchit c4d6d58243e5a9a0cbcd8d347e43a7d0>}}
