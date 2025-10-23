# scxcaptchaAjaxForm

![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![MODX](https://img.shields.io/badge/MODX-2.8%2B%20%7C%203.x-blue)
![PHP](https://img.shields.io/badge/PHP-7.4%2B%20%7C%208.x-blue)
![Status](https://img.shields.io/badge/Status-stable-brightgreen)

---

**scxcaptchaAjaxForm** — неблокирующая числовая CAPTCHA (5 цифр) для **AjaxForm** и **FormIt**.  
Простая для человека, сложнее для ботов. Все имена и классы имеют префикс `scx_`.

---

## 🔧 Требования
- MODX Revolution **2.6+** или **3.x**
- PHP **7.4+** (рекомендуется **8.x**)
- **GD + TrueType** (для TTF-шрифта)

---

## ⚙️ Установка
1. Установите пакет через установщик MODX.  
2. Убедитесь, что файл  
   `assets/components/scxcaptchaajaxform/fonts/DejaVuSansMono.ttf`  
   существует.  
   *(Если нет — будет использован bitmap-фолбэк.)*

---

## 🚀 Быстрый старт

Вставьте блок капчи в форму (**некэшируемо!**):

```html
[[!ScxCaptchaAjaxForm]]
Пример с AjaxForm
html
Копировать код
[[!AjaxForm?
  &snippet=`FormIt`
  &form=`tpl.AjaxForm.example`
  &hooks=`ScxCaptchaAjaxFormHook,email`
  &validate=`name:required,email:required:email,scx_code:required`
]]
Пример с FormIt
html
Копировать код
[[!FormIt?
  &hooks=`ScxCaptchaAjaxFormHook,email`
  &validate=`name:required,email:required:email,scx_code:required`
]]
[[!ScxCaptchaAjaxForm]]
⚙️ Параметры сниппета
Параметр	Описание	По умолчанию
ttl	Время жизни токена (секунды)	1200
includeAssets	Как подключать CSS/JS: head, inline, none	head
render	Возвращать ли HTML блока: 1 — да, 0 — только ассеты	1

Примеры параметров
1️⃣ Обычная страница (ассеты в head):

html
Копировать код
[[!ScxCaptchaAjaxForm]]
2️⃣ Модалка (ассеты inline):

html
Копировать код
[[!ScxCaptchaAjaxForm? &includeAssets=`inline`]]
3️⃣ Подключить ассеты без разметки:

html
Копировать код
[[!ScxCaptchaAjaxForm? &render=`0`]]
4️⃣ Если CSS/JS уже подключены глобально:

html
Копировать код
[[!ScxCaptchaAjaxForm? &includeAssets=`none`]]
🧠 Поведение при первой загрузке
Сессия не закрывается (совместимо с AjaxForm).

При первом показе рендерится прозрачный GIF 1×1.

Реальная капча загружается:

по клику на «Обновить»,

по клику на картинку,

при фокусе поля ввода.

🧩 Поля формы
Поле	Назначение
scx_hp	honeypot (должно быть пустым)
scx_ts	timestamp рендера формы (мс), защита ≥ 3 сек
scx_code	ответ пользователя (ровно 5 цифр)
scx_token	скрытый токен текущей капчи

🔍 Как это работает
Сниппет создаёт токен и сохраняет в $_SESSION['scx_allowed'].

JS подставляет src картинки по запросу пользователя.

captcha.php генерирует код (5 цифр), хранит хэш в $_SESSION['scx_code'][TOKEN] и отдаёт PNG.

Хук ScxCaptchaAjaxFormHook валидирует (honeypot, время, токен, код) и очищает данные.

🔗 Подключение хука
Включите хук и добавьте проверку:

html
Копировать код
&hooks=`ScxCaptchaAjaxFormHook,...`
&validate=`name:required,email:required:email,scx_code:required`
🧰 Отладка
Добавьте &debug=1 к URL картинки (captcha.php?...&debug=1).
Если видите Bad token, проверьте:

вызов капчи некэшируемо ([[!ScxCaptchaAjaxForm]]);

страница и captcha.php на одном хосте;

ассеты подключены корректно (inline или глобально);

форма AjaxForm проинициализирована (<input name="af_action">).

🌐 Глобальное подключение ассетов
Можно подключить один раз, например, в шаблоне:

html
Копировать код
<link rel="stylesheet" href="[[++assets_url]]components/scxcaptchaajaxform/scx.css">
<script src="[[++assets_url]]components/scxcaptchaajaxform/scx.js"></script>
🎨 Кастомизация
Переопределяйте CSS-классы:

css
Копировать код
.scx-captcha, .scx-img, .scx-refresh, .scx-input, .scx-hp
🔒 Безопасность
Код хранится как хэш (password_hash).

Токены короткоживущие и очищаются.

Ответ — строго 5 цифр.

👨‍💻 Автор и поддержка
Разработчик: sait-modx.by
Email: info@sait-modx.by

Если нашли баг или идею улучшения — пишите нам.
Возможна помощь с установкой, интеграцией и кастомизацией под ваш проект.

🐞 Как сообщить об ошибке
Коротко опишите проблему и ожидаемое поведение.

Укажите версии MODX, PHP и scxcaptchaAjaxForm.

Приложите вывод отладки captcha.php?&debug=1.

Если используете модалку/AJAX — укажите способ вставки формы и наличие af_action.

🙏 Благодарности и вклад
Будем рады пул-реквестам и отзывам.
Если дополнение помогло, можете упомянуть автора или ссылку на sait-modx.by.

💡 Брендинг
Название пакета: scxcaptchaAjaxForm

Короткое обозначение: scx

Допускается упоминание:
«капча от sait-modx.by» в интерфейсе или футере формы.

💼 Коммерческая поддержка
Нужна доработка под ваш фронтенд, мультидомены, модалки, шрифты или интеграция с UI?
Пишите: info@sait-modx.by

📜 Лицензия
MIT License
© 2025, sait-modx.by
