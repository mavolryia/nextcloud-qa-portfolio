# Методы и адрес загрузки

## TC-129. Попытка загрузки методом POST на адрес файла

**Описание:** POST для загрузки в WebDAV не поддерживается (OPTIONS: Allow без POST). По HTTP (RFC 9110) ожидается 405. Факт на 35.0.2: 404 «File with name /post-test.txt could not be located» (наблюдение).

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла post-test.txt нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить POST | /remote.php/dav/files/user1/post-test.txt<br>Body (raw): test | Код 405 Method Not Allowed |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/post-test.txt | Код 404 Not Found |

## TC-130. Попытка загрузки методом POST формой на папку

**Описание:** По HTTP ожидается 405. Факт на 35.0.2: 501 «There was no plugin in the system that was willing to handle this POST method» (наблюдение).

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла post-form.txt нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить POST, Body (form-data): поле file с файлом post-form.txt | /remote.php/dav/files/user1/ | Код 405 Method Not Allowed |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/post-form.txt | Код 404 Not Found |

## TC-131. Попытка загрузки файла в несуществующую папку

**Описание:** По RFC 4918 (раздел 9.7.1) ожидается 409 Conflict. Факт на 35.0.2: 404, s:exception = Sabre\DAV\Exception\NotFound (наблюдение).

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: папки no-such-folder нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/no-such-folder/test.txt<br>Body (raw): test | Код 409 Conflict |
| 2 | Проверить, что папка не создана: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/no-such-folder | Код 404 Not Found |
