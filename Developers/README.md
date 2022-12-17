## ACELAN.WebUI

Основной проект с пользовательским интерфейсом.
Для запуска на машине разработчика потребуется установленный MySQL.
В файле `appsettings.Development.json` нужно указать логин и пароль от MySQL в строке покдлючения.
При отсутствии БД приложение создаст ее при первом запуске и заполнит дефолтными данными. 

## ACELAN.Solo

Консольное приложение для отдельного использования. В качестве входной информации получает скрипты на языке ACELAN.

Сборка под Windows:

`dotnet publish -c release`

Сборка под linux:

`dotnet publish -c release --self-contained --runtime linux-x64 --framework netcoreapp2.2`

