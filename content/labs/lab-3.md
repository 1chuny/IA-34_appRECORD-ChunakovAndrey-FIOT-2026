## Тема, Мета, Місце розташування

**Тема:** Веб-орієнтований застосунок агрегатор авіабілетів

**Мета:** Метою даної лабораторної роботи є
- вивчення принципів побудови REST API;
- набуття практичних навичок розробки серверного застосунку з використанням платформи
Node.js і фреймворку Express;
- реалізувати механізми реєстрації та авторизації користувачів;
- забезпечити валідацію вхідних даних;
- забезпечити обробку помилок;
- організувати захищений доступ до ресурсів із використанням JWT-токенів і системи ролей
користувачів.

**Місце розташування:**
- GitHub: [(встав посилання)](https://github.com/1chuny/IA-34_appWEB-ChunakovAndrey-FIOT-2026)
- Live demo: [(встав посилання)](https://1chuny.github.io/IA-34_appWEB-ChunakovAndrey-FIOT-2026/)

---

## Опис предметного середовища

Веб-агрегатор авіаквитків "SkyTickets". Сайт надає перелік актуальних авіарейсів, систему пошуку за напрямками, інформацію про проєкт та можливість забронювати квиток через інтерактивну форму.

---

## Виконання завдань

1. Встановити необхідні бібліотеки
![Скрін 1](/assets/labs/lab3/1.png)

2. Реалізувати реєстрацію та авторизацію користувача
```
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');

// Реєстрація користувача
app.post('/register', async (req, res) => {
    const { email, password, confirmPassword } = req.body;
    
    // Перевірка збігу паролів
    if (password !== confirmPassword) {
        return res.status(400).json({ message: 'Passwords do not match.' });
    }

    let users = readDB(DB_FILE);
    // Перевірка, чи існує вже такий користувач
    if (users.find(u => u.email === email)) {
        return res.status(400).json({ message: 'User already exists.' });
    }

    // Хешування пароля
    const hashedPassword = await bcrypt.hash(password, 10);
    
    // Створення та збереження нового користувача (за замовчуванням роль 'user')
    users.push({ 
        id: Date.now().toString(), 
        email, 
        password: hashedPassword, 
        role: 'user' 
    });
    writeDB(DB_FILE, users);
    
    res.status(201).json({ message: 'Registered successfully.' });
});

// Авторизація користувача (Login)
app.post('/login', async (req, res) => {
    const { email, password } = req.body;
    const users = readDB(DB_FILE);
    const user = users.find(u => u.email === email);

    // Перевірка існування користувача та правильності пароля
    if (!user || !(await bcrypt.compare(password, user.password))) {
        return res.status(400).json({ message: 'Invalid credentials.' });
    }

    // Генерація JWT токена
    const accessToken = jwt.sign(
        { id: user.id, email: user.email, role: user.role }, 
        ACCESS_TOKEN_SECRET, 
        { expiresIn: '1h' }
    );
    
    res.json({ accessToken, role: user.role });
});
```

3. Додати валідацію даних, обробку помилок
```
app.post('/register', async (req, res) => {
    const { email, password, confirmPassword } = req.body;
    
    // Валідація 1: Перевірка, чи збігаються паролі
    if (password !== confirmPassword) {
        // Обробка помилки: повертаємо статус 400 (Bad Request)
        return res.status(400).json({ message: 'Passwords do not match.' });
    }

    let users = readDB(DB_FILE);
    
    // Валідація 2: Перевірка на дублювання (чи існує вже такий email)
    if (users.find(u => u.email === email)) {
        // Обробка помилки: блокуємо створення дубліката
        return res.status(400).json({ message: 'User already exists.' });
    }
```

4. Реалізувати захищений маршрут
```
// Middleware:
const authenticateToken = (req, res, next) => {
    // Отримуємо токен із заголовка Authorization (формат "Bearer TOKEN")
    const token = req.headers['authorization']?.split(' ')[1];
    
    // Якщо токена немає — відмова в доступі
    if (!token) return res.status(401).json({ message: 'Access denied. No token.' });

    // Перевірка справжності токена за допомогою секретного ключа
    jwt.verify(token, ACCESS_TOKEN_SECRET, (err, user) => {
        if (err) return res.status(403).json({ message: 'Invalid token.' }); // Токен підроблений або прострочений
        
        req.user = user; // Зберігаємо розшифровані дані користувача у запиті
        next(); // Пропускаємо запит далі до маршруту
    });
};

// ЗАХИЩЕНИЙ МАРШРУТ (отримання профілю)
// Використовуємо authenticateToken як проміжний крок перед виконанням логіки
app.get('/profile', authenticateToken, (req, res) => {
    const user = readDB(DB_FILE).find(u => u.id === req.user.id);
    if (!user) return res.status(404).json({ message: 'User not found.' });
    
    res.json({ email: user.email, role: user.role });
});
```

5. Протестувати API через Postman або curl
![Скрін 1](/assets/labs/lab3/5.png)

6. Проаналізувати отримані результати


7. Додати підтвердження пароля при реєстрації
8. Додати роль користувача (admin/user)
```
// Маршрут реєстрації
app.post('/register', async (req, res) => {
    // Отримуємо пароль та його підтвердження з тіла запиту
    const { email, password, confirmPassword } = req.body;
    
    // Завдання 7: Перевірка підтвердження пароля
    if (password !== confirmPassword) {
        return res.status(400).json({ message: 'Passwords do not match.' });
    }

    let users = readDB(DB_FILE);
    if (users.find(u => u.email === email)) {
        return res.status(400).json({ message: 'User already exists.' });
    }

    const hashedPassword = await bcrypt.hash(password, 10);
    
    // Завдання 8: Додавання ролі користувача
    // За замовчуванням всі нові акаунти отримують роль 'user'. 
    // Адміном можна стати, лише змінивши це поле в базі вручну.
    users.push({ 
        id: Date.now().toString(), 
        email, 
        password: hashedPassword, 
        role: 'user' // <--- Присвоєння ролі
    });
    
    writeDB(DB_FILE, users);
    res.status(201).json({ message: 'Registered successfully.' });
});

// Завдання 8 (додатково): Middleware для перевірки ролі адміністратора
const requireAdmin = (req, res, next) => {
    // req.user береться з JWT токена після проходження authenticateToken
    if (req.user.role !== 'admin') {
        return res.status(403).json({ message: 'Access denied. Admins only.' });
    }
    next();
};
```

9. Реалізувати logout
```
// Завдання 9: Маршрут для виходу з системи
app.post('/logout', (req, res) => {
    // У stateless JWT бекенд просто повертає успішний статус.
    // Якщо використовуються refresh-токени в базі, тут має бути логіка їх видалення.
    res.json({ message: 'Logged out successfully.' });
});
```

10. Додати оновлення профілю
```
// Завдання 10: Оновлення профілю (зміна пароля)
// authenticateToken гарантує, що користувач авторизований
app.put('/profile/password', authenticateToken, async (req, res) => {
    const { oldPassword, newPassword } = req.body;
    
    let users = readDB(DB_FILE);
    // Шукаємо користувача за ID, який ми дістали з токена
    const userIndex = users.findIndex(u => u.id === req.user.id);

    if (userIndex === -1) {
        return res.status(404).json({ message: 'User not found.' });
    }

    // Перевіряємо, чи правильно введено старий пароль
    const isMatch = await bcrypt.compare(oldPassword, users[userIndex].password);
    if (!isMatch) {
        return res.status(400).json({ message: 'Incorrect old password.' });
    }

    // Хешуємо та зберігаємо новий пароль
    users[userIndex].password = await bcrypt.hash(newPassword, 10);
    writeDB(DB_FILE, users);

    res.json({ message: 'Password updated successfully.' });
});
```

11. Зберігати користувачів у базі
```
const fs = require('fs');
const path = require('path');

// Шлях до файлу бази даних
const DB_FILE = path.join(__dirname, 'users.json');

// Створюємо файл, якщо його ще не існує
if (!fs.existsSync(DB_FILE)) {
    fs.writeFileSync(DB_FILE, JSON.stringify([]));
}

// Функції-хелпери для читання та запису в базу
const readDB = (file) => JSON.parse(fs.readFileSync(file, 'utf8'));
const writeDB = (file, data) => fs.writeFileSync(file, JSON.stringify(data, null, 2));

// Використання:
// let users = readDB(DB_FILE);
// writeDB(DB_FILE, users);
```

12. Реалізувати refresh token
```
const ACCESS_TOKEN_SECRET = 'super_secret_access_key_123';
const REFRESH_TOKEN_SECRET = 'super_secret_refresh_key_456';
let refreshTokens = []; // Тимчасове сховище для дійсних refresh-токенів

// Маршрут для оновлення Access токена
app.post('/refresh', (req, res) => {
    const { token } = req.body;
    
    if (!token) return res.status(401).json({ message: 'Refresh token required.' });
    if (!refreshTokens.includes(token)) return res.status(403).json({ message: 'Invalid refresh token.' });

    // Перевіряємо дійсність Refresh токена
    jwt.verify(token, REFRESH_TOKEN_SECRET, (err, user) => {
        if (err) return res.status(403).json({ message: 'Token expired or invalid.' });
        
        // Генеруємо новий Access токен
        const newAccessToken = jwt.sign(
            { id: user.id, email: user.email, role: user.role }, 
            ACCESS_TOKEN_SECRET, 
            { expiresIn: '15m' }
        );
        res.json({ accessToken: newAccessToken });
    });
});
```

13. Додати логування помилок
```
const LOG_FILE = path.join(__dirname, 'error.log');

// Middleware для перехоплення та логування помилок
const logError = (err, req, res, next) => {
    // Формуємо повідомлення: Час + Метод (GET/POST) + URL + Текст помилки
    const logMsg = `[${new Date().toISOString()}] ${req.method} ${req.url} - ${err.message}\n`;
    
    // Дописуємо повідомлення у файл (не перезаписуючи його)
    fs.appendFileSync(LOG_FILE, logMsg);
    console.error(logMsg); // Виводимо в консоль
    
    res.status(500).json({ message: 'Internal Server Error' });
};

// Цей рядок обов'язково додається в самому кінці server.js
app.use(logError);
```

14. Обмежити кількість спроб входу
```
const rateLimit = require('express-rate-limit');

// Налаштування ліміту: максимум 5 спроб за 15 хвилин з однієї IP-адреси
const loginLimiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 хвилин у мілісекундах
    max: 5, 
    message: { message: 'Too many login attempts. Please try again later.' }
});

// Застосовуємо цей "фільтр" тільки до маршруту авторизації
app.post('/login', loginLimiter, async (req, res) => {
    // ... логіка перевірки email та пароля ...
});
```

15. Додати middleware для перевірки токена
```
const jwt = require('jsonwebtoken');

// Middleware: Перевірка токена
const authenticateToken = (req, res, next) => {
    // Дістаємо токен із HTTP-заголовка Authorization (формат "Bearer <token>")
    const authHeader = req.headers['authorization'];
    const token = authHeader && authHeader.split(' ')[1]; 

    // Якщо токена немає взагалі
    if (!token) {
        return res.status(401).json({ message: 'Access denied. No token provided.' });
    }

    // Розшифровуємо токен
    jwt.verify(token, ACCESS_TOKEN_SECRET, (err, user) => {
        // Якщо токен підроблений або його час життя минув
        if (err) {
            return res.status(403).json({ message: 'Invalid or expired token.' });
        }
        
        // Зберігаємо розшифровані дані (id, email, role) для використання в маршруті
        req.user = user; 
        next(); // Успіх: передаємо управління наступній функції
    });
};
```

16. Реалізувати зміну пароля
```
// Маршрут для зміни пароля (захищений через authenticateToken)
app.put('/profile/password', authenticateToken, async (req, res) => {
    const { oldPassword, newPassword } = req.body;
    
    let users = readDB(DB_FILE);
    // Знаходимо користувача за його ID (який ми розшифрували з токена)
    const userIndex = users.findIndex(u => u.id === req.user.id);

    if (userIndex === -1) {
        return res.status(404).json({ message: 'User not found.' });
    }

    // Перевіряємо, чи збігається введений старий пароль з тим, що в базі
    const isMatch = await bcrypt.compare(oldPassword, users[userIndex].password);
    if (!isMatch) {
        return res.status(400).json({ message: 'Incorrect old password.' });
    }

    // Хешуємо новий пароль і зберігаємо в базу
    users[userIndex].password = await bcrypt.hash(newPassword, 10);
    writeDB(DB_FILE, users);

    res.json({ message: 'Password changed successfully.' });
});
```

17. Реалізувати видалення користувача
```
// Маршрут для видалення власного акаунта (захищений)
app.delete('/profile', authenticateToken, (req, res) => {
    let users = readDB(DB_FILE);
    
    // Фільтруємо масив користувачів, залишаючи всіх, ОКРІМ поточного
    const initialLength = users.length;
    users = users.filter(u => u.id !== req.user.id);

    // Якщо довжина масиву змінилася, значить користувача було видалено
    if (users.length < initialLength) {
        writeDB(DB_FILE, users);
        res.json({ message: 'Account deleted successfully.' });
    } else {
        res.status(404).json({ message: 'User not found.' });
    }
});
```

18. Реалізувати відновлення пароля
```
// Маршрут запиту на відновлення пароля
app.post('/forgot-password', (req, res) => {
    const { email } = req.body;
    
    // У реальному проєкті тут генерується одноразовий токен і відправляється 
    // через бібліотеку Nodemailer на вказаний email.
    // Для лабораторної ми просто повертаємо успішну відповідь:
    
    res.json({ 
        message: `If the email ${email} is registered, a recovery link has been sent.` 
    });
});
```

19. Додати підтвердження email
```
// Маршрут для підтвердження електронної пошти
// (Зазвичай сюди переходять по кліку з листа)
app.get('/confirm-email/:userId', (req, res) => {
    const userId = req.params.userId;
    let users = readDB(DB_FILE);
    
    const userIndex = users.findIndex(u => u.id === userId);
    
    if (userIndex === -1) {
        return res.status(404).json({ message: 'User not found.' });
    }
    
    // Оновлюємо статус підтвердження пошти в базі
    users[userIndex].isEmailConfirmed = true;
    writeDB(DB_FILE, users);
    
    res.json({ message: 'Email confirmed successfully. You can now log in.' });
});
```

20. Реалізувати OAuth (Google login)
```
// Маршрут для входу через Google
app.post('/auth/google', (req, res) => {
    // У реальному проєкті клієнт відправляє сюди Google ID Token,
    // а сервер перевіряє його справжність через бібліотеку "google-auth-library".
    
    // Імітація успішної верифікації Google:
    const mockGoogleEmail = 'googleuser@example.com'; 
    
    // Створюємо JWT токен нашого додатку для цього Google-користувача
    const payload = { 
        id: 'google123', 
        email: mockGoogleEmail, 
        role: 'user' 
    };
    
    const accessToken = jwt.sign(payload, ACCESS_TOKEN_SECRET, { expiresIn: '15m' });
    
    res.json({ 
        accessToken, 
        message: 'Google login successful.' 
    });
});
```

## Висновки

Під час виконання даної лабораторної роботи було успішно спроєктовано та розроблено повноцінний веб-додаток із клієнтською та серверною частинами (на базі Node.js/Express), що реалізує систему бронювання авіаквитків. Практично закріплено ключові механізми безпеки та аутентифікації: реалізовано реєстрацію та вхід з використанням JWT-токенів і хешуванням паролів (bcrypt), налаштовано розмежування прав доступу (Admin/User) із відповідними CRUD-операціями для управління даними, а також впроваджено захист маршрутів, логування і обмеження кількості запитів (rate limiting). Крім того, побудовано архітектурні заглушки для розширеного функціоналу (OAuth-авторизація, відновлення паролів, підтвердження email) та надійну систему обробки помилок, що в комплексі дозволило здобути реальний досвід розробки безпечних, функціональних та масштабованих сучасних веб-сервісів.