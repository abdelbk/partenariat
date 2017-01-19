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
Authorization:Token token="d4e9b0993d1e437605"
``` 

(clé : `Authorization`, valeur: `Token token="d4e9b0993d1e437605"`)

### Structure de l'API

#### Format des données
Les données de création de Lead doivent être envoyées par requête POST.
Les différents arguments sont :

| parametre | valeur | Description | Obligatoire |
| --- | --- | --- | ---         |
| partner_code  | "foussier" | Code d’identification du partenaire de Simplébo                  | **oui**     |
| partner       | "LBA" ou "Foussier" | Source de l'appel                                       | **oui**     |
| campaign_code | 'foussier-simplebo-12' | Code d'identification de la campagne                 | non         |
| client_id     | "54d8b0fad5922248bf2f" | Code du client, que vous choisissez                  | **oui**     |
| lastname      | "Lhermitte" | Nom du client                                                    | **oui**     |
| firstname     | "Thierry" | Prénom du client                                                 | non         |
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
  "response": 0,
  "text": "OK",
  "redirect_to": "https://www.url-de-redirection.simplebo.net"
}
```

En cas d'échec :
```json
{
  "response": 1,
  "text": "Raison de l’erreur"
}
```

### Activation du service payant

Pour activer le service d'un client, qui peut avoir déjà été envoyé en Lead, ou ne pas encore exister dans la BDD de Simplébo, il suffit d'effectuer de nouveau une requete `POST` vers l'API sur l'URL : `https://api.www.simplebo.fr/v1/customers`

Les réponses sont exactement les mêmes que pour l'envoi de Lead en cas de succès ou d'échec.

### Exemples de code
```php
  
// Vars initialization
define('SB_API_URL', 'https://api.www.simplebo.fr/v1/');
define('LEAD_PATH', 'leads');
define('CUST_PATH', 'customers');

$lead_url = SB_API_URL . LEAD_PATH;
$cust_url = SB_API_URL . CUST_PATH;

// Postfield array initialization
$customer_info = array(
  'partner_code'  => 'foussier',
  'partner'       => 'LBA',
  'campaign_code' => 'foussier-simplebo-12',
  'client_id'     => '54d8b0fad5922248bf2f',
  'lastname'      => 'Lhermitte',
  'firstname'     => 'Thierry',
  'client_title'  => 'Mme',
  'email'         => 'thierry.lhermitte@simplebo.fr',
  'company_name'  => '',
  'zipcode'       => '28000',
  'city'          => 'Chartres',
  'country'       => 'France',
  'siret'         => '182918291',
  'typology'      => 12,
  'other_info'    => "Autres informations dont Simplébo pourrait avoir l'utilité"
);

// HTTPS Request preparation
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $lead_url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, $customer_info);

// Send request
$req_result = curl_exec($ch);

// Process data
$api_response = json_decode($req_result);
//...
//...
//...
```

## 2. FTP - Échange de la liste des clients à facturer

### Structure des données

### Format de fichier

### Dépot du fichier

## 3. Webhooks - Récupération de modification d'état d'un client

### Fonctionnement du Webhooks

Le Webhooks est un callback vous permettant d'être abonné à certains événements sur Simplébo. Lorsqu'un de ces événements est déclenché, Simplébo envoie une requête HTTP POST à l'URL configurée du webhook. Le Webhooks peut être appelé lors d'une modification d'état d'un client. Par exemple, lorsque Simplébo résilie un client, un appel au Webhooks peut être effectué pour vous informer de la résiliation.

Dans un premier temps, vous pourrez transmettre à Simplébo une URL dur Webhooks, qui sera appelée lors d'un événement.

### Structure des données transmises lors d'un appel Webhooks

Les données transmises par la plateforme Simplébo lors d'un appel Webhooks sont envoyées dans les paramètres d'une requête POST.

Voici la table des paramètres :

| parametre     | valeurs         | Description                                                 |
| ---           | ---             | ---                                                         |
| event_type    | 0 / 1 / 2       | Code représentant le type d'événement                       |
| lead_creation_date | 2017-01-21 | Date de demande de maquette (première transmission d'info)  |
| service_starting_date | 2017-01-22 | Date d'activation du service, format YYYY-MM-DD                |
| billing_starting_date | 2017-01-22 | Date de début de facturation du service au client par Foussier, format YYYY-MM-DD |
| end_of_contract_date | "2018-11-11" / nil | Date de résiliation du service, format YYYY-MM-DD (si résiliation) |
| unsubscription_reason | "Retards de paiement" / "Ne voit plus l'intérêt d'un site" | Raison de la résiliation (si résiliation) |
| nb_of_paid_months | 14 | Nombre de mois déjà payés par le client                              |
| nb_of_offered_months | 6 | Nombre de mois offerts au client                                  |
| nb_of_options  | 0 / 1 / 2 | Nombre d'options (facturation Simplébo) souscrites par le client |
| client_id     | "54d8b0fad5922248bf2f" | Code du client                                       |
| lastname      | "foussier" | Nom du client                                                    |
| firstname     | "foussier" | Prénom du client                                                 |
| client_title  | "Mme" | Civilité du client                                                    |
| company_name  | "DécOrato" | Nom de la société cliente (Raison sociale)                       |
| siret         | "182918291" | Siret de la société cliente                                     |
| email         | "thierry.lhermitte@thierrylhermite.fr" | Email du client                      |
| phone_number1 | "0182103621" | Telephone 1 du client                                          |
| phone_number2 | "0629172923" | Téléphone 2 du client                                          |
| address1      | "12 chemin de la Cote aux Ecureuils" | Première ligne de l'adresse du client  |
| address2      | "" | Deuxième ligne de l'adresse du client                                    |
| zipcode       | "28000" | Code postal du client                                               |
| city          | "Chartres" | Ville du client                                                  |
| country       | "France" | Pays du client                                                     |

Lors d'un appel webhooks, la plateforme Simplébo envoie par la même occasion un certain nombre d'informations qui correspondent à un instantané de l'état des informations disponibles sur le client à ce moment. Ces infos peuvent être interpretées ou ignorées.

### Evénements entrainant un appel Webhooks

* Création du lead (callback d'appel API)
* Activation du service pour un client (callback d'appel API)
* Début de facturation du service par Foussier
* Résiliation du contrat d'un client
* Passage d'un contrat de payant à gratuit pour le client (geste commercial de Foussier)

D'autres événements ou d'autres données peuvent être implémentés par Simplébo sur demande. N'hésitez pas à faire des retours sur ce sujet.

## 4. Echanges non-automatisés - E-mails, téléphone...

### Vous souhaitez offrir le service pour l'un de vos clients

Dans le cas où vous souhaitez offrir le service à l'un de vos clients, pour faire une geste commercial par exemple, la transmission de cette information se fait par e-mail.

Exemple d'e-mail :
```
Expediteur : Foussier <partenariat@foussier.fr>
Destinataire : Simplebo <partenariats@simplebo.fr>
Sujet : [Résiliation][Foussier] Client Thierry Lhermitte - 54d8b0fad5922248bf2f

Bonjour,

Nous souhaitons faire un geste commercial en offrant le contrat au client Thierry Lhermitte (numéro client 54d8b0fad5922248bf2f).
Raison : gagnant de la loterie de Noël

Merci d'avance
```

### Vous souhaitez l'arrêt du service pour l'un de vos clients

Dans le cas où vous souhaitez résilier le service de façon unilatérale pour l'un de vos clients, suite à un impayé par exemple, ici encore, la transmission de cette information se fait par e-mail.

Exemple d'e-mail :
```
Expediteur : Foussier <partenariat@foussier.fr>
Destinataire : Simplebo <partenariats@simplebo.fr>
Sujet : [Résiliation][Foussier] Client Thierry Lhermitte - 54d8b0fad5922248bf2f

Bonjour,

Nous souhaitons résilier le client Thierry Lhermitte (numéro client 54d8b0fad5922248bf2f).
Raison : retards de paiement intempestifs.

Merci d'avance
```
