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

![Версии](/lab2/screenshots/00-versions.png)

*На скриншоте — логи контейнера после запуска. Видны версии Node-RED (v5.0.7) и Node.js (v24.20.0), а также путь к файлу потоков `/data/flows.json`.*

## 5. Скриншоты всех flow

### 5.1. Inject → Debug

![Inject Debug](/lab2/screenshots/01-inject-debug.png)

*Поток inject → debug. Inject срабатывает каждые 5 секунд и отправляет строку `Ковкель` с темой `lab2/Ковкель/basic`. Debug настроен на complete msg object — показывает все поля: `payload`, `topic`, `_msgid`.*

### 5.2. Function

![Function](/lab2/screenshots/02-function.png)

*Поток inject → function → debug. Inject отправляет число `7`. Function-нода использует базовые элементы JS: считает сумму чисел от 1 до 7 (`28`), проверяет тип, переводит массив имён в верхний регистр. Возвращает объект `{input, status, sum, names, count}`.*

### 5.3. Switch

![Switch](/lab2/screenshots/03-switch.png)

*Поток inject → function → switch → два debug. Switch проверяет `msg.payload.sum`: если `>= 15` — идёт в верхний debug, иначе — в нижний. При сумме 28 сработал верхний выход.*

### 5.4. Change

![Change](/lab2/screenshots/04-change.png)

*Поток inject → change → debug. Change-нода устанавливает три поля за один проход: `msg.payload`, `msg.topic`, `msg.timestamp`.*

### 5.5. Template

![Template](/lab2/screenshots/05-template.png)

*Поток inject → template → debug. Template по Mustache-шаблону подставляет `{{payload.name}}`, `{{payload.group}}`, `{{payload.age}}` и формирует JSON.*

### 5.6. HTTP Request

![HTTP Request](/lab2/screenshots/06-http-request.png)

*Поток inject → http request → debug. GET-запрос к `https://catfact.ninja/fact`. Return = parsed JSON object. Debug показывает `{fact, length}`.*

### 5.7. MQTT

![MQTT](/lab2/screenshots/07-mqtt.png)

*Две ветки: публикация и подписка на топик `student/Ковкель/lab2/sensor`. Брокер `broker.hivemq.com:1883`, QoS 0. Число публикуется и возвращается обратно через подписку.*

### 5.8. GET-эндпоинты

**Первый эндпоинт — `GET /api/text`:**

![Endpoint text](/lab2/screenshots/08a-endpoint-text.png)

*Простой текстовый эндпоинт. Возвращает `Привет! Это простой текстовый ответ.`*

**Второй эндпоинт — `GET /api/info`:**

![Endpoint info](/lab2/screenshots/08b-endpoint-info.png)

*JSON-эндпоинт. Возвращает `{name: "Ковкель", role: "student", lab: "lab2"}`.*

**Третий эндпоинт — `GET /api/items?id=N` — три сценария:**

![Endpoint items OK](/lab2/screenshots/08c-endpoint-items-ok.png)

*Успешный запрос: `?id=3` → 200 с объектом товара.*

![Endpoint items 400](/lab2/screenshots/08d-endpoint-items-400.png)

*Ошибка 400 — параметр отсутствует.*

![Endpoint items 404](/lab2/screenshots/08e-endpoint-items-404.png)

*Ошибка 404 — товар не найден.*

### 5.9. Dashboard

![Dashboard](/lab2/screenshots/09-dashboard.png)

*Dashboard с gauge и chart. Имитация датчика температуры 15–35 °C. Доступ: `http://localhost:1880/ui`.*

### 5.10. Telegram-бот

**Команда `/start`:**

![Telegram start](/lab2/screenshots/10a-telegram-start.png)

*Бот отвечает на `/start` приветствием. Ноды: telegram command → template → telegram sender.*

**Echo-ответ:**

![Telegram echo](/lab2/screenshots/10b-telegram-echo.png)

*Echo-ветка: receiver → function → sender. Пользователь пишет `a` — бот отвечает `Эхо: a`.*

### 5.11. Файлы (с доказательством сохранения между перезапусками)

**До перезапуска:**

![Files before](/lab2/screenshots/11a-before-restart.png)

*Запись и чтение файла `/data/lab2-test.txt`. Строка записана.*

**Перезапуск контейнера:**

![Restart](/lab2/screenshots/11b-restart.png)

*Терминал: `docker restart mynodered` + `docker ps`.*

**После перезапуска:**

![Files after](/lab2/screenshots/11c-after-restart.png)

*Та же строка в Debug — файл сохранился в volume.*

### 5.12. Контекст (счётчик)

![Context 1](/lab2/screenshots/12a-context.png)

*Flow context: inject → function (increment) → debug. `counter: 1, 2, 3` — значение сохраняется между сообщениями.*

![Context 2](/lab2/screenshots/12b-context.png)

*Reset: `flow.set("counter", 0)`. После reset счётчик снова начинает с 1.*

### 5.13. Ачивка 11 — Telegram inline keyboard

![Achievement 11](/lab2/screenshots/13-achievement-inline-keyboard.png)

*Ачивка: бот с inline-клавиатурой. Команда `/music` → меню с 4 кнопками (Рок, Поп, Классика, Электроника). Нажатие ловится `telegram event` (Callback Query). Роутер отвечает «Твой выбор: X» + кнопка «Вернуться в меню».*

## 6. Выводы

В ходе лабораторной работы я освоила Node-RED как low-code инструмент. Разобралась как работают потоки. Научилась создавать собственные REST-эндпоинты, публиковать и подписываться на MQTT-топики, работать с Telegram-ботом, в том числе с inline-клавиатурой и обработкой `callback_query`. Отдельно разобралась с dashboard для визуализации данных. Работа с файлами и volume показала, как данные сохраняются между перезапусками контейнера. Понравилось больше всего создание бота - реально можно углубиться и много чего придумать, создать свой боткак бы особо без написания кода
