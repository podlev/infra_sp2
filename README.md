# REST API сервиса YaMDb

### Описание
Проект представляет собой API для проекта YaMDb  — базы отзывов о фильмах, книгах и музыке.
Проект YaMDb собирает отзывы (Review) пользователей на произведения (Titles). Произведения делятся на категории: «Книги», «Фильмы», «Музыка». 
В каждой категории есть произведения: книги, фильмы или музыка. 
Произведению может быть присвоен жанр (Genre) из списка предустановленных. Новые жанры может создавать только администратор.
Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы (Review) и ставят произведению оценку в диапазоне от одного до десяти (целое число), затем из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (целое число). На одно произведение пользователь может оставить только один отзыв.

### Технологии 
- Python
- Django
- Django REST Framework
- Simple-JWT
- PostgreSQL
- Nginx
- Gunicor
- Docker
- Docker-compose 3

### Особенности
- Применены вьюсеты
- Для аутентификации использованы JWT-токены
- У неаутентифицированных пользователей доступ к API только на чтение
- Аутентифицированным пользователям разрешено изменение и удаление своего контента; в остальных случаях доступ предоставляется только для чтения

### Установка
- Склонировать репозиторий

```commandline
git clone github.com/podlev/infra_sp2.git
```

- В директории infra переименуйте файл .env.example в файл .env и укажите переменные окружения:

```commandline
SECRET_KEY=Django secret key
DB_ENGINE=django.db.backends.postgresql 
DB_NAME=xxxxxxxxxxx
POSTGRES_USER=xxxxxxxxxxx
POSTGRES_PASSWORD=xxxxxxxxxxx
DB_HOST=xxxxxxxxxxx
DB_PORT=xxxxxxxxxxx
```

- Запустите docker-compose
```
docker-compose up
# или для пересборки контейнеров
# docker-compose up -d --build
```
- Выполните миграции, создайте суперюзера, соберите статику
```
# sudo docker-compose exec web python manage.py makemigrations users
docker-compose exec web python manage.py migrate
docker-compose exec web python manage.py createsuperuser
docker-compose exec web python manage.py collectstatic --no-input
```
Создать или загрузить дамп базы данных:
```
# sudo docker-compose exec web python manage.py dumpdata > fixtures.json
sudo docker-compose exec web python manage.py loaddata fixtures.json
```

### Примеры

- Для доступа к API необходимо получить токен: POST-запрос localhost:8000/api/v1/auth/signup/ передав поля username и email. API отправляет письмо с кодом подтверждения (confirmation_code) на указанный адрес email.
```
{ 
    "email": "string",
    "username": "string" 
}
```

- Пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт /api/v1/auth/token/,в ответе на запрос ему приходит token (JWT-токен).
```
{ 
    "username": "string",
    "confirmation_code": "string" 
}
```

- После регистрации и получения токена пользователь может отправить PATCH-запрос на эндпоинт /api/v1/users/me/ и заполнить поля в своём профайле.

- Затем, отправляя токен с каждым запросом, можно будет обращаться к методам, например: 
```
/api/v1/titles/ (GET, POST, PATCH, DELETE)    
/api/v1/genre/ (GET, POST, DELETE)    
/api/v1/categories/ (GET, POST, DELETE)    
```
- При отправке запроса необходимо передать токен в заголовке Authorization: Bearer <токен>
- Основной список ресурсов API /api/v1/
### Автор
Telegram: [Лев Подъельников](https://t.me/podlev)

