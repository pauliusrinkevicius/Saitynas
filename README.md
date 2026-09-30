# Tyrimas REST API

REST API sistema, skirta apklausų kūrimui, klausimų valdymui ir atsakymų pateikimui.

Projektas sukurtas naudojant **Node.js**, **Express.js** ir **PostgreSQL**.  
API dokumentacijai naudojamas **OpenAPI / Swagger**, o API metodų testavimui naudojamas **Postman**.

---

## Projekto paskirtis

Sistema skirta apklausų valdymui.

Naudotojas gali:

- peržiūrėti apklausas;
- sukurti naują apklausą;
- redaguoti apklausą;
- ištrinti apklausą;
- pridėti klausimus prie apklausos;
- redaguoti ir šalinti klausimus;
- pateikti atsakymus į klausimus;
- peržiūrėti ir valdyti pateiktus atsakymus.

Vienas klausimas turi keturis galimus atsakymo variantus:

- A
- B
- C
- D

Duomenų bazėje pasirinktas variantas saugomas skaičiumi:

```text
1 = A
2 = B
3 = C
4 = D
```

---

# Naudotos technologijos

Projektui naudojamos šios technologijos:

- Node.js
- Express.js
- PostgreSQL
- `pg`
- dotenv
- cors
- swagger-jsdoc
- swagger-ui-express
- nodemon
- Postman
- OpenAPI 3.0

---

# Projekto struktūra

```text
saitynas/
│
├── database/
│   ├── schema.sql
│   └── seed.sql
│
├── src/
│   │
│   ├── config/
│   │   └── db.js
│   │
│   ├── controllers/
│   │   ├── apklausaController.js
│   │   ├── klausimasController.js
│   │   └── atsakymasController.js
│   │
│   ├── routes/
│   │   ├── apklausaRoutes.js
│   │   ├── klausimasRoutes.js
│   │   └── atsakymasRoutes.js
│   │
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

# Sistemos architektūra

Sistema sudaryta iš trijų pagrindinių dalių:

```text
Klientas
(Postman / Swagger / būsimas frontend)
        │
        │ HTTP
        ▼
Node.js + Express REST API
        │
        │ SQL užklausos
        ▼
PostgreSQL
```

Express maršrutai priima HTTP užklausas ir perduoda jas atitinkamiems controlleriams.

Controlleriai:

- gauna duomenis iš užklausos;
- atlieka duomenų validaciją;
- vykdo SQL užklausas;
- grąžina JSON atsakymus ir HTTP statuso kodus.

---

# Duomenų bazės struktūra

Sistema turi tris pagrindines lenteles:

```text
apklausa
   │
   │ 1:N
   ▼
klausimas
   │
   │ 1:N
   ▼
atsakymas
```

## `apklausa`

Saugo apklausų informaciją.

Pagrindiniai laukai:

```text
id
pavadinimas
aprasymas
sukurta
```

---

## `klausimas`

Saugo apklausos klausimus.

Pagrindiniai laukai:

```text
id
apklausa_id
klausimo_tekstas
a_variantas
b_variantas
c_variantas
d_variantas
```

`apklausa_id` yra išorinis raktas į `apklausa` lentelę.

---

## `atsakymas`

Saugo pateiktus atsakymus.

Pagrindiniai laukai:

```text
id
klausimas_id
pasirinktas_variantas
pateikta
```

`klausimas_id` yra išorinis raktas į `klausimas` lentelę.

Leidžiamos `pasirinktas_variantas` reikšmės:

```text
1
2
3
4
```

---

# Projekto paruošimas

## 1. Reikalinga programinė įranga

Norint paleisti projektą reikia:

- Node.js
- npm
- PostgreSQL
- pgAdmin 4 arba kito PostgreSQL administravimo įrankio

Postman reikalingas API testavimui.

---

# 2. Projekto priklausomybių įdiegimas

Atidarykite terminalą projekto kataloge ir vykdykite:

```bash
npm install
```

Tai įdiegs visas `package.json` faile nurodytas priklausomybes.

---

# 3. PostgreSQL duomenų bazės sukūrimas

PostgreSQL sukurkite naują duomenų bazę:

```text
tyrimas_db
```

Pavyzdžiui, naudojant pgAdmin:

```text
Servers
→ PostgreSQL
→ Databases
→ Create
→ Database
```

Duomenų bazės pavadinimas:

```text
tyrimas_db
```

---

# 4. Lentelių sukūrimas

Atidarykite:

```text
database/schema.sql
```

ir paleiskite jo turinį `tyrimas_db` duomenų bazėje.

Failas sukuria lenteles:

```text
apklausa
klausimas
atsakymas
```

bei jų tarpusavio ryšius.

---

# 5. Pradinių duomenų įkėlimas

Demonstracinius duomenis galima įkelti naudojant:

```text
database/seed.sql
```

Failą reikia vykdyti `tyrimas_db` duomenų bazėje.

`seed.sql` skirtas užpildyti duomenų bazę prasmingais testiniais duomenimis.

Dabartinis demonstracinis duomenų rinkinys gali sukurti:

```text
10 apklausų
50 klausimų
1000 atsakymų
```

Prieš įkeliant demonstracinius duomenis gali būti išvalomi ankstesni testiniai duomenys ir atstatomi ID skaitikliai.

---

# 6. Aplinkos kintamųjų nustatymas

Projekto šakniniame kataloge reikia sukurti `.env` failą.

Pavyzdys:

```env
PORT=3000

DB_HOST=localhost
DB_PORT=5432
DB_NAME=tyrimas_db
DB_USER=postgres
DB_PASSWORD=JUSU_POSTGRESQL_SLAPTAZODIS
```

Tikro PostgreSQL slaptažodžio nerekomenduojama saugoti Git repozitorijoje.

`.env` failas turi būti įtrauktas į `.gitignore`.

Pavyzdžiui:

```text
node_modules/
.env
```

---

# 7. Serverio paleidimas

Projektą galima paleisti komanda:

```bash
npm run dev
```

Sėkmingai paleidus serverį terminale turėtų būti rodomas pranešimas:

```text
Tyrimas REST API is running on port 3000
```

API bus pasiekiamas adresu:

```text
http://localhost:3000
```

---

# Serverio patikrinimas

Pagrindinis adresas:

```http
GET http://localhost:3000/
```

Turėtų grąžinti:

```json
{
    "message": "Tyrimas REST API veikia"
}
```

---

# Duomenų bazės ryšio patikrinimas

Duomenų bazės ryšį galima patikrinti:

```http
GET http://localhost:3000/api/test-db
```

Sėkmingo prisijungimo atveju grąžinamas JSON atsakymas su informacija apie duomenų bazės ryšį.

---

# REST API metodai

Sistemoje realizuota **15 pagrindinių REST API metodų**.

---

## Apklausos

### Gauti visas apklausas

```http
GET /api/apklausos
```

Sėkmingas statusas:

```text
200 OK
```

---

### Gauti apklausą pagal ID

```http
GET /api/apklausos/:id
```

Pavyzdys:

```http
GET /api/apklausos/1
```

Galimi statusai:

```text
200 OK
404 Not Found
```

---

### Sukurti apklausą

```http
POST /api/apklausos
```

Pavyzdinis JSON:

```json
{
    "pavadinimas": "Programavimo kalbų apklausa",
    "aprasymas": "Apklausa apie studentų mėgstamas programavimo kalbas"
}
```

Galimi statusai:

```text
201 Created
422 Unprocessable Entity
```

---

### Atnaujinti apklausą

```http
PUT /api/apklausos/:id
```

Pavyzdys:

```http
PUT /api/apklausos/1
```

JSON:

```json
{
    "pavadinimas": "Atnaujinta apklausa",
    "aprasymas": "Atnaujintas apklausos aprašymas"
}
```

Galimi statusai:

```text
200 OK
404 Not Found
422 Unprocessable Entity
```

---

### Ištrinti apklausą

```http
DELETE /api/apklausos/:id
```

Pavyzdys:

```http
DELETE /api/apklausos/1
```

Galimi statusai:

```text
204 No Content
404 Not Found
```

---

# Klausimai

Klausimai priklauso konkrečiai apklausai.

### Gauti visus apklausos klausimus

```http
GET /api/apklausos/:apklausaId/klausimai
```

Pavyzdys:

```http
GET /api/apklausos/1/klausimai
```

Statusas:

```text
200 OK
```

---

### Gauti klausimą pagal ID

```http
GET /api/apklausos/:apklausaId/klausimai/:id
```

Pavyzdys:

```http
GET /api/apklausos/1/klausimai/1
```

Galimi statusai:

```text
200 OK
404 Not Found
```

---

### Sukurti klausimą

```http
POST /api/apklausos/:apklausaId/klausimai
```

Pavyzdys:

```http
POST /api/apklausos/1/klausimai
```

JSON:

```json
{
    "klausimo_tekstas": "Kokia programavimo kalba jums labiausiai patinka?",
    "a_variantas": "Python",
    "b_variantas": "Java",
    "c_variantas": "C++",
    "d_variantas": "JavaScript"
}
```

Galimi statusai:

```text
201 Created
404 Not Found
422 Unprocessable Entity
```

---

### Atnaujinti klausimą

```http
PUT /api/apklausos/:apklausaId/klausimai/:id
```

Pavyzdys:

```http
PUT /api/apklausos/1/klausimai/1
```

JSON:

```json
{
    "klausimo_tekstas": "Kurią programavimo kalbą naudojate dažniausiai?",
    "a_variantas": "Python",
    "b_variantas": "Java",
    "c_variantas": "C++",
    "d_variantas": "JavaScript"
}
```

Galimi statusai:

```text
200 OK
404 Not Found
422 Unprocessable Entity
```

---

### Ištrinti klausimą

```http
DELETE /api/apklausos/:apklausaId/klausimai/:id
```

Pavyzdys:

```http
DELETE /api/apklausos/1/klausimai/1
```

Galimi statusai:

```text
204 No Content
404 Not Found
```

---

# Atsakymai

Atsakymai priklauso konkrečiam klausimui.

### Gauti visus klausimo atsakymus

```http
GET /api/klausimai/:klausimasId/atsakymai
```

Pavyzdys:

```http
GET /api/klausimai/1/atsakymai
```

Statusas:

```text
200 OK
```

---

### Gauti atsakymą pagal ID

```http
GET /api/klausimai/:klausimasId/atsakymai/:id
```

Pavyzdys:

```http
GET /api/klausimai/1/atsakymai/1
```

Galimi statusai:

```text
200 OK
404 Not Found
```

---

### Sukurti atsakymą

```http
POST /api/klausimai/:klausimasId/atsakymai
```

Pavyzdys:

```http
POST /api/klausimai/1/atsakymai
```

JSON:

```json
{
    "pasirinktas_variantas": 2
}
```

Čia:

```text
1 = A
2 = B
3 = C
4 = D
```

Galimi statusai:

```text
201 Created
404 Not Found
422 Unprocessable Entity
```

---

### Atnaujinti atsakymą

```http
PUT /api/klausimai/:klausimasId/atsakymai/:id
```

Pavyzdys:

```http
PUT /api/klausimai/1/atsakymai/1
```

JSON:

```json
{
    "pasirinktas_variantas": 3
}
```

Galimi statusai:

```text
200 OK
404 Not Found
422 Unprocessable Entity
```

---

### Ištrinti atsakymą

```http
DELETE /api/klausimai/:klausimasId/atsakymai/:id
```

Pavyzdys:

```http
DELETE /api/klausimai/1/atsakymai/1
```

Galimi statusai:

```text
204 No Content
404 Not Found
```

---

# HTTP statuso kodai

API naudoja standartinius HTTP statuso kodus.

| Statusas | Reikšmė |
|---|---|
| `200 OK` | Užklausa sėkmingai įvykdyta |
| `201 Created` | Naujas objektas sėkmingai sukurtas |
| `204 No Content` | Objektas sėkmingai ištrintas |
| `404 Not Found` | Prašomas objektas nerastas |
| `422 Unprocessable Entity` | Pateikti duomenys neatitinka validacijos taisyklių |
| `500 Internal Server Error` | Serverio arba duomenų bazės klaida |

---

# Duomenų validacija

API tikrina gaunamus duomenis prieš vykdant SQL užklausas.

Pavyzdžiui, apklausos pavadinimas negali būti tuščias.

Neteisingas request body:

```json
{
    "pavadinimas": "",
    "aprasymas": "Aprašymas"
}
```

Tokiu atveju API grąžina:

```text
422 Unprocessable Entity
```

Atsakymo variantas turi būti sveikasis skaičius nuo `1` iki `4`.

Neteisingas pavyzdys:

```json
{
    "pasirinktas_variantas": 8
}
```

API grąžina:

```text
422 Unprocessable Entity
```

---

# 404 klaidos pavyzdys

Jeigu prašoma neegzistuojančios apklausos:

```http
GET /api/apklausos/999999
```

API grąžina:

```text
404 Not Found
```

Pavyzdinis JSON:

```json
{
    "error": "Apklausa nerasta"
}
```

---

# OpenAPI / Swagger dokumentacija

API dokumentacijai naudojamas **OpenAPI 3.0** ir **Swagger UI**.

Paleidus serverį Swagger dokumentacija pasiekiama:

```text
http://localhost:3000/api-docs
```

Swagger dokumentacijoje pateikti visi 15 REST API metodų.

Endpointai suskirstyti į tris grupes:

```text
Apklausos
Klausimai
Atsakymai
```

Swagger leidžia:

- peržiūrėti endpointus;
- matyti HTTP metodus;
- matyti URL parametrus;
- matyti request body struktūrą;
- matyti galimus HTTP statuso kodus;
- vykdyti API užklausas naudojant `Try it out`.

---

# Postman testavimas

REST API testavimui naudojamas **Postman**.

Patikrinti visi pagrindiniai API metodai:

```text
GET
POST
PUT
DELETE
```

Postman testais tikrinami HTTP statuso kodai.

Pavyzdžiui:

```javascript
pm.test("Statusas yra 200", function () {
    pm.response.to.have.status(200);
});
```

Objekto sukūrimo testas:

```javascript
pm.test("Statusas yra 201", function () {
    pm.response.to.have.status(201);
});
```

Objekto ištrynimo testas:

```javascript
pm.test("Statusas yra 204", function () {
    pm.response.to.have.status(204);
});
```

Taip pat tikrinami klaidų atvejai:

```text
404 Not Found
422 Unprocessable Entity
```

Postman Collection Runner leidžia vienu paleidimu automatiškai patikrinti kelis API metodus.

---

# Realizuoti REST API metodai

Iš viso realizuota:

```text
Apklausos   5 metodai
Klausimai   5 metodai
Atsakymai   5 metodai
----------------------
Iš viso    15 metodų
```

---

# Duomenų bazės ryšiai

Ryšiai tarp objektų:

```text
Apklausa 1
    │
    ▼
    0..* Klausimų

Klausimas 1
    │
    ▼
    0..* Atsakymų
```

Vienas klausimas priklauso vienai apklausai.

Vienas atsakymas priklauso vienam klausimui.

Todėl iš atsakymo galima nustatyti klausimą, o iš klausimo galima nustatyti apklausą.

---

# SQL saugumas

SQL užklausose naudojami parametrizuoti kintamieji.

Pavyzdžiui:

```javascript
const result = await pool.query(
    'SELECT * FROM apklausa WHERE id = $1',
    [id]
);
```

Vietoje reikšmių tiesioginio įterpimo į SQL naudojami:

```text
$1
$2
$3
```

Tai sumažina SQL injection riziką.

---

# Klaidos apdorojimas

Controlleriuose naudojama:

```javascript
try {
    // SQL operacija
} catch (error) {
    console.error(error);

    res.status(500).json({
        error: 'Serverio klaida'
    });
}
```

Jeigu SQL užklausa arba prisijungimas prie duomenų bazės nepavyksta, API grąžina:

```text
500 Internal Server Error
```

---

# Objektų šalinimas

Duomenų bazėje naudojamas `ON DELETE CASCADE`.

Tai reiškia, kad ištrynus apklausą automatiškai ištrinami jai priklausantys klausimai ir su tais klausimais susiję atsakymai.

Ryšys:

```text
Apklausa
    ↓
Klausimas
    ↓
Atsakymas
```

---

# Projekto paleidimo santrauka

Trumpa projekto paleidimo seka:

### 1.

Sukurti PostgreSQL duomenų bazę:

```text
tyrimas_db
```

### 2.

Paleisti:

```text
database/schema.sql
```

### 3.

Jeigu reikalingi demonstraciniai duomenys, paleisti:

```text
database/seed.sql
```

### 4.

Sukurti `.env`:

```env
PORT=3000

DB_HOST=localhost
DB_PORT=5432
DB_NAME=tyrimas_db
DB_USER=postgres
DB_PASSWORD=JUSU_SLAPTAZODIS
```

### 5.

Įdiegti priklausomybes:

```bash
npm install
```

### 6.

Paleisti projektą:

```bash
npm run dev
```

### 7.

API:

```text
http://localhost:3000
```

### 8.

Swagger:

```text
http://localhost:3000/api-docs
```

---
