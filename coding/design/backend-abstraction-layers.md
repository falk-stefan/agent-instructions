# Backend Abstraction Layers

How a request travels from the API boundary to the response. Independent of language and framework.

```
Controller → Service → View → Mapper → DTO
```

| Layer      | Knows about                   | Must not                        |
|------------|-------------------------------|---------------------------------|
| Controller | API framework, DTOs, services | Touch entities or the database  |
| Service    | Views, entities, mappers      | Build query definitions inline  |
| View       | Entities, the data layer      | Contain business logic          |
| Mapper     | Entities, DTOs                | Compute business facts or query |
| DTO        | Nothing                       | Reference entities              |

## Layout

Every layer has its own top-level folder, named after the layer. An import path names the layer it
reaches into.

```
src/
├── controller/
│   └── article.controller
├── service/
│   └── article/
│       ├── article.meta
│       ├── article.access
│       ├── article.read
│       ├── article.read.spec
│       ├── article.publish
│       └── article.helpers
├── view/
│   └── article.view
├── mapper/
│   └── article.mapper
├── dto/
│   └── article.dto
└── db/
    ├── entity/
    └── migrations/
```

| Kind         | File                                | Exports                    |
|--------------|-------------------------------------|----------------------------|
| Controller   | `controller/<name>.controller`      | `<Name>Controller`         |
| Operation    | `service/<domain>/<domain>.<op>`    | operations                 |
| Meta load    | `service/<domain>/<domain>.meta`    | `get<Entity>Meta`          |
| Access check | `service/<domain>/<domain>.access`  | `assert<Entity>…ElseThrow` |
| Helpers      | `service/<domain>/<domain>.helpers` | shared by the op files     |
| View         | `view/<entity>.view`                | `<Entity>View`             |
| Mapper       | `mapper/<name>.mapper`              | `to<Dto>`, `to<Dto>s`      |
| DTO          | `dto/<name>.dto`                    | `<Dto>`                    |

- Adapt file name casing and extensions to the language's conventions.
- Tests sit next to the file they test.
- `db/` holds the schema only: entities, migrations, seed data.

## Who calls whom

| Layer      | May call                           | Why                                       |
|------------|------------------------------------|-------------------------------------------|
| Controller | Services                           | The controller only describes the API.    |
| Service    | Services, Views, entities, mappers | It owns the use case.                     |
| View       | Views                              | Views compose; they never run queries.    |
| Mapper     | Mappers                            | Pure conversion. No I/O and no decisions. |

- No dependency cycles between service domains. If two domains need each other, move the shared
  part into a third one.

## Controller

- Describes the API only: route, security, validation, parameters, return type.
- One line per endpoint: delegate to a service with the caller and the inputs.
- No entity queries, no mappers, no not-found handling.

## Service

- Owns the use case: authorize, load, apply business rules, write, map.
- Stateless operations. No state held between calls.
- Names are descriptive (`getSection`, `createSection`), not disambiguated by a namespace.
- Takes the caller as its first argument when the operation depends on them.
- Returns DTOs to the controller.

### Operation files

Every domain has one file per operation: `<domain>.<op>` with its test next to it. This keeps
source and test files small.

`<op>` follows the endpoint vocabulary of [rest-api-model.md](rest-api-model.md), not HTTP methods. Every
endpoint has exactly one op file, derived from its URL:

| Endpoint                                      | `<op>`                                      |
|-----------------------------------------------|---------------------------------------------|
| `GET` one / list                              | `read`                                      |
| `POST` on the collection                      | `create`                                    |
| `PATCH` / `PUT` on the entity                 | `update`                                    |
| `DELETE` on the entity                        | `delete`                                    |
| Order: `PATCH` on the collection with `order` | `order`                                     |
| Action: `POST /{id}/<verb>`                   | `<verb>` (`publish`, `submit`)              |
| Part: `/{id}/<part>`                          | `<part>` (`cover-image`), one file per part |

- Helpers shared across a domain's op files live in `<domain>.helpers`.
- Infrastructure (media, storage, external APIs) is a service domain like any other.

### Meta load

`get<Entity>Meta(publicId)` resolves a client-facing id and loads the small set of fields that
decisions need (id, owner, status, pricing). It throws not-found when nothing matches.

- Lives in `<domain>.meta`. Any domain may use it, with or without an access check.

### Access check

The first step of a service that touches a protected entity. Authorize explicitly before loading
anything heavy. A failed query can't tell the client *why*; an explicit check can (not found vs. not
purchased vs. not the owner).

```
meta = getArticleMeta(articleId)        // small fetch: id, owner, status, pricing
assertArticleAccessElseThrow(meta, user) // or assertArticleOwnerElseThrow
```

- One way only: an `assert…ElseThrow` on the meta.
- No access rules hidden in queries (filtering by owner as a check).
- No checks around the assert: bypasses (e.g. admin) live inside it, not in the caller.
- Lives in `<domain>.access`. Other domains import it.
- Not-found and not-allowed both throw typed API errors. Never a generic error.

### Business logic

- Facts derived from the caller or from other data (`isOwner`, `isPurchased`) are computed in the
  service and passed to the mapper.
- Writes happen in the service. After a write, reload through the View and map, so every endpoint
  returns the same shape.

## View

A View is a reusable, named query definition. It is the one place that defines what a fetch loads:
relations, fields, filters, order. Change it there instead of in every copy.

- Views compose. Nested relations reference other Views instead of repeating them.
- Every query that loads relations goes through a View. No relation trees inside services.
- Parameters (language, owner) are function arguments: `ArticleView({ languageId })`.
- Never share mutable query definitions. Build a fresh one on every call.
- One View per shape, not per endpoint. If two endpoints need the same data, they share the View.

## Mapper

- Pure function: `to<Dto>(entity, facts?) → Dto`. No queries, no I/O, no decisions.
- Composes other mappers.
- Caller-dependent values come in as an explicit `facts` argument, not as raw inputs (user ids,
  purchase lists) the mapper has to interpret.
- Reads loaded relations directly, not data-layer internals.

## DTO

- The API contract. The API spec is generated from it.
- Plain data types, with validation on input DTOs.
- Shape and naming follow [rest-api-model.md](rest-api-model.md).

## End-to-end

```
// controller
return getArticle(caller, id, languageId)

// service/article/article.read
getArticle(user, id, languageId):
  meta = getArticleMeta(id)
  assertArticleAccessElseThrow(meta, user)

  article = query(ArticleView({ languageId: languageId ?? meta.languageId }), where id = meta.id)
  if article is missing: throw NotFound("article")

  isOwner = meta.ownerId == user.id
  isPurchased = isArticlePurchased(user, meta.id)
  return toArticle(article, { isOwner, isPurchased })
```
