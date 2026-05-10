# Бие Даалт 14 — Integration & API Testing

**Сургууль:** ШУТИС — МХТС  
**Хичээл:** F.CSM311 Программ хангамжийн бүтээлт  
**API:** JSONPlaceholder (`https://jsonplaceholder.typicode.com`)  
**Хэрэгсэл:** Postman, Newman, GitHub Actions

---

## Хурдан эхлүүлэх (Quick Start)

### Шаардлага

- [Node.js](https://nodejs.org) v18+
- npm

### Newman суулгах

```bash
npm install -g newman newman-reporter-htmlextra
```

### Local дээр тест ажиллуулах

```bash
newman run postman/collection.json -e postman/env.dev.json
```

### HTML report үүсгэх

```bash
newman run postman/collection.json \
  -e postman/env.dev.json \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export reports/api.html
```

Дараа нь `reports/api.html` файлыг browser-т нээнэ.

---

## Repo-ийн бүтэц

```
bie-daalt-14/
├── README.md
├── REFLECTION.md
├── partA/
│   ├── SETUP.md          — API тайлбар, auth, base URL
│   └── screenshot.png    — Эхний амжилттай request-ийн screenshot
├── postman/
│   ├── collection.json   — 8 request, 3 folder-аар бүлэглэсэн
│   ├── env.dev.json      — dev environment
│   └── env.ci.json       — CI environment
├── .github/
│   └── workflows/
│       └── api-tests.yml — GitHub Actions CI
└── reports/
    └── api.html          — Newman HTML report
```

---

## Collection-ийн агуулга

### Folder: Posts (5 request)

| # | Нэр                  | Метод  | Зорилго                        |
|---|----------------------|--------|--------------------------------|
| 1 | GET All Posts        | GET    | Жагсаалт авах — happy path     |
| 2 | GET Post by ID       | GET    | ID-аар авах, schema шалгах     |
| 3 | POST Create Post     | POST   | Үүсгэх, createdPostId chain    |
| 4 | PUT Update Post      | PUT    | {{createdPostId}} ашиглан update |
| 5 | DELETE Post          | DELETE | Устгах                         |

### Folder: Users (2 request)

| # | Нэр              | Метод | Зорилго                         |
|---|------------------|-------|---------------------------------|
| 6 | GET All Users    | GET   | Жагсаалт авах                   |
| 7 | GET User by ID   | GET   | email бизнес дүрэм шалгах      |

### Folder: Error Cases (1 request)

| # | Нэр                         | Метод | Зорилго                   |
|---|-----------------------------|-------|---------------------------|
| 8 | GET Non-existent Post (404) | GET   | 404 negative test шалгах  |

---

## Тестийн статистик

| Assertion төрөл | Тоо |
|-----------------|-----|
| Status code     | 8   |
| Response time   | 5   |
| Schema/property | 7   |
| Type check      | 4   |
| Business rule   | 3   |
| Header          | 1   |
| **Нийт**        | **27+** |

---

## CI/CD

Push хийх бүрт GitHub Actions автоматаар Newman ажиллуулна.  
**Actions tab** → `API Tests (Newman)` → artifacts-аас `api-test-report` татаж үзнэ.
