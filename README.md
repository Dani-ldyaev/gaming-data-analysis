# Исследование данных игровых платформ

Курсовой проект на тему **«Исследование данных игровых платформ для выявления паттернов популярности и предпочтений игроков»**.

Цель проекта — объединить данные различных игровых и медиаплатформ и исследовать взаимосвязи между характеристиками игр, пользовательской активностью, отзывами и медиапопулярностью.

Основные источники данных:

- Steam
- RAWG
- Epic Games Store
- Twitch
- YouTube
- Metacritic
- Росстат
- Habr
- Kaggle datasets

В проекте используются **Pandas, PySpark, DuckDB, SQL и Parquet**.  
Машинное обучение в рамках проекта **не используется**.

---

## Структура проекта

```text
gaming-data-analysis/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   ├── 01_data_acquisition.ipynb
│   ├── 02_data_preparation.ipynb
│   ├── 03_data_integration.ipynb
│   ├── 04_eda.ipynb
│   ├── 05_hypothesis_testing.ipynb
│   └── 06_visualization.ipynb
│
├── raw/
│   ├── steam/
│   ├── twitch/
│   ├── epic/
│   ├── rawg/
│   ├── youtube/
│   ├── rosstat/
│   └── habr/
│
├── processed/
│   ├── games/
│   ├── reviews/
│   ├── recommendations/
│   ├── players/
│   ├── twitch/
│   └── youtube/
│
├── warehouse/
│   └── gaming_analytics.duckdb
│
└── docs/
    └── ...
```

## Слои данных

**`raw/`** — исходные данные без изменений.

**`processed/`** — очищенные, нормализованные и подготовленные данные в формате Parquet.

**`warehouse/`** — аналитическое хранилище DuckDB со Star Schema.

Основные таблицы:

```text
dim_game

fact_reviews
fact_recommendations
fact_players
fact_twitch
fact_youtube
```

## Основной аналитический контур

```text
Steam ─────┐
RAWG ──────┼──→ dim_game
Epic ──────┘

Reviews ───────────────→ fact_reviews
Recommendations ──────→ fact_recommendations
Players ──────────────→ fact_players
Twitch ───────────────→ fact_twitch
YouTube ──────────────→ fact_youtube
```

Дополнительные источники (Metacritic, Росстат, Habr и другие Kaggle datasets) используются для расширения и проверки анализа.

## Технологии

- Python
- Jupyter Notebook
- Pandas
- PySpark
- DuckDB
- SQL
- Parquet
- Kaggle API / `kagglehub`
- Steam API
- RAWG API
- YouTube Data API

## Основные задачи

1. Собрать данные из различных источников.
2. Провести очистку и подготовку данных.
3. Интегрировать данные различных платформ.
4. Построить аналитическое хранилище в DuckDB.
5. Исследовать популярность игр и предпочтения игроков.
6. Проверить статистические гипотезы.
7. Представить результаты с помощью визуализаций.
