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
1. Custom Hook'lar (src/hooks/)
📍 useLocalStorage.js
Tokenlar va mavzuni (theme) xotirada saqlash uchun:

JavaScript
import { useState, useEffect } from 'react';

export const useLocalStorage = (key, initialValue) => {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored ? JSON.parse(stored) : initialValue;
    } catch (error) {
      console.error(`Error reading key "${key}":`, error);
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(`Error setting key "${key}":`, error);
    }
  }, [key, value]);

  return [value, setValue];
};
📍 useFetch.js
useEffect va AbortController orqali so'rovlarni xavfsiz yuborish uchun:

JavaScript
import { useState, useEffect } from 'react';

export const useFetch = (url, options = {}) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) return;

    const controller = new AbortController();
    setLoading(true);
    setError(null);

    fetch(url, { ...options, signal: controller.signal })
      .then((res) => {
        if (!res.ok) throw new Error(`Xatolik: ${res.status}`);
        return res.json();
      })
      .then((data) => {
        setData(data);
        setLoading(false);
      })
      .catch((err) => {
        if (err.name !== 'AbortError') {
          setError(err.message);
          setLoading(false);
        }
      });

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
};
📍 useDebounce.js
Qidiruv tizimi samaradorligini oshirish uchun:

JavaScript
import { useState, useEffect } from 'react';

export const useDebounce = (value, delay = 500) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(handler);
  }, [value, delay]);

  return debouncedValue;
};
2. Context'lar (src/context/)
📍 AuthContext.jsx
JWT bilan ishlash, kirish va chiqish mantiqi:

JavaScript
import { createContext, useContext } from 'react';
import { useLocalStorage } from '../hooks/useLocalStorage';

const AuthContext = createContext(null);

export const AuthProvider = ({ children }) => {
  const [token, setToken] = useLocalStorage('jwt_token', null);
  const [user, setUser] = useLocalStorage('user_data', null);

  const login = (userData, authToken) => {
    setUser(userData);
    setToken(authToken);
  };

  const logout = () => {
    setUser(null);
    setToken(null);
  };

  const isAuthenticated = !!token;

  return (
    <AuthContext.Provider value={{ user, token, isAuthenticated, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => useContext(AuthContext);
3. Router va Protected Route (src/App.jsx)
Protected route (himoyalangan sahifa) va marshrutchilash sozlamalari:

JavaScript
import { BrowserRouter, Routes, Route, Navigate } from 'react-router-dom';
import { AuthProvider, useAuth } from './context/AuthContext';

// Sahifalar (Pages)
import Home from './pages/Home';
import Recipes from './pages/Recipes';
import RecipeDetail from './pages/RecipeDetail';
import Favorites from './pages/Favorites';
import Profile from './pages/Profile';
import Login from './pages/Login';
import Register from './pages/Register';
import NotFound from './pages/NotFound';

// Himoyalangan Marshrut Komponenti
const ProtectedRoute = ({ children }) => {
  const { isAuthenticated } = useAuth();
  return isAuthenticated ? children : <Navigate to="/login" replace />;
};

export default function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <Routes>
          {/* Ochiq sahifalar */}
          <Route path="/" element={<Home />} />
          <Route path="/recipes" element={<Recipes />} />
          <Route path="/recipes/:id" element={<RecipeDetail />} />
          <Route path="/login" element={<Login />} />
          <Route path="/register" element={<Register />} />

          {/* Himoyalangan sahifalar (Auth kerak) */}
          <Route
            path="/favorites"
            element={
              <ProtectedRoute>
                <Favorites />
              </ProtectedRoute>
            }
          />
          <Route
            path="/profile"
            element={
              <ProtectedRoute>
                <Profile />
              </ProtectedRoute>
            }
          />

          {/* 404 Sahifa */}
          <Route path="*" element={<NotFound />} />
        </Routes>
      </BrowserRouter>
    </AuthProvider>
  );
}
📝 Keyingi Commit Tayyorgarligi
Ushbu fayllarni yaratganingizdan so'ng, quyidagi tartibda alohida-alohida commit qilib boring:

Bash
git add src/hooks/
git commit -m "feat: add useLocalStorage, useFetch, and useDebounce custom hooks"

git add src/context/
git commit -m "feat: setup AuthContext for JWT authentication"

git add src/App.jsx
git commit -m "feat: configure React Router with protected routes structure"
Frontend qismini yozishni nimadan boshlaymiz?
Agar xohlasangiz, AuthContext, useFetch hook'i yoki Routing strukturasining tayyor kod shablonini birgalikda tuzishdan boshlashimiz mumkin. Qaysi biridan boshlaymiz?
