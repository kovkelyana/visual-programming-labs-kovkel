# Отчёт по лабораторной работе №1

## Тема
Моделирование процессов с использованием Git и визуальных нотаций.

## Описание процесса
Описывала процесс заказа еды в ресторане с доставкой.Клиент выбирает блюда в приложении, система проверяет их наличие и обрабатывает оплату. Если оплата прошла — заказ готовится и доставляется курьером; если нет — отменяется или предлагается повторить.

## Диаграммы

### BPMN-диаграмма
![BPMN](diagrams/process-bpmn.png)

### UML Activity Diagram
![UML Activity](diagrams/activity-uml.png)

### Sequence Diagram

```mermaid
sequenceDiagram
    participant К as Клиент
    participant П as Приложение
    participant ПС as Платёжная система

    К->>П: Выбор блюд из меню
    П->>П: Проверка наличия блюд
    alt Блюдо есть в наличии
        П-->>К: Заказ доступен
        К->>П: Подтверждение и оплата
        П->>ПС: Запрос на оплату
        alt Оплата прошла
            ПС-->>П: Успешно
            П-->>К: Заказ принят
        else Оплата не прошла
            ПС-->>П: Отказ
            П-->>К: Предложение повторить оплату
        end
    else Блюда нет
        П-->>К: Предложение замены
    end
```

### Flowchart

```mermaid
flowchart TD
    A([Начало]) --> B[Выбор блюд из меню]
    B --> C{Блюдо в наличии?}
    C -- Нет --> D[Предложить замену]
    D --> B
    C -- Да --> E[Оформить заказ]
    E --> F{Оплата прошла?}
    F -- Нет --> G[Отменить заказ]
    G --> Z([Конец])
    F -- Да --> H[Передать заказ на кухню]
    H --> I[Готовить блюда]
    H --> J[Готовить напитки]
    I --> K[Собрать заказ]
    J --> K
    K --> L[Доставить клиенту]
    L --> M[Клиент получил заказ]
    M --> Z
```

## Diff: текстовый vs бинарный формат

### BPMN (.bpmn) — текстовый XML
![Diff BPMN](docs/screenshots/diff_bpmn.png)

### PNG (.png) — бинарный
![Diff PNG](docs/screenshots/diff_png.png)

### UML Activity (.drawio) — текстовый XML
![Diff drawio](docs/screenshots/diff_activity_drawio.png)

### UML Activity PNG (.png) — бинарный
![Diff activity png](docs/screenshots/diff_activity_uml.png)

### Sequence (.md) — текстовый Mermaid
![Diff sequence](docs/screenshots/diff_sequence_md.png)

### Flowchart (.md) — текстовый Mermaid
![Diff flowchart](docs/screenshots/diff_flowchart_md.png)

## Выводы
В ходе работы вспомнила базовые команды Git, вспомнила как с ним работать. Узнала о разнообразии диаграмм: какие они бывают, как их строить, попробовала новые для себя инструменты. На практике увидела как работает команда diff для разных типов файлов.