# Linux-Administration

Praxisnahe Linux-Administrationsübungen aus dem Rahmen der Umschulung zum
Fachinformatiker Systemintegration (GFN), Lernfeld LF09V2 „Netzwerke und
Dienste bereitstellen".

## Inhalt

- **command-log.md** — Chronologisch/thematisch aufbereitetes Log echter
  Linux-Befehle (SSH-Setup, Paketverwaltung, Dienstverwaltung,
  Netzwerkprüfung, Dateioperationen, Web-Content-Publishing über Apache,
  Datei-Transfer per scp, MariaDB-Einrichtung, Nextcloud-Installation),
  rekonstruiert aus eigenen Screenshots einer Debian-13-VM (Hyper-V).
- **evidence/** — Ausgewählte Screenshots als Beleg der echten Terminal-Arbeit:
  - `01-ssh-login.png` — SSH-Login auf die Debian-VM
  - `02-systemctl-apache2.png` — Apache-Dienststatus prüfen
  - `03-ip-a.png` — Netzwerkkonfiguration auslesen
  - `04-mariadb-setup.png` — Datenbank/Nutzer für Nextcloud anlegen
  - `05-nextcloud-apache-config.png` — Apache-vHost & Module für Nextcloud aktivieren
  - `optional-bandit-level0.png` — *(optional, siehe Hinweis unten)*

## Hinweis zu OverTheWire Bandit

Neben der Kurs-VM wurde zusätzlich das öffentliche Sicherheits-Wargame
[OverTheWire Bandit](https://overthewire.org/wargames/bandit/) genutzt
(Level 0 → 1 erreicht). Das zugehörige Log findet sich in `command-log.md`
im eigenen Abschnitt — **bitte vor dem Push kurz prüfen**, ob dieser
Abschnitt und der optionale Screenshot in der veröffentlichten Version
enthalten bleiben sollen.

---
*Hinweis: Bei der Rekonstruktion und Aufbereitung der Befehlsprotokolle aus
Screenshots wurde Claude (Anthropic) unterstützend eingesetzt.*
