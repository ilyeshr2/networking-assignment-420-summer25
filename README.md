Ilies Harrache


# tp1

## introduction


dans ce rapport on va cree un reseau local securise a laide de virtualbox, pfsense, ubuntu server (vm) rt windows 11 (vm). lobjectife est de cree un environnement virtuelle dans lequele un site web est heberge sur un serveur ubuntu est accesible depuis notre machine physique.

## prerequis

- une machine physique avec des ressources suffisantes (ram, cpu, stockage) pour heberger plusieurs machines virtuelles.
- une connexion internet stable pour telecharger les logiciels et les packages logiciels.

## configuration de l'environnement virtuel

### installation de virtualbox

1. **telecharger virtualbox:**
   - acceder a la page [virtualbox downloads](https://www.virtualbox.org/wiki/downloads)
   - selectionnez la version appropriee pour notre systeme dexploitation (windows dans mon cas).

2. **install virtualbox:**
   - executez le programme dinstallation telecharger.
   - suiver les instructions a l'ecran et accepter les parametres par defaut.
   - quand linstallation est terminer on lance virtualbox.

### creation des machines virtuelles

on va cree deux machine virtuelles: **ubuntu server** et **windows 11**.

#### vm ubuntu server

1. **initiate une nouvelle vm:**
   - dans virtualbox, on click sur le bouton  `nouveau` .

2. **nom et systeme dexploitation:**
   - **nom:** ubuntu_server
   - **folder:**     
        ```bash
     c:\users\2417034\virtualbox vms
     ```
   - **iso image:** on selection l'image iso que on a telecharger du site official de ubuntu [ubuntu server](https://ubuntu.com/download/server).
   - et on selectione 'skip unattended installation' pour que on install le guest os manuellement. 

3. **allocation de resources:**
   - 2 processeurs
   - 2048mo de memoire.
   - 30go de disque

5. **finaliser:**
   - click `create`.

6. **install ubuntu server:**
   - on demarre la vm et on suit les invites dinstallation de ubuntu.
   - configurez les parametres comme la langue et la disposition du clavier.
   - creer un compte utilisateur nomee ilies avec un mot de pass 123.

#### vm windows 11

1. **initiate une nouvelle vm:**
   - dans virtualbox, cliquez sur le bouton  `nouveau` .

2. **nom et systeme dexploitation:**
   - **nom:** win
   - **folder:**     
        ```bash
     c:\users\2417034\virtualbox vms
     ```
   - **iso image:** on selection l'image iso que on a telecharger du site  [institutgrassetinfo](http://reseaux.institutgrassetinfo.com/windows\_11\_enterprise\_edition\_22h2\_x64.iso).
   - et on selectione 'skip unattended installation' pour que on insrtall le guest os manuellement. 

3. **allocation de resources:**
   - 2 processeurs
   - 3000mo de memoire.
   - 60go de disque

5. **finaliser:**
   - click `create`.

6. **install windows 11:**
   - demarre la vm et suivere les invites dinstallation dans le document windows11_installation du cours system d'exploitation.

## configuration du reseau virtuel

### configuration des adaptateurs reseau sur le reseau interne

les machines virtuelles ubuntu server et windows 11 sont initialement configurees sur adaptateur `acces par pont`. on a remplacee cette configuration par un `reseau interne` nomme `ih` (ilies harrache) pour creer un `reseau local separe`.

1. **acceder aux parametres de la vm:**
   - faites un clic droit sur les deux vm dans virtualbox et selectionnez `configuration`.

2. **acceder au reseau:**
   - cliquez sur longlet `reseau`.

3. **configurer ladaptateur:**
   - **adapter 1:**
     - **attache a:** resaux interne
     - **nome:** ih

4. **apply changes:**
   - cliquez sur `ok` pour enregistrer les parametres.
  
   - ![13](https://github.com/user-attachments/assets/62043379-ee97-46f3-83ff-f023b413e6e5)
     
   - ![14](https://github.com/user-attachments/assets/e4fb9902-bc46-45ca-b12d-78f0b59135ae)



**pourquoi on a fait ca?:**
pour cree un reseau `prive isole` dautres reseaux `externes`.

## configuration du pare-feu pfsense

### telechargement de pfsense

1. **access au repo de pfsense:**
   
   - acceder a [repo.ialab.dsu.edu/pfsense/](http://repo.ialab.dsu.edu/pfsense/) pour telecharger l'iso pfsense.

3. **telecharger iso:**
   - selectionner [pfsense-ce-2.7.2-release-amd64.iso.gz](pfsense-ce-2.7.2-release-amd64.iso.gz) et telecharger le fichier iso.
     
   - ![1](https://github.com/user-attachments/assets/51de48de-47fc-489c-8c72-51f6d6de646a)


### creation de la machine virtuelle pfsense

1. **initiate une nouvelle vm:**
   - dans virtualbox, cliquez sur le bouton `nouveau`.

2. **nom et systeme d'exploitation:**
   - **nom:** pfsense_ih
   - **type:** bsd
   - **version:** freebsd (64-bit)

3. **allocation de resources:**
   - `1024 mb` de ram.
   - `1` cpu
   - `40 gb`.
     
   - ![3](https://github.com/user-attachments/assets/695dc213-ba9a-4e5b-be68-00bf5cdfaa89)


5. **adaptateurs reseau:**

    il faut configurer le parfeu pour quil gere le trafic entre internet et notre resau intern, donc on doit configurer deux adaptateurs reseau;

   
   - **adaptateur 1 (wan):**
     - **attacher a:** acces par pont
   - **adaptateur 2 (lan):**
     - **attacher a:** resaux interne
     - **nom:** ih
       
     - ![4](https://github.com/user-attachments/assets/76d95e8b-b7e2-406c-8945-36c6226ea314)
       
     - ![5](https://github.com/user-attachments/assets/0a1b892f-69a0-4f06-b1db-79c0d158a810)



7. **finaliser:**
   - click `create`.

8. **install pfsense:**
   - demarre la vm et suivez les invites d’installation de pfsense.
   - ![6](https://github.com/user-attachments/assets/a203678a-ea5e-4fdc-a895-6df094539d71)
     
   - ![7](https://github.com/user-attachments/assets/64cc598b-7117-4ec8-b3f6-e055d4b74e88)
     
   - ![8](https://github.com/user-attachments/assets/4662df7f-da75-4834-8df5-0ea78f2eb568)
     
   - ![9](https://github.com/user-attachments/assets/19a7b0bb-1b1f-4993-bf70-2b39fcd68b3e)
     
   - ![10](https://github.com/user-attachments/assets/ebbf57ab-3a54-42e8-8207-9c07e8256a5f)
     
   - noubliez paz de enlever l'image iso.
     
   - ![11](https://github.com/user-attachments/assets/a390f179-3e7e-43ab-ae80-5eced3fb40a2)







### configuration initiale de pfsense

apres l'installation, pfsense nous donne les interfaces reseau initiale:

- **wan (em0):** ip attribuee `192.168.20.107/22` via dhcp.
- **lan (em1):** ip initialement attribuee `192.168.1.1/24`.
  
- ![12](https://github.com/user-attachments/assets/6320ac8b-bce7-46b7-ab28-7309286b8564)



1. **attribuer des adresses ip dinterface:**
pour activer le dhcp pour quil fait une attribution automatique de ip aux clients, on va attribuer des adresses ip dinterface 
   - **configurer linterface lan:**
     - definir ladresse ipv4 statique:`10.10.10.1`.
     - le nouveau nombre de bits du sous reseau `24` 
     - configure ipv6 via dhcp (`configure ipv6 address lan interface via dhcp6 (y/n): y`).
    - nous faisons cela pour garantir que linterface lan pfsense dispose une adresse ip coherente (10.10.10.1), servant de gateway par defaut pour les clients internes.

3. **enable dhcp server dans lan:**
   - definir la plage dhcp ipv4:
     - **start:** `10.10.10.100`
     - **end:** `10.10.10.199`
    - nous faisons cela pour automatiser l'attribution d'adresses ip dans la plage specifiee, facilitant ainsi la gestion du reseau.
    - comme ca l'attribution d'adresses ip est fait dans cette plage 
   - appuyez sur entree.
     
   - ![15](https://github.com/user-attachments/assets/f1d6e0a6-3224-4072-9e22-5753a95a7ad6)
     
   - ![16](https://github.com/user-attachments/assets/0d5f7b6c-ee42-4906-81b0-44288a687b79)
     
   - ![17](https://github.com/user-attachments/assets/53d2eb1c-3539-419d-9b7d-05f67fb082ba)
  
   - ![18](https://github.com/user-attachments/assets/4d6fd717-2dab-495f-b08c-5ac3500ddd1c)

5. **resultat:**
    -obtention des l'adresses ip:
        -`10.10.10.100` pour windows
        -`10.10.10.101` pour ubuntu server

   - ![19](https://github.com/user-attachments/assets/6b366b4d-1d17-4e19-b778-6f81a554833f)
     
   - ![20](https://github.com/user-attachments/assets/784390bc-bb4f-470a-a6b3-153c48d06a17)



### configuration des mappages ip statiques


il faut fournire des adresses ip fixes pour les machines virtuelles.


1. **acces a l'interface web pfsense:**
   - dans la vm windows 11, on cherche `http://10.10.10.1` dans le navigateur web.
   - identifiants utilises:
     - **username:** admin
     - **password:** pfsense
       
     - ![21](https://github.com/user-attachments/assets/480c387e-f67b-4541-88d6-62ee8a0bb5d8)


2. **accedez aux parametres du serveur dhcp:**
   - `services` > `dhcp server`.
     
   - ![22](https://github.com/user-attachments/assets/a9ab0818-d3a6-4be8-b23f-0bd1b042c16f)


3. **ajouter un mappage statique:**
   - click sur `add static mapping`
     
   - ![23](https://github.com/user-attachments/assets/c9b72bbf-bce0-43d7-9afd-908eeb0bfdcd)

   - **pour windows 11:**
     - **address mac:** (recuperer a partir de la machine virtuelle windows a l'aide de `getmac`)
       
     - ![24](https://github.com/user-attachments/assets/a1511aef-cbff-4eee-bad2-416caae521e6)

     - **ip address:** `10.10.10.10`
     - **description:** win11
       
     - ![26](https://github.com/user-attachments/assets/cb5e102a-496d-49fd-b36f-cf981e6a86ff)

   - **pour ubuntu server:**
     - **mac address:** (recuperer a partir de ubuntu vm a l'aide de `ipconfig`)
       
     - ![25](https://github.com/user-attachments/assets/5b03a2be-93ee-428d-8c29-2e325464c4c0)

     - **ip address:** `10.10.10.11`
     - **description:** ubuntuserver
       
     - ![27](https://github.com/user-attachments/assets/1632a714-4cbc-4215-94b0-88e9462c2004)


4. **appliquer la configuration:**
   - enregistrer les mappages statiques.
     
   - ![28](https://github.com/user-attachments/assets/71243cac-9ef8-4e7f-a5d4-15be60bbbc63)


5. **renouveler les adresses ip sur les vms:**
   - **windows 11 vm:**
     - ouvrire cmd.
     - executer `ipconfig /release` suivi de `ipconfig /renew`.
     - confirmer que l'adresse ip est maintenant `10.10.10.10`.
       


on va ouvrire dm ipconfig/release
ensuit ipconfig/renew
est voila
     - ![29](https://github.com/user-attachments/assets/b9c0ccff-7b2d-4dc9-9990-64529cb00b93)

   - **ubuntu server vm:**
     - ouvrire le terminal.
     - executer `sudo dhclient -v -r` pour liberer l'ip.
     - dans mon cas `dhclient` nest pas present donc jai installer avec (`sudo apt install dhclient`).
       
     - ![30](https://github.com/user-attachments/assets/dfafc7de-ff61-4b21-a817-4ea392c44ece)
       
     - ![31](https://github.com/user-attachments/assets/fc515822-9705-4444-abf6-82ae0734eb3c)


     - execute `sudo dhclient -v` pour renouveler l'ip.
     -  confirmer que l'adresse ip est maintenant `10.10.10.11`.
       
     -  ![32](https://github.com/user-attachments/assets/b80d0ce3-04a6-4edf-a22d-d9ab5c5a0db3)


## configuration des regles de pare-feu

maintenant, il faut ouvrir des ports specifiques dans pfsense pour pouvoir acceder a des services comme ssh (pour putty), http (pour le site web) et rdp (pour le bureau a distance). sans ouvrir ces ports, les services des machines virtuelles ne seraient pas accessibles depuis la machine physique ou les reseaux externes.

1. **acces aux parametres du pare-feu pfsense:**
    - navigation vers `pare-feu` > `nat` dans l'interface web pfsense
      
    - ![33](https://github.com/user-attachments/assets/e41b2fc7-b29f-4f0e-8812-f135c2e5991e)

    - **ouverture du port ssh:**
pour permettre l'acces ssh a l'ubuntu server depuis l'exterieur du reseau on doit ouvrire le port `22` (`ssh`)
        - clic sur `ajouter` pour creer une nouvelle regle
        - interface: `wan`
        - protocole: `tcp`
        - definition de la plage de ports de destination : de `ssh` a `ssh`
        - metre l'ip cible de redirection a `10.10.10.11` (ubuntu server)
        - definition de l'association de regle de filtrage a `passer`
          
        - ![34](https://github.com/user-attachments/assets/c9e6ab05-698b-4519-8284-e9f0764a4672)

    - **ouverture du port http:**
pour permettre l'acces web a l'ubuntu server on doit ouvrire le port `80` (`http`)
        - repetition des etapes ci-dessus, mais pour le port http
          
        - ![37](https://github.com/user-attachments/assets/d8e800c9-86e0-4da5-8b8f-11bd3aef0bfa)

    - **ouverture du port http:**
pour permettre l'acces bureau a distance a la vm windows 11 on doit ouvrire le port `3389` (`msrdp`)
        - repetition des etapes, mais pour le port msrdp et ciblant 10.10.10.10 (windows 11)
          
        - ![35](https://github.com/user-attachments/assets/3db4dc31-d267-4b3c-9dca-a8637435b5b5)

![39](https://github.com/user-attachments/assets/fc438139-c28b-4dcd-a3e2-495369b51bc2)



## securisation de l'interface web pfsense

pour ameliorer la securite, l'interface web pfsense a ete securisee a l'aide de https avec des certificats personnalises.


![40](https://github.com/user-attachments/assets/25be04ef-794d-49af-9a54-209f44025a6c)


### creation d'autorites de certification et de certificats

1. **acceder a l'interface web de pfsense:**
   - acceder a `http://10.10.10.1`.
   - connection avec  (`admin`, `pfsense`).

2. **acceder aux certificats:**
   - aller a `system` > `cert. manager` > `cas`.
     
   - ![41](https://github.com/user-attachments/assets/888e4bf4-1431-43b4-bc17-c184ef56dc13)


3. **creer une autorite de certification racine (ca):**
   - click `add`.
   - **descriptive name:** `ih_autorite_de_certification`
   - **method:** create an internal certificate authority.
   - **key type:** rsa
   - **key length:** 2048 bits.
   - **digest algorithm:** sha256.
   - **lifetime (days):** 3650
   - **common name:** `ih_autorite_de_certification`.
   - click `save`.
     
   - ![42](https://github.com/user-attachments/assets/5f7ce05b-466f-476d-9113-46ee1c12ff85)
     
   - ![43](https://github.com/user-attachments/assets/30739b8b-b06a-406e-b9fa-18b3a7add894)



4. **creer une autorite de certification intermediaire:**
   - aller a `system` > `cert. manager` > `certificates`.
   - click `add`.
   - **descriptive name:** `ih_autorite_intermediaire_de_certification`.
   - **method:** create an intermediate certificate authority.
   - **signing ca:** selectioner `ih_autorite_de_certification`.
   - **key type:** rsa
   - **key length:** 2048 bits.
   - **digest algorithm:** sha256.
   - **lifetime:** default (3650).
   - **common name:** `ih_autorite_intermediaire_de_certification`.
     
   - ![44](https://github.com/user-attachments/assets/43def03d-8c0e-4bcd-857b-b3f0dc199c1f)
     
   - ![45](https://github.com/user-attachments/assets/ab159a80-c98f-45b7-a76d-75ee1cbf8bd3)


   
   - click `save`.

5. **creer un certificat de serveur:**
   - aller a `system` > `cert. manager` > `certificates`.
   - click `add`.
   - **descriptive name:** `pfsenseih.grasset`.
   - **method:** create an internal certificate.
   - **certificate authority:** selectioner `ih_autorite_intermediaire_de_certification`.
   - **key type:** rsa
   - **key length:** 2048 bits.
   - **digest algorithm:** sha256.
   - **common name:** `pfsenseih.grasset`.
   - **lifetime:** default (3650).
   - click `save`.
     
   - ![46](https://github.com/user-attachments/assets/113a80d8-3e06-4c58-9475-6b09cf2ae48a)
     
   - ![47](https://github.com/user-attachments/assets/784f6414-b1c9-4afd-bcf0-a60f9eb307a8)



**pourquoi les certificats?:**
- **certificate authorities (ca):** etablissez une hierarchie de confiance, permettant des connexions ssl/tls securisees.
- **intermediate ca:** ajoute une couche de securite supplementaire, en isolant l'autorite de certification racine.
- **server certificate:** facilite l'acces https a l'interface web pfsense.

### importation de le ca racine dans windows
pour que windows fasse confiance aux certificats auto-signes il faut les importee a windows
1. **exporter le certificat d'autorite de certification racine:**
   - dans pfsense, alle a `system` > `cert. manager` > `cas`.
   - click `export` button pres de `ih_autorite_de_certification`.
   - enregistrer le fichier de certificat ( `ih_autorite_de_certification.pem`).
     
   - ![49](https://github.com/user-attachments/assets/f2969c98-cd2b-4ab6-8d31-8cc4219f4d3f)


3. **importer un certificat dans la racine de confiance:**
   - ovrire `mmc` (microsoft management console):
     - appuyer sur `win + r`, tapez `mmc`, et appuyer `entree`.
   - ajoutez le snap-in du certificates :
     - allez a `fichier` > `ajouter/suprimer un composant logiciel enfichable...`.
     - selectioner `certificates` et click `add`.
   - acceder a `trusted root certification authorities` > `certificates`.
   - clic droit et selectionnez `all tasks` > `import`.
   - select `ih_autorite_de_certification.pem`.
   - confirmez que le certificat est repertorie sous `trusted root certification authorities`.
     
   - ![48](https://github.com/user-attachments/assets/da8d1e6d-b70f-44f6-b49f-20c67586045a)


de cette maniere, nous garantissons que les connexions https a pfsense sont reconnues comme securisees par windows 11, eliminant ainsi les avertissements du navigateur.

### configuration https pour pfsense

pour chiffrer le trafic vers l'interface web pfsense et ameliorer la securite on doit configuration https pour pfsense

1. **acceder a l'interface web de pfsense:**
   - acceder a `http://10.10.10.1`.
   - connection avec  (`admin`, `pfsense`).

2. **accedez aux parametres webconfigurator:**
   - allez a `system` > `advanced` > `admin access`.
     
   - ![50](https://github.com/user-attachments/assets/3caf6129-b2c1-430a-8766-144b86a95fd5)


3. **configurer https:**
   - **protocol:** selectionner `https` (ssl/tls).
   - **ssl/tls certificate:** choisir `pfsenseih.grasset`.
     
   - ![51](https://github.com/user-attachments/assets/763bc370-62d9-41f2-9735-9b33aa62fc39)


4. **autres parametres:**
   - **enable webconfigurator login autocomplete:** `checked`.
   - **allow gui administrator client ip address to change during a login session:** `checked`.
   - **disable dns rebinding checks:** `checked`.
   - **disable http_referer enforcement check:** `checked`.
     
   - ![52](https://github.com/user-attachments/assets/45f5f20f-bd1c-45a8-b61f-3d99d932d18c)
     
   - ![53](https://github.com/user-attachments/assets/2cab69d8-7893-4a62-8db9-22ca4e5fabc8)



5. **enregistrer et appliquer:**
   - click `save` pour appliquer les parametres.

maintenant que l'interface webconfigurator est securise, on est proteger contre les ecoutes clandestines (eavesdropping) et les attaques de l'homme du milieu (man-in-the-middle).


![54](https://github.com/user-attachments/assets/bfb3086b-262f-4b4e-a868-4d5242ad3ddb)

