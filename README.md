[![Foo](https://img.shields.io/badge/Version-1.1-brightgreen.svg?style=flat-square)](#versions)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0%B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/Hamming?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)

# Hamming
Библиотека для упаковки и распаковки данных по алгоритму Хэмминга (избыточные данные для восстановления)
- Порядок алгоритма 4-7 (чем выше, тем надёжнее, но дольше обработка)
- Восстановление данных, повреждённых при пересылке
- Принимает любой тип данных

### Совместимость
Совместима со всеми Arduino платформами (используются Arduino-функции)

## Содержание
- [Установка](#install)
- [Инициализация](#init)
- [Использование](#usage)
- [Пример](#example)
- [Версии](#versions)
- [Баги и обратная связь](#feedback)

<a id="install"></a>
## Установка
- Библиотеку можно найти по названию **Hamming** и установить через менеджер библиотек в:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Скачать библиотеку](https://github.com/GyverLibs/Hamming/archive/refs/heads/main.zip) .zip архивом для ручной установки:
    - Распаковать и положить в *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Распаковать и положить в *C:\Program Files\Arduino\libraries* (Windows x32)
    - Распаковать и положить в *Документы/Arduino/libraries/*
    - (Arduino IDE) автоматическая установка из .zip: *Скетч/Подключить библиотеку/Добавить .ZIP библиотеку…* и указать скачанный архив
- Читай более подробную инструкцию по установке библиотек [здесь](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Обновление
- Рекомендую всегда обновлять библиотеку: в новых версиях исправляются ошибки и баги, а также проводится оптимизация и добавляются новые фичи
- Через менеджер библиотек IDE: найти библиотеку как при установке и нажать "Обновить"
- Вручную: **удалить папку со старой версией**, а затем положить на её место новую. "Замену" делать нельзя: иногда в новых версиях удаляются файлы, которые останутся при замене и могут привести к ошибкам!


<a id="init"></a>
## Инициализация
```cpp
Hamming<5> buf;   // <> - порядок кода (4-7)
```

<a id="usage"></a>
## Использование
```cpp
bool pack(T &data);     // запаковать данные любого типа в буфер
uint32_t unpack(uint8_t* data, uint32_t size);    // распаковать данные. возврат: 0 ОК, 1 исправлены ошибки, 2 и 3 - есть неисправленные ошибки
uint8_t status();       // возврат: 0 ОК, 1 исправлены ошибки, 2 и 3 - есть неисправленные ошибки
uint32_t length();      // размер буфера (больше чем размер входных данных)
void stop();            // освободить буфер
uint8_t *buffer;        // внутренний буфер
```

<a id="example"></a>
## Пример
Остальные примеры смотри в **examples**!
```cpp
#include <Hamming.h>
void setup() {
  Serial.begin(9600);

  // создали дату (любой тип)
  char data0[] = "Hello, world! Lorem Ipsum";

  // запаковали
  Hamming<5> buf;   // <> - порядок кода (4-7)
  // пакуем во внутренний буфер buf.buffer
  buf.pack(data0);  // 12мс
  
  // запакованные данные хранятся в buf.buffer с размером buf.length()
  // можно их "отправить"
  
  // ======== "ПЕРЕДАЧА" ========

  // проебали часть (два байта!)
  buf.buffer[5] = 0;
  buf.buffer[6] = 0;
  
  // ======== "ПРИЁМ" ========

  // распаковываем. Порядок в <> такой же!
  Hamming<5> buf2;
  
  // передаём "принятые" запакованные данные и их длину (её тоже надо передать или знать)
  buf2.unpack(buf.buffer, buf.length());  // 6мс
  
  // выводим как строку
  Serial.println((char*)buf2.buffer);
  
  // выводим статус распаковки
  Serial.println(buf2.status());
}

void loop() {
}
```

<a id="versions"></a>
## Версии
- v1.0
- v1.1 - исправлена критическая ошибка

<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!


При сообщении о багах или некорректной работе библиотеки нужно обязательно указывать:
- Версия библиотеки
- Какой используется МК
- Версия SDK (для ESP)
- Версия Arduino IDE
- Корректно ли работают ли встроенные примеры, в которых используются функции и конструкции, приводящие к багу в вашем коде
- Какой код загружался, какая работа от него ожидалась и как он работает в реальности
- В идеале приложить минимальный код, в котором наблюдается баг. Не полотно из тысячи строк, а минимальный код
