План работ по улучшению инструмента

- Добавить анализ исполняемых в данный момент скриптов на perl/php, в случае CGI это просто, оно в environ: GATEWAY_INTERFACE=CGI/1.1, а вот что-то иное как php fastcgi анриал
- Добавить парсер fdinfo для определения режима, в котором открыт файл: http://man7.org/linux/man-pages/man5/proc.5.html
- Сортировать дескрипторы по типу протокола - v4/v6
- Проверяем спиcки доверенных ключей SSH
- Проверяем глобальный системный CRON /etc/crontab
- Проверять root cron не помешает также
- Проверяем Смотрим, с каких IP были входы по SSH: Debian: cat /var/log/auth.log | grep 'Accepted password' 
- См предыдущий пункт Сentos: cat /var/log/secure | grep 'Accepted password for' и cat /var/log/secure | grep 'Accepted publickey'
- Еще не помешает, конечно, извлекать малварь, у которой удален exe
- Добавить проверку размера очередей пофикса  http://www.stableit.ru/2009/06/postfix.html
- Провера rc.local не помешала никому! Исключать пустые строки и комментарии 
- ДОбавить проверки для тщательного аудита процессов httpв и apache, малварь ну очень любит под них прятаться
- Уведомлять о приложениях запущенных из ^/etc, такое уже было много раз
- Анализировать процессы, запущенные без соответствующего символьного пользователя в системе - часто это малварь
- Стоит проверять cwd с точками и пробелами - жди беды!
- Интегрировать clamd - демон сканирования!
- yum install -y clamav clamd; freshclam; /etc/init.c/clamd start; chkconfig clamd on
- ClamAV интеграция: он как на CentOS 5 так и на CentOS 6 вполне актульных версий из Epel
- При запуска симлинка программы, он получает exe именно симлинка, а не самой exe программы, это нужно учитываться
- Как определить, что программа запущена из SUID бинарика? По разнице effective/real?
- check_changed_proc_name включить по умолчанию в случае, если exe и Name отличаются кардинально, тоже самое касается cmdline
- Проанализировать с pstree, работа с процессами без детей намного проще
- Трижды внимательно проверять апача
- Добавить сканер /tmp на предмет бинарных файло
- Can't parse tcp connection line:  522: 00000000:1622 00000000:0000 0A 00000000:00000000 00:00000000 00000000    -1        0 23890959 1 ffff880a5d74c100 99 0 0 10 -1  --- это на овз16
- Добавить проверку lsattr ом всех бинарных файликов
- пожалуй, более корректно парсить вот это дело: /proc/1045280/net/tcp
- Вирусота: https://github.com/creaktive/psf
- У virustotal есть API: https://www.virustotal.com/ru/about/
- Также сделать спец анализатор сигнализирующий просто о процессах с кучей сокетов  (в основном Udp)
- все файлы с ppid apache (httpd) выводить как подозрительные! 
- анализировать  smaps на предмет библитек загруженных в приложение и при этом удаленных с диска
- а как хэшировать точно протрояненые либы??? 
- TODO: добавить проверку at spool:  at 1am tomorrow: они создаются в папке:  /var/spool/cron/atjobs/a000010163c1ac
- Добавить проверки, чтобы родительский процесс был не PHP 
- Сделаь аналог unhide
- Почему-то на vzctl enter 1142 не видит прослушиваемый squid ом 3128й порт
- Добавить отображение, если есть ssh сессии активные из удаленных машин
# tcp        0      0 78.46.12.227:22         5.61.35.75:58492        ESTABLISHED 8652/sshd: andrey [
# tcp        0    848 78.46.12.227:22         60.173.26.146:10697     ESTABLISHED 15051/sshd: [accept
- Генерация сигнатур для ClamAV: http://www.clamav.net/doc/webinars/Webinar-Alain-2009-03-04.pdf
- Как работать с prelink и как получить файл до prelink: https://access.redhat.com/site/solutions/75013

TODO audit mode:
- Схлопывать похожие по basedir пути
- Отображать число "сжатых" форков
- Удалять дублирующиеся file записи, отображать только уникальные

Проверка анонимных прокси:
```bash
macbook-pavel:~ pavel_odintsov$ telnet xx.xx.xx.xx 3128
Trying xxx.155...
Connected to xxx
Escape character is '^]'.
CONNECT ya.ru HTTP/1.1

HTTP/1.0 407 Proxy Authentication Required
Server: squid/3.1.20
```

# Биндинг RPM для Perl
```bash
yum install -y perl-RPM2
use RPM2;
Список пакетов perl -e 'use RPM2;  my $db = RPM2->open_rpm_db();  my $i = $db->find_all_iter();  my @packages_list=();while (my $pkg = $i->next) {  print $pkg->as_nvre, "\n"; push @packages_list, $pkg->as_nvre }'
```
