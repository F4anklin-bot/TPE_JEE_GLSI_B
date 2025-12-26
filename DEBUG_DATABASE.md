# Guide de débogage - Problème de persistance en base de données

## Modifications apportées

1. ✅ Ajout de `EntityManager.flush()` après chaque `save()` pour forcer l'écriture immédiate
2. ✅ Amélioration de la configuration des transactions dans `application.properties`

## Vérifications à faire

### 1. Vérifier la connexion à MySQL

Assurez-vous que :
- MySQL est démarré
- Le port 3306 est accessible
- Les identifiants dans `application.properties` sont corrects :
  - username: `root`
  - password: `frfrfrfr`

### 2. Vérifier que la base de données existe

Connectez-vous à MySQL et vérifiez :
```sql
SHOW DATABASES;
```

La base `gest_banque` devrait exister. Si elle n'existe pas, elle sera créée automatiquement au démarrage.

### 3. Vérifier les tables

```sql
USE gest_banque;
SHOW TABLES;
```

Vous devriez voir les tables `clients` et `comptes`.

### 4. Vérifier les données

```sql
SELECT * FROM clients;
SELECT * FROM comptes;
```

### 5. Vérifier les logs de l'application

Au démarrage de l'application, vous devriez voir :
- Les requêtes SQL générées (car `spring.jpa.show-sql=true`)
- Les messages de connexion à la base de données

### 6. Tester avec Postman

1. Créer un client via POST `/api/clients`
2. Vérifier la réponse (devrait retourner 201 CREATED avec les données)
3. Immédiatement après, exécuter GET `/api/clients` pour voir si le client apparaît
4. Vérifier directement dans MySQL avec `SELECT * FROM clients;`

### 7. Problèmes possibles

#### Problème : Les données n'apparaissent pas dans MySQL mais l'API retourne 201

**Causes possibles :**
- La transaction n'est pas commitée (peut-être un rollback silencieux)
- Connexion à une autre base de données
- Problème de cache Hibernate

**Solutions :**
- Vérifier les logs pour voir les requêtes SQL
- Vérifier que vous êtes connecté à la bonne base de données
- Redémarrer l'application

#### Problème : Erreur de connexion

**Vérifier :**
- MySQL est démarré
- Les identifiants sont corrects
- Le port 3306 n'est pas bloqué par un firewall

#### Problème : Les tables n'existent pas

**Solution :**
- Vérifier `spring.jpa.hibernate.ddl-auto=update` dans `application.properties`
- Redémarrer l'application pour créer les tables

## Commandes SQL utiles

```sql
-- Voir tous les clients
SELECT * FROM clients;

-- Voir tous les comptes
SELECT * FROM comptes;

-- Voir les comptes avec les informations du client
SELECT c.*, cl.nom, cl.prenom 
FROM comptes c 
JOIN clients cl ON c.id_client = cl.id;

-- Compter les clients
SELECT COUNT(*) FROM clients;

-- Compter les comptes
SELECT COUNT(*) FROM comptes;

-- Supprimer toutes les données (ATTENTION !)
DELETE FROM comptes;
DELETE FROM clients;
```

## Test rapide

1. Démarrer l'application Spring Boot
2. Dans Postman, créer un client :
   ```
   POST http://localhost:8080/api/clients
   Content-Type: application/json
   
   {
     "nom": "Test",
     "prenom": "User",
     "dateNaissance": "1990-01-01",
     "sexe": "M",
     "courriel": "test@test.com",
     "adresse": "123 Test Street",
     "numTelephone": "+33612345678",
     "nationalite": "Française"
   }
   ```
3. Vérifier dans MySQL :
   ```sql
   SELECT * FROM clients WHERE courriel = 'test@test.com';
   ```

Si le client n'apparaît pas dans MySQL mais que l'API retourne 201, il y a un problème de transaction ou de connexion.

