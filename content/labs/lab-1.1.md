## Тема, Мета, Місце розташування

**Тема:** Веб-орієнтований застосунок агрегатор авіабілетів

**Мета:** Проектування та розробка веб-платформи SkyTickets, призначеної для агрегації даних про авіарейси та забезпечення користувачам інтуїтивно зрозумілого інтерфейсу для ефективного пошуку та вибору квитків.

**Місце розташування:**
- GitHub: [(встав посилання)](https://github.com/1chuny/IA-34_appWEB-ChunakovAndrey-FIOT-2026)
- Live demo: [(встав посилання)](https://1chuny.github.io/IA-34_appWEB-ChunakovAndrey-FIOT-2026/)

---

## Опис предметного середовища

Веб-агрегатор авіаквитків "SkyTickets". Сайт надає перелік актуальних авіарейсів, систему пошуку за напрямками, інформацію про проєкт та можливість забронювати квиток через інтерактивну форму.

---

## Структура веб-застосунку

Головна сторінка — шапка (header) з логотипом та навігацією, Hero-блок із синьою секцією пошуку (input-поля для міст та дати), основна частина (main) із заголовком та динамічною сіткою квитків (ticket-grid), футер з копірайтом та іменем розробника.

Про нас (About Us) — окрема сторінка з описом місії проєкту, перевагами сервісу та технічними деталями, оформлена в загальному стилі сайту для збереження цілісності інтерфейсу.

## Сценарій взаємодії (бізнес-логіка)

1. Користувач відкриває сайт і бачить актуальні пропозиції у розділі Popular Flights.

2. Для швидкого пошуку користувач вводить назву міста у поле "To?" (куди) у блоці пошуку.

3. Після натискання кнопки "Search Flights" система фільтрує масив даних через JavaScript та миттєво оновлює сітку квитків на екрані.

4. Користувач обирає потрібний рейс, аналізуючи час вильоту/прильоту та ціну, і натискає кнопку "Choose Ticket".

5. З'являється модальне вікно (або вікно підтвердження), де користувач може завершити бронювання.

6. Кнопки Login/Register дозволяють користувачу відкрити форми для майбутньої авторизації в системі.

## Вимоги

### Функціональні вимоги:

Зручна навігація між головною сторінкою та сторінкою "About Us".

Динамічна генерація карток квитків на основі масиву даних (JavaScript DOM).

Функціональна система пошуку та фільтрації рейсів у реальному часі.

Наявність модальних вікон для входу та реєстрації.

Анімація появи елементів (каскадне випливання карток) для кращого UX.

### Нефункціональні вимоги:

Адаптивність: коректне відображення на мобільних пристроях (перебудова шапки та пошукового блоку в один стовпчик).

Продуктивність: швидке завантаження сторінок та миттєва реакція інтерфейсу на дії користувача.

Візуальний стиль: використання сучасної типографіки (Montserrat) та контрастної кольорової схеми (Blue/Gold).

Кросбраузерність: підтримка Chrome, Firefox, Safari та Edge.

### Стек технологій

HTML5 — семантична структура сторінок.

CSS3 — стилізація, Flexbox/Grid для макетів, Media Queries для адаптивності, CSS-анімації.

JavaScript (Vanilla) — логіка фільтрації квитків, обробка подій (кліки, інпути), динамічне керування модальними вікнами.

Google Fonts — підключення зовнішніх шрифтів.

Git + GitHub — контроль версій та хостинг (GitHub Pages).

## Діаграми

### UML-case діаграма
![Скрін 1](/assets/labs/lab-1/uml.png)

### ER діаграма
![Скрін 1](/assets/labs/lab-1/er.png)

## Структура документа
Нижче наведено узагальнену структуру HTML для двох сторінок проєкту — index.html (Головна) та about.html (Про нас). Це спрощені DOM-скелети, які демонструють основні блоки: header (логотип + навігація), main (секції), footer (контакти).

### 1. Структура сторінки index.html (Головна)
```
<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>SkyTickets — Find Cheap Flight Tickets</title>
	<link rel="stylesheet" href="css/style.css">
	<link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@900&display=swap" rel="stylesheet">
</head>

<body>
	<header class="header">
		<div class="logo">Sky<span>Tickets</span></div>

		<nav class="nav-links">
			<a href="index.html">Main</a>
			<a href="about.html">About us</a>
			<a href="#" class="placeholder">Directions</a>
			<a href="#" class="placeholder">Offers</a>
			<a href="#" class="placeholder">Help</a>
		</nav>

		<div class="auth-group">
			<button class="btn-login">Log in</button>
			<button class="btn-register">Register</button>
		</div>
	</header>

	<section class="search-section">
		<div class="search-container">
			<div class="search-box">
				<input type="text" placeholder="From?">
				<input type="text" placeholder="To?">
				<input type="date">
				<button class="search-btn">Find Tickets</button>
			</div>
		</div>
	</section>

	<main class="main">
		<h1>Popular Flights</h1>
		<div id="flights-container" class="ticket-grid"></div>
	</main>

	<footer class="footer">
		<p>&copy; 2026 Air Tickets Online</p>
	</footer>

	<script src="js/script.js"></script>
</body>

</html>
```
### 2. Структура сторінки about.html (Про нас)
```
<!DOCTYPE html>
<html lang="ru">

<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>SkyTickets — О нас</title>
	<link rel="stylesheet" href="css/style.css">
	<link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@900&display=swap" rel="stylesheet">
</head>

<body>
	<header class="header">
		<div class="logo">Sky<span>Tickets</span></div>

		<nav class="nav-links">
			<a href="index.html">Main</a>
			<a href="about.html">About us</a>
			<a href="#" class="placeholder">Directions</a>
			<a href="#" class="placeholder">Special Offers</a>
			<a href="#" class="placeholder">Help</a>
		</nav>

		<div class="auth-group">
			<button class="btn-login" onclick="alert('Окно входа в разработке')">Log in</button>
			<button class="btn-register" onclick="alert('Регистрация временно недоступна')">Register</button>
		</div>
	</header>

	<section class="search-section">
		<div class="search-container">
			<h1 style="color: white; margin: 0; font-size: 3rem; text-align: center;">About our service</h1>
		</div>
	</section>

	<main class="main">
		<div class="about-container" style="max-width: 800px; margin: 0 auto; line-height: 1.8;">
			<div class="about-card"
				style="background: white; padding: 40px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.05);">
				<h2>SkyTickets — your reliable guide in the sky</h2>
				<p>We are a modern flight ticket aggregator created to make travel accessible to everyone. Our service analyzes hundreds of routes to find the most favorable offers in just a few seconds.</p>

				<h3 style="color: #003580; margin-top: 30px;">Why choose us?</h3>
				<ul style="list-style: none; padding: 0;">
					<li style="margin-bottom: 15px;">🚀 <strong>Speed:</strong> Instant search across all available flights.
					</li>
					<li style="margin-bottom: 15px;">🧡 <strong>Convenience:</strong> Simple and intuitive interface without unnecessary noise.</li>
					<li style="margin-bottom: 15px;">🛡️ <strong>Reliability:</strong> We work only with verified airlines.</li>
				</ul>

				<div style="margin-top: 40px; padding-top: 20px; border-top: 1px solid #eee; color: #666;">
					<p><strong>Developer:</strong> Andrey Chunakov</p>
					<p><strong>Year of Foundation:</strong> 2026</p>
				</div>

				<a href="index.html" class="buy-btn"
					style="display: block; text-align: center; text-decoration: none; margin-top: 30px;">Return to ticket search</a>
			</div>
		</div>
	</main>

	<footer class="footer">
		<p>&copy; 2026 Air Tickets Online | Project Chunakov Andrey</p>
	</footer>

	<script src="js/script.js"></script>
</body>

</html>
```
## Скріншоти
![Скрін 1](/assets/labs/lab-1/main.png)
![Скрін 1](/assets/labs/lab-1/about.png)

## Висновки

У результаті виконання проекту було успішно розроблено прототип веб-сервісу SkyTickets, призначеного для пошуку та агрегації авіаквитків. Під час роботи було створено семантичну структуру сторінок на основі HTML5, реалізовано адаптивний інтерфейс за допомогою сучасних методів CSS (Flexbox та Grid), а також налаштовано динамічне відображення даних та систему фільтрації рейсів у реальному часі через JavaScript. Впровадження модальних вікон для авторизації та каскадних анімацій появи елементів дозволило значно підвищити рівень зручності користувацького інтерфейсу (UX). Отриманий досвід роботи з маніпуляціями DOM-деревом та оптимізацією контенту для мобільних пристроїв забезпечує високу гнучкість проекту та його готовність до подальшої інтеграції з серверною частиною для роботи з реальними базами даних.
