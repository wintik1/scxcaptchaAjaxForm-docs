# scxcaptchaAjaxForm-docs

==================

Неблокирующая числовая CAPTCHA (5 цифр) для AjaxForm/FormIt.
Простая для человека, сложнее для ботов. Все имена/классы с префиксом scx_.

Требования
----------
- MODX Revolution 2.8+ или 3.x
- PHP 7.4+ (рекомендуется 8.x)
- GD + TrueType (для TTF-шрифта)

Установка
---------
1) Установите пакет.
2) Убедитесь, что `assets/components/scxcaptchaajaxform/fonts/DejaVuSansMono.ttf` существует.
   (если нет — будет использован bitmap-фолбэк)

Быстрый старт
-------------
Вставьте блок капчи в форму (важно: некэшируемо):

[[!ScxCaptchaAjaxForm]]

AjaxForm:
[[!AjaxForm?
  &snippet=`FormIt`
  &form=`tpl.AjaxForm.example`
  &hooks=`ScxCaptchaAjaxFormHook,email`
  &validate=`name:required,email:required:email,scx_code:required`
]]

FormIt:
[[!FormIt?
  &hooks=`ScxCaptchaAjaxFormHook,email`
  &validate=`name:required,email:required:email,scx_code:required`
]]
[[!ScxCaptchaAjaxForm]]

Параметры сниппета
------------------
- ttl — время жизни токена, сек. По умолчанию 1200 (20 минут).
  Пример: [[!ScxCaptchaAjaxForm? &ttl=`900`]]

- includeAssets — как подключать CSS/JS. Значения:
  * head  (по умолчанию) — добавить в `<head>` через regClient* (для обычных страниц);
  * inline — вернуть CSS/JS вместе с HTML блока (удобно для AJAX-модалок/фрагментов);
  * none  — не подключать (если вы уже подключили scx.css/scx.js глобально).

- render — возвращать ли сам HTML блока.
  * 1 (по умолчанию) — вернуть HTML капчи;
  * 0 — отдать только ассеты (удобно вызвать один раз на странице).

Примеры использования параметров
--------------------------------
1) Обычная страница (ассеты в head, вернуть блок):
[[!ScxCaptchaAjaxForm]]

2) Модалка/фрагмент, приходящий через AJAX (ассеты inline):
[[!ScxCaptchaAjaxForm? &includeAssets=`inline`]]

3) Подключить ассеты в шапке один раз (без разметки):
[[!ScxCaptchaAjaxForm? &render=`0`]]

4) Если scx.css/js подключены глобально — вернуть только разметку:
[[!ScxCaptchaAjaxForm? &includeAssets=`none`]]

Поведение при первой загрузке
-----------------------------
Сессия **не закрывается** (совместимо с AjaxForm). При первом показе рендерится
«заглушка» (1×1 прозрачный GIF). Реальная картинка капчи подгружается:
- по клику «Обновить»,
- по клику на саму картинку,
- при фокусе поля ввода кода.

Поля формы
----------
- scx_hp    — honeypot (должно оставаться пустым)
- scx_ts    — timestamp рендера формы (мс) для анти-бот проверки (>= 3 сек)
- scx_code  — ответ пользователя (ровно 5 цифр)
- scx_token — скрытый токен текущей капчи

Как это работает
----------------
1) Сниппет создаёт токен, кладёт его в $_SESSION['scx_allowed'].
2) JS подставляет src картинки по запросу пользователя.
3) captcha.php генерирует код (5 цифр), хранит его хэш в $_SESSION['scx_code'][TOKEN] и отдаёт PNG.
4) Хук ScxCaptchaAjaxFormHook валидирует (honeypot, время, токен, код) и очищает данные (одноразово).

Хук (подключение)
-----------------
&hooks=`ScxCaptchaAjaxFormHook,...`
И добавьте `scx_code:required` в &validate, если используете стандартную валидацию FormIt.

Отладка
-------
- Добавьте `&debug=1` к URL картинки (captcha.php?...&debug=1) — получите текст:
  session_name, cookie, requested token, allowed tokens.
- Если видите `Bad token`, проверьте:
  * вызов капчи некэшируемо: [[!ScxCaptchaAjaxForm]];
  * страница и captcha.php от одного и того же хоста/поддомена;
  * для модалок используйте `&includeAssets=inline` или подключите scx.css/js глобально;
  * форма AjaxForm проинициализирована (перед отправкой есть `<input name="af_action">`).

Глобальное подключение ассетов (опция)
--------------------------------------
Можно подключить раз и навсегда:
<link rel="stylesheet" href="[[++assets_url]]components/scxcaptchaajaxform/scx.css">
<script src="[[++assets_url]]components/scxcaptchaajaxform/scx.js"></script>

Кастомизация внешнего вида
--------------------------
Переопределяйте CSS-классы:
.scx-captcha, .scx-img, .scx-refresh, .scx-input, .scx-hp

Безопасность
------------
- Код хранится как хэш (password_hash).
- Токены короткоживущие и чистятся.
- Ответ — строго 5 цифр.

Автор и поддержка
-----------------
Разработчик: sait-modx.by  
Сайт: https://sait-modx.by  
Email: info@sait-modx.by

Если нашли баг или есть идея улучшения — напишите нам. Возможна помощь с установкой/интеграцией и кастомизацией под ваш проект.

Как сообщить об ошибке
----------------------
1) Коротко опишите проблему и ожидаемое поведение.
2) Укажите версию MODX, PHP и версию scxcaptchaAjaxForm.
3) Приложите вывод отладки с captcha.php?&debug=1 (4 строки: session_name, cookie, requested token, allowed tokens).
4) Если используете модалку/AJAX — укажите способ вставки формы и есть ли в форме скрытый input `af_action`.

Благодарности и вклад
---------------------
Будем рады пул-реквестам и отзывам. Если дополнение помогло, можете упомянуть автора в «О проекте» или оставить ссылку на sait-modx.by.

Брендинг
--------
Название пакета: scxcaptchaAjaxForm  
Короткое обозначение: scx  
Допускается упоминание «капча от sait-modx.by» в интерфейсе/футере формы.

Коммерческая поддержка
----------------------
Нужна доработка под нестандартный фронтенд, мультидомены, хитрые модалки, нестандартные шрифты или интеграция с вашим UI? Пишите: info@sait-modx.by.


Лицензия
--------
MIT — см. LICENSE.
