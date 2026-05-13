# Сценарии экрана регистрии (разбор макета сценариев интерфейса пользователя)

## Описание:
Экран представляет собой форму решистрации пользователя в BookStore.

> 1. Все элементы одинаковые - во всех вариантах интерфейса: \
> **Поля формы**
> - *First Name* - имя пользователя
> - *Last Name* - фамилия пользователя
> - *UserName* - логин пользователя
> - *Password* - пароль пользователя
> - *reCAPTCHA* - чек бокс `"I'm not a robot"`
> - Кнопка *Register*
> - Кнопка *Back to Login*

1. Экран 1 - Начальное состояние регистрации

Поведение UI
- Все поля пустые
- Поле Password скрывает вводимые символы
- Кнопка Register доступна для нажатия
- reCAPTCHA не подтверждена
- Ошибки отсутствуют


2. Экран 2 и 3 - Сценарии успешной регистрации 

Экран 2. - Пользователь заполняет поля:
  1. First Name = Ivan
  2. Last Name = Ivanov
  3. UserName = Ivan
  4. Password = ************
  5. подтверждает reCAPTCHA
    6. нажимает кнопку Register

Экран 3 - После нажатия Register - сценарий успешной регистрации

Поведение UI
- Затемненение формы
- Сообщение `"Loading..."`
- popap dialog с текстом `"User Register Successfully"` 
  > вопрос при подтверждении диалогового окна какие действия:
  > - redirect на login page?
  > - автоматический login?

3. Экран 4 - Ошибка пароль не соответствует требованиям

Пароль не соотвествует требованиям сложности.

Поведение UI
- под формой отображается красное сообщение: `Password must have at least one non alphanumeric character, one digit ('0'-'9'), one uppercase ('A'-'Z'), one lowercase ('a'-'z'), one special character and Password must be eight characters or longer.`

> Требования сложности из поведения UI:
> - нет спецсимвола
> - нет uppercase
> - слишком короткий

Возможные ожидаемые ответ с бэка: 
- HTTP Status: 400 - Bad Request, 
- Response Body: json: {"errorCode": "INVALID_PASSWORD", "message": "Password does not meet complexity requirements"} - например

4. Экран 5 - Ошибка пользователь уже существует

Поведение UI
- под формой отображается красное сообщение: `User exists!`

Возможные ожидаемый ответ с бэка: 
- HTTP Status: 409 - Conflict, 
- Response Body: json: {"errorCode": "USER_ALREADY_EXISTS", "message": "User exists!"} - например

4. Экран 5 - Ошибка captcha не подтверждена

Поведение UI
- под формой отображается красное сообщение: `Please verify reCaptcha to register!`

  > Вопрос: фронтенду необходимо отпарвлять запрос в бэк?

Если токен валидируется, то возможные ожидаемые ответ с бэка: 
- HTTP Status: 400 - Bad Request, 
- Response Body: json: {"errorCode": "CAPTCHA_REQUIRED", "message": "Please verify reCaptcha to register!"} - например