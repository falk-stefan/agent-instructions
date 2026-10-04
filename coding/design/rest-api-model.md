# REST API Model

How a REST API models entities and collections. Independent of language and framework.

## Entities are first-class

Every entity a client can create has its own top-level collection. It keeps that path no matter
which parent it belongs to.

```
/v1/articles
/v1/sections
/v1/comments
```

- Paths are plural and kebab-case: `/v1/article-pricing`, `/v1/reading-lists`.
- An entity is addressed by its public id: `/v1/sections/{id}`.
- A singleton per client type has a fixed id: `/v1/client-configs/web`, `/v1/client-configs/mobile`.

## Only an id follows a collection

> **Warning:** the path segment after a collection is always `{id}`. Any other word there is read
> as an id: `/v1/sections/reorder` is the section with id `reorder`.

Operations on a collection use its path and a method. Operations on an entity go after its id.

```http
PATCH /v1/sections                 → the collection
POST  /v1/articles/{id}/submit     → one entity
```

## The parent is a field

The parent's id travels with the request: in the body on create, in the query on list.

```http
POST /v1/sections
{ "articleId": "a1", "title": "Introduction" }

GET /v1/sections?articleId=a1
```

## Methods

| Method   | On             | Does                           | Body                  | Returns                    | Use it to                          |
|----------|----------------|--------------------------------|-----------------------|----------------------------|------------------------------------|
| `GET`    | collection     | Lists it                       | —                     | `Paged<Entity>`            | Read a list                        |
| `GET`    | entity or part | Reads it                       | —                     | `Entity`                   | Read one thing                     |
| `POST`   | collection     | Creates an entity              | `Create<Entity>`      | `Entity`                   | Add something new                  |
| `POST`   | entity + verb  | Runs an action                 | as needed             | as needed                  | Change state beyond a field        |
| `PATCH`  | entity         | Changes the fields in the body | `Update<Entity>`      | `Entity`                   | Edit some fields                   |
| `PATCH`  | collection     | Sets its order                 | `Update<Entity>Order` | the list, in the new order | Set the order                      |
| `PUT`    | part           | Sets it as a whole             | `Update<Part>`        | the part                   | Replace a file or a setting        |
| `DELETE` | entity or part | Deletes it                     | —                     | nothing                    | Remove it                          |

- `GET`, `PUT` and `DELETE` can be repeated with the same result. `POST` can't.
- A `GET` never changes data.

## Order

A collection's order is set on the collection, with the full list of ids.

```http
PATCH /v1/sections
{ "articleId": "a1", "order": ["s3", "s1", "s2"] }
```

## Children stay in their collection

An entity in a list or a response carries its own fields. Its children are read from their own
collection.

```http
GET /v1/articles               → articles
GET /v1/sections?articleId=a1  → that article's sections
```

## One per parent

An entity that exists at most once per parent is looked up through its parent. Everything else
uses its id.

```http
GET   /v1/article-pricing?articleId=a1   → ArticlePricing | null
PATCH /v1/article-pricing/{id}
```

- The lookup returns `null` while none exists yet.

## Parts of an entity

A part has no meaning on its own. It is read and shown as a piece of its entity: a file, a location,
the translations, a gallery. A part gets a path under the entity.

```http
PUT    /v1/articles/{id}/cover-image
DELETE /v1/articles/{id}/location
GET    /v1/sections/{id}/blocks
```

A part that is a list works like a collection under the entity.

```http
POST   /v1/sections/{id}/blocks
PATCH  /v1/sections/{id}/blocks/{blockId}
PATCH  /v1/sections/{id}/blocks            { "order": ["b2", "b1"] }
```

## Actions

A state change that is more than setting a field is a `POST` to a verb under the entity.

```http
POST /v1/articles/{id}/submit
POST /v1/articles/{id}/publish
```

## Computed resources

A computed resource is calculated on request and never stored. Its collection is named after the
result. `POST` computes it.

```http
POST /v1/directions   { waypoints }
POST /v1/price-quotes { items }
```

## The caller's data

A path under `/v1` means the caller's data. A collection without a filter returns what belongs to
the caller.

```http
GET /v1/articles        → the caller's articles
GET /v1/articles/{id}   → any article the caller may see
```

## The caller

`/v1/user` is the caller, identified by the token. The caller's parts live beneath it.

```http
GET   /v1/user
PUT   /v1/user/image
PATCH /v1/user/settings
```

## Scopes

The prefix sets whose data a path covers.

| Prefix         | Covers                                     |
|----------------|--------------------------------------------|
| `/v1/*`        | The caller's data, or one entity by its id |
| `/v1/search/*` | The public catalog                         |
| `/v1/admin/*`  | Everyone's data, for admins only           |

`/v1/admin` holds lists and actions that span all users. An entity keeps its own path.

```http
GET  /v1/admin/users?status=pending
POST /v1/admin/users/{id}/verify
POST /v1/articles/{id}/publish
```

## Permissions

- Each endpoint sets who may call it. Everyone else gets 403.

## Query parameters

Query parameters on a list narrow, sort and page it. They never change the shape of an element.

| Kind   | Parameters            | Example                          |
|--------|-----------------------|----------------------------------|
| Parent | `<parent>Id`          | `?articleId=a1`                  |
| Filter | named after the field | `?status=PUBLISHED&title=intro`  |
| Sort   | `orderBy`, `order`    | `?orderBy=createdAt&order=desc`  |
| Page   | `index`, `size`       | `?index=0&size=50`               |
| Locale | `languageCode`        | `?languageCode=de`               |

- A list returns `Paged<T>`: `{ elements, page }`.
- An ordered list under one parent, or a small fixed reference list, returns `T[]`:
  `GET /v1/sections?articleId=a1`, `GET /v1/languages`.
- Search with many filters is a `POST` with the filter in the body: `POST /v1/search/articles`.

## Responses

Each endpoint returns one fixed type. Data only the owner may see gets its own endpoint.

```http
GET /v1/articles/{id}          → Article
GET /v1/articles/{id}/stats    → owner or admin, else 403
```

## Errors

| Status | When                                                       |
|--------|------------------------------------------------------------|
| 400    | The request is invalid                                     |
| 403    | The entity belongs to someone else                         |
| 404    | No entity has this id                                      |
| 409    | The entity's state doesn't allow it, e.g. submitting twice |

## One group per entity

Each endpoint is grouped (tagged) by its entity in the API spec. A generated client then has one
class per entity.

- A part carries its entity's group: `GET /v1/sections/{id}/blocks` → `Section`.
- The public catalog under `/v1/search` carries `Search`.
- A computed resource carries the name of its result: `POST /v1/directions` → `Directions`.
