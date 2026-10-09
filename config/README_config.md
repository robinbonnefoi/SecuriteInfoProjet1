# Information

Ici se trouve les différentes information sur les configs qui peuvent ou doivent être changer pour assurer le bon fonctionnement du serveur


## ossec.conf
Localisation : /var/ossec/etc/ossec.conf


    <ossec_config>
      <global>
        <jsonout_output>yes</jsonout_output>
        <alerts_log>yes</alerts_log>
        <logall>no</logall>
        <logall_json>no</logall_json>
        <email_notification>yes</email_notification>        -> accepte les notifications
        <email_to>wazuh@example.wazuh.com</email_to>        -> E-mail de réception
        <smtp_server>localhost</smtp_server>
        <email_from>wazuh@example.wazuh.com</email_from>    -> E-mail d'envoie
        <email_maxperhour>12</email_maxperhour>             -> nombre max d'email par heure
        <email_log_source>alerts.log</email_log_source>
        <agents_disconnection_time>15m</agents_disconnection_time>
        <agents_disconnection_alert_time>0</agents_disconnection_alert_time>
        <update_check>yes</update_check>
      </global>

      <alerts>
        <log_alert_level>3</log_alert_level>
        <email_alert_level>9</email_alert_level>  -> niveau d'alerte requis pour l'envoi d'un mail
      </alerts>

## filebeat.yml
Localisation : /etc/filebeat/filebeat.yml

    output.elasticsearch:
        ...
        username: "elastic"
        password: "fR9LUhV4oh8c-aADYM*K"  -> mot de passe définis grâce a la commande sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

### sasl_passwd
Localisation : /etc/postfix/sasl_passwd


    [smtp.gmail.com]:587 maild'envoie:motdepassedumail

port et serveur à modifier selon le service de mail utilisé

    Gmail	smtp.gmail.com	587	
    Outlook.com smtp-mail.outlook.com	587	
    Microsoft 365 smtp.office365.com	587	
    Yahoo Mail	smtp.mail.yahoo.com	587	
    
Pour avoir le mot de passe du mail, il faut obtenir un mot de passe d'application qui est différent selon le service de mail utilisé

## main.cf
Localisation : /etc/postfix/main.cf

## local_rules.xml
Localisation : /var/ossec/etc/rules/local_rules.xml

## syslog-ng.conf
Localisation : /etc/syslog-ng/syslog-ng.conf

## local.rules
Localisation : /var/lib/suricata/rules/local.rules

## suricata.yaml
Localisation : /etc/suricata/suricata.yaml

# Commande de restart pour appliquer les modifications

    sudo systemctl daemon-reload
    sudo systemctl restart wazuh-manager
    sudo systemctl restart elasticsearch
    sudo systemctl restart kibana
    sudo systemctl restart postfix
    sudo systemctl restart filebeat
    sudo systemctl restart suricata
    sudo systemctl restart syslog_ng

