# Self-Database

## Description
Self-Database est un mini SGBD educatif inspire de l'algebre relationnelle.
Le projet implemente un moteur SQL simple, un stockage de tables sur fichiers,
et un mode client/serveur avec un protocole texte. Il permet de creer des
bases, manipuler des relations, et executer des requetes elementaires.

## Fonctionnement general
- Le client envoie une commande SQL en texte brut.
- Le serveur parse la commande, l'execute, puis renvoie le resultat.
- Les donnees sont stockees dans le dossier defini par `DATABASE` dans la
	configuration.

Architecture principale :
- `server/DBServer` : demarre le serveur TCP.
- `server/DBClient` : client CLI pour envoyer des requetes.
- `execution/SQLExecutor` : route les commandes et applique les operations.
- `relation/` et `operation/` : representation relationnelle et operations.

## Architecture (vue rapide)
```
Client CLI (DBClient)
		|
		v
	TCP Socket
		|
		v
Serveur (DBServer) -> ClientHandler -> SQLParser -> SQLExecutor
										  |
										  v
						   Relation/Operation/TableStorage
```

## Prerequis
- Java 8+.
- PowerShell (commandes ci-dessous) ou un autre shell Java compatible.

## Lancer le projet (serveur + client)
1) Compiler tout le projet depuis le dossier `Self-Database` :

```powershell
javac -d out (Get-ChildItem -Recurse -Filter *.java | ForEach-Object { $_.FullName })
```

2) Demarrer le serveur :

```powershell
java -cp out server.DBServer
```

3) Demarrer un client (dans un autre terminal) :

```powershell
java -cp out server.DBClient
```

4) Entrer des commandes SQL dans le client. Tapez `exit` pour quitter.

## Exemple complet (creation + insertion + select)
```
CREATE DATABASE demo
USE demo

CREATE TABLE Cours (ID int, NOM string)
INSERT INTO Cours (ID, NOM) VALUES (1, Algo)
INSERT INTO Cours (ID, NOM) VALUES (2, BD)

SELECT * FROM Cours
SELECT NOM FROM Cours WHERE ID = 2
```

## Lancer le projet (exemple JDBC)
Un exemple d'utilisation type JDBC est disponible dans `main/Main`.
Apres compilation, vous pouvez le lancer :

```powershell
java -cp out main.Main
```

La connexion utilise une URL de type `jdbc:mydatabase://localhost:5000/test2`.

## Configuration
Le fichier [config/conf.txt](config/conf.txt) contient :
- `DATABASE` : dossier racine des bases de donnees (ex: `MaBaseDeDonnees/`).
- `PORT` : port du serveur TCP.
- `HOST` : adresse du serveur pour le client.

## Fonctionnalites SQL supportees
Liste issue de [config/fonctionalite.txt](config/fonctionalite.txt).

1) Creer une table
```
CREATE TABLE Table_name (Attribute1 Attribute1_type Limite1, Attribute2 Attribute2_type Limite2)
```

2) Insertion
```
INSERT INTO Table_name (Attribute1, Attribute2) VALUES (Value1, Values2)
```

3) Supprimer une table
```
DROP TABLE test2
```

4) Voir les informations de chaque attribut d'une table
```
DESCRIBE test1
```

5) Selection avec ou sans condition
```
SELECT * FROM Table_name
SELECT * FROM Table_name WHERE condition1 AND condition2
```
Exemple de condition :
```
NOM = Algo
ID = 2
```

6) Jointure
```
SELECT * FROM Table_name1 CROSS JOIN Table_name2
SELECT * FROM Table_name1 JOIN Table_name2
SELECT * FROM Table_name1 INNER JOIN Table_name2 ON Table_name1.Attribute != Table_name2.Attribute
```

7) Selection d'attributs
```
SELECT Attribute1 Attribute2 FROM Table_name
SELECT Attribute1 Attribute2 FROM Table_name WHERE condition1 AND condition2
SELECT Attribute1 FROM Table_name1 CROSS JOIN Table_name2
```

8) Effacer une ou plusieurs lignes
```
DELETE FROM test2
DELETE FROM test2 WHERE ID = 10
```

9) Mettre a jour une ligne
```
UPDATE test2 SET ID = 10 WHERE ID = 1
```

10) Calculer un attribut numerique
```
SELECT MIN(id) FROM test2
SELECT MAX(id) FROM test2
SELECT AVG(id) FROM test2
SELECT SUM(id) FROM test2
```

11) Afficher toutes les tables d'une base
```
SHOW tables
```

12) Creer une base de donnees
```
CREATE DATABASE nom_de_la_Base
```

13) Supprimer une base de donnees
```
DROP DATABASE nom_de_la_Base
```

14) Afficher toutes les bases existantes
```
SHOW DATABASES
```

## Limitations connues
- Parser SQL volontairement simple (syntaxe stricte, pas de sous-requetes).
- Pas de transactions, ni d'index, ni de contraintes avancees.
- Jointures limitees aux formes presentes dans la liste des fonctionnalites.

## Notes
- Les bases sont des dossiers, et les tables sont stockees sur fichiers.
- Le fichier [script.sql](script.sql) peut servir d'exemple de commandes.