# Урок 1. Установка и настройка окружения esp-idf под Linux & Windows.
1. Espressif
    1. DIY
    2. Сравненение с другими процессорами 
    3. Полезные ссылки
2. Linux
    1. Установка среды разработки на Ubuntu 21.04 
    2. Установка и настройка VSCode и плагина esp-idf
    3. Установка и настройка Eclipse и плагина esp-idf
3. Windows 10
    1. Установка среды разработки на Windows
    2. Установка и настройка VSCode и плагина esp-idf
    3. Установка и настройка Eclipse и плагина esp-idf

## Espressif
### Сравнение
#### ESP32
Основные причины использования ESP32
1. 2.4 мГц
2. WiFi
3. Bluetooth/BLE
4. По соотношению цена/производительность, STM гонку проигрывает. Цена на модуль от 50 руб и на макетную плату от 250 руб
#### STM
1. Линейка STM *гораздо* лучше документирована
2. По производительности STM выигрывает. Так, STM32H750 работает на частоте 400 мГц
3. Поддержка компилятора STM сделана *значительно* лучше. Так, можно использовать инструкции DSP отдельно от C-кода.
### Полезные ссылки
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
12. https://0x04.net/~mwk/doc/xtensa.pdf Xtensa® Instruction Set Architecture (ISA) Reference Manual
### Установка и настройка esp-idf
#### На Linux
##### esp-idf
**Задача**: Установить *esp-idf*, *VSCode*, *Eclipse*, так, чтобы программирование из консоли, *VSCode* и *Eclipse* осуществлялись с одного дистрибутива *esp-idf*
1. Установить git 
```sudo apt install git-all```, ```sudo snap install git```, ```sudo dnf install git-all``` и так далее.
2. Проверить, установлен ли python. Обычно, если python установлен, то 3.x версия, которая вызывается командой ```python3```. Если нельзя вызвать ```python```, cделать мягкую ссылку ```ln -sf /usr/bin/python3.9 ~/.local/bin/python```
3. Создать каталог *~/espressif*, 

```mkdir ~/espressif```

перейти в него и скачать репозиторий *esp-idf*: 

```git clone https://github.com/espressif/esp-idf.git --recursive```

4. Перейти в каталог *~/espressif/esp-idf*:

```cd ~/espressif/esp-idf```

и запустить установку набора инструментов -- tollchain

```./install.sh```

Если всё правильно установлено, начнётся скачивание инструментария.

5. После завершения скачивания, запустить настройку программного окружения (для каждого открытого терминала запускается отдельно)

```. ./export.hs```

Точка перед путём к скрипту ```export.sh``` стоит, потому, что разрешения на него из соображений безопасности установлены в *0644*, следовательно, его надо вызывать, как source, а не как исполняемый скрипт. Например, ```. ./export.hs``` или ```source ./export.sh```

6. Если видим что-то вроде 

```
Detecting the Python interpreter
Checking "python" ...
Python 3.9.5
"python" has been detected
Adding ESP-IDF tools to PATH...
Not using an unsupported version of tool cmake found in PATH: 3.21.1.
Not using an unsupported version of tool ninja found in PATH: 1.10.1.
Using Python interpreter in ~/.espressif/python_env/idf4.4_py3.9_env/bin/python
Checking if Python packages are up to date...
Python requirements from ~/espressif/esp-idf/requirements.txt are satisfied.
Added the following directories to PATH:
  ~/espressif/esp-idf/components/esptool_py/esptool
  ~/espressif/esp-idf/components/espcoredump
  ~/espressif/esp-idf/components/partition_table
  ~/espressif/esp-idf/components/app_update
  ~/.espressif/tools/xtensa-esp32-elf/esp-2021r1-8.4.0/xtensa-esp32-elf/bin
  ~/.espressif/tools/xtensa-esp32s2-elf/esp-2021r1-8.4.0/xtensa-esp32s2-elf/bin
  ~/.espressif/tools/xtensa-esp32s3-elf/esp-2021r1-8.4.0/xtensa-esp32s3-elf/bin
  ~/.espressif/tools/riscv32-esp-elf/esp-2021r1-8.4.0/riscv32-esp-elf/bin
  ~/.espressif/tools/esp32ulp-elf/2.28.51-esp-20191205/esp32ulp-elf-binutils/bin
  ~/.espressif/tools/esp32s2ulp-elf/2.28.51-esp-20191205/esp32s2ulp-elf-binutils/bin
  ~/.espressif/tools/cmake/3.20.3/bin
  ~/.espressif/tools/openocd-esp32/v0.10.0-esp32-20210721/openocd-esp32/bin
  ~/.espressif/tools/ninja/1.10.2/
  ~/.espressif/python_env/idf4.4_py3.9_env/bin
  ~/espressif/esp-idf/tools
Done! You can now compile ESP-IDF projects.
Go to the project directory and run:

  idf.py build

```

7. Проверяем работоспособность фреймворка и рабочего окружения:
    1. Переходим в каталог примеров: ```cd ~/espressif/esp-idf/examples/get-started/hello-world```
    2. Вызываем ```idf.py build```. ```idf.py``` — это инструмент управления сборкой esp32 проектов из командной строки
    3. Если всё удачно собралось, залогиниться под суперюзером: ```sudo su```
    4. Скопировать файл ```cp ./40-dfuse.rule /etc/udev/rules.d/``` или создать файл ```cd /etc/udev/rules.d && vim 40-dfuse.rule``` и скопировать туда правила доступа для устройств файл c правилами  ```/etc/udev/rules.d/40-dfuse.rule```
    5. Перезапустить ```sudo systemctl restart udev.service```
    6. Перечитать правила: ```sudo udevadm control --reload-rules```

Файл ```/etc/udev/rules.d/40-dfuse```

```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="303a", ATTRS{idProduct}=="00??", GROUP="plugdev", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="60??", MODE="0666", ENV{ID_MM_DEVICE_IGNORE}="1"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", MODE="0666", ENV{ID_MM_DEVICE_IGNORE}="1"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="067b", ATTRS{idProduct}=="2303", MODE="0666", ENV{ID_MM_DEVICE_IGNORE}="1"
```


8. Не обязательно, но можно создать файл конфигурации переменных окружения и для создания алиаса ```idfexp``` — настройка текущего терминала для работы утилиты ```idf.py```. Проверить работу файла можно при помощи команды ```source ~/.idfrc```. Этот скрипт создаёт переменные окружения *ESP_IDF* — путь к фреймворкам *esp-idf*, *esp-adf*, *esp-mdf* (ADF, MDF создаются, если эти фреймворки установлены)
```
# Скрипт ~/.idfrc для "подёма" окружения esp-idf, esp-adf, esp-mdf
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

9. Подключить автозагрузку либо в ```.profile```:

```
if [ -f "$HOME/.idfrc" ] ; then 
  source "$HOME/.idfrc"
fi
```

Или тот же код в ```.bashrc```

Или в ```.idfrc```, если у вас установлен ```zsh```

```[ -s "$HOME/.zshrc" ] && \. "$HOME/.idfrc"```

10. Если всё настроено, снова переходим в каталог ```~/espressif/esp-idf/examples/``` и запускаем сначала ```~/espressif/esp-idf/export.sh``` или если настроен алиас ```idfexp```. Далее, ```idf.py flash -p /dev/ttyUSB0``` (или другой порт, на котором находится ваше устройство). Если всё прошло удачно, увидим проценты загрузки прошивки на борт процессора.
11. Запускаем ```idf.py monitor -p /dev/ttyUSB0```. Строго говоря, если к компьютеру подключено только одно устройство, порт можно не указывать. Правда, **idf.py** может начать по очереди опрашивать доступные порты, что удлиннит время работы.
12. Выход из команды монитора **Ctrl+]**

##### VSCode

1. Скачать *.tar.gz* архив *VSCode* с линка https://code.visualstudio.com/download
2. Создать каталог *~/IDE/*, скопировать туда архив и развернуть его ```tar -zxvf code-stable-x64-xxxxxxxxxxxx.tar.gz```
3. Создать мягкий линк ```ln -sf ~\IDE\VSCode-Linux-x64\bin\code ~\.local\bin``` именно из каталога *bin*, потому, что именно ```.../bin/code```, «отпускает» консоль после запуска.
4. Если Вы используете среду *GNOME*, заменить *${HOME}* в *vscode.desktop* и копировать ```cp vscode.desktop ~/.local/share/applications``` или в ```/usr/share/applications```, если Вы хотите чтобы Ваш *VSCode* был бы виден глобально.



```
[Desktop Entry]
Type=Application
Exec="${HOME}/IDE/VSCode-linux-x64/bin/code" %F
Name=VSCode
GenericName=The Microsoft IDE for C/C++, Javascript, Python e.t.c. development.
Icon=${HOME}/IDE/VSCode-linux-x64/resources/app/resources/linux/code.png
StartupWMClass=code
Terminal=false
Categories=Development;IDE;C/C++;Python;Java;Java Script;Node;NodeJS
MimeType=text/x-c++src;text/x-c++hdr;text/x-xsrc;application/x-designer;

```



5. Установить плагин *esp-idf* в *VSCode*
6. Запустить ```code .``` из консоли, где был запущен скрипт ```. ~/espressif/esp-idf/export.sh```, чтобы плагин смог «подхватить» переменные окружения и узнать, где установлен фреймворк и набор инструментов с *venv* python.
7. При помощи клавиши **F1** вызвать меню команд *VSCode* и найти **\>ESP-IDF: Настроить расширение EPS-IDF**
8. Если появился экран с возможностью выбора 

«USE EXISTING SETUP

We have found ESP-IDF version: 4.4 @/home/grandfatherpikhto/espressif/esp-idf and ESP-IDF tools in @ /home/grandfatherpikhto/.espressif. Click here to use them.»

Всё в порядке. Выбираем «USE EXISTING SETUP»

9. Если установка прошла удачно, жмём **Ctrl + E N**. Если нет, **F1 -> \>ESP-IDF: Команда врача** вставляем содержимо в новый файл ищем, что не так.
10. Запускаем **Ctrl+E N** создание нового проекта. Выбираем шаблон приложения (например, «template-app» — Hello World).
11. Если всё прошло удачно (будьте внимательны, каталог, в котором создаётся новый проект должен существовать, иначе, будет сообщение об ошибке) соглашаемся открыть новое окно.
12. Пытаемся собрать проект. Иногда, после первого запуска случается так, что проект не собирается из-за того, что не хватает привилегий. С чем это связано, не знаю, но надо просто перезапустить *VSCode*
13. Запускаем сборку, прошивку и мониторинг проекта — **Ctrl+E D**. Обратите внимание: сверху может появиться выбор способа прошивки проекта: **UART/JTAG**. Выбираем **UART**.
14. Если всё прошло удачно, запустится мониторинг проекта с отображений журнала сообщений от процессора **ESP32**, передаваемых через **USB**.
##### Eclipse
1. Скачиваем установщик *Eclipse* с https://www.eclipse.org/downloads/. Важно! Должна быть установлена *Oracle Java*: https://www.oracle.com/ru/java/technologies/javase-jre8-downloads.html
2. Кладём установщик в каталог ```~/IDE/eclipse```. Его можно будет запускать повторно и в средах *GNOME*, *KDE*, *XFCE*, *MATE*, установщик создаст нужные скрипты запуска.
3. Если справа сверху на иконке «бургера» появится восклицательный знак, обновляем установщик, чтобы он мог установить последние версии сборок *Eclipse*
4. Устанавливаем *Eclipse developing C/C++*
5. После запуска установленного *Eclipse*, идём в *Help->Market place*. В строке поиска выбираем **esp-idf**. Устанавливаем. Перезапускаем *Eclipse*. 
6. В меню *Help*, выбираем *Help->Download and configure ESP-IDF*. Далее, выбираем *Use an existing ESP-IDF directory from file system*. Указываем путь к установленному пакету **esp-idf**. В данном случае, это ```~/espressif/esp-idf```.
7. Появится окошко с путями к **esp-idf**, **git**, **python**. Если какой-либо путь не «подхватился», добавьте его вручную, используя в консоли команду ```which```, например, ```which git```, ```which python``` и т.д.
8. После перезапуска выбираем *File->Project*. В диалоговом окне выбираем *Espressif->Espressif IDF Project*. *Next*. Если далее выбрать *«Next»*, появится окно с выбором шаблонного проекта из списка ```~/espressif/esp-idf/examples```. Самые простые проекты — «hello world», «blink», «sample_project». Можете выбрать любой.
9. Выбираем в поле «on», «Esp32». Нажимаем на шестерёнку и выбираем порт USB. Обычно, это ```/dev/ttyUSB0```. Нажимаем «Run». Если все прошло удачно, проект будет собран и прошит.
10. Нажимаем на иконку с монитором в верхнем тулбаре, указываем кодировку *UTF-8*, порт ```/dev/ttyUSB0```. Должен запуститься *IDF-monitor*. К сожалению, выход по **Ctrl+]** не работает. Когда в мониторе не будет необходимости, его надо просто закрыть.
#### На Windows
##### esp-idf
##### VSCode
##### Eclipse
