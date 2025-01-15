# Partie I : Script autoconfiguration


## 1. Ce que doit faire le script


🌞 **Script `autoconfig.sh`**
```bash
[chachat@music opt]$ ./autoconfig.sh
Le script doit être lancé en root!
[chachat@music opt]$ sudo ./autoconfig.sh
SSH est sur le port 22 , à modifier
success
Warning: NOT_ENABLED: 22:tcp
success
success
SSH tourne maintenant sur le port 18121
L'utilisateur chachat n'est pas dans le groupe wheel
```
# Partie II : Serveur de streaming

## 1. Préparation de la machine

🌞 **Exécution du script `autoconfig.sh` développé à la partie I**

```bash
[chachat@music opt]$ sudo chmod +x autoconfig.sh
[chachat@music opt]$  sudo ./autoconfig.sh
SSH est sur le port 22 , à modifier
success
Warning: NOT_ENABLED: 22:tcp
success
success
SSH tourne maintenant sur le port 9414
L'utilisateur chachat n'est pas dans le groupe wheel
```


🌞 **Création d'un dossier où on hébergera les fichiers de musique**

```bash
[chachat@music srv]$ sudo mkdir music
[chachat@music srv]$ ls
music
[chachat@music srv]$

```

🌞 **Déposez quelques fichiers son là dedans**

```bash
[chachat@music music]$ ls /tmp/
 systemd-private-d981f0deab2340329c5ef28a16503b62-chronyd.service-zJKohU
 systemd-private-d981f0deab2340329c5ef28a16503b62-dbus-broker.service-tf6d4W
 systemd-private-d981f0deab2340329c5ef28a16503b62-kdump.service-E9CmYJ
 systemd-private-d981f0deab2340329c5ef28a16503b62-systemd-logind.service-Y6lAHN
'To Ashes and Blood (from the series Arcane League of Legends).mp3'
[chachat@music music]$ sudo mv /tmp/'To Ashes and Blood (from the series Arcane League of Legends).mp3' /srv/music/
[chachat@music music]$ ls -al /srv/music/
total 5792
drwxr-xr-x. 2 root    root         79 Jan 15 11:05  .
drwxr-xr-x. 3 root    root         19 Jan 15 10:33  ..
-rw-r--r--. 1 chachat chachat 5929633 Jan 15 11:02 'To Ashes and Blood (from the series Arcane League of Legends).mp3'

Même chose pour une deuxième :
-rw-r--r--. 1 chachat chachat 4410980 Jan 15 11:28 'Yaelokre - Harpy Hare 𓆱 (Illustrated Song).mp3'
```

## 2. Installation du service de streaming

🌞 **Ajoutez les dépôts nécessaires pour installer Jellyfin**

```bash
sudo dnf install --nogpgcheck https://mirrors.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-$(rpm -E %rhel).noarch.rpm -y
sudo dnf config-manager --set-enabled crb

```

🌞 **Installer le paquet `jellyfin`**

```bash
sudo dnf install jellyfin
```

🌞 **Lancer le service `jellyfin`**
```bash
[chachat@music music]$ sudo systemctl start jellyfin
[chachat@music music]$ sudo systemctl status jellyfin
```
🌞 **Afficher la liste des ports TCP en écoute**

```bash
[chachat@music music]$ sudo ss -lntp | grep jellyfin
LISTEN 0      512          0.0.0.0:8096      0.0.0.0:*    users:(("jellyfin",pid=12737,fd=310))
```
🌞 **Ouvrir le port derrière lequel Jellyfin écoute**
```bash
[chachat@music music]$ sudo firewall-cmd --permanent --add-port=8096/tcp
success
[chachat@music music]$ sudo firewall-cmd --reload
success
```

🌞 **Visitez l'interface Web !**
```bash
[chachat@music music]$ curl -I "http://10.3.1.11:8096/web/index.html"
HTTP/1.1 200 OK
Content-Length: 7442
Content-Type: text/html
Date: Wed, 15 Jan 2025 11:01:48 GMT
Server: Kestrel
Accept-Ranges: bytes
ETag: "1da23514f439592"
Last-Modified: Thu, 30 Nov 2023 05:51:49 GMT
X-Response-Time-ms: 0
```

# Partie III : Serveur de monitoring


🌞 **Dérouler le script `autoconfig.sh` développé à la partie I**
```bash
[chachat@monitoring ~]$ sudo nano autoscript.sh
[sudo] password for chachat:
[chachat@monitoring ~]$ sudo chmod +x autoscript.sh
[chachat@monitoring ~]$ sudo ./autoscript.sh
SSH est sur le port 22 , à modifier
success
Warning: NOT_ENABLED: 22:tcp
success
success
SSH tourne maintenant sur le port 26961
L'utilisateur chachat n'est pas dans le groupe wheel
```

🌞 **Installer Netdata**

```bash
 curl https://get.netdata.cloud/kickstart.sh > /tmp/netdata-kickstart.sh && sh /tmp/netdata-kickstart.sh --no-updates --stable-channel --disable-telemetry
 ```

🌞 **Ajouter un check TCP**

```bash
[chachat@monitoring ~]$ sudo systemctl start netdata
[chachat@monitoring ~]$ systemctl status netdata
[chachat@monitoring ~]$ sudo ss -lntp
State         Recv-Q        Send-Q                 Local Address:Port                  Peer Address:Port        Process
LISTEN        0             128                          0.0.0.0:22                         0.0.0.0:*            users:(("sshd",pid=1338,fd=3))
LISTEN        0             4096                       127.0.0.1:8125                       0.0.0.0:*            users:(("netdata",pid=12771,fd=53))
LISTEN        0             4096                         0.0.0.0:19999                      0.0.0.0:*            users:(("netdata",pid=12771,fd=6))
LISTEN        0             128                             [::]:22                            [::]:*            users:(("sshd",pid=1338,fd=4))
LISTEN        0             4096                            [::]:19999                         [::]:*            users:(("netdata",pid=12771,fd=7))
LISTEN        0             4096                           [::1]:8125                          [::]:*            users:(("netdata",pid=12771,fd=52))

[chachat@monitoring ~]$ sudo firewall-cmd --permanent --add-port=19999/tcp
success
[chachat@monitoring ~]$ sudo firewall-cmd --reload
success

```
🌞 **Ajout d'une alerte Discord**

```bash
[chachat@monitoring ~]$ cd /etc/netdata 2>/dev/null || cd /opt/netdata/etc/netdata
sudo ./edit-config go.d/portcheck.conf
Copying '/etc/netdata/../../usr/lib/netdata/conf.d//go.d/portcheck.conf' to '/etc/netdata//go.d/portcheck.conf' ...
Editing '/etc/netdata/go.d/portcheck.conf' ...
[chachat@monitoring netdata]$ cd /etc/netdata 2>/dev/null || cd /opt/netdata/etc/netdata
sudo ./edit-config health_alarm_notify.conf
Copying '/etc/netdata/../../usr/lib/netdata/conf.d//health_alarm_notify.conf' to '/etc/netdata//health_alarm_notify.conf' ...
Editing '/etc/netdata/health_alarm_notify.conf' .

Copying '/etc/netdata/../../usr/lib/netdata/conf.d//health_alarm_notify.conf' to '/etc/netdata//health_alarm_notify.conf' ...
Editing '/etc/netdata/health_alarm_notify.conf' ...
[chachat@monitoring netdata]$ cd /etc/netdata 2>/dev/null || cd /opt/netdata/etc/netdata
sudo ./edit-config go.d/portcheck.conf
Editing '/etc/netdata/go.d/portcheck.conf' ...
                      7L, 272B written
[chachat@monitoring netdata]$ sudo ./edit-config go.d/portcheck.conf
Editing '/etc/netdata/go.d/portcheck.conf' ...


```
