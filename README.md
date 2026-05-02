<div align="center">

# ULVİS

**Kurumsal Lisans ve Varlık İzleme Sistemi**

*Donanım, yazılım lisansı, zimmet, arıza ve satınalma — rol tabanlı, tek panel.*

[![Node.js](https://img.shields.io/badge/node.js-%3E%3D18-339933?style=flat&logo=node.js&logoColor=white)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/Express-4.x-000000?style=flat&logo=express&logoColor=white)](https://expressjs.com/)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat&logo=react&logoColor=black)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-5-646CFF?style=flat&logo=vite&logoColor=white)](https://vitejs.dev/)
[![SQLite](https://img.shields.io/badge/SQLite-3-003B57?style=flat&logo=sqlite&logoColor=white)](https://www.sqlite.org/)


*İçerik:* bu sayfada **Özellikler**, **Teknoloji yığını**, **Kurulum**, **Ortam dosyası**, **npm komutları**, **Örnek hesaplar**, **Cron**, **API**, **Sorun giderme** ve **GitHub About** önerileri bulunur.

</div>

> Bu depodaki `README.md`, GitHub’da `main` / `default` daldayken **proje ana sayfasında** otomatik gösterilir. Açıklamalar, Issues/PR açıklamaları ve “About” açıklama kutusunu da tutarlı tutun.

**Önerilen GitHub *Topics*:** `react` · `vite` · `express` · `sqlite` · `jwt` · `bcrypt` · `inventory-management` · `license-management` · `it-asset` · `full-stack`

| | |
| --- | --- |
| **Arayüz (dev)** | http://localhost:3000 — Vite, `/api` → `http://localhost:5000` proksi |
| **API (dev)** | http://localhost:5000 — `GET /api/health` |
| **Repo kökü** | `gordon` veya tercihen `ulvis` (klasör adı serbest) |

---

<!-- 
  GitHub’da ekran görüntüsü: ör. `docs/preview.png` dosyasını commitleyin ve açıklamalı alt metinle kullanın. 
  ![ULVİS arayüzü](docs/preview.png)
-->

## Özellikler

| Alan | Açıklama |
|------|----------|
| **Kimlik** | JWT, bcrypt, şifre politikası, KVKK onayı, isteğe bağlı e-posta (şifre sıfırlama / IT sıfırlama) |
| **Roller** | IT Müdürü, IT Destek, Satınalma, Personel (sayfa ve API uçlarına göre kısıtlanır) |
| **Envanter** | Donanım ve yazılım lisansı CRUD; lisans bitişi ve `durum` senkronu; veri gezgininde PDF dışa aktarma |
| **Zimmet** | Talep, onay, red, iade; donanım / yazılım ataması |
| **Destek** | Arıza biletleri, IT tarafında durum güncelleme |
| **Raporlama** | IT Müdürü panosu: grafikler, yaklaşan lisans maliyetleri, dağılımlar |
| **Denetim** | `islem_loglari` + başarılı mutasyon/okuma yanıtlarına bağlı denetim |
| **Bildirim** | Uygulama içi bildirimler; 30 / 15 / 7 gün kala lisans (cron + manuel tetik) |

---

## Teknoloji yığını

| Katman | Teknoloji |
|--------|-----------|
| **Backend** | Node.js, Express, better-sqlite3, JWT, bcrypt, node-cron, Nodemailer (opsiyonel) |
| **Veritabanı** | SQLite — `database.sqlite` veya `DB_PATH` |
| **Frontend** | React 18, Vite 5, Tailwind CSS, React Router, Axios, Recharts, react-hot-toast |
| **PDF (istemci)** | pdfmake + VFS; fontlar `addVirtualFileSystem` ile yüklenir |

---

## Dizin yapısı (özet)

```
gordon/
├── backend/           # REST API, SQLite, seed, cron
│   ├── config/        # db.js — şema, WAL, migrasyon
│   ├── routes/        # /api/* kaynakları
│   ├── middleware/    # JWT, denetim
│   ├── services/      # Bildirim, e-posta, denetim
│   └── server.js
├── frontend/
│   ├── public/        # logo, statik
│   └── src/
│       ├── pages/
│       ├── components/
│       ├── context/
│       └── services/
├── .gitignore
└── README.md
```

> **Neden `node_modules` yok?** `.gitignore` ile hariç tutulur. Klon sonrası `npm install` zorunludur.  
> **Neden `dist` yok?** Üretim önüzü `npm run build` ile üretilir; kaynak sadece `src/`.

---

## Gereksinimler

- **Node.js** 18+ (LTS önerilir)  
- **npm**  
- (İsteğe bağlı) SMTP — e-posta istemiyorsanız doldurmayın; uygulama çalışmaya devam eder

---

## Hızlı başlangıç (2 terminal)

### 1) Depoyu al

```bash
git clone https://github.com/<kullanici-adin>/<depo-adi>.git
cd <depo-adi>
```

### 2) Backend

```bash
cd backend
npm install
```

`backend/.env` dosyasını **oluştur** (aşağıdaki değişkenlere bakın). Ardından:

```bash
npm run seed
npm run dev
```

- `seed` mevcut SQLite verisini siler ve örnek veri basar; **dikkatli kullanın**  
- API: **http://localhost:5000** — canlılık: `GET /api/health`

### 3) Frontend

Yeni terminal:

```bash
cd frontend
npm install
npm run dev
```

- Arayüz: **http://localhost:3000** (`vite.config.js` → `server.port: 3000`)  
- İstekler: `/api` → Vite proksi → **http://localhost:5000**

### 4) Önizleme (üretim derlemesi, isteğe bağlı)

```bash
cd frontend
npm run build
npm run preview
```

`frontend/dist` repoya alınmaz; sunucunuzda `dist`’i statik servis edin veya ters proksi ile API’ye yönlendirin.

---

## Ortam dosyası (backend)

`backend/.env` örnek (tırnak/ boşluk sizin ortamınıza göre):

```env
PORT=5000
JWT_SECRET=gercek-ortamda-uzun-ve-tekil-bir-anahtar
DB_PATH=./database.sqlite

# Şifre sıfırlama e-postasındaki frontend adresi (bu repoda Vite: 3000)
APP_URL=http://localhost:3000

RESET_TOKEN_TTL_MINUTES=60

# Opsiyonel SMTP — dört değer birden yoksa e-posta atlanır
# SMTP_HOST=
# SMTP_PORT=587
# SMTP_SECURE=false
# SMTP_USER=
# SMTP_PASS=
# SMTP_FROM="ULVİS <no-reply@ornek.com>"
```

| Değişken | Zorunlu | Açıklama |
|----------|---------|----------|
| `JWT_SECRET` | Evet (üretim) | JWT imzası; repoya asla koyulmaz |
| `PORT` | Hayır | Varsayılan `5000` |
| `DB_PATH` | Hayır | Varsayılan `backend` altında `database.sqlite` |
| `APP_URL` | Hayır | Şifre sıfırlama linki tabanı |
| `RESET_TOKEN_TTL_MINUTES` | Hayır | Varsayılan `60` |
| `SMTP_*` | Hayır | Hepsi anlamlı dolduğunda e-posta |

`.env` zaten **`.gitignore`** içindedir; sadece bu README veya kendi notlarınızı paylaşın.

---

## npm komutları

### Backend

| Komut | Açıklama |
|-------|----------|
| `npm run dev` | nodemon ile geliştirme |
| `npm start` | `node server.js` (üretim) |
| `npm run seed` | Veritabanı + örnek veri (mevcut veri silinir) |

### Frontend

| Komut | Açıklama |
|-------|----------|
| `npm run dev` | Vite + API proksi |
| `npm run build` | `dist` üretimi |
| `npm run preview` | `dist` önizleme |

---

## Örnek hesaplar (`npm run seed` sonrası)

> **Dikkat:** Yalnızca geliştirme. Üretimde tüm parolalar değiştirilmelidir.

| Rol | E-posta | Şifre |
|-----|---------|-------|
| IT Müdürü | `admin@ulvis.com.tr` | `admin123` |
| IT Destek | `itdestek@ulvis.com.tr` | `destek123` |
| Satınalma | `satinalma@ulvis.com.tr` | `satin123` |
| Personel | `ali.ozturk@ulvis.com.tr` | `personel123` |

---

## Zamanlayıcı (cron)

Sunucu açılışında ve her gün **02:00** (sunucu saati):

1. Bitişi güncellenmiş ama hâlâ “Süresi Dolmuş” kalan lisans → **Aktif**e alınabilir.  
2. Bitişi geçmiş **Aktif** lisans → **Süresi Dolmuş** işaretlenebilir.  
3. 30 / 15 / 7 gün kala → Satınalma ve IT Müdürü’ne in-app bildirim denenir.

---

## API

- Kök: **`/api`** (ör. `POST /api/auth/login`, `GET /api/licenses`)  
- Ön uç: Axios tabanı `'/api'`; geliştirmede Vite proksi devreye girer

---

## Sorun giderme

| Belirti | Ne yapılabilir |
|--------|----------------|
| 401, login’e düşme | Token süresi, `JWT_SECRET` değişimi; `localStorage`’ı temizleme |
| PDF / font hatası | pdfmake VFS: `addVirtualFileSystem`; `Database.jsx` yüklemesi |
| E-posta yok | `SMTP_*` eksik/yanlış — bilinçli olarak opsiyonel |
| Port meşgul | `PORT` (backend) veya Vite `server.port` değiştirme |

---

## GitHub’da proje adı (About / Description)

- **Kısa açıklama (max ~350 karakter, örnek):**  
  *Full-stack IT envanteri: donanım, yazılım lisansı, zimmet, arıza; JWT + SQLite + React/Vite. Node 18+.*

- **Website:** (canlı demoyu yayınlarsanız) boş bırakılabilir.  
- **Sponsor:** ders/kişisel repoda genelde yok.  
- **Releases / Packages:** sürüm yayınlamak isterseniz `v1.0.0` etiketli tag ile başlayın.

---

## Katkı ve lisans

Ekip/öğrenci sözleşmenize uyun. Üretimde **HTTPS**, güçlü parolalar, yedekleme ve ayrı bir veri deposu kullanılmalıdır. Lisans türü (ör. MIT) seçecekseniz repoya `LICENSE` dosyası ekleyin; ardından burada belirtin.

---

<div align="center">

**ULVİS** — *Kurumsal Lisans ve Varlık İzleme*

</div>
