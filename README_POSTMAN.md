# Guide de test avec Postman

## Configuration

- **Base URL**: `http://localhost:8080/api`
- **Content-Type**: `application/json`

## ⚠️ Important : Génération automatique des IBAN

**Le numéro de compte (IBAN) est maintenant généré automatiquement !**

- Si vous ne fournissez pas de `numCompte`, un IBAN français valide sera généré automatiquement
- Si vous fournissez un `numCompte`, il sera validé comme IBAN valide
- Les IBAN générés sont uniques et conformes à la norme IBAN française

## Étapes de test

### 1. Créer les clients

Utilisez **POST** `/api/clients` avec les corps de requête suivants :

#### Client 1 - Jean Dupont
```json
{
  "nom": "Dupont",
  "prenom": "Jean",
  "dateNaissance": "1985-03-15",
  "sexe": "M",
  "courriel": "jean.dupont@email.com",
  "adresse": "123 Rue de la République, 75001 Paris, France",
  "numTelephone": "+33612345678",
  "nationalite": "Française"
}
```

#### Client 2 - Marie Martin
```json
{
  "nom": "Martin",
  "prenom": "Marie",
  "dateNaissance": "1990-07-22",
  "sexe": "F",
  "courriel": "marie.martin@email.com",
  "adresse": "45 Avenue des Champs-Élysées, 75008 Paris, France",
  "numTelephone": "+33623456789",
  "nationalite": "Française"
}
```

#### Client 3 - Pierre Bernard
```json
{
  "nom": "Bernard",
  "prenom": "Pierre",
  "dateNaissance": "1978-11-08",
  "sexe": "M",
  "courriel": "pierre.bernard@email.com",
  "adresse": "78 Boulevard Saint-Germain, 75006 Paris, France",
  "numTelephone": "+33634567890",
  "nationalite": "Française"
}
```

**Important** : Notez les IDs retournés pour chaque client créé. Vous devrez les utiliser pour créer les comptes.

### 2. Créer les comptes

Utilisez **POST** `/api/comptes` avec les corps de requête suivants :

**⚠️ Remplacez les `clientId` par les IDs réels retournés lors de la création des clients !**

**💡 Le champ `numCompte` est maintenant optionnel - un IBAN sera généré automatiquement si omis !**

#### Compte 1 - Compte courant pour Jean Dupont (IBAN auto-généré)
```json
{
  "typeCompte": "COURANT",
  "solde": 2500.50,
  "clientId": 1
}
```

#### Compte 2 - Compte épargne pour Jean Dupont (IBAN auto-généré)
```json
{
  "typeCompte": "EPARGNE",
  "solde": 15000.00,
  "clientId": 1
}
```

#### Compte 3 - Compte courant pour Marie Martin (IBAN auto-généré)
```json
{
  "typeCompte": "COURANT",
  "solde": 3200.75,
  "clientId": 2
}
```

#### Compte 4 - Compte épargne pour Marie Martin (IBAN auto-généré)
```json
{
  "typeCompte": "EPARGNE",
  "solde": 25000.00,
  "clientId": 2
}
```

#### Compte 5 - Compte courant pour Pierre Bernard (IBAN auto-généré)
```json
{
  "typeCompte": "COURANT",
  "solde": 5000.25,
  "clientId": 3
}
```

#### Compte 6 - Compte épargne pour Pierre Bernard (IBAN auto-généré)
```json
{
  "typeCompte": "EPARGNE",
  "solde": 35000.50,
  "clientId": 3
}
```

### 3. Optionnel : Créer un compte avec IBAN manuel

Si vous souhaitez fournir votre propre IBAN (doit être valide) :

```json
{
  "numCompte": "FR7612345678901234567890123",
  "typeCompte": "COURANT",
  "solde": 2500.50,
  "clientId": 1
}
```

**Note** : L'IBAN fourni sera validé. S'il n'est pas valide, une erreur sera retournée.

## Types de compte disponibles

- `COURANT` : Compte courant
- `EPARGNE` : Compte épargne

## Vérifications

Après la création, vous pouvez tester :

- **GET** `/api/clients` - Liste tous les clients
- **GET** `/api/clients/{id}` - Récupère un client par ID
- **GET** `/api/comptes` - Liste tous les comptes
- **GET** `/api/comptes/{id}` - Récupère un compte par ID
- **GET** `/api/comptes/client/{clientId}` - Liste les comptes d'un client

## Fonctionnalités IBAN

- ✅ Génération automatique d'IBAN français valides et uniques
- ✅ Validation des IBAN fournis manuellement
- ✅ Vérification d'unicité avant création
- ✅ Format conforme à la norme IBAN (FR)
