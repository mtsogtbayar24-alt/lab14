# REFLECTION — Бие Даалт 14

**Хичээл:** F.CSM311 Программ хангамжийн бүтээлт  
**Сэдэв:** Integration & API Testing — Postman + Newman

---

## 1. Аль assertion (тест) хамгийн их үнэ цэнэтэй санагдсан вэ? Яагаад?

Хамгийн их үнэ цэнэтэй assertion бол **бизнес дүрмийн тест** — тухайлбал GET User by ID дахь email формат шалгах тест:

```javascript
pm.expect(json.email).to.be.a("string").and.match(/@/);
```

Үнэ цэнэтэй байгаа шалтгаан нь: зөвхөн "200 эргэж ирсэн үү" гэдгийг шалгахаас гадна, response-д байгаа өгөгдөл нь бизнесийн хувьд утга учиртай эсэхийг баталгаажуулдаг. Жишээ нь, API нь статус 200 буцааж, body нь ямар нэг JSON байлаа гэсэн ч, email талбар нь "@" тэмдэгтгүй байвал тухайн өгөгдлийг системд ашиглах боломжгүй. Энэ төрлийн тест нь API contract-ийн гүнийг шалгадаг — зөвхөн "ажиллаж байна уу" гэдгийг биш, "зөв ажиллаж байна уу" гэдгийг шалгадаг.

Status code assertion нь хялбар ч чухал. Гэхдээ status 200 байхад л тест pass болох тохиолдолд response body-д хог өгөгдөл байж болно. Тиймээс бизнес дүрмийн assertion нь "API нь зөв contract баримталж байна" гэдгийг баталдаг учраас хамгийн их үнэ цэнэтэй гэж үзэж байна.

---

## 2. Negative test жишээгээ дэлгэрэнгүй тайлбарла — яг ямар алдааг олох вэ?

**GET Non-existent Post (404)** тест:

```
GET {{baseUrl}}/posts/999999
```

Энэ тест нь дараах алдааг илрүүлдэг:

- **Алдаа 1 — 500 Internal Server Error буцаах:** Зарим API нь байхгүй ID-г хайхад database error-т орж, 404-ийн оронд 500 буцаадаг. Энэ нь аюултай — дотоод алдааны мэдээллийг external-д задруулдаг.

- **Алдаа 2 — 200 буцааж, хоосон эсвэл null буцаах:** Маш ноцтой алдаа. Client код нь `if (response.status === 200)` гэж шалгаад, `null` body-тай ажиллахад NullPointerException гардаг.

- **Алдаа 3 — Алдааны тайлбар байхгүй:** 404 буцааж байлаа ч body нь хоосон байвал client нь "яагаад олдсонгүй" гэдгийг мэдэхгүй.

Энэ тест нь "алдааны зам ч бас contract" гэдэг зарчмыг хэрэгжүүлдэг. Зөвхөн happy path шалгавал, эдгээр алдаанууд production орчинд л гарч ирдэг.

---

## 3. Postman дотор амжилттай ажиллаж байсан тест Newman-д орвол fail болсон уу? Яагаад?

Энэ лабораторид JSONPlaceholder ашигласан тул нэмэлт auth шаардлагагүй байсан, тиймээс Postman болон Newman хооронд тест fail болох тохиолдол гараагүй.

Гэхдээ ерөнхийдөө энэ нь нийтлэг асуудал бөгөөд дараах шалтгаануудаас үүсдэг:

- **Environment variable алга:** Postman-д manually тохируулсан env variable-ийг Newman-д `-e` flag-гаар өгөөгүй тохиолдолд `undefined` болдог.
- **Pre-request script-ийн хамаарал:** Нэг request-ийн Test script-т тохируулсан variable нь дараагийн request-д байхгүй байдаг, хэрэв collection order өөрчлөгдвөл.
- **SSL certificate:** Corporate network-д Newman нь SSL verify хийдэг ч Postman skip хийдэг тохиолдол байдаг — `--insecure` flag хэрэгтэй болдог.

Энэ туршлагаас сурсан зүйл: Newman-д local-д эхлээд ажиллуулаад, CI-д push хийх хэрэгтэй.

---

## 4. Token эсвэл secret-ыг хэрхэн зохицуулсан вэ? (env-ийн соёл)

JSONPlaceholder нь auth token шаардахгүй тул энэ лабораторид token хадгалах шаардлага гараагүй. Гэхдээ environment-ийн соёлыг дараах байдлаар хэрэгжүүлсэн:

1. **`env.dev.json`** — `baseUrl` болон ажиллах утгуудыг хадгалсан. Хэрэв token байсан бол `REPLACE_THIS_WITH_YOUR_TOKEN` гэсэн placeholder ашиглаад `.gitignore`-д нэмэх байсан.

2. **`env.ci.json`** — CI-д зориулсан тусдаа file. Хэрэв API token шаардагдсан бол `${{ secrets.API_TOKEN }}` хэлбэрээр GitHub Secrets-аас авч, workflow дотор `sed` командаар substitute хийх байсан:
   ```yaml
   - run: sed -i 's/REPLACE_TOKEN/${{ secrets.API_TOKEN }}/' postman/env.ci.json
   ```

3. **`.gitignore`-д нэмэх:** Real token-тэй env file-г хэзээ ч commit хийхгүй.

Энэ арга нь "secret нь code-д байж болохгүй" гэдэг DevSecOps-ийн үндсэн зарчмыг баримталдаг.

---

## 5. API өөрчлөгдвөл collection-ийн аль хэсэг ХАМГИЙН ИХ эвдрэх вэ? Үүнийг яаж бууруулах вэ?

**Хамгийн эмзэг газар:** Chain хийсэн request-үүд — тухайлбал POST Create Post → PUT Update Post.

Хэрэв POST `/posts`-ийн response schema өөрчлөгдөж, `id` талбарын нэр `postId` болвол:
```javascript
pm.environment.set("createdPostId", json.id);  // undefined болно
```
PUT request нь `{{createdPostId}}` = `undefined` ашиглан `/posts/undefined` руу request явуулж fail болно. Энэ нь нэг цэгийн өөрчлөлтөөс олон тест cascade fail болох жишээ.

**Мөн эмзэг газар:** Business rule assertion-ууд — `pm.expect(json.id).to.eql(1)` гэх мэт hardcode утга. Хэрэв API-ийн seed data өөрчлөгдвөл эдгээр тест fail болно.

**Бууруулах арга:**
1. **Schema validation ашиглах** — тодорхой утга биш, бүтцийг шалгах (Ajv library эсвэл Postman-ийн JSON Schema validation).
2. **Dynamic assertion** — `pm.expect(json.id).to.be.a("number")` нь тодорхой утгаас илүү тогтвортой.
3. **API versioning** — endpoint-д `/v1/posts` гэх мэт version оруулах нь breaking change-г хойшлуулдаг.
4. **Collection-д contract test нэмэх** — "API-ийн хариу ямар талбартай байх ёстой" гэдгийг тусад нь document хийж тест болгох.

Эцэст нь, тестийн "эмзэг газар" нь зөвхөн тест бичсэн газар биш — design-ийн шийдвэрийг тусгасан газар юм. Тест fail болох нь API-ийн contract өөрчлөгдсөн дохио болж, зориудаар ч байж болно.
