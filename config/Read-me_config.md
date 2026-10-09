# Information

Ici se trouve les différentes information qui peuvent ou doivent être changer pour assurer le bon fonctionnement du serveur


## ossec.conf
  
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
        <email_alert_level>9</email_alert_level>              -> niveau d'alerte requis pour l'envoi d'un mail
      </alerts>

## filebeat.yml

    username: "elastic"
    password: "fR9LUhV4oh8c-aADYM*K"                        -> mot de passe définis grâce a la commande sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
