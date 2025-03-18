# 42.Born2beRoot
---
### Introduction

Le projet Born2beroot est conçu pour développer des compétences en administration système en configurant une machine Linux sécurisée.\
L'objectif est de mettre en place une VM avec des paramètres de sécurité avancés, la gestion des utilisateurs et des partitions, ainsi que l'installation de services essentiels.

Dans ce projet, nous abordons les notions suivantes :

* Virtualisation : Utilisation d'une machine virtuelle pour isoler un environnement Linux sans affecter le système hôte.

* Système d'exploitation : Configuration et administration d'un système Debian ou CentOS (Debianpour ma part) et apprendre à configurer un serveur sécurisé..

* Gestion des partitions : Structuration et gestion avancée du stockage grâce à LVM (Logical Volume Manager), qui permet une meilleure flexibilité des partitions..

* Utilisateurs et groupes : Gestion des permissions et des droits d'accès, en utilisant des groupes et des privilèges pour sécuriser l'accès au système.

* SSH et Sécurité : Mise en place d'un accès distant sécurisé et renforcement des connexions via des clés SSH.

* Firewall : Configuration d'un pare-feu (UFW sous Debian ou FirewallD sous CentOS) pour restreindre l'accès au serveur.

* Monitoring : Surveillance des performances du système et automatisation des rapports pour détecter d'éventuels problèmes.
---
### Configuration de la machine virtuelle

1. Choix de l'hyperviseur
Le projet impose l'utilisation d'un hyperviseur tel que VirtualBox ou UTM selon le système hôte.\
Un hyperviseur est un logiciel permettant de créer et gérer des machines virtuelles (VM). Pour ce projet, VirtualBox ou UTM sont recommandés selon le système d'exploitation de l'hôte.

2. Installation du système
* Installation d'une distribution Debian ou CentOS minimaliste.
* Création d'un utilisateur sudo permettant d'exécuter des commandes administratives sans se connecter en tant que root.

3. Configuration du hostname et du fichier hosts
* Définition d'un nom d'hôte unique pour identifier la machine sur le réseau.
* Modification du fichier /etc/hosts pour associer ce nom d'hôte à une adresse IP locale.
---

### Gestion des partitions et de LVM

1. Pourquoi utiliser LVM ?\
LVM (Logical Volume Manager) permet de créer des partitions flexibles et dynamiques, ce qui facilite la gestion du stockage, notamment pour augmenter ou réduire l'espace alloué aux différentes partitions sans redémarrer le système.

2. Exemple de structure de partition
Le partitionnement suit une organisation stricte basée sur LVM. Voici un exemple conforme aux exigences du projet :
```
# lsblk
NAME                  SIZE  TYPE  MOUNTPOINT
sda                   30G  disk  ├─ sda1  500M  part  /boot
                         ├─ sda2    1K  part  (partition BIOS)
                         └─ sda5   30G  part  (partition chiffrée LUKS)
  └─ LVMGroup-root   10G  lvm   /
  ├─ LVMGroup-swap    2.3G  lvm  [SWAP]
  ├─ LVMGroup-home    5G  lvm   /home
  ├─ LVMGroup-var     3G  lvm   /var
  ├─ LVMGroup-srv     3G  lvm   /srv
  ├─ LVMGroup-tmp     3G  lvm   /tmp
  └─ LVMGroup-var-log 4G  lvm   /var/log
```

* /boot : Partition séparée et non chiffrée pour le démarrage du système.
* / (root) : Partition principale contenant le système d’exploitation.
* /home, /var, /srv, /tmp, /var/log : Partitions spécifiques pour organiser les données et assurer une meilleure sécurité et gestion des ressources.

### Configuration des utilisateurs et groupes
* Création d'un groupe user42.
* Ajout de l'utilisateur principal à ce groupe.
* Implémentation de sudo avec journaux d'activités.

### Configuration des utilisateurs et groupes
* Création d'un groupe user42.
* Ajout de l'utilisateur principal à ce groupe.
* Implémentation de sudo avec journaux d'activités.

### Sécurisation de l'accès SSH

1. Pourquoi sécuriser SSH ?
* SSH (Secure Shell) permet un accès distant sécurisé au serveur. Par défaut, il est une cible pour les attaques, il est donc essentiel de renforcer sa configuration.

2. Étapes de sécurisation
* Changement du port par défaut pour éviter les scans automatiques.
* Désactivation de l'accès root distant pour empêcher les connexions directes avec le compte administrateur.
* Utilisation des clés SSH plutôt que des mots de passe pour sécuriser l'authentification.
  
### Mise en place du pare-feu
* UFW (Debian) / FirewallD (CentOS) permettent de contrôler le trafic réseau autorisé et interdit.
* Autorisation des services essentiels uniquement : SSH, HTTP/HTTPS (si un serveur web est utilisé).

Exemple de configuration avec UFW :
```
sudo ufw allow 22/tcp
sudo ufw enable
```

### Monitoring du système
* Surveillance des ressources système : CPU, mémoire, espace disque.
* Automatisation avec cron : Génération de rapports réguliers pour détecter d'éventuels problèmes.
* Installation d’outils comme htop et journalctl pour l’analyse des performances.

### Bonus réalisés

1. Structure avancée des partitions
* Mise en place d'une partition chiffrée avec LUKS.
* Séparation des partitions critiques pour une meilleure gestion des logs et du stockage.

2. Installation d'un serveur Web avec WordPress
* Lighttpd : Serveur web léger et performant.
* MariaDB : Base de données open-source pour stocker le contenu WordPress.
* PHP : Interprétation des pages dynamiques du site.

Étapes de l'installation de WordPress
1. Installation des paquets :
```
sudo apt install lighttpd mariadb-server php php-mysql
```
2. Configuration de la base de données :
```
sudo mysql -u root -p
CREATE DATABASE wordpress;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'localhost';
```
3. Installation de WordPress et configuration
