# Medicines

**Medicines** — система управления списком лекарственных препаратов, состоящая из двух отдельных проектов:

- **Backend**: [spring-medicines](https://github.com/Greem4/spring-medicines) — серверное приложение на **Spring Boot**, предоставляющее REST API, а также реализующее аутентификацию/авторизацию и отправку email-уведомлений.
- **Frontend**: [spring-frontend](https://github.com/Greem4/spring-frontend) — клиентское приложение на **React**, взаимодействующее с бэкендом.

## Технологический стек

### Backend (spring-medicines)

- **Язык**: Java 21+
- **Фреймворк**: Spring Boot 3.4.0 (Security, Data JPA, Mail)
- **База данных**: PostgreSQL
- **Миграции**: Liquibase
- **Аутентификация**:
   - Локальная (логин/пароль, `BCryptPasswordEncoder`)
   - OAuth2 (через Яндекс) с выдачей собственного JWT
- **Авторизация**: ролевой доступ (**USER**, **ADMIN**)
- **Токены**: JWT
- **Отправка email**: `JavaMailSender` + SMTP
- **Сборка**: Gradle, Docker
- **Тестирование**: JUnit, Testcontainers
- **Документация API**: Springdoc OpenAPI

### Frontend (spring-frontend)

- **Библиотека**: React
- **Цель**: Предоставление удобного интерфейса для работы с препаратами, аутентификации и взаимодействия с API бэкенда.

## Функциональность

### Backend

1. **Просмотр препаратов без авторизации**
   - `GET /api/medicines` — список лекарств
   - `GET /api/medicines/{id}` — детали препарата

2. **Аутентификация**
   - **Локальная**: `POST /api/auth/login` — выдаёт JWT
   - **OAuth2 через Яндекс**: при успехе возвращает локальный JWT

3. **Авторизация (роли)**
   - **USER** — просмотр списка и деталей препаратов
   - **ADMIN** — добавление, обновление, удаление препаратов, управление пользователями

4. **Email-уведомления**
   - Отправка писем об истечении срока годности препаратов
   - SMTP-настройки задаются в `application.yaml` или через `.env`
   - Используется `JavaMailSender - RabbirMQ`

5. **Liquibase**
   - Автоматическое создание таблиц и вставка тестовых данных при старте приложения

6. **Документация API**
   - Доступна по адресу: [http://localhost:8080/swagger-ui/index.html](http://localhost:8080/swagger-ui/index.html)

### Frontend

- Реализованы страницы для просмотра списка лекарств, деталей препаратов.
- Поддержка аутентификации и авторизации пользователей через взаимодействие с бэкендом.
- Удобный интерфейс для администрирования (доступен пользователям с ролью **ADMIN**).

## Ключевые особенности

- **JWT**: При входе (локальный логин/пароль или OAuth2 Яндекс) приложение генерирует JWT, который клиент использует для доступа к API.
- **Яндекс OAuth2**: Успешный вход через Яндекс возвращает JWT, делая приложение stateless.
- **Ролевая авторизация**: Используются аннотации типа `@PreAuthorize("hasRole('ADMIN')")` для контроля доступа к защищенным ресурсам.
- **Инфраструктура для продакшена**: Подготовлена база для работы на сервере с nginx (HTTP → HTTPS).
- **Отправка уведомлений через брокер RabbitMQ**

## Запуск и использование через Docker

Проекты будут полностью упакованы в Docker-контейнеры.

### Предварительные шаги

1. **Склонировать** репозитории:

   ```bash
   git clone https://github.com/Greem4/Medicine.git
   cd Medicine
   git submodule update --init --recursive
   git checkout stable
   git submodule foreach --recursive git checkout stable
- **Backend**: [spring-medicines](https://github.com/Greem4/spring-medicines)
- **Frontend**: [spring-frontend](https://github.com/Greem4/spring-frontend)

2. **Подставить данные**
   
   .env (на основе .env.template)
3. **Запуск докера**
   ```bash
    docker compose up -d