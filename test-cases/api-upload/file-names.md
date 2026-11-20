# Имена файлов

## TC-115. Загрузить пустой файл (0 байт)

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла api-empty.txt в корне нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT с пустым Body | /remote.php/dav/files/user1/api-empty.txt<br>Body: пусто | Код 201 Created. В заголовках ответа есть ETag и OC-FileId |
| 2 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/api-empty.txt | Код 207 Multi-Status. getcontentlength = 0 |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/api-empty.txt, очистить корзину.

## TC-116. Загрузить файл повторно с тем же именем (замена)

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: в корне есть файл api-empty.txt размером 0 байт.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT на адрес существующего файла | /remote.php/dav/files/user1/api-empty.txt<br>Body (raw): second (6 байт) | Код 204 No Content. В заголовках ответа есть новый ETag |
| 2 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/api-empty.txt | Код 207 Multi-Status. getcontentlength = 6 (файл заменён, а не создан второй) |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/api-empty.txt, очистить корзину.

## TC-117. Попытка загрузки файла с тем же именем без перезаписи (If-None-Match: *)

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: в корне есть файл api-empty.txt размером 0 байт.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT с заголовком If-None-Match: * | /remote.php/dav/files/user1/api-empty.txt<br>Заголовок: If-None-Match: *<br>Body (raw): second | Код 412 Precondition Failed. s:exception = Sabre\DAV\Exception\PreconditionFailed |
| 2 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/api-empty.txt | Код 207 Multi-Status. getcontentlength = 0 (содержимое не изменилось) |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/api-empty.txt, очистить корзину.

## TC-118. Загрузить файл с именем из 250 байт (латиница)

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла с этим именем нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); предел длины имени 250 байт.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/&lt;246 × a>.txt<br>Имя: 246 букв a + .txt = 250 байт<br>Body (raw): test | Код 201 Created. В заголовках ответа есть ETag и OC-FileId |
| 2 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/&lt;имя из 250 байт> | Код 207 Multi-Status. getcontentlength = 4 |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/&lt;имя из 250 байт>, очистить корзину.

## TC-119. Попытка загрузки файла с именем из 251 байта (латиница)

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла с этим именем нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); предел длины имени 250 байт.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/&lt;247 × a>.txt<br>Имя: 247 букв a + .txt = 251 байт (скопировать из test-data/names/251-bytes-latin.txt)<br>Body (raw): test | Код 400 Bad Request. Тело ответа (XML): s:exception = OCA\DAV\Connector\Sabre\Exception\InvalidPath, s:message = «Filename is too long» |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/&lt;имя из 251 байта> | Код 404 Not Found |

## TC-120. Загрузить файл с именем из 125 букв кириллицы (250 байт)

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла с этим именем нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); предел длины имени 250 байт.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/&lt;125 × я><br>Имя: 125 букв я, без расширения (по 2 байта = 250 байт)<br>Body (raw): test | Код 201 Created. В заголовках ответа есть ETag и OC-FileId |
| 2 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/&lt;125 × я> | Код 207 Multi-Status. getcontentlength = 4 |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/&lt;125 × я>, очистить корзину.

## TC-121. Попытка загрузки файла с именем из 126 букв кириллицы (252 байта)

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла с этим именем нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); предел длины имени 250 байт.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/&lt;126 × я><br>Имя: 126 букв я (252 байта)<br>Body (raw): test | Код 400 Bad Request. Тело ответа (XML): s:exception = OCA\DAV\Connector\Sabre\Exception\InvalidPath, s:message = «Filename is too long» |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/&lt;126 × я> | Код 404 Not Found |

## TC-122. Загрузить файл с символами, запрещёнными в Windows

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла a?b:c*d.txt нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT (символы закодированы в адресе) | /remote.php/dav/files/user1/a%3Fb%3Ac%2Ad.txt<br>Имя: a?b:c*d.txt<br>Body (raw): test | Код 201 Created. В заголовках ответа есть ETag и OC-FileId |
| 2 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/a%3Fb%3Ac%2Ad.txt | Код 207 Multi-Status. getcontentlength = 4 |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/a%3Fb%3Ac%2Ad.txt, очистить корзину.

## TC-123. Попытка загрузки файла с обратным слэшем в имени

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла a\b.txt нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/a%5Cb.txt<br>Имя: a\b.txt<br>Body (raw): test | Код 400 Bad Request. Тело ответа (XML): s:exception = OCA\DAV\Connector\Sabre\Exception\InvalidPath, s:message = «"\" is not allowed inside a file or folder name.» |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/a%5Cb.txt | Код 404 Not Found |

## TC-124. Попытка загрузки файла .htaccess

**Описание:** Факт на 35.0.2: код 500, s:exception = OCP\Files\ForbiddenException (кандидат в дефект №8).

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла .htaccess нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); имя .htaccess запрещено.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/.htaccess<br>Body (raw): test | Код 400 Bad Request. s:exception = OCA\DAV\Connector\Sabre\Exception\InvalidPath, s:message сообщает, что имя запрещено |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/.htaccess | Код 404 Not Found |

## TC-125. Попытка загрузки файла .HTACCESS (верхний регистр)

**Описание:** Запрет имён не зависит от регистра. Факт на 35.0.2: код 500 (кандидат в дефект №8).

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла .HTACCESS нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); имя .htaccess запрещено.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/.HTACCESS<br>Body (raw): test | Код 400 Bad Request. s:exception = OCA\DAV\Connector\Sabre\Exception\InvalidPath, s:message сообщает, что имя запрещено |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/.HTACCESS | Код 404 Not Found |

## TC-126. Попытка загрузки файла с расширением .part

**Описание:** Факт на 35.0.2: PROPFIND на file.part отвечает 207 и показывает пустой элемент (getcontentlength 0), хотя на диске файла нет.

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла file.part нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); расширения .part и .filepart запрещены.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/file.part<br>Body (raw): test | Код 400 Bad Request. Тело ответа (XML): s:exception = OCA\DAV\Connector\Sabre\Exception\InvalidPath, s:message = «".part" is a forbidden file type.» |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/file.part | Код 404 Not Found |

## TC-127. Попытка загрузки файла с расширением .filepart

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла file.filepart нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); расширения .part и .filepart запрещены.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT | /remote.php/dav/files/user1/file.filepart<br>Body (raw): test | Код 400 Bad Request. Тело ответа (XML): s:exception = OCA\DAV\Connector\Sabre\Exception\InvalidPath, s:message = «".filepart" is a forbidden file type.» |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/file.filepart | Код 404 Not Found |

## TC-128. Попытка загрузки файла с переводом строки в конце имени

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла api-newline.txt нет.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT (перевод строки закодирован как %0A) | /remote.php/dav/files/user1/api-newline.txt%0A<br>Body (raw): test | Код 400 Bad Request. Тело ответа (XML): s:exception = OCA\DAV\Connector\Sabre\Exception\InvalidPath, s:message = «Filename contains at least one invalid character» |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/api-newline.txt | Код 404 Not Found |
