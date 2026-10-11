# SecuriteInfoProjet1

## Groupe  6 :
  * BAZILLE Tony
  
  * BONNEFOI Robin
  
  * BEN MAKHLOUF Ines
  

## Outils utilisés dans notre système : 
* Wazuh
* syslog-ng
* Elasticsearch
* Kibana
* Filebeat
* Postfix
* Suricata

## Outils utilisés pour nos tests en tant qu'attaquant:
* Nmap
* Ncrack
* MikuMikuBeam
  

## Présentation de l'architecture :

L'utilisation de Wazuh et de Suricata permet de détecter différents comportements suspects, tandis que syslog-ng et Filebeat assurent la collecte et le transmission des journaux. Elasticsearch permet de stocker et d'indexer les événements, qui peuvent ensuite être visualisés et analysés dans Kibana. Enfin, Postfix permet d'envoyer des notifications par courriel lorsque certaines alertes atteignent un certain niveau de gravité défini.

## Schéma d'architecture :

<img width="754" height="397" alt="Schéma_architecture drawio" src="https://github.com/user-attachments/assets/dacfbe40-95bc-488d-9ae2-4dba274c0621" />

## Organisation du GitHub :

	SecuriteInfoProjet1/
	├── README.md
	└── config/
		└──README_config.md
	    ├── elasticsearch/
	    │   └── elasticsearch.yml
	    ├── filebeat/
	    │   └── filebeat.yml
	    ├── postfix/
	 	│   ├── main.cf
	    │   └── sasl_passwd
	    ├── suricata/
	 	│   ├── local.rules
	    │   └── suricata.yaml
	    ├── syslog_ng/
	    │   └── syslog-ng.conf
	    └── wazuh/
	 	    ├── local_rules.xml
	        └── ossec.conf

		
## Installations :

### Wazuh manager
	sudo apt install -y wazuh-manager
	curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
	echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list 10.0.0.132="10.0.0.2" 
	sudo systemctl daemon-reload
	sudo nano /var/ossec/etc/ossec.conf # configurer le fichier (voir config)
	sudo systemctl enable --now wazuh-manager
	sudo systemctl restart wazuh-manager
	sudo systemctl status wazuh-manager
	

### Suricata et Syslog-ng
	sudo apt install suricata syslog-ng
	nano /etc/syslog-ng/syslog-ng.conf # configurer le fichier (voir config)
	sudo systemctl restart syslog-ng

### Elasticsearch
	curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
	echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
	sudo apt update && sudo apt install elasticsearch
	sudo systemctl enable --now elasticsearch   
	sudo systemctl start elasticsearch.service

### Mot de passe elsaticsearch
	sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic #ajout du -i pour choisir le mot de passe 

### Kibana
	wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
	echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/9.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-9.x.list
	sudo apt-get update && sudo apt-get install kibana   

###  Downgrade kibana to 8.19.22

	sudo systemctl stop kibana
	sudo rm /etc/apt/sources.list.d/elastic-9.x.list
	echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
	sudo apt update
	sudo apt install kibana=8.19.22 --allow-downgrades
	sudo apt-mark hold kibana
	sudo systemctl daemon-reload
	sudo systemctl start kibana
	sudo journalctl -u kibana -f

### Filebeat : 

	sudo apt-get install elasticsearch -y
	sudo systemctl daemon-reload
	sudo systemctl enable elasticsearch
	sudo systemctl start elasticsearch
	sudo apt-get install filebeat -y
	sudo nano /etc/filebeat/filebeat.yml # configurer le fichier (voir config)
	sudo systemctl daemon-reload
	sudo systemctl enable --now filebeat
	sudo systemctl restart filebeat
	sudo filebeat test config #doit répondre OK
	sudo systemctl daemon-reload
	sudo systemctl restart filebeat
	curl -o wazuh-template.json https://raw.githubusercontent.com/wazuh/wazuh/4.9/extensions/elasticsearch/8.x/wazuh-template.json
	sudo nano wazuh-template.json

### Service de Mail

	sudo apt update && sudo apt install postfix -y
	sudo nano /etc/postfix/sasl_passwd # configurer le fichier (voir config)
	sudo chmod 600 /etc/postfix/sasl_passwd
	sudo postmap /etc/postfix/sasl_passwd
	sudo nano /etc/postfix/main.cf # configurer le fichier (voir config)
	sudo systemctl restart postfix
	sudo nano /var/ossec/etc/ossec.conf # configurer le fichier (voir config)
	sudo systemctl restart wazuh-manager
	
### Lancement de l'interface Kibana :

	sudo ss -ltnp | grep 5601
	curl -I http://127.0.0.1:5601
	sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token --scope kibana # Rentrer le code dans le site
	sudo /usr/share/kibana/bin/kibana-verification-code # Rentrer le code de vérification dans le site

Recuperation du mot de passe :

	sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic #ajout du -i pour choisir le mot de passe si ca na pas déjà été fait avant

<img width="526" height="541" alt="image" src="https://github.com/user-attachments/assets/f6ef89fc-9a57-4652-a0a4-02fc8c125842" />

	Username : elastic
	Password : mot de passe récupérer par la commande précédente 

<img width="1799" height="960" alt="2026_10_07_0wj_Kleki" src="https://github.com/user-attachments/assets/6a9f5c4d-581b-4473-a0d7-6c5aafe32374" />


Interface Kibana, pour créer une nouvelle source de données, il faut aller dans stack management puis dans Data views

<img width="1799" height="960" alt="2026_10_07_0wm_Kleki" src="https://github.com/user-attachments/assets/bdd477ac-9b9d-457b-bab4-71a8e035c6f8" />

Puis Create data view :

<img width="1799" height="960" alt="image" src="https://github.com/user-attachments/assets/99b12802-ece0-41e9-aa60-e679d68fc0b6" />

	Name : paramètre de notre choix
	Index pattern : le pattern d'index utilisé
puis save data view
	
Analytics puis Discover pour voir les analyses
On peut filtrer par niveau de dangerosité dans le KQL avec la formule rule.level >= 10
avec le niveau de danger qui varie entre 0 et 15.



## Interface Kibana :
<img width="1798" height="966" alt="image" src="https://github.com/user-attachments/assets/67f72ad2-62bf-4451-a753-2cc1c5feb3fd" />
Dans le menu Analytics -> Discover, on peut voir les différents horaires a laquelle les tests ont eu lieu, avec une description et le niveau d'impact. 



## Scénarios de tests :
Les scénarios de tests suivants ont été sélectionnés afin d'évaluer la capacité de notre infrastructure de sécurité à détecter et à gérer différents types de cyberattaques. Chaque scénario repose sur un mécanisme d'attaque distinct et permet de tester des aspects spécifiques de notre système de surveillance.

* **DOS par HTTP flood :** Envoie massif de requête http à la VM avec l'outil Mikumikubeam.
* **Scan de ports :** Scan les ports de la VM afin de déterminer les ports qui sont ouverts à l'aide de l'outil Nmap.
* **Injections SQL :** Injection de requête SQL dans une requête http envoyé sur un service apache2 hébergé par la VM.
* **Attaque par dictionnaire :** Brute force SSH utilisant un dictionnaire de mot de passe coomun à l'aide de l'outil Ncrack.
* **Trafic HTTP frauduleux :** Envoies de requêtes Web malveillantes ou suspectes envoyées à un serveur ou à une application Web dans le but de manipuler son fonctionnement, d'exploiter une vulnérabilité ou d'accéder à des informations sans autorisation. 

	
## Mise en place des scénarios de tests

### DOS par HTTP flood :

#### MikumikuBeam :

Installation et d'utilisation :
	
https://github.com/sammwyy/mikumikubeam.git

<img width="1165" height="686" alt="Diagramme sans nom drawio" src="https://github.com/user-attachments/assets/083bd87e-957a-464d-b275-ee07605bafbf" />


### Scan de port :

#### Nmap :

Installation et d'utilisation :

https://nmap.org/download.html


### Attaque par dictionnaire :

#### NCrack

Installation et d'utilisation :

https://nmap.org/ncrack/
	

### Injections SQL

#### Installation et mise en place de apache2 :
	sudo apt update
	sudo apt install apache2
	sudo systemctl enable --now apache2

#### Attaque :
	curl -i "http://192.168.56.10/?id=SELECT+*+FROM+users"


### Requête Http frauduleuse :

#### Attaque :
	curl -i "http://192.168.56.10/?exploit=1"



	
## Menace de chaque attaque :

### Déni de service (DoS) :

<img width="1605" height="787" alt="image(1)" src="https://github.com/user-attachments/assets/12e5fc80-e273-481a-af1f-6b0d643b6f4a" />

* *Indisponibilité des services :* les utilisateurs ne peuvent plus accéder au site ou à l'application.
* *Pertes financières :* l'indisponibilité peut entraîner des coûts et nuire à la réputation.
* *Saturation de l'infrastructure :* les serveurs, les réseaux ou les équipements de sécurité peuvent être surchargés.

### Scan de ports :

<img width="1605" height="787" alt="image(1)" src="https://github.com/user-attachments/assets/204a84c3-a0e8-47a2-aa87-c96362c41e94" />

* *Découverte des services exposés :* l'attaquant identifie les applications accessibles depuis le réseau.
* *Repérage des vulnérabilités :* les services détectés peuvent être examinés pour rechercher des failles de sécurité dans le but d'être exploité


### Attaque par dictionnaire :
  
<img width="1605" height="787" alt="image(1)" src="https://github.com/user-attachments/assets/fdc6bc76-0dbf-4edf-a658-6e3ddbb8525e" />

* *Vol de données personnelles :* il peut consulter des informations confidentielles et accéder a son compte.
* *Usurpation d'identité :* il peut se faire passer pour la victime (permettrait de faire de l'usurpation hiérarchique, de la fraude au président, etc)
* *Utilisation sur d'autres comptes :* l'attaquant pourrait réutiliser ce même mot de passe sur d'autres applications.


### Injections SQL :

<img width="1612" height="792" alt="image" src="https://github.com/user-attachments/assets/c29c1ede-dd2c-465f-bc58-56a37808b4cc" />

* *Vol de données :* récupération de renseignements personnels, de mots de passe ou d'informations confidentielles.
* *Modification des données :* altération des dossiers, des commandes ou des informations enregistrées.
* *Suppression de données :* destruction de certaines tables ou informations, selon les privilèges disponibles.


### Trafic HTTP frauduleux :

<img width="1615" height="795" alt="image" src="https://github.com/user-attachments/assets/75e553b3-5977-4852-be78-2e0b6ede9586" />

* *Vol de données :* des requêtes malveillantes peuvent chercher à récupérer des informations confidentielles.
* *Exploitation de vulnérabilités :* certaines requêtes peuvent viser des failles dans l'application Web.
* *Contournement des mécanismes de sécurité :* des requêtes spécialement conçues peuvent chercher à contourner les contrôles d'accès.
  



## Analyse et conclusion :

### 1. Limites du projet
* **Absence de protection automatique complète :** On détecte certains types d'attaques, mais on n'y apporte pas nécessairement de réponse automatique.
* **Sécurisation de la configuration :** Les mots de passe sont écrits directement dans les fichiers de configuration, ce qui n'est pas très sécuritaire.
* **Couverture limitée des scénarios :** Le système est testé sur des attaques connues et définies à l'avance, ce qui limite sa capacité à détecter des attaques inconnues.
* **Utilisation d'une machine pour la simulation d'un réseau :** En entreprise, il y aura beaucoup plus de trafic et de requêtes que dans notre environnement de simulation.
  
### 2. Améliorations possibles
* **Automatisation de la réponse aux incidents :** Mettre en place des mécanismes de défense automatisés contre certains types d'attaques déjà identifiées, par exemple en bloquant temporairement une adresse IP malveillante lors d'une attaque DoS, lorsque cela est pertinent.
* **Renforcement de la sécurité :** Éviter de stocker les mots de passe en clair dans les fichiers de configuration et utiliser des solutions plus sécurisées, comme des variables d'environnement ou un gestionnaire de secrets.
* **Scénarios complémentaires :** Ajouter plusieurs autres scénarios d'attaque afin d'améliorer la couverture des menaces.

### 3. Perspectives (veilles techno)
* **Automatisation des opérations de sécurité :** Explorer des solutions de type SOAR pour automatiser certaines tâches de réponse aux incidents.
* **Détection basée sur l'intelligence artificielle :** Explorer des solutions capables d'analyser les comportements du réseau afin de détecter des anomalies ou des attaques qui ne correspondent pas aux règles de détection existantes.
