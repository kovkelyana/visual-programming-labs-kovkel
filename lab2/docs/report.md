# Отчёт по лабораторной работе №2 — Node-RED

## 1. Краткое описание выполненного

В рамках лабораторной работы №2 я освоила Node-RED. Были собраны и задеплоены 13 потоков, покрывающих базовые и продвинутые ноды:

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

Дополнительно выполнена ачивка 11 — Telegram-бот с inline-клавиатурой и состоянием.

## 2. Использованные AI-промпты

DeepSeek использовался для генерации кода Function-нод, синтаксиса Mustache-шаблонов и структуры inline_keyboard. Ключевые промпты:

1. **Для Function-ноды с базовыми элементами JS:**
   > «Сгенерируй код для function node Node-RED: используй let/const, if/else, цикл for, массив, объект. Функция получает число из msg.payload и возвращает объект с полями: input, status (положительное/отрицательное), sum (сумма чисел от 1 до input), names (массив имён в верхнем регистре), count. Верни msg с полем payload-объектом.»

2. **Для inline_keyboard в Telegram:**
   > «Как в Node-RED сформировать inline_keyboard для Telegram-бота с 4 кнопками (Рок, Поп, Классика, Электроника) в 2 ряда по 2 кнопки? Каждая кнопка с callback_data вида genre_rock и т.д. Покажи структуру msg.payload. Сгенерируй код и объясни его.»

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

**Скриншот версий:** `screenshots/00-versions.png`

## 5. Скриншоты всех flow

Все скриншоты находятся в папке `lab2/screenshots/`.

### 2.1. Inject → Debug
![Inject Debug](screenshots/01-inject-debug.png)

### 2.2. Function
![Function](screenshots/02-function.png)

### 2.3. Switch
![Switch](screenshots/03-switch.png)

### 2.4. Change
![Change](screenshots/04-change.png)

### 2.5. Template
![Template](screenshots/05-template.png)

### 2.6. HTTP Request
![HTTP Request](screenshots/06-http-request.png)

### 2.7. MQTT
![MQTT](screenshots/07-mqtt.png)

### 2.8. GET-эндпоинты
![Endpoint text](screenshots/08a-endpoint-text.png)
![Endpoint info](screenshots/08b-endpoint-info.png)
![Endpoint items OK](screenshots/08c-endpoint-items-ok.png)
![Endpoint items 400](screenshots/08d-endpoint-items-400.png)
![Endpoint items 404](screenshots/08e-endpoint-items-404.png)

### 2.9. Dashboard
![Dashboard](screenshots/09-dashboard.png)

### 2.10. Telegram-бот
![Telegram start](screenshots/10a-telegram-start.png)
![Telegram echo](screenshots/10b-telegram-echo.png)

### 2.11. Файлы (с доказательством сохранения между перезапусками)
![Files before restart](screenshots/11a-before-restart.png)
![Restart](screenshots/11b-restart.png)
![Files after restart](screenshots/11c-after-restart.png)

### 2.12. Контекст (счётчик)
![Context](screenshots/12a-context.png)
![Context 2](screenshots/12b-context.png)

### Ачивка 11. Telegram inline keyboard
![Achievement 11](screenshots/13-achievement-inline-keyboard.png)

## 6. Выводы

В ходе лабораторной работы я освоила Node-RED как low-code инструмент. Разобралась как работают потоки. Научился создавать собственные REST-эндпоинты, публиковать и подписываться на MQTT-топики, работать с Telegram-ботом, в том числе с inline-клавиатурой и обработкой `callback_query`. Отдельно разобралась с dashboard для визуализации данных. Работа с файлами и volume показала, как данные сохраняются между перезапусками контейнера. Понравилось больше всего создание бота - реально можно углубиться и много чего придумать, создать свой боткак бы особо без написания кода