# Espressif <div id="espressif"></div>

## Почему, Esp32? <div id="whyesp32"></div>

Esp32 использует два процессора Xtensa с симметричной адресацией. Это разработка Гарварда, сделанная аж в 1997 году. Заявленная тактовая частота 240/160 МГц.
Частота таймеров 80 МГц

Очень низкое энергопотребление в режиме «глубокого сна» — 5-2,5 мкА.

Аналогичные по архитектуре модули STM32, скажем  стоят значительно дороже. Да, 400 МГц, да отличная документация. Но, скажем процессор STM32F407 стоит около 1000 рублей на АлиЭкспрессе.

Почти нет микропроцессоров, у которых были бы «на борту» Bluetooth и Wi-Fi стоимостью 150-200 рублей.

Удобные для любительской распайки модули с микропроцессором «на борту»
Можно сказать, что в сегменте [DIY](#DIY) у модуля с процессором ESP32 конкурентов нет.

<div id="DIY"></div>Самодельничество, гаражные разработки, «сделай сам» (англ. DIY — Do It Yourself), — вид деятельности, при котором люди самостоятельно производят какие-либо изделия для собственного использования. © https://ru.wikipedia.org/wiki/DIY

## Сравнение <div id="compare"></div>

### ESP32
Основные причины использования ESP32

1. 2.4 мГц
2. WiFi
3. Bluetooth/BLE
4. По соотношению цена/производительность, STM гонку проигрывает. Цена на модуль от 50 руб и на макетную плату от 250 руб

### STM
1. Линейка STM *гораздо* лучше документирована
2. По производительности STM выигрывает. Так, STM32H750 работает на частоте 400 мГц
3. Поддержка компилятора STM сделана *значительно* лучше. Так, можно использовать инструкции DSP отдельно от C-кода.

## Полезные ссылки <div id="usefullinks"></div>
1. https://espressif.com — Основной сайт Espressif
2. https://docs.espressif.com — Описание API esp-mdf, esp-adf, esp-idf и многого другого
3. https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/index.html 
4. https://github.com/espressif/esp-idf — Espressif IoT Development Framework — IDF
5. https://github.com/espressif/esp-mdf — ESP-WIFI-MESH Development Framework — MDF
6. https://github.com/espressif/esp-adf — Espressif Audio Development Framework — ADF.
7. https://github.com/espressif/esp-iot-solution Набор решений для создания IoT
8. https://github.com/espressif/idf-eclipse-plugin Плагин для Eclipse
9. https://github.com/espressif/vscode-esp-idf-extension Плагин для VSCode
10. https://www.espressif.com/sites/default/files/documentation/esp32_datasheet_en.pdf
11. https://www.espressif.com/en/support/documents/technical-documents Вся техническая документация
12. https://0x04.net/~mwk/doc/xtensa.pdf Xtensa® Instruction Set Architecture (ISA) Reference Manual (Для тех, кто хочет изучить более глубоко архитектуру MCU Xtensa)

