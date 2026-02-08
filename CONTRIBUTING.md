# Руководство по внесению вклада

Спасибо за интерес к проекту! Мы приветствуем любые улучшения.

## Как внести вклад

### 1. Сообщить об ошибке (Bug Report)

Если вы нашли ошибку:

1. Проверьте, нет ли уже такого [issue](../../issues)
2. Создайте новый issue с шаблоном:

```markdown
**Описание ошибки**
Краткое описание проблемы

**Как воспроизвести**
1. Шаг 1
2. Шаг 2
3. ...

**Ожидаемое поведение**
Что должно было произойти

**Скриншоты**
Если применимо

**Окружение**
- ОС: [Windows/Linux/macOS]
- Python версия: [3.8/3.9/3.10]
- Версия библиотек: см. requirements.txt
```

### 2. Предложить улучшение (Feature Request)

1. Создайте issue с тегом `enhancement`
2. Опишите:
   - Какую проблему это решит
   - Предложенное решение
   - Альтернативы

### 3. Внести код (Pull Request)

#### Подготовка

```bash
# Форкните репозиторий через GitHub UI

# Клонируйте свой форк
git clone https://github.com/YOUR_USERNAME/car_accident_analysis.git
cd car_accident_analysis

# Создайте ветку для изменений
git checkout -b feature/my-feature
# или
git checkout -b fix/bug-description
```

#### Разработка

```bash
# Установите зависимости
pip install -r requirements.txt

# Внесите изменения

# Проверьте код (если есть тесты)
pytest

# Проверьте стиль кода
flake8 .
```

#### Отправка

```bash
# Закоммитьте изменения
git add .
git commit -m "feat: добавил анализ сезонности"

# Пушьте в свой форк
git push origin feature/my-feature

# Создайте Pull Request через GitHub UI
```

## Стандарты кода

### Python код

- Следуйте [PEP 8](https://www.python.org/dev/peps/pep-0008/)
- Используйте осмысленные имена переменных
- Добавляйте docstrings для функций
- Комментируйте сложную логику

**Пример**:
```python
def calculate_fatality_rate(df: pd.DataFrame, group_by: str) -> pd.DataFrame:
    """
    Рассчитывает коэффициент смертности по группам.
    
    Args:
        df: DataFrame с данными ДТП
        group_by: Колонка для группировки
        
    Returns:
        DataFrame с коэффициентами
    """
    return df.groupby(group_by).agg({
        'dead_count': 'sum',
        'id': 'count'
    }).assign(
        fatality_rate=lambda x: x['dead_count'] / x['id']
    )
```

### Jupyter Notebooks

- Очищайте outputs перед коммитом
- Используйте markdown ячейки для структуры
- Разбивайте на логические секции
- Добавляйте комментарии

### Коммиты

Используйте [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: новая функциональность
fix: исправление бага
docs: обновление документации
style: форматирование кода
refactor: рефакторинг
test: добавление тестов
chore: обновление зависимостей
```

**Примеры**:
```bash
git commit -m "feat: добавил анализ сезонности ДТП"
git commit -m "fix: исправил ошибку в парсинге дат"
git commit -m "docs: обновил README с новыми графиками"
```

## Структура проекта

При добавлении новых файлов следуйте структуре:

```
car_accident_analysis/
├── data/
│   ├── raw/              # Только чтение
│   └── processed/        # Результаты обработки
├── notebooks/            # Jupyter notebooks
│   └── XX_descriptive_name.ipynb  # Используйте префиксы 01_, 02_
├── src/                  # Переиспользуемый код (если нужно)
│   ├── __init__.py
│   ├── preprocessing.py
│   ├── analysis.py
│   └── visualization.py
├── outputs/              # Результаты анализа
│   ├── reports/
│   ├── maps/
│   └── figures/
└── docs/                 # Документация
```

## Приоритетные улучшения

Мы особенно приветствуем:

### Высокий приоритет
- [ ] Predictive modeling (XGBoost, Random Forest)
- [ ] Dashboard с Streamlit/Plotly Dash
- [ ] Time series forecasting
- [ ] Автоматизация обновления данных

### Средний приоритет
- [ ] Network analysis нарушений
- [ ] Сравнение с другими регионами РФ
- [ ] A/B тестирование мер безопасности
- [ ] Sentiment analysis отзывов водителей

### Низкий приоритет
- [ ] Мобильное приложение
- [ ] API для доступа к данным
- [ ] Интеграция с Telegram bot

## Процесс ревью

1. Автоматические проверки (если настроены CI/CD)
2. Ревью кода мейнтейнером
3. Обсуждение и правки (если нужно)
4. Мерж в main

**Время ревью**: обычно 2-3 дня

## Вопросы?

- Создайте [issue с вопросом](../../issues/new)
- Напишите в Discussions
- Email: your.email@example.com

## Кодекс поведения

Мы придерживаемся [Contributor Covenant](https://www.contributor-covenant.org/).

Будьте:
- Уважительны к другим
- Конструктивны в критике
- Открыты к обратной связи
- Благодарны за вклад

## Лицензия

Ваш вклад будет лицензирован под [MIT License](LICENSE).

---

Спасибо за вклад в проект! 🙏
