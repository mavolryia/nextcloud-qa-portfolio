# Загрузка частями

## TC-132. Попытка собрать файл с пропущенной частью

**Описание:** Загрузка частями (chunking v2): MKCOL временной папки, PUT частей, MOVE .file. Документация: https://docs.nextcloud.com/server/latest/developer_manual/client_apis/WebDAV/chunking.html. В Postman запускать подпапку целиком через Runner.

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла sc-missing.bin нет; части p1.bin, p2.bin, p3.bin по 5 242 880 байт (вместе 15 728 640) в C:\nextcloud-test-big\chunks.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Создать временную папку загрузки: отправить MKCOL с заголовком Destination | /remote.php/dav/uploads/user1/{{uploadId}}<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-missing.bin | Код 201 Created |
| 2 | Отправить PUT части 1 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/1<br>Файл: p1.bin<br>OC-Total-Length: 15728640 | Код 201 Created |
| 3 | Отправить PUT части 3 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/3<br>Файл: p3.bin<br>OC-Total-Length: 15728640 | Код 201 Created |
| 4 | Собрать файл: отправить MOVE на .file с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/.file<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-missing.bin<br>OC-Total-Length: 15728640 | Код 400 Bad Request. s:message = «Chunks on server do not sum up to 15728640 but to 10485760 bytes» |
| 5 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/sc-missing.bin | Код 404 Not Found |

**Постусловия:** Если временная папка осталась: DELETE /remote.php/dav/uploads/user1/{{uploadId}}.

## TC-133. Собрать файл с пропуском номера части и подогнанным OC-Total-Length

**Описание:** Загрузка частями (chunking v2): MKCOL временной папки, PUT частей, MOVE .file. Документация: https://docs.nextcloud.com/server/latest/developer_manual/client_apis/WebDAV/chunking.html. В Postman запускать подпапку целиком через Runner.  
Сервер не сверяет, что номера частей идут подряд: защищает только сверка общего размера. Наблюдение, не дефект.

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла sc-gap.bin нет; части p1.bin и p3.bin по 5 242 880 байт.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Создать временную папку загрузки: отправить MKCOL с заголовком Destination | /remote.php/dav/uploads/user1/{{uploadId}}<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-gap.bin | Код 201 Created |
| 2 | Отправить PUT части 1 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/1<br>Файл: p1.bin<br>OC-Total-Length: 10485760 | Код 201 Created |
| 3 | Отправить PUT части 3 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/3<br>Файл: p3.bin<br>OC-Total-Length: 10485760 | Код 201 Created |
| 4 | Собрать файл: отправить MOVE на .file с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/.file<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-gap.bin<br>OC-Total-Length: 10485760 | Код 201 Created |
| 5 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/sc-gap.bin | Код 207 Multi-Status. getcontentlength = 10485760 |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/sc-gap.bin, очистить корзину.

## TC-134. Попытка собрать файл с неверным OC-Total-Length (на 1 байт больше)

**Описание:** Загрузка частями (chunking v2): MKCOL временной папки, PUT частей, MOVE .file. Документация: https://docs.nextcloud.com/server/latest/developer_manual/client_apis/WebDAV/chunking.html. В Postman запускать подпапку целиком через Runner.

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла sc-length.bin нет; части p1.bin, p2.bin, p3.bin по 5 242 880 байт (вместе 15 728 640) в C:\nextcloud-test-big\chunks.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Создать временную папку загрузки: отправить MKCOL с заголовком Destination | /remote.php/dav/uploads/user1/{{uploadId}}<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-length.bin | Код 201 Created |
| 2 | Отправить PUT части 1 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/1<br>Файл: p1.bin<br>OC-Total-Length: 15728641 | Код 201 Created |
| 3 | Отправить PUT части 2 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/2<br>Файл: p2.bin<br>OC-Total-Length: 15728641 | Код 201 Created |
| 4 | Отправить PUT части 3 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/3<br>Файл: p3.bin<br>OC-Total-Length: 15728641 | Код 201 Created |
| 5 | Собрать файл: отправить MOVE на .file с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/.file<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-length.bin<br>OC-Total-Length: 15728641 | Код 400 Bad Request. s:message = «Chunks on server do not sum up to 15728641 but to 15728640 bytes» |
| 6 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/sc-length.bin | Код 404 Not Found |

**Постусловия:** Если временная папка осталась: DELETE /remote.php/dav/uploads/user1/{{uploadId}}.

## TC-135. Собрать файл из частей, отправленных не по порядку

**Описание:** Загрузка частями (chunking v2): MKCOL временной папки, PUT частей, MOVE .file. Документация: https://docs.nextcloud.com/server/latest/developer_manual/client_apis/WebDAV/chunking.html. В Postman запускать подпапку целиком через Runner.  
По документации части собираются по порядку номеров, а не по порядку отправки.

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла sc-order.bin нет; части p1.bin, p2.bin, p3.bin по 5 242 880 байт (вместе 15 728 640) в C:\nextcloud-test-big\chunks; full.bin = p1 + p2 + p3.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Создать временную папку загрузки: отправить MKCOL с заголовком Destination | /remote.php/dav/uploads/user1/{{uploadId}}<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-order.bin | Код 201 Created |
| 2 | Отправить PUT части 2 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/2<br>Файл: p2.bin<br>OC-Total-Length: 15728640 | Код 201 Created |
| 3 | Отправить PUT части 1 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/1<br>Файл: p1.bin<br>OC-Total-Length: 15728640 | Код 201 Created |
| 4 | Отправить PUT части 3 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/3<br>Файл: p3.bin<br>OC-Total-Length: 15728640 | Код 201 Created |
| 5 | Собрать файл: отправить MOVE на .file с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/.file<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-order.bin<br>OC-Total-Length: 15728640 | Код 201 Created |
| 6 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/sc-order.bin | Код 207 Multi-Status. getcontentlength = 15728640 |
| 7 | Скачать файл (GET) и сравнить SHA-256 с full.bin | /remote.php/dav/files/user1/sc-order.bin | Код 200. Хеш скачанного файла совпадает с хешем full.bin |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/sc-order.bin, очистить корзину.

## TC-136. Собрать файл из частей меньше 5 МиБ

**Описание:** Загрузка частями (chunking v2): MKCOL временной папки, PUT частей, MOVE .file. Документация: https://docs.nextcloud.com/server/latest/developer_manual/client_apis/WebDAV/chunking.html. В Postman запускать подпапку целиком через Runner.  
Документация требует части от 5 МБ (кроме последней). Это ограничение S3; на локальном хранилище оно не проверяется. Наблюдение.

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла sc-small.bin нет; части small1.bin и small2.bin по 1 048 576 байт.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен); локальное хранилище.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Создать временную папку загрузки: отправить MKCOL с заголовком Destination | /remote.php/dav/uploads/user1/{{uploadId}}<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-small.bin | Код 201 Created |
| 2 | Отправить PUT части 1 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/1<br>Файл: small1.bin<br>OC-Total-Length: 2097152 | Код 201 Created |
| 3 | Отправить PUT части 2 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/2<br>Файл: small2.bin<br>OC-Total-Length: 2097152 | Код 201 Created |
| 4 | Собрать файл: отправить MOVE на .file с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/.file<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-small.bin<br>OC-Total-Length: 2097152 | Код 201 Created |
| 5 | Проверить, что файл создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/sc-small.bin | Код 207 Multi-Status. getcontentlength = 2097152 |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user1/sc-small.bin, очистить корзину.

## TC-137. Попытка загрузить части сверх квоты без OC-Total-Length

**Описание:** Загрузка частями (chunking v2): MKCOL временной папки, PUT частей, MOVE .file. Документация: https://docs.nextcloud.com/server/latest/developer_manual/client_apis/WebDAV/chunking.html. В Postman запускать подпапку целиком через Runner.  
Факт на 35.0.2: все части принимаются (201) и не учитываются в квоте, отказ только при сборке (кандидат в дефект №9).

**Предусловия:**  
Кто: user2, Basic Auth с паролем приложения (окружение Postman NextcloudLocal user2).  
Данные: у user2 нет файлов (занято 0 байт), папка uploads пустая; часть p100.bin = 104 857 600 байт.  
Настройки: у user2 квота 300 MB (314 572 800 байт), правила имён по умолчанию.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Создать временную папку загрузки: отправить MKCOL с заголовком Destination | /remote.php/dav/uploads/user2/{{uploadId}}<br>Destination: {{baseUrl}}/remote.php/dav/files/user2/sc-noquota.bin | Код 201 Created |
| 2 | Отправить по очереди PUT частей 1-6 без заголовка OC-Total-Length | /remote.php/dav/uploads/user2/{{uploadId}}/1 ... /6<br>Файл: p100.bin (6 × 104 857 600 = 629 145 600 байт > 314 572 800) | Части, которые не помещаются в квоту, отклоняются с кодом 507 Insufficient Storage |
| 3 | Проверить место на диске: размер папки data/user2/uploads на сервере | docker exec nextcloud-docker-app-1 du -sb /var/www/html/data/user2/uploads | Не больше 314 572 800 байт |
| 4 | Собрать файл: отправить MOVE на .file | /remote.php/dav/uploads/user2/{{uploadId}}/.file<br>Destination: {{baseUrl}}/remote.php/dav/files/user2/sc-noquota.bin | Код 507 Insufficient Storage |
| 5 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user2/sc-noquota.bin | Код 404 Not Found |

**Постусловия:** Если временная папка осталась: DELETE /remote.php/dav/uploads/user2/{{uploadId}}.

## TC-138. Отменить загрузку частями

**Описание:** Загрузка частями (chunking v2): MKCOL временной папки, PUT частей, MOVE .file. Документация: https://docs.nextcloud.com/server/latest/developer_manual/client_apis/WebDAV/chunking.html. В Postman запускать подпапку целиком через Runner.

**Предусловия:**  
Кто: user1, Basic Auth с паролем приложения (окружение Postman NextcloudLocal).  
Данные: файла sc-abort.bin нет; части p1.bin, p2.bin, p3.bin по 5 242 880 байт (вместе 15 728 640) в C:\nextcloud-test-big\chunks.  
Настройки: по умолчанию (правила имён по умолчанию, режим совместимости с Windows выключен).

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Создать временную папку загрузки: отправить MKCOL с заголовком Destination | /remote.php/dav/uploads/user1/{{uploadId}}<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-abort.bin | Код 201 Created |
| 2 | Отправить PUT части 1 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/1<br>Файл: p1.bin<br>OC-Total-Length: 15728640 | Код 201 Created |
| 3 | Отправить PUT части 2 с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/2<br>Файл: p2.bin<br>OC-Total-Length: 15728640 | Код 201 Created |
| 4 | Отменить загрузку: отправить DELETE на временную папку | /remote.php/dav/uploads/user1/{{uploadId}} | Код 204 No Content |
| 5 | Собрать файл: отправить MOVE на .file с заголовком OC-Total-Length | /remote.php/dav/uploads/user1/{{uploadId}}/.file<br>Destination: {{baseUrl}}/remote.php/dav/files/user1/sc-abort.bin<br>OC-Total-Length: 15728640 | Код 404 Not Found: собирать нечего |
| 6 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user1/sc-abort.bin | Код 404 Not Found |
