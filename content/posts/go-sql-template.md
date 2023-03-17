---
title: No ORM
date: 2021-07-31T00:14:45+09:00
tags: ["go", "sql", "orm"]
cover:
  image: https://images.unsplash.com/photo-1489875347897-49f64b51c1f8?xid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=2100&q=80
showToc: true
draft: true
---

## ORM

Go 생태계에는 여러 종류의 ORM이 존재한다.

- GORM
- XORM
- ent

이 중 Production 레벨까지 사용해봤던 ORM으로는 `GORM`과 `ent`가 있다.

## ORM을 계속 사용해야 할까?

ORM을 사용하다 보면 N+1 쿼리와 같은 문제를 만나게 된다. N+1 문제는 `Eager Loading` 같은 기법으로 해결할 수 있다. ORM을 사용하면서 가장 빈번하게 어려움을 겪는 경우는 ORM에서 지원하지 않거나 구현하기에 까다로운 쿼리를 작성해야 할 때다.

ORM을 사용하면 분명 장점이 있다. 도메인 레이어에서 사용하는 객체를 그대로 저장소로 보관할 수 있도록 도와주기 때문에 ORM을 사용하면 분명 생산성 측면에서 큰 도움이 된다.

ORM으로 한계를 겪게 되는 쿼리는 Raw 쿼리를 직접 작성하여 사용 할 수 있다.

ent는 코드로 정의한 Entity들의 선언과 관련 쿼리들을 Go 코드로 생성해준다. 코드로 생성되므로 컴파일시에 검증이 되어 타입 불일치와 같은 문제를 컴파일 타임에 검증할 수 있다. 또한 Validation, Mutation Hook을 설정할 수 있으며 Compile/Runtime 모두 설정이 가능하다.

## ORM 이외의 대안은?

Go는 기본적으로 강력한 템플릿 엔진이 탑재되어 있다. 이를 통해 조건부 쿼리를 작성할 수 있다. 쿼리를 작성하고 Database Driver로 해당 쿼리를 보내고 결과를 타입으로 매핑한다. `sqlc`와 같은 라이브러리도 대안이 될 수 있다.

```gotpl
{{ .Partial }}
{{ if }} {{ end }}
```
