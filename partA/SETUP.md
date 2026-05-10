# Part A — Setup

## Сонгосон API

**JSONPlaceholder** — `https://jsonplaceholder.typicode.com`

Энэ нь хуурамч REST API бөгөөд хөгжүүлэгчдэд зориулсан үнэгүй онлайн тестийн орчин юм.
CRUD үйлдэл бүрийг дэмждэг. Auth шаардахгүй.

---

## Brief (товч тайлбар)

| Талбар        | Утга                                          |
|---------------|-----------------------------------------------|
| Нэр           | JSONPlaceholder                               |
| URL           | https://jsonplaceholder.typicode.com          |
| Auth          | Байхгүй (No authentication required)         |
| Rate limit    | Байхгүй (No rate limiting)                   |
| Формат        | JSON (application/json)                       |
| Боломжит resources | /posts, /users, /comments, /todos, /albums |

---

## Үндсэн endpoint-үүд

```
GET    /posts          — Бүх нийтлэл авах
GET    /posts/:id      — ID-аар нийтлэл авах
POST   /posts          — Шинэ нийтлэл үүсгэх
PUT    /posts/:id      — Нийтлэл шинэчлэх
DELETE /posts/:id      — Нийтлэл устгах

GET    /users          — Бүх хэрэглэгч авах
GET    /users/:id      — ID-аар хэрэглэгч авах
```

---

## Environment Variables

| Хувьсагч       | dev утга                                      |
|----------------|-----------------------------------------------|
| `baseUrl`      | https://jsonplaceholder.typicode.com          |
| `createdPostId`| (POST-ийн response-оос автоматаар тохируулагдана) |
| `randomUserId` | (pre-request script-аас үүсгэгдэнэ)          |
| `newPostTitle` | (pre-request script-аас үүсгэгдэнэ)          |

---

## Postman Workspace & Collection

- **Workspace:** F.CSM311 — Lab14
- **Collection:** Munguntuya — JSONPlaceholder
- **Environments:** dev, staging, prod (зөвхөн dev бөглөсөн)
