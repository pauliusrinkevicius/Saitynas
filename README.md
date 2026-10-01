# Tyrimas REST API

REST API sistema, skirta apklausų kūrimui, klausimų valdymui ir atsakymų pateikimui.

Projektas realizuotas naudojant **Node.js**, **Express.js** ir **PostgreSQL**. API dokumentacijai naudojamas **OpenAPI / Swagger**, o API testavimui naudojamas **Postman**.

---

## Projekto paskirtis

Pagrindiniai taikomosios srities objektai:

```text
Apklausa
   ↓ 1:N
Klausimas
   ↓ 1:N
Atsakymas
```

Sistema leidžia kurti, peržiūrėti, atnaujinti ir šalinti apklausas, klausimus bei atsakymus. Taip pat realizuotas puslapiavimas, filtravimas, hypermedia nuorodos ir hierarchinis endpointas, apimantis visus tris objektus.

---

## Naudotos technologijos

- Node.js
- Express.js
- PostgreSQL
- `pg`
- dotenv
- cors
- swagger-jsdoc
- swagger-ui-express
- nodemon
- OpenAPI 3.0
- Swagger UI
- Postman
- Git / GitHub

---

## Projekto struktūra

```text
saitynas/
│
├── database/
│   ├── schema.sql
│   └── seed.sql
│
├── src/
│   ├── config/
│   │   └── db.js
│   ├── controllers/
│   │   ├── apklausaController.js
│   │   ├── klausimasController.js
│   │   └── atsakymasController.js
│   ├── routes/
│   │   ├── apklausaRoutes.js
│   │   ├── klausimasRoutes.js
│   │   └── atsakymasRoutes.js
│   ├── utils/
│   │   └── validation.js
│   ├── app.js
│   └── swagger.js
│
├── .env
├── .gitignore
├── package.json
├── package-lock.json
└── README.md
```

---

## Sistemos architektūra

```text
Postman / Swagger / klientas
            ↓
          HTTP
            ↓
          app.js
            ↓
          routes
            ↓
        controllers
            ↓
       validation.js
            ↓
        pool.query()
            ↓
        PostgreSQL
            ↓
      JSON + HTTP statusas
```

- `app.js` registruoja middleware, route'us ir paleidžia serverį.
- `routes` aprašo URL, HTTP metodus ir Swagger specifikaciją.
- `controllers` vykdo validaciją, SQL užklausas ir formuoja atsakymus.
- `validation.js` turi bendras validacijos funkcijas.

---

## Duomenų bazė

Naudojama PostgreSQL duomenų bazė `tyrimas_db`.

### `apklausa`

```text
id
pavadinimas
aprasymas
sukurta
```

### `klausimas`

```text
id
apklausa_id
klausimo_tekstas
a_variantas
b_variantas
c_variantas
d_variantas
```

### `atsakymas`

```text
id
klausimas_id
pasirinktas_variantas
pateikta
```

`pasirinktas_variantas` reikšmės:

```text
1 = A
2 = B
3 = C
4 = D
```

Ryšiai:

```text
Apklausa 1:N Klausimas
Klausimas 1:N Atsakymas
```

Naudojamas `ON DELETE CASCADE`, todėl šalinant tėvinį resursą pašalinami ir susiję priklausomi įrašai.

---

## Projekto paleidimas

### 1. Įdiegti priklausomybes

```bash
npm install
```

### 2. Sukurti PostgreSQL duomenų bazę

```text
tyrimas_db
```

### 3. Paleisti DB schemą

```text
database/schema.sql
```

### 4. Įkelti demonstracinius duomenis

```text
database/seed.sql
```

### 5. Sukurti `.env`

```env
PORT=3000
DB_HOST=localhost
DB_PORT=5432
DB_NAME=tyrimas_db
DB_USER=postgres
DB_PASSWORD=JUSU_POSTGRESQL_SLAPTAZODIS
```

Tikras slaptažodis neturi būti laikomas Git saugykloje.

`.gitignore` rekomenduojama turėti:

```text
node_modules/
.env
.idea/
```

### 6. Paleisti serverį

```bash
npm run dev
```

Serveris:

```text
http://localhost:3000
```

Swagger:

```text
http://localhost:3000/api-docs
```

---

## 15 pagrindinių REST API metodų

### Apklausos

```text
GET     /api/apklausos
GET     /api/apklausos/:id
POST    /api/apklausos
PUT     /api/apklausos/:id
DELETE  /api/apklausos/:id
```

### Klausimai

```text
GET     /api/apklausos/:apklausaId/klausimai
GET     /api/apklausos/:apklausaId/klausimai/:id
POST    /api/apklausos/:apklausaId/klausimai
PUT     /api/apklausos/:apklausaId/klausimai/:id
DELETE  /api/apklausos/:apklausaId/klausimai/:id
```

### Atsakymai

```text
GET     /api/klausimai/:klausimasId/atsakymai
GET     /api/klausimai/:klausimasId/atsakymai/:id
POST    /api/klausimai/:klausimasId/atsakymai
PUT     /api/klausimai/:klausimasId/atsakymai/:id
DELETE  /api/klausimai/:klausimasId/atsakymai/:id
```

---

## Hierarchinis endpointas

Papildomai realizuotas endpointas, apimantis visą hierarchiją:

```http
GET /api/apklausos/:apklausaId/klausimai/:klausimasId/atsakymai
```

Pavyzdys:

```http
GET /api/apklausos/1/klausimai/1/atsakymai
```

Šis endpointas viename response sujungia:

```text
Apklausa
+
Klausimas
+
Atsakymai
```

Taip realizuojamas URL scoping ir reikalavimas grąžinti resursą, sukonstruotą iš kelių esybių.

---

## Puslapiavimas

Puslapiavimas realizuotas pagrindiniuose LIST endpointuose.

### Apklausos

```http
GET /api/apklausos?page=1&limit=5
```

### Klausimai

```http
GET /api/apklausos/1/klausimai?page=1&limit=5
```

### Atsakymai

```http
GET /api/klausimai/1/atsakymai?page=1&limit=5
```

Naudojami query parametrai:

```text
page
limit
```

`limit` leidžiamas intervale `1–100`.

Pavyzdinė response struktūra:

```json
{
  "page": 1,
  "limit": 5,
  "total": 10,
  "totalPages": 2,
  "data": []
}
```

---

## Filtravimas

### Apklausų filtravimas

```http
GET /api/apklausos?pavadinimas=programavimo
```

### Klausimų filtravimas

```http
GET /api/apklausos/1/klausimai?tekstas=programavimo
```

### Atsakymų filtravimas

```http
GET /api/klausimai/1/atsakymai?variantas=1
```

Tekstiniam filtravimui PostgreSQL naudojamas `ILIKE`.

Puslapiavimą ir filtravimą galima naudoti kartu:

```http
GET /api/apklausos?page=1&limit=5&pavadinimas=programavimo
```

---

## Hypermedia

Esminiams resursams grąžinamas `_links` objektas.

Pavyzdys:

```json
{
  "id": 1,
  "pavadinimas": "Programavimo apklausa",
  "_links": {
    "self": "/api/apklausos/1",
    "klausimai": "/api/apklausos/1/klausimai"
  }
}
```

Klausimo resurse pateikiamos nuorodos į:

```text
self
apklausa
atsakymai
```

Atsakymo resurse pateikiamos nuorodos į:

```text
self
collection
```

Hierarchiniame response taip pat pateikiamos nuorodos į susijusius resursus.

---

## Validacija

Bendra validacijos logika laikoma:

```text
src/utils/validation.js
```

Naudojamos funkcijos:

```javascript
parsePositiveInteger(...)
isNonEmptyString(...)
```

### Blogi ID

Tokios reikšmės:

```text
abc
-1
0
1.5
```

grąžina:

```text
400 Bad Request
```

Teisingo formato, bet neegzistuojantis ID grąžina:

```text
404 Not Found
```

### Blogas request body

Pavyzdys:

```json
{
  "pavadinimas": 123
}
```

grąžina:

```text
422 Unprocessable Entity
```

Neteisingas atsakymo variantas:

```json
{
  "pasirinktas_variantas": 8
}
```

grąžina `422`.

---

## HTTP statuso kodai

| Statusas | Reikšmė |
|---|---|
| `200 OK` | Sėkmingas GET arba PUT |
| `201 Created` | Sėkmingas POST |
| `204 No Content` | Sėkmingas DELETE be response body |
| `400 Bad Request` | Neteisingas path/query parametras |
| `404 Not Found` | Resursas nerastas |
| `422 Unprocessable Entity` | Neteisingas request body |
| `500 Internal Server Error` | Netikėta serverio arba DB klaida |

Vartotojo įvesties klaidos validuojamos prieš SQL vykdymą, todėl jos neturi virsti `500` klaidomis.

---

## SQL saugumas

Naudojamos parametrizuotos SQL užklausos.

Pavyzdys:

```javascript
const result = await pool.query(
  'SELECT * FROM apklausa WHERE id = $1',
  [id]
);
```

Tai sumažina SQL injection riziką, nes vartotojo duomenys nėra tiesiogiai jungiami su SQL tekstu.

---

## Swagger / OpenAPI

Swagger dokumentacija pasiekiama:

```text
http://localhost:3000/api-docs
```

Dokumentacijoje aprašyti:

- HTTP metodai;
- path parametrai;
- query parametrai;
- request body;
- response struktūros;
- statuso kodai;
- pagination;
- filtering;
- hypermedia;
- hierarchinis endpointas.

Swagger UI leidžia išbandyti endpointus naudojant `Try it out`.

---

## Postman testavimas

Parengtas Postman requestų rinkinys pagrindiniams API metodams ir klaidų scenarijams.

Tikrinami statusai:

```text
200
201
204
400
404
422
```

Pavyzdys:

```javascript
pm.test("Statusas yra 200", function () {
    pm.response.to.have.status(200);
});
```

Taip pat testuojama:

- puslapiavimo struktūra;
- filtravimo rezultatai;
- hypermedia `_links`;
- hierarchinis response;
- neteisingi ID;
- neteisingi payload.

Collection Runner leidžia greitai pademonstruoti API metodų veikimą.

---

## REST principų taikymas

API URL aprašo resursus, o veiksmą nusako HTTP metodas.

Teisingi pavyzdžiai:

```http
GET /api/apklausos
POST /api/apklausos
GET /api/apklausos/1
GET /api/apklausos/1/klausimai
```

Nenaudojami veiksmais pavadinti URL, tokie kaip:

```text
/createApklausa
/getApklausa
/deleteKlausimas
```

API yra stateless: kiekviena užklausa turi visą informaciją, reikalingą jai apdoroti.

---

## Projekto būklė

- [x] 3 taikomosios srities objektai
- [x] 1:N hierarchiniai ryšiai
- [x] 15 CRUD + LIST API metodų
- [x] Hierarchinis endpointas per visus 3 objektus
- [x] Resursas iš kelių esybių
- [x] PostgreSQL duomenų bazė
- [x] Prasmingi seed duomenys
- [x] REST URI struktūra
- [x] JSON atsakymai
- [x] Teisingi HTTP statuso kodai
- [x] ID ir payload validacija
- [x] Puslapiavimas
- [x] Filtravimas
- [x] Hypermedia
- [x] OpenAPI / Swagger
- [x] Postman testavimo aplinka
- [x] GitHub saugykla
- [x] README dokumentacija

---

## Trumpa paleidimo seka

```text
1. Sukurti PostgreSQL DB tyrimas_db
2. Paleisti database/schema.sql
3. Paleisti database/seed.sql
4. Sukurti .env
5. npm install
6. npm run dev
7. Atidaryti http://localhost:3000/api-docs
8. Paleisti Postman testus
```

---

## Autorius

KTU Informatikos fakultetas  
Programų sistemos

Projektas parengtas dalykui:

**T120B165 Saityno taikomųjų programų projektavimas**
