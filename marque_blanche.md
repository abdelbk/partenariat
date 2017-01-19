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

### Fonctionnement de l'API REST

#### Protocole

Le seul protocole autorisé est `HTTPS`. Toutes les communications sont donc chiffrées.

#### URL

L'URL de l'API est `https://api.www.simplebo.fr/v1/$chemin`. Le chemin sera explicité dans les sections suivantes.

#### Authentification 

L'authentification fonctionne sur un système de token transmis dans le header.
Si le token est **d4e9b0993d1e437605**, chaque appel à l'API doit contenir le header suivant :

```
Authorization:Token token="azertyuiop"
``` 

(clé : `Authorization`, valeur: `Token token="azertyuiop"`)

### Structure de l'API

#### Format des données
Les données de création de Lead doivent être envoyées par requête POST.
Les différents arguments sont :

| clé | valeur | Description | Obligatoire |
| --- | --- | --- | ---         |
| partner_code  | "foussier" | Code d’identification du partenaire de Simplébo                  | **oui**     |
| partner       | "LBA" ou "Foussier" | Source de l'appel                                       | **oui**     |
| campaign_code | 'foussier-simplebo-12' | Code d'identification de la campagne                 | non         |
| client_id     | "54d8b0fad5922248bf2f" | Code du client, que vous choisissez                  | **oui**     |
| lastname      | "foussier" | Nom du client                                                    | **oui**     |
| firstname     | "foussier" | Prénom du client                                                 | non         |
| client_title  | "Mme" | Civilité du client                                                    | non         |
| company_name  | "DécOrato" | Nom de la société cliente (Raison sociale)                       | non         |
| siret         | "182918291" | Siret de la société cliente                                     | non         |
| email         | "thierry.lhermitte@thierrylhermite.fr" | Email du client                      | **oui**     |
| phone_number1 | "0182103621" | Telephone 1 du client                                          | **oui**     |
| phone_number2 | "0629172923" | Téléphone 2 du client                                          | non         |
| address1      | "12 chemin de la Cote aux Ecureuils" | Première ligne de l'adresse du client  | non         |
| address2      | "" | Deuxième ligne de l'adresse du client                                    | non         |
| zipcode       | "28000" | Code postal du client                                               | **oui**     |
| city          | "Chartres" | Ville du client                                                  | **oui**     |
| country       | "France" | Pays du client                                                     | **oui**     |
| typology      | 12 | Typologie du client                                                      | **oui**     |

Voici une représentation JSON des données telles qu'elles pourraient être : 
```json
{
  "partner_code": "foussier",
  "partner": "LBA",
  "campaign_code": "foussier-simplebo-12",
  "client_id": "54d8b0fad5922248bf2f",
  "lastname": "Lhermitte",
  "firstname": "Thierry",
  "client_title": "Mme",
  "email": "thierry.lhermitte@thierrylhermite.fr",
  "company_name": "",
  "zipcode": "28000",
  "city": "Chartres",
  "country": "France",
  "siret": "182918291",
  "typology": 12
}
```
### Demande de création de maquette gratuite (envoi de Lead vers Simplébo)

Pour envoyer un lead à Simplébo par API lorsqu'il actionne le bouton `Demande de création de maquette` par exemple, il suffit d'effectuer une requete `POST` vers l'API sur l'URL : `https://api.www.simplebo.fr/v1/leads`

#### Réponse

La réponse de Simplébo est au format JSON.

En cas de succès :
```json
{
  "response" : 0,
  "text" : "OK",
  "redirect_to" :  "https://www.url-de-redirection.simplebo.net"
}
```

En cas d'échec :
```json
{
  "response" : 1,
  "text" : " Raison de l’erreur "
}
```

### Activation du service payant

Pour activer le service d'un client, qui peut avoir déjà été envoyé en Lead, ou ne pas encore exister dans la BDD de Simplébo, il suffit d'effectuer de nouveau une requete `POST` vers l'API sur l'URL : `https://api.www.simplebo.fr/v1/customers`

Les réponses sont exactement les mêmes que pour l'envoi de Lead en cas de succès ou d'échec.

### Exemples de code


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
