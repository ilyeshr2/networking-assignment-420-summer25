
# Networking Assignment Report

# Table des matières

1. [Introduction](#introduction)
2. [Prérequis](#prerequis)
3. [Configuration de l'environnement virtuel](#configuration-de-lenvironnement-virtuel)
   - [Installation de VirtualBox](#installation-de-virtualbox)
   - [Création des machines virtuelles](#creation-des-machines-virtuelles)
     - [VM Ubuntu Server](#vm-ubuntu-server)
     - [VM Windows 11](#vm-windows-11)
4. [Configuration du réseau virtuel](#configuration-du-reseau-virtuel)
   - [Configuration des adaptateurs réseau sur le réseau interne](#configuration-des-adaptateurs-reseau-sur-le-reseau-interne)
5. [Configuration du pare-feu pfSense](#configuration-du-pare-feu-pfsense)
   - [Téléchargement de pfSense](#telechargement-de-pfsense)
   - [Création de la machine virtuelle pfSense](#creation-de-la-machine-virtuelle-pfsense)
   - [Configuration initiale de pfSense](#configuration-initiale-de-pfsense)
   - [Configuration des mappages IP statiques](#configuration-des-mappages-ip-statiques)
6. [Configuration des règles de pare-feu](#configuration-des-regles-de-pare-feu)
7. [Sécurisation de l'interface Web pfSense](#securisation-de-linterface-web-pfsense)
   - [Création d'autorités de certification et de certificats](#creation-dautorites-de-certification-et-de-certificats)
   - [Importation du certificat CA racine dans Windows](#importation-du-certificat-ca-racine-dans-windows)
   - [Configuration HTTPS pour pfSense](#configuration-https-pour-pfsense)
   - [Mise à jour du fichier Hosts de Windows](#mise-a-jour-du-fichier-hosts-de-windows)
8. [Configuration du serveur Web](#configuration-du-serveur-web)
   - [Installation du LAMP Stack](#installation-du-lamp-stack)
   - [Configuration d'Apache pour le site Web](#configuration-dapache-pour-le-site-web)
   - [Test du serveur Web](#test-du-serveur-web)
9. [Test final et vérification](#test-final-et-verification)
10. [Conclusion](#conclusion)
11. [Annexe](#annexe)
    - [Commandes utilisées](#commandes-utuliser)
---

## Introduction

Ce rapport décrit le processus complet pour créer un réseau local sécurisé à l'aide de VirtualBox, pfSense, Ubuntu Server et Windows 11. L'objectif était de créer un environnement virtualisé dans lequel un site Web hébergé sur un serveur Ubuntu est accessible depuis la machine hôte (physique).

## Prerequis

- Une machine physique avec des ressources suffisantes (RAM, CPU, stockage) pour héberger plusieurs machines virtuelles.
- Des privilèges d'administrateur sur la machine hôte pour installer des logiciels et modifier les paramètres du système.
- Une connexion Internet stable pour télécharger les packages logiciels nécessaires.

## Configuration de l'Environnement Virtuel

### Installation de VirtualBox

1. **Telecharger VirtualBox:**
   - Accéder à la page [VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads)
   - Sélectionnez la version appropriée pour notre système d’exploitation hôte (Windows dans mon cas).

2. **Install VirtualBox:**
   - Exécutez le programme d’installation téléchargé.
   - Suivez les instructions à l'écran en acceptant les paramètres par défaut.
   - Terminez l'installation et lancez VirtualBox.

### Creation des Machines Virtuelles

Deux machines virtuelles ont été créées: **Ubuntu Server** and **Windows 11**.

#### VM Ubuntu Server

1. **Initiate une nouvelle VM:**
   - Dans VirtualBox, cliquez sur le bouton  `Nouveau` .

2. **Name and Operating System:**
   - **Nom:** ubuntu_server
   - **folder:**     
        ```bash
     C:\Users\2417034\VirtualBox VMs
     ```
   - **ISO Image:** on selection l'image iso que on a telecharger du site official de ubuntu [ubuntu server](https://ubuntu.com/download/server).
   - et on selectione 'Skip Unattended installation' pour que on insrtall le guest OS manuellement. 

3. **Allocation de resources:**
   - 2 processeurs
   - 2048Mo de mémoire.
   - 30Go de disque

5. **Finaliser:**
   - Click `Create`.

6. **Install Ubuntu Server:**
   - Démarre la VM et suivez les invites d’installation d’Ubuntu.
   - Configurez les paramètres nécessaires tels que la langue et la disposition du clavier lors de l'installation.
   - Créez un compte utilisateur avec des privilèges administratifs.

#### VM Windows 11

1. **Initiate une nouvelle VM:**
   - Dans VirtualBox, cliquez sur le bouton  `Nouveau` .

2. **Name and Operating System:**
   - **Nom:** win
   - **folder:**     
        ```bash
     C:\Users\2417034\VirtualBox VMs
     ```
   - **ISO Image:** on selection l'image iso que on a telecharger du site  [institutgrassetinfo](http://reseaux.institutgrassetinfo.com/windows\_11\_enterprise\_edition\_22h2\_x64.iso).
   - et on selectione 'Skip Unattended installation' pour que on insrtall le guest OS manuellement. 

3. **Allocation de resources:**
   - 2 processeurs
   - 3000Mo de mémoire.
   - 60Go de disque

5. **Finaliser:**
   - Click `Create`.

6. **Install Ubuntu Server:**
   - Démarre la VM et suivez les invites d’installation dans le document windows11_installation du cours system d'exploitation.

## Configuration du reseau virtuel

### Configuration des adaptateurs reseau sur le reseau interne

Les machines virtuelles Ubuntu Server et Windows 11 étaient initialement configurées sur Adaptateur `acces par pont`. Cette configuration a été remplacée par un `réseau interne` nommé `ih` (ilies harrache) pour créer un `réseau local séparé`.

1. **Acceder aux parametres de la VM:**
   - Faites un clic droit sur chaque VM dans VirtualBox et sélectionnez `Configuration`.

2. **Acceder au reseau:**
   - Cliquez sur l’onglet `Réseau`.

3. **Configurer l'adaptateur:**
   - **Adapter 1:**
     - **attaché a:** resaux interne
     - **Nome:** ih

4. **Apply Changes:**
   - Cliquez sur `OK` pour enregistrer les paramètres.
  
   - ![13](https://github.com/user-attachments/assets/62043379-ee97-46f3-83ff-f023b413e6e5)
     
   - ![14](https://github.com/user-attachments/assets/e4fb9902-bc46-45ca-b12d-78f0b59135ae)



**Pourquoi on a fait ça?:**
Pour crée un réseau `privé isolé` d'autres réseaux `externes`, facilitant la communication `sécurisée` entre les machines virtuelles.

## Configuration du Pare-feu pfSense

### Telechargement de pfSense

1. **Access au repo de pfSense:**
   
   - Accéder à [repo.ialab.dsu.edu/pfsense/](http://repo.ialab.dsu.edu/pfsense/) pour télécharger l'ISO pfSense.

3. **Download ISO:**
   - Sélectionner [pfSense-CE-2.7.2-RELEASE-amd64.iso.gz](pfSense-CE-2.7.2-RELEASE-amd64.iso.gz) et téléchargez le fichier ISO.
     
   - ![1](https://github.com/user-attachments/assets/51de48de-47fc-489c-8c72-51f6d6de646a)


### Creation de la machine virtuelle pfSense

1. **Initiate une nouvelle VM:**
   - Dans VirtualBox, cliquez sur le bouton `Nouveau`.

2. **Nom et systeme d'exploitation:**
   - **Nom:** pfSense_ih
   - **Type:** BSD
   - **Version:** FreeBSD (64-bit)

3. **Allocation de memoire et CPU:**
   - Allouer `1024 MB` de RAM.
   - `1` coeur CPU

4. **Disque Dur:**
   - Définir la taille du disque a  `40 GB`.
     
   - ![3](https://github.com/user-attachments/assets/695dc213-ba9a-4e5b-be68-00bf5cdfaa89)


5. **Adaptateurs reseau:**

    -Pour configurer un pare-feu qui peut gérer le trafic entre le réseau interne et Internet, on doit configurer 2 adaptateurs réseau :

   
   - **Adaptateur 1 (WAN):**
     - **Attacher a:** Adaptateur par pont
   - **Adaptateur 2 (LAN):**
     - **Attacher a:** Resaux interne
     - **Nom:** ih
       
     - ![4](https://github.com/user-attachments/assets/76d95e8b-b7e2-406c-8945-36c6226ea314)
       
     - ![5](https://github.com/user-attachments/assets/0a1b892f-69a0-4f06-b1db-79c0d158a810)



7. **Finaliser:**
   - Click `Create`.

8. **Install pfSense:**
   - Démarre la VM et suivez les invites d’installation de pfsense.
   - ![6](https://github.com/user-attachments/assets/a203678a-ea5e-4fdc-a895-6df094539d71)
     
   - ![7](https://github.com/user-attachments/assets/64cc598b-7117-4ec8-b3f6-e055d4b74e88)
     
   - ![8](https://github.com/user-attachments/assets/4662df7f-da75-4834-8df5-0ea78f2eb568)
     
   - ![9](https://github.com/user-attachments/assets/19a7b0bb-1b1f-4993-bf70-2b39fcd68b3e)
     
   - ![10](https://github.com/user-attachments/assets/ebbf57ab-3a54-42e8-8207-9c07e8256a5f)
     
   - noubliez paz de enlever l'image iso.
     
   - ![11](https://github.com/user-attachments/assets/a390f179-3e7e-43ab-ae80-5eced3fb40a2)







### Configuration initiale de pfSense

Apres l'installation, pfSense initialise les interfaces réseau:

- **WAN (em0):** IP attribuée `192.168.20.107/22` via DHCP.
- **LAN (em1):** IP initialement attribuée `192.168.1.1/24`.
  
- ![12](https://github.com/user-attachments/assets/6320ac8b-bce7-46b7-ab28-7309286b8564)


**Mesures prises:**

1. **Acceder à la console pfSense:**
   - Au démarrage de la VM, accédez à la console pfSense via VirtualBox.

2. **Attribuer des adresses IP d'interface:**
Pour établir la structure du réseau et activer DHCP pour l'attribution automatique d'IP aux clients, on va Attribuer des adresses IP d'interface:
   - **Configurer l'interface LAN:**
     - Définir l'adresse IPv4 statique:`10.10.10.1`.
     - entrez le nouveau nombre de bits du sous-réseau `24` 
     - Configure IPv6 via DHCP (`configure IPv6 address lan interface via DHCP6 (y/n): y`).
     - Désactiver DHCP sur IPv4 pour le LAN (`for LAN, press Enter for none`).

       
    -    nous faisons cela pour garantir que l'interface LAN pfSense dispose d'une adresse IP cohérente (10.10.10.1), servant de passerelle par défaut pour les clients internes.

3. **Enable DHCP Server dans LAN:**
   - Définir la plage DHCP IPv4:
     - **Start:** `10.10.10.100`
     - **End:** `10.10.10.199`

       
    -    nous faisons cela pour automatiser l'attribution d'adresses IP dans la plage spécifiée, facilitant ainsi la gestion du réseau.

4. **Appliquer la configuration:**
   - appuyez sur Entrée.
     
   - ![15](https://github.com/user-attachments/assets/f1d6e0a6-3224-4072-9e22-5753a95a7ad6)
     
   - ![16](https://github.com/user-attachments/assets/0d5f7b6c-ee42-4906-81b0-44288a687b79)
     
   - ![17](https://github.com/user-attachments/assets/53d2eb1c-3539-419d-9b7d-05f67fb082ba)
  
   - ![18](https://github.com/user-attachments/assets/4d6fd717-2dab-495f-b08c-5ac3500ddd1c)

5. **Resultat:**
    -Obtention des l'adresses IP:
        -`10.10.10.100` pour Windows
        -`10.10.10.101` pour ubuntu server

   - ![19](https://github.com/user-attachments/assets/6b366b4d-1d17-4e19-b778-6f81a554833f)
     
   - ![20](https://github.com/user-attachments/assets/784390bc-bb4f-470a-a6b3-153c48d06a17)



### Configuration des Mappages IP Statiques


Il faut fournire des adresses IP fixes pour les machines virtuelles, garantissant un accès cohérent et facilitant les configurations des règles de pare-feu.


1. **Acces à l'Interface Web pfSense:**
   - Dans la VM Windows 11, navigation vers `http://10.10.10.1` dans un navigateur web.
   - Identifiants utilisés:
     - **Username:** admin
     - **Password:** pfsense
       
     - ![21](https://github.com/user-attachments/assets/480c387e-f67b-4541-88d6-62ee8a0bb5d8)


2. **Accedez aux parametres du serveur DHCP:**
   - Navigation vers `Services` > `DHCP Server`.
     
   - ![22](https://github.com/user-attachments/assets/a9ab0818-d3a6-4be8-b23f-0bd1b042c16f)


3. **Ajouter un Mappage Statique:**
   - Click sur `Add Static Mapping`
     
   - ![23](https://github.com/user-attachments/assets/c9b72bbf-bce0-43d7-9afd-908eeb0bfdcd)

   - **Pour Windows 11:**
     - **Address MAC:** (Récupérer à partir de la machine virtuelle Windows à l'aide de `getmac`)
       
     - ![24](https://github.com/user-attachments/assets/a1511aef-cbff-4eee-bad2-416caae521e6)

     - **IP Address:** `10.10.10.10`
     - **Description:** win11
       
     - ![26](https://github.com/user-attachments/assets/cb5e102a-496d-49fd-b36f-cf981e6a86ff)

   - **pour Ubuntu Server:**
     - **MAC Address:** (Récupérer à partir de Ubuntu VM à l'aide de `ipconfig`)
       
     - ![25](https://github.com/user-attachments/assets/5b03a2be-93ee-428d-8c29-2e325464c4c0)

     - **IP Address:** `10.10.10.11`
     - **Description:** ubuntuserver
       
     - ![27](https://github.com/user-attachments/assets/1632a714-4cbc-4215-94b0-88e9462c2004)


4. **Appliquer la configuration:**
   - Enregistrer les mappages statiques.
     
   - ![28](https://github.com/user-attachments/assets/71243cac-9ef8-4e7f-a5d4-15be60bbbc63)


5. **Renouveler les adresses IP sur les VMs:**
   - **Windows 11 VM:**
     - Ouvrire cmd.
     - Executer `ipconfig /release` suivi de `ipconfig /renew`.
     - Confirmer que l'adresse IP est maintenant `10.10.10.10`.
       
     - ![29](https://github.com/user-attachments/assets/b9c0ccff-7b2d-4dc9-9990-64529cb00b93)

   - **Ubuntu Server VM:**
     - Ouvrire le Terminal.
     - Executer `sudo dhclient -v -r` pour libérer l'IP.
     - Dans mon cas `dhclient` nest pas présent donc jai installer avec (`sudo apt install dhclient`).
       
     - ![30](https://github.com/user-attachments/assets/dfafc7de-ff61-4b21-a817-4ea392c44ece)
       
     - ![31](https://github.com/user-attachments/assets/fc515822-9705-4444-abf6-82ae0734eb3c)


     - Execute `sudo dhclient -v` pour renouveler l'IP.
     -  Confirmer que l'adresse IP est maintenant `10.10.10.11`.
       
     -  ![32](https://github.com/user-attachments/assets/b80d0ce3-04a6-4edf-a22d-d9ab5c5a0db3)


## Configuration des Regles de Pare-feu

maintenant, il faut ouvrir des ports spécifiques dans pfSense pour pouvoir accéder à des services comme SSH (pour putty), HTTP (pour le site Web) et RDP (pour le bureau à distance). Sans ouvrir ces ports, les services des machines virtuelles ne seraient pas accessibles depuis la machine physique ou les réseaux externes.

1. **Acces aux Parametres du Pare-feu pfSense:**
    - Navigation vers `Pare-feu` > `NAT` dans l'interface web pfSense
      
    - ![33](https://github.com/user-attachments/assets/e41b2fc7-b29f-4f0e-8812-f135c2e5991e)

    - **Ouverture du Port SSH:**
Pour permettre l'accès SSH à l'Ubuntu Server depuis l'extérieur du réseau on doit ouvrire le port `22` (`ssh`)
        - Clic sur `Ajouter` pour créer une nouvelle règle
        - Interface: `WAN`
        - Protocole: `TCP`
        - Définition de la Plage de Ports de Destination : De `SSH` à `SSH`
        - Metre l'IP Cible de Redirection à `10.10.10.11` (Ubuntu Server)
        - Définition de l'Association de Règle de Filtrage à `Passer`
          
        - ![34](https://github.com/user-attachments/assets/c9e6ab05-698b-4519-8284-e9f0764a4672)

    - **Ouverture du Port HTTP:**
Pour permettre l'accès web à l'Ubuntu Server on doit ouvrire le port `80` (`HTTp`)
        - Répétition des étapes ci-dessus, mais pour le port HTTP
          
        - ![37](https://github.com/user-attachments/assets/d8e800c9-86e0-4da5-8b8f-11bd3aef0bfa)

    - **Ouverture du Port HTTP:**
Pour permettre l'accès bureau à distance à la VM Windows 11 on doit ouvrire le port `3390` (`MSRDP`)
        - Répétition des étapes, mais pour le port MSRDP et ciblant 10.10.10.10 (Windows 11)
          
        - ![35](https://github.com/user-attachments/assets/3db4dc31-d267-4b3c-9dca-a8637435b5b5)

![39](https://github.com/user-attachments/assets/fc438139-c28b-4dcd-a3e2-495369b51bc2)



## Securisation de l'Interface Web pfSense

Pour améliorer la sécurité, l'interface Web pfSense a été sécurisée à l'aide de HTTPS avec des certificats personnalisés.


![40](https://github.com/user-attachments/assets/25be04ef-794d-49af-9a54-209f44025a6c)


### Creation d'autorites de certification et de certificats

1. **Acceder à l'interface Web de pfSense:**
   - Accéder à `http://10.10.10.1`.
   - Connection avec les identifiants d'administrateur (`admin`, `pfsense`).

2. **Acceder aux certificats:**
   - Aller à `System` > `Cert. Manager` > `CAs`.
     
   - ![41](https://github.com/user-attachments/assets/888e4bf4-1431-43b4-bc17-c184ef56dc13)


3. **Creer une autorite de certification racine (CA):**
   - Click `Add`.
   - **Descriptive Name:** `IH_autorite_de_certification`
   - **Method:** Create an internal Certificate Authority.
   - **Key type:** RSA
   - **Key Length:** 2048 bits.
   - **Digest Algorithm:** SHA256.
   - **Lifetime (days):** 3650
   - **Common Name:** `IH_autorite_de_certification`.
   - Click `Save`.
     
   - ![42](https://github.com/user-attachments/assets/5f7ce05b-466f-476d-9113-46ee1c12ff85)
     
   - ![43](https://github.com/user-attachments/assets/30739b8b-b06a-406e-b9fa-18b3a7add894)



4. **Creer une autorite de certification intermediaire:**
   - Aller à `System` > `Cert. Manager` > `Certificates`.
   - Click `Add`.
   - **Descriptive Name:** `IH_autorite_intermediaire_de_certification`.
   - **Method:** Create an intermediate Certificate Authority.
   - **Signing CA:** Selectioner `IH_autorite_de_certification`.
   - **Key type:** RSA
   - **Key Length:** 2048 bits.
   - **Digest Algorithm:** SHA256.
   - **Lifetime:** Default (3650).
   - **Common Name:** `IH_autorite_intermediaire_de_certification`.
     
   - ![44](https://github.com/user-attachments/assets/43def03d-8c0e-4bcd-857b-b3f0dc199c1f)
     
   - ![45](https://github.com/user-attachments/assets/ab159a80-c98f-45b7-a76d-75ee1cbf8bd3)


   
   - Click `Save`.

5. **Creer un certificat de serveur:**
   - Aller à `System` > `Cert. Manager` > `Certificates`.
   - Click `Add`.
   - **Descriptive Name:** `pfsenseih.grasset`.
   - **Method:** Create an internal Certificate.
   - **Certificate Authority:** Selectioner `IH_autorite_intermediaire_de_certification`.
   - **Key type:** RSA
   - **Key Length:** 2048 bits.
   - **Digest Algorithm:** SHA256.
   - **Common Name:** `pfsenseih.grasset`.
   - **Lifetime:** Default (3650).
   - Click `Save`.
     
   - ![46](https://github.com/user-attachments/assets/113a80d8-3e06-4c58-9475-6b09cf2ae48a)
     
   - ![47](https://github.com/user-attachments/assets/784f6414-b1c9-4afd-bcf0-a60f9eb307a8)



**Pourquoi les certificats?:**
- **Certificate Authorities (CA):** Établissez une hiérarchie de confiance, permettant des connexions SSL/TLS sécurisées.
- **Intermediate CA:** Ajoute une couche de sécurité supplémentaire, en isolant l'autorité de certification racine.
- **Server Certificate:** Facilite l'accès HTTPS à l'interface Web pfSense.

### Importation de le CA Racine dans Windows
Pour que Windows fasse confiance aux certificats auto-signés il faut les importee a windows
1. **Exporter le certificat d'autorite de certification racine:**
   - dans pfSense, alle a `System` > `Cert. Manager` > `CAs`.
   - Click `Export` button pres de `IH_autorite_de_certification`.
   - Enregistrer le fichier de certificat ( `IH_autorite_de_certification.pem`).
     
   - ![49](https://github.com/user-attachments/assets/f2969c98-cd2b-4ab6-8d31-8cc4219f4d3f)


3. **Importer un certificat dans la racine de confiance:**
   - ovrire `MMC` (Microsoft Management Console):
     - Appuyer sur `Win + R`, tapez `mmc`, et appuyer `Entrée`.
   - Ajoutez le Snap-in du Certificates :
     - Allez a `fichier` > `ajouter/suprimer un composant logiciel enfichable...`.
     - Selectioner `Certificates` et click `Add`.
   - Accéder à `Trusted Root Certification Authorities` > `Certificates`.
   - clic droit et sélectionnez `All Tasks` > `Import`.
   - Select `IH_autorite_de_certification.pem`.
   - Confirmez que le certificat est répertorié sous `Trusted Root Certification Authorities`.
     
   - ![48](https://github.com/user-attachments/assets/da8d1e6d-b70f-44f6-b49f-20c67586045a)


de cette manière, nous garantissons que les connexions HTTPS à pfSense sont reconnues comme sécurisées par Windows 11, éliminant ainsi les avertissements du navigateur.

### Configuration HTTPS pour pfSense

Pour chiffrer le trafic vers l'interface web pfSense et améliorer la sécurité on doit configuration HTTPS pour pfSense

1. **Acceder a l'interface Web de pfSense:**
   - Accéder à `http://10.10.10.1`.
   - Connection avec les identifiants d'administrateur (`admin`, `pfsense`).

2. **Accedez aux parametres WebConfigurator:**
   - allez a `System` > `Advanced` > `Admin Access`.
     
   - ![50](https://github.com/user-attachments/assets/3caf6129-b2c1-430a-8766-144b86a95fd5)


3. **Configurer HTTPS:**
   - **Protocol:** Sélectionner `HTTPS` (SSL/TLS).
   - **SSL/TLS Certificate:** Choisir `pfsenseih.grasset`.
     
   - ![51](https://github.com/user-attachments/assets/763bc370-62d9-41f2-9735-9b33aa62fc39)


4. **autres parametres:**
   - **Enable WebConfigurator Login Autocomplete:** `Checked`.
   - **Allow GUI Administrator Client IP Address to Change During a Login Session:** `Checked`.
   - **Disable DNS Rebinding Checks:** `Checked`.
   - **Disable HTTP_REFERER Enforcement Check:** `Checked`.
     
   - ![52](https://github.com/user-attachments/assets/45f5f20f-bd1c-45a8-b61f-3d99d932d18c)
     
   - ![53](https://github.com/user-attachments/assets/2cab69d8-7893-4a62-8db9-22ca4e5fabc8)



5. **Enregistrer et appliquer:**
   - Click `Save` pour appliquer les paramètres.

Maintenant que l'interface WebConfigurator est Sécurise, on est protéger contre les écoutes clandestines (eavesdropping) et les attaques de l'homme du milieu (man-in-the-middle).


![54](https://github.com/user-attachments/assets/bfb3086b-262f-4b4e-a868-4d5242ad3ddb)


### Mise à Jour du Fichier Hosts de Windows

1. **Modifier le fichier Hosts sur Windows 11:**
   - Accéder à `C:\Windows\System32\drivers\etc\`.
     
   - ![55](https://github.com/user-attachments/assets/c41044d1-e508-4f70-9d51-f76ac61abdab)
     
   - Right-click sur `hosts` et sélectionnez `Ouvrir avec le Bloc-notes` (Exécuter le Bloc-notes en tant qu’administrateur).

2. **ajouter pfSense Host Entry:**
   - Ajoutez la ligne suivante:
     ```
     10.10.10.1 pfsenseih.grasset
     ```
   - Enregistrer le fichier.
     
   - ![56](https://github.com/user-attachments/assets/5380e8d1-e1fc-45b0-add6-a205923f78a3)


3. **Acceder à pfSense via Hostname:**
   - ouvrire Google Chrome.
   - aller a `https://pfsenseih.grasset`.
   - le WebConfigurator de pfSense doit se charger en toute sécurité sans avertissement de certificat.
     
   - ![57](https://github.com/user-attachments/assets/6cfab316-f8af-4a66-a2a8-715e1baa9599)


maintenant nous avons simplifié l'accès en autorisant l'utilisation d'un nom d'hôte convivial au lieu de l'adresse IP

## Configuration du serveur Web

### Installation du LAMP Stack (Linux, apach, Mysql, php)

1. **Acceder au serveur Ubuntu:**
   - ouvrire putty.
   - entrez ladress WAN de pfsense.

2. **Mettre à jour les Package Lists:**
   ```bash
   sudo apt update
   sudo apt upgrade
   ```

3. **Installez Apache2, MariaDB Server, et PHP:**
   ```bash
   sudo apt install apache2 mariadb-server php
   ```
   
   ![58](https://github.com/user-attachments/assets/94891c65-9d2f-4bc1-8053-f873225d9cea)


4. **Verifier l'installation:**
   - Vérifier l'état d'Apache2:
     ```bash
     sudo systemctl status apache2
     ```
   - Assurez-vous que MariaDB est en cours d'exécution:
     ```bash
     sudo systemctl status mariadb
     ```
   - Confirmer l'installation de PHP:
     ```bash
     php -v
     ```
![94](https://github.com/user-attachments/assets/d5686281-e489-4d0e-9a43-7269db4d1f0a)


![95](https://github.com/user-attachments/assets/616475cb-4031-42ea-8195-2ab0c097a94d)


![96](https://github.com/user-attachments/assets/9c70a9e2-57e2-42ed-bc64-ddc4ae5ad17e)

     
### Configuration d'Apache pour le site Web

1. **Tester l'installation d'Apache:**
   - Dans ubuntu exécutez:
     ```bash
     wget localhost
     ```
     
     ![60](https://github.com/user-attachments/assets/76c3f7e8-2e7f-43b4-819f-e92a7e3e123d)

   - Dans la machine physique, accès à http://192.168.20.107 (IP WAN pfSense).
   - Output doit afficher le contenu HTML par défaut d'Apache.
     
   - ![61](https://github.com/user-attachments/assets/fea56ecb-66e4-43d3-806e-a635fa212039)


2. **Creer une structure de repertoire de site Web:**
   ```bash
   sudo mkdir -p /var/www/tpiliesharrache/public_html
   ```
3. **Creer des fichiers de site Web:**
   - Accéder au répertoire du site Web:
     ```bash
     cd /var/www/tpiliesharrache/public_html
     ```
     
     ![62](https://github.com/user-attachments/assets/49aedd0e-0dc7-475c-adef-9496d28dc9e7)

   - Créer `index.html`:
     
     ```bash
     sudo vim index.html
     ```
     - ajouter le code html fourni par le prof.
   - Créer `script.js`:
     
     ```bash
     sudo vim script.js
     ```
     - ajouter le code JavaScript fourni par le prof.
   - Créer `style.css`:
     
     ```bash
     sudo vim style.css
     ```
     - ajouter le code css fourni par le prof .
       
   - ![64](https://github.com/user-attachments/assets/f6b2709a-2df8-41e4-93b6-4b9c233c20aa)


5. **Configuration de l'Hote Virtuel Apache:**

Il faut configurer Apache pour servir le site web personnalisé.

   - Copier la configuration par défaut:
     
     ```bash
     sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/tpiliesharrache.conf
     ```
     
     ![65](https://github.com/user-attachments/assets/f4a1cd67-c30c-4561-bb81-2c6784cfb88b)

   - Edit la nouvelle configuration:
     ```bash
     sudo vim /etc/apache2/sites-available/tpiliesharrache.conf
     ```
     
     ![66 0](https://github.com/user-attachments/assets/e17bf6d6-51e3-4871-973b-ae1a823676e3)

     - **Change `ServerName`:**
       ```
       ServerName tpiliesharrache.grasset
       ```
     - **Change `DocumentRoot`:**
       ```
       DocumentRoot /var/www/tpiliesharrache/public_html
       ```
   - Enregistrer et quitter (`:wq`).
     
   - ![66 1](https://github.com/user-attachments/assets/6224e5bd-e018-4663-9cd8-b203063fed3d)


6. **Activer le nouveau site et reload Apache:**
   
   ```bash
   sudo a2ensite tpiliesharrache.conf
   sudo systemctl reload apache2
   ```
   
   ![67](https://github.com/user-attachments/assets/263fbcfb-54e1-4f23-943e-cbf8ac6fcd3a)
   
   ![68](https://github.com/user-attachments/assets/a9844456-fbfb-4e14-8ca9-b2e2feacd153)



**un Custom Virtual Host:** Permet d'héberger plusieurs sites Web sur le même serveur, chacun avec son propre nom de domaine et son propre répertoire.

### Tester le serveur Web

1. **Mise à Jour du Fichier Hosts de la Machine Physique:**
   - Accéder à `C:\Windows\System32\drivers\etc\`.
   - Ouvrez `hosts` avec le Bloc-notes en tant qu’administrateur.
   - Ajoutez la ligne suivante:
     ```
     192.168.20.107 tpiliesharrache.grasset
     ```
   - Enregistrer le fichier.
     
   - ![69](https://github.com/user-attachments/assets/04491a87-0032-4fa2-b9d2-a1f97bf0426b)


2. **Acceder au site Web:**
   - Sur la machine physique, ouvrez Google Chrome.
   - Accéder à `http://tpiliesharrache.grasset`.
   - Le site Web personnalisé doit s'afficher tel que configuré.

maintenant, nous dirigeons le domaine `tpiliesharrache.grasset` vers l'IP WAN pfSense (`192.168.20.107`), permettant l'accès externe au site Web hébergé.

![70](https://github.com/user-attachments/assets/dbe1ada6-ed36-45e6-a14d-d67162993776)


## Test final et verification

1. **SSH a Ubuntu Server:**
     - Ouvrir PuTTY sur Windows 10 (physique).
     - Connect a `192.168.20.107` via SSH.
2. **MSRDP Remote Desktop a Windows 10:**
     - Utilisez la connexion Bureau à distance sur Windows physique .
     - Connect a `192.168.20.107`.
3.  **HTTP:** 
    - Accéder à `http://tpiliesharrache.grasset` avec un navigateure dans la machine physique.



## Conclusion

Cette mission a démontré avec succès la création d'un environnement réseau sécurisé et virtualisé à l'aide de VirtualBox et de pfSense. En configurant méticuleusement les paramètres réseau, en établissant des règles de pare-feu, en sécurisant les interfaces administratives avec des certificats SSL/TLS et en déployant un serveur Web, un réseau local fonctionnel et sécurisé a été établi. Le site Web hébergé est accessible à partir de la machine physique, ce qui démontre des compétences efficaces en matière de virtualisation et de gestion de réseau.

### Flux d'information, Architecture client-serveur et API


- **Client** : Le code JavaScript s'exécute dans le navigateur de l'utilisateur sur la machine physique.
- **Serveur** : Le serveur Apache sur Ubuntu sert les fichiers statiques (HTML, CSS, JS).
- **API externe** : L'application fait des requêtes à l'API Ghibli (https://ghibliapi.vercel.app/films) pour récupérer les données des films.

Le flux de trafic réseau est le suivant :

1. **Requête initiale du client** :
   - L'utilisateur entre l'URL http://tpiliesharrache.grasset dans son navigateur sur la machine physique.
   - Le navigateur résout le nom de domaine en utilisant le fichier hosts modifié, qui pointe vers l'adresse IP WAN de pfSense (192.168.20.107).

2. **Traitement par pfSense** :
   - La requête HTTP arrive sur l'interface WAN de pfSense.
   - pfSense examine la requête et applique la règle NAT correspondante.
   - La requête est redirigée vers l'adresse IP interne du serveur Ubuntu (10.10.10.11) sur le port 80.

3. **Traitement par le serveur Ubuntu** :
   - Apache reçoit la requête HTTP sur le port 80.
   - Le VirtualHost correspondant à "tpiliesharrache.grasset" est sélectionné.
   - Apache sert les fichiers statiques (HTML, CSS, JS) depuis le répertoire /var/www/tpiliesharrache/public_html.

4. **Exécution du code JavaScript** :
   - Le navigateur reçoit et exécute le code JavaScript.
   - Le JavaScript initie une requête XMLHttpRequest vers https://ghibliapi.vercel.app/films.

5. **Nouvelle requête vers l'API externe** :
   - Cette requête passe à nouveau par pfSense, mais cette fois en tant que trafic sortant.
   - pfSense applique la translation d'adresse source (SNAT) pour permettre la communication avec Internet.

6. **Réponse de l'API Ghibli** :
   - L'API Ghibli répond avec les données JSON des films.
   - La réponse traverse Internet et arrive à pfSense.

7. **Retour de la réponse au client** :
   - pfSense applique la translation d'adresse de destination inverse (DNAT) et renvoie la réponse au serveur Ubuntu.
   - Le serveur Ubuntu transmet la réponse au client via pfSense.

8. **Traitement final par le navigateur** :
   - Le JavaScript dans le navigateur reçoit les données JSON.
   - Il traite ces données et met à jour le DOM pour afficher les informations des films.

### Protocoles utilisés 

1. **DNS (Domain Name System)** :
   - Utilisé normalement pour résoudre les noms de domaine en adresses IP.
   - Dans notre cas, remplacé par une entrée statique dans le fichier hosts pour tpiliesharrache.grasset.
   - Toujours utilisé pour résoudre ghibliapi.vercel.app lors de l'appel à l'API.

2. **HTTP (Hypertext Transfer Protocol)** :
   - Version : HTTP/1.1
   - Utilisé pour la communication entre le navigateur et le serveur Apache.
   - Méthodes utilisées : GET (pour récupérer les fichiers statiques et les données de l'API)
   - En-têtes importants :
     - Host: tpiliesharrache.grasset
     - User-Agent: [Information sur le navigateur]
     - Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8

3. **HTTPS (HTTP Secure)** :
   - Utilisé pour la communication sécurisée avec l'API Ghibli.
   - Protocole sous-jacent : TLS (Transport Layer Security)
   - Assure le chiffrement des données échangées avec l'API.

4. **TCP (Transmission Control Protocol)** :
   - Protocole de transport utilisé par HTTP et HTTPS.
   - Assure une transmission fiable et ordonnée des paquets.
   - Ports utilisés : 80 (HTTP), 443 (HTTPS)

5. **IP (Internet Protocol)** :
   - Version : IPv4
   - Gère l'adressage et le routage des paquets entre les différents réseaux.

6. **ARP (Address Resolution Protocol)** :
   - Utilisé dans le réseau local pour mapper les adresses IP aux adresses MAC.

7. **SSH (Secure Shell)** :
   - Version : SSH-2
   - Utilisé pour l'accès sécurisé à distance au serveur Ubuntu (PuTTy).
   - Port par défaut : 22

8. **RDP (Remote Desktop Protocol)** :
   - Utilisé pour l'accès à distance à la machine virtuelle Windows 11.
   - Port par défaut : 3389

9. **DHCP (Dynamic Host Configuration Protocol)** :
   - Utilisé par pfSense pour attribuer des adresses IP aux machines du réseau interne.
   - Plage d'adresses configurée : 10.10.10.100 - 10.10.10.199

10. **NTP (Network Time Protocol)** :
    - Utilisé pour synchroniser l'horloge des différentes machines du réseau.
    - Important pour la validité des certificats SSL/TLS et la cohérence des logs.

## Analyse approfondie du protocole HTTP

Le protocole HTTP est crucial pour notre application. Voici une analyse détaillée d'une requête HTTP à l'API Ghibli :
GET /films HTTP/1.1
Host: ghibliapi.vercel.app
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/93.0.4577.63 Safari/537.36
Accept: /
Origin: http://tpiliesharrache.grasset
Referer: http://tpiliesharrache.grasset/

Cette requête GET demande la liste des films à l'API. La réponse contiendra un tableau JSON avec les détails des films.

[ capture Wireshark ]

### Configuration NAT de pfSense

La configuration NAT (Network Address Translation) sur pfSense permet de rediriger le trafic entrant vers les serveurs internes :

- Port 80 (HTTP) redirigé vers 10.10.10.11:80 (serveur Ubuntu)
- Port 22 (SSH) redirigé vers 10.10.10.11:22 (accès SSH à Ubuntu)
- Port 3389 (RDP) redirigé vers 10.10.10.10:3389 (accès RDP à Windows 11)

Cette configuration permet d'exposer des services internes de manière sécurisée, en n'ouvrant que les ports nécessaires sur l'interface WAN.

### Virtualisation réseau

Les adaptateurs réseau de VirtualBox créent un réseau virtuel isolé :

- **Adaptateur 1 (WAN) de pfSense** : Configuré en mode "Bridged", il obtient une IP du réseau physique.
- **Adaptateur 2 (LAN) de pfSense et adaptateurs des VMs** : Configurés en mode "Internal Network", ils créent un réseau isolé nommé "ih".

Cette configuration permet d'isoler le réseau virtuel du réseau physique, avec pfSense agissant comme passerelle entre les deux.

### Apache VirtualHost

La configuration VirtualHost d'Apache permet de servir différents sites basés sur le nom de domaine demandé. Notre configuration :

```apache
<VirtualHost *:80>
    ServerName tpiliesharrache.grasset
    DocumentRoot /var/www/tpiliesharrache/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
### Modifications de l'application pour le stockage local et l'accès hors ligne

## 1. Ajout de la fonctionnalité de stockage local

Nous allons utiliser IndexedDB pour stocker les données de l'API et le logo localement. Voici les modifications à apporter à votre code JavaScript :

```javascript
let db;

const dbName = "GhibliDB";
const storeName = "films";
const logoStoreName = "logo";

// Fonction pour initialiser la base de données
function initDB() {
  const request = indexedDB.open(dbName, 1);
  
  request.onerror = function(event) {
    console.error("Erreur d'ouverture de la base de données");
  };

  request.onsuccess = function(event) {
    db = event.target.result;
    loadData();
  };

  request.onupgradeneeded = function(event) {
    db = event.target.result;
    db.createObjectStore(storeName, { keyPath: "id" });
    db.createObjectStore(logoStoreName, { keyPath: "id" });
  };
}

// Fonction pour charger les données
function loadData() {
  getLogoFromDB();
  getFilmsFromDB();
}

// Fonction pour récupérer le logo de la base de données
function getLogoFromDB() {
  const transaction = db.transaction([logoStoreName], "readonly");
  const objectStore = transaction.objectStore(logoStoreName);
  const request = objectStore.get("logo");

  request.onerror = function(event) {
    console.error("Erreur de récupération du logo");
  };

  request.onsuccess = function(event) {
    if (request.result) {
      displayLogo(request.result.src);
    } else {
      fetchAndStoreLogo();
    }
  };
}

// Fonction pour récupérer et stocker le logo
function fetchAndStoreLogo() {
  fetch('https://taniarascia.github.io/sandbox/ghibli/logo.png')
    .then(response => response.blob())
    .then(blob => {
      const reader = new FileReader();
      reader.onloadend = function() {
        const logoData = { id: "logo", src: reader.result };
        storeLogo(logoData);
        displayLogo(reader.result);
      }
      reader.readAsDataURL(blob);
    })
    .catch(error => console.error('Erreur de récupération du logo:', error));
}

// Fonction pour stocker le logo dans IndexedDB
function storeLogo(logoData) {
  const transaction = db.transaction([logoStoreName], "readwrite");
  const objectStore = transaction.objectStore(logoStoreName);
  const request = objectStore.put(logoData);

  request.onerror = function(event) {
    console.error("Erreur de stockage du logo");
  };
}

// Fonction pour afficher le logo
function displayLogo(src) {
  const logo = document.createElement('img');
  logo.src = src;
  app.appendChild(logo);
}

// Fonction pour récupérer les films de la base de données
function getFilmsFromDB() {
  const transaction = db.transaction([storeName], "readonly");
  const objectStore = transaction.objectStore(storeName);
  const request = objectStore.getAll();

  request.onerror = function(event) {
    console.error("Erreur de récupération des films");
  };

  request.onsuccess = function(event) {
    if (request.result.length > 0) {
      displayFilms(request.result);
    } else {
      fetchAndStoreFilms();
    }
  };
}

// Fonction pour récupérer et stocker les films
function fetchAndStoreFilms() {
  fetch('https://ghibliapi.vercel.app/films')
    .then(response => response.json())
    .then(data => {
      storeFilms(data);
      displayFilms(data);
    })
    .catch(error => {
      console.error('Erreur de récupération des films:', error);
      displayErrorMessage();
    });
}

// Fonction pour stocker les films dans IndexedDB
function storeFilms(films) {
  const transaction = db.transaction([storeName], "readwrite");
  const objectStore = transaction.objectStore(storeName);
  
  films.forEach(film => {
    const request = objectStore.put(film);
    request.onerror = function(event) {
      console.error("Erreur de stockage du film");
    };
  });
}

// Fonction pour afficher les films
function displayFilms(films) {
  const container = document.createElement('div');
  container.setAttribute('class', 'container');

  films.forEach(movie => {
    const card = document.createElement('div');
    card.setAttribute('class', 'card');

    const h1 = document.createElement('h1');
    h1.textContent = movie.title;

    const p = document.createElement('p');
    movie.description = movie.description.substring(0, 300);
    p.textContent = `${movie.description}...`;

    container.appendChild(card);
    card.appendChild(h1);
    card.appendChild(p);
  });

  app.appendChild(container);
}

// Fonction pour afficher un message d'erreur
function displayErrorMessage() {
  const errorMessage = document.createElement('marquee');
  errorMessage.textContent = `Une erreur est survenue`;
  app.appendChild(errorMessage);
}

// Initialiser la base de données au chargement de la page
initDB();
```
## 2. Modifications apportées

1. **Utilisation d'IndexedDB :**
   
   - Nous avons ajouté une base de données locale pour stocker les films et le logo.
     
2. **Gestion du logo :**

   - Le logo est maintenant récupéré depuis la base de données locale s'il existe.
Si le logo n'est pas en cache, il est téléchargé, stocké dans IndexedDB, puis affiché.


3. **Gestion des films :**

   - Les films sont d'abord recherchés dans la base de données locale.
S'ils ne sont pas présents, l'application tente de les récupérer depuis l'API et les stocke localement.


4. **Fonctionnement hors ligne :**

   - Si l'application ne peut pas se connecter à l'API, elle utilisera les données stockées localement.
Un message d'erreur s'affiche uniquement si aucune donnée n'est disponible localement et que l'API est inaccessible.


5. **Optimisation des performances :**

   - Les données sont chargées une seule fois depuis l'API, puis stockées localement.
Les chargements ultérieurs seront plus rapides car les données seront récupérées depuis IndexedDB.

![93](https://github.com/user-attachments/assets/6b0b2c43-1d0a-4c11-a2f2-18b1aeafdaa1)


![90](https://github.com/user-attachments/assets/4392e3f5-7778-4588-bcf2-abcf713c7145)


![91](https://github.com/user-attachments/assets/587db6ce-8ee3-4d76-b89e-74f39c137091)


![92](https://github.com/user-attachments/assets/6475ef32-813a-47fc-8046-3538333990ca)


## 2.  pourquoi cette modification? (Avantages de cette modification)

   - **Résilience :** L'application fonctionne même sans connexion Internet une fois les données chargées.
   - **Performance :** Les temps de chargement sont réduits après la première visite.
   - **Économie de bande passante :** Les données ne sont téléchargées qu'une seule fois.


## Appendix

### Commands Utuliser

```bash
sudo apt update
sudo apt upgrade
getmac
ip addr
ipconfig
ipconfig /release
ipconfig /renew
sudo apt install dhclient
sudo dhclient -v -r
sudo dhclient -v
sudo apt install apache2 mariadb-server php
sudo systemctl status apache2
sudo systemctl status mariadb
php -v
wget localhost
sudo mkdir -p /var/www/tpiliesharrache/public_html
cd /var/www/tpiliesharrache/public_html
sudo vim index.html
sudo vim script.js
sudo vim style.css
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/tpiliesharrache.conf
sudo vim /etc/apache2/sites-available/tpiliesharrache.conf
sudo a2ensite tpiliesharrache.conf
sudo systemctl reload apache2
```

