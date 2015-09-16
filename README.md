# bitset
Битовое множество реализовано базовым классом Bitset
```
template <size_t size_, typename BaseType = unsigned char>
class Bitset{
	private:
		BaseType value_[(size_ >> (sizeof(value_type) + 2)) + 
			((size_ & ((sizeof(value_type) << 3) - 1)) ? 1 : 0)];
};
```
Данные хранятся последовательно в непрерывном массиве. Доступ к элементам битового множества реализован двумя способами:
* прямой
```
set() / clear() / test()
```
* через итераторы
```
operator[] / begin() / end()
```

## Оптимизация производительности
В текущей реализации использовались приемы для оптимизации производительности. В частности медленные операции деления и деления с остатком заменены бытовыми операциями. Например, для доступа к i-тому биту массива в общем случае необходимо произвести следующее вычисление:
```
Bitset<40,unsigned short> b;
b.test(20) := b.value_[20 / sizeof(b.value_[0]) / 8] & (1 << (20 % sizeof(b.value_[0]*8))) 
```
В данном примере используются:
* две операции деления
* одна операция деления с остатком
* одна операция умножения
* одна операция битового И
* одна операция битового сдвига 
Принимая во внимание следующие факты:
* операции деления и умножения на 2 можно заменить битовым сдвигом
* встроенные целочисленные типы имеют размер кратный 2
* остаток от деления A на 2^n представляет из себя последние n бит числа A
получим:
```
b.test(20) := b.value_[20 >> (sizeof(b.value_[0]) + 2)] & (1 << (20 & (sizeof(b.value_[0] << 3) - 1))) 
```
В последнем примере используются:
* одна операция битового И
* три операции битового сдвига
* одна операция сложения
* одна опрерация вычитания
Дальнейшая оптимизация возможна за счет хранения кешированных значений ``` sizeof(b.value_[0]) + 2``` и ``` sizeof(b.value_[0]) - 1```.


## Требования
* CMake version 3.3.2
* git version 1.8.3.1
* gcc version 4.8.3

## Установка CMake
На момент сборки стабильная версия CMake 3.3.2
```
mkdir /opt/cmake
cd /opt/cmake
curl https://cmake.org/files/v3.3/cmake-3.3.2-Linux-x86_64.sh > cmake-3.3.2-Linux-x86_64.sh
sh cmake-3.3.2-Linux-x86_64.sh
```

## Сборка проекта
```
cd ~
git clone https://github.com/russkin/bitset.git
cd bitset
mkdir build
cd build
/opt/cmake/bin/cmake ..
make
make test
```
## TODO
* Контроль выхода за границы массива
* Динамически расширяемое множество
* Конструктор из различных типов данных

## Известные ошибки

## Органичения

## Тесты производительности

## License MIT
