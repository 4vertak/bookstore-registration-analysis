# Спецификация REST API регистрации пользователя

Пример реализации проектирования спецификации REST API запроса при нажатии пользователем на кнопку “Register”.

Спецификация в формате OpenAPI: [openapi.yaml](./openapi.yaml)


## **Эндпоинт:** `POST /api/v1/auth/register`

**Метод:** POST

**Заголовки:** 
> - `Content-Type: application/json`

**Тело запроса (Request Body)**

| Параметр      | Тип    | Обязательный | Ограничения                        |
|---------------|--------|--------------|------------------------------------|
| firstName     | string | да           | -                                  |
| lastName      | string | да           | -                                  |
| username      | string | да           | -                                  |
| password      | string | да           | Мин. 8 символов, 1 цифра, 1 спецсимвол, 1 заглавная, 1 строчная |
| captchaToken  | string | да           | Токен от reCAPTCHA                 |

Пример запроса:
```json
{
  "firstName": "Ivan",
  "lastName": "Ivanov",
  "username": "Ivan",
  "password": "Password123!",
  "captchaToken": "recaptcha-response-token"
}
```

---

### Коды ответов

**Успешный ответ:** 201 Created

**Коды ошибок:**
- 400 Bad Request – ошибка валидации (не заполнены поля, неверный пароль, не пройдена капча)
- 409 Conflict – пользователь с таким username уже существует
- 415 Unsupported Media Type – неверный Content-Type
- 500 Internal Server Error – внутренняя ошибка сервера

### Структура успешного ответа (201)

| Поле    | Тип     | Описание                  |
|---------|---------|---------------------------|
| success | boolean | Всегда true               |
| message | string  | Текст подтверждения       |

Пример:

\```json
{
  "success": true,
  "message": "User Register Successfully"
}
\```

### Структура ответа с ошибкой (400, 409, 500)

| Поле      | Тип     | Обязательность | Описание                                      |
|-----------|---------|----------------|-----------------------------------------------|
| success   | boolean | да             | Всегда false                                  |
| errorCode | string  | да             | Код ошибки (например, USER_ALREADY_EXISTS)    |
| message   | string  | да             | Человекочитаемое сообщение                    |
| errors    | object  | нет            | Опционально: детализация ошибок по полям      |

#### Примеры ошибок

**Ошибка валидации (400) – не заполнены поля**

```json
{
  "success": false,
  "errorCode": "VALIDATION_ERROR",
  "message": "Please correct the highlighted fields",
  "errors": {
    "firstName": ["required"],
    "lastName": ["required"],
    "username": ["required"]
  }
}
```

**Ошибка валидации (400) – неверный пароль**

```json
{
  "success": false,
  "errorCode": "INVALID_PASSWORD",
  "message": "Passwords must have at least one non alphanumeric character, one digit ('0'-'9'), one uppercase ('A'-'Z'), one lowercase ('a'-'z'), one special character and Password must be eight characters or longer."
}
```

**Ошибка reCAPTCHA (400)**

```json
{
  "success": false,
  "errorCode": "CAPTCHA_REQUIRED",
  "message": "Please verify reCaptcha to register!"
}
```

**Пользователь уже существует (409)**

```json
{
  "success": false,
  "errorCode": "USER_ALREADY_EXISTS",
  "message": "User exists!"
}
```