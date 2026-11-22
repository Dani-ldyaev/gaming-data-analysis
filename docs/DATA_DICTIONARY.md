# Словарь данных

## Правила чтения

Описаны таблицы, формируемые ноутбуком 2.3. Имена исходных полей сохраняются, если ноутбук явно их не преобразует.

Шкалы: номинальная — ID и категории; порядковая — упорядоченные категории; интервальная — даты, годы и рейтинги без естественного нуля; отношений — количественные величины с естественным нулём; бинарная — два состояния.

## Игровые каталоги

### Steam Games

Файл: `processed/games/steam_games.parquet`. Источник: `games.json`. Единица наблюдения — игра; ключ — `AppID`.

| Поле | Физический смысл | Тип | Аналитическая роль |
|---|---|---|---|
| `AppID` | Steam AppID | Строка | Номинальный ключ и связь |
| `name` | Название игры | Строка | Идентификация |
| `release_date` | Дата выхода в исходном формате | Строка | Временной фактор |
| `price` | Цена из набора | Число | Количественный фактор |
| `genres`, `categories` | Жанры и категории в сериализованном виде | Строка | Категориальные факторы |
| `positive`, `negative` | Число положительных и отрицательных оценок | Целое | Показатели интереса |
| `recommendations` | Число рекомендаций | Целое | Показатель популярности |

2.3 читает каталог из JSON, поскольку CSV-представление может иметь смещённые столбцы.

### RAWG Games

Файл: `processed/games/rawg_games.parquet`. Источник: `raw/rawg/videogames_data.parquet`. Единица наблюдения — игра; ключ — `id`.

| Поле | Физический смысл | Аналитическая роль |
|---|---|---|
| `id` | Идентификатор игры в RAWG | Ключ источника |
| `name` | Название | Идентификация и сопоставление |
| `released` | Дата выхода | Временной фактор |
| `rating`, `rating_top` | Оценка и верхняя граница шкалы RAWG | Характеристики оценки |
| `ratings_count`, `reviews_count` | Количество оценок и отзывов | Показатели вовлечённости |
| `metacritic` | Оценка Metacritic | Показатель оценки |
| `playtime` | Игровое время по метрике источника | Показатель вовлечённости |
| `genres`, `platforms`, `developers`, `publishers` | Жанры, платформы, разработчики и издатели | Категориальные характеристики |

Числовые поля и поля с характеристиками сохраняются из выбранного RAWG Parquet с типами источника. RAWG `id` не является Steam AppID.

## Steam Recommendations

### Каталог игр

Файл: `processed/recommendations/steam_recommendation_games.parquet`. Единица наблюдения — игра; ключ — `app_id`.

Поля: `app_id`, `title`, `date_release`, `win`, `mac`, `linux`, `rating`, `positive_ratio`, `user_reviews`, `price_final`, `price_original`, `discount`, `steam_deck`.

`app_id` — ID игры; `title` — название; `date_release` — дата; `win`, `mac`, `linux` и `steam_deck` — признаки поддержки; `rating` — категория рейтинга; `positive_ratio` — доля положительных отзывов; `user_reviews` — число отзывов; `price_final` и `price_original` — цены; `discount` — скидка.

### Рекомендации

Файл: `processed/recommendations/steam_recommendations.parquet`. Единица наблюдения — рекомендация; ключ — `review_id`.

Поля: `app_id`, `helpful`, `funny`, `date`, `is_recommended`, `hours`, `user_id`, `review_id`.

`app_id` и `user_id` связывают запись с игрой и пользователем. `is_recommended` — бинарный признак; `date` — дата; `hours` — игровое время; `helpful` и `funny` — счётчики реакции. В текущей схеме нет отдельного текстового поля отзыва.

### Пользователи

Файл: `processed/recommendations/steam_users.parquet`. Единица наблюдения — пользователь; ключ — `user_id`.

Поля: `user_id`, `products`, `reviews`. `products` и `reviews` — агрегированные счётчики активности.

### Метаданные игр

Файл: `processed/recommendations/steam_game_metadata.parquet`. Единица наблюдения — игра; ключ — `app_id`.

- `app_id` — Steam AppID.
- `description` — описание игры, текстовый признак.
- `tags` — список тегов.

## Steam Monthly Players

Файл: `processed/players/steam_monthly_players.parquet`. Единица наблюдения — игра за месяц; предполагаемый составной ключ — `steam_appid` и `month`.

Поля: `month`, `avg_players`, `gain`, `gain_percent`, `peak_players`, `name`, `steam_appid`, `month_raw`, `gain_raw`.

- `month` — преобразованная дата месяца; `month_raw` — исходная строка.
- `avg_players` и `peak_players` — среднее и пиковое число игроков.
- `gain` — числовое изменение; `gain_raw` — исходное значение.
- `gain_percent` — процентное изменение.
- `name` и `steam_appid` — название и Steam AppID.

Некоторые значения `gain` не преобразуются в число и остаются пропусками. Таблица связывается с каталогом Steam по AppID.

## Twitch

### Игры по месяцам

Файл: `processed/twitch/twitch_games.parquet`. Единица наблюдения — игра за месяц. Поля идентификации — `Game`, `Year`, `Month`.

Поля: `Rank`, `Game`, `Month`, `Year`, `Hours_watched`, `Hours_streamed`, `Peak_viewers`, `Peak_channels`, `Streamers`, `Avg_viewers`, `Avg_channels`, `Avg_viewer_ratio`.

`Rank` — позиция в исходном рейтинге; `Game` — название-кандидат для сопоставления; `Year` и `Month` задают время. Остальные поля характеризуют аудиторию и объём трансляций.

### Общая статистика

Файл: `processed/twitch/twitch_global.parquet`. Единица наблюдения — платформа за месяц; ключ — `year` и `Month`.

Поля: `year`, `Month`, `Hours_watched`, `Avg_viewers`, `Peak_viewers`, `Streams`, `Avg_channels`, `Games_streamed`, `Viewer_ratio`.

### Стримеры

Файл: `processed/twitch/twitch_streamers.parquet`. Единица наблюдения — стример; ключ — `NAME`.

Поля: `RANK`, `NAME`, `LANGUAGE`, `TYPE`, `MOST_STREAMED_GAME`, `2ND_MOST_STREAMED_GAME`, `AVERAGE_STREAM_DURATION`, `FOLLOWERS_GAINED_PER_STREAM`, `AVG_VIEWERS_PER_STREAM`, `AVG_GAMES_PER_STREAM`, `TOTAL_TIME_STREAMED`, `TOTAL_FOLLOWERS`, `TOTAL_VIEWS`, `TOTAL_GAMES_STREAMED`, `ACTIVE_DAYS_PER_WEEK`, `MOST_ACTIVE_DAY`, `DAY_WITH_MOST_FOLLOWERS_GAINED`.

Поля описывают место в рейтинге, имя, язык и тип стримера, наиболее транслируемые игры, аудиторию и активность. Названия игр не являются внешними ключами к Steam.

## YouTube

Файл: `processed/youtube/youtube_search_rows.parquet`. Единица наблюдения — результат поискового запроса.

Поля: `game_name`, `video_id`, `title`, `description`, `channel_title`, `published_at`, `views`, `likes`, `comments`.

`video_id` идентифицирует ролик; `game_name` хранит поисковый запрос и не подтверждает принадлежность ролика игре. `title` и `description` — текстовые признаки; `channel_title` — название канала; `published_at` — дата публикации; `views`, `likes` и `comments` — счётчики реакции.

Один `video_id` может присутствовать по нескольким запросам. Для TF-IDF используется одна запись на видео. Недоступная статистика не означает нулевого значения.

## Habr

Файл: `processed/text/habr_articles.parquet`. Единица наблюдения — статья; ключ — `url`.

Поля: `url`, `title`, `status_code`, `text`, `clean_text`.

`url` идентифицирует статью; `title` и `text` содержат заголовок и исходный текст; `status_code` фиксирует HTTP-ответ; `clean_text` содержит очищенные заголовок и текст для NLP. Источник ограничен двумя статьями.

## Wikidata

Файл: `processed/games/wikidata_games.parquet`. Единица наблюдения — строка результата SPARQL, а не уникальная игра.

Поля: `game`, `gameLabel`, `releaseDate`, `genreLabel`, `platformLabel`, `developerLabel`, `publisherLabel`.

`game` — URI сущности Wikidata; остальные поля — название, дата выхода, жанр, платформа, разработчик и издатель. Одна сущность может иметь несколько строк из-за множественных значений атрибутов.

## Сопоставление названий

### Кандидаты

Файл: `processed/games/game_match_candidates.parquet`.

Поля: `normalized_name`, `source`, `source_count`, `steam_count`, `match_status`.

Таблица хранит общее нормализованное название, источник-кандидат (RAWG, Wikidata или Twitch), число сущностей с этим названием и статус. `ambiguous_name` означает неоднозначное совпадение; `unique_name_match_unverified` — уникальное, но не проверенное вручную совпадение.

### Уникальные кандидаты

Файл: `processed/games/game_source_mapping.parquet`.

Поля: `steam_app_id`, `steam_name`, `normalized_name`, `source_game_id`, `source_game_name`, `source`, `match_method`, `match_status`.

Связь основана на точном совпадении нормализованных названий. Статус `unique_name_match_unverified` не доказывает идентичность игр. Для Twitch `source_game_id` отсутствует.

## NLP-файлы

Для Habr и YouTube 2.3 создаёт в `processed/text/` по три файла:

- `<source>_tfidf.npz` — разреженная TF-IDF-матрица;
- `<source>_tfidf_vocabulary.csv` — индексы признаков и термины;
- `<source>_tfidf_documents.csv` — ID документов в порядке строк матрицы.

Используются униграммы и биграммы, словарь ограничен 3000 признаками. Для YouTube обрабатываются заголовки и описания, но не видео или аудио.

## Отчёты качества

2.3 создаёт в `docs/data_quality/`:

- `column_quality_2_3.csv` — типы полей и пропуски;
- `table_summary_2_3.csv` — размеры таблиц и повторы по заданным ключам;
- `numeric_ranges_2_3.csv` — диапазоны числовых полей;
- `raw_inventory_2_3.csv` — инвентаризация исходных файлов;
- `warehouse_summary_2_3.csv` — таблицы DuckDB и число строк;
- `processing_times_2_3.csv` — время этапов обработки.

Отчёты описывают конкретный запуск и обновляются при повторном выполнении ноутбука.

## Аналитические результаты 2.4

### Объединённый профиль игры

Файл: `processed/analysis/cross_source_game_profile_2_4.parquet`; таблица DuckDB: `cross_source_game_profile`. Единица наблюдения — Steam-игра; ключ — `steam_app_id`.

Поля Steam включают `name`, `release_year`, `primary_genre`, `price`, `recommendations`, `positive_share` и `avg_players_12m`. `avg_players_12m` оставлен пропущенным для игр с недостаточно полными данными за период.

Поля RAWG: `rawg_id`, `rawg_rating`, `rawg_ratings_count`, `rawg_metacritic`.

Поля Wikidata: `wikidata_uri`, `wikidata_name`, минимальный и максимальный год выпуска из доступных записей, а также объединённые списки `wikidata_genres`, `wikidata_platforms`, `wikidata_developers`, `wikidata_publishers` и `wikidata_source_rows`.

Поля Twitch: `twitch_months`, `twitch_year_start`, `twitch_year_end`, `twitch_hours_watched`, `twitch_avg_viewers`, `twitch_peak_viewers`. Показатели агрегированы за временной диапазон источника, а не за период Steam Monthly Players.

Поля YouTube: `youtube_video_count`, `youtube_median_views`, `youtube_median_likes`, `youtube_median_comments`. `game_name` исходной таблицы является поисковым запросом. Метрики видео не подтверждают, что ролик посвящён игре.

`external_sources_present` — число источников из RAWG, Wikidata, Twitch и YouTube, представленных в строке профиля. Совпадения по названиям остаются непроверенными.

### Отчёты Habr

- `habr_text_metrics_2_4.csv` — число слов, уникальных слов и числовых упоминаний по статье.
- `habr_tfidf_terms_2_4.csv` — наиболее значимые TF-IDF-термины каждой статьи.
- `habr_steam_comparison_2_4.csv` — сравнение опубликованной оценки доли игр без рекомендаций с результатом для релизов Steam 2021–2025 годов.

Корпус содержит две статьи. Эти результаты служат иллюстрацией NLP и контекстным сравнением, а не репрезентативной оценкой индустрии.

### Отчёты межисточниковой интеграции

- `cross_source_coverage_2_4.csv` — число и доля Steam-игр, для которых найдены сведения каждого источника.
- `wikidata_entity_profile_2_4.csv` — записи Wikidata, сведённые к одной строке на URI игры.