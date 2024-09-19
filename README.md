
# Networking Assignment Report

# Table des matieres

1. [Introduction](#introduction)
2. [Prerequis](#prerequis)
3. [Configuration de l'environnement virtuel](#configuration-de-lenvironnement-virtuel)
   - [Installation de VirtualBox](#installation-de-virtualbox)
   - [Creation des machines virtuelles](#creation-des-machines-virtuelles)
     - [VM Ubuntu Server](#vm-ubuntu-server)
     - [VM Windows 11](#vm-windows-11)
4. [Configuration du reseau virtuel](#configuration-du-reseau-virtuel)
   - [Configuration des adaptateurs reseau sur le reseau interne](#configuration-des-adaptateurs-reseau-sur-le-reseau-interne)
5. [Configuration du pare-feu pfSense](#configuration-du-pare-feu-pfsense)
   - [Telechargement de pfSense](#telechargement-de-pfsense)
   - [Creation de la machine virtuelle pfSense](#creation-de-la-machine-virtuelle-pfsense)
   - [Configuration initiale de pfSense](#configuration-initiale-de-pfsense)
   - [Configuration des mappages IP statiques](#configuration-des-mappages-ip-statiques)
6. [Configuration des regles de pare-feu](#configuration-des-regles-de-pare-feu)
7. [Securisation de l'interface Web pfSense](#securisation-de-linterface-web-pfsense)
   - [Creation d'autorites de certification et de certificats](#creation-dautorites-de-certification-et-de-certificats)
   - [Importation du certificat CA racine dans Windows](#importation-du-certificat-ca-racine-dans-windows)
   - [Configuration HTTPS pour pfSense](#configuration-https-pour-pfsense)
   - [Mise a jour du fichier Hosts de Windows](#mise-a-jour-du-fichier-hosts-de-windows)
8. [Configuration du serveur Web](#configuration-du-serveur-web)
   - [Installation du LAMP Stack](#installation-du-lamp-stack)
   - [Configuration d'Apache pour le site Web](#configuration-dapache-pour-le-site-web)
   - [Tester le serveur Web](#tester-le-serveur-web)
9. [Test final et verification](#test-final-et-verification)
10. [Finalement](finalement)
11. [Flux d'information, Architecture client-serveur et API](#flux-dinformation-architecture-client-serveur-et-api)
11. [schema-reseau](#schema-reseau)
11. [Protocoles utilises](#protocoles-utilises)

11. [Analyse approfondie du protocole HTTP](#analyse-approfondie-du-protocole-http)
11. [Modifications de l'application pour le stockage local et l'acces hors ligne](#Modifications-de-lapplication-pour-le-stockage-local-et-lacces-hors-ligne)
      - [Ajout de la fonctionnalite de stockage local](#ajout-de-la-fonctionnalite-de-stockage-local)
      - [Modifications apportees](#modifications-apportees)
      - [pourquoi cette modification? (Avantages de cette modification)](#pourquoi-cette-modification?-(Avantages-de-cette-modification))

13. [Appendix](#Appendix)
    - [Commandes utilisees](#commandes-utuliser)
---

## Introduction


dans ce rapport on va cree un reseau local securise a laide de virtualBox, pfSense, ubuntu server (vm) rt windows 11 (vm). Lobjectife est de cree un environnement virtuelle dans lequele un site web est heberge sur un serveur Ubuntu est accesible depuis notre machine physique.

## Prerequis

- Une machine physique avec des ressources suffisantes (RAM, CPU, stockage) pour heberger plusieurs machines virtuelles.
- Une connexion Internet stable pour telecharger les logiciels et les packages logiciels.

## Configuration de l'Environnement Virtuel

### Installation de VirtualBox

1. **Telecharger VirtualBox:**
   - acceder a la page [VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads)
   - selectionnez la version appropriee pour notre systeme dexploitation (Windows dans mon cas).

2. **Install VirtualBox:**
   - executez le programme dinstallation telecharger.
   - suiver les instructions a l'ecran et accepter les parametres par defaut.
   - quand linstallation est terminer on lance virtualbox.

### Creation des Machines Virtuelles

on va cree deux machine virtuelles: **Ubuntu Server** et **Windows 11**.

#### VM Ubuntu Server

1. **Initiate une nouvelle VM:**
   - dans virtualbox, on click sur le bouton  `Nouveau` .

2. **Nom et systeme dexploitation:**
   - **Nom:** ubuntu_server
   - **folder:**     
        ```bash
     C:\Users\2417034\VirtualBox VMs
     ```
   - **ISO Image:** on selection l'image iso que on a telecharger du site official de ubuntu [ubuntu server](https://ubuntu.com/download/server).
   - et on selectione 'Skip Unattended installation' pour que on install le guest OS manuellement. 

3. **Allocation de resources:**
   - 2 processeurs
   - 2048Mo de memoire.
   - 30Go de disque

5. **Finaliser:**
   - click `Create`.

6. **install Ubuntu Server:**
   - on demarre la VM et on suit les invites dinstallation de Ubuntu.
   - Configurez les parametres comme la langue et la disposition du clavier.
   - Creer un compte utilisateur nomee ilies avec un mot de pass 123.

#### VM Windows 11

1. **Initiate une nouvelle VM:**
   - Dans VirtualBox, cliquez sur le bouton  `Nouveau` .

2. **Nom et systeme dexploitation:**
   - **Nom:** win
   - **folder:**     
        ```bash
     C:\Users\2417034\VirtualBox VMs
     ```
   - **ISO Image:** on selection l'image iso que on a telecharger du site  [institutgrassetinfo](http://reseaux.institutgrassetinfo.com/windows\_11\_enterprise\_edition\_22h2\_x64.iso).
   - et on selectione 'Skip Unattended installation' pour que on insrtall le guest OS manuellement. 

3. **Allocation de resources:**
   - 2 processeurs
   - 3000Mo de memoire.
   - 60Go de disque

5. **Finaliser:**
   - Click `Create`.

6. **Install windows 11:**
   - demarre la VM et suivere les invites dinstallation dans le document windows11_installation du cours system d'exploitation.

## Configuration du reseau virtuel

### Configuration des adaptateurs reseau sur le reseau interne

les machines virtuelles Ubuntu Server et Windows 11 sont initialement configurees sur adaptateur `acces par pont`. on a remplacee cette configuration par un `reseau interne` nomme `ih` (ilies harrache) pour creer un `reseau local separe`.

1. **Acceder aux parametres de la VM:**
   - faites un clic droit sur les deux vm dans VirtualBox et selectionnez `configuration`.

2. **Acceder au reseau:**
   - cliquez sur longlet `Reseau`.

3. **configurer ladaptateur:**
   - **Adapter 1:**
     - **attache a:** resaux interne
     - **Nome:** ih

4. **Apply Changes:**
   - cliquez sur `OK` pour enregistrer les parametres.
  
   - ![13](https://github.com/user-attachments/assets/62043379-ee97-46f3-83ff-f023b413e6e5)
     
   - ![14](https://github.com/user-attachments/assets/e4fb9902-bc46-45ca-b12d-78f0b59135ae)



**Pourquoi on a fait ça?:**
pour cree un reseau `prive isole` dautres reseaux `externes`.

## Configuration du Pare-feu pfSense

### Telechargement de pfSense

1. **Access au repo de pfSense:**
   
   - acceder a [repo.ialab.dsu.edu/pfsense/](http://repo.ialab.dsu.edu/pfsense/) pour telecharger l'ISO pfSense.

3. **telecharger ISO:**
   - selectionner [pfSense-CE-2.7.2-RELEASE-amd64.iso.gz](pfSense-CE-2.7.2-RELEASE-amd64.iso.gz) et telecharger le fichier ISO.
     
   - ![1](https://github.com/user-attachments/assets/51de48de-47fc-489c-8c72-51f6d6de646a)


### Creation de la machine virtuelle pfSense

1. **Initiate une nouvelle VM:**
   - dans virtualbox, cliquez sur le bouton `Nouveau`.

2. **Nom et systeme d'exploitation:**
   - **Nom:** pfSense_ih
   - **Type:** BSD
   - **Version:** FreeBSD (64-bit)

3. **Allocation de resources:**
   - `1024 MB` de RAM.
   - `1` CPU
   - `40 GB`.
     
   - ![3](https://github.com/user-attachments/assets/695dc213-ba9a-4e5b-be68-00bf5cdfaa89)


5. **Adaptateurs reseau:**

    il faut configurer le parfeu pour quil gere le trafic entre internet et notre resau intern, donc on doit configurer deux adaptateurs reseau;

   
   - **adaptateur 1 (WAN):**
     - **attacher a:** acces par pont
   - **Adaptateur 2 (LAN):**
     - **Attacher a:** Resaux interne
     - **Nom:** ih
       
     - ![4](https://github.com/user-attachments/assets/76d95e8b-b7e2-406c-8945-36c6226ea314)
       
     - ![5](https://github.com/user-attachments/assets/0a1b892f-69a0-4f06-b1db-79c0d158a810)



7. **Finaliser:**
   - Click `Create`.

8. **Install pfSense:**
   - Demarre la VM et suivez les invites d’installation de pfsense.
   - ![6](https://github.com/user-attachments/assets/a203678a-ea5e-4fdc-a895-6df094539d71)
     
   - ![7](https://github.com/user-attachments/assets/64cc598b-7117-4ec8-b3f6-e055d4b74e88)
     
   - ![8](https://github.com/user-attachments/assets/4662df7f-da75-4834-8df5-0ea78f2eb568)
     
   - ![9](https://github.com/user-attachments/assets/19a7b0bb-1b1f-4993-bf70-2b39fcd68b3e)
     
   - ![10](https://github.com/user-attachments/assets/ebbf57ab-3a54-42e8-8207-9c07e8256a5f)
     
   - noubliez paz de enlever l'image iso.
     
   - ![11](https://github.com/user-attachments/assets/a390f179-3e7e-43ab-ae80-5eced3fb40a2)







### Configuration initiale de pfSense

apres l'installation, pfsense nous donne les interfaces reseau initiale:

- **WAN (em0):** IP attribuee `192.168.20.107/22` via DHCP.
- **LAN (em1):** IP initialement attribuee `192.168.1.1/24`.
  
- ![12](https://github.com/user-attachments/assets/6320ac8b-bce7-46b7-ab28-7309286b8564)



1. **attribuer des adresses IP dinterface:**
pour activer le DHCP pour quil fait une attribution automatique de ip aux clients, on va attribuer des adresses IP dinterface 
   - **configurer linterface LAN:**
     - definir ladresse IPv4 statique:`10.10.10.1`.
     - le nouveau nombre de bits du sous reseau `24` 
     - configure IPv6 via DHCP (`configure IPv6 address lan interface via DHCP6 (y/n): y`).
    - nous faisons cela pour garantir que linterface LAN pfSense dispose une adresse IP coherente (10.10.10.1), servant de gateway par defaut pour les clients internes.

3. **enable DHCP Server dans LAN:**
   - definir la plage DHCP IPv4:
     - **Start:** `10.10.10.100`
     - **End:** `10.10.10.199`
    - nous faisons cela pour automatiser l'attribution d'adresses IP dans la plage specifiee, facilitant ainsi la gestion du reseau.
    - comme ca l'attribution d'adresses ip est fait dans cette plage 
   - appuyez sur Entree.
     
   - ![15](https://github.com/user-attachments/assets/f1d6e0a6-3224-4072-9e22-5753a95a7ad6)
     
   - ![16](https://github.com/user-attachments/assets/0d5f7b6c-ee42-4906-81b0-44288a687b79)
     
   - ![17](https://github.com/user-attachments/assets/53d2eb1c-3539-419d-9b7d-05f67fb082ba)
  
   - ![18](https://github.com/user-attachments/assets/4d6fd717-2dab-495f-b08c-5ac3500ddd1c)

5. **Resultat:**
    -obtention des l'adresses IP:
        -`10.10.10.100` pour Windows
        -`10.10.10.101` pour ubuntu server

   - ![19](https://github.com/user-attachments/assets/6b366b4d-1d17-4e19-b778-6f81a554833f)
     
   - ![20](https://github.com/user-attachments/assets/784390bc-bb4f-470a-a6b3-153c48d06a17)



### Configuration des Mappages IP Statiques


il faut fournire des adresses IP fixes pour les machines virtuelles.


1. **acces a l'interface Web pfSense:**
   - dans la vm Windows 11, on cherche `http://10.10.10.1` dans le navigateur web.
   - identifiants utilises:
     - **Username:** admin
     - **Password:** pfsense
       
     - ![21](https://github.com/user-attachments/assets/480c387e-f67b-4541-88d6-62ee8a0bb5d8)


2. **accedez aux parametres du serveur DHCP:**
   - `Services` > `DHCP Server`.
     
   - ![22](https://github.com/user-attachments/assets/a9ab0818-d3a6-4be8-b23f-0bd1b042c16f)


3. **Ajouter un mappage statique:**
   - click sur `Add Static Mapping`
     
   - ![23](https://github.com/user-attachments/assets/c9b72bbf-bce0-43d7-9afd-908eeb0bfdcd)

   - **Pour Windows 11:**
     - **address MAC:** (Recuperer a partir de la machine virtuelle Windows a l'aide de `getmac`)
       
     - ![24](https://github.com/user-attachments/assets/a1511aef-cbff-4eee-bad2-416caae521e6)

     - **IP Address:** `10.10.10.10`
     - **Description:** win11
       
     - ![26](https://github.com/user-attachments/assets/cb5e102a-496d-49fd-b36f-cf981e6a86ff)

   - **pour Ubuntu Server:**
     - **MAC Address:** (Recuperer a partir de Ubuntu vm a l'aide de `ipconfig`)
       
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
       


on va ouvrire dm ipconfig/release
ensuit ipconfig/renew
est voila
     - ![29](https://github.com/user-attachments/assets/b9c0ccff-7b2d-4dc9-9990-64529cb00b93)

   - **Ubuntu Server VM:**
     - Ouvrire le Terminal.
     - Executer `sudo dhclient -v -r` pour liberer l'IP.
     - Dans mon cas `dhclient` nest pas present donc jai installer avec (`sudo apt install dhclient`).
       
     - ![30](https://github.com/user-attachments/assets/dfafc7de-ff61-4b21-a817-4ea392c44ece)
       
     - ![31](https://github.com/user-attachments/assets/fc515822-9705-4444-abf6-82ae0734eb3c)


     - Execute `sudo dhclient -v` pour renouveler l'IP.
     -  Confirmer que l'adresse IP est maintenant `10.10.10.11`.
       
     -  ![32](https://github.com/user-attachments/assets/b80d0ce3-04a6-4edf-a22d-d9ab5c5a0db3)


## Configuration des Regles de Pare-feu

maintenant, il faut ouvrir des ports specifiques dans pfSense pour pouvoir acceder a des services comme SSH (pour putty), HTTP (pour le site Web) et RDP (pour le bureau a distance). Sans ouvrir ces ports, les services des machines virtuelles ne seraient pas accessibles depuis la machine physique ou les reseaux externes.

1. **Acces aux Parametres du Pare-feu pfSense:**
    - Navigation vers `Pare-feu` > `NAT` dans l'interface web pfSense
      
    - ![33](https://github.com/user-attachments/assets/e41b2fc7-b29f-4f0e-8812-f135c2e5991e)

    - **Ouverture du Port SSH:**
Pour permettre l'acces SSH a l'Ubuntu Server depuis l'exterieur du reseau on doit ouvrire le port `22` (`ssh`)
        - Clic sur `Ajouter` pour creer une nouvelle regle
        - Interface: `WAN`
        - Protocole: `TCP`
        - Definition de la Plage de Ports de Destination : De `SSH` a `SSH`
        - Metre l'IP Cible de Redirection a `10.10.10.11` (Ubuntu Server)
        - Definition de l'Association de Regle de Filtrage a `Passer`
          
        - ![34](https://github.com/user-attachments/assets/c9e6ab05-698b-4519-8284-e9f0764a4672)

    - **Ouverture du Port HTTP:**
Pour permettre l'acces web a l'Ubuntu Server on doit ouvrire le port `80` (`HTTp`)
        - Repetition des etapes ci-dessus, mais pour le port HTTP
          
        - ![37](https://github.com/user-attachments/assets/d8e800c9-86e0-4da5-8b8f-11bd3aef0bfa)

    - **Ouverture du Port HTTP:**
Pour permettre l'acces bureau a distance a la VM Windows 11 on doit ouvrire le port `3389` (`MSRDP`)
        - Repetition des etapes, mais pour le port MSRDP et ciblant 10.10.10.10 (Windows 11)
          
        - ![35](https://github.com/user-attachments/assets/3db4dc31-d267-4b3c-9dca-a8637435b5b5)

![39](https://github.com/user-attachments/assets/fc438139-c28b-4dcd-a3e2-495369b51bc2)



## Securisation de l'Interface Web pfSense

Pour ameliorer la securite, l'interface Web pfSense a ete securisee a l'aide de HTTPS avec des certificats personnalises.


![40](https://github.com/user-attachments/assets/25be04ef-794d-49af-9a54-209f44025a6c)


### Creation d'autorites de certification et de certificats

1. **Acceder a l'interface Web de pfSense:**
   - Acceder a `http://10.10.10.1`.
   - Connection avec  (`admin`, `pfsense`).

2. **Acceder aux certificats:**
   - Aller a `System` > `Cert. Manager` > `CAs`.
     
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
   - Aller a `System` > `Cert. Manager` > `Certificates`.
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
   - Aller a `System` > `Cert. Manager` > `Certificates`.
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
- **Certificate Authorities (CA):** etablissez une hierarchie de confiance, permettant des connexions SSL/TLS securisees.
- **Intermediate CA:** Ajoute une couche de securite supplementaire, en isolant l'autorite de certification racine.
- **Server Certificate:** Facilite l'acces HTTPS a l'interface Web pfSense.

### Importation de le CA Racine dans Windows
Pour que Windows fasse confiance aux certificats auto-signes il faut les importee a windows
1. **Exporter le certificat d'autorite de certification racine:**
   - dans pfSense, alle a `System` > `Cert. Manager` > `CAs`.
   - Click `Export` button pres de `IH_autorite_de_certification`.
   - Enregistrer le fichier de certificat ( `IH_autorite_de_certification.pem`).
     
   - ![49](https://github.com/user-attachments/assets/f2969c98-cd2b-4ab6-8d31-8cc4219f4d3f)


3. **Importer un certificat dans la racine de confiance:**
   - ovrire `MMC` (Microsoft Management Console):
     - Appuyer sur `Win + R`, tapez `mmc`, et appuyer `Entree`.
   - Ajoutez le Snap-in du Certificates :
     - Allez a `fichier` > `ajouter/suprimer un composant logiciel enfichable...`.
     - Selectioner `Certificates` et click `Add`.
   - Acceder a `Trusted Root Certification Authorities` > `Certificates`.
   - clic droit et selectionnez `All Tasks` > `Import`.
   - Select `IH_autorite_de_certification.pem`.
   - Confirmez que le certificat est repertorie sous `Trusted Root Certification Authorities`.
     
   - ![48](https://github.com/user-attachments/assets/da8d1e6d-b70f-44f6-b49f-20c67586045a)


de cette maniere, nous garantissons que les connexions HTTPS a pfSense sont reconnues comme securisees par Windows 11, eliminant ainsi les avertissements du navigateur.

### Configuration HTTPS pour pfSense

Pour chiffrer le trafic vers l'interface web pfSense et ameliorer la securite on doit configuration HTTPS pour pfSense

1. **Acceder a l'interface Web de pfSense:**
   - Acceder a `http://10.10.10.1`.
   - Connection avec  (`admin`, `pfsense`).

2. **Accedez aux parametres WebConfigurator:**
   - allez a `System` > `Advanced` > `Admin Access`.
     
   - ![50](https://github.com/user-attachments/assets/3caf6129-b2c1-430a-8766-144b86a95fd5)


3. **Configurer HTTPS:**
   - **Protocol:** Selectionner `HTTPS` (SSL/TLS).
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
   - Click `Save` pour appliquer les parametres.

Maintenant que l'interface WebConfigurator est Securise, on est proteger contre les ecoutes clandestines (eavesdropping) et les attaques de l'homme du milieu (man-in-the-middle).


![54](https://github.com/user-attachments/assets/bfb3086b-262f-4b4e-a868-4d5242ad3ddb)


### Mise a Jour du Fichier Hosts de Windows

1. **Modifier le fichier Hosts sur Windows 11:**
   - Acceder a `C:\Windows\System32\drivers\etc\`.
     
   - ![55](https://github.com/user-attachments/assets/c41044d1-e508-4f70-9d51-f76ac61abdab)
     
   - Right-click sur `hosts` et selectionnez `Ouvrir avec le Bloc-notes` (Executer le Bloc-notes en tant qu’administrateur).

2. **ajouter pfSense Host Entry:**
   - Ajoutez la ligne suivante:
     ```
     10.10.10.1 pfsenseih.grasset
     ```
   - Enregistrer le fichier.
     
   - ![56](https://github.com/user-attachments/assets/5380e8d1-e1fc-45b0-add6-a205923f78a3)


3. **Acceder a pfSense via Hostname:**
   - ouvrire Google Chrome.
   - aller a `https://pfsenseih.grasset`.
   - le WebConfigurator de pfSense doit se charger en toute securite sans avertissement de certificat.
     
   - ![57](https://github.com/user-attachments/assets/6cfab316-f8af-4a66-a2a8-715e1baa9599)


maintenant nous avons simplifie l'acces en autorisant l'utilisation d'un nom d'hôte convivial au lieu de l'adresse IP

## Configuration du serveur Web

### Installation du LAMP Stack (Linux, apach, Mysql, php)

1. **Acceder au serveur Ubuntu:**
   - ouvrire putty.
   - entrez ladress WAN de pfsense.

2. **Mettre a jour les Package Lists:**
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
   - Verifier l'etat d'Apache2:
     ```bash
     sudo systemctl status apache2
     ```
   - Assurez-vous que MariaDB est en cours d'execution:
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
   - Dans ubuntu executez:
     ```bash
     wget localhost
     ```
     
     ![60](https://github.com/user-attachments/assets/76c3f7e8-2e7f-43b4-819f-e92a7e3e123d)

   - Dans la machine physique, acces a http://192.168.20.107 (IP WAN pfSense).
   - Output doit afficher le contenu HTML par defaut d'Apache.
     
   - ![61](https://github.com/user-attachments/assets/fea56ecb-66e4-43d3-806e-a635fa212039)


2. **Creer une structure de repertoire de site Web:**
   ```bash
   sudo mkdir -p /var/www/tpiliesharrache/public_html
   ```
3. **Creer des fichiers de site Web:**
   - Acceder au repertoire du site Web:
     ```bash
     cd /var/www/tpiliesharrache/public_html
     ```
     
     ![62](https://github.com/user-attachments/assets/49aedd0e-0dc7-475c-adef-9496d28dc9e7)

   - Creer `index.html`:
     
     ```bash
     sudo vim index.html
     ```
     - ajouter le code html fourni par le prof.
   - Creer `script.js`:
     
     ```bash
     sudo vim script.js
     ```
     - ajouter le code JavaScript fourni par le prof.
   - Creer `style.css`:
     
     ```bash
     sudo vim style.css
     ```
     - ajouter le code css fourni par le prof .
       
   - ![64](https://github.com/user-attachments/assets/f6b2709a-2df8-41e4-93b6-4b9c233c20aa)


5. **Configuration de l'Hote Virtuel Apache:**

Il faut configurer Apache pour servir le site web personnalise.

   - Copier la configuration par defaut:
     
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
       
       ```bash
       ServerName tpiliesharrache.grasset
       ```
     - **Change `DocumentRoot`:**
       
       ```bash
       DocumentRoot /var/www/tpiliesharrache/public_html
       ```
   - Enregistrer et quitter (`:wq!`).
     
   - ![66 1](https://github.com/user-attachments/assets/6224e5bd-e018-4663-9cd8-b203063fed3d)


6. **Activer le nouveau site et reload Apache:**
   
   ```bash
   sudo a2ensite tpiliesharrache.conf
   sudo systemctl reload apache2
   ```
   
   ![67](https://github.com/user-attachments/assets/263fbcfb-54e1-4f23-943e-cbf8ac6fcd3a)
   
   ![68](https://github.com/user-attachments/assets/a9844456-fbfb-4e14-8ca9-b2e2feacd153)



**un Custom Virtual Host:** Permet d'heberger plusieurs sites Web sur le meme serveur, chacun avec son propre nom de domaine et son propre repertoire.

### Tester le serveur Web

1. **Mise a Jour du Fichier Hosts de la Machine Physique:**
   - Acceder a `C:\Windows\System32\drivers\etc\`.
   - Ouvrez `hosts` avec le Bloc-notes en tant qu’administrateur.
   - Ajoutez la ligne suivante:
     ```
     192.168.20.107 tpiliesharrache.grasset
     ```
   - Enregistrer le fichier.
     
   - ![69](https://github.com/user-attachments/assets/04491a87-0032-4fa2-b9d2-a1f97bf0426b)


2. **Acceder au site Web:**
   - Sur la machine physique, ouvrez Google Chrome.
   - Acceder a `http://tpiliesharrache.grasset`.
   - Le site Web personnalise doit s'afficher tel que configure.

maintenant, nous dirigeons le domaine `tpiliesharrache.grasset` vers l'IP WAN pfSense (`192.168.20.107`), permettant l'acces externe au site Web heberge.

![70](https://github.com/user-attachments/assets/dbe1ada6-ed36-45e6-a14d-d67162993776)


## Test final et verification

1. **SSH a Ubuntu Server:**
     - Ouvrir PuTTY sur Windows 10 (physique).
     - Connect a `192.168.20.107` via SSH.
2. **MSRDP Remote Desktop a Windows 10:**
     - Utilisez la connexion Bureau a distance sur Windows physique .
     - Connect a `192.168.20.107`.
3.  **HTTP:** 
    - Acceder a `http://tpiliesharrache.grasset` avec un navigateure dans la machine physique.



## Finalement

avec succes on a cree un environnement reseau securise et virtualise a l'aide de VirtualBox et de pfSense. En configurant meticuleusement les parametres reseau, en etablissant des regles de pare-feu, en securisant les interfaces administratives avec des certificats SSL/TLS et en deployant un serveur Web, un reseau local fonctionnel et securise a ete etabli. Le site Web heberge est accessible a partir de la machine physique.

### Flux d'information, Architecture client-serveur et API


- **Client** : Le code JavaScript s'execute dans le navigateur de l'utilisateur sur la machine physique.
- **Serveur** : Le serveur Apache sur Ubuntu sert les fichiers statiques (HTML, CSS, JS).
- **API externe** : L'application fait des requetes a l'API Ghibli (https://ghibliapi.vercel.app/films) pour recuperer les donnees des films.

Le flux de trafic reseau est le suivant :

1. **Requete initiale du client** :
   - L'utilisateur entre l'URL http://tpiliesharrache.grasset dans son navigateur sur la machine physique.
   - Le navigateur resout le nom de domaine en utilisant le fichier hosts modifie, qui pointe vers l'adresse IP WAN de pfSense (192.168.20.107).

2. **Traitement par pfSense** :
   - La requete HTTP arrive sur l'interface WAN de pfSense.
   - pfSense examine la requete et applique la regle NAT correspondante.
   - La requete est redirigee vers l'adresse IP interne du serveur Ubuntu (10.10.10.11) sur le port 80.

3. **Traitement par le serveur Ubuntu** :
   - Apache reçoit la requete HTTP sur le port 80.
   - Le VirtualHost correspondant a "tpiliesharrache.grasset" est selectionne.
   - Apache sert les fichiers statiques (HTML, CSS, JS) depuis le repertoire /var/www/tpiliesharrache/public_html.

4. **Execution du code JavaScript** :
   - Le navigateur reçoit et execute le code JavaScript.
   - Le JavaScript initie une requete XMLHttpRequest vers https://ghibliapi.vercel.app/films.

6. **Reponse de l'API Ghibli** :
   - L'API Ghibli repond avec les donnees JSON des films.
   - La reponse traverse Internet et arrive au navigateur (client).

7. **Traitement final par le navigateur** :
   - Le JavaScript dans le navigateur reçoit les donnees JSON.
   - Il traite ces donnees et met a jour le DOM pour afficher les informations des films.


### schema reseau

![102](https://github.com/user-attachments/assets/bcbee948-4f26-4444-87d4-fbc2bb2c2693)


### Protocoles utilises 

1. **DNS (Domain Name System)** :
   - Utilise normalement pour resoudre les noms de domaine en adresses IP.
   - Dans notre cas, remplace par une entree statique dans le fichier hosts pour tpiliesharrache.grasset.
   - Toujours utilise pour resoudre ghibliapi.vercel.app lors de l'appel a l'API.

2. **HTTP (Hypertext Transfer Protocol)** :
   - Version : HTTP/1.1
   - Utilise pour la communication entre le navigateur et le serveur Apache.
   - Methodes utilisees : GET (pour recuperer les fichiers statiques et les donnees de l'API)


![100](https://github.com/user-attachments/assets/1a6c872d-f8c2-4c4f-9cc4-2a3e9b20805a)




3. **HTTPS (HTTP Secure)** :
   - Utilise pour la communication securisee avec l'API Ghibli.
   - Protocole sous-jacent : TLS (Transport Layer Security)
   - Assure le chiffrement des donnees echangees avec l'API.
  
     
![98](https://github.com/user-attachments/assets/f9274c02-daca-4a71-8f4a-e21c31a154ac)

![99](https://github.com/user-attachments/assets/156b543e-5622-4470-9732-701f38980502)


     

4. **TCP (Transmission Control Protocol)** :
   - Protocole de transport utilise par HTTP et HTTPS.
   - Assure une transmission fiable et ordonnee des paquets.
   - Ports utilises : 80 (HTTP), 443 (HTTPS)

5. **IP (Internet Protocol)** :
   - Version : IPv4
   - Gere l'adressage et le routage des paquets entre les differents reseaux.

6. **ARP (Address Resolution Protocol)** :
   - Utilise dans le reseau local pour mapper les adresses IP aux adresses MAC.

7. **SSH (Secure Shell)** :
   - Version : SSH-2
   - Utilise pour l'acces securise a distance au serveur Ubuntu (PuTTy).
   - Port par defaut : 22

8. **MSRDP (Microsoft Remote Desktop Protocol)** :
   - Utilise pour l'acces a distance a la machine virtuelle Windows 11.
   - Port par defaut : 3389

9. **DHCP (Dynamic Host Configuration Protocol)** :
   - Utilise par pfSense pour attribuer des adresses IP aux machines du reseau interne.
   - Plage d'adresses configuree : 10.10.10.100 - 10.10.10.199

10. **NTP (Network Time Protocol)** :
    - Utilise pour synchroniser l'horloge des differentes machines du reseau.
    - Important pour la validite des certificats SSL/TLS et la coherence des logs.

## Analyse approfondie du protocole HTTP

Le protocole HTTP est crucial pour notre application. Voici une analyse detaillee d'une requete HTTP du client a notre serveur apach :

- **General**
   - Request URL: http://tpiliesharrache.grasset/script.js
   - Request Method: GET
   - Status Code: 200 OK
   - Remote Address: 192.168.20.107:80
   - Referrer Policy: strict-origin-when-cross-origin

- **Response Headers**
   - accept-ranges: bytes
   - connection: Keep-Alive
   - content-encoding: gzip
   - content-length: 561
   - content-type: text/javascript
   - date: Tue, 17 Sep 2024 20:22:45 GMT
   - etag: "4cb-6225649eaa7bb-gzip"
   - keep-alive: timeout=5, max=100
   - last-modified: Tue, 17 Sep 2024 20:11:02 GMT
   - server: Apache/2.4.58 (Ubuntu)
   - vary: Accept-Encoding
- **Request Headers**
   - accept: */*
   - accept-encoding: gzip, deflate
   - accept-language: fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7
   - cache-control:
   - no-cache
   - connection: keep-alive
   - host: tpiliesharrache.grasset
   - pragma: no-cache
   - referer: http://tpiliesharrache.grasset/
   - user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/128.0.0.0 Safari/537.36
  

Cette requete HTTP est une demande **GET** pour recuperer un fichier JavaScript a l'URL `http://tpiliesharrache.grasset/script.js`. Le **code de statut 200 OK** indique que la demande a reussi et le fichier a ete renvoye avec succes. L'adresse distante montre que la reponse vient de pfsense `192.168.20.107` sur le port 80, ce qui suggere une communication HTTP classique.

Les en-tetes de reponse incluent beaucoup de informations une de cest informations comme :
- **content-type**: le fichier est du JavaScript (`text/javascript`).
- **content-encoding**: la reponse est compressee en **gzip**.
- **content-length**: la taille du fichier compresse est de 561 octets.
- Le serveur utilise est **Apache/2.4.58** tournant sur notre Ubuntu UM.

Les en-tetes de requete montrent que le navigateur accepte differents formats de reponse et utilise une politique de cache stricte (`no-cache`) pour s'assurer que le fichier demande est toujours a jour. Le client est un navigateur Chrome recent.

[ TODO!: capture Wireshark ]

### Modifications de l'application pour le stockage local et l'acces hors ligne

## 1. Ajout de la fonctionnalite de stockage local

Nous allons utiliser IndexedDB pour stocker les donnees de l'API et le logo localement. Voici les modifications a apporter a votre code JavaScript :

```javascript
let db;
const dbName = "GhibliDB";
const storeName = "films";
const logoStoreName = "logo";

// Fonction pour initialiser la base de donnees
function initDB() {
  const request = indexedDB.open(dbName, 1);
  
  request.onerror = function(event) {
    console.error("Erreur d'ouverture de la base de donnees");
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

// Fonction pour charger les donnees
function loadData() {
  getLogoFromDB();
  getFilmsFromDB();
}

// Fonction pour recuperer le logo de la base de donnees
function getLogoFromDB() {
  const transaction = db.transaction([logoStoreName], "readonly");
  const objectStore = transaction.objectStore(logoStoreName);
  const request = objectStore.get("logo");

  request.onerror = function(event) {
    console.error("Erreur de recuperation du logo");
  };

  request.onsuccess = function(event) {
    if (request.result) {
      displayLogo(request.result.src);
    } else {
      fetchAndStoreLogo();
    }
  };
}

// Fonction pour recuperer et stocker le logo
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
    .catch(error => console.error('Erreur de recuperation du logo:', error));
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
  document.getElementById('root').appendChild(logo);
}

// Fonction pour recuperer les films de la base de donnees
function getFilmsFromDB() {
  const transaction = db.transaction([storeName], "readonly");
  const objectStore = transaction.objectStore(storeName);
  const request = objectStore.getAll();

  request.onerror = function(event) {
    console.error("Erreur de recuperation des films");
  };

  request.onsuccess = function(event) {
    if (request.result.length > 0) {
      displayFilms(request.result);
    } else {
      fetchAndStoreFilms();
    }
  };
}

// Fonction pour recuperer et stocker les films
function fetchAndStoreFilms() {
  fetch('https://ghibliapi.vercel.app/films')
    .then(response => response.json())
    .then(data => {
      storeFilms(data);
      displayFilms(data);
    })
    .catch(error => {
      console.error('Erreur de recuperation des films:', error);
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

  document.getElementById('root').appendChild(container);
}

// Fonction pour afficher un message d'erreur
function displayErrorMessage() {
  const errorMessage = document.createElement('marquee');
  errorMessage.textContent = `Une erreur est survenue`;
  document.getElementById('root').appendChild(errorMessage);
}

// Initialiser la base de donnees au chargement de la page
initDB();

```
## 2. Modifications apportees

1. **Utilisation d'IndexedDB :**
   
   - Nous avons ajoute une base de donnees locale pour stocker les films et le logo.
     
2. **Gestion du logo :**

   - Le logo est maintenant recupere depuis la base de donnees locale s'il existe.
Si le logo n'est pas en cache, il est telecharge, stocke dans IndexedDB, puis affiche.


3. **Gestion des films :**

   - Les films sont d'abord recherches dans la base de donnees locale.
S'ils ne sont pas presents, l'application tente de les recuperer depuis l'API et les stocke localement.


4. **Fonctionnement hors ligne :**

   - Si l'application ne peut pas se connecter a l'API, elle utilisera les donnees stockees localement.
Un message d'erreur s'affiche uniquement si aucune donnee n'est disponible localement et que l'API est inaccessible.


5. **Optimisation des performances :**

   - Les donnees sont chargees une seule fois depuis l'API, puis stockees localement.
Les chargements ulterieurs seront plus rapides car les donnees seront recuperees depuis IndexedDB.

![93](https://github.com/user-attachments/assets/6b0b2c43-1d0a-4c11-a2f2-18b1aeafdaa1)


![90](https://github.com/user-attachments/assets/4392e3f5-7778-4588-bcf2-abcf713c7145)


![91](https://github.com/user-attachments/assets/587db6ce-8ee3-4d76-b89e-74f39c137091)


![92](https://github.com/user-attachments/assets/6475ef32-813a-47fc-8046-3538333990ca)


## 2.  pourquoi cette modification? (Avantages de cette modification)

   - **Resilience :** L'application fonctionne meme sans connexion Internet une fois les donnees chargees.
   - **Performance :** Les temps de chargement sont reduits apres la premiere visite.
   - **economie de bande passante :** Les donnees ne sont telechargees qu'une seule fois.


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

