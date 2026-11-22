# Источники данных

## Назначение

Здесь перечислены источники, используемые текущими ноутбуками 2.2 и 2.3. Статус и дата получения в конкретном запуске фиксируются в создаваемом ноутбуком 2.2 файле `docs/data_sources_registry.csv`.

Steam Reviews и Epic Games Store не входят в текущий процесс: актуальный 2.2 их не загружает, а 2.3 не подготавливает.

## Kaggle

Ноутбук 2.2 загружает шесть наборов через KaggleHub и копирует их файлы в `raw/`.

| Набор | Kaggle handle | Каталог назначения | Использование |
|---|---|---|---|
| FronkonGames Steam Games Dataset | `fronkongames/steam-games-dataset` | `raw/steam/fronkongames/` | Каталог игр Steam |
| RAWG Games Dataset | `atalaydenknalbant/rawg-games-dataset` | `raw/rawg/` | Каталог игр RAWG |
| Top 1000 Twitch Streamers | `hibrahimag1/top-1000-twitch-streamers-data-may-2024` | `raw/twitch/streamers/` | Профили стримеров |
| Game Recommendations on Steam | `antonkozyriev/game-recommendations-on-steam` | `raw/steam/recommendations/` | Игры, рекомендации, пользователи и метаданные |
| Evolution of Top Games on Twitch | `rankirsh/evolution-of-top-games-on-twitch` | `raw/twitch/evolution/` | Помесячные данные Twitch |
| Steam Monthly Average Players | `lunthu/steam-monthly-average-players` | `raw/steam/monthly_players/` | Динамика числа игроков Steam |

### Steam Games

Основной файл для подготовки — `raw/steam/fronkongames/games.json`. В наборе также есть `games.csv`, но 2.3 использует JSON: CSV-представление может иметь смещённые столбцы.

Результат подготовки: `processed/games/steam_games.parquet`.

### RAWG

Набор представлен CSV, Parquet и JSONL — альтернативными форматами одного источника. В 2.3 используется `raw/rawg/videogames_data.parquet`.

Результат подготовки: `processed/games/rawg_games.parquet`.

### Steam Recommendations

Используются файлы:

- `raw/steam/recommendations/games.csv`;
- `raw/steam/recommendations/recommendations.csv`;
- `raw/steam/recommendations/users.csv`;
- `raw/steam/recommendations/games_metadata.json` в формате JSON Lines.

2.3 подготавливает таблицы игр, рекомендаций, пользователей и метаданных. Рекомендации связываются с играми по `app_id`.

### Steam Monthly Players

Файл: `raw/steam/monthly_players/steamcharts.csv`.

Единица наблюдения — игра за месяц. В 2.3 месяц преобразуется в дату, а `gain` — в числовой тип. Значения, которые не удалось преобразовать, становятся пропусками; исходные значения сохраняются в `gain_raw`.

### Twitch

Используются:

- `raw/twitch/evolution/Twitch_game_data.csv` — показатели отдельных игр по месяцам;
- `raw/twitch/evolution/Twitch_global_data.csv` — сводные показатели платформы;
- `raw/twitch/streamers/datasetV2.csv` — характеристики стримеров.

В наборах Twitch нет универсального ID игры. Название используется только для формирования кандидатов сопоставления.

## YouTube Data API v3

В 2.2 выбираются 30 игр Steam с наибольшим числом рекомендаций. Для каждой выполняется поиск до 10 видео по запросу с названием игры. Для найденных видео запрашиваются доступные счётчики просмотров, отметок «Нравится» и комментариев.

Создаваемые исходные файлы:

- `raw/youtube/youtube_search_results.json`;
- `raw/youtube/youtube_video_statistics.json`;
- `raw/youtube/youtube_videos.csv`.

В 2.3 используется CSV. Поле `game_name` — поисковый запрос, а не подтверждение того, что ролик посвящён этой игре. Один `video_id` может встречаться в результатах нескольких запросов.

Ключ хранится локально в корневом `.env` как `YOUTUBE_API_KEY`; его нельзя добавлять в Git. Результаты зависят от API-квоты и доступности статистики.

## Habr

2.2 получает текст двух статей об игровой индустрии и сохраняет URL, заголовок, HTTP-статус и текст в `raw/habr/articles.json`.

2.3 очищает заголовок и текст для NLP и записывает `processed/text/habr_articles.parquet`. Две статьи подходят для демонстрации обработки текста, но не являются репрезентативной выборкой отрасли.

## Wikidata Query Service

2.2 выполняет SPARQL-запрос с лимитом 1000 строк и сохраняет исходный XML в `raw/xml/wikidata/wikidata_video_games.xml`.

Запрос получает URI игры, название, дату выхода, жанр, платформу, разработчика и издателя. В 2.3 XML преобразуется в `processed/games/wikidata_games.parquet`.

Лимит относится к строкам результата, а не к уникальным сущностям. Одна игра Wikidata может встречаться в нескольких строках из-за нескольких значений атрибутов.

## Неиспользуемые источники

Текущие ноутбуки не используют Steam Reviews, Epic Games Store, Steam API или RAWG API. Наличие старых либо дополнительных локальных файлов не означает, что источник входит в основной процесс проекта.