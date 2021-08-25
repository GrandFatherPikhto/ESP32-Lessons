# Установка и настройка консольного инструментария esp-idf под Linux на примере Ubuntu 21.04
[Назад](./README.md)
- [Установка программного обеспечения: git, python3, cmake, ninja](#installsoftware)
- [Настройка **udev**, чтобы можно было подключать отладочную плату через USB](#udev)
- [Клонирование репозитория с github](#clone)
- [Установка инструментария **esp-idf**](#toolchain)
- [Настройка быстрого экспорта переменных окружения **esp-idf**](#idfexp)
- [Сборка, прошивка и мониторинг рабочего проекта 'hello_world'](#helloworld)
- [Правка кода в проекте 'hello_world'](#edit)

**Задача**: Установить *esp-idf*, *VSCode*, *Eclipse*, так, чтобы программирование из консоли, *VSCode* и *Eclipse* осуществлялись с одного дистрибутива *esp-idf*

1. Установим необходимые программные пакеты: git, python, ninja-build, cmake <div id="installsoftware"></div>

```console
sudo apt install git-all python3.9 ninja-build cmake
```

Проверим, есть ли алиас или мягкая ссылка ```python```. Обычно, если python установлен, то 3.x версия, которая вызывается командой ```python3```. Если нельзя вызвать ```python```, можно cделать мягкую ссылку ```ln -sf /usr/bin/python3.9 ~/.local/bin/python``` или ```alias python=/usr/bin/python3```. Если делаем ссылко,важно делать именно мягкую ссылку, поскольку файл жёсткой ссылки будет искать сопутствующие необходимые библиотеки в том же каталоге, откуда он запущен.  ```python``` нужен потому, что плагины **idf-eclipse-plugin** и **idf-vscode-plugin** требуют для работы именно возможность вызова ```python```, не ```python3```, ```python3.9``` и т.д.

2.  Настроим **udev** — управление устройствами для новых версий ядра Linux. Логинимся под суперюзером: ```sudo su``` <div id="udev"></div>
   1. Копируем файл **40-dfuse.rules** в ```cp ./40-dfuse.rule /etc/udev/rules.d/```. Или его можно создать при помощи ```vim /etc/udev/rules.d && vim 40-dfuse.rules``` или ```nano /etc/udev/rules.d && vim 40-dfuse.rules``` или какого-то другого консольного редактора и скопируем туда правила доступа для устройств.

Файл ```/etc/udev/rules.d/40-dfuse```

```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="303a", ATTRS{idProduct}=="00??", GROUP="plugdev", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="60??", MODE="0666", ENV{ID_MM_DEVICE_IGNORE}="1"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", MODE="0666", ENV{ID_MM_DEVICE_IGNORE}="1"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="067b", ATTRS{idProduct}=="2303", MODE="0666", ENV{ID_MM_DEVICE_IGNORE}="1"
```

   3.  Перезапускаем службу **udev** ```sudo systemctl restart udev.service```
   4.  Перечитываем правила **udev**: ```sudo udevadm control --reload-rules```

3. <div id="clone"></div>Создадим какой-нибудь каталог, куда будем складывать фреймворки *~/espressif*. Дело в том, что есть, как минимум, три фреймворка, которые, возможно Вы будете использовать. [ESP-IDF](https://github.com/espressif/esp-idf) — Espressif IoT Development Framework, [ESP-ADF](https://github.com/espressif/esp-adf) — Espressif Audio Development Framework, [ESP-MDF](https://github.com/espressif/esp-mdf) — Espressif Mesh Development Framework. Устраивать в корневом домашнем каталоге зоопарк из фреймворков не очень хочется. Итак, создали каталог, перешли в него и вызвали ```git``` с ключём *--recursive*, чтобы скачать все "подлинкованные" в репозитории дополнительные проекты. Не забываем про ключ **--recursive**. Он нужен для того, чтобы сказать все «подлинкованные» проекты, которые использует фреймворк.

```console
mkdir ~/espressif
cd ~/espressif
git clone https://github.com/espressif/esp-idf.git --recursive
```
<details>
<summary>Журнал закачки esp-idf</summary>
~/espressif  $ git clone https://github.com/espressif/esp-idf.git --recursive

Клонирование в «esp-idf»…

remote: Enumerating objects: 265284, done.

remote: Counting objects: 100% (3872/3872), done.

remote: Compressing objects: 100% (1844/1844), done.

remote: Total 265284 (delta 1993), reused 3378 (delta 1860), pack-reused 261412

Получение объектов: 100% (265284/265284), 148.87 МиБ | 10.94 МиБ/с, готово.

Определение изменений: 100% (195633/195633), готово.

Подмодуль «components/asio/asio» (https://github.com/espressif/asio.git) зарегистрирован по пути «components/asio/asio»

Подмодуль «components/bootloader/subproject/components/micro-ecc/micro-ecc» (https://github.com/kmackay/micro-ecc.git) зарегистрирован по пути «components/bootloader/subproject/components/micro-ecc/micro-ecc»

Подмодуль «components/bt/controller/lib_esp32» (https://github.com/espressif/esp32-bt-lib.git) зарегистрирован по пути «components/bt/controller/lib_esp32»

Подмодуль «components/bt/controller/lib_esp32c3_family» (https://github.com/espressif/esp32c3-bt-lib.git) зарегистрирован по пути «components/bt/controller/lib_esp32c3_family»

Подмодуль «components/bt/host/nimble/nimble» (https://github.com/espressif/esp-nimble.git) зарегистрирован по пути «components/bt/host/nimble/nimble»

Подмодуль «components/cbor/tinycbor» (https://github.com/intel/tinycbor.git) зарегистрирован по пути «components/cbor/tinycbor»

Подмодуль «components/cmock/CMock» (https://github.com/ThrowTheSwitch/CMock.git) зарегистрирован по пути «components/cmock/CMock»

Подмодуль «components/coap/libcoap» (https://github.com/obgm/libcoap.git) зарегистрирован по пути «components/coap/libcoap»

Подмодуль «components/esp_phy/lib» (https://github.com/espressif/esp-phy-lib.git) зарегистрирован по пути «components/esp_phy/lib»

Подмодуль «components/esp_wifi/lib» (https://github.com/espressif/esp32-wifi-lib.git) зарегистрирован по пути «components/esp_wifi/lib»

Подмодуль «components/esptool_py/esptool» (https://github.com/espressif/esptool.git) зарегистрирован по пути «components/esptool_py/esptool»

Подмодуль «components/expat/expat» (https://github.com/libexpat/libexpat.git) зарегистрирован по пути «components/expat/expat»

Подмодуль «components/ieee802154/lib» (https://github.com/espressif/esp-ieee802154-lib.git) зарегистрирован по пути «components/ieee802154/lib»

Подмодуль «components/json/cJSON» (https://github.com/DaveGamble/cJSON.git) зарегистрирован по пути «components/json/cJSON»

Подмодуль «components/libsodium/libsodium» (https://github.com/jedisct1/libsodium.git) зарегистрирован по пути «components/libsodium/libsodium»

Подмодуль «components/lwip/lwip» (https://github.com/espressif/esp-lwip.git) зарегистрирован по пути «components/lwip/lwip»

Подмодуль «components/mbedtls/mbedtls» (https://github.com/espressif/mbedtls.git) зарегистрирован по пути «components/mbedtls/mbedtls»

Подмодуль «components/mqtt/esp-mqtt» (https://github.com/espressif/esp-mqtt.git) зарегистрирован по пути «components/mqtt/esp-mqtt»

Подмодуль «components/nghttp/nghttp2» (https://github.com/nghttp2/nghttp2.git) зарегистрирован по пути «components/nghttp/nghttp2»

Подмодуль «components/openthread/lib» (https://github.com/espressif/esp-thread-lib.git) зарегистрирован по пути «components/openthread/lib»

Подмодуль «components/openthread/openthread» (https://github.com/espressif/openthread.git) зарегистрирован по пути «components/openthread/openthread»

Подмодуль «components/protobuf-c/protobuf-c» (https://github.com/protobuf-c/protobuf-c.git) зарегистрирован по пути «components/protobuf-c/protobuf-c»

Подмодуль «components/spiffs/spiffs» (https://github.com/pellepl/spiffs.git) зарегистрирован по пути «components/spiffs/spiffs»

Подмодуль «components/tinyusb/tinyusb» (https://github.com/espressif/tinyusb.git) зарегистрирован по пути «components/tinyusb/tinyusb»

Подмодуль «components/unity/unity» (https://github.com/ThrowTheSwitch/Unity.git) зарегистрирован по пути «components/unity/unity»

Подмодуль «examples/build_system/cmake/import_lib/main/lib/tinyxml2» (https://github.com/leethomason/tinyxml2.git) зарегистрирован по пути «examples/build_system/cmake/import_lib/main/lib/tinyxml2»

Подмодуль «examples/peripherals/secure_element/atecc608_ecdsa/components/esp-cryptoauthlib» (https://github.com/espressif/esp-cryptoauthlib.git) зарегистрирован по пути «examples/peripherals/secure_element/atecc608_ecdsa/components/esp-cryptoauthlib»

Клонирование в «/home/denis/espressif/esp-idf/components/asio/asio»…

remote: Enumerating objects: 48394, done.

remote: Counting objects: 100% (15239/15239), done.

remote: Compressing objects: 100% (1324/1324), done.

remote: Total 48394 (delta 14813), reused 13915 (delta 13915), pack-reused 33155

Получение объектов: 100% (48394/48394), 15.06 МиБ | 9.87 МиБ/с, готово.

Определение изменений: 100% (33905/33905), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/bootloader/subproject/components/micro-ecc/micro-ecc»…

remote: Enumerating objects: 1143, done.

remote: Counting objects: 100% (48/48), done.

remote: Compressing objects: 100% (37/37), done.

remote: Total 1143 (delta 21), reused 28 (delta 9), pack-reused 1095

Получение объектов: 100% (1143/1143), 687.07 КиБ | 2.38 МиБ/с, готово.

Определение изменений: 100% (664/664), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/bt/controller/lib_esp32»…

remote: Enumerating objects: 1547, done.

remote: Counting objects: 100% (667/667), done.

remote: Compressing objects: 100% (363/363), done.

remote: Total 1547 (delta 427), reused 525 (delta 285), pack-reused 880

Получение объектов: 100% (1547/1547), 6.13 МиБ | 9.09 МиБ/с, готово.

Определение изменений: 100% (1025/1025), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/bt/controller/lib_esp32c3_family»…

remote: Enumerating objects: 107, done.

remote: Counting objects: 100% (107/107), done.

remote: Compressing objects: 100% (69/69), done.

remote: Total 107 (delta 46), reused 86 (delta 25), pack-reused 0

Получение объектов: 100% (107/107), 383.46 КиБ | 2.64 МиБ/с, готово.

Определение изменений: 100% (46/46), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/bt/host/nimble/nimble»…

remote: Enumerating objects: 40824, done.

remote: Counting objects: 100% (380/380), done.

remote: Compressing objects: 100% (141/141), done.

remote: Total 40824 (delta 261), reused 338 (delta 234), pack-reused 40444

Получение объектов: 100% (40824/40824), 11.91 МиБ | 9.76 МиБ/с, готово.

Определение изменений: 100% (24989/24989), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/cbor/tinycbor»…

remote: Enumerating objects: 2797, done.

remote: Counting objects: 100% (27/27), done.

remote: Compressing objects: 100% (19/19), done.

remote: Total 2797 (delta 12), reused 17 (delta 8), pack-reused 2770

Получение объектов: 100% (2797/2797), 1.30 МиБ | 4.79 МиБ/с, готово.

Определение изменений: 100% (1888/1888), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/cmock/CMock»…

remote: Enumerating objects: 5719, done.

remote: Counting objects: 100% (108/108), done.

remote: Compressing objects: 100% (73/73), done.

remote: Total 5719 (delta 52), reused 61 (delta 25), pack-reused 5611

Получение объектов: 100% (5719/5719), 3.60 МиБ | 7.83 МиБ/с, готово.

Определение изменений: 100% (3881/3881), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/coap/libcoap»…

remote: Enumerating objects: 12280, done.

remote: Counting objects: 100% (1351/1351), done.

remote: Compressing objects: 100% (442/442), done.

remote: Total 12280 (delta 888), reused 1218 (delta 853), pack-reused 10929

Получение объектов: 100% (12280/12280), 5.46 МиБ | 9.02 МиБ/с, готово.

Определение изменений: 100% (8697/8697), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/esp_phy/lib»…

remote: Enumerating objects: 45, done.

remote: Counting objects: 100% (45/45), done.

remote: Compressing objects: 100% (38/38), done.

remote: Total 45 (delta 9), reused 42 (delta 6), pack-reused 0

Получение объектов: 100% (45/45), 687.79 КиБ | 644.00 КиБ/с, готово.

Определение изменений: 100% (9/9), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/esp_wifi/lib»…

remote: Enumerating objects: 15182, done.

remote: Counting objects: 100% (2927/2927), done.

remote: Compressing objects: 100% (598/598), done.

remote: Total 15182 (delta 2362), reused 2892 (delta 2329), pack-reused 12255

Получение объектов: 100% (15182/15182), 174.46 МиБ | 10.76 МиБ/с, готово.

Определение изменений: 100% (12417/12417), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/esptool_py/esptool»…

remote: Enumerating objects: 3410, done.

remote: Counting objects: 100% (447/447), done.

remote: Compressing objects: 100% (248/248), done.

remote: Total 3410 (delta 268), reused 338 (delta 197), pack-reused 2963

Получение объектов: 100% (3410/3410), 10.72 МиБ | 8.51 МиБ/с, готово.

Определение изменений: 100% (2139/2139), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/expat/expat»…

remote: Enumerating objects: 17563, done.

remote: Counting objects: 100% (1063/1063), done.

remote: Compressing objects: 100% (381/381), done.

remote: Total 17563 (delta 733), reused 935 (delta 672), pack-reused 16500

Получение объектов: 100% (17563/17563), 14.40 МиБ | 10.28 МиБ/с, готово.

Определение изменений: 100% (13077/13077), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/ieee802154/lib»…

remote: Enumerating objects: 22, done.

remote: Counting objects: 100% (22/22), done.

remote: Compressing objects: 100% (15/15), done.

remote: Total 22 (delta 6), reused 20 (delta 4), pack-reused 0

Получение объектов: 100% (22/22), 54.74 КиБ | 1.12 МиБ/с, готово.

Определение изменений: 100% (6/6), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/json/cJSON»…

remote: Enumerating objects: 4498, done.

remote: Counting objects: 100% (16/16), done.

remote: Compressing objects: 100% (16/16), done.

remote: Total 4498 (delta 4), reused 7 (delta 0), pack-reused 4482

Получение объектов: 100% (4498/4498), 2.46 МиБ | 6.38 МиБ/с, готово.

Определение изменений: 100% (2987/2987), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/libsodium/libsodium»…

remote: Enumerating objects: 32905, done.

remote: Counting objects: 100% (512/512), done.

remote: Compressing objects: 100% (266/266), done.

remote: Total 32905 (delta 260), reused 448 (delta 235), pack-reused 32393

Получение объектов: 100% (32905/32905), 8.66 МиБ | 9.59 МиБ/с, готово.

Определение изменений: 100% (19517/19517), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/lwip/lwip»…

remote: Enumerating objects: 50119, done.

remote: Counting objects: 100% (153/153), done.

remote: Compressing objects: 100% (101/101), done.

remote: Total 50119 (delta 90), reused 92 (delta 51), pack-reused 49966

Получение объектов: 100% (50119/50119), 9.93 МиБ | 9.59 МиБ/с, готово.

Определение изменений: 100% (37831/37831), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/mbedtls/mbedtls»…

remote: Enumerating objects: 77211, done.

remote: Total 77211 (delta 0), reused 0 (delta 0), pack-reused 77211

Получение объектов: 100% (77211/77211), 34.91 МиБ | 10.43 МиБ/с, готово.

Определение изменений: 100% (59486/59486), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/mqtt/esp-mqtt»…

remote: Enumerating objects: 2558, done.

remote: Counting objects: 100% (268/268), done.

remote: Compressing objects: 100% (148/148), done.

remote: Total 2558 (delta 147), reused 234 (delta 115), pack-reused 2290

Получение объектов: 100% (2558/2558), 1.53 МиБ | 5.48 МиБ/с, готово.

Определение изменений: 100% (1378/1378), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/nghttp/nghttp2»…

remote: Enumerating objects: 41726, done.

remote: Counting objects: 100% (1000/1000), done.

remote: Compressing objects: 100% (372/372), done.

remote: Total 41726 (delta 652), reused 941 (delta 624), pack-reused 40726

Получение объектов: 100% (41726/41726), 34.54 МиБ | 10.63 МиБ/с, готово.

Определение изменений: 100% (30992/30992), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/openthread/lib»…

remote: Enumerating objects: 143, done.

remote: Counting objects: 100% (143/143), done.

remote: Compressing objects: 100% (78/78), done.

remote: Total 143 (delta 74), reused 127 (delta 58), pack-reused 0

Получение объектов: 100% (143/143), 2.61 МиБ | 7.18 МиБ/с, готово.

Определение изменений: 100% (74/74), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/openthread/openthread»…

remote: Enumerating objects: 83554, done.

remote: Counting objects: 100% (63/63), done.

remote: Compressing objects: 100% (39/39), done.

remote: Total 83554 (delta 24), reused 34 (delta 24), pack-reused 83491

Получение объектов: 100% (83554/83554), 82.49 МиБ | 8.48 МиБ/с, готово.

Определение изменений: 100% (65311/65311), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/protobuf-c/protobuf-c»…

remote: Enumerating objects: 3650, done.

remote: Counting objects: 100% (117/117), done.

remote: Compressing objects: 100% (81/81), done.

remote: Total 3650 (delta 59), reused 71 (delta 31), pack-reused 3533

Получение объектов: 100% (3650/3650), 1.49 МиБ | 3.46 МиБ/с, готово.

Определение изменений: 100% (2326/2326), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/spiffs/spiffs»…

remote: Enumerating objects: 1502, done.

remote: Counting objects: 100% (19/19), done.

remote: Compressing objects: 100% (14/14), done.

remote: Total 1502 (delta 7), reused 12 (delta 5), pack-reused 1483

Получение объектов: 100% (1502/1502), 1.06 МиБ | 3.69 МиБ/с, готово.

Определение изменений: 100% (1042/1042), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/tinyusb/tinyusb»…

remote: Enumerating objects: 49097, done.

remote: Counting objects: 100% (1406/1406), done.

remote: Compressing objects: 100% (638/638), done.

remote: Total 49097 (delta 759), reused 1254 (delta 721), pack-reused 47691

Получение объектов: 100% (49097/49097), 26.61 МиБ | 9.77 МиБ/с, готово.

Определение изменений: 100% (32404/32404), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/unity/unity»…

remote: Enumerating objects: 5865, done.

remote: Counting objects: 100% (88/88), done.

remote: Compressing objects: 100% (56/56), done.

remote: Total 5865 (delta 43), reused 69 (delta 32), pack-reused 5777

Получение объектов: 100% (5865/5865), 6.62 МиБ | 8.75 МиБ/с, готово.

Определение изменений: 100% (3627/3627), готово.

Клонирование в «/home/denis/espressif/esp-idf/examples/build_system/cmake/import_lib/main/lib/tinyxml2»…

remote: Enumerating objects: 4456, done.

remote: Counting objects: 100% (303/303), done.

remote: Compressing objects: 100% (145/145), done.

remote: Total 4456 (delta 229), reused 188 (delta 158), pack-reused 4153

Получение объектов: 100% (4456/4456), 3.20 МиБ | 3.33 МиБ/с, готово.

Определение изменений: 100% (2961/2961), готово.

Клонирование в «/home/denis/espressif/esp-idf/examples/peripherals/secure_element/atecc608_ecdsa/components/esp-cryptoauthlib»…

remote: Enumerating objects: 596, done.

remote: Counting objects: 100% (596/596), done.

remote: Compressing objects: 100% (303/303), done.

remote: Total 596 (delta 325), reused 553 (delta 282), pack-reused 0

Получение объектов: 100% (596/596), 905.33 КиБ | 4.64 МиБ/с, готово.

Определение изменений: 100% (325/325), готово.

Подмодуль по пути «components/asio/asio»: забрано состояние «f31694c9f1746ba189a4bcae2e34db15135ddb22»

Подмодуль по пути «components/bootloader/subproject/components/micro-ecc/micro-ecc»: забрано состояние «d037ec89546fad14b5c4d5456c2e23a71e554966»

Подмодуль по пути «components/bt/controller/lib_esp32»: забрано состояние «fb49791b7c1a8a35f06e68124c90022667b4cff1»

Подмодуль по пути «components/bt/controller/lib_esp32c3_family»: забрано состояние «9ca8afd50afde57958a67fca65847edc52f7d91c»

Подмодуль по пути «components/bt/host/nimble/nimble»: забрано состояние «aef55bbf636ed580d4d6408a5c2e75d1f70a875e»

Подмодуль по пути «components/cbor/tinycbor»: забрано состояние «7c349dbb6b8d76db39383b226d3ebdf59b8ab37d»

Подмодуль по пути «components/cmock/CMock»: забрано состояние «eeecc49ce8af123cf8ad40efdb9673e37b56230f»

Подмодуль «vendor/c_exception» (https://github.com/throwtheswitch/cexception.git) зарегистрирован по пути «components/cmock/CMock/vendor/c_exception»

Подмодуль «vendor/unity» (https://github.com/throwtheswitch/unity.git) зарегистрирован по пути «components/cmock/CMock/vendor/unity»

Клонирование в «/home/denis/espressif/esp-idf/components/cmock/CMock/vendor/c_exception»…

remote: Enumerating objects: 326, done.

remote: Counting objects: 100% (61/61), done.

remote: Compressing objects: 100% (47/47), done.

remote: Total 326 (delta 23), reused 34 (delta 9), pack-reused 265

Получение объектов: 100% (326/326), 547.04 КиБ | 2.97 МиБ/с, готово.

Определение изменений: 100% (150/150), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/cmock/CMock/vendor/unity»…

remote: Enumerating objects: 5865, done.

remote: Counting objects: 100% (88/88), done.

remote: Compressing objects: 100% (56/56), done.

remote: Total 5865 (delta 43), reused 69 (delta 32), pack-reused 5777

Получение объектов: 100% (5865/5865), 6.62 МиБ | 8.93 МиБ/с, готово.

Определение изменений: 100% (3627/3627), готово.

Подмодуль по пути «components/cmock/CMock/vendor/c_exception»: забрано состояние «71b47be7c950f1bf5f7e5303779fa99a16224bb6»

Подмодуль по пути «components/cmock/CMock/vendor/unity»: забрано состояние «cf949f45ca6d172a177b00da21310607b97bc7a7»

Подмодуль по пути «components/coap/libcoap»: забрано состояние «98954eb30a2e728e172a6cd29430ae5bc999b585»

Подмодуль «ext/tinydtls» (https://github.com/eclipse/tinydtls.git) зарегистрирован по пути «components/coap/libcoap/ext/tinydtls»

Клонирование в «/home/denis/espressif/esp-idf/components/coap/libcoap/ext/tinydtls»…

remote: Enumerating objects: 3360, done.

remote: Counting objects: 100% (789/789), done.

remote: Compressing objects: 100% (120/120), done.

remote: Total 3360 (delta 686), reused 670 (delta 669), pack-reused 2571

Получение объектов: 100% (3360/3360), 987.54 КиБ | 4.26 МиБ/с, готово.

Определение изменений: 100% (2397/2397), готово.

Подмодуль по пути «components/coap/libcoap/ext/tinydtls»: забрано состояние «7f8c86e501e690301630029fa9bae22424adf618»

Подмодуль по пути «components/esp_phy/lib»: забрано состояние «8b1137c35cc3d2b1085e7f857c2530efb115d3a3»

Подмодуль по пути «components/esp_wifi/lib»: забрано состояние «492bb8b01b6ccff1e830b52c7b15d8c3d90101c6»

Подмодуль по пути «components/esptool_py/esptool»: забрано состояние «9876dfe58353f01c873e1543dd0654c5b04314a4»

Подмодуль по пути «components/expat/expat»: забрано состояние «a28238bdeebc087071777001245df1876a11f5ee»

Подмодуль по пути «components/ieee802154/lib»: забрано состояние «efbc05d641040253567e825dae53731da595c7b5»

Подмодуль по пути «components/json/cJSON»: забрано состояние «d2735278ed1c2e4556f53a7a782063b31331dbf7»

Подмодуль по пути «components/libsodium/libsodium»: забрано состояние «4f5e89fa84ce1d178a6765b8b46f2b6f91216677»

Подмодуль по пути «components/lwip/lwip»: забрано состояние «2195f7416fb3136831babf3e96c027a73075bd4f»

Подмодуль по пути «components/mbedtls/mbedtls»: забрано состояние «6465247f67167518b8813ae2faaf422704e4b1a3»

Подмодуль по пути «components/mqtt/esp-mqtt»: забрано состояние «f10321a53b53a146ee299cfecc320b89c0cf6611»

Подмодуль по пути «components/nghttp/nghttp2»: забрано состояние «8f7b008b158e12de0e58247afd170f127dbb6456»

Подмодуль «third-party/mruby» (https://github.com/mruby/mruby) зарегистрирован по пути «components/nghttp/nghttp2/third-party/mruby»

Подмодуль «third-party/neverbleed» (https://github.com/tatsuhiro-t/neverbleed.git) зарегистрирован по пути «components/nghttp/nghttp2/third-party/neverbleed»

Клонирование в «/home/denis/espressif/esp-idf/components/nghttp/nghttp2/third-party/mruby»…

remote: Enumerating objects: 65288, done.

remote: Counting objects: 100% (865/865), done.

remote: Compressing objects: 100% (342/342), done.

remote: Total 65288 (delta 484), reused 835 (delta 480), pack-reused 64423

Получение объектов: 100% (65288/65288), 17.60 МиБ | 10.21 МиБ/с, готово.

Определение изменений: 100% (40872/40872), готово.

Клонирование в «/home/denis/espressif/esp-idf/components/nghttp/nghttp2/third-party/neverbleed»…

remote: Enumerating objects: 234, done.

remote: Total 234 (delta 0), reused 0 (delta 0), pack-reused 234

Получение объектов: 100% (234/234), 83.16 КиБ | 860.00 КиБ/с, готово.

Определение изменений: 100% (144/144), готово.

Подмодуль по пути «components/nghttp/nghttp2/third-party/mruby»: забрано состояние «7c91efc1ffda769a5f1a872c646c82b00698f1b8»

Подмодуль по пути «components/nghttp/nghttp2/third-party/neverbleed»: забрано состояние «b967ca054f48a36f82d8fcdd32e54ec5144f2751»

Подмодуль по пути «components/openthread/lib»: забрано состояние «105f3610d2258d7a7dd1c72f5f1adea89077c6cc»

Подмодуль по пути «components/openthread/openthread»: забрано состояние «a662c32eb074cc624bf344f810f65f8637a89552»

Подмодуль по пути «components/protobuf-c/protobuf-c»: забрано состояние «dac1a65feac4ad72f612aab99f487056fbcf5c1a»

Подмодуль по пути «components/spiffs/spiffs»: забрано состояние «f5e26c4e933189593a71c6b82cda381a7b21e41c»

Подмодуль по пути «components/tinyusb/tinyusb»: забрано состояние «c4badd394eda18199c0196ed0be1e2d635f0a5f6»

Подмодуль по пути «components/unity/unity»: забрано состояние «7d2bf62b7e6afaf38153041a9d53c21aeeca9a25»

Подмодуль по пути «examples/build_system/cmake/import_lib/main/lib/tinyxml2»: забрано состояние «7e8e249990ec491ec15990cf95b6d871a66cf64a»

Подмодуль по пути «examples/peripherals/secure_element/atecc608_ecdsa/components/esp-cryptoauthlib»: забрано состояние «bb672b0437485fc7420add178299631692b15ac3»

</details>

4. <div id="toolchain"></div>Устанавливаем инструментарий **esp-idf**, необходимый для сборки проектов:

```console
~/espressif/esp-idf/install.sh
```

или если мы находимся в каталоге ```~/espressif/esp-idf/```
```console
./install.sh
```

<details>
<summary>Журнал установки инструментария(toolchain) **esp-idf**</summary>
~/espressif/esp-idf  ‹master› $ ./install.sh

Detecting the Python interpreter

Checking "python" ...

Python 3.9.6

"python" has been detected

Installing ESP-IDF tools

WARNING: File /home/denis/.espressif/idf-env.json was not found.

Creating /home/denis/.espressif/idf-env.json

WARNING: File /home/denis/.espressif/idf-env.json can not be created.

Selected targets are:

Installing tools: xtensa-esp32-elf, xtensa-esp32s2-elf, xtensa-esp32s3-elf, riscv32-esp-elf, esp32ulp-elf, esp32s2ulp-elf, openocd-esp32

Installing xtensa-esp32-elf@esp-2021r1-8.4.0

Downloading xtensa-esp32-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz to /home/denis/.espressif/dist/xtensa-esp32-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz.tmp

Done

Extracting /home/denis/.espressif/dist/xtensa-esp32-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz to /home/denis/.espressif/tools/xtensa-esp32-elf/esp-2021r1-8.4.0

Installing xtensa-esp32s2-elf@esp-2021r1-8.4.0

Downloading xtensa-esp32s2-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz to /home/denis/.espressif/dist/xtensa-esp32s2-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz.tmp

Done

Extracting /home/denis/.espressif/dist/xtensa-esp32s2-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz to /home/denis/.espressif/tools/xtensa-esp32s2-elf/esp-2021r1-8.4.0

Installing xtensa-esp32s3-elf@esp-2021r1-8.4.0

Downloading xtensa-esp32s3-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz to /home/denis/.espressif/dist/xtensa-esp32s3-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz.tmp

Done

Extracting /home/denis/.espressif/dist/xtensa-esp32s3-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz to /home/denis/.espressif/tools/xtensa-esp32s3-elf/esp-2021r1-8.4.0

Installing riscv32-esp-elf@esp-2021r1-8.4.0

Downloading riscv32-esp-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz to /home/denis/.espressif/dist/riscv32-esp-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz.tmp

Done

Extracting /home/denis/.espressif/dist/riscv32-esp-elf-gcc8_4_0-esp-2021r1-linux-amd64.tar.gz to /home/denis/.espressif/tools/riscv32-esp-elf/esp-2021r1-8.4.0

Installing esp32ulp-elf@2.28.51-esp-20191205

Downloading binutils-esp32ulp-linux-amd64-2.28.51-esp-20191205.tar.gz to /home/denis/.espressif/dist/binutils-esp32ulp-linux-amd64-2.28.51-esp-20191205.tar.gz.tmp

Done

Extracting /home/denis/.espressif/dist/binutils-esp32ulp-linux-amd64-2.28.51-esp-20191205.tar.gz to /home/denis/.espressif/tools/esp32ulp-elf/2.28.51-esp-20191205

Installing esp32s2ulp-elf@2.28.51-esp-20191205

Downloading binutils-esp32s2ulp-linux-amd64-2.28.51-esp-20191205.tar.gz to /home/denis/.espressif/dist/binutils-esp32s2ulp-linux-amd64-2.28.51-esp-20191205.tar.gz.tmp

Done

Extracting /home/denis/.espressif/dist/binutils-esp32s2ulp-linux-amd64-2.28.51-esp-20191205.tar.gz to /home/denis/.espressif/tools/esp32s2ulp-elf/2.28.51-esp-20191205

Installing openocd-esp32@v0.10.0-esp32-20210721

Downloading openocd-esp32-linux64-0.10.0-esp32-20210721.tar.gz to /home/denis/.espressif/dist/openocd-esp32-linux64-0.10.0-esp32-20210721.tar.gz.tmp

Done

Extracting /home/denis/.espressif/dist/openocd-esp32-linux64-0.10.0-esp32-20210721.tar.gz to /home/denis/.espressif/tools/openocd-esp32/v0.10.0-esp32-20210721

Installing Python environment and packages

Creating a new Python environment in /home/denis/.espressif/python_env/idf4.4_py3.9_env

created virtual environment CPython3.9.6.final.0-64 in 1715ms

  creator CPython3Posix(dest=/home/denis/.espressif/python_env/idf4.4_py3.9_env, clear=False, no_vcs_ignore=False, global=False)

  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/denis/.local/share/virtualenv)

    added seed packages: pip==21.2.3, setuptools==57.4.0, wheel==0.37.0

  activators BashActivator,CShellActivator,FishActivator,PowerShellActivator,PythonActivator

Installing Python packages from /home/denis/espressif/esp-idf/requirements.txt

Looking in indexes: https://pypi.org/simple, https://dl.espressif.com/pypi

Ignoring None: markers 'sys_platform == "win32"' don't match your environment

Requirement already satisfied: setuptools>=21 in /home/denis/.espressif/python_env/idf4.4_py3.9_env/lib/python3.9/site-packages (from -r /home/denis/espressif/esp-idf/requirements.txt (line 4)) (57.4.0)

Collecting click>=7.0

  Using cached https://dl.espressif.com/pypi/click/click-8.0.1-py3-none-any.whl (97 kB)

Collecting pyserial>=3.3

  Using cached https://dl.espressif.com/pypi/pyserial/pyserial-3.5-py2.py3-none-any.whl (90 kB)

Collecting future>=0.15.2

  Using cached https://dl.espressif.com/pypi/future/future-0.18.2-py3-none-any.whl (491 kB)

Collecting cryptography>=2.1.4

  Using cached cryptography-3.4.8-cp36-abi3-manylinux_2_24_x86_64.whl (3.0 MB)

Collecting pyparsing<2.4.0,>=2.0.3

  Using cached https://dl.espressif.com/pypi/pyparsing/pyparsing-2.3.1-py2.py3-none-any.whl (61 kB)

Collecting pyelftools>=0.22

  Using cached https://dl.espressif.com/pypi/pyelftools/pyelftools-0.27-py2.py3-none-any.whl (151 kB)

Collecting gdbgui==0.13.2.0

  Using cached https://dl.espressif.com/pypi/gdbgui/gdbgui-0.13.2.0-py3-none-any.whl (878 kB)

Collecting pygdbmi<=0.9.0.2

  Using cached https://dl.espressif.com/pypi/pygdbmi/pygdbmi-0.9.0.2-py3-none-any.whl (16 kB)

Collecting python-socketio<5

  Using cached python_socketio-4.6.1-py2.py3-none-any.whl (51 kB)

Collecting kconfiglib==13.7.1

  Using cached https://dl.espressif.com/pypi/kconfiglib/kconfiglib-13.7.1-py2.py3-none-any.whl (145 kB)

Collecting reedsolo<=1.5.4,>=1.5.3

  Using cached reedsolo-1.5.4-py3-none-any.whl

Collecting bitstring>=3.1.6

  Using cached https://dl.espressif.com/pypi/bitstring/bitstring-3.1.9-py3-none-any.whl (38 kB)

Collecting ecdsa>=0.16.0

  Using cached https://dl.espressif.com/pypi/ecdsa/ecdsa-0.17.0-py2.py3-none-any.whl (119 kB)

Collecting construct==2.10.54

  Using cached https://dl.espressif.com/pypi/construct/construct-2.10.54-py3-none-any.whl (57 kB)

Collecting Flask-Compress<2.0,>=1.4.0

  Using cached Flask_Compress-1.10.1-py3-none-any.whl (7.9 kB)

Collecting Flask<1.0,>=0.12.2

  Using cached https://dl.espressif.com/pypi/flask/Flask-0.12.5-py2.py3-none-any.whl (81 kB)

Collecting gevent<2.0,>=1.2.2

  Using cached https://dl.espressif.com/pypi/gevent/gevent-1.5.0-cp39-cp39-linux_x86_64.whl (4.7 MB)

Collecting Pygments<3.0,>=2.2.0

  Using cached Pygments-2.10.0-py3-none-any.whl (1.0 MB)

Collecting Flask-SocketIO<3.0,>=2.9

  Using cached Flask_SocketIO-2.9.6-py2.py3-none-any.whl (16 kB)

Collecting cffi>=1.12

  Using cached cffi-1.14.6-cp39-cp39-manylinux1_x86_64.whl (405 kB)

Collecting six>=1.9.0

  Using cached https://dl.espressif.com/pypi/six/six-1.16.0-py2.py3-none-any.whl (11 kB)

Collecting python-engineio<4,>=3.13.0

  Using cached python_engineio-3.14.2-py2.py3-none-any.whl (51 kB)

Collecting pycparser

  Using cached https://dl.espressif.com/pypi/pycparser/pycparser-2.20-py2.py3-none-any.whl (112 kB)

Collecting itsdangerous>=0.21

  Using cached https://dl.espressif.com/pypi/itsdangerous/itsdangerous-2.0.1-py3-none-any.whl (18 kB)

Collecting Jinja2>=2.4

  Using cached https://dl.espressif.com/pypi/jinja2/Jinja2-3.0.1-py3-none-any.whl (133 kB)

Collecting Werkzeug<1.0,>=0.7

  Using cached https://dl.espressif.com/pypi/werkzeug/Werkzeug-0.16.1-py2.py3-none-any.whl (327 kB)

Collecting brotli

  Using cached https://dl.espressif.com/pypi/brotli/Brotli-1.0.9-cp39-cp39-manylinux1_x86_64.whl (357 kB)

Collecting greenlet>=0.4.14

  Using cached greenlet-1.1.1-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (147 kB)

Collecting MarkupSafe>=2.0

  Using cached MarkupSafe-2.0.1-cp39-cp39-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_12_x86_64.manylinux2010_x86_64.whl (30 kB)

Installing collected packages: six, MarkupSafe, Werkzeug, python-engineio, Jinja2, itsdangerous, click, python-socketio, pycparser, greenlet, Flask, brotli, Pygments, pygdbmi, gevent, Flask-SocketIO, Flask-Compress, cffi, reedsolo, pyserial, pyparsing, pyelftools, kconfiglib, gdbgui, future, ecdsa, cryptography, construct, bitstring

Successfully installed Flask-0.12.5 Flask-Compress-1.10.1 Flask-SocketIO-2.9.6 Jinja2-3.0.1 MarkupSafe-2.0.1 Pygments-2.10.0 Werkzeug-0.16.1 bitstring-3.1.9 brotli-1.0.9 cffi-1.14.6 click-8.0.1 construct-2.10.54 cryptography-3.4.8 ecdsa-0.17.0 future-0.18.2 gdbgui-0.13.2.0 gevent-1.5.0 greenlet-1.1.1 itsdangerous-2.0.1 kconfiglib-13.7.1 pycparser-2.20 pyelftools-0.27 pygdbmi-0.9.0.2 pyparsing-2.3.1 pyserial-3.5 python-engineio-3.14.2 python-socketio-4.6.1 reedsolo-1.5.4 six-1.16.0

WARNING: You are using pip version 21.2.3; however, version 21.2.4 is available.

You should consider upgrading via the '/home/denis/.espressif/python_env/idf4.4_py3.9_env/bin/python -m pip install --upgrade pip' command.

All done! You can now run:



  . ./export.sh
</details>


Предлагается обновить python virtual environment установленного в инструментарии **esp-idf**. Обновляем:
```console 
~/.espressif/python_env/idf4.4_py3.9_env/bin/python -m pip install --upgrade pip
```

В принципе, уже можно экспортировать переменные окружения **esp-idf**, чтобы сделать текущий терминал рабочей средой для сборки, прошивки и отладки проектов **esp-idf**. Для этого нужно вызвать 
```console
. ./export.sh
```

Точка перед запуском скрипта нужна, потому, что разрешения у него 0644.

5. <div id="idfexp"></div>Сделаем текущий терминал, средой разработки **esp-idf**. Это можно сделать двумя путями
    1. Первый, самый простой — просто вызвать скрипт экспорта переменных окружения **esp-idf**:
```. ~/espressif/esp-idf/export.sh```

<details>
<summary>Настройка терминала для сборки и прошивки проектов esp-idf</summary>
~/espressif/esp-idf  ‹master› $ idfexp

Detecting the Python interpreter

Checking "python" ...

Python 3.9.6

"python" has been detected

Adding ESP-IDF tools to PATH...

Using Python interpreter in /home/denis/.espressif/python_env/idf4.4_py3.9_env/bin/python

Checking if Python packages are up to date...

Python requirements from /home/denis/espressif/esp-idf/requirements.txt are satisfied.

Updated PATH variable:

  /home/denis/espressif/esp-idf/components/esptool_py/esptool:/home/denis/espressif/esp-idf/components/espcoredump:/home/denis/espressif/esp-idf/components/partition_table:/home/denis/espressif/esp-idf/components/app_update:/home/denis/espressif/esp-idf/components/esptool_py/esptool:/home/denis/espressif/esp-idf/components/espcoredump:/home/denis/espressif/esp-idf/components/partition_table:/home/denis/espressif/esp-idf/components/app_update:/home/denis/.espressif/tools/xtensa-esp32-elf/esp-2021r1-8.4.0/xtensa-esp32-elf/bin:/home/denis/.espressif/tools/xtensa-esp32s2-elf/esp-2021r1-8.4.0/xtensa-esp32s2-elf/bin:/home/denis/.espressif/tools/xtensa-esp32s3-elf/esp-2021r1-8.4.0/xtensa-esp32s3-elf/bin:/home/denis/.espressif/tools/riscv32-esp-elf/esp-2021r1-8.4.0/riscv32-esp-elf/bin:/home/denis/.espressif/tools/esp32ulp-elf/2.28.51-esp-20191205/esp32ulp-elf-binutils/bin:/home/denis/.espressif/tools/esp32s2ulp-elf/2.28.51-esp-20191205/esp32s2ulp-elf-binutils/bin:/home/denis/.espressif/tools/openocd-esp32/v0.10.0-esp32-20210721/openocd-esp32/bin:/home/denis/.espressif/python_env/idf4.4_py3.9_env/bin:/home/denis/espressif/esp-idf/tools:/home/denis/.nvm/versions/node/v15.14.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin://home/denis/.local/bin

Done! You can now compile ESP-IDF projects.

Go to the project directory and run:



  idf.py build


</details>

   Точка перед путём к скрипту ```export.sh``` стоит, потому, что разрешения на него из соображений безопасности установлены в *0644*, следовательно, его надо вызывать, как source, а не как исполняемый скрипт. Например, ```. ./export.hs``` или ```source ./export.sh```

<details>
<summary>Переменные окружения esp-idf</summary>
USER=grandfatherpikhto

LOGNAME=grandfatherpikhto

HOME=/home/grandfatherpikhto

PATH=/home/grandfatherpikhto/espressif/esp-idf/components/esptool_py/esptool:/home/grandfatherpikhto/espressif/esp-idf/components/espcoredump:/home/grandfatherpikhto/espressif/esp-idf/components/partition_table:/home/grandfatherpikhto/espressif/esp-idf/components/app_update:/home/grandfatherpikhto/espressif/esp-idf/components/esptool_py/esptool:/home/grandfatherpikhto/espressif/esp-idf/components/espcoredump:/home/grandfatherpikhto/espressif/esp-idf/components/partition_table:/home/grandfatherpikhto/espressif/esp-idf/components/app_update:/home/grandfatherpikhto/.espressif/tools/xtensa-esp32-elf/esp-2021r1-8.4.0/xtensa-esp32-elf/bin:/home/grandfatherpikhto/.espressif/tools/xtensa-esp32s2-elf/esp-2021r1-8.4.0/xtensa-esp32s2-elf/bin:/home/grandfatherpikhto/.espressif/tools/xtensa-esp32s3-elf/esp-2021r1-8.4.0/xtensa-esp32s3-elf/bin:/home/grandfatherpikhto/.espressif/tools/riscv32-esp-elf/esp-2021r1-8.4.0/riscv32-esp-elf/bin:/home/grandfatherpikhto/.espressif/tools/esp32ulp-elf/2.28.51-esp-20191205/esp32ulp-elf-binutils/bin:/home/grandfatherpikhto/.espressif/tools/esp32s2ulp-elf/2.28.51-esp-20191205/esp32s2ulp-elf-binutils/bin:/home/grandfatherpikhto/.espressif/tools/openocd-esp32/v0.10.0-esp32-20210721/openocd-esp32/bin:/home/grandfatherpikhto/.espressif/python_env/idf4.4_py3.9_env/bin:/home/grandfatherpikhto/espressif/esp-idf/tools:/home/grandfatherpikhto/.nvm/versions/node/v15.14.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin://home/grandfatherpikhto/.local/bin

SHELL=/usr/bin/zsh

TERM=xterm

XDG_SESSION_ID=37

XDG_RUNTIME_DIR=/run/user/1000

DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus

XDG_SESSION_TYPE=tty

XDG_SESSION_CLASS=user

MOTD_SHOWN=pam

LANG=ru_RU.UTF-8

SSH_CLIENT=10.95.2.67 51735 22

SSH_CONNECTION=10.95.2.67 51735 10.95.2.122 22

SSH_TTY=/dev/pts/1

SHLVL=1

PWD=/home/grandfatherpikhto/espressif/esp-idf

OLDPWD=/home/grandfatherpikhto/espressif

ZSH=/home/grandfatherpikhto/.oh-my-zsh

PAGER=less

LESS=-R

LSCOLORS=Gxfxcxdxbxegedabagacad

LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.webp=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:

NVM_DIR=/home/grandfatherpikhto/.nvm

NVM_CD_FLAGS=-q

NVM_BIN=/home/grandfatherpikhto/.nvm/versions/node/v15.14.0/bin

NVM_INC=/home/grandfatherpikhto/.nvm/versions/node/v15.14.0/include/node

IDF_PATH=/home/grandfatherpikhto/espressif/esp-idf

IDF_TOOLS_PATH=/home/grandfatherpikhto/.espressif

IDF_TOOLS_EXPORT_CMD=/home/grandfatherpikhto/espressif/esp-idf/export.sh

IDF_TOOLS_INSTALL_CMD=/home/grandfatherpikhto/espressif/esp-idf/install.sh

OPENOCD_SCRIPTS=/home/grandfatherpikhto/.espressif/tools/openocd-esp32/v0.10.0-esp32-20210721/openocd-esp32/share/openocd/scripts

IDF_PYTHON_ENV_PATH=/home/grandfatherpikhto/.espressif/python_env/idf4.4_py3.9_env

_=/usr/bin/env
</details>

   Существенны здесь изменения в **Path** ()
   И переменные 

   IDF_PATH=${HOME}/espressif/esp-idf — Путь к фреймворку **esp-idf**

   IDF_TOOLS_PATH=${HOME}/.espressif — Путь к инструментарию (toolchain), необходимому для сборки проектов **esp-idf**

   IDF_TOOLS_EXPORT_CMD=${HOME}/espressif/esp-idf/export.sh — Скрипт экспорта переменных окружения **esp-idf**

   IDF_TOOLS_INSTALL_CMD=${HOME}/espressif/esp-idf/install.sh — Скрипт для установки инструментария фреймворка **esp-idf**

   2. Второй, немного более сложный. Гораздо короче вызвать алиас ```idfexp``` из любого пути в терминале, чем каждый раз набирать ```~/espressif/esp-idf/install.sh```. Для этого нужно, скажем, создать файл конфигурации переменных окружения и для создания алиаса ```idfexp```. Проверить работу ```~/.idfrc``` можно при помощи команды ```source ~/.idfrc```. Cкрипт ```~/.idfrc``` создаёт переменные окружения *ESP_IDF* — путь к фреймворкам *esp-idf*, *esp-adf*, *esp-mdf* (ADF, MDF создаются, если эти фреймворки установлены). Файл можно взять [отсюда](./files/.idfrc) или просто скопировать из текста ниже:


```console
# Скрипт ~/.idfrc для экспорта переменных окружения esp-idf, esp-adf, esp-mdf
# и создания алиаса idfexp
#
ESP_PATH="$HOME/espressif"
IDF_TOOLS_PATH="$HOME/.espressif"

if [ -d "$ESP_PATH/esp-idf" ] ; then
	IDF_PATH="$ESP_PATH/esp-idf"
	export IDF_PATH
	if [ -f "$IDF_PATH/export.sh" ] ; then
		alias idfexp="\. $IDF_PATH/export.sh"
	fi
fi

if [ -f "$ESP_PATH/esp-adf" ] ; then
	ADF_PATH="$ESP_PATH/esp-adf"
	export ADF_PATH
fi

if [ -f "$ESP_PATH/esp-mdf" ] ; then
	MDF_PATH="$ESP_PATH/esp-mdf"
	export MDF_PATH
fi

if [ -d "$IDF_TOOLS_PATH" ] ; then
	export IDF_TOOLS_PATH
fi
```

   3. Подключаем автозагрузку либо в ```.profile```:

```
if [ -f "$HOME/.idfrc" ] ; then 
  source "$HOME/.idfrc"
fi
```

Или тот же код в ```.bashrc```

Или в ```.idfrc```, если у вас установлен ```zsh```

```[ -s "$HOME/.zshrc" ] && \. "$HOME/.idfrc"```


6. <div id="helloworld"></div>Проверяем работоспособность фреймворка и рабочего окружения:
   1. Скопируем из примеров, самый простой — hello_world (кстати, он служит основным шаблоном для создания проектов). Примеры находятся в ```~/espressif/esp-idf/examples```. Стартовые примеры, в  ```~/espressif/esp-idf/examples/get-started```. Нам нужен ```~/espressif/esp-idf/examples/get-started/hello_world```:
   2. Создаём каталог для программ esp и копируем туда ```hello_world``` и пытаемся его собрать:

```console
mkdir ~/Lessons/
mkdir ~/Lessons/esp32/
mkdir ~/Lessons/esp32/lesson01
cp -r ~/espressif/esp-idf/examples/get-started/hello_world ~/Lessons/esp32/lesson01
cd ~/Lessons/esp32/lesson01/hello_world
idf.py build
```

Если сборка прошла успешно, увидим что-то вроде 
<details>
<summary>журнала сборки проекта «Hello World!»</summary>
~/Lessons/esp32/lesson01/hello_world  $ idfexp

Detecting the Python interpreter

Checking "python" ...

Python 3.9.6

"python" has been detected

Adding ESP-IDF tools to PATH...

Using Python interpreter in /home/denis/.espressif/python_env/idf4.4_py3.9_env/bin/python

Checking if Python packages are up to date...

Python requirements from /home/denis/espressif/esp-idf/requirements.txt are satisfied.

Updated PATH variable:

  /home/denis/espressif/esp-idf/components/esptool_py/esptool:/home/denis/espressif/esp-idf/components/espcoredump:/home/denis/espressif/esp-idf/components/partition_table:/home/denis/espressif/esp-idf/components/app_update:/home/denis/.espressif/tools/xtensa-esp32-elf/esp-2021r1-8.4.0/xtensa-esp32-elf/bin:/home/denis/.espressif/tools/xtensa-esp32s2-elf/esp-2021r1-8.4.0/xtensa-esp32s2-elf/bin:/home/denis/.espressif/tools/xtensa-esp32s3-elf/esp-2021r1-8.4.0/xtensa-esp32s3-elf/bin:/home/denis/.espressif/tools/riscv32-esp-elf/esp-2021r1-8.4.0/riscv32-esp-elf/bin:/home/denis/.espressif/tools/esp32ulp-elf/2.28.51-esp-20191205/esp32ulp-elf-binutils/bin:/home/denis/.espressif/tools/esp32s2ulp-elf/2.28.51-esp-20191205/esp32s2ulp-elf-binutils/bin:/home/denis/.espressif/tools/openocd-esp32/v0.10.0-esp32-20210721/openocd-esp32/bin:/home/denis/.espressif/python_env/idf4.4_py3.9_env/bin:/home/denis/espressif/esp-idf/tools:/home/denis/.nvm/versions/node/v15.14.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin://home/denis/.local/bin

Done! You can now compile ESP-IDF projects.

Go to the project directory and run:



  idf.py build

</details>

   3. ```idf.py build```. ```idf.py``` — это инструмент управления сборкой esp32 проектов из командной строки. Подробнее: ```idf.py --help```:

<details>
<summary>idf.py --help</summary>
~/Lessons/esp32/lesson01/hello_world  $ idf.py --help

Usage: idf.py [OPTIONS] COMMAND1 [ARGS]... [COMMAND2 [ARGS]...]...



  ESP-IDF CLI build management tool. For commands that are not known to idf.py an attempt to execute it as a build system target will be

  made.



Options:

  --version                       Show IDF version and exit.

  --list-targets                  Print list of supported targets and exit.

  -C, --project-dir PATH          Project directory.

  -B, --build-dir PATH            Build directory.

  -w, --cmake-warn-uninitialized / -n, --no-warnings

                                  Enable CMake uninitialized variable warnings for CMake files inside the project directory. (--no-

                                  warnings is now the default, and doesn't need to be specified.) The default value can be set with the

                                  IDF_CMAKE_WARN_UNINITIALIZED environment variable.

  -v, --verbose                   Verbose build output.

  --preview                       Enable IDF features that are still in preview.

  --ccache / --no-ccache          Use ccache in build. Disabled by default. The default value can be set with the IDF_CCACHE_ENABLE

                                  environment variable.

  -G, --generator [Ninja|Unix Makefiles]

                                  CMake generator.

  -D, --define-cache-entry TEXT   Create a cmake cache entry. This option can be used at most once either globally, or for one subcommand.

  -b, --baud INTEGER              Baud rate for flashing. The default value can be set with the ESPBAUD environment variable. This option

                                  can be used at most once either globally, or for one subcommand.

  -p, --port TEXT                 Serial port. The default value can be set with the ESPPORT environment variable. This option can be used

                                  at most once either globally, or for one subcommand.

  --help                          Show this message and exit.



Commands:

  all                    Aliases: build. Build the project.

  app                    Build only the app.

  app-flash              Flash the app only.

  bootloader             Build only bootloader.

  bootloader-flash       Flash bootloader only.

  build-system-targets   Print list of build system targets.

  clean                  Delete build output files from the build directory.

  confserver             Run JSON configuration server.

  create-component       Create a new component.

  create-project         Create a new project.

  docs                   Open web browser with documentation for ESP-IDF

  efuse_common_table     Generate C-source for IDF's eFuse fields.

  efuse_custom_table     Generate C-source for user's eFuse fields.

  encrypted-app-flash    Flash the encrypted app only.

  encrypted-flash        Flash the encrypted project.

  erase_flash            Erase entire flash chip.

  erase_otadata          Erase otadata partition.

  flash                  Flash the project.

  fullclean              Delete the entire build directory contents.

  gdb                    Run the GDB.

  gdbgui                 GDB UI in default browser.

  gdbtui                 GDB TUI mode.

  menuconfig             Run "menuconfig" project configuration tool.

  monitor                Display serial output.

  openocd                Run openocd from current path

  partition_table        Build only partition table.

  partition_table-flash  Flash partition table only.

  post_debug             Utility target to read the output of async debug action and stop them.

  python-clean           Delete generated Python byte code from the IDF directory

  read_otadata           Read otadata partition.

  reconfigure            Re-run CMake.

  set-target             Set the chip target to build.

  show_efuse_table       Print eFuse table.

  size                   Print basic size information about the app.

  size-components        Print per-component size information.

  size-files             Print per-source-file size information.

  uf2                    Generate the UF2 binary with all the binaries included

  uf2-app                Generate an UF2 binary for the application only

</details>

   4. Прошьём процессор при помощи команды ```idf.py flash -p /dev/ttyUSB0```. Порт можно не указывать, правда **idf.py** может начать по очереди опрашивать доступные порты, что удлиннит время работы. Система журналирования фреймворка настолько подробна, что чаще всего нет нужды в отладчике.

<details>
<summary>Журнал прошивки процессора</summary>
~/Lessons/esp32/lesson01/hello_world  $ idf.py flash -p /dev/ttyUSB0

Executing action: flash

Running ninja in directory /home/denis/Lessons/esp32/lesson01/hello_world/build

Executing "ninja flash"...

[1/5] cd /home/denis/Lessons/esp32/lesson01/hello_world/build/esp-idf/esptool_py && /home/denis/.espressif/python_env..._world/build/partition_table/partition-table.bin /home/denis/Lessons/esp32/lesson01/hello_world/build/hello-world.bin

hello-world.bin binary size 0x28eb0 bytes. Smallest app partition is 0x100000 bytes. 0xd7150 bytes (84%) free.

[2/5] Performing build step for 'bootloader'

[1/1] cd /home/denis/Lessons/esp32/lesson01/hello_world/build/bootloader/esp-idf/esptool_py && /home/denis/.espressif/python_env/idf4.4_py3.9_env/bin/python /home/denis/espressif/esp-idf/components/partition_table/check_sizes.py --offset 0x8000 bootloader 0x1000 /home/denis/Lessons/esp32/lesson01/hello_world/build/bootloader/bootloader.bin

Bootloader binary size 0x6330 bytes. 0xcd0 bytes (13%) free.

[2/3] cd /home/denis/espressif/esp-idf/components/esptool_py && /usr/bin/cmake -D IDF_PATH="/home/denis/espressif/esp...essons/esp32/lesson01/hello_world/build" -P /home/denis/espressif/esp-idf/components/esptool_py/run_serial_tool.cmake

esptool.py esp32 -p /dev/ttyUSB0 -b 460800 --before=default_reset --after=hard_reset write_flash --flash_mode dio --flash_freq 40m --flash_size 2MB 0x1000 bootloader/bootloader.bin 0x10000 hello-world.bin 0x8000 partition_table/partition-table.bin

esptool.py v3.2-dev

Serial port /dev/ttyUSB0

Connecting....

Chip is ESP32-D0WDQ6 (revision 1)

Features: WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None

Crystal is 40MHz

MAC: 08:3a:f2:a9:a8:c0

Uploading stub...

Running stub...

Stub running...

Changing baud rate to 460800

Changed.

Configuring flash size...

Flash will be erased from 0x00001000 to 0x00007fff...

Flash will be erased from 0x00010000 to 0x00038fff...

Flash will be erased from 0x00008000 to 0x00008fff...

Compressed 25392 bytes to 15840...

Writing at 0x00001000... (100 %)

Wrote 25392 bytes (15840 compressed) at 0x00001000 in 0.7 seconds (effective 297.4 kbit/s)...

Hash of data verified.

Compressed 167600 bytes to 88662...

Writing at 0x00010000... (16 %)

Writing at 0x0001ae3e... (33 %)

Writing at 0x000205e9... (50 %)

Writing at 0x00025dcf... (66 %)

Writing at 0x0002e3d4... (83 %)

Writing at 0x000367a0... (100 %)

Wrote 167600 bytes (88662 compressed) at 0x00010000 in 2.3 seconds (effective 595.2 kbit/s)...

Hash of data verified.

Compressed 3072 bytes to 103...

Writing at 0x00008000... (100 %)

Wrote 3072 bytes (103 compressed) at 0x00008000 in 0.0 seconds (effective 546.0 kbit/s)...

Hash of data verified.



Leaving...

Hard resetting via RTS pin...

Done

</details>

   5. Осталось запустить журналирование работы процессора:

```console 
idf.py monitor -p /dev/ttyUSB0
```

Детализация отчёта настолько подробная, что зачастую нет никакой необходимости в отладчике.

<details>
<summary>Журнал мониторинга работы программы</summary>
~/Programs/esp32/hello_world  $ idf.py monitor -p /dev/ttyUSB0

Executing action: monitor

Running idf_monitor in directory /home/denis/Programs/esp32/hello_world

Executing "/home/denis/.espressif/python_env/idf4.4_py3.9_env/bin/python /home/denis/espressif/esp-idf/tools/idf_monitor.py -p /dev/ttyUSB0 -b 115200 --toolchain-prefix xtensa-esp32-elf- --target esp32 --revision 0 /home/denis/Programs/esp32/hello_world/build/hello-world.elf -m '/home/denis/.espressif/python_env/idf4.4_py3.9_env/bin/python' '/home/denis/espressif/esp-idf/tools/idf.py'"...

--- idf_monitor on /dev/ttyUSB0 115200 ---

--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---



configsiets Jun  8 2016 00:22:57



rst:0x1 (POWERON_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)

configsip: 0, SPIWP:0xee

clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00

mode:DIO, clock div:2

load:0x3fff0030,len:6724

load:0x40078000,len:14780

load:0x40080400,len:3792

0x40080400: _init at ??:?



entry 0x40080694

I (27) boot: ESP-IDF v4.4-dev-2740-gf65c8249af 2nd stage bootloader

I (27) boot: compile time 15:51:32

I (27) boot: chip revision: 1

I (32) boot_comm: chip revision: 1, min. bootloader chip revision: 0

I (39) boot.esp32: SPI Speed      : 40MHz

I (43) boot.esp32: SPI Mode       : DIO

I (48) boot.esp32: SPI Flash Size : 2MB

I (52) boot: Enabling RNG early entropy source...

I (58) boot: Partition Table:

I (61) boot: ## Label            Usage          Type ST Offset   Length

I (69) boot:  0 nvs              WiFi data        01 02 00009000 00006000

I (76) boot:  1 phy_init         RF data          01 01 0000f000 00001000

I (83) boot:  2 factory          factory app      00 00 00010000 00100000

I (91) boot: End of partition table

I (95) boot_comm: chip revision: 1, min. application chip revision: 0

I (102) esp_image: segment 0: paddr=00010020 vaddr=3f400020 size=07444h ( 29764) map

I (122) esp_image: segment 1: paddr=0001746c vaddr=3ffb0000 size=0236ch (  9068) load

I (125) esp_image: segment 2: paddr=000197e0 vaddr=40080000 size=06838h ( 26680) load

I (139) esp_image: segment 3: paddr=00020020 vaddr=400d0020 size=147f8h ( 83960) map

I (169) esp_image: segment 4: paddr=00034820 vaddr=40086838 size=04650h ( 18000) load

I (177) esp_image: segment 5: paddr=00038e78 vaddr=50000000 size=00010h (    16) load

I (183) boot: Loaded app from partition at offset 0x10000

I (183) boot: Disabling RNG early entropy source...

I (198) cpu_start: Pro cpu up.

I (198) cpu_start: Starting app cpu, entry point is 0x40080fc4

0x40080fc4: call_start_cpu1 at /home/denis/espressif/esp-idf/components/esp_system/port/cpu_start.c:156



I (0) cpu_start: App cpu up.

I (212) cpu_start: Pro cpu start user code

I (212) cpu_start: cpu freq: 160000000

I (212) cpu_start: Application information:

I (217) cpu_start: Project name:     hello-world

I (222) cpu_start: App version:      1

I (227) cpu_start: Compile time:     Aug 25 2021 15:51:27

I (233) cpu_start: ELF file SHA256:  c4d32a004a5bf6b1...

I (239) cpu_start: ESP-IDF:          v4.4-dev-2740-gf65c8249af

I (245) heap_init: Initializing. RAM available for dynamic allocation:

I (252) heap_init: At 3FFAE6E0 len 00001920 (6 KiB): DRAM

I (258) heap_init: At 3FFB2C48 len 0002D3B8 (180 KiB): DRAM

I (265) heap_init: At 3FFE0440 len 00003AE0 (14 KiB): D/IRAM

I (271) heap_init: At 3FFE4350 len 0001BCB0 (111 KiB): D/IRAM

I (278) heap_init: At 4008AE88 len 00015178 (84 KiB): IRAM

I (285) spi_flash: detected chip: generic

I (288) spi_flash: flash io: dio

W (292) spi_flash: Detected size(4096k) larger than the size in the binary image header(2048k). Using the size in the binary image header.

I (306) cpu_start: Starting scheduler on PRO CPU.

I (0) cpu_start: Starting scheduler on APP CPU.

Hello world!

This is esp32 chip with 2 CPU core(s), WiFi/BT/BLE, silicon revision 1, 2MB external flash

Minimum free heap size: 294448 bytes

Restarting in 10 seconds...

Restarting in 9 seconds...

Restarting in 8 seconds...

Restarting in 7 seconds...

Restarting in 6 seconds...

Restarting in 5 seconds...

Restarting in 4 seconds...

Restarting in 3 seconds...

Restarting in 2 seconds...

Restarting in 1 seconds...

Restarting in 0 seconds...

Restarting now.

ets Jun  8 2016 00:22:57



rst:0xc (SW_CPU_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)

configsip: 0, SPIWP:0xee

clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00

mode:DIO, clock div:2

load:0x3fff0030,len:6724

load:0x40078000,len:14780

load:0x40080400,len:3792

0x40080400: _init at ??:?



entry 0x40080694

</details>
Программа устроена очень просто:

```c
/* Hello World Example

   This example code is in the Public Domain (or CC0 licensed, at your option.)

   Unless required by applicable law or agreed to in writing, this
   software is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   CONDITIONS OF ANY KIND, either express or implied.
*/
#include <stdio.h>
#include "sdkconfig.h"
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_system.h"
#include "esp_spi_flash.h"

void app_main(void)
{
    printf("Hello world!\n");

    /* Print chip information */
    esp_chip_info_t chip_info;
    esp_chip_info(&chip_info);
    printf("This is %s chip with %d CPU core(s), WiFi%s%s, ",
            CONFIG_IDF_TARGET,
            chip_info.cores,
            (chip_info.features & CHIP_FEATURE_BT) ? "/BT" : "",
            (chip_info.features & CHIP_FEATURE_BLE) ? "/BLE" : "");

    printf("silicon revision %d, ", chip_info.revision);

    printf("%dMB %s flash\n", spi_flash_get_chip_size() / (1024 * 1024),
            (chip_info.features & CHIP_FEATURE_EMB_FLASH) ? "embedded" : "external");

    printf("Minimum free heap size: %d bytes\n", esp_get_minimum_free_heap_size());

    for (int i = 10; i >= 0; i--) {
        printf("Restarting in %d seconds...\n", i);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
    printf("Restarting now.\n");
    fflush(stdout);
    esp_restart();
}
```
Сначала печатаются штатные отладочные сообщения запуска процессора (это находится вне доступа программы)

Затем, выводится приветствие программы "Hello world!"

Затем функция **esp_chip_info()** читает в переменную chip_info характеристики процессора

Функция **printf()** выводит на печать некоторые характеристики процессора

Начинается обратный отсчёт от 10 до 0

Функция **fflush()** сбрасывает всё, что осталось в буффере на печать

**esp_restart()** перезапускает процессор

Всё снова.

Вечность.

7. <div id="edit"></div>В принципе, можно начать программировать. Скажем, открыть файл при помощи ```vim ./main/hello_world_main.c```. Или ```gedit ./main/hello_world_main.c```... но это совершенно не то, чего хотелось бы. Хотелось бы полноценную IDE (Интегрированную Среду Разработки) потому, что объём кода проектов запросто может достигать нескольких тысяч строк и может содержать больше десятка файлов.
Например, откроем при помощи ```gedit ~/Lessons/esp32/lesson01/hello_world/main/hello_world_main.c``` или ```kedit ~/Lessons/esp32/lesson01/hello_world/main/hello_world_main.c``` и поправим "Hello World!", скажем, на "Привет, EPS32!". Соберём проект, прошьём его и запустим мониторинг:
```console
idf.py build
idf.py flash -p /dev/ttyUSB0
idf.py monitor -p /dev/ttyUSB0
```
Убеждаемся, что вывод журнала поменялся. Выход из консоли журналирования — **Ctrl+]**

[Назад](./README.md)
