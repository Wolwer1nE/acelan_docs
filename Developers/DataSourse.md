##Получение данных из файла

Тестовые данные сетки конечных элементов выгружаются из пакета COMSOL в формате .nas
и размещаются в папке `Acelan.Tests/DataForTests`.

Класс NastranDataSourse наследует интерфейсы IFileBasedDataSource и
IDataSourse.
Интерфейс IDataSourse включает в себя сетку, свойства материалов,
граничные условия и свойства решателя и нагрузки.

Метод Init(string path) класса NastranDataSourse определяет узлы, элементы и
свойства элементов входного файла.

Тестовые скрипты для выгрузки сеток расположены
в файле `Acelan.Tests/FemTests/DataSourseTest.cs`.

Тестовые скрипты для узлов расположены
в файле `Acelan.Tests/FemTests/NastranDsTest.cs`.