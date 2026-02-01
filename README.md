# RNB Data Ingestion - MongoDB

## 📋 Description

Ce projet permet d'ingérer les données du **Référentiel National des Bâtiments (RNB)** dans une base de données MongoDB en utilisant Apache Spark et PySpark.

### À propos de l'API RNB

Le RNB est une API publique fournie par le gouvernement français (beta.gouv.fr) qui recense l'ensemble des bâtiments en France. L'API permet d'accéder aux informations suivantes :

- **Identifiants uniques** des bâtiments (rnb_id)
- **Adresses** complètes (numéro, rue, ville, code postal, code INSEE)
- **Géolocalisation** (coordonnées GPS, polygones de forme)
- **Statut** du bâtiment (construit, en construction, démoli, etc.)
- **Identifiants externes** provenant d'autres référentiels (BDNB, BDTOPO)

**Documentation API** : https://rnb-api.beta.gouv.fr/

## 🎯 Fonctionnalités

- Récupération automatique de tous les bâtiments d'une commune via pagination curseur
- Transformation des données avec Apache Spark
- Stockage dans MongoDB Atlas
- Gestion des erreurs et retry automatique
- Affichage de la progression de l'ingestion

## 🔧 Prérequis

### Logiciels requis

- **Python 3.8+**
- **Google Colab** (recommandé) ou environnement Jupyter local
- **Compte MongoDB Atlas** (gratuit) ou instance MongoDB locale

### Bibliothèques Python

Les bibliothèques suivantes seront installées automatiquement dans le notebook :

```
pyspark==3.5.0
pymongo==4.16.0
requests==2.32.4
```

## 🚀 Installation et lancement

### Option 1 : Google Colab (recommandé)

1. **Ouvrir le notebook dans Google Colab**
   - Téléchargez le fichier `rnb_ingestion__2_.ipynb`
   - Allez sur [Google Colab](https://colab.research.google.com/)
   - Cliquez sur `Fichier` > `Importer un notebook`
   - Sélectionnez le fichier téléchargé

2. **Configurer MongoDB**
   - Remplacez la variable `MONGO_URI` dans la cellule 2 par votre propre URI de connexion MongoDB :
   
   ```python
   MONGO_URI = "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/?appName=<AppName>"
   ```

3. **Paramétrer la commune** (optionnel)
   - Par défaut, le code récupère les données de Bordeaux (INSEE: 33063)
   - Pour changer de commune, modifiez le paramètre `insee_code` dans l'URL de la cellule 3 :
   
   ```python
   BASE_URL = "https://rnb-api.beta.gouv.fr/api/alpha/buildings/?insee_code=XXXXX&limit=100"
   ```

4. **Exécuter le notebook**
   - Exécutez les cellules une par une avec `Shift + Enter`
   - Ou exécutez tout le notebook : `Exécution` > `Tout exécuter`

### Option 2 : Environnement local

1. **Cloner ou télécharger le projet**
   ```bash
   git clone <votre-repo>
   cd rnb-ingestion
   ```

2. **Installer Jupyter**
   ```bash
   pip install jupyter
   ```

3. **Installer les dépendances**
   ```bash
   pip install pyspark pymongo requests
   ```

4. **Configurer MongoDB**
   - Modifiez la variable `MONGO_URI` avec vos identifiants

5. **Lancer Jupyter**
   ```bash
   jupyter notebook
   ```

6. **Ouvrir et exécuter le notebook**
   - Ouvrez `rnb_ingestion__2_.ipynb` dans l'interface Jupyter
   - Exécutez les cellules

## 📊 Structure du projet

```
.
├── rnb_ingestion__2_.ipynb    # Notebook principal
└── README.md                   # Ce fichier
```

## 🗄️ Configuration MongoDB

### Créer un cluster MongoDB Atlas (gratuit)

1. Allez sur [MongoDB Atlas](https://www.mongodb.com/cloud/atlas/register)
2. Créez un compte gratuit
3. Créez un nouveau cluster (M0 - gratuit)
4. Créez un utilisateur de base de données :
   - `Database Access` > `Add New Database User`
   - Choisissez un nom d'utilisateur et un mot de passe
5. Autorisez votre IP :
   - `Network Access` > `Add IP Address`
   - Ajoutez `0.0.0.0/0` pour autoriser toutes les IPs (pour le développement uniquement)
6. Récupérez l'URI de connexion :
   - `Database` > `Connect` > `Connect your application`
   - Copiez l'URI et remplacez `<password>` par votre mot de passe

### Structure de la base de données

- **Database** : `rnb`
- **Collection** : `buildings`

Chaque document contient :

```json
{
  "building_id": "EC9ES2F1M8SM",
  "status": "constructed",
  "point": {
    "type": "Point",
    "coordinates": [-0.572615, 44.856833]
  },
  "shape": {
    "type": "MultiPolygon",
    "coordinates": [...]
  },
  "addresses": [...],
  "ext_ids": [...],
  "is_active": true
}
```

## 📝 Workflow du notebook

1. **Cellule 1** : Installation des dépendances
2. **Cellule 2** : Configuration de la session Spark et connexion MongoDB
3. **Cellule 3** : Récupération des données via l'API RNB avec pagination
4. **Cellule 4** : Transformation et aplatissement des données
5. **Cellule 5** : Écriture dans MongoDB

## ⚙️ Paramètres personnalisables

### Dans la cellule 3 :

```python
# Code INSEE de la commune (33063 = Bordeaux)
BASE_URL = "https://rnb-api.beta.gouv.fr/api/alpha/buildings/?insee_code=33063&limit=100"

# Délai entre les requêtes (en secondes)
sleep_s = 0.05

# Limite de résultats par page
limit = 100
```

### Dans la cellule 5 :

```python
# Mode d'écriture : "append", "overwrite", "ignore", "error"
.mode("append")

# Nom de la base de données
.option("database", "rnb")

# Nom de la collection
.option("collection", "buildings")
```

## 🔍 Codes INSEE des communes

Pour récupérer les données d'une autre commune, vous devez connaître son code INSEE :

- Paris : 75056
- Lyon : 69123
- Marseille : 13055
- Toulouse : 31555
- Nice : 06088

**Rechercher un code INSEE** : https://www.insee.fr/fr/recherche/recherche-geographique

## 📈 Exemple de sortie

```
300 buildings processed
600 buildings processed
🎉 TOTAL buildings fetched: 75112

root
 |-- building_id: string
 |-- status: string
 |-- point: map
 |-- shape: map
 |-- addresses: array
 |-- ext_ids: array
 |-- is_active: boolean
```

## ⚠️ Limitations et bonnes pratiques

- **Rate limiting** : L'API RNB peut avoir des limites de taux. Le délai de 0.05s entre les requêtes est recommandé
- **Volume de données** : Certaines grandes villes peuvent avoir 100 000+ bâtiments, ce qui peut prendre du temps
- **Mémoire** : Google Colab a des limites de RAM. Pour de très grandes communes, envisagez un traitement par lots
- **Connexion MongoDB** : N'exposez jamais vos identifiants MongoDB dans un dépôt public

## 🛠️ Dépannage

### Erreur de connexion MongoDB

```
MongoServerError: bad auth
```

**Solution** : Vérifiez vos identifiants MongoDB et assurez-vous que votre IP est autorisée

### Erreur de mémoire dans Colab

```
Killed - Out of Memory
```

**Solution** : Réduisez le nombre de bâtiments en choisissant une plus petite commune ou en ajoutant un filtre `limit` dans l'URL

### Timeout API

```
ReadTimeout
```

**Solution** : Augmentez le délai `sleep_s` à 0.1 ou 0.2 secondes

## 📄 Licence

Ce projet utilise des données publiques du RNB (beta.gouv.fr) sous licence ouverte.

## 👤 Auteur

Christian MBIPBIPE FOUEMKEU et Omar BAYOUD