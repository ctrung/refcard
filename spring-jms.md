# Lexique

JMS \
`jakarta.jms.ConnectionFactory` : (interface) L'usine utilisé par le client pour créer un objet `jakarta.jms.Connection`. 
`jakarta.jms.Connection` : (interface) Une connexion active vers un serveur JMS.
`jakarta.jms.Session` : (interface) Un contexte dédié à un seul thread pour envoyer et recevoir des messages. 

Spring \
(Réception) \
`org.springframework.jms.annotation.JmsListener` : (annotation) A déclarer sur une méthode pour recevoir des messages JMS. Plus facile à utiliser que `JmsTemplate` et en plus est asynchrone.
`org.springframework.jms.config.JmsListenerContainerFactory` : (interface) Une usine de `MessageListenerContainer`

(Envoi) \
`org.springframework.jms.core.JmsTemplate` : Classe utilitaire pour envoyer des messages. SUpporte aussi la réception, en synchrone seulement. 
