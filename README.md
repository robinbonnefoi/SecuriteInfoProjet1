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
* (Suricata)

## Présentation de l'architecture :
Pour ce projet, nous avons utilisé syslog-ng pour s'occuper des logs et de leur transport, Wazuh pour l'IDS/IPS, Elasticsearch pour la gestion des logs et Kibana pour leur visualisation. (suricata)

## Schéma d'architecture :

## Organisation du GitHub :

## Installations :
  {mettre le fichier d'installations ici}

### lancement de l'interface Kibana :

sudo ss -ltnp | grep 5601

LISTEN 0      511             127.0.0.1:5601       0.0.0.0:*    users:(("MainThread",pid=57610,fd=22)) # Verification du fonctionnement

curl -I http://127.0.0.1:5601 # Se connecter à http://127.0.0.1:5601

sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token --scope kibana # Rentrer le code dans le site

sudo /usr/share/kibana/bin/kibana-verification-code # Rentrer le code de vérification dans le site

Recuperation du mot de passe :


sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic #ajout du -i pour choisir le mot de passe

<img width="526" height="541" alt="image" src="https://github.com/user-attachments/assets/f6ef89fc-9a57-4652-a0a4-02fc8c125842" />

Username : elastic

Password : mot de passe récupérer par la commande précédente 


menu de gauche -> Management -> Stack Management 
Puis menu de gauche -> Kibana -> Data views
Puis Create data view :
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
  {mettre les différents scénarios (5) et justifier les choix de scénarios}

## Résultats des tests : 
  {mettre les screens et résultats de chacun des tests}

