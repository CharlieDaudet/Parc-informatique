# Partie I : Des beaux one-liners

## 1. Intro

**Le but de cette première partie, c'est un échauffement, vous allez fabriquer des commandes pour aller récupérer des infos précises.**


## 2. Let's go

🌞 **Afficher la quantité d'espace disque disponible**


```bash
[chachat@node1 ~]$ df -h |grep /dev/mapper/rl_vbox-root | tr -s ' ' | cut -d' ' -f4
16G
[chachat@node1 ~]$
```

🌞 **Afficher combien de fichiers il est possible de créer**


```bash
[chachat@node1 ~]$ df -i /
Filesystem                Inodes IUsed   IFree IUse% Mounted on
/dev/mapper/rl_vbox-root 8910848 33315 8877533    1% /
```

🌞 **Afficher l'heure et la date**

- au format suivant : `dd/mm/yy hh:mm:ss`
- **📎 commande `date`**

```bash
[chachat@node1 ~]$  date +"%d/%m/%y %H:%M:%S"
16/12/24 17:47:07
```

🌞 **Afficher la version de l'OS précise**


```bash
[chachat@node1 ~]$ source /etc/os-release && echo $PRETTY_NAME
Rocky Linux 9.5 (Blue Onyx)
```

🌞 **Afficher la version du kernel en cours d'utilisation précise**

- **📎 commande `uname`**

```bash
[chachat@node1 ~]$ uname -r
5.14.0-503.14.1.el9_5.x86_64
```

🌞 **Afficher le chemin vers la commande `python3`**

- **📎 commande `which`**

```bash
[chachat@node1 ~]$ which python3
/usr/bin/python3
```

🌞 **Afficher l'utilisateur actuellement connecté**


```bash
[chachat@node1 ~]$ echo $USER
chachat
```

🌞 **Afficher le shell par défaut de votre utilisateur actuellement connecté**

```bash
[chachat@node1 ~]$ cat /etc/passwd | grep $USER | awk -F: '{print $6,$7}'
/home/chachat /bin/bash
```

🌞 **Afficher le nombre de paquets installés**


```bash
[chachat@node1 ~]$ rpm -qa | wc -l
358
```

🌞 **Afficher le nombre de ports en écoute**


- **📎 commande `ss`**

```bash
[chachat@node1 ~]$ ss -tuln | grep LISTEN | wc -l
2
```
