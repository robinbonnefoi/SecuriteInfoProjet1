# SecuriteInfoProjet1

## Groupe  6 :
  * BAZILLE Tony
  
  * BONNEFOI Robin
  
  * BEN MAKHLOUF Ines
  

## Outils utilisés : 
* Wazuh
* syslog-ng
* Elasticsearch
* Kibana
* Filebeat
* Postfix
* Suricata

## Présentation de l'architecture :
Pour ce projet, nous avons utilisé syslog-ng pour s'occuper des logs et de leur transport, Wazuh et Suricata pour la détection et génération d'alertes et logs, Elasticsearch pour la gestion de ceux-ci et Kibana pour leur visualisation. On a aussi utilisé Postfix afin de générer un mail si l'alerte atteint un certain niveau.

## Schéma d'architecture :

## Organisation du GitHub :

## Installations :

### wazuh manager
	sudo apt install -y wazuh-manager
	curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
	echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list 10.0.0.132="10.0.0.2" 
	sudo systemctl daemon-reload
	sudo nano /var/ossec/etc/ossec.conf # configurer le fichier (voir config)
	sudo systemctl enable --now wazuh-manager
	sudo systemctl restart wazuh-manager
	sudo systemctl status wazuh-manager
	

### suricata et syslog-ng
	sudo apt install suricata syslog-ng
	nano /etc/syslog-ng/syslog-ng.conf # configurer le fichier (voir config)
	sudo syslog-ng -s # vérifier s'il y a des erreur
	sudo systemctl restart syslog-ng
	sudo ss -lunp | grep 514 # s'il entend le port 514
	>>>> UNCONN 0      0            0.0.0.0:514        0.0.0.0:*    users:(("syslog-ng",pid=19291,fd=11)) 
	logger --server 127.0.0.1 --port 514 --udp "TEST security log" # envoyer le message
	sudo cat /var/log/network-test.log 
	>>>>> Sep 28 14:28:59 127.0.0.1 1 2026-09-28T14:28:59.773934-04:00 debian silver - - [timeQuality tzKnown="1" isSynced="1" syncAccuracy="585000"] TEST security log



### elastic search
	curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
	echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
	sudo apt update && sudo apt install elasticsearch
	sudo systemctl enable --now elasticsearch   
	sudo systemctl start elasticsearch.service

### reset elsatic search password
	sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

### kibana
	wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
	echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/9.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-9.x.list
	sudo apt-get update && sudo apt-get install kibana   

###  Downgrade kibana to 8.19.22
#### 1. Check versions
	curl -k -u elastic:VOTRE_MOT_DE_PASSE https://localhost:9200 | grep number
	dpkg -l | grep -E 'kibana|elasticsearch'

##### 2. Stop Kibana and back up its config
	sudo systemctl stop kibana
	sudo cp -a /etc/kibana /etc/kibana.bak

#### 3. Switch the APT repo from 9.x to 8.x
	ls /etc/apt/sources.list.d/            # find the elastic-9.x file
	sudo rm /etc/apt/sources.list.d/elastic-9.x.list
	echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
	sudo apt update

#### 4. Install the exact matching version
	apt list -a kibana                      # confirm 8.19.22 is available
	sudo apt install kibana=8.19.22 --allow-downgrades

#### 5. Prevent an accidental upgrade to 9.x
	sudo apt-mark hold kibana

#### 6. Start it
	sudo systemctl daemon-reload
	sudo systemctl start kibana
	sudo journalctl -u kibana -f

### filebeat : 

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
	{
	  "index_patterns": ["wazuh-alerts-4.x-*"],
	  "data_stream": {},
	  "template": {
	    "settings": {
	      "index.refresh_interval": "5s"
	    }
	  }
	}

### Service de Mail

	sudo apt update && sudo apt install postfix -y
	sudo nano /etc/postfix/sasl_passwd # configurer le fichier (voir config)
	sudo chmod 600 /etc/postfix/sasl_passwd
	sudo postmap /etc/postfix/sasl_passwd
	sudo nano /etc/postfix/main.cf # configurer le fichier (voir config)
	sudo systemctl restart postfix
	sudo nano /var/ossec/etc/ossec.conf # configurer le fichier (voir config)
	sudo systemctl restart wazuhmanager
	
### lancement de l'interface Kibana :

	sudo ss -ltnp | grep 5601
LISTEN 0      511             127.0.0.1:5601       0.0.0.0:*    users:(("MainThread",pid=57610,fd=22)) # Ce que la commande doit retourner
	curl -I http://127.0.0.1:5601 # Se connecter à http://127.0.0.1:5601
	sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token --scope kibana # Rentrer le code dans le site
	sudo /usr/share/kibana/bin/kibana-verification-code # Rentrer le code de vérification dans le site

Recuperation du mot de passe :


	sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic #ajout du -i pour choisir le mot de passe

<img width="526" height="541" alt="image" src="https://github.com/user-attachments/assets/f6ef89fc-9a57-4652-a0a4-02fc8c125842" />

	Username : elastic
	Password : mot de passe récupérer par la commande précédente 

<img width="1799" height="960" alt="2026_10_07_0wj_Kleki" src="https://github.com/user-attachments/assets/6a9f5c4d-581b-4473-a0d7-6c5aafe32374" />


Interface Kibana, pour créer une nouvelle source de données, il faut aller dans stack management puis dans Data views

<img width="1799" height="960" alt="2026_10_07_0wm_Kleki" src="https://github.com/user-attachments/assets/bdd477ac-9b9d-457b-bab4-71a8e035c6f8" />

Puis Create data view :

<img width="1799" height="960" alt="image" src="https://github.com/user-attachments/assets/99b12802-ece0-41e9-aa60-e679d68fc0b6" />

	Name : parametre de notre choix
	Index pattern : le pattern d'index utilisé
puis save data view
	
Analytics puis Discover pour voir les analyses
On peut filtrer par niveau de dangerosité dans le QKL avec la formule rule.level >= 10
avec le niveau de danger qui varie entre 0 et 15.

## Interface Kibana :
<img width="1798" height="966" alt="image" src="https://github.com/user-attachments/assets/67f72ad2-62bf-4451-a753-2cc1c5feb3fd" />
Dans le menu Analytics -> Discover, on peut voir les différents horaires a laquelle les tests ont eu lieu, avec une description et le niveau d'impact. 

## Scénarios de tests :
  {justifier les choix de scénarios}
  DOS -> flood
  Spearphishing grâce à l'application Gophish
  scan de port
  Attaque par dictionnaire
  http frauduleux

## Mise en place des scénarios de tests

### Installation de MikuMikuBeam (DOS)
Suivre les instructions de ce git (dépend de la machine utilisé) :
	
https://github.com/sammwyy/mikumikubeam.git

### Installation de Hydra (Attaque par dictionnaire)

	sudo apt install hydra
#### petite liste
	curl -L -o /tmp/rockyou.txt.gz https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt

#### Ou la grande liste
	git clone https://github.com/danielmiessler/SecLists.git /tmp/SecLists
	ls /tmp/SecLists/Passwords/Common-Credentials/
	hydra -l user@vbox -P /tmp/SecLists/Passwords/Common-Credentials/xato-net-10-million-passwords.txt ssh://127.0.0.1:2200 -t 4

### Installation de Gophish (Spearphishing)
	git clone https://github.com/gophish/gophish.git 
	cd gophish
	go build
	sudo ./gophish
le mot de passe est dans le log: time="2026-10-07T18:58:04-04:00" level=info msg="Please login with the username admin and the password 1ee58d66f89515d7"
Connecter sur https://127.0.0.1:3333

## Résultats des tests : 
  {mettre les screens et résultats de chacun des tests}

