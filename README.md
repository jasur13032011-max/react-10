# react-10
Ustozning fikridan ko'rinib turibdi: Backend qismini juda zo'r bajargansiz (Flask, SQLAlchemy, JWT va CORS ideal ishlayapti), lekin Frontend qismi repo'da umuman yo'qligi va commitlar kamligi sababli balingiz yetmay qolgan.

Xafa bo'lishga o'rin yo'q — eng murakkab joyi (Backend) tayyor! Endi Frontend'ni qo'shib, repository'ni chiroyli tartibga keltirsak, 90+ balni bemalol olasiz.

🚀 Reja va Amalga Oshirish Bosqichlari
Loyiha uchun berilgan 21 kunlik muddat doirasida topshiriqni 100% ga yetkazish uchun quyidagi qadamlarni tartib bilan bajaramiz:

1. Repository Strukturasini To'g'rilash (Monorepo)
Loyiha papkasida frontend va backend'ni alohida ajrating:

Plaintext
my-recipe-app/
├── backend/            # Hozirgi Flask kodingiz
│   ├── app.py
│   ├── models.py
│   └── ...
├── frontend/           # Yangi yaratiladigan React loyiha
│   ├── src/
│   ├── public/
│   └── package.json
└── README.md
2. Frontend'ni Yaratish va Sozlash
Frontend papkasida Vite yordamida React loyihasini yarating:

Bash
npm create vite@latest frontend -- --template react
cd frontend
npm install react-router-dom
Kerakli custom hook'larni yaratish:
useFetch (useEffect + AbortController bilan)

useDebounce (qidiruv tizimi uchun)

useLocalStorage (JWT va mavzu/theme uchun)

Context'larni sozlash:
AuthContext: JWT token, login, logout, foydalanuvchi holati.

ThemeContext: Dark/Light mode sozlamasi.

Sahifalar va Routing (7+ sahifa):
Home Page (/) — Asosiy sahifa, mashhur retseptlar slider'i/katalogi.

Recipes (/recipes) — Qidiruv, filter va pagination (sahifalash) mavjud katalog.

Recipe Detail (/recipes/:id) — Retsept tafsilotlari va izohlar (comments) bo'limi.

Favorites (/favorites) — Sevimli retseptlar (Protected Route).

Profile (/profile) — Profil ma'lumotlari (Protected Route).

Login (/login) — Tizimga kirish shakli.

Register (/register) — Ro'yxatdan o'tish shakli.

404 (*) — Topilmagan sahifa.

3. Ustoz Ko'rsatgan Xatolarni Tuzatish
🟢 Commit'lar Sonini Ko'paytirish (Juda Muhim!)
Bitta katta commit emas, har bir kichik qism uchun alohida commit g'ishtday terilishi kerak:

git commit -m "feat: init vite react project"

git commit -m "feat: setup react router and pages structure"

git commit -m "feat: add AuthContext and useLocalStorage hook"

git commit -m "feat: create recipe card and list components with React.memo"

git commit -m "feat: implement dark mode context"

va hokazo (kamida 15-20+ commit bo'lsin).

🟢 README.md Faylini Boyitish
README faylingizga quyidagilarni kiriting:

Loyiha haqida qisqacha ma'lumot.

Arxitektura diagrammasi (Frontend <-> Backend API <-> PostgreSQL).

Loyihani lokal ishga tushirish yo'riqnomasi (npm run dev, flask run).

Environment variable (.env) namunalari.

🛠️ Keyingi Qadam
Frontend qismini yozishni nimadan boshlaymiz?
Agar xohlasangiz, AuthContext, useFetch hook'i yoki Routing strukturasining tayyor kod shablonini birgalikda tuzishdan boshlashimiz mumkin. Qaysi biridan boshlaymiz?
