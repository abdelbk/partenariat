# Documentation d'intégration Simplébo 

**Intégration d'un partenariat avec Simplébo**

L'objectif du système de partenariat de Simplébo est de permettre à vos clients de prendre contact avec Simplébo dans l'optique de créer un site internet et d'informer Simplébo que c'est vous qui avez envoyé ce client.

Le système de partenariat s'appuie sur des "codes partenaires" fournis par Simplébo afin d'identifier le partenaire et l'offre à laquelle pourrait souscrire le client. Pour savoir qu'un client vient de votre part, un système de tracking faisant intervenir un "code partenaire" a été mis en place.

Ce code, ainsi que les coordonnées du client, peuvent être transmis à Simplébo de plusieurs façons.

### Sommaire
1. Page co-brandée
2. Checkbox dans le panier


## 1. Page co-brandée

Simplébo fournit à ses partenaires un formulaire permettant à leurs clients de remplir leurs informations de contact afin de se faire rappeler par Simplébo. Ce formulaire peut être intégré à votre site via une iframe ou être atteint par le client via un simple lien.

### Iframe

Le code de l'iframe se trouve dans le kit de partage de l'interface partenaire, sur le site de Simplébo.

Par exemple, pour un partenariat avec le code coupon **monpartenaire2015**, le code de l'iframe sera :
```html
<iframe src="https://www.simplebo.fr/demande-information?co=monpartenaire2015&i" width="640" height="480"></iframe>
```

L'espace laissé à l'iframe doit faire (h*l) 640px * 480px. L'iframe s'intègre en transparence dans la page dont le fond doit être d'une couleur claire pour obtenir un bon constraste.


### Lien vers le site Simplébo
Le lien vers le formulaire de contact tracké se trouve dans le kit de partage de l'interface partenaire, sur le site de Simplébo.

Par exemple, pour un partenariat avec le code coupon **monpartenaire2015**, le lien vers le formulaire sera :
```
https://www.simplebo.fr/demande-information?co=monpartenaire2015
```

## 2. Checkbox "je suis intéressé" dans le panier d'achat

### Intégration d'une checkbox au panier
La checkbox peut être intégrée à votre panier à côté d'une phrase du type :
```
Créez votre site web d'artisan et améliorez vos ventes.

80% des clients cherchent des informations sur Google avant d'accepter un devis.
Créez un site web simple et professionnel en 20 min grâce à notre partenaire Simplébo,
et gagnez plus de clients.
```

La sélection de la checkbox par le client entraîne l'envoi d'une requête de votre serveur vers le serveur Simplébo afin de transmettre les données clients.

### Requête server-to-server pour transmettre les infos à Simplébo
Simplébo vous permet d'envoyer directement les informations d'un client de serveur à serveur (S-to-S) s'il a exprimé d'une manière ou d'une autre le souhait d'être contacté par Simplébo pour en savoir plus.

Afin de simplifier l'intégration, cette fonctionnalité a été simplifiée au maximum : elle ne contient qu'une seule requête possible.

La requête attendue pour transmettre les données de contact du client est une requête POST à l'URL suivante :
```
https://api.www.simplebo.fr/users
```

Cette requête POST doit contenir les paramètres suivants :
- firstname *
- lastname *
- email *
- phone_number OU cell *
- code_partner *
- job_category
- other_info
(* champs obligatoires)

La réponse du serveur Simplébo est un JSON très simple qui contient les informations suivantes :
- response_code
- response_text

Si la requête a été effectuée correctement, le json retour est :
```json
{"response_code":0, "response_text":"OK"}
```

En cas d'erreur, le code réponse est égal à 1 et le texte dépend de l'erreur, par exemple :
```json
{"response_code":1, "response_text":"Missing code_partner parameter"}
```

L'API étant très récente, nous n'avons pas développé de codes d'erreurs spécifiques par soucis d'évolutivité.

### Exemple de code php d'utilisation du service S-to-S

```php

// Vars initialization
define('PARTNER_API_URL', 'https://api.www.simplebo.fr/users');

$other_info .= 'Départements d\'intervention : 75,91,78' . "\n";
$other_info .= 'Entreprise : Charpenterie SARL' . "\n";
$other_info .= 'Adresse complète : 79 avenue de la République, 75011 Paris';

// Postfield array initialization
$customer_info = array(
  'firstname'    = 'Thierry',
  'lastname'     = 'Lhermitte',
  'email'        = 'thierry.lhermitte@simplebo.fr',
  'phone_number' = '+33 3 01 19 28 19',
  'cell'         = '+33 6 10 91 82 91',
  'code_partner' = 'monpartenaire2015',
  'job_category' = 'charpentier',
  'other_info'   = $other_info
);

// HTTPS Request preparation
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, PARTNER_API_URL);
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
