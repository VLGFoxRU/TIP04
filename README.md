# ЭФМО-01-25 Буров М.А. ПР4

# Описание проекта
Маршрутизация с chi (альтернатива — gorilla/mux). Создание небольшого CRUD-сервиса «Список задач».

# Требования к проекту
* Go 1.25+
* Git

# Версия Go
<img width="340" height="55" alt="image" src="https://github.com/user-attachments/assets/e7fec853-899d-442e-9d95-94d494a5cb46" />

# Цели:
- Освоить базовую маршрутизацию HTTP-запросов в Go на примере роутера chi.
- Научиться строить REST-маршруты и обрабатывать методы GET/POST/PUT/DELETE.
- Реализовать небольшой CRUD-сервис «ToDo» (без БД, хранение в памяти).
- Добавить простое middleware (логирование, CORS).
- Научиться тестировать API запросами через curl/Postman/HTTPie.

# Структура проекта
Дерево структуры проекта: 
```
pz4-todo/
├── internal/
│   └── task/
│       ├── handler.go
│       ├── model.go
│       └── repo.go
├── pkg/
│   └── middleware/
│       ├── cors.go
│       └── logger.go
├── go.mod
├── go.sum
└── main.go
```
