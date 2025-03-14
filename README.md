# Apache-OpenOffice-Improvement
Целью проекта является добавление фичей для российских ГОСТов в Apache OpenOffice.

Репоситорий Apache OpenOffice: https://github.com/apache/openoffice

Далее идет инструкция по сборке билда Apache OpenOffice 4.1 на CentOS 5:
1. Для установки выделите 60 Гб памяти минимум. После установки CentOS 5 укажите:
    - Installation method: `mirror.nsc.liu.se` 
    - 64 bit: `/centos-store/5.11/os/x86_64`

2. Дальше загрузите конфигурационный файл `CentOS-Base.repo` в директорию `/etc/yum.repos.d/CentOS-Base.repo`, вот гайд для замены если файла нет:
>	Разкоментить и прописать эти команды:
>	baseurl=http://vault.centos.org/5.11/os/x86_64/,
>	baseurl=http://vault.centos.org/5.11/updates/x86_64/,
>	baseurl=http://vault.centos.org/5.11/extras/x86_64/
>	
>	Закоментить mirrorlist у первых трёх
>	Изменить в gpgcheck=0

3. `sudo yum update -y`
4. `sudo reboot`

>Наши сборки в сообществе фактически выполняются на виртуальной машине VMware Fusion. Время сборки в основном зависит от количества процессоров и выделенной оперативной памяти; мы используем 8-ядерную виртуальную машину с 24 ГБ памяти.
### Настройка репозиториев и пакетов
5. `cd /`
6.  `wget http://archives.fedoraproject.org/pub/archive/epel/5/i386/epel-release-5-4.noarch.rpm`
7.  `sudo rpm -ivh epel-release-5-4.noarch.rpm`
8.  `sudo yum update -y`
9. `sudo yum install gcc expat-devel openssl-devel autoconf gcc-c++ cups-devel pam-devel java-1.6.0-openjdk-devel rpm-build dpkg fakeroot gperf freetype-devel libX11-devel libXt-devel fontconfig-devel libXrandr-devel bison flex GConf2-devel gnome-vfs2-devel gtk2-devel gstreamer-devel gstreamer-plugins-base-devel mesa-libGLU-devel git ccache`
10. `ccache -M 2G`
### Загрузка Ant
11. `sudo yum install ant17 ant17-apache-regexp`
12. `cd /`
13. Загружаем в корневой каталог через **WinSCP** файл - `apache-ant-1.9.15-bin.tar.bz2`
>	wget http://mirrors.nxnethosting.com/apache//ant/binaries/apache-ant-1.9.15-bin.tar.bz2
14. `tar xvf apache-ant-1.9.15-bin.tar.bz2`
15. `ln -s apache-ant-1.9.15 ant`
### Установка OpenSSL
16. `cd /usr/local/src`
17. Загружаем в каталог `/usr/local/src` через **WinSCP** файл - `openssl-1.0.2a.tar.gz`
>	wget https://www.openssl.org/source/openssl-1.0.2a.tar.gz
18. `tar xzf openssl-1.0.2a.tar.gz`
19. `cd openssl-1.0.2a`
20. `./config -fpic shared; make`
21. `make install`
22. `echo "/usr/local/ssl/lib" >> /etc/ld.so.conf`
23. `ldconfig`
### Установка модулей Perl
24. `cd /usr/local/src`
25. `wget http://www.cpan.org/src/5.0/perl-5.14.4.tar.gz`
26. `tar xvf perl-5.14.4.tar.gz`
27. `cd perl-5.14.4`
28. `export OPENSSL_PREFIX=/usr/local/ssl`
29. `./configure.gnu ; make`
30. `make install`
31. `/usr/local/bin/perl -MCPAN -e shell`
      *[ reply "yes" to all questions to carry out cpan's autoconfiguration ]*
  * cpan[1]> `force install Archive::Zip`
  * cpan[2]> `install XML::Parser LWP::UserAgent Digest::SHA Digest::MD5 LWP::Protocol::https`
  * cpan[3]> `exit`

>Если будет выходить ошибка связанная с *LWP::Protocol::https*, то необходимо повторно прописать `sudo cpan -f LWP::Protocol::https` - эта команда проигнорирует тесты и их результаты!

---
### Загрузка репозитория
32. `sudo mkdir /source`
33. `sudo chown <yourusername> /source`
34. `cd /source`
35. Загружаем в каталог `/source` через **WinSCP** файл - `openoffice-AOO41X.zip`
>	git clone https://github.com/apache/openoffice.git
36. `unzip openoffice-AOO41X.zip`
37. Переименовываем `openoffice-AOO41X` через **WinSCP** в `openoffice`
38. `cd /source/openoffice/main`

### Решение проблем
* #### Отсутствует geo-2.0
>	1. Загружаем в корень через WinSCP файл - glib-2.26.0.tar.gz
>	2. Распаковываем через WinSCP
>	3. Перейти в cd /glib-2.26.0
>	4. Пишем ./configure -> make -> sudo make install
>	5. glib pkg-config --modversion glib-2.0
>	6. gio pkg-config --modversion gio-2.0
* #### Отсутствует junit
>	Загружаем в /root через WinSCP файл - junit-4.13.2.jar (кидать .jar можно куда угодно, главное указать путь при запуске ./configure).
* #### Отсутствует dmake
>	1. Загружаем в корень через WinSCP файл - dmake-4.12.tar.bz2
>	2. Распаковываем через WinSCP
>	3. Перейти в cd /dmake-4.12
>	4. Пишем ./configure -> make -> sudo make install
### Сборка ./configure
39. `autoconf`
40. `./configure --with-dmake-url --with-epm-url --with-junit=/root/junit-4.13.2.jar`
41. `./bootstrap`
