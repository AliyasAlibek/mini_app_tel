# 🎂 Кондитерская с Конструктором Тортов

Telegram Mini App для кондитерских с функцией создания кастомных тортов.

## ✨ Особенности

### Базовый функционал:
- 📱 Каталог товаров с категориями
- 🛒 Корзина с анимациями
- 💳 Прием платежей через Kaspi
- 📸 Отправка чеков клиентами
- ✅ Подтверждение/отклонение оплаты админом
- 📊 Админ-панель для управления
- 📢 Рассылка уведомлений клиентам
- 🌐 Двуязычность (РУ/KZ)

### Дополнительно (конструктор):
- 🎨 **Конструктор кастомных тортов**
- 📏 Выбор размера (маленький/средний/большой)
- 🍰 Выбор начинки (ванильный/шоколадный/клубничный/сырный)
- ✨ Выбор декора (минималистичный/цветочный/ягодный/свой вариант)
- 📸 **Загрузка фото референса** для своего декора
- 💰 Автоматический расчёт стоимости
- 📦 Отправка всех деталей админу

## 🚀 Быстрый старт

### 1. Установка

```bash
npm install
```

### 2. Настройка переменных окружения

Скопируйте `.env.example` в `.env`:

```bash
cp .env.example .env
```

Заполните файл `.env`:

```env
# Telegram Bot Token (получить у @BotFather)
BOT_TOKEN=123456789:ABCdefGHIjklMNOpqrsTUVwxyz

# Telegram ID админа (узнать у @userinfobot)
ADMIN_ID=123456789

# Supabase (создать на https://supabase.com)
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_KEY=your-anon-key-here

# Порт сервера
PORT=3000
```

### 3. Создание таблиц в Supabase

Выполните SQL в Supabase SQL Editor:

```sql
-- Таблица товаров
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL,
  name_kk TEXT,
  description TEXT,
  description_kk TEXT,
  price INTEGER NOT NULL,
  image TEXT NOT NULL,
  category TEXT NOT NULL,
  category_kk TEXT,
  available BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Таблица заказов
CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  date TIMESTAMP DEFAULT NOW(),
  customer_name TEXT NOT NULL,
  customer_phone TEXT NOT NULL,
  customer_comment TEXT,
  telegram_user_id BIGINT,
  telegram_username TEXT,
  items JSONB NOT NULL,
  total INTEGER NOT NULL,
  status TEXT DEFAULT 'new',
  created_at TIMESTAMP DEFAULT NOW()
);

-- Таблица настроек
CREATE TABLE settings (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  shop_name TEXT DEFAULT 'Кондитерская',
  shop_logo TEXT,
  payment_enabled BOOLEAN DEFAULT false,
  kaspi_phone TEXT,
  kaspi_link TEXT,
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Вставить начальные настройки
INSERT INTO settings (shop_name, payment_enabled) 
VALUES ('Наша Кондитерская', true);

-- RLS политики (безопасность)
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE settings ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Public read products" ON products FOR SELECT USING (available = true);
CREATE POLICY "Public insert orders" ON orders FOR INSERT WITH CHECK (true);
CREATE POLICY "Public read orders" ON orders FOR SELECT USING (true);
CREATE POLICY "Public read settings" ON settings FOR SELECT USING (true);
```

### 4. Настройка Telegram бота

1. Создайте бота у [@BotFather](https://t.me/BotFather)
2. Получите токен бота
3. Настройте Mini App у @BotFather:
   - `/newapp`
   - Выберите вашего бота
   - Название: Кондитерская
   - Описание: Заказ сладостей онлайн
   - Загрузите фото (640x360)
   - URL: `https://your-domain.com` (или Render URL)

### 5. Запуск локально

```bash
npm start
```

Откройте в браузере:
- Mini App: http://localhost:3000
- Админка: http://localhost:3000/admin.html

### 6. Деплой на Render

1. Зарегистрируйтесь на [Render.com](https://render.com)
2. Создайте новый Web Service
3. Подключите GitHub репозиторий
4. Настройки:
   - Build Command: `npm install`
   - Start Command: `npm start`
5. Добавьте Environment Variables:
   - `BOT_TOKEN`
   - `ADMIN_ID`
   - `SUPABASE_URL`
   - `SUPABASE_KEY`
6. Deploy!

## 📱 Использование

### Для клиентов:

1. Открыть бота в Telegram
2. Запустить Mini App
3. Просмотреть каталог ИЛИ создать свой торт
4. Добавить товары в корзину
5. Оформить заказ
6. Получить реквизиты для оплаты
7. Отправить скриншот чека

### Конструктор торта:

1. Нажать "🎨 Создать свой торт"
2. **Шаг 1:** Выбрать размер (маленький/средний/большой)
3. **Шаг 2:** Выбрать начинку (4 варианта)
4. **Шаг 3:** Выбрать декор
5. Если "Свой вариант":
   - Описать текстом желаемый декор
   - Загрузить фото референса (опционально)
6. Добавить в корзину
7. Оформить заказ

### Для админа:

1. Открыть админ-панель: `https://your-domain.com/admin.html`
2. Управление заказами:
   - Просмотр всех заказов
   - Изменение статусов
   - Подтверждение/отклонение оплаты
3. Управление товарами:
   - Добавление новых товаров
   - Редактирование существующих
   - Удаление товаров
4. Настройки магазина:
   - Название магазина
   - Логотип
   - Kaspi реквизиты
5. Рассылка сообщений клиентам

## 🎨 Настройка конструктора

Опции конструктора находятся в `public/index.html`:

```javascript
const cakeOptions = {
  sizes: [
    { id: 'small', name_ru: 'Маленький (1 чел)', name_kk: 'Кіші (1 адам)', price: 3000, image: '🧁' },
    { id: 'medium', name_ru: 'Средний (2-3 чел)', name_kk: 'Орташа (2-3 адам)', price: 5000, image: '🎂' },
    { id: 'large', name_ru: 'Большой (4-6 чел)', name_kk: 'Үлкен (4-6 адам)', price: 8000, image: '🍰' }
  ],
  fillings: [
    { id: 'vanilla', name_ru: 'Ванильный крем', name_kk: 'Ванильді крем', price: 0 },
    { id: 'chocolate', name_ru: 'Шоколадный крем', name_kk: 'Шоколадты крем', price: 0 },
    { id: 'strawberry', name_ru: 'Клубничный крем', name_kk: 'Құлпынайлы крем', price: 0 },
    { id: 'cheese', name_ru: 'Сырный крем', name_kk: 'Ірімшікті крем', price: 500 }
  ],
  decors: [
    { id: 'minimal', name_ru: 'Минималистичный', name_kk: 'Минималистік', price: 500, image: '✨' },
    { id: 'flowers', name_ru: 'Цветочный', name_kk: 'Гүлдер', price: 1000, image: '🌸' },
    { id: 'berries', name_ru: 'С ягодами', name_kk: 'Жидектермен', price: 1500, image: '🍓' },
    { id: 'custom', name_ru: 'Свой вариант', name_kk: 'Өз нұсқасы', price: 2000, image: '🎨' }
  ]
};
```

Измените цены, названия и добавьте новые опции по необходимости!

## 📦 Структура проекта

```
constructor-version/
├── public/
│   ├── index.html      # Mini App с конструктором
│   └── admin.html      # Админ-панель
├── server.js           # Backend с поддержкой фото
├── package.json        # Зависимости
├── .env                # Конфигурация (не коммитить!)
├── .env.example        # Пример конфигурации
├── .gitignore          # Игнорируемые файлы
└── README.md           # Документация
```

## 🔒 Безопасность

- ✅ Row Level Security (RLS) в Supabase
- ✅ CORS настроен
- ✅ Валидация данных на сервере
- ✅ .env файл в .gitignore
- ✅ Проверка размера фото (макс 5MB)

## 🐛 Решение проблем

### Бот не отвечает
- Проверьте BOT_TOKEN в .env
- Убедитесь что сервер запущен
- Проверьте логи: `console.log` в server.js

### Товары не загружаются
- Проверьте SUPABASE_URL и SUPABASE_KEY
- Убедитесь что таблицы созданы
- Проверьте RLS политики

### Фото референса не отправляется
- Убедитесь что установлен `form-data`: `npm install`
- Проверьте размер фото (макс 5MB)
- Проверьте логи сервера

### Админка не работает
- Откройте DevTools (F12) → Console
- Проверьте URL API в admin.html
- Убедитесь что сервер доступен

## 📞 Поддержка

Если есть вопросы - пишите!

## 📄 Лицензия

MIT

---

**Сделано с ❤️ для кондитерских Казахстана**
