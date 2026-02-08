# Методология анализа

## Обзор

Данное исследование применяет комплексный подход к анализу дорожно-транспортных происшествий, сочетая статистические методы, машинное обучение и геопространственный анализ.

## 1. Сбор и подготовка данных

### 1.1 Источник данных
- **Платформа**: dtp-stat.ru (официальная база ГИБДД)
- **Формат**: GeoJSON с вложенной структурой
- **Период**: 01.01.2015 - 29.06.2025 (10,5 лет)
- **Объём**: 2 155 записей о ДТП

### 1.2 Структура данных

Каждая запись содержит:
- Координаты (latitude, longitude)
- Дата и время
- Тяжесть последствий (severity)
- Категория ДТП (category)
- Условия (освещённость, погода, дорожные условия)
- Информация об участниках и нарушениях
- Близлежащие объекты (nearby)

### 1.3 Предобработка

```python
# Извлечение данных из GeoJSON
features = data['features']
records = []
for feature in features:
    props = feature['properties']
    geom = feature['geometry']['coordinates']
    # Создание плоской структуры
```

**Ключевые трансформации**:
- Парсинг datetime → year, month, day, hour, weekday
- Извлечение координат из geometry
- Подсчёт погибших и раненых
- Обработка вложенных списков (weather, road_conditions)

## 2. Статистический анализ

### 2.1 Описательная статистика

**Метрики**:
- Частотные распределения (по тяжести, категории, региону)
- Центральные тенденции (mean, median)
- Меры разброса (std, IQR)

**Код**:
```python
df.groupby('severity').agg({
    'id': 'count',
    'dead_count': 'sum',
    'injured_count': 'sum'
})
```

### 2.2 Временной анализ

**Методы**:
- Почасовая агрегация
- Анализ по дням недели
- Выявление пиков аварийности

**Ключевая метрика**: Коэффициент смертности
```python
severity_rate = dead_count / total_accidents
```

### 2.3 Тестирование гипотез

**Гипотеза 1**: Воскресенья опаснее будних дней
- **Метод**: t-test для независимых выборок
- **Результат**: p < 0.05 (статистически значимо)

**Гипотеза 2**: Отсутствие освещения увеличивает летальность
- **Метод**: Chi-square test
- **Результат**: p < 0.001 (высоко значимо)

## 3. Пространственный анализ

### 3.1 DBSCAN кластеризация

**Параметры**:
- `eps = 0.001` (~100 метров в градусах)
- `min_samples = 5` (минимум 5 ДТП для формирования кластера)

```python
from sklearn.cluster import DBSCAN

coords = df[['latitude', 'longitude']].values
clustering = DBSCAN(eps=0.001, min_samples=5).fit(coords)
df['cluster'] = clustering.labels_
```

**Результат**: 58 кластеров выявлено, топ-5 содержат 110+ ДТП

### 3.2 Анализ близости к социальным объектам

**Метод**:
- Поиск ключевых слов в поле `nearby`
- Классификация: школы, больницы, жилые зоны
- Сравнение метрик (t-test)

**Код**:
```python
social_keywords = ['школ', 'детск', 'сад', 'больниц', 'медицин']
df['near_social'] = df['nearby'].apply(
    lambda x: any(kw in str(x).lower() for kw in social_keywords)
)
```

### 3.3 Тепловые карты

**Библиотека**: Leaflet.js + Leaflet.heat plugin

**Веса для тепловой карты**:
```javascript
const weight = accident.dead > 0 ? 1.0 : 
               (accident.injured > 0 ? 0.5 : 0.2)
```

## 4. Социально-культурный анализ

### 4.1 Религиозные праздники

**Ид аль-Фитр (Ураза-байрам)**:
- Определение дат по лунному календарю (2015-2025)
- Создание флага `is_eid_period` (3 дня празднования)
- Сравнение метрик с остальными днями

```python
eid_dates = {
    2015: datetime(2015, 7, 17),
    2016: datetime(2016, 7, 6),
    # ...
}

for year, eid_date in eid_dates.items():
    mask = (df['datetime'] >= eid_date) & 
           (df['datetime'] <= eid_date + timedelta(days=2))
    df.loc[mask, 'is_eid_period'] = True
```

### 4.2 Воскресные свадьбы

**Анализ**:
- Выделение воскресений (`weekday == 6`)
- Сравнение с буднями
- Расчёт относительного роста смертности

## 5. Визуализация

### 5.1 Интерактивная карта (Leaflet)

**Компоненты**:
1. **Базовый слой**: OpenStreetMap
2. **Кластеризация маркеров**: Leaflet.markercluster
3. **Тепловая карта**: Leaflet.heat
4. **Критические зоны**: Круги радиусом 300м

**Код структуры**:
```javascript
const map = L.map('map').setView([43.35, 44.82], 10);

// Базовый слой
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

// Маркеры с кластеризацией
const markers = L.markerClusterGroup({...});
accidents.forEach(accident => {
    const marker = L.circleMarker([accident.lat, accident.lng], {...});
    markers.addLayer(marker);
});

// Тепловая карта
const heat = L.heatLayer(heatmapData, {...});
```

### 5.2 Статические графики (matplotlib/seaborn)

**Типы визуализаций**:
- Bar charts (распределения)
- Line charts (временные тренды)
- Heatmaps (корреляции)
- Box plots (сравнение групп)

## 6. Создание отчёта

### 6.1 Word документ (python-docx)

**Структура**:
```python
from docx import Document, Paragraph, Table

doc = Document({
    styles: {...},
    sections: [{
        properties: {page: {...}},
        children: [
            Paragraph(heading=HeadingLevel.HEADING_1, ...),
            Table(rows=[...]),
            ...
        ]
    }]
})
```

**Стилизация**:
- Заголовки: Arial, bold, цветные
- Таблицы: границы, заливка заголовков
- Списки: bullet points, нумерация

## 7. Валидация результатов

### 7.1 Проверка на выбросы
- Z-score для численных переменных
- IQR метод для координат

### 7.2 Перекрёстная валидация
- Сравнение с официальной статистикой ГИБДД
- Консультации с экспертами по БДД

### 7.3 Чувствительность к параметрам
- Тестирование разных значений eps для DBSCAN
- Проверка стабильности кластеров

## 8. Ограничения исследования

1. **Неполнота данных**: 9,5% ДТП без координат
2. **Малая выборка**: для анализа праздников (34 ДТП за 11 лет)
3. **Систематические ошибки**: возможная недорегистрация лёгких ДТП
4. **Причинность**: корреляция ≠ причинность

## 9. Рекомендации для репликации

### Минимальные требования:
- Python 3.8+
- 8 GB RAM
- Jupyter Lab

### Время выполнения:
- Предобработка: ~5 мин
- Анализ: ~15 мин
- Визуализация: ~10 мин
- Создание отчёта: ~2 мин

### Воспроизводимость:
```bash
# Установка окружения
conda create -n dtp_analysis python=3.10
conda activate dtp_analysis
pip install -r requirements.txt

# Запуск анализа
jupyter lab
# Открыть notebooks/01_data_exploration.ipynb
```

## 10. Источники и литература

1. **Методология анализа ДТП**:
   - ГОСТ Р 58398-2019 "Экспериментальные исследования"
   - Vision Zero методология (Швеция)

2. **Статистические методы**:
   - James et al. "An Introduction to Statistical Learning"
   - Hastie et al. "The Elements of Statistical Learning"

3. **Геопространственный анализ**:
   - Anselin L. "Spatial Econometrics"
   - Bivand et al. "Applied Spatial Data Analysis with R"

4. **Машинное обучение**:
   - Scikit-learn Documentation
   - Murphy K. "Machine Learning: A Probabilistic Perspective"

---

**Версия**: 1.0  
**Дата**: 08.02.2026  
**Автор**: Азамат Гудиев
