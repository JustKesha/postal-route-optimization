# 🛫 Оптимизация авиамаршрутов для почтовых отправлений с использованием роевого интеллекта

[![Сайт](https://img.shields.io/badge/Сайт-LIVE-green?logo=github)](https://justkesha.github.io/postal-route-optimization)
[![Языки: RU](https://img.shields.io/badge/Языки-RU-red.svg)](#)
[![Лицензия: MIT](https://img.shields.io/badge/Лицензия-MIT-blue.svg)](https://opensource.org/licenses/MIT)

[Translate into English?](README_ENG.md)

<img alt="Banner" width="100%" src="preview.png" />

Проект представляет собой простое [веб-приложение](https://justkesha.github.io/postal-route-optimization/) для построения оптимальных маршрутов доставки почтовых отправлений авиатранспортом с применением алгоритмов роевого интеллекта.

## Содержание
1. [Математическая модель](#математическая-модель)
2. [Алгоритмы оптимизации](#алгоритмы-оптимизации)
3. [Расчет расстояний](#расчет-расстояний)
4. [Интерфейс](#интерфейс)
5. [Запуск проекта](#запуск-проекта)
6. [Добавление новых городов](#добавление-новых-городов)

## Математическая модель

### Формальная постановка задачи
Задача представляет собой модифицированную версию задачи маршрутизации транспортных средств (CVRP - Capacitated Vehicle Routing Problem) с дополнительными ограничениями:

1. **Входные параметры**:
   - `G = (V, E)` - граф городов (вершины) и авиамаршрутов (ребра)
   - `cᵢⱼ` - стоимость перелета из города `i` в город `j` (расстояние в км)
   - `dᵢ` - вес отправлений для города `i` (кг)
   - `Q` - грузоподъемность одного самолета (кг)
   - `v₀` - начальный город (хаб)

2. **Целевая функция**:
   Минимизировать общую стоимость доставки:

   ```min Σ c(Rₖ) для всех рейсов k```,

    где `c(Rₖ)` - стоимость рейса `k`, рассчитываемая как сумма расстояний между городами в маршруте `Rₖ`

3. **Ограничения**:
- `Σ dᵢ ≤ Q` для каждого рейса (суммарный вес в одном рейсе не превышает грузоподъемность)
- Каждый город назначения посещается ровно один раз
- Все рейсы начинаются и заканчиваются в начальном городе

## Алгоритмы оптимизации

### 1. Роевой алгоритм (PSO - Particle Swarm Optimization)

#### Параметры алгоритма:
- Размер роя: 50 частиц
- Количество итераций: 100
- Инерция (`ω`): 0.7
- Когнитивный параметр (`c₁`): 1.5
- Социальный параметр (`c₂`): 1.5

#### Формулы обновления:
1. Обновление скорости:

    ```vᵢᵗ⁺¹ = ω·vᵢᵗ + c₁·r₁·(pbestᵢ - xᵢᵗ) + c₂·r₂·(gbest - xᵢᵗ)```

2. Обновление позиции:

    ```xᵢᵗ⁺¹ = xᵢᵗ + vᵢᵗ⁺¹```


#### Особенности реализации:
- Каждая частица представляет собой возможный маршрут
- Оператор изменения позиции реализован как оператор мутации маршрута
- Функция приспособленности учитывает:
- Общее расстояние маршрута
- Количество необходимых рейсов (с коэффициентом штрафа 500 км за каждый дополнительный рейс)

### 2. Жадный алгоритм (для сравнения)
1. Сортировка городов по расстоянию от начального
2. Последовательное добавление в маршрут ближайших городов с учетом грузоподъемности

## Расчет расстояний

Расстояния между городами рассчитываются по формуле гаверсинусов для сферической Земли:

    a = sin²(Δφ/2) + cos φ₁ · cos φ₂ · sin²(Δλ/2)
    c = 2 · atan2(√a, √(1−a))
    d = R · c


где:
- `φ` - широта в радианах
- `λ` - долгота в радианах
- `R` - радиус Земли (6371 км)

Пример расчета для Москва → Санкт-Петербург:

    φ₁ = 55.7558° = 0.9731 рад
    φ₂ = 59.9343° = 1.0459 рад
    Δφ = 4.1785° = 0.0729 рад
    Δλ = 7.2822° = 0.1271 рад

    a = sin²(0.03645) + cos(0.9731)·cos(1.0459)·sin²(0.06355) ≈ 0.00306
    c = 2·atan2(√0.00306, √0.99694) ≈ 0.1108
    d = 6371 · 0.1108 ≈ 706 км


## Интерфейс

### Структура приложения:
1. **Панель ввода данных**:
   - Выбор начального города
   - Добавление пунктов назначения с указанием веса
   - Настройка параметров алгоритма

2. **Визуализация**:
   - Интерактивная карта с городами
   - Анимация маршрутов
   - Графическое отображение самолетов

3. **Панель результатов**:
   - Общая статистика по маршруту
   - Детализация по каждому рейсу
   - Расчет времени и стоимости

## Запуск проекта

Проект не требует серверной части и дополнительных зависимостей. Для запуска:

1. Сохраните код в файл `index.html`
2. Откройте файл в любом современном браузере

## Добавление новых городов

Для добавления новых городов в базу данных необходимо изменить массив `cities` в JavaScript-коде:

```javascript
const cities = [
    // существующие города
    {
        name: "Новый город",
        lat: XX.XXXX, // широта в градусах
        lng: YY.YYYY, // долгота в градусах
        airport: "Название аэропорта"
    },
    // ...
];
```

Матрица расстояний будет автоматически пересчитана при загрузке страницы.

## Дополнительные параметры
Можно настроить: 

- Среднюю скорость самолета (по умолчанию 800 км/ч)
- Время обработки в аэропорту (по умолчанию 1 час)
- Стоимость перевозки за км (по умолчанию 100 руб/км)
