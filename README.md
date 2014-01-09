# Documentation d'intégration Simplébo 

**Intégration d'un partenariat avec Simplébo**

L'objectif du partenariat avec Simplébo est de permettre à des clients de devispresto de prendre contact avec Simplébo dans l'optique de créer un site internet, en informant Simplébo du fait que le client a été envoyé par devispresto.

Simplébo a développé un système de partenariat qui s'appuie sur des "codes partenaires" fournis par Simplébo afin d'identifier le partenaire et l'offre auquel pourrait souscrire le client. Pour savoir si un client vient de devispresto, un système de tracking faisant intervenir un "code partenaire" a été mis en place.

Ce code, ainsi que les coordonnées du client, peuvent être transmis à Simplébo de plusieurs façons.

### Sommaire
1. Page co-brandée
2. Checkbox dans le panier


## 1. Page co-brandée

Simplébo fournit à devispresto un formulaire permettant à son client de remplir ses informations de base afin de se faire rappeler par Simplébo. Ce formulaire peut être intégré au site devispresto via une iframe ou être atteint par le client via un simple lien.

### Iframe

Le code de l'iframe se trouve dans le kit de partage de l'interface partenaire, sur le site de Simplébo.

Par exemple, pour un partenariat avec le code coupon **devispresto2014**, le code de l'iframe sera :
```html
<iframe src="https://www.simplebo.fr/demande-information?co=devispresto2014&i" width="640" height="480"></iframe>
```

L'espace laissé à l'iframe doit faire (h*l) 640px * 480px. L'iframe s'intègre en transparence dans la page dont le fond doit être d'une couleur claire pour obtenir un bon constraste.


### Lien vers le site Simplébo
Le lien vers le formulaire de contact tracké se trouve dans le kit de partage de l'interface partenaire, sur le site de Simplébo.

Par exemple, pour un partenariat avec le code coupon **devispresto2014**, le lien vers le formulaire sera :
```
https://www.simplebo.fr/demande-information?co=devispresto2014
```

## 2. Checkbox "je suis intéressé" dans le panier d'achat

### Intégration de la checkbox au panier
La checkbox est intégrée au panier par devispresto à côté d'une phrase du type :
```
Créez votre site web d'artisan et améliorez vos ventes.

80% des clients cherchent des informations sur Google avant d'accepter un devis.
Créez un site web simple et professionnel en 20 min grâce à notre partenaire Simplébo,
et gagnez plus de clients.
```

La selection de la checkbox entraîne l'envoi d'une requête du serveur devispresto au serveur Simplébo afin de transmettre les données clients.

### Requête server-to-server pour transmettre les infos à Simplébo
Simplébo permet à devispresto d'envoyer directement les informations d'un client de serveur à serveur (S-to-S) si celui-ci a exprimé d'une manière ou d'une autre le souhait d'être contacté par Simplébo pour en savoir plus.

Afin de simplifier l'intégration, cette fonctionnalité a été simplifiée au maximum : elle ne contient qu'une seule requête possible.

Devispresto enverra une requête POST à l'URL suivante :
```
https://api.www.simplebo.fr/users
```

Cette requête POST doit contenir les paramètres suivants :
- firstname
- lastname
- email
- phone_number
- code_partner

La réponse du serveur Simplébo est un JSON qui contient les informations suivantes :
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

### Exemple de code php d'utilisation du service S-to-S

```php

// Vars initialization
$partner_api_url = 'https://api.www.simplebo.fr/users'

// Postfield array initialization
$customer_info = array(
  'firstname'    = 'Thierry',
  'lastname'     = 'Lhermitte',
  'email'        = 'thierry.lhermitte@simplebo.fr',
  'phone_number' = '+33610918291',
  'code_partner' = 'devispresto2014',
);

// HTTPS Request preparation
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $partner_api_url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, $customer_info);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);

// Send request
$req_result = curl_exec($ch);

// Process data
$api_response = json_decode($result);
//...
//...
//...

```
