# Apache-OpenOffice-Improvement
Целью проекта является добавление фичей для российских ГОСТов в Apache OpenOffice.

Репоситорий Apache OpenOffice: https://github.com/apache/openoffice

Собственная инструкция по сборке билда Apache OpenOffice 4.1 на Fedora 41:
### **Сборка AOO на Fedora 41**
1. Обновляем систему перед сборкой
```bash
sudo dnf update -y
```

2. Устанавливаем основные инструменты для сборки и указанные в руководстве (с поправкой на Fedora 41, т.е. не устанавливаем пакеты, которые уже есть в более новых версиях, по сравнению с Fedora 19)
```bash
sudo dnf install gcc-c++ ant autoconf bison flex perl gperf libtool nasm python3 cmake java-11-openjdk-devel gtk3-devel gdk-pixbuf2-devel libXt-devel cups-devel dbus-glib-devel librsvg2-devel openldap-devel unixODBC-devel
```

3. Клонируем исходный код OpenOffice с GitHub
```bash
sudo mkdir /source
sudo chown <username> /source
cd /source
git clone https://gitbox.apache.org/repos/asf/openoffice.git
cd openoffice/
git checkout <branch>
вместо <username> пишем наше имя пользователя в линуксе
вместо <branch> пишем ветку, которую будем собирать, в нашем случае AOO42X
```
---
### **Установка DMAKE**
4. Переходим в папку загрузок с помощью команды 
```bash
cd /home/<username>/Загрузки
```
5. Прописываем следующую строчку, получаем репозиторий с `dmake`
```bash
wget https://github.com/jimjag/dmake/archive/v4.13.1/dmake-4.13.1.tar.gz
```
6. Заходим в проводник, в папку загрузки и извлекаем папку с `dmake` из загруженного архива
7. Копируем извлечённую папку в каталог `main` репозитория `openoffice`
8. Переходим в эту скопированную папку с помощью 
```bash
cd /source/openoffice/main/dmake-4.13.1
```
9. Прописать 
```bash
./configure --prefix=/usr/local; make install
```
10. После установки `dmake` запускаем `./configure` из main, процесс будет ругаться на отсутствие некоторых perl модулей и даст инструкцию по их установке, следуем ей, соглашаемся на автонастройку
11. После установки всех библиотек заходим в `source/openoffice/main` и прописываем после этого файл сам создастся
```bash
autoconf
```
---
### **Дополнительная установка библиотек**
```bash
sudo dnf install pam-devel
sudo dnf install perl-XML-Parser
```
12. Пояснение: xml парсер почему-то не установился по встроенной в терминал инструкции, поэтому качаем его отдельно `pam-devel` нужна обязательно, устанавливать её можно было ещё раньше
13. `LWP::Protocol::https` (который не устанавливается вместе с perl), можно установить командой ниже:
```bash
sudo dnf install perl-LWP-Protocol-https
```
14. Чтобы проверить работоспособность модуля можно ввести
```bash
perl -MLWP::Protocol::https -e 'print "Module installed\n"'
```

15. По аналогии с DMAKE устанавливаем и EPM [с оф гайда на сайте](https://wiki.openoffice.org/wiki/Documentation/Building_Guide_AOO/Step_by_step_Linux#CentOS_7_and_Fedora_19_for_AOO_4.2.x_and_later)
```bash
wget https://github.com/jimjag/epm/archive/v5.0.0/epm-5.0.0.tar.gz
cd epm-5.0.0
./configure --prefix=/usr/local ; make install
```

16. После устанавливаем файл `unowinreg.dll`
```bash
wget -O external/unowinreg/unowinreg.dll https://www.openoffice.org/tools/unowinreg_prebuild/680/unowinreg.dll
```

17. Устанавливаем дополнительные библиотеки
```bash
sudo dnf install GConf2-devel
sudo dnf install gnome-vfs2-devel
sudo dnf install gtk2-devel
sudo dnf install rpm-build
sudo dnf install rpm-build
```

---

18. Скачиваем `hamcrest-core` в `cd /usr/share/java`
```bash
sudo wget https://repo1.maven.org/maven2/org/hamcrest/hamcrest-core/1.3/hamcrest-core-1.3.jar
```

---

19. Для сборки OpenOffice нам нужна java именно версии 1.8.0, устанавливаем её командой
```bash
sudo dnf install java-1.8.0-openjdk-devel
```
20. Затем пишем команду
```bash
sudo update-alternatives --config java
```
21. Там по подсказке выбираем нужную нам версию java (просто вводим её номер). Затем, при запуске конфигурации прописываем следующее
```bash
--with-jdk-home=/usr/lib/jvm/java-1.8.0-openjdk
```
22. Переходим в 
```bash
cd /source/openoffice/main
```
23. Так как GTK у нас почему-то не встаёт, не смотря на наличие обеих версии, будем собирать без него, для этого поставим необходимую настройку при запуске конфигурации `disable-gtk`, получится следующая команда
```bash
./configure --disable-gtk --with-jdk-home=/usr/lib/jvm/java-1.8.0-openjdk
```

---

24. При выполнении `./bootstrap` происходит ошибка скачивания `expat`, может быть ссылка устарела, поэтому качаем вручную, вот архив `...`. Далее мы кидаем этот архив в папку `source/openoffice/main/ext_sources` но данный файл нужно переименовать в  
```bash
5e9974d422dc4b157f300568ad28ebf6-expat-2.5.0.tar.bz2
```
25. Сделать это у нас не получится, так как в этом каталоге уже есть такой файл (как я понял это битый архив, который не смог скачаться), поэтому мы сначала находим и удаляем уже существующий, а потом переименовываем уже наш `expat` архив, после этого можно прописать 
```bash
md5sum /source/openoffice/ext_sources/5e9974d422dc4b157f300568ad28ebf6-expat-2.5.0.tar.bz2
```
25. Если всё будет без ошибок, значит сработало, можно дальше продолжать выполнение ./bootstrap

---

24. После успешного выполнения ./configure поочерёдно выполняем следующие команды
```bash
./bootstrap
source *.Set.sh
cd instsetoo_native
build --all
```
