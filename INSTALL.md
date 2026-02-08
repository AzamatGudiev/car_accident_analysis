# Инструкция по установке

## Системные требования

- **Python**: 3.8 или выше
- **ОС**: Windows, Linux, macOS
- **RAM**: минимум 4 GB (рекомендуется 8 GB)
- **Место на диске**: ~2 GB

## Установка

### Вариант 1: С использованием venv (рекомендуется)

```bash
# 1. Клонировать репозиторий
git clone https://github.com/AzamatGudiev/car_accident_analysis.git
cd car_accident_analysis

# 2. Создать виртуальное окружение
python -m venv venv

# 3. Активировать окружение
# На Windows:
venv\Scripts\activate
# На Linux/macOS:
source venv/bin/activate

# 4. Обновить pip
pip install --upgrade pip

# 5. Установить зависимости
pip install -r requirements.txt

# 6. Запустить Jupyter Lab
jupyter lab
```

### Вариант 2: С использованием Conda

```bash
# 1. Клонировать репозиторий
git clone https://github.com/AzamatGudiev/car_accident_analysis.git
cd car_accident_analysis

# 2. Создать окружение из файла
conda create -n dtp_analysis python=3.10

# 3. Активировать окружение
conda activate dtp_analysis

# 4. Установить зависимости
pip install -r requirements.txt

# 5. Запустить Jupyter Lab
jupyter lab
```

## Настройка данных

### Скачивание исходных данных

1. Перейдите на https://dtp-stat.ru/opendata
2. Выберите регион: **Республика Ингушетия**
3. Скачайте файл в формате **GeoJSON**
4. Сохраните файл как `data/raw/respublika-ingushetiia_2.geojson`

Или используйте wget (Linux/macOS):
```bash
# Замените URL на актуальный
wget -O data/raw/respublika-ingushetiia_2.geojson \
  "https://dtp-stat.ru/api/geojson/region/id"
```

### Проверка данных

```bash
# Проверить размер файла (должен быть ~3-4 MB)
ls -lh data/raw/

# Проверить формат (первые строки)
head -n 5 data/raw/respublika-ingushetiia_2.geojson
```

## Первый запуск

```bash
# Активировать окружение (если ещё не активировано)
source venv/bin/activate  # Linux/macOS
# или
venv\Scripts\activate      # Windows

# Запустить Jupyter Lab
jupyter lab

# В браузере откроется интерфейс
# Перейдите в папку notebooks/
# Откройте 01_data_exploration.ipynb
# Выполните все ячейки (Run -> Run All Cells)
```

## Возможные проблемы

### Problem: `ModuleNotFoundError: No module named 'pandas'`
**Решение**: 
```bash
pip install pandas
# или переустановите все зависимости
pip install -r requirements.txt
```

### Problem: `FileNotFoundError: data/raw/respublika-ingushetiia_2.geojson`
**Решение**: Скачайте файл данных (см. раздел "Настройка данных")

### Problem: `OSError: [Errno 28] No space left on device`
**Решение**: Освободите место на диске (минимум 2 GB)

### Problem: Jupyter Lab не открывается в браузере
**Решение**: 
```bash
# Запустите с явным указанием браузера
jupyter lab --browser=chrome
# или скопируйте URL из терминала и откройте вручную
```

### Problem: Ошибки с кодировкой (Windows)
**Решение**: 
```bash
# Установите кодировку UTF-8
set PYTHONUTF8=1
# или в PowerShell
$env:PYTHONUTF8=1
```

## Проверка установки

Выполните следующий скрипт:

```python
# test_installation.py
import sys
print(f"Python версия: {sys.version}")

try:
    import pandas as pd
    print(f"✓ pandas {pd.__version__}")
except ImportError:
    print("✗ pandas не установлен")

try:
    import numpy as np
    print(f"✓ numpy {np.__version__}")
except ImportError:
    print("✗ numpy не установлен")

try:
    import sklearn
    print(f"✓ scikit-learn {sklearn.__version__}")
except ImportError:
    print("✗ scikit-learn не установлен")

try:
    import folium
    print(f"✓ folium установлен")
except ImportError:
    print("✗ folium не установлен")

print("\n✅ Установка завершена успешно!")
```

Запустите:
```bash
python test_installation.py
```

## Деинсталляция

```bash
# Удалить виртуальное окружение
rm -rf venv/  # Linux/macOS
# или
rmdir /s venv  # Windows

# Удалить репозиторий
cd ..
rm -rf car_accident_analysis/
```

## Обновление

```bash
# Обновить код из репозитория
git pull origin main

# Обновить зависимости
pip install --upgrade -r requirements.txt
```

## Поддержка

При возникновении проблем:

1. Проверьте [Issues](https://github.com/AzamatGudiev/car_accident_analysis/issues)
2. Создайте новый Issue с описанием проблемы
3. Напишите на email: your.email@example.com

---

**Успешной установки! 🚀**
