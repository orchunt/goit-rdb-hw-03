# goit-rdb-hw-03: завантаження даних та DQL/EDA у PostgreSQL

## Датасет
- **Назва:** UCI Bank Marketing, файл `bank-additional-full.csv` (Moro, Cortez, Rita, 2014)
- **Офіційне джерело:** https://archive.ics.uci.edu/dataset/222/bank+marketing
- **Архів, який завантажує notebook:** https://archive.ics.uci.edu/static/public/222/bank+marketing.zip (вкладений `bank-additional.zip` → `bank-additional/bank-additional-full.csv`)
- **Домен:** телемаркетингові кампанії португальського банку, травень 2008 – листопад 2010; target `y` — оформлення строкового депозиту

## Вибірка
Використано **увесь файл** без семплювання: **41 188 рядків × 21 колонка**. Це в межах рекомендованих 10–100 тис. CSV (5,8 MB) лежить у `data/`.
Notebook завантажує його сам, якщо файлу немає. Для контролю ідентичності друкується MD5 (`f6cb2c1256ffe2836b36df321f46e92c`).

## Структура
```
hw3_bank_marketing.ipynb   # notebook з output клітинок
data/bank-additional-full.csv
README.md
```

## Pipeline
1. pgserver (PostgreSQL 16) у Colab, підключення через SQLAlchemy + psycopg2.
2. Офіційний ZIP → `data/bank-additional-full.csv`.
3. `COPY FROM STDIN` (`DELIMITER ';'`) → `hw3_bank_staging` (усі колонки `TEXT`).
4. `INSERT INTO ... SELECT` з очищенням → `hw3_bank_contacts` (типи, `NOT NULL`, `CHECK`, PK).
5. Data-quality audit, розподіли, outlier-check, 11 DQL/EDA-запитів, Reflection — усе SQL-запитами.

## Запуск
1. Відкрити `hw3_bank_marketing.ipynb` у Google Colab.
2. **Runtime → Restart session and run all.** Ручних правок шляхів чи SQL не потрібно.

Примітки до середовища (вже враховані в першій клітинці):
- PyPI має wheel-и `pgserver` лише для Python ≤ 3.12. На Python 3.13+ notebook ставить cp312-wheel з тегом `abi3` (C-розширення пакета зібране під stable ABI).
- SQLAlchemy 2.1 за замовчуванням використовує драйвер psycopg 3, тому в URI явно вказано `postgresql+psycopg2://`, потрібний для `copy_expert`.
