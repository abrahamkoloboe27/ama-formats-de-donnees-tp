# TP · Formats de données — AMA

Travaux pratiques du module **« Formats de données : du CSV au lakehouse »** (UE Ingénierie des données, AMA · cours 2 h + TD/TP 2 h).

**Objectif :** lire, écrire et convertir les principaux formats de données avec **pandas** (CSV, Excel, JSON, Parquet, Delta Lake), et voir comment chacun se comporte sur **un même fichier volumineux** : taille sur le disque, vitesse de lecture et types conservés.

Chaque notebook suit le même rythme : un paragraphe court, un bout de code, la sortie, puis une ou deux phrases pour l'interpréter. Il se termine par des **exercices corrigés** (sauf le 00) et un encadré **« À retenir »**.

---

## Le fichier de base : les taxis jaunes de New York

Tout le TP part d'un seul fichier officiel : les **3 952 451 courses des taxis jaunes de New York en mars 2026**, publiées en Parquet par la [NYC Taxi & Limousine Commission](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

| Le même mois, enregistré en… | Taille |
|---|---:|
| Parquet (fichier officiel, compression ZSTD) | **68 Mo** |
| CSV (converti au notebook 02) | **425 Mo** |

On le convertit dans chaque format du cours pour comparer. Les petits fichiers « pièges » (séparateur, encodage, JSON imbriqué) reprennent la **boutique de Cotonou** des slides. Le notebook 07 récupère aussi des données des bases **PostgreSQL** et **MongoDB** du TP « Sources de données ».

## Le parcours

| # | Notebook | Ce que l'on fait | Durée |
|---|---|---|---|
| 00 | [Préparer son poste et les données](notebooks/00_preparer_son_poste.ipynb) | Vérifier les bibliothèques et télécharger le fichier de base | 5 min |
| 01 | [Pandas 101 : les bases](notebooks/01_pandas_101.ipynb) | Lire, regarder, filtrer, grouper, joindre et écrire un tableau | 20 min |
| 02 | [Le format CSV](notebooks/02_format_csv.ipynb) | Parquet → CSV de 425 Mo, types perdus, lecture partielle, gzip, séparateurs et encodages | 20 min |
| 03 | [Le format Excel](notebooks/03_format_excel.ipynb) | Limite du million de lignes, un `.xlsx` est un zip, moteurs de lecture, plusieurs feuilles | 15 min |
| 04 | [Le format JSON](notebooks/04_format_json.ipynb) | JSON et JSON Lines, dates perdues, accents, JSON imbriqué et `json_normalize` | 15 min |
| 05 | [Le format Parquet](notebooks/05_format_parquet.ipynb) | Métadonnées, lecture de colonnes, filtres, compressions, partitionnement | 15 min |
| 06 | [Comparatif des formats](notebooks/06_comparatif_formats.ipynb) | Le même échantillon dans 7 formats : taille, écriture, lecture, types conservés ; le mois complet en CSV contre Parquet | 15 min |
| 07 | [Des bases de données vers les fichiers](notebooks/07_bases_de_donnees_vers_fichiers.ipynb) | PostgreSQL et MongoDB → CSV, JSON, Parquet | 15 min |
| 08 | [Delta Lake et Iceberg](notebooks/08_delta_lake_iceberg.ipynb) | Une table « vivante » : ajout, suppression, historique, *time travel* ; Iceberg en bonus | 15 min |

Le parcours complet dépasse les 2 heures de la séance : c'est voulu. Le **[plan de la séance](docs/plan_tp.md)** indique ce qui se fait en salle et ce qui se termine en autonomie.

---

## Installation

### 1. Récupérer le dépôt

```bash
git clone https://github.com/abrahamkoloboe27/ama-formats-de-donnees-tp.git
cd ama-formats-de-donnees-tp
```

### 2. Installer les dépendances (Python ≥ 3.11)

Avec **uv** (recommandé) :

```bash
uv sync
```

Ou avec **pip**, dans un environnement virtuel :

```bash
python -m venv .venv
source .venv/bin/activate        # Windows : .venv\Scripts\activate
pip install -r requirements.txt
```

### 3. Lancer Jupyter

```bash
uv run jupyter lab               # ou simplement : jupyter lab (environnement pip activé)
```

Exécutez d'abord `notebooks/00_preparer_son_poste.ipynb` : il vérifie les bibliothèques et télécharge les 68 Mo du fichier de base dans `data/`. Prévoyez environ **1,5 Go** d'espace libre pour les fichiers produits dans `output/`.

### 4. Pour le notebook 07 uniquement : le fichier `.env`

Le notebook 07 se connecte aux bases PostgreSQL et MongoDB du TP « Sources de données », avec les **mêmes identifiants**. Copiez votre `.env` de ce TP à la racine de ce dépôt, ou créez-le à partir du modèle :

```bash
cp .env.example .env             # Windows : copy .env.example .env
```

Le `.env` n'est jamais versionné (il est dans `.gitignore`).

---

## Structure du dépôt

```
├── notebooks/        # les 9 notebooks du TP (00 à 08)
├── data/             # fichiers téléchargés par le notebook 00 (ignorés par Git)
├── output/           # fichiers produits par les notebooks (ignorés par Git)
├── docs/
│   └── plan_tp.md    # déroulé de la séance de 2 h + fiche enseignant
├── .env.example      # modèle du fichier .env (notebook 07)
├── pyproject.toml / uv.lock
└── requirements.txt
```

## Les données : sources et licences

- **NYC Taxi & Limousine Commission** : [TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page), données publiques de la ville de New York, réutilisables librement, sans garantie d'exactitude.
- **Boutique de Cotonou** : petits exemples fictifs repris des slides du cours.
- **PostgreSQL « gestion scolaire » et MongoDB « ride share »** : données synthétiques du TP « Sources de données », générées pour l'enseignement. On les traite comme des données personnelles : lecture seule, extractions limitées.
