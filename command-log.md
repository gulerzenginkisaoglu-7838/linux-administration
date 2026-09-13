# Linux-Administration – Praxis-Log (LF09V2)

Dieses Dokument fasst reale Linux-Befehle zusammen, die im Rahmen von LF09V2
(„Netzwerke und Dienste bereitstellen") auf einer Debian-13-VM (Hyper-V,
teils per SSH von Windows aus erreicht) ausgeführt wurden. Die Befehle
wurden aus Screenshots vom 31.08.–03.09.2026 rekonstruiert (verbatim, wo
lesbar; teilweise abgeschnittene Zeilen sind mit `[...]` markiert).

Host: `debian13GUI` (auch als `deb13gui` / `deb13ohnegui` bezeichnet),
IP `10.100.32.122` (auch `10.100.17.102` / `10.100.19.111` in anderen
Sitzungen), Nutzer `student`.

---

## 1. Login & Basis-Setup

```bash
# Erster Login-Versuch – Nutzer war noch nicht in der sudoers-Datei
student@debian13GUI:~$ sudo tasksel
[sudo] Passwort für student:
»student« ist nicht in der sudoers-Datei.

student@debian13GUI:~$ sudo apt update
»student« ist nicht in der sudoers-Datei.
```

Nach Freigabe der sudo-Rechte (außerhalb des Screenshots gelöst):

```bash
student@debian13gui:~$ sudo apt install cmatrix
[...]
Setting up cmatrix (2.0-6) ...
```

SSH-Login von der Windows-Seite aus:

```bash
C:\Users\Student>ssh student@10.100.32.122
The authenticity of host '10.100.32.122 (10.100.32.122)' can't be established.
ED25519 key fingerprint is SHA256:I/90wQWgZoYL5gqo2wehJ+30Hqz/n+JCSGsDqz9Fwic.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.100.32.122' (ED25519) to the list of known hosts.
student@10.100.32.122's password:
Linux debian13GUI 6.12.107+deb13-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.12.107-1 (2026-08-29) x86_64
student@debian13GUI:~$
```

## 2. Paketverwaltung (apt)

```bash
student@debian13GUI:~$ sudo apt update
[sudo] Passwort für student:
Ign:1 cdrom://[Debian GNU/Linux 13.4.0 _Trixie_ ...] trixie InRelease
Fehl:2 cdrom://[...] trixie Release
  Bitte verwenden Sie apt-cdrom, um APT diese CD-ROM bekannt zu machen.

student@debian13GUI:~$ cat /etc/apt/sources.list
deb cdrom:[Debian GNU/Linux 13.4.0 _Trixie_ ...]/ trixie contrib main non-free-firmware
```

CD-ROM-Repo durch echte Debian-Mirrors ersetzt (`nano /etc/apt/sources.list`):

```bash
student@debian13GUI:~$ sudo nano /etc/apt/sources.list
student@debian13GUI:~$ sudo apt update
Holen:1 http://deb.debian.org/debian trixie InRelease [140 kB]
Holen:2 http://security.debian.org/debian-security trixie-security InRelease [43,4 kB]
[...]
Es wurden 29,2 MB in 5 s geholt (5.701 kB/s).
Aktualisierung für 261 Pakete verfügbar.
```

Installierte Pakete: `openssh-server`, `apache2`, `tree`, `cmatrix`, `php`
(inkl. `libapache2-mod-php`, `php8.4-cli`, `php8.4-cgi`), `mariadb-server`.

```bash
student@debian13GUI:~$ sudo apt install openssh-server
openssh-server ist schon die neueste Version (1:10.0p1-7+deb13u4).

student@debian13GUI:~$ apt install apache2
Fehler: Sperrdatei /var/lib/dpkg/lock-frontend konnte nicht geöffnet werden.
Fehler: Erlangen der Sperre für die Dpkg-Oberfläche nicht möglich; sind Sie root?
# -> ohne sudo ausgeführt, daher Lock-Fehler; danach mit sudo wiederholt.

root@debian13GUI:~# apt install php
Installiere: php, php8.4
Fortfahren? [J/n] j
[...]
root@debian13GUI:~# php --version
PHP 8.4.24 (cli) (built: Jul 31 2026 05:11:11) (NTS)
```

## 3. Netzwerkprüfung

```bash
student@debian13GUI:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
    inet 127.0.0.1/8 scope host lo
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    link/ether 00:15:5d:20:4c:03 brd ff:ff:ff:ff:ff:ff
    inet 10.100.32.122/24 brd 10.100.32.255 scope global dynamic noprefixroute eth0
```

## 4. Dienste verwalten (systemctl)

```bash
student@debian13GUI:~$ systemctl status apache2
● apache2.service - The Apache HTTP Server
     Active: active (running) since Wed 2026-09-02 09:22:58 CEST
   Main PID: 3143 (apache2)

student@debian13GUI:~$ service apache2 stop
-bash: service: Kommando nicht gefunden.

student@debian13GUI:~$ sudo systemctl stop apache2
student@debian13GUI:~$ sudo systemctl start apache2

student@debian13GUI:~$ sudo systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Active: active (running) since Tue 2026-09-01 16:06:29 CEST

# root-Wechsel und zurück
student@debian13GUI:~$ su -
Passwort:
root@debian13GUI:~# exit
Abgemeldet
student@debian13GUI:~$
```

## 5. Dateioperationen

```bash
student@debian13GUI:~$ ls
Bilder  Dokumente  Downloads  Musik  Öffentlich  Ordner  Schreibtisch  Videos  Vorlagen  notizen.txt

student@debian13GUI:~$ rm notizen.txt
student@debian13GUI:~$ touch notizen.txt
student@debian13GUI:~$ rm notizen.txt

student@debian13GUI:~$ cd ~
student@debian13GUI:~$ mkdir projekte
student@debian13GUI:~$ touch a.txt b.txt c.txt
student@debian13GUI:~$ ls *.txt
a.txt  b.txt  c.txt  notizen.txt  Welt.txt
student@debian13GUI:~$ mv *.txt projekte/
student@debian13GUI:~$ ls -l projekte
insgesamt 8
-rw-rw-r-- 1 student student  0  2. Sep 14:04 a.txt
-rw-rw-r-- 1 student student  0  2. Sep 14:04 b.txt
-rw-rw-r-- 1 student student  0  2. Sep 14:04 c.txt
-rw-rw-r-- 1 student student 14  2. Sep 11:12 notizen.txt
-rw-rw-r-- 1 student student 11  1. Sep 11:38 Welt.txt

student@debian13GUI:~$ touch projekte/dokument.md
student@debian13GUI:~$ mv projekte/a.txt projekte/alpha.txt
student@debian13GUI:~$ rm projekte/b.txt
student@debian13GUI:~$ rm -r projekte
```

## 6. Web-Inhalte veröffentlichen (Apache /var/www/html)

```bash
student@debian13GUI:~$ nano IPv4_Quiz.html
student@debian13GUI:~$ sudo mv IPv4_Quiz.html /var/www/html/

# mehrere Iterationen (Inhalt überarbeitet, Datei gelöscht & neu angelegt)
student@debian13GUI:~$ rm IPv4_Quiz.html
student@debian13GUI:~$ nano IPv4_Quiz.html

student@debian13GUI:~$ sudo nano /var/www/html/index.html
student@debian13GUI:~$ sudo rm /var/www/html/index.html
student@debian13GUI:~$ sudo nano /var/www/html/index.html

# alle vorbereiteten HTML-Dateien auf einmal veröffentlicht
student@debian13GUI:~$ ls
index.html  IPv4_Quiz.html  'ipv4QuizV2 2.html'  IT_Grundschutz_Lernkarten.html  LF09_Quiz_W1.html  'zzahlenquiz 1.html'  [...]
student@debian13GUI:~$ sudo mv *html /var/www/html/
student@debian13GUI:~$ cd /var/www/html/
student@debian13GUI:/var/www/html$ sudo mv 'ipv4QuizV2 2.html' 'ipv4QuizV2.html'
student@debian13GUI:/var/www/html$ sudo mv 'zzahlenquiz 1.html' 'zzahlenquiz.html'
student@debian13GUI:/var/www/html$ sudo mv 'ipv4QuizV2.html' LF09_Quiz_W2.html
```

Ergebnis im Browser aufgerufen: `http://10.100.32.122/IPv4_Quiz.html`

## 7. Datei-Transfer von Windows (scp)

Mehrere fehlgeschlagene Versuche (falsches Arbeitsverzeichnis auf der
Windows-Seite), danach erfolgreich:

```powershell
PS C:\...> scp notizen.txt student@10.100.17.102:/home/student
notizen.txt: No such file or directory
# ... (mehrfach wiederholt, Datei existierte im aktuellen Verzeichnis nicht)

PS C:\...> echo "test" > notizen.txt
PS C:\...> scp notizen.txt student@10.100.17.102:/home/student
notizen.txt                                    100%   10     0.5KB/s   00:00
```

## 8. MariaDB einrichten

```sql
root@debian13GUI:~# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Server version: 11.8.6-MariaDB-0+deb13u1 from Debian

MariaDB [(none)]> CREATE DATABASE nextclouddb;
Query OK, 1 row affected (0,002 sec)

MariaDB [(none)]> GRANT ALL ON nextclouddb.* TO 'nextclouduser'@'localhost';
ERROR 1133 (28000): Can't find any matching row in the user table

MariaDB [(none)]> CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'Password';
Query OK, 0 rows affected (0,004 sec)

MariaDB [(none)]> GRANT ALL ON nextclouddb.* TO 'nextclouduser'@'localhost';
Query OK, 0 rows affected (0,006 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0,001 sec)

MariaDB [(none)]> EXIT;
```

> Hinweis: Das oben verwendete Passwort ist ein reines Übungs-/Lab-Passwort
> (lokale MariaDB-Instanz einer Kurs-VM ohne Internetzugang zum Live-Betrieb)
> und wurde absichtlich nicht anonymisiert, da es keinen produktiven Wert hat.
> Bitte trotzdem vor der Veröffentlichung kurz prüfen/bestätigen.

## 9. Nextcloud-Installation

```bash
root@debian13GUI:~# unzip nextcloud-*.zip
  inflating: nextcloud/LICENSES/...
  inflating: nextcloud/COPYING
  inflating: nextcloud/AUTHORS
  inflating: nextcloud/.htaccess

root@debian13GUI:~# mv nextcloud /var/www/html/
root@debian13GUI:~# chown -R www-data:www-data /var/www/html/nextcloud/
root@debian13GUI:~# chmod -R 755 /var/www/html/nextcloud/
root@debian13GUI:~# nano /etc/apache2/sites-available/nextcloud.conf
root@debian13GUI:~# a2ensite nextcloud.conf
Enabling site nextcloud.
To activate the new configuration, you need to run:
  systemctl reload apache2

root@debian13GUI:~# a2enmod rewrite
root@debian13GUI:~# a2enmod headers
root@debian13GUI:~# a2enmod env      # bereits aktiviert
root@debian13GUI:~# a2enmod dir      # bereits aktiviert
root@debian13GUI:~# a2enmod mime     # bereits aktiviert
root@debian13GUI:~# service apache2 reload
root@debian13GUI:~# systemctl restart apache2
```

Beim ersten Durchlauf des Web-Installers schlug die Admin-Konto-Erstellung
mehrfach fehl (`Access denied for user 'student'@'localhost'` /
`'nextclouduser'@'localhost'`), bis DB-Nutzer und Passwort im Installer mit
den in MariaDB tatsächlich vergebenen Werten übereinstimmten:

```sql
root@debian13GUI:~# mysql -u root -p
MariaDB [(none)]> ALTER USER 'nextclouduser'@'localhost' IDENTIFIED BY 'Password';
Query OK, 0 rows affected (0,017 sec)
MariaDB [(none)]> FLUSH PRIVILEGES;
MariaDB [(none)]> EXIT;
```

Anschließend Aufruf des Nextcloud-Setup-Assistenten im Browser und
erfolgreiche Einrichtung des Admin-Kontos.

---

## OverTheWire Bandit (separates Wargame, nicht Teil der VM-Übung)

Neben der Debian-VM wurde auch das OverTheWire-„Bandit"-Wargame verwendet
(`bandit.labs.overthewire.org`, Port `2220`) — ein öffentliches SSH-basiertes
Sicherheitstraining, komplett unabhängig von der Kurs-VM.

```bash
bandit0@bandit:~$ ls
readme
bandit0@bandit:~$ cat readme
Congratulations on your first steps into the bandit game!!
The password you are looking for is: 6y2kwmwK6grgvwvpvLaa2T1cpFEKOhNR
bandit0@bandit:~$ exit
```

> **Wichtig — vor Veröffentlichung entscheiden:** Das oben angezeigte
> Passwort ist der öffentlich bekannte Level-0-Login von Bandit (jeder, der
> das Spiel startet, bekommt genau dieses Passwort — es handelt sich nicht
> um ein persönliches Geheimnis). Ob es trotzdem im öffentlichen Repo
> stehen soll, ist eine Stilfrage, keine Sicherheitsfrage — bitte kurz mit
> Web-Claude abstimmen, bevor gepusht wird. Level 1 wurde erreicht.

---

## Quellenhinweis

Rekonstruiert aus Screenshots vom 31.08.–03.09.2026
(`Screenshots/LF09V2 Netzwerke und Dienste bereitstellen 17.08.2026-04.09.2026/`).
Einige Zeilen waren durch überlappende Fenster oder Bildschirmränder
abgeschnitten; in diesen Fällen wurde der lesbare Teil übernommen.
