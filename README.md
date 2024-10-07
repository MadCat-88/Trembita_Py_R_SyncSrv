# Проект на FastAPI з підтримкою системи Трембіта

## Опис
Цей проєкт входить в набір прикладів взаємодії з API шлюзу безпечного обміну системи Трембіта (ШБО), що демонструють технічні особливості розробки вебсервісів та вебклієнтів, сумісних з системою Трембіта. Даний проєкт представляє опис створення сумісного REST-сервісу на мові програмування Python з використанням фреймворку FastAPI. FastAPI - це сучасний, швидкий (високопродуктивний) web-фреймворк для побудови API з Python 3.10+ на основі стандартних асинхронних викликів. Проєкт підтримує систему Трембіта (логування заголовків).\
Приклад описує отримання з умовної бази даних (реєстру) відомостей про інформаційні об'єкти (у прикладі об'єктом є користувач) та управління їх статусом, у тому числі обробку запитів на пошук, отримання, створення, редагування та видалення об'єктів через REST API, що доступне через систему Трембіта. [Як запустити сервіс у оточені Docker](/README.Docker.md)


## Вимоги
- Python 3.10+
- Git (для клонування репозиторію)
- MariaDB 10.5+
- Ubuntu Server 24.04

## Встановлення

### Встановлення за допомогою скрипта deploy.sh

Ми додали скрипт `deploy.sh` для автоматизації процесу встановлення. Скрипт виконує наступні кроки:
1. Встановлює системні залежності.
2. Клонує репозиторій.
3. Створює та активує віртуальне середовище.
4. Встановлює залежності Python.
5. Налаштовує конфігурацію бази даних.
6. Створює структуру бази даних за допомогою Alembic.
7. Створює `systemd` сервіс для запуску застосунку.

#### Використання скрипта deploy.sh

1. Відредагуйте скрипт `deploy.sh`, змінивши у секції `[database]` значення змінних `name`, `username`, `password`, база даних з цими параметрами буде встановлена скриптом. Завантажте тільки скрипт `deploy.sh`. Скрипт автоматично встановить СУБД MariaDB, створить БД (користувача БД, та структуру БД)
2. Зробіть файл виконуваним:

   ```bash
   chmod +x deploy.sh
   ```

3. Запустіть скрипт:

   ```bash
   ./deploy.sh
   ```

### Ручне встановлення

#### Клонування репозиторію

```bash
git clone https://github.com/kshypachov/FastAPI_trembita_service.git
cd FastAPI_trembita_service
```

#### Створення та активація віртуального середовища

```bash
python3 -m venv venv
source venv/bin/activate   # Для Windows: venv\Scripts\activate
```

#### Встановлення залежностей

```bash
sudo apt-get install -y libmariadb-dev gcc python3-dev 
pip install -r requirements.txt
```

#### Створення БД

Цей сервіс використовує MySQL (MariaDB). Створіть базу даних та користувача на сервері СКБД.

#### Конфігурування сервісу

Відредагуйте наступні файли:

- У файлі `alembic.ini` відредагуйте рядок:
  ```ini
  sqlalchemy.url = mariadb+mariadbconnector://user:pass@localhost/dbname
  ```
- У файлі `config.ini` відредагуйте секцію `[database]`:
  ```ini
  host = your_db_host
  port = your_db_port
  name = your_db_name
  username = your_db_user
  password = your_db_password
  ```

- Опис полів `config.ini` :

   ```ini
   [database]
   # Тип бази даних, яку ви використовуєте. Можливі значення: mysql або postgres
   db_type = mysql
   
   # IP-адреса або доменне ім'я сервера бази даних
   host = your_db_host
   
   # Порт, через який здійснюється підключення до бази даних. За замовчуванням для MySQL це 3306
   port = your_db_port
   
   # Ім'я бази даних, до якої потрібно підключитися
   name = your_db_name
   
   # Ім'я користувача для підключення до бази даних
   username = your_db_user
   
   # Пароль користувача для підключення до бази даних
   password = your_db_password
   
   [logging]
   # Шлях до файлу, куди буде записуватися лог
   filename = path/to/client.log
   
   # filemode визначає режим, в якому буде відкритий файл логування.
   # 'a' - дописувати до існуючого файлу
   # 'w' - перезаписувати файл кожен раз при старті програми
   filemode = a
   
   # format визначає формат повідомлень логування.
   # %(asctime)s - час створення запису
   # %(name)s - ім'я логгера
   # %(levelname)s - рівень логування
   # %(message)s - текст повідомлення
   # %(pathname)s - шлях до файлу, звідки було зроблено виклик
   # %(lineno)d - номер рядка у файлі, звідки було зроблено виклик
   format = %(asctime)s,%(msecs)d %(name)s %(levelname)s %(message)s
   
   # dateformat визначає формат дати в повідомленнях логування.
   # Можливі формати можуть бути такими, як:
   # %Y-%m-%d %H:%M:%S - 2023-06-25 14:45:00
   # %d-%m-%Y %H:%M:%S - 25-06-2023 14:45:00
   dateformat = %H:%M:%S
   
   # level визначає рівень логування. Найбільш детальний це DEBUG, за замовчуванням INFO
   # DEBUG - докладна інформація, корисна для відлагодження роботи, логується вміст запитів та відповідей
   # INFO - загальна інформація про стан виконання програми
   # WARNING - попередження про можливі проблеми
   # ERROR - помилки, які завадили нормальному виконанню
   # CRITICAL - критичні помилки, що призводять до завершення програми
   level = DEBUG
   ```

#### Створення таблиць

Створенням та обслуговуванням структури БД займається Alembic. Для створення структури БД виконайте наступні команди:

```bash
alembic revision --autogenerate -m "Init migration"
alembic upgrade head
```

## Запуск застосунку

### Запуск сервера

```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4 --log-level info
```
Тут `main` - це ім'я файлу, що містить об'єкт FastAPI `app`. Відкрийте браузер і перейдіть за адресою http://[адреса серверу]:8000.

## Структура проєкту

Структура проєкту виглядає наступним чином:

```
FastAPI_trembita_service/
├── main.py                # Точка входу застосунку
├── config.ini             # Конфігурація проєкту
├── alembic.ini            # Конфігурація міграцій БД
├── utils/
│   ├── validations.py     # Валідація параметрів
│   ├── update_person.py   # Оновлення запису у БД
│   ├── get_person.py      # Пошук запису у БД за критерієм
│   ├── get_all_persons.py # Отримання всіх записів БД
│   ├── delete_person.py   # Видалення запису з БД
│   ├── create_person.py   # Створення запису у БД
│   └── config_utils.py    # Зчитування конфігураційного файлу
├── models/
│   └── person.py          # Моделі даних
├── migrations/
│   └── env.py             # Підключення моделей для Alembic
├── requirements.txt       # Залежності проєкту
├── README.md              # Документація
├── deploy.sh              # Скрипт для автоматизації встановлення
├── remove.sh              # Скрипт для видалення сервісу та очищення системи
```

## Документація

Після запуску сервера ви можете отримати доступ до автоматичної документації API за наступними адресами:

- Swagger UI: http://[адреса серверу]:8000/docs
- ReDoc: http://[адреса серверу]:8000/redoc

## Розгортання

Для розгортання на сервері використовуйте один з наступних методів:

- **Docker**: Створіть Dockerfile для контейнеризації застосунку.
- **Gunicorn**: Використовуйте Gunicorn у поєднанні з Uvicorn для запуску застосунку у виробничому середовищі.

Після розгортання та запуску сервісу його потрібно опублікувати на ШБО Трембіти як REST API, із заданням відповідного коду сервісу та базового URL endpoint http://[адреса_серверу]:8000. Додатково, для того, щоб відповідний REST-клієнт мав змогу викликати методи цього сервісу через систему Трембіта, потрібно задати права доступу до його виклику згідно відповідних інструкцій з налаштування шлюзу безпечного обміну Трембіти (https://trembita.gov.ua/techinfo).

### Приклад запуску з Gunicorn:

```bash
gunicorn -k uvicorn.workers.UvicornWorker main:app
```

## Видалення

Ми додали скрипт `remove.sh` для автоматизації процесу видалення сервісу та очищення системи.

### Використання скрипта remove.sh

1. Зробіть файл виконуваним:

   ```bash
   chmod +x remove.sh
   ```

2. Запустіть скрипт:

   ```bash
   ./remove.sh
   ```

Скрипт автоматично зупинить та видалить системний сервіс, видалить віртуальне середовище, клонований репозиторій та системні залежності.

## Внесок

Якщо ви хочете зробити свій внесок у проєкт, будь ласка, створіть форк репозиторію і відправте Pull Request.

## Ліцензія

Цей проект ліцензується відповідно до умов MIT License.
