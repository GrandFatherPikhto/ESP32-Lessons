1. Мы не можем использовать Windows PowerShell — скрипт ```export.bat``` из пакета **esp-idf** не может изменять переменные окружения, необходимые для работы **idf.py**. Поэтому, все действия производим из «командной строки».

4. Не забываем добавить в **Path** пути к ```git``` и ```python```(На момент написания этой заметки, актуален Python3.9. Может быть установлено и другое окружение). Для этого вызываем из главного меню «Настройки», через поиск находим «Изменение переменных среды текущего пользователя». Находим переменную **Path** и добавляем туда ```C:\Program Files\Git\bin```, ```C:\Program Files\Python39``` 
2. Устанавливаем **git**: https://git-scm.com/download/win
3. Устанавливаем **python**: https://www.python.org/downloads/
5. Запускаем командную строку.


```
cd %USERPROFILE%
mkdir espressif
cd .\espressif
git clone https://github.com/espressif/esp-idf.git --recursive
cd .\esp-idf\
.\install.bat
```

Если всё пошло удачно, видим что-то вроде:

```
C:\Users\grand\espressif\esp-idf>.\install.bat
Installing ESP-IDF tools
WARNING: File C:\Users\grand\.espressif\idf-env.json was not found.
Creating C:\Users\grand\.espressif\idf-env.json
Selected targets are: esp32s3, esp32c3, esp32s2, esp32
Installing tools: xtensa-esp32-elf, xtensa-esp32s2-elf, xtensa-esp32s3-elf, riscv32-esp-elf, esp32ulp-elf, esp32s2ulp-elf, cmake, openocd-esp32, ninja, idf-exe, ccache, dfu-util
Skipping xtensa-esp32-elf@esp-2021r1-8.4.0 (already installed)
Skipping xtensa-esp32s2-elf@esp-2021r1-8.4.0 (already installed)
Skipping xtensa-esp32s3-elf@esp-2021r1-8.4.0 (already installed)
Skipping riscv32-esp-elf@esp-2021r1-8.4.0 (already installed)
Skipping esp32ulp-elf@2.28.51-esp-20191205 (already installed)
Skipping esp32s2ulp-elf@2.28.51-esp-20191205 (already installed)
Skipping cmake@3.20.3 (already installed)
Skipping openocd-esp32@v0.10.0-esp32-20210721 (already installed)
Skipping ninja@1.10.2 (already installed)
Skipping idf-exe@1.0.1 (already installed)
Skipping ccache@4.3 (already installed)
Skipping dfu-util@0.9 (already installed)
Setting up Python environment
Python 3.9.6
pip 21.2.4 from C:\Users\grand\.espressif\python_env\idf4.4_py3.9_env\lib\site-packages\pip (python 3.9)
Installing Python packages from C:\Users\grand\espressif\esp-idf\requirements.txt
...


Successfully built esp-windows-curses
Installing collected packages: esp-windows-curses
  Attempting uninstall: esp-windows-curses
    Found existing installation: esp-windows-curses 0.1
    Uninstalling esp-windows-curses-0.1:
      Successfully uninstalled esp-windows-curses-0.1
Successfully installed esp-windows-curses-0.1
All done! You can now run:
   export.bat
```

Если всё настроено правильно, увидим что-то такое:


```
C:\Users\grand\espressif\esp-idf>.\export.bat
Setting IDF_PATH: C:\Users\grand\espressif\esp-idf

Adding ESP-IDF tools to PATH...
No directories added to PATH:

C:\Users\grand\.espressif\tools\xtensa-esp32-elf\esp-2021r1-8.4.0\xtensa-esp32-elf\bin;C:\Users\grand\.espressif\tools\xtensa-esp32s2-elf\esp-2021r1-8.4.0\xtensa-esp32s2-elf\bin;C:\Users\grand\.espressif\tools\xtensa-esp32s3-elf\esp-2021r1-8.4.0\xtensa-esp32s3-elf\bin;C:\Users\grand\.espressif\tools\riscv32-esp-elf\esp-2021r1-8.4.0\riscv32-esp-elf\bin;C:\Users\grand\.espressif\tools\esp32ulp-elf\2.28.51-esp-20191205\esp32ulp-elf-binutils\bin;C:\Users\grand\.espressif\tools\esp32s2ulp-elf\2.28.51-esp-20191205\esp32s2ulp-elf-binutils\bin;C:\Users\grand\.espressif\tools\cmake\3.20.3\bin;C:\Users\grand\.espressif\tools\openocd-esp32\v0.10.0-esp32-20210721\openocd-esp32\bin;C:\Users\grand\.espressif\tools\idf-exe\1.0.1\;C:\Users\grand\.espressif\tools\ccache\4.3\ccache-4.3-windows-64;C:\Users\grand\.espressif\tools\dfu-util\0.9\dfu-util-0.9-win64;C:\Users\grand\.espressif\python_env\idf4.4_py3.9_env\Scripts;C:\Users\grand\espressif\esp-idf\tools;C:\ActiveTcl\bin;C:\Program Files (x86)\Common Files\Intel\Shared Libraries\redist\intel64\compiler;C:\Program Files\Python39\Scripts\;C:\Program Files\Python39\;C:\Program Files\Common Files\Oracle\Java\javapath;C:\WINDOWS\system32;C:\WINDOWS;C:\WINDOWS\System32\Wbem;C:\WINDOWS\System32\WindowsPowerShell\v1.0\;C:\WINDOWS\System32\OpenSSH\;C:\Program Files\NVIDIA Corporation\NVIDIA NvDLISR;C:\Program Files (x86)\Calibre2\;C:\Program Files\PuTTY\;C:\Program Files\MiKTeX\miktex\bin\x64\;C:\Program Files\nodejs\;C:\ProgramData\chocolatey\bin;C:\Program Files\Polyspace\R2021a\runtime\win64;C:\Program Files\Polyspace\R2021a\bin;C:\Program Files\Polyspace\R2021a\polyspace\bin;C:\Program Files\Microchip\xc8\v2.31\bin;C:\Program Files\Microsoft SQL Server\Client SDK\ODBC\110\Tools\Binn\;C:\Program Files (x86)\Microsoft SQL Server\120\Tools\Binn\;C:\Program Files\Microsoft SQL Server\120\Tools\Binn\;C:\Program Files\Microsoft SQL Server\120\DTS\Binn\;C:\Program Files (x86)\Windows Kits\8.1\Windows Performance Toolkit\;C:\Utils\;C:\Program Files\dotnet\;C:\Program Files\Python39\;C:\Program Files\Notepad++;C:\Program Files\Git\cmd;C:\Users\grand\AppData\Local\Microsoft\WindowsApps;C:\Program Files\Python39;C:\Program Files\Git\bin;;C:\Users\grand\AppData\Local\Programs\Microsoft VS Code\bin

Checking if Python packages are up to date...
Python requirements from C:\Users\grand\espressif\esp-idf\requirements.txt are satisfied.

Done! You can now compile ESP-IDF projects.
Go to the project directory and run:

  idf.py build

```

Если набрать команду ```>set```, мы должны увидеть что-то такое:

```
ALLUSERSPROFILE=C:\ProgramData
APPDATA=C:\Users\grand\AppData\Roaming
ChocolateyInstall=C:\ProgramData\chocolatey
CommonProgramFiles=C:\Program Files\Common Files
CommonProgramFiles(x86)=C:\Program Files (x86)\Common Files
CommonProgramW6432=C:\Program Files\Common Files
COMPUTERNAME=ILLUSION
ComSpec=C:\WINDOWS\system32\cmd.exe
DriverData=C:\Windows\System32\Drivers\DriverData
HOMEDRIVE=C:
HOMEPATH=\Users\grand
IDF_CCACHE_ENABLE=1
IDF_PATH=C:\Users\grand\espressif\esp-idf
IDF_PYTHON_ENV_PATH=C:\Users\grand\.espressif\python_env\idf4.4_py3.9_env
INTEL_DEV_REDIST=C:\Program Files (x86)\Common Files\Intel\Shared Libraries\
KICAD_SYMBOL_DIR=C:\Program Files\KiCad\share\kicad\library
KICAD_TEMPLATE_DIR=C:\Program Files\KiCad\share\kicad\template
KISYS3DMOD=C:\Program Files\KiCad\share\kicad\modules\packages3d
KISYSMOD=C:\Program Files\KiCad\share\kicad\modules
LOCALAPPDATA=C:\Users\grand\AppData\Local
LOGONSERVER=\\ILLUSION
MIC_LD_LIBRARY_PATH=C:\Program Files (x86)\Common Files\Intel\Shared Libraries\compiler\lib\mic
NUMBER_OF_PROCESSORS=24
OneDrive=C:\Users\grand\OneDrive
OneDriveConsumer=C:\Users\grand\OneDrive
OPENOCD_SCRIPTS=C:\Users\grand\.espressif\tools\openocd-esp32\v0.10.0-esp32-20210721\openocd-esp32\share\openocd\scripts
OS=Windows_NT
Path=C:\Users\grand\.espressif\tools\xtensa-esp32-elf\esp-2021r1-8.4.0\xtensa-esp32-elf\bin;C:\Users\grand\.espressif\tools\xtensa-esp32s2-elf\esp-2021r1-8.4.0\xtensa-esp32s2-elf\bin;C:\Users\grand\.espressif\tools\xtensa-esp32s3-elf\esp-2021r1-8.4.0\xtensa-esp32s3-elf\bin;C:\Users\grand\.espressif\tools\riscv32-esp-elf\esp-2021r1-8.4.0\riscv32-esp-elf\bin;C:\Users\grand\.espressif\tools\esp32ulp-elf\2.28.51-esp-20191205\esp32ulp-elf-binutils\bin;C:\Users\grand\.espressif\tools\esp32s2ulp-elf\2.28.51-esp-20191205\esp32s2ulp-elf-binutils\bin;C:\Users\grand\.espressif\tools\cmake\3.20.3\bin;C:\Users\grand\.espressif\tools\openocd-esp32\v0.10.0-esp32-20210721\openocd-esp32\bin;C:\Users\grand\.espressif\tools\idf-exe\1.0.1\;C:\Users\grand\.espressif\tools\ccache\4.3\ccache-4.3-windows-64;C:\Users\grand\.espressif\tools\dfu-util\0.9\dfu-util-0.9-win64;C:\Users\grand\.espressif\python_env\idf4.4_py3.9_env\Scripts;C:\Users\grand\espressif\esp-idf\tools;C:\ActiveTcl\bin;C:\Program Files (x86)\Common Files\Intel\Shared Libraries\redist\intel64\compiler;C:\Program Files\Python39\Scripts\;C:\Program Files\Python39\;C:\Program Files\Common Files\Oracle\Java\javapath;C:\WINDOWS\system32;C:\WINDOWS;C:\WINDOWS\System32\Wbem;C:\WINDOWS\System32\WindowsPowerShell\v1.0\;C:\WINDOWS\System32\OpenSSH\;C:\Program Files\NVIDIA Corporation\NVIDIA NvDLISR;C:\Program Files (x86)\Calibre2\;C:\Program Files\PuTTY\;C:\Program Files\MiKTeX\miktex\bin\x64\;C:\Program Files\nodejs\;C:\ProgramData\chocolatey\bin;C:\Program Files\Polyspace\R2021a\runtime\win64;C:\Program Files\Polyspace\R2021a\bin;C:\Program Files\Polyspace\R2021a\polyspace\bin;C:\Program Files\Microchip\xc8\v2.31\bin;C:\Program Files\Microsoft SQL Server\Client SDK\ODBC\110\Tools\Binn\;C:\Program Files (x86)\Microsoft SQL Server\120\Tools\Binn\;C:\Program Files\Microsoft SQL Server\120\Tools\Binn\;C:\Program Files\Microsoft SQL Server\120\DTS\Binn\;C:\Program Files (x86)\Windows Kits\8.1\Windows Performance Toolkit\;C:\Utils\;C:\Program Files\dotnet\;C:\Program Files\Python39\;C:\Program Files\Notepad++;C:\Program Files\Git\cmd;C:\Users\grand\AppData\Local\Microsoft\WindowsApps;C:\Program Files\Python39;C:\Program Files\Git\bin;;C:\Users\grand\AppData\Local\Programs\Microsoft VS Code\bin
PATHEXT=.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.PY;.PYW
PREFIX=python.exe C:\Users\grand\espressif\esp-idf
PROCESSOR_ARCHITECTURE=AMD64
PROCESSOR_IDENTIFIER=Intel64 Family 6 Model 63 Stepping 2, GenuineIntel
PROCESSOR_LEVEL=6
PROCESSOR_REVISION=3f02
ProgramData=C:\ProgramData
ProgramFiles=C:\Program Files
ProgramFiles(x86)=C:\Program Files (x86)
ProgramW6432=C:\Program Files
PROMPT=$P$G
PSModulePath=C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\;C:\Program Files (x86)\Microsoft SQL Server\120\Tools\PowerShell\Modules\
PUBLIC=C:\Users\Public
SESSIONNAME=Console
SystemDrive=C:
SystemRoot=C:\WINDOWS
TEMP=C:\Users\grand\AppData\Local\Temp
TMP=C:\Users\grand\AppData\Local\Temp
USERDOMAIN=ILLUSION
USERDOMAIN_ROAMINGPROFILE=ILLUSION
USERNAME=grand
USERPROFILE=C:\Users\grand
VBOX_MSI_INSTALL_PATH=C:\Program Files\Oracle\VirtualBox\
windir=C:\WINDOWS
```


Нас интересует всё, что связано с IDF:

```
IDF_CCACHE_ENABLE=1
IDF_PATH=C:\Users\grand\espressif\esp-idf
IDF_PYTHON_ENV_PATH=C:\Users\grand\.espressif\python_env\idf4.4_py3.9_env
PREFIX=python.exe C:\Users\grand\espressif\esp-idf
IDF_CCACHE_ENABLE=1
```

6. После того, как всё установилось, запускаем ```.\export.bat``` и можем запустить для проверки сборку примера **Hello World**:


```
cd .\examples\get-started\hello_world
idf.py build 
idf.py flash 
idf.py monitor
```

Причём, порт, например, ```-p COM3```, можно не указывать. Просто ```idf.py``` будет перебирать все доступные COM-порты, пока не найдёт первый с ответом характерным для ESP32:

```
C:\Users\grand\espressif\esp-idf\examples\get-started\hello_world>idf.py flash
Executing action: flash
Serial port COM7
Connecting...
COM7 failed to connect: Write timeout
Serial port COM6
COM6 failed to connect: could not open port 'COM6': OSError(22, 'Превышен таймаут семафора.', None, 121)
Serial port COM3
Connecting.........
Detecting chip type... ESP32
Running ninja in directory c:\users\grand\espressif\esp-idf\examples\get-started\hello_world\build
Executing "ninja flash"...
[1/5] cmd.exe /C "cd /D C:\Users\grand\espressif\esp-i...xamples/get-started/hello_world/build/hello-world.bin"
hello-world.bin binary size 0x28d70 bytes. Smallest app partition is 0x100000 bytes. 0xd7290 bytes (84%) free.
[2/5] Performing build step for 'bootloader'
[1/1] cmd.exe /C "cd /D C:\Users\grand\espressif\esp-idf\examples\get-started\hello_world\build\bootloader\esp-idf\esptool_py && C:\Users\grand\.espressif\python_env\idf4.4_py3.9_env\Scripts\python.exe C:/Users/grand/espressif/esp-idf/components/partition_table/check_sizes.py --offset 0x8000 bootloader 0x1000 C:/Users/grand/espressif/esp-idf/examples/get-started/hello_world/build/bootloader/bootloader.bin"
Bootloader binary size 0x5ff0 bytes. 0x1010 bytes (17%) free.
[2/3] cmd.exe /C "cd /D C:\Users\grand\espressif\esp-i...f/esp-idf/components/esptool_py/run_serial_tool.cmake"
esptool.py esp32 -p COM3 -b 460800 --before=default_reset --after=hard_reset write_flash --flash_mode dio --flash_freq 40m --flash_size 2MB 0x1000 bootloader/bootloader.bin 0x10000 hello-world.bin 0x8000 partition_table/partition-table.bin
esptool.py v3.2-dev
Serial port COM3
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
Flash will be erased from 0x00001000 to 0x00006fff...
Flash will be erased from 0x00010000 to 0x00038fff...
Flash will be erased from 0x00008000 to 0x00008fff...
Compressed 24560 bytes to 15331...
Writing at 0x00001000... (100 %)
Wrote 24560 bytes (15331 compressed) at 0x00001000 in 0.6 seconds (effective 306.1 kbit/s)...
Hash of data verified.
Compressed 167280 bytes to 88451...
Writing at 0x00010000... (16 %)
Writing at 0x0001ae14... (33 %)
Writing at 0x00020646... (50 %)
Writing at 0x00025e0f... (66 %)
Writing at 0x0002e418... (83 %)
Writing at 0x00036830... (100 %)
Wrote 167280 bytes (88451 compressed) at 0x00010000 in 2.3 seconds (effective 578.9 kbit/s)...
Hash of data verified.
Compressed 3072 bytes to 103...
Writing at 0x00008000... (100 %)
Wrote 3072 bytes (103 compressed) at 0x00008000 in 0.1 seconds (effective 431.9 kbit/s)...
Hash of data verified.

Leaving...
Hard resetting via RTS pin...
Done
```
7. Если всё собралось и прошилось удачно, увидим журналирование примера на экране:


```
I (241) cpu_start: ESP-IDF:          v4.4-dev-2594-ga20df743f1
I (248) heap_init: Initializing. RAM available for dynamic allocation:
I (255) heap_init: At 3FFAE6E0 len 00001920 (6 KiB): DRAM
I (261) heap_init: At 3FFB2C50 len 0002D3B0 (180 KiB): DRAM
I (267) heap_init: At 3FFE0440 len 00003AE0 (14 KiB): D/IRAM
I (273) heap_init: At 3FFE4350 len 0001BCB0 (111 KiB): D/IRAM
I (280) heap_init: At 4008AD80 len 00015280 (84 KiB): IRAM
I (287) spi_flash: detected chip: generic
I (291) spi_flash: flash io: dio
W (295) spi_flash: Detected size(4096k) larger than the size in the binary image header(2048k). Using the size in the binary image header.
I (309) cpu_start: Starting scheduler on PRO CPU.
I (0) cpu_start: Starting scheduler on APP CPU.
Hello world!
This is esp32 chip with 2 CPU core(s), WiFi/BT/BLE, silicon revision 1, 2MB external flash
Minimum free heap size: 294440 bytes
Restarting in 10 seconds...
Restarting in 9 seconds...
Restarting in 8 seconds...
Restarting in 7 seconds...
Restarting in 6 seconds...
Restarting in 5 seconds...
Restarting in 4 seconds...
Restarting in 3 seconds...
```


Выход из монитора **Ctrl+]**



12. **Выход** из мониторинга **Ctrl+]**
