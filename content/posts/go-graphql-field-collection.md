---
title: GraphQL Field Collection
date: 2021-07-31T00:52:51+09:00
tags: ["go", "graphql"]
description: "GraphQL의 N+1 문제를 해결하기 위한 Field Collection 기법"
draft: true
---

GraphQL은 N+1 문제가 발생할 수 있다. 이를 해결하기 위해서 소개되는 방법은 `Dataloader`이다. `Dataloader` 이외에 `Field Collection`을 사용해서 N+1 문제를 해결할 수 있다.

```graphql
query posts {
  edges {
    node {
      id
      comments {
        edges {
          node {
            id
            content
            createdAt
          }
        }
      }
    }
  }
}
```

위와 같은 쿼리에서 `comments` 리졸버를 호출할 때 N+1 쿼리가 발생할 수 있다. 이를 해결하기 위해서는 `posts`를 위한 `DataLoader`를 구현하는 방법이 있을 수 있다. 다른 방법으로는 `Field Collection` 기법을 사용할 수 있다.

`Field Collection`이란 GraphQL 쿼리를 분석할 때 어떤 필드들이 요청되는지를 파악하여 넘겨주는 방식을 의미한다. 이 `Field Collection`에 어떠한 필드들이 담겨 있는지 리졸버 처리 이전에 파악이 가능하다. 이를 통해 N+1이 발생할 수 있는 쿼리라면 해당 데이터들을 사전에 불러들이는 방식으로 N+1 쿼리를 회피하는 것이 가능하다.

예를 들어 위 쿼리에서 `comments` 가 쿼리에 포함되어 있다면 어떨까? 중첩된 컬렉션을 호출할 수 있으므로 N+1 문제가 발생할 수 있다. 따라서 `posts` 리졸버에서는 `comments` 필드가 포함되어 있는 쿼리가 발생하면, 미리 `comments`를 불러들인 후 결합하는 방식을 사용할 수 있다. ORM을 사용하고 있다면 각 ORM에서 지원하는 Eager Loading을 사용하거나, SQL을 직접 호출한다면 JOIN을 사용할 수 있다.
