# Серверное окружение для разработки на основе Docker

## Установка

Скачиваем содержимое репозитория в папку docker в корне проекта. Всю папку можно исключить из индекса, чтобы не нагружать IDE, если Вы ей пользуетесь. Внутри есть файл с конфигом .env.example - его нужно переименовать в .env и заполнить необходимыми данными. Также есть файл package.json, в котором есть набор скриптов для быстрого управления сервером (запуск, остановка, пересборка новых версий модулей и.т.д)
