# I. Service SSH



🌞 **S'assurer que le service `sshd` est démarré**
```bash
● web.tp1.b1
    State: running
    Units: 283 loaded (incl. loaded aliases)
     Jobs: 0 queued
   Failed: 0 units
    Since: Mon 2024-12-02 14:30:36 CET; 2min 57s ago
  systemd: 252-46.el9_5.2.0.1
   CGroup: /
           ├─init.scope
           │ └─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 31
           ├─system.slice
           │ ├─NetworkManager.service
           │ │ └─684 /usr/sbin/NetworkManager --no-daemon
           │ ├─auditd.service
           │ │ └─643 /sbin/auditd
           │ ├─chronyd.service
           │ │ └─681 /usr/sbin/chronyd -F 2
           │ ├─crond.service
           │ │ └─725 /usr/sbin/crond -n
           │ ├─dbus-broker.service
           │ │ ├─674 /usr/bin/dbus-broker-launch --scope system --audit
           │ │ └─675 dbus-broker --log 4 --controller 9 --machine-id 3c45b0b91e964fa1bd272ad4be713782 --max-bytes 53687>
           │ ├─firewalld.service
           │ │ └─678 /usr/bin/python3 -s /usr/sbin/firewalld --nofork --nopid
           │ ├─rsyslog.service
           │ │ └─769 /usr/sbin/rsyslogd -n
           │ ├─sshd.service
           │ │ └─715 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"
           │ ├─systemd-journald.service
lines 1-29
```

🌞 **Analyser les processus liés au service SSH**


```bash
[chachat@web ~]$ systemctl status sshd
● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; preset: enabled)
     Active: active (running) since Mon 2024-12-02 14:30:39 CET; 19min ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 715 (sshd)
      Tasks: 1 (limit: 11083)
     Memory: 9.0M
        CPU: 283ms
     CGroup: /system.slice/sshd.service
             └─715 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

Dec 02 14:30:39 web.tp1.b1 sshd[715]: Server listening on :: port 22.
Dec 02 14:30:39 web.tp1.b1 systemd[1]: Started OpenSSH server daemon.
Dec 02 14:31:16 web.tp1.b1 sshd[1260]: Accepted password for chachat from 10.1.1.3 port 50371 ssh2
Dec 02 14:31:16 web.tp1.b1 sshd[1260]: pam_unix(sshd:session): session opened for user chachat(uid=1000) by chachat(uid>
Dec 02 14:41:27 web.tp1.b1 sshd[1294]: Accepted password for chachat from 10.1.1.3 port 50394 ssh2
Dec 02 14:41:27 web.tp1.b1 sshd[1294]: pam_unix(sshd:session): session opened for user chachat(uid=1000) by chachat(uid>
Dec 02 14:43:14 web.tp1.b1 sshd[1329]: Accepted password for chachat from 10.1.1.3 port 50396 ssh2
Dec 02 14:43:14 web.tp1.b1 sshd[1329]: pam_unix(sshd:session): session opened for user chachat(uid=1000) by chachat(uid>
Dec 02 14:50:09 web.tp1.b1 sshd[1391]: Accepted password for chachat from 10.1.1.3 port 50406 ssh2
Dec 02 14:50:09 web.tp1.b1 sshd[1391]: pam_unix(sshd:session): session opened for user chachat(uid=1000) by chachat(uid>
lines 1-22/22 (END)


[chachat@web ~]$ ps 1391 | grep sshd
   1391 ?        Ss     0:00 sshd: chachat [priv]
```

🌞 **Déterminer le port sur lequel écoute le service SSH**
```bash
[chachat@web ~]$ sudo ss -alnpt | grep sshd
LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=715,fd=3))
LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=715,fd=4))
```

🌞 **Consulter les logs du service SSH**

```bash
[chachat@web ~]$ journalctl -xe -u sshd
~
~
~
Dec 02 14:30:39 web.tp1.b1 systemd[1]: Starting OpenSSH server daemon...
░░ Subject: A start job for unit sshd.service has begun execution
░░ Defined-By: systemd
░░ Support: https://wiki.rockylinux.org/rocky/support
░░
░░ A start job for unit sshd.service has begun execution.
░░
░░ The job identifier is 225.
Dec 02 14:30:39 web.tp1.b1 sshd[715]: Server listening on 0.0.0.0 port 22.
Dec 02 14:30:39 web.tp1.b1 sshd[715]: Server listening on :: port 22.
Dec 02 14:30:39 web.tp1.b1 systemd[1]: Started OpenSSH server daemon.
░░ Subject: A start job for unit sshd.service has finished successfully
░░ Defined-By: systemd
░░ Support: https://wiki.rockylinux.org/rocky/support
░░
░░ A start job for unit sshd.service has finished successfully.
░░
░░ The job identifier is 225.
Dec 02 14:31:16 web.tp1.b1 sshd[1260]: Accepted password for chachat from 10.1.1.3 port 50371 ssh2
Dec 02 14:31:16 web.tp1.b1 sshd[1260]: pam_unix(sshd:session): session opened for user chachat(uid=1000) by chachat(uid>
Dec 02 14:41:27 web.tp1.b1 sshd[1294]: Accepted password for chachat from 10.1.1.3 port 50394 ssh2
Dec 02 14:41:27 web.tp1.b1 sshd[1294]: pam_unix(sshd:session): session opened for user chachat(uid=1000) by chachat(uid>
Dec 02 14:43:14 web.tp1.b1 sshd[1329]: Accepted password for chachat from 10.1.1.3 port 50396 ssh2
Dec 02 14:43:14 web.tp1.b1 sshd[1329]: pam_unix(sshd:session): session opened for user chachat(uid=1000) by chachat(uid>
Dec 02 14:50:09 web.tp1.b1 sshd[1391]: Accepted password for chachat from 10.1.1.3 port 50406 ssh2
Dec 02 14:50:09 web.tp1.b1 sshd[1391]: pam_unix(sshd:session): session opened for user chachat(uid=1000) by chachat(uid>
lines 1-26/26 (END)


[chachat@web log]$ tail -n 10 lastlog
��Mgpts/310.1.1.3[chachat@web log]$
```

## 2. Modification du service


🌞 **Identifier le fichier de configuration du serveur SSH**
```bash
[chachat@web ~]$ cd /etc/ssh
[chachat@web ssh]$ ls
moduli      ssh_config.d  sshd_config.d       ssh_host_ecdsa_key.pub  ssh_host_ed25519_key.pub  ssh_host_rsa_key.pub
ssh_config  sshd_config   ssh_host_ecdsa_key  ssh_host_ed25519_key    ssh_host_rsa_key

[chachat@web ssh]$ file sshd_config
sshd_config: regular file, no read permission
```
🌞 **Modifier le fichier de conf**
```bash
[chachat@web ~]$ echo $RANDOM
32638

[chachat@web ~]$ sudo cat /etc/ssh/sshd_config | grep Port
#Port 22
Port 32638


[chachat@web ~]$ sudo firewall-cmd --permanent --remove-port=22/tcp
Warning: NOT_ENABLED: 22:tcp
success
[chachat@web ~]$ sudo firewall-cmd --reload
success

[chachat@web ~]$ sudo firewall-cmd --permanent --add-port=32638/tcp
success
[chachat@web ~]$ sudo firewall-cmd --reload
success

[chachat@web ~]$ sudo firewall-cmd --list-all | grep 32638
  ports: 32638/tcp
```

🌞 **Redémarrer le service**
```bash
[chachat@web ssh]$ sudo systemctl restart sshd
```
🌞 **Effectuer une connexion SSH sur le nouveau port**
```bash
PS C:\Users\Asus> ssh -p 32638 chachat@10.1.1.1
chachat@10.1.1.1's password:
Last login: Mon Dec  2 15:24:46 2024 from 10.1.1.3
[chachat@web ~]$
```

# II. Service HTTP


## 1. Mise en place

🌞 **Installer le serveur NGINX**
```bash
[chachat@web ~]$ dnf search NGINX
Rocky Linux 9 - BaseOS                                                                  2.4 MB/s | 2.3 MB     00:00
Rocky Linux 9 - AppStream                                                               5.2 MB/s | 8.3 MB     00:01
Rocky Linux 9 - Extras                                                                   33 kB/s |  16 kB     00:00
============================================ Name & Summary Matched: NGINX =============================================nginx-all-modules.noarch : A meta package that installs all available Nginx modules
nginx-core.x86_64 : nginx minimal core
nginx-filesystem.noarch : The basic directory layout for the Nginx server
nginx-mod-http-image-filter.x86_64 : Nginx HTTP image filter module
nginx-mod-http-perl.x86_64 : Nginx HTTP perl module
nginx-mod-http-xslt-filter.x86_64 : Nginx XSLT module
nginx-mod-mail.x86_64 : Nginx mail modules
nginx-mod-stream.x86_64 : Nginx stream modules
pcp-pmda-nginx.x86_64 : Performance Co-Pilot (PCP) metrics for the Nginx Webserver
================================================= Name Matched: NGINX ==================================================
nginx.x86_64 : A high performance web server and reverse proxy server
[chachat@web ~]$ dnf install NGINX
Error: This command has to be run with superuser privileges (under the root user on most systems).
[chachat@web ~]$ sudo dnf install NGINX
Last metadata expiration check: 0:51:55 ago on Mon 02 Dec 2024 03:38:15 PM CET.
No match for argument: NGINX
  * Maybe you meant: nginx
Error: Unable to find a match: NGINX
[chachat@web ~]$ sudo dnf install nginx
Last metadata expiration check: 0:52:08 ago on Mon 02 Dec 2024 03:38:15 PM CET.
Dependencies resolved.
========================================================================================================================
 Package                         Architecture         Version                             Repository               Size
========================================================================================================================
Installing:
 nginx                           x86_64               2:1.20.1-20.el9.0.1                 appstream                36 k
Installing dependencies:
 nginx-core                      x86_64               2:1.20.1-20.el9.0.1                 appstream               566 k
 nginx-filesystem                noarch               2:1.20.1-20.el9.0.1                 appstream               8.4 k
 rocky-logos-httpd               noarch               90.15-2.el9                         appstream                24 k

Transaction Summary
========================================================================================================================
Install  4 Packages

Total download size: 634 k
Installed size: 1.8 M
Is this ok [y/N]: y
Downloading Packages:
(1/4): nginx-filesystem-1.20.1-20.el9.0.1.noarch.rpm                                     58 kB/s | 8.4 kB     00:00
(2/4): rocky-logos-httpd-90.15-2.el9.noarch.rpm                                         114 kB/s |  24 kB     00:00
(3/4): nginx-1.20.1-20.el9.0.1.x86_64.rpm                                                93 kB/s |  36 kB     00:00
(4/4): nginx-core-1.20.1-20.el9.0.1.x86_64.rpm                                          1.7 MB/s | 566 kB     00:00
------------------------------------------------------------------------------------------------------------------------
Total                                                                                   838 kB/s | 634 kB     00:00
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                1/1
  Running scriptlet: nginx-filesystem-2:1.20.1-20.el9.0.1.noarch                                                    1/4
  Installing       : nginx-filesystem-2:1.20.1-20.el9.0.1.noarch                                                    1/4
  Installing       : nginx-core-2:1.20.1-20.el9.0.1.x86_64                                                          2/4
  Installing       : rocky-logos-httpd-90.15-2.el9.noarch                                                           3/4
  Installing       : nginx-2:1.20.1-20.el9.0.1.x86_64                                                               4/4
  Running scriptlet: nginx-2:1.20.1-20.el9.0.1.x86_64                                                               4/4
  Verifying        : rocky-logos-httpd-90.15-2.el9.noarch                                                           1/4
  Verifying        : nginx-filesystem-2:1.20.1-20.el9.0.1.noarch                                                    2/4
  Verifying        : nginx-2:1.20.1-20.el9.0.1.x86_64                                                               3/4
  Verifying        : nginx-core-2:1.20.1-20.el9.0.1.x86_64                                                          4/4

Installed:
  nginx-2:1.20.1-20.el9.0.1.x86_64                              nginx-core-2:1.20.1-20.el9.0.1.x86_64
  nginx-filesystem-2:1.20.1-20.el9.0.1.noarch                   rocky-logos-httpd-90.15-2.el9.noarch

Complete!
[chachat@web ~]$
```
🌞 **Démarrer le service NGINX**
```bash
[chachat@web ~]$ systemctl status nginx
○ nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
     Active: inactive (dead)
[chachat@web ~]$ sudo systemctl start nginx
[chachat@web ~]$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
     Active: active (running) since Mon 2024-12-02 16:34:56 CET; 9s ago
    Process: 2002 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
    Process: 2003 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
    Process: 2004 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 2005 (nginx)
```

🌞 **Déterminer sur quel port tourne NGINX**
```bash
[chachat@web ~]$ sudo ss -alnpt | grep nginx
[sudo] password for chachat:
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=2006,fd=6),("nginx",pid=2005,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=2006,fd=7),("nginx",pid=2005,fd=7))

[chachat@web ~]$ sudo firewall-cmd --permanent --add-port=80/tcp
success
[chachat@web ~]$ sudo firewall-cmd --reload
success
```

🌞 **Déterminer les processus liés au service NGINX**

```bash
[chachat@web ~]$ systemctl status nginx | grep 2003
    Process: 2003 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
Dec 02 16:34:56 web.tp1.b1 nginx[2003]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Dec 02 16:34:56 web.tp1.b1 nginx[2003]: nginx: configuration file /etc/nginx/nginx.conf test is successful
```

🌞 **Déterminer le nom de l'utilisateur qui lance NGINX**

```bash
[chachat@web etc]$ cat /etc/passwd | grep chachat
chachat:x:1000:1000:chachat:/home/chachat:/bin/bash
```

🌞 **Test !**

```bash
Asus@AsusExpertBook MINGW64 ~
$ curl http://10.1.1.1:80 | head
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  7620  100  7620 <!doctype html> 0      0 --:--:-- --:--:-- --:--:--     0
<html>
  <head>
    <meta charset='utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
    <title>HTTP Server Test Page powered by: Rocky Linux</title>
    <style type="text/css">
      /*<![CDATA[*/

      html {
   0     0   878k      0 --:--:-- --:--:-- --:--:--  930k
curl: Failed writing body
```

## 2. Analyser la conf de NGINX

🌞 **Déterminer le path du fichier de configuration de NGINX**

```bash 
[chachat@web etc]$ which nginx
/usr/sbin/nginx
[chachat@web etc]$ ls -al /usr/sbin/nginx
-rwxr-xr-x. 1 root root 1329232 Nov  8 17:44 /usr/sbin/nginx
```
🌞 **Trouver dans le fichier de conf**

```bash
[chachat@web etc]$ cat /etc/nginx/nginx.conf | grep "server {" -A 20
    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }

# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2;
#        listen       [::]:443 ssl http2;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers PROFILE=SYSTEM;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;


[chachat@web etc]$ cat /etc/nginx/nginx.conf | grep "include"
include /usr/share/nginx/modules/*.conf;
    include             /etc/nginx/mime.types;
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/default.d/*.conf;
#        include /etc/nginx/default.d/*.conf;

[chachat@web etc]$ cat /etc/nginx/nginx.conf | grep user
user nginx;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
```
## 3. Déployer un nouveau site web

🌞 **Créer un site web**

```bash
[chachat@web var]$ sudo mkdir /var/www
[sudo] password for chachat:
[chachat@web var]$ ls
adm    crash  empty  games     lib    lock  mail  opt       run    tmp  yp
cache  db     ftp    kerberos  local  log   nis   preserve  spool  www
[chachat@web var]$ cd www
[chachat@web www]$ mkdir /www/tp1_parc
mkdir: cannot create directory ‘/www/tp1_parc’: No such file or directory
[chachat@web www]$ mkdir tp1_parc
mkdir: cannot create directory ‘tp1_parc’: Permission denied
[chachat@web www]$ sudo mkdir tp1_parc
[chachat@web www]$ ls
tp1_parc
[chachat@web www]$ cd tp1_parc
[chachat@web tp1_parc]$ sudo nano index.html
```
🌞 **Gérer les permissions**

```bash
[chachat@web tp1_parc]$ sudo chown -R nginx:nginx /var/www/tp1_parc
[chachat@web tp1_parc]$ sudo chmod -R 755 /var/www/tp1_parc
[chachat@web tp1_parc]$ ls -l /var/www/tp1_parc
total 4
-rwxr-xr-x. 1 nginx nginx 38 Dec  2 17:37 index.html
```

🌞 **Adapter la conf NGINX**

- dans le fichier de conf principal
  - vous supprimerez le bloc `server {}` repéré plus tôt pour que NGINX ne serve plus le site par défaut (parce que ça sert à rien le site par défaut)
  - redémarrez NGINX pour que les changements prennent effet
- créez un nouveau fichier de conf
  - il doit être nommé correctement
  - il doit être placé dans le bon dossier
  - c'est quoi un "nom correct" et "le bon dossier" ?
    - bah vous avez repéré dans la partie d'avant les fichiers qui sont inclus par le fichier de conf principal non ?
    - créez votre fichier en conséquence
  - redémarrez NGINX pour que les changements prennent effet
  - le contenu doit être le suivant :
    - il écoute sur un port que vous aurez déterminé aléatoirement avec `echo $RANDOM`
      - n'oubliez pas d'ouvrir ce port dans le firewall, et fermer l'ancien
    - il définit que le site web est stocké dans /var/www/tp1_parc

```nginx
server {
  # le port choisi devra être obtenu avec un 'echo $RANDOM' là encore
  listen <PORT>;

  root /var/www/tp1_parc;
}
```

🌞 **Visitez votre super site web**

- toujours avec une commande `curl` depuis votre PC (ou un navigateur)