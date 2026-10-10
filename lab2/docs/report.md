# Отчёт по лабораторной работе №2 — Node-RED

## 1. Краткое описание выполненного

В рамках лабораторной работы №2 я освоила Node-RED как low-code инструмент визуального программирования. Были собраны и задеплоены **13 потоков**, покрывающих базовые и продвинутые ноды:

- Inject / Debug
- Function (JS-логика)
- Switch (ветвление)
- Change (установка свойств)
- Template (Mustache)
- HTTP Request (запрос к публичному API)
- MQTT (публикация и подписка через публичный брокер)
- HTTP In / HTTP Response (REST API: 3 GET-эндпоинта)
- Dashboard (gauge + chart)
- Telegram-бот (команды + echo)
- File (запись и чтение)
- Context (счётчик на flow context)

Дополнительно выполнена **ачивка 11** — Telegram-бот с inline-клавиатурой и состоянием.

## 2. Использованные AI-промпты

ИИ (DeepSeek) использовался для генерации кода Function-нод, синтаксиса Mustache-шаблонов и структуры inline_keyboard. Ключевые промпты:

1. **Для Function-ноды с базовыми элементами JS:**
   > «Сгенерируй код для function node Node-RED: используй let/const, if/else, цикл for, массив, объект. Функция получает число из msg.payload и возвращает объект с полями: input, status (положительное/отрицательное), sum (сумма чисел от 1 до input), names (массив имён в верхнем регистре), count.»

2. **Для inline_keyboard в Telegram:**
   > «Как в Node-RED сформировать inline_keyboard для Telegram-бота с 4 кнопками (Рок, Поп, Классика, Электроника) в 2 ряда по 2 кнопки? Каждая кнопка с callback_data вида genre_rock и т.д.»

3. **Для HTTP-эндпоинта с валидацией:**
   > «Напиши функцию для Node-RED, которая проверяет query-параметр id: если пусто — 400, если не число — 400, если вне диапазона 1..5 — 404, иначе 200 с объектом товара.»

## 3. Освоенные ноды

| Нода | Назначение |
|---|---|
| **inject** | запуск потока (вручную или по расписанию) |
| **debug** | вывод сообщений |
| **function** | JS-логика |
| **switch** | ветвление по условию |
| **change** | установка/изменение свойств |
| **template** | генерация текста/JSON по Mustache |
| **http request** | HTTP-запросы к внешним API |
| **http in / http response** | создание собственных REST-эндпоинтов |
| **mqtt in / mqtt out** | публикация и подписка через MQTT-брокер |
| **gauge, chart** (dashboard) | виджеты визуализации |
| **telegram command / receiver / sender / event** | Telegram-бот |
| **file (write / read)** | запись и чтение файлов |
| **flow context** (в function) | хранение состояния |

## 4. Способ установки и версии

- **Способ установки:** Docker Desktop
- **Образ:** `nodered/node-red:latest`
- **Контейнер:** `mynodered`
- **Volume:** `~/node-red-data:/data`
- **Порт:** 1880

**Версии (из логов Docker):**

```
Node-RED version: v5.0.7
Node.js  version: v24.20.0
Linux 6.18.40.1-microsoft-standard-WSL2 x64 LE
```

### Скриншот версий Node-RED и Node.js из логов Docker

![Версии](screenshots/00-versions.png)

*На скриншоте — логи контейнера после запуска. Видны версии Node-RED (v5.0.7) и Node.js (v24.20.0), а также путь к файлу потоков `/data/flows.json`.*

## 5. Скриншоты всех flow

### 5.1. Inject → Debug

![Inject Debug](screenshots/01-inject-debug.png)

*Поток inject → debug. Inject срабатывает каждые 5 секунд и отправляет строку `Ковкель` с темой `lab2/Ковкель/basic`. Debug настроен на **complete msg object** — показывает все поля: `payload`, `topic`, `_msgid`. Видно, что сообщения приходят с интервалом 5 секунд.*

### 5.2. Function

![Function](lab2/screenshots/02-function.png)

*Поток inject → function → debug. Inject отправляет число `7`. Function-нода использует базовые элементы JS (`let`/`const`, `if/else`, цикл `for`, массив, объект): считает сумму чисел от 1 до 7 (`28`), проверяет тип значения, переводит массив имён в верхний регистр. Возвращает объект `{input, status, sum, names, count}`.*

### 5.3. Switch

![Switch](screenshots/03-switch.png)

*Поток inject → function → switch → два debug. Switch проверяет `msg.payload.sum`: если `>= 15` — сообщение идёт в верхний debug (`debug 3`), иначе — в нижний (`debug 4`). Так как сумма = 28, сработал верхний выход.*

### 5.4. Change

![Change](screenshots/04-change.png)

*Поток inject → change → debug. Change-нода устанавливает три поля за один проход: `msg.payload = "изменённый payload"`, `msg.topic = "lab2/Ковкель/change"`, `msg.timestamp = текущее время`. Debug показывает весь объект — видно, что все три поля изменились.*

### 5.5. Template

![Template](screenshots/05-template.png)

*Поток inject → template → debug. Inject отправляет JSON-объект `{name, group, age}`. Template по Mustache-шаблону подставляет значения в `{{payload.name}}`, `{{payload.group}}`, `{{payload.age}}` и формирует JSON. На выходе — объект со студенческой информацией.*

### 5.6. HTTP Request

![HTTP Request](screenshots/06-http-request.png)

*Поток inject → http request → debug. HTTP Request делает GET-запрос к публичному API `https://catfact.ninja/fact` (без токенов). Return настроен на **parsed JSON object**. Debug показывает объект `{fact, length}` — случайный факт о котах.*

### 5.7. MQTT

![MQTT](screenshots/07-mqtt.png)

*Поток с двумя ветками: inject → function → mqtt out (публикация), mqtt in → debug (подписка). Топик `student/Ковкель/lab2/sensor`. Inject раз в 5 секунд публикует случайное число 10–35 через MQTT-брокер `broker.hivemq.com:1883` (QoS 0). mqtt in подписан на тот же топик, поэтому число возвращается обратно — видно в Debug.*

### 5.8. GET-эндпоинты

**Первый эндпоинт — `GET /api/text`:**

![Endpoint text](screenshots/08a-endpoint-text.png)

*Простой текстовый эндпоинт. http in принимает запрос, template формирует текст, http response отправляет. В браузере виден ответ: `Привет! Это простой текстовый ответ.`*

**Второй эндпоинт — `GET /api/info`:**

![Endpoint info](screenshots/08b-endpoint-info.png)

*JSON-эндпоинт. Возвращает объект `{name: "Ковкель", role: "student", lab: "lab2"}`. Template настроен на вывод JSON.*

**Третий эндпоинт — `GET /api/items?id=N` — три сценария:**

![Endpoint items OK](screenshots/08c-endpoint-items-ok.png)

*Успешный запрос: `?id=3` → **200** с объектом товара `{id: 3, name: "Товар №3", price: 300}`.*

![Endpoint items 400](screenshots/08d-endpoint-items-400.png)

*Ошибка 400 — параметр отсутствует. Function проверяет `msg.req.query.id` — если пусто, возвращает статус 400 и сообщение «Параметр 'id' обязателен».*

![Endpoint items 404](screenshots/08e-endpoint-items-404.png)

*Ошибка 404 — товар не найден. Function проверяет диапазон `1..5`. Значение `99` вне диапазона → статус 404 с сообщением «Товар с id=99 не найден».*

### 5.9. Dashboard

![Dashboard](screenshots/09-dashboard.png)

*Dashboard с двумя виджетами: gauge (текущее значение) и chart (история). Inject раз в 5 секунд имитирует датчик температуры 15–35 °C. Виджеты объединены во вкладку «Лабораторная 2» и группу «Датчики». Доступ: `http://localhost:1880/ui`.*

### 5.10. Telegram-бот

**Команда `/start`:**

![Telegram start](screenshots/10a-telegram-start.png)

*Бот отвечает на команду `/start` приветствием. Используются ноды: telegram command (ловит команду), template (формирует текст), telegram sender (отправляет). Текст приветствия содержит описание доступных команд.*

**Echo-ответ:**

![Telegram echo](screenshots/10b-telegram-echo.png)

*Echo-ветка: telegram receiver ловит любое сообщение → function формирует ответ «Эхо: <текст>» → telegram sender отправляет обратно. Пользователь пишет `a` — бот отвечает `Эхо: a`.*

### 5.11. Файлы (с доказательством сохранения между перезапусками)

**До перезапуска:**

![Files before](screenshots/11a-before-restart.png)

*Поток записи и чтения файла. Две ветки: inject → function → write file (запись в `/data/lab2-test.txt`) и inject → read file → debug (чтение). Строка `[2026-10-05T16:02:57.240Z] мяу` была записана в файл.*

**Перезапуск контейнера:**

![Restart](screenshots/11b-restart.png)

*Терминал Git Bash: команда `docker restart mynodered` перезапускает контейнер. `docker ps` показывает статус `Up` — контейнер работает.*

**После перезапуска:**

![Files after](screenshots/11c-after-restart.png)

*После перезапуска снова кликаем «Read file» — в Debug появляется **та же самая** строка `[2026-10-05T16:02:57.240Z] мяу`. Значит, файл сохранился между перезапусками, потому что лежит в **volume** (`~/node-red-data:/data`).*

### 5.12. Контекст (счётчик)

![Context 1](screenshots/12a-context.png)

*Поток со счётчиком на **flow context**. Верхняя ветка: inject → function (increment) → debug. Function читает `flow.get("counter")`, увеличивает на 1, сохраняет `flow.set("counter", counter)`. Debug показывает `counter: 1, 2, 3` — значение сохраняется между сообщениями.*

![Context 2](screenshots/12b-context.png)

*Нижняя ветка: inject (reset) → function → debug. Function делает `flow.set("counter", 0)` — счётчик сбрасывается. После reset следующее нажатие `+1` даёт `counter: 1` — счётчик пошёл заново.*

### 5.13. Ачивка 11 — Telegram inline keyboard

![Achievement 11](screenshots/13-achievement-inline-keyboard.png)

*Ачивка: Telegram-бот с inline-клавиатурой. Команда `/music` показывает меню с 4 кнопками (Рок, Поп, Классика, Электроника). Нажатие кнопки ловится через ноду `telegram event` (Callback Query), роутер определяет, какая кнопка нажата, и отвечает «Твой выбор: <жанр>» + кнопка «Вернуться в меню». Возврат в меню показывает 4 кнопки заново.*

## 6. Выводы

В ходе лабораторной работы я освоила Node-RED как low-code инструмент. Разобралась как работают потоки. Научилась создавать собственные REST-эндпоинты, публиковать и подписываться на MQTT-топики, работать с Telegram-ботом, в том числе с inline-клавиатурой и обработкой `callback_query`. Отдельно разобралась с dashboard для визуализации данных. Работа с файлами и volume показала, как данные сохраняются между перезапусками контейнера. Понравилось больше всего создание бота - реально можно углубиться и много чего придумать, создать свой боткак бы особо без написания кода
