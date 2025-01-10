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
# Partie II : Un premier ptit script

## 1. Intro script

## 2. Premiers pas scripting

🌞 **Ecrire un script qui produit exactement l'affichage demandé**

```bash
!#/bin/bash
#Script info importante. 

USER_NAME=$echo $USER
DATE=$(date +"%d/%m/%y %H:%M:%S")
USER_SHELL=$(cat /etc/passwd | grep $USER | awk -F: '{print $7}')
OS_VERSION= source /etc/os-release && echo $PRETTY_NAME
KERNEL_VERSION= uname -r
FREE_RAM=free -mh | grep 'Mem:' | tr -s ' ' | cut -d' ' -f7
FREE_DISK=df -h |grep /dev/mapper/rl_vbox-root | tr -s ' ' | cut -d' ' -f4
INODES_LEFT=df -i /
PACKETS_INSTALLED=rpm -qa | wc -l
OPEN_PORTS= ss -tuln | grep LISTEN | wc -l
PYTHON_PATH=which python3

echo "Salu a toa $USER_NAME."
echo "Nouvelle connexion $DATE".
echo "Connecté avec le shell $USER_SHELL".
echo "OS : $OS_VERSION - Kernel : $KERNEL_VERSION"
echo "Ressources :"
echo "- $FREE_RAM RAM dispo"
echo "- $FREE_DISK espace disque dispo"
echo "- $INODES_LEFT fichiers restants"
echo "Actuellement : "
echo "- $PACKETS_INSTALLED paquets installés"
echo "- $OPEN_PORTS port(s) ouvert(s)"
echo "Python est bien installé sur la machine au chemin : $PYTHON_PATH"

```
## 3. Amélioration du script


🌞 **Le script `id.sh` affiche l'état du firewall**

```bash
firewall=$(systemctl is-active firewalld)
if [[ $firewall -eq "active" ]]
then
  echo "Le firewall est actif."
else
  echo "Le firewall est inactif."
fi
```

🌞 **Le script `id.sh` affiche l'URL vers une photo de chat random**

```bash
cat_pic=$(curl https://api.thecatapi.com/v1/images/search | tr -s '"' | cut -d '"' -f8)
echo "Voila ta photo de chat : $cat_pic"
```

## 4. Bannière



🌞 **Stocker le fichier `id.sh` dans `/opt`**
```bash
[chachat@node1 ~]$ sudo mv /home/chachat/id.sh /opt
[sudo] password for chachat:
[chachat@node1 ~]$ ls
opt
```
🌞 **Prouvez que tout le monde peut exécuter le script**

```bash
[chachat@node1 ~]$ ls -al /opt
total 4
drwxr-xr-x.  2 root    root     19 Jan 10 19:01 .
dr-xr-xr-x. 18 root    root    235 Nov 29 17:39 ..
-rw-r--r--.  1 chachat chachat 970 Jan 10 18:58 id.sh
```

🌞 **Ajouter l'exécution au `.bashrc` de votre utilisateur**



```bash
PS C:\Users\Asus> ssh chachat@10.2.1.1
chachat@10.2.1.1's password:
Last login: Mon Dec 23 19:32:37 2024 from 10.2.1.2
/opt/id.sh: line 13: 8877501: command not found
Salu a toa chachat.
Nouvelle connexion 23-12-2024 19:34:57.
Connecté avec le shell /bin/bash.
OS : Rocky Linux 9.5 - Kernel : 5.14.0-503.14.1.el9_5.x86_64
Ressources :
- 1.4Gi RAM dispo
- 16G espace disque dispo
- 8877525 fichiers restants
Actuellement :
- 358 paquets installés
- 2 port(s) ouvert(s)
Python est bien installé sur la machine au chemin : /usr/bin/python
[chachat@node1 ~]$

```

