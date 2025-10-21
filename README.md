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

# Фрагменты кода
```
func (h *Handler) Routes() chi.Router {
	r := chi.NewRouter()
	r.Get("/", h.list)          // GET /tasks
	r.Post("/", h.create)       // POST /tasks
	r.Get("/{id}", h.get)       // GET /tasks/{id}
	r.Put("/{id}", h.update)    // PUT /tasks/{id}
	r.Delete("/{id}", h.delete) // DELETE /tasks/{id}
	return r
}
```

```
package middleware

import (
	"log"
	"net/http"
	"time"
)

func Logger(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		start := time.Now()
		next.ServeHTTP(w, r)
		log.Printf("%s %s %s", r.Method, r.URL.Path, time.Since(start))
	})
}
```

```
func (h *Handler) create(w http.ResponseWriter, r *http.Request) {
	var req createReq
	if err := json.NewDecoder(r.Body).Decode(&req); err != nil || req.Title == "" {
		httpError(w, http.StatusBadRequest, "invalid json: require non-empty title")
		return
	}
	t := h.repo.Create(req.Title)
	writeJSON(w, http.StatusCreated, t)
}
```

# Примеры запросов

Запрос GET /health

<img width="380" height="99" alt="image" src="https://github.com/user-attachments/assets/65ba2845-f867-47b0-83c4-536e3a6ed822" />

Запрос POST /tasks

<img width="593" height="218" alt="image" src="https://github.com/user-attachments/assets/d0201672-5c2f-4f2f-8183-9e4efc641509" />

Запрос GET /tasks

<img width="608" height="276" alt="image" src="https://github.com/user-attachments/assets/e4a48f69-5c94-4a3a-94e9-6d00dc7fca3e" />

Запрос GET /tasks/{id}

<img width="601" height="287" alt="image" src="https://github.com/user-attachments/assets/290e1494-349c-4e39-b49a-f08cb9e1f6c6" />

Запрос PUT /tasks/{id}

<img width="586" height="258" alt="image" src="https://github.com/user-attachments/assets/01225494-96df-4205-b165-44e5c067f1a0" />

Запрос DELETE /tasks/{id}

<img width="424" height="113" alt="image" src="https://github.com/user-attachments/assets/4329275b-3c8c-4cc8-9922-15faef7119b8" />

# Обработка ошибок и кодов ответов

200 OK - успешный ответ с данными
201 Created - успешное создание задачи
204 No Content - успешное удаление
400 Bad Request - невалидные данные, запроса
404 Not Found - задача не найдена
422 Unprocessable Entity - нарушена валидация данных
500 Internal Server Error - ошибка сервера

# Результаты тестирования

| Маршрут                | Запрос                                                     | Ожидаемый ответ                    | Фактический ответ      |
|------------------------|------------------------------------------------------------|------------------------------------|------------------------|
| **POST /tasks**        | `{"title":"Выучить chi"}`                                  | 201 Created + задача               | Совпадает              |
| **GET /tasks**         | без параметров                                             | 200 OK + список задач              | Совпадает              |
| **GET /tasks**         | `?done=true`                                               | 200 OK + список выполненных задач  | Совпадает              |
| **GET /tasks**         | `?page=1&limit=1`                                          | 200 OK + первая задача             | Совпадает              |
| **GET /tasks/{id}**    | `/tasks/1`                                                 | 200 OK + задача                    | Совпадает              |
| **GET /tasks/{id}**    | `/tasks/999`                                               | 404 Not Found                      | Совпадает              |
| **PUT /tasks/{id}**    | `/tasks/1` + `{"title":"Выучить chi глубже","done":true}`  | 200 OK + обновлённая задача        | Совпадает              |
| **PUT /tasks/{id}**    | `/tasks/999` + `{"title":"Облом","done":true}`             | 404 Not Found                      | Совпадает              |
| **DELETE /tasks/{id}** | `/tasks/1`                                                 | 204 No Content                     | Совпадает              |
| **DELETE /tasks/{id}** | `/tasks/999`                                               | 404 Not Found                      | Совпадает              |

# Выводы

В результате выполнения работы были реализованы основные операции с CRUD (создание, чтение, обновление и удаление), а также проведено их тестирование. 
