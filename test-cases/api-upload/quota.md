# Квота

## TC-113. Попытка загрузки файла больше свободного места

**Предусловия:**  
Кто: user2, Basic Auth с паролем приложения (окружение Postman NextcloudLocal user2).  
Данные: у user2 нет файлов (занято 0 байт).  
Настройки: у user2 квота 300 MB (314 572 800 байт), правила имён по умолчанию.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT, в Body (binary) выбрать файл | /remote.php/dav/files/user2/536870912.bin<br>Файл: 536870912.bin (536 870 912 байт > 314 572 800) | Код 507 Insufficient Storage. s:exception = Sabre\DAV\Exception\InsufficientStorage, s:message = «Insufficient space in /536870912.bin» |
| 2 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user2/536870912.bin | Код 404 Not Found |
| 3 | Проверить занятое место: отправить PROPFIND (Depth: 0) на корень со свойством quota-used-bytes | /remote.php/dav/files/user2/ | quota-used-bytes = 0 |

## TC-114. Загрузить файлы по очереди до превышения квоты

**Описание:** Размер файлов подобран так, чтобы второй файл точно не помещался: 167 772 160 + 167 772 160 = 335 544 320 > 314 572 800.

**Предусловия:**  
Кто: user2, Basic Auth с паролем приложения (окружение Postman NextcloudLocal user2).  
Данные: у user2 нет файлов (занято 0 байт).  
Настройки: у user2 квота 300 MB (314 572 800 байт), правила имён по умолчанию.

| № | Шаг | Данные | Ожидаемый результат |
|---|---|---|---|
| 1 | Отправить PUT с первым файлом | /remote.php/dav/files/user2/160MiB-1.bin<br>Файл: 160MiB-1.bin (167 772 160 байт) | Код 201 Created. В заголовках ответа есть ETag и OC-FileId |
| 2 | После ответа на первый запрос отправить PUT со вторым файлом | /remote.php/dav/files/user2/160MiB-2.bin<br>Файл: 160MiB-2.bin (167 772 160 байт) | Код 507 Insufficient Storage, s:message = «Insufficient space in /160MiB-2.bin» |
| 3 | Проверить, что файл не создан: отправить PROPFIND с заголовком Depth: 0 | /remote.php/dav/files/user2/160MiB-2.bin | Код 404 Not Found |
| 4 | Проверить занятое место: отправить PROPFIND (Depth: 0) на корень со свойством quota-used-bytes | /remote.php/dav/files/user2/ | quota-used-bytes = 167 772 160 (не больше квоты) |

**Постусловия:** Удалить созданный файл: DELETE /remote.php/dav/files/user2/160MiB-1.bin, очистить корзину.
