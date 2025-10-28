# sms-api-documentation

# Aperçu

L’API HTTP SMS permet d’envoyer des messages SMS, de récupérer les accusés de réception (DLR), de consulter le solde de votre compte et d’obtenir la liste des expéditeurs autorisés.  
Ce document décrit les détails techniques et la manière d’intégrer cette API à votre système.

---

## 1. Authentification

Pour utiliser cette API, vous devez disposer :  
- D’une clé d’authentification (fournie lors de l’activation)  
- D’un expéditeur déclaré (nom d’expéditeur qui apparaît chez le destinataire)  

---

## 2. Envoi de SMS

**Requête :**
```http
GET https://api.l2t.io/dz/s/api/v1/sms?fct=sms&key=%KEY%&mobile=%MOBILE%&sms=%SMS%&sender=%SENDER%&date=%DATE%&heure=%HEURE%&content-type=%CONTENT-TYPE%
```
**Description :**  
Permet d’envoyer un SMS à un ou plusieurs destinataires.

**Paramètres :**

| Paramètre      | Obligatoire | Type   | Description                                      |
|----------------|------------|--------|--------------------------------------------------|
| fct            | Oui        | Chaîne | Doit être sms                                    |
| key            | Oui        | Chaîne | Votre clé d’authentification                     |
| mobile         | Oui        | Chaîne | Numéro du destinataire au format international (ex : 21612345678) |
| sms            | Oui        | Chaîne | Contenu du message                               |
| sender         | Oui        | Chaîne | Un de vos expéditeurs déclarés                  |
| date           | Non        | Date   | Date planifiée d’envoi                           |
| heure          | Non        | Heure  | Heure planifiée d’envoi                          |
| content-type   | Non        | Chaîne | Format de la réponse : json (par défaut) ou xml  |

**Exemple de requête :**
```
https://api.l2t.io/dz/s/api/v1/sms?fct=sms&key=abcd1234&mobile=21612345678&sms=Bonjour+Monde&sender=MaMarque
```

**Exemple de réponse :**

```json
{
  "success": true,
  "message": "the message is saved in the queue",
  "code": "message_queued",
  "status": "200",
  "data": {
    "message_id": 12345,
    "msisdn": "21612345678",
    "ref": "abc123"
  }
}
```
---

## 3. Récupération du DLR (Accusé de Réception)

**Requête :**
```http
GET  https://api.l2t.io/dz/s/api/v1/sms?fct=dlr&key=%KEY%&msg_id=%MSG_ID%&content-type=%CONTENT-TYPE%
```
**Description :**

Permet d’obtenir le statut de livraison d’un ou plusieurs SMS envoyés.

**Exemple de réponse :**

```json
{
  "success": true,
  "message": "dlr response",
  "code": "dlr_success",
  "status": 200,
  "data": [
    {
      "message_id": 12345,
      "dlr": "DELIVERED",
      "dlr_date": "2025-10-20 10:00:00",
      "ref": "abc123"
    }
  ]
}

```

**Statuts possibles :**

| DLR     | Signification     |
|----------------|-------|
|DELIVERED       |Le SMS a été remis avec succès |
| UNDELIVERED    | Le SMS n’a pas pu être remis |
| EXPIRED        | Le SMS a expiré avant livraison |
| REJECTED       | Le SMS a été rejeté (numéro sur liste noire) |
| UNKNOWN        | Le SMS est en attente de confirmation |

---
## 4. Récupération du Solde

**Requête :**
```http
GET https://api.l2t.io/dz/s/api/v1/sms?fct=balance&key=%KEY%&content-type=%CONTENT-TYPE%
```
**Description :**

Permet de consulter le solde de votre compte.

**Exemple de réponse :**

```json
{
  "success": true,
  "message": "Account balance",
  "code": "balance_success",
  "status": 200,
  "data": {
    "id_client": 123,
    "balance": 250.00
  }
}
```
---

## 5. Récupération de la Liste des Expéditeurs

**Requête :**
```http
GET https://api.l2t.io/dz/s/api/v1/sms?fct=sender&key=%KEY%&content-type=%CONTENT-TYPE%
```
**Description :**

Renvoie la liste des noms d’expéditeurs autorisés pour votre compte.

**Exemple de réponse :**

```json
{
  "success": true,
  "message": "Account list sender authorized",
  "code": "sender_success",
  "status": 200,
  "data": [
    { "sender": "MaMarque", "total_mt": 1000 },
    { "sender": "PromoSMS", "total_mt": 500 }
  ]
}
```
---
## 6. Codes d’État HTTP

| DLR     | Signification     |
|----------------|-------|
|200       |Requête réussie |
| 400    | Paramètre manquant ou invalide |
| 401       | Clé API invalide |
| 500      | Erreur interne du serveur |

---


