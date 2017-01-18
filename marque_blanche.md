# Documentation d'intégration technique Simplébo 

**Intégration d'un partenariat de type "facturation" avec Simplébo**

L'objectif de ce système de partenariat proposé par Simplébo est de permettre à une entreprise de proposer à ses clients un site internet professionel en intégrant le système Simplébo.

Le système de partenariat fonctionne par API et Webhooks. Il permet par exemple de transmettre à Simplébo les informations d'un compte client à créer, ou de récupérer la liste des clients à facturer.

### Sommaire
1. API - Demande de création maquette et activation service
  1. Fonctionnement de l'API
  2. Structure des données de l'API
  3. Demande de création de maquette
  4. Activation du service
  5. Exemples de code
2. FTP - Échange de la liste des clients à facturer
  1. Structure des données
  2. Format de fichier
  3. Dépot du fichier
3. Webhooks - Récupération de modification d'état d'un client
  1. Fonctionnement d'un webhooks
  2. Structure des données transmises via webhooks
  3. Evénements pris en compte
4. Echanges non-automatisés - E-mails, téléphone...
  1. Offrir le service à un client
  2. Vous souhaitez l'arrêt du service pour un de vos client

## 1. API - Demande de création maquette et activation service

Simplébo met à votre disposition une API qui permet de transmettre deux informations : la demande de création de maquette (gratuite) et l'activation du service (payant).

### Structure de l'API

### Demande de création de maquette gratuite

### Activation du service payant

### Exemple de code


## 2. FTP - Échange de la liste des clients à facturer

### Structure des données

### Format de fichier

### Dépot du fichier

## 3. Webhooks - Récupération de modification d'état d'un client

### Fonctionnement d'un webhooks
### Structure des données transmises via webhooks
### Evénements pris en compte

## 4. Echanges non-automatisés - E-mails, téléphone...

### Offrir le service à un client

### Vous souhaitez l'arrêt du service pour un de vos client
