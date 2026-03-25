# Библиотека Python {#python-library}

Пакет `lingtrain-aligner` для Python предоставляет основной движок выравнивания, используемый веб-приложением Lingtrain. Вы можете использовать его напрямую в своих скриптах, ноутбуках или конвейерах обработки.

## Установка {#installation}

```bash
pip install lingtrain-aligner
```

Пакет требует Python 3.6+ и устанавливает SentenceTransformers как зависимость. Для ускорения на GPU предварительно установите PyTorch с поддержкой CUDA.

## Быстрый старт {#quick-start}

```python
from lingtrain_aligner import aligner, resolver, reader, saver

# 1. Подготовка текстов (одно предложение на строку)
with open("source_en.txt") as f:
    lines_en = f.read().splitlines()
with open("target_ru.txt") as f:
    lines_ru = f.read().splitlines()

# 2. Создание базы данных выравнивания
db_path = "my_alignment.db"
aligner.fill_db(
    db_path,
    lang_from="en",
    lang_to="ru",
    splitted_from=lines_en,
    splitted_to=lines_ru,
    name="My Alignment"
)

# 3. Запуск выравнивания
aligner.align_db(
    db_path,
    model_name="sentence_transformer_multilingual",
    batch_size=200,
    window=40
)

# 4. Разрешение конфликтов
resolver.resolve_all_conflicts(db_path)

# 5. Экспорт результатов
saver.save_tmx(db_path, "output.tmx", lang_from="en", lang_to="ru")
```

## Основные функции {#core-functions}

### aligner.fill_db() {#fill-db}

Создаёт и заполняет SQLite-базу данных выравнивания исходными и целевыми предложениями.

```python
aligner.fill_db(
    db_path,          # Путь к базе данных SQLite (создаётся, если не существует)
    lang_from,        # Код исходного языка ("en", "ru", "de" и т.д.)
    lang_to,          # Код целевого языка
    splitted_from,    # Список исходных предложений
    splitted_to,      # Список целевых предложений
    proxy_from=[],    # Опционально: подстрочник для исходного языка
    proxy_to=[],      # Опционально: подстрочник для целевого языка
    name=""           # Название проекта выравнивания
)
```

### aligner.align_db() {#align-db}

Запускает основной процесс выравнивания для заполненной базы данных.

```python
aligner.align_db(
    db_path,                      # Путь к базе данных выравнивания
    model_name,                   # Идентификатор модели эмбеддингов
    batch_size=200,               # Предложений на батч
    window=40,                    # Размер контекстного окна
    batch_ids=[],                 # Конкретные батчи для обработки (пусто = все)
    shift=0,                      # Ручная коррекция смещения
    embed_batch_size=10,          # Предложений на батч эмбеддингов
    normalize_embeddings=True,    # L2-нормализация эмбеддингов
    save_pic=False,               # Сохранять изображения визуализации
    img_path="",                  # Путь для изображений визуализации
    use_proxy_from=False,         # Использовать подстрочник для исходного языка
    use_proxy_to=False,           # Использовать подстрочник для целевого языка
    store_embeddings=False,       # Сохранять эмбеддинги в базе данных
    use_api=False,                # Использовать внешний API для эмбеддингов
    api=None,                     # Провайдер API ("hf-inference" или "openai")
    model_api=None,               # Идентификатор модели для API
    api_key=None                  # API-ключ
)
```

### resolver.resolve_all_conflicts() {#resolve-conflicts}

Автоматически разрешает конфликты выравнивания с помощью оценки семантического сходства.

```python
resolver.resolve_all_conflicts(
    db_path,              # Путь к базе данных выравнивания
    model_name=None,      # Модель для пересчёта эмбеддингов (опционально)
    show_info=False       # Выводить информацию о прогрессе
)
```

### resolver.prepare_index() {#prepare-index}

Извлекает индекс выравнивания для просмотра или пользовательской обработки.

```python
index, total_batches = resolver.prepare_index(
    db_path,          # Путь к базе данных выравнивания
    batch_id=-1       # Конкретный батч (-1 = все)
)
```

## Функции экспорта {#export}

### saver.save_tmx() {#save-tmx}

Экспортирует выровненные пары в формате TMX (Translation Memory eXchange).

```python
saver.save_tmx(
    db_path,          # Путь к базе данных выравнивания
    output_path,      # Путь к выходному TMX-файлу
    lang_from,        # Код исходного языка
    lang_to           # Код целевого языка
)
```

### saver.save_plain_text() {#save-plain}

Экспортирует выровненные предложения как обычный текст (одно предложение на строку).

```python
saver.save_plain_text(
    db_path,          # Путь к базе данных выравнивания
    output_path,      # Путь к выходному текстовому файлу
    side,             # "from" или "to"
    batch_ids=[]      # Конкретные батчи (пусто = все)
)
```

### saver.save_json() / saver.save_xml() {#save-structured}

Экспортирует структуру выровненной книги в формате JSON или XML.

```python
saver.save_json(db_path, output_path, lang_order=["en", "ru"], direction="to")
saver.save_xml(db_path, output_path, lang_order=["en", "ru"], direction="to")
```

### reader.get_aligned_pair_chains() {#get-pairs}

Извлекает цепочки выровненных пар предложений из базы данных.

```python
pairs = reader.get_aligned_pair_chains(
    db_path,          # Путь к базе данных выравнивания
    min_len=2         # Минимальная длина цепочки
)
# Возвращает: список пар [text_from, text_to]
```

## Модели эмбеддингов {#models}

Доступные идентификаторы моделей для `model_name`:

| Идентификатор | Модель | Языки |
|---------------|--------|-------|
| `sentence_transformer_multilingual` | distiluse-base-multilingual-cased-v2 | 50+ |
| `sentence_transformer_multilingual_xlm_100` | XLM-RoBERTa 100 языков | 100+ |
| `sentence_transformer_multilingual_labse` | LaBSE | 100+ |
| `rubert_tiny` | rubert-tiny2 | Русский |
| `sonar` | Meta SONAR | 200+ |

Вы также можете передать любое имя модели SentenceTransformers с Hugging Face напрямую.

## Использование подстрочных переводов {#proxy}

Подстрочные переводы — это машинные переводы, используемые как посредники для малоресурсных языковых пар. Подстрочник должен иметь то же количество строк, что и оригинальный разбитый текст.

```python
# Загрузка подстрочников
with open("source_ba_proxy_en.txt") as f:
    proxy_from = f.read().splitlines()

# Создание базы данных с подстрочником
aligner.fill_db(
    db_path,
    lang_from="ba",
    lang_to="ru",
    splitted_from=lines_ba,
    splitted_to=lines_ru,
    proxy_from=proxy_from
)

# Выравнивание с использованием подстрочника
aligner.align_db(
    db_path,
    model_name="sentence_transformer_multilingual_labse",
    batch_size=200,
    window=40,
    use_proxy_from=True
)
```

## Использование внешних API {#api}

### Hugging Face Inference API {#hf-api}

```python
aligner.align_db(
    db_path,
    model_name="",
    batch_size=200,
    window=40,
    use_api=True,
    api="hf-inference",
    model_api="sentence-transformers/LaBSE",
    api_key="hf_..."
)
```

### OpenAI Embeddings API {#openai-api}

```python
aligner.align_db(
    db_path,
    model_name="",
    batch_size=200,
    window=40,
    use_api=True,
    api="openai",
    model_api="text-embedding-3-small",
    api_key="sk-..."
)
```

## Поддерживаемые языки {#languages}

Коды языков для `lang_from` / `lang_to`:

| Код | Язык | Код | Язык |
|-----|------|-----|------|
| `en` | Английский | `pl` | Польский |
| `ru` | Русский | `pt` | Португальский |
| `de` | Немецкий | `hu` | Венгерский |
| `fr` | Французский | `cs` | Чешский |
| `es` | Испанский | `ja` | Японский |
| `it` | Итальянский | `ko` | Корейский |
| `zh` | Китайский | `sv` | Шведский |
| `tr` | Турецкий | `nl` | Нидерландский |
| `uk` | Украинский | `be` | Белорусский |
| `ba` | Башкирский | `cv` | Чувашский |
| `hy` | Армянский | `xx` | Общий (любой язык) |

Используйте `xx` (General) для языков, не входящих в список — модель эмбеддингов охватывает 100+ языков даже без языко-специфичного разбиения на предложения.

## Формат базы данных {#database}

База данных выравнивания — это файл SQLite со следующими ключевыми таблицами:

- **splitted_from / splitted_to** — исходные и целевые предложения с метаданными
- **processing_from / processing_to** — выровненные пары предложений по батчам
- **doc_index** — карта выравнивания (batch_id → пары предложений)
- **meta** — маркеры структуры документа (заголовки, абзацы)
- **batches** — метаданные пакетной обработки (время, сдвиг, окно)
- **languages** — коды исходного и целевого языков

Формат `.lt`, используемый веб-приложением Lingtrain — это та же база данных SQLite с другим расширением.
