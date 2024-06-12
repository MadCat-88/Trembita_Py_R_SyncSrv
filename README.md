# Проект на FastAPI з підтримкою системи Трембіта

## Опис

Цей проект представляє собою веб-сервіс, розроблений з використанням FastAPI. FastAPI - це сучасний, швидкий (високопродуктивний) web-фреймворк для побудови API з Python 3.7+ на основі стандартних асинхронних викликів. Проект підтримує систему Трембіта (логування заголовків). 

## Вимоги

- Python 3.10+
- Git (для клонування репозиторію)

## Встановлення

1. **Клонування репозиторію**

   ```bash
   git clone https://github.com/kshypachov/FastAPI_trembita_service.git
   cd FastAPI_trembita_service
   ```

2. **Створення та активація віртуального середовища**

   ```bash
   python3 -m venv venv
   source venv/bin/activate   # Для Windows: venv\Scripts\activate
   ```

3. **Встановлення залежностей**

   ```bash
   sudo apt-get install -y libmariadb-dev gcc
   pip3 install -r requirements.txt
   ```

4**Створення БД**

Цей сервіс використовує MySQL (MariaDB). Створіть базу даних та кристувача на сервері СКБД.

5**Конфігурування сервісу**

У файлі alembic.ini відредагуйте строчку sqlalchemy.url = mariadb+mariadbconnector://user:pass@localhost/dbname
У файлі config.ini відредагуте секцію [database] 

6**Сворення таблиць**

Своренням та обслуговуванням структури БД, займається alembic. Для створення структури БД, виконайте наступні команди 

alembic init alembic
alembic init migrations
alembic revision --autogenerate -m "Init migration"
alembic upgrade head

## Запуск застосунку

1. **Запуск сервера**

   ```bash
   uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4 --log-level info
   ```

   Тут `main` - це ім'я файлу, що містить об'єкт FastAPI `app`. Відкрийте браузер і перейдіть за адресою `http://[адреса серверу]:8000`.

## Структура проекту

Структура проекта виглядає наступним чином:

```
FastAPI_trembita_service/

├── main.py                # Точка входу застосунку
├── config.ini             # Конфігурація проекту
├── alembic.ini            # Конфігурація міграцій БД
├── utils/
│   ├── validations.py     # Валідація параметрів
│   ├── update_person.py   # Оновлення запису у БД
│   ├── get_person.py      # Пошук запису у БД за критеріем
│   ├── get_all_persons.py # Отримання всіх записів БД
│   ├── delete_person.py   # Видалення запису з БД
│   ├── create_person.py   # Створення запису у БД
│   └── config_utils.py    # Зчитування конфігураційного файлу
├── models/
│   └── person.py          # Моделі даних
├── requirements.txt    # Залежності проекту
├── README.md           # Документація
```

## Документація

Після запуску сервера ви можете отримати доступ до автоматичної документації API за наступними адресами:
- Swagger UI: `http://[адреса серверу]:8000/docs`
- ReDoc: `http://[адреса серверу]:8000/redoc`

## Розгортання

Для розгортання на сервері використовуйте один з наступних методів:

- **Docker**: Створіть Dockerfile для контейнеризації застосунку.
- **Gunicorn**: Використовуйте Gunicorn у поєднанні з Uvicorn для запуску застосунку у виробничому середовищі.

Приклад запуску з Gunicorn:

```bash
gunicorn -k uvicorn.workers.UvicornWorker main:app
```

## Внесок

Якщо ви хочете зробити свій внесок у проект, будь ласка, створіть форк репозиторію і відправте пулреквест.

## Ліцензія

Цей проект ліцензується відповідно до умов MIT License.
