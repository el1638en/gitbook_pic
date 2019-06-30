# Nexus

Nexus est un gestionnaire de repository qui répond principalement à 2 problématiques :
  - gérer les téléchargements des dépendances des projets,
  - stocker les livrables des applications développées en interne de l'entreprise.

Pendant le développement des applications, les développeurs téléchargent des librairies sur les repositories publiques Maven. Dans ce contexte, Nexus est un proxy entre les développeurs et les repositories publiques Maven. Il télécharge sur Internet les dépendances et les stocke dans un repository local. Pour les prochaines demandes de téléchargement, Nexus utilise son repository local et diminue les téléchargements excessifs sur Internet.

Ensuite, Nexus constitue un repository privé où on peut stocker les livrales des applications.
