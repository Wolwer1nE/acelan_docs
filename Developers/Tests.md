## Тестирование в пакете  ACELAN-COMPOS

Разработка пакета ведется через тестирование.
В пакете ACELAN-COMPOS реализованы два типа тестов:
- UnitTests - это smoke-тесты и регресионные тесты.
- PerformanceTests - нагрузочные тесты

Для проверки системы на ошибки необходимо каждый раз проверять все тесты из набора UnitTests. Для того чтобы тестирование не занимало много времени, все долгие тесты вынесены в PerformanceTests. Их прогонять не обязательно, если вы не писали новые тесты в этом наборе.


### Создание теста
 Расмотрим создание теста на примере функции сложения [Add](C:\Users\elfomenko\acelan\Acelan.Tests\Example\SumTest.cs).
 
 1. Необходимо создать функцию ```Add```, которую будем тестировать

 2. В проекте ```acelan\Acelan.Tests``` необходимо создать новый файл, создать класс нашего теста ```internal class SumTest``` и написать атрибут ```[TestFixture]```.

 3. В классе создается void-функция с атрибутом ```[Test]```.

 4. Используя нашу функцию сложения, находим сумму двух чисел (2+3).

 5. Для сравнения полученных результатов используются методы [assert](https://docs.nunit.org/articles/nunit/writing-tests/assertions/assertions.html). 


``````c# 
namespace Acelan.Tests.Example
{
    [TestFixture]
    internal class SumTest
    {
        public int Add(int a, int b)
        {
            return a + b;   
        }

        [Test]
        public void TestAdd()
        {
            var c = Add(2,3);
            c.Equals(5);
        }
    }
}
``````

