[![Foo](https://img.shields.io/badge/Version-3.5-brightgreen.svg?style=flat-square)](#versions)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0%B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-brightgreen.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/microLED?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)


# microLED
microLED - ультра-лёгкая библиотека для работы с адресной лентой/матрицей
- Основная фишка: сжатие цвета, код занимает в разы меньше места в SRAM по сравнению с аналогами (FastLED, NeoPixel и др.)
- Поддержка сжатия цвета: 8, 16 и 24 бита
- Возможность работать вообще без буфера (с некоторыми ограничениями)
- Работа с цветом:
- RGB
- HSV
- HEX (WEB цвета)
- "Цветовое колесо" (1500 или 255 самых ярких оттенков)
- 16 встроенных цветов
- Цвет по теплоте
- Градиенты
- Возможность чтения сжатого цвета в mHEX 0xRRGGBB и массив RGB
- Оптимизированный asm вывод
- Встроенная поддержка работы с адресными матрицами
- Поддержка чипов: 2811/2812/2813/2815/2818/WS6812/APA102
- Встроенная tinyLED для работы на ATtiny
- Совместимость типов данных и инструментов из FastLED
- Расширенная настройка прерываний
- Нативная поддержка матриц
- Сохранение работы millis() (только для AVR)
- Поддержка SPI лент (программная и аппаратная)

### Совместимость
Только AVR, ATmega и ATtiny

### Документация
К библиотеке есть [расширенная документация](https://alexgyver.ru/microLED/)

## Содержание
- [Установка](#install)
- [Инициализация](#init)
- [Использование](#usage)
- [Пример](#example)
- [Версии](#versions)
- [Баги и обратная связь](#feedback)

<a id="install"></a>
## Установка
- Библиотеку можно найти по названию **microLED** и установить через менеджер библиотек в:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Скачать библиотеку](https://github.com/GyverLibs/microLED/archive/refs/heads/main.zip) .zip архивом для ручной установки:
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
microLED< amount, pin, clock, chip, order, cli, millis>
- amount – количество светодиодов в ленте. Для работы в режиме потока можно указать 0, так как длина ленты фактически ничем не ограничена.
- pin –  пин, к которому подключен дата-вход ленты (D, Din, DI).
- clock – пин, к которому подключен тактовый-вход ленты (C, CLK). Этот пин подключается только для SPI лент, например APA102.
    - Для работы с лентами серии WSxxxx нужно указать вместо этого пина параметр MLED_NO_CLOCK или минус 1 , т.е. -1
- chip – модель ленты (светодиодов), в библиотеке поддерживаются LED_WS2811, LED_WS2812, LED_WS2813, LED_WS2815, LED_WS2818, LED_WS6812, APA102, APA102_SPI. Выбор модели ленты задаёт скорость протокола (она у них разная) и настройки тока потребления для режимов ограничения (о них читай дальше).
- order – порядок цветов в ленте. В идеальном мире порядок цветов должен зависеть от модели чипа и эта настройка должна быть встроена в выбор чипа, но китайцы торгуют лентами, которые по протоколу совпадают с одним чипом, но имеют другой порядок цветов. Таким образом библиотека поддерживает больше типов лент, чем написано выше, но нужно угадать с выбором “клона” и порядок цветов. 
    - Порядок: ORDER_RGB, ORDER_RBG, ORDER_BRG, ORDER_BGR, ORDER_GRB, ORDER_GBR.

microLED< NUMLEDS, STRIP_PIN, -1, LED_WS2811, ORDER_GBR> strip;
microLED< NUMLEDS, STRIP_PIN, -1, LED_WS2812, ORDER_GRB> strip;
microLED< NUMLEDS, STRIP_PIN, -1, LED_WS2813, ORDER_GRB> strip;
microLED< NUMLEDS, STRIP_PIN, -1, LED_WS2815, ORDER_GRB> strip;
microLED< NUMLEDS, STRIP_PIN, -1, LED_WS2818, ORDER_RGB> strip;
microLED< NUMLEDS, STRIP_PIN, -1, LED_WS6812, ORDER_RGB> strip;
microLED< NUMLEDS, STRIP_PIN, CLOCK_PIN, LED_APA102, ORDER_BGR> strip;
microLED< NUMLEDS, MLED_NO_CLOCK, MLED_NO_CLOCK, LED_APA102_SPI, ORDER_BGR> strip;
```

<a id="usage"></a>
## Использование
См. [документацию](https://alexgyver.ru/microLED/)
```cpp
// шаблон: < количество, пин, чип, порядок, прерывания, миллис>
// инициализация ЛЕНТА: нет аргументов
microLED;
// инициализация МАТРИЦА: ширина матрицы, высота матрицы, тип матрицы, угол подключения, направление (см. ПОДКЛЮЧЕНИЕ МАТРИЦЫ)
microLED(uint8_t width, uint8_t height, M_type type, M_connection conn, M_dir dir);
// лента и матрица
void set(int n, mData color);  // ставим цвет светодиода mData (равносильно leds[n] = color)  
mData get(int num);// получить цвет диода в mData (равносильно leds[n])
void fill(mData color);  // заливка цветом mData
void fill(int from, int to, mData color);// заливка цветом mData
void fillGradient(int from, int to, mData color1, mData color2);  // залить градиентом двух цветов
void fade(int num, byte val);  // уменьшить яркость
// матрица
uint16_t getPixNumber(int x, int y);  // получить номер пикселя в ленте по координатам
void set(int x, int y, mData color);  // ставим цвет пикселя x y в mData
mData get(int x, int y);// получить цвет пикселя в mData
void fade(int x, int y, byte val);// уменьшить яркость
void drawBitmap8(int X, int Y, const uint8_t *frame, int width, int height);  // вывод битмапа (битмап 1мерный PROGMEM)
void drawBitmap16(int X, int Y, const uint16_t *frame, int width, int height);  // вывод битмапа (битмап 1мерный PROGMEM)
void drawBitmap32(int X, int Y, const uint32_t *frame, int width, int height);  // вывод битмапа (битмап 1мерный PROGMEM)
// общее
void setMaxCurrent(int ma);// установить максимальный ток (автокоррекция яркости). 0 - выключено
void setBrightness(uint8_t newBright);  // яркость 0-255
void clear();  // очистка
// вывод буфера
void show();  // вывести весь буфер
// вывод потока
void begin();  // начать вывод потоком
void send(mData data);  // отправить один светодиод
void end();// закончить вывод потоком
// цвет
uint32_t getHEX(mData data);            // перепаковать в 24 бит HEX
mData getFade(mData data, uint8_t val);        // уменьшить яркость на val
mData getBlend(int x, int amount, mData c0, mData c1);  // получить промежуточный цвет
mData mRGB(uint8_t r, uint8_t g, uint8_t b);    // RGB 255, 255, 255
mData mWheel(int color, uint8_t bright=255);    // цвета 0-1530 + яркость 
mData mWheel8(uint8_t color, uint8_t bright=255);  // цвета 0-255 + яркость
mData mHEX(uint32_t color);              // mHEX цвет
mData mHSV(uint8_t h, uint8_t s, uint8_t v);    // HSV 255, 255, 255
mData mHSVfast(uint8_t h, uint8_t s, uint8_t v);  // HSV 255, 255, 255
mData mKelvin(int kelvin);              // температура
// макросы уменьшения яркости
fade8(x, b)
fade8R(x, b)
fade8G(x, b)
fade8B(x, b) 
// упаковка-распаковка
getR(x)
getG(x)
getB(x)
mergeRGB(r,g,b)
mergeRGBraw(r,g,b)
getCRT(byte x) - получить скорректированное значение яркости x с учётом выбранной модели CRT гамма-коррекции
getCRT_PGM(byte x) - получить CRT из прогмем (работает только если выбрана PGM модель)
getCRT_SQUARE(byte x) - получить CRT по квадратной модели
getCRT_QUBIC(byte x) - получить CRT по кубической модели
RGB24to16(x) - конвертация 24-бит цвета в 16-бит
RGB24to8(x) - конвертация 24-бит цвета в 8-бит
RGB16to24(x) - конвертация 16-бит цвета в 24-бит
RGB8to24(x) - конвертация 8-бит цвета в 24-бит
RGB24toR(x) - вытащить байт R из 24-бит цвета
RGB24toG(x) - вытащить байт G из 24-бит цвета
RGB24toB(x) - вытащить байт B из 24-бит цвета
RGBto24(r,g,b) - склеить 24-бит цвет
RGBto16(r,g,b) - склеить 16-бит цвет
RGBto8(r,g,b) - склеить 8-бит цвет
```

<a id="example"></a>
## Пример
Остальные примеры смотри в **examples**!
```cpp
// базовый пример работы с лентой, основные возможности
// библиотека microLED версии 3.0+
// для более подробной информации читай документацию

// константы для удобства
#define STRIP_PIN 2     // пин ленты
#define NUMLEDS 20      // кол-во светодиодов

// ===== ЦВЕТОВАЯ ГЛУБИНА =====
// 1, 2, 3 (байт на цвет)
// на меньшем цветовом разрешении скетч будет занимать в разы меньше места,
// но уменьшится и количество оттенков и уровней яркости!
// дефайн делается ДО ПОДКЛЮЧЕНИЯ БИБЛИОТЕКИ
// без него будет 3 байта по умолчанию
#define COLOR_DEBTH 3

#include <microLED.h>   // подключаем библу

// ======= ИНИЦИАЛИЗАЦИЯ =======
// <колво-ледов, пин, клок пин, чип, порядок>
// microLED<NUMLEDS, DATA_PIN, CLOCK_PIN, LED_WS2818, ORDER_GRB> strip;
// CLOCK пин нужен только для SPI лент (например APA102)
// для обычных WS лент указываем MLED_NO_CLOCK
// по APA102 смотри отдельный гайд в примерах

// различные китайские подделки могут иметь совместимость
// с одним чипом, но другой порядок цветов!
// поддерживаемые чипы лент и их официальный порядок цветов:
// microLED<NUMLEDS, STRIP_PIN, MLED_NO_CLOCK, LED_WS2811, ORDER_GBR> strip;
// microLED<NUMLEDS, STRIP_PIN, MLED_NO_CLOCK, LED_WS2812, ORDER_GRB> strip;
// microLED<NUMLEDS, STRIP_PIN, MLED_NO_CLOCK, LED_WS2813, ORDER_GRB> strip;
// microLED<NUMLEDS, STRIP_PIN, MLED_NO_CLOCK, LED_WS2815, ORDER_GRB> strip;
// microLED<NUMLEDS, STRIP_PIN, MLED_NO_CLOCK, LED_WS2818, ORDER_RGB> strip;
// microLED<NUMLEDS, STRIP_PIN, MLED_NO_CLOCK, LED_WS6812, ORDER_RGB> strip;
// microLED<NUMLEDS, STRIP_PIN, CLOCK_PIN, LED_APA102, ORDER_BGR> strip;
// microLED<NUMLEDS, MLED_NO_CLOCK, MLED_NO_CLOCK, LED_APA102_SPI, ORDER_BGR> strip;	// для аппаратного SPI


// ======= ПРЕРЫВАНИЯ =======
// для повышения надёжности передачи данных на ленту можно отключать прерывания.
// В библиотеке есть 4 режима:
// CLI_OFF - прерывания не отключаются (возможны сбои в работе ленты)
// CLI_LOW - прерывания отключаются на время передачи одного цвета
// CLI_AVER - прерывания отключаются на время передачи одного светодиода (3 цвета)
// CLI_HIGH - прерывания отключаются на время передачи даных на всю ленту

// По умолчанию отключение прерываний стоит на CLI_OFF (не отключаются)
// Параметр передаётся 5ым при инициализации:
// microLED<NUMLEDS, STRIP_PIN, LED_WS2818, ORDER_GRB, CLI_AVER> strip;

// ======= СПАСИТЕ МИЛЛИС =======
// При отключении прерываний в режиме среднего и высокого проритета (CLI_AVER и CLI_HIGH)
// неизбежно будут чуть отставать функции времени millis() и micros()
// В библиотеке встроено обслуживание функций времени, для активации передаём SAVE_MILLIS
// 6-ым аргументом при инициализации:
// microLED<NUMLEDS, STRIP_PIN, MLED_NO_CLOCK, LED_WS2818, ORDER_GRB, CLI_AVER, SAVE_MILLIS> strip;
// это НЕЗНАЧИТЕЛЬНО замедлит вывод на ленту, но позволит миллису считать без отставания!

// инициализирую ленту (выше был гайд!)
microLED<NUMLEDS, STRIP_PIN, MLED_NO_CLOCK, LED_WS2818, ORDER_GRB, CLI_AVER> strip;

void setup() {
  // ===================== БАЗОВЫЕ ШТУКИ =====================
  // яркость (0-255)
  strip.setBrightness(60);
  // яркость применяется по CRT гамме
  // применяется при выводе .show() !

  // очистка буфера (выключить диоды, чёрный цвет)
  strip.clear();
  // применяется при выводе .show() !

  strip.show(); // вывод изменений на ленту
  delay(1);     // между вызовами show должна быть пауза минимум 40 мкс !!!!

  // ===================== УСТАНОВКА ЦВЕТА =====================
  // Библиотека поддерживает два варианта работы с лентой:
  // изменение цвета конкретного диода при помощи функции set(диод, цвет)
  // или работа с массивом .leds[] "вручную"

  // запись strip.set(диод, цвет); равносильна strip.leds[диод] = цвет;

  // ------------- ОСНОВНЫЕ ФУНКЦИИ РАБОТЫ С ЦВЕТОМ ------------
  // указанные ниже функции врзвращают тип данных mData - сжатое представление цвета

  // mRGB(uint8_t r, uint8_t g, uint8_t b);   // цвет RGB, 0-255 каждый канал
  strip.set(0, mRGB(255, 0, 0));              // диод 0, цвет RGB (255 0 0) (красный)

  // mHSV(uint8_t h, uint8_t s, uint8_t v);   // цвет HSV, 0-255 каждый канал
  strip.leds[1] = mHSV(30, 255, 255);         // диод 1, (цвет 30, яркость и насыщенность максимум)

  // mHSVfast(uint8_t h, uint8_t s, uint8_t v); // цвет HSV, 0-255 каждый канал
  // расчёт выполняется чуть быстрее, но цвета не такие плавные
  strip.set(2, mHSVfast(90, 255, 255));         // диод 2, цвет 90, яркость и насыщенность максимум

  // mHEX(uint32_t color);        // WEB цвета (0xRRGGBB)
  strip.set(3, mHEX(0x30B210));   // диод 3, цвет HEX 0x30B210

  // в библиотеке есть 17 предустановленных цветов (макс. яркость)
  strip.leds[4] = mAqua;          // диод 4, цвет aqua

  // mWheel(int color);                   // цвета радуги 0-1530
  // mWheel(int color, uint8_t bright);   // цвета радуги 0-1530 + яркость 0-255
  strip.set(5, mWheel(1200));             // диод 5, цвет 1200

  // mWheel8(int color);                  // цвета радуги 0-255
  // mWheel8(int color, uint8_t bright);  // цвета радуги 0-255 + яркость 0-255
  //strip.set(6, mWheel8(100));     // диод 6, цвет 100 (диапазон 0-255 вдоль радуги)
  strip.set(6, mWheel8(100, 50));   // вторым параметром можно передать яркость

  // mKelvin(int kelvin);           // цветовая температура 1'000-40'000 Кельвин
  strip.set(7, mKelvin(3500));      // диод 7, цветовая температура 3500К

  strip.show();                     // выводим все изменения на ленту
  delay(2000);                      // задержка показа

  // ===================== ЗАЛИВКА =====================
  // Есть готовая функция для заливки всей ленты цветом - .fill()
  // принимает конвертированный цвет, например от функций цвета или констант выше
  strip.fill(mYellow);  // заливаем жёлтым
  strip.show();         // выводим изменения
  delay(2000);

  // также можно указать начало и конец заливки
  strip.fill(3, 7, mWheel8(100));   // заливаем ~зелёным с 3 по 6: счёт идёт с 0, заливается до указанного -1
  strip.show();                     // выводим изменения
  delay(2000);

  // ------------- РУЧНАЯ ЗАЛИВКА В ЦИКЛЕ ------------
  // Например покрасим половину ленты в один, половину в другой
  for (int i = 0; i < NUMLEDS / 2; i++) strip.leds[i] = mHSV(0, 255, 255);  	  // красный
  for (int i = NUMLEDS / 2; i < NUMLEDS; i++) strip.leds[i] = mHSV(80, 255, 255); // примерно зелёный
  strip.show(); // выводим изменения
  delay(2000);

  // ------------------------------------------
  // Для ускорения ручных заливок (ускорения расчёта цвета) можно создать переменную типа mData
  mData value1, value2;
  value1 = mHSV(60, 100, 255);
  value2 = mHSV(190, 255, 190);
  for (int i = 0; i < NUMLEDS; i++) {
    if (i < NUMLEDS / 2) strip.leds[i] = value1;  // первая половина ленты
    else strip.leds[i] = value2;                  // вторая половина ленты
  }
  strip.show(); // выводим изменения
  delay(2000);

  // ------------------------------------------
  // в цикле можно менять параметры генерации цвета. Например, сделаем радугу
  for (int i = 0; i < NUMLEDS; i++) strip.set(i, mWheel8(i * 255 / NUMLEDS)); // полный круг от 0 до 255
  strip.show(); // выводим изменения
  delay(2000);

  // или градиент от красного к чёрному (последовательно меняя яркость)
  for (int i = 0; i < NUMLEDS; i++) strip.set(i, mWheel8(0, i * 255 / NUMLEDS)); // полный круг от 0 до 255
  strip.show(); // выводим изменения
}

void loop() {
}
```

<a id="versions"></a>
## Версии
- v1.1
    - Поправлена инициализация
    - Добавлен оранжевый цвет
    
- v2.0
    - Переписан и сильно ускорен алгоритм вывода
    - Добавлено ограничение тока
    
- v2.1
    - Поправлена ошибка с матрицей
    
- v2.2
    - Цвет PINK заменён на MAGENTA
    
- v2.3
    - Добавлена дефайн настройка MICROLED_ALLOW_INTERRUPTS
    - Исправлены мелкие ошибки, улучшена стабильность
    
- v2.4
    - Добавлен ORDER_BGR
    
- v2.5
    - Яркость по CRT гамме
    
- v3.0
    - Добавлены функции и цвета: 
    - Цветовая температура .setKelvin() и дата Kelvin
    - getBlend(позиция, всего, цвет1, цвет2) и getBlend2(позиция, всего, цвет1, цвет2)
    - .fill(от, до)
    - .fillGradient(от, до, цвет1, цвет2)
    - Добавлен шум Перлина (вытащил из FastLED)
    - Добавлены градиенты
    - Полностью переделан и оптимизирован вывод
    - Возможность работать вообще без буфера
    - Настройка ограничения тока для всех типов лент
    - Настраиваемый запрет прерываний
    - Сохранение работы миллиса на время отправки
    - Поддержка лент 2811, 2812, 2813, 2815, 2818
    - Поддержка 4х цветных лент: WS6812
    - Инициализация переделана под шаблон, смотри примеры!
    - Много изменений в названиях, всё переделано и упрощено, читай документацию!
    
- v3.1
    - Поправлены ошибки компиляции для нестандартных ядер Ардуино и Аттини
    - Добавлен класс tinyLED.h для вывода потоком с ATtiny и вообще любых AVR (см. пример)
    - Вырезаны инструменты FastLED (рандом, шум), будем работать напрямую с фастлед
    - Добавлена поддержка совместной работы с библиотекой FastLED и конвертация из её типов!
    - Добавлена поддержка ленты APA102 (а также других SPI), программная и аппаратная SPI
    
- v3.2
    - Чуть оптимизации и исправлений
    
- v3.3
    - Исправлен критический баг с влиянием на другие пины
    
- v3.4
    - Переработан ASM вывод, меньше весит, легче адаптируется под другие частоты / тайминги
    - Добавлена поддержка LGT8F328P с частотой 32/16/8 MHz
    - Переработан поллинг millis()/micros() - прямой вызов прерывания TIMER0_OVF, убран лишний код
    
- v3.5
    - Исправлена ошибка компиляции в некоторых угодных компилятору случаях

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
