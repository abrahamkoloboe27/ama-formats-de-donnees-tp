# Plan du TP « Formats de données » — séance de 2 h

Fiche de déroulé pour l'enseignant, que les étudiants peuvent aussi lire pour savoir où ils vont.
Le cours de 2 h a posé les notions : un format est un contrat, chaque format a sa fiche d'identité, et il n'y a pas de meilleur format, seulement un format adapté à un usage. Le TP les met en pratique sur **un même fichier volumineux** : les 3,95 millions de courses des taxis jaunes de New York en mars 2026.

## Objectifs : à la fin du TP, l'étudiant sait…

1. lire, filtrer, grouper, joindre et écrire un tableau avec **pandas** ;
2. lire et écrire un **CSV** sans casser les accents, les séparateurs ni les types ;
3. écrire un classeur **Excel** de plusieurs feuilles, et connaître ses limites ;
4. passer du **JSON** (classique, *Lines*, imbriqué) à un tableau, et inversement ;
5. exploiter **Parquet** : lire quelques colonnes, filtrer à la lecture, compresser, partitionner ;
6. **comparer** les formats (taille, vitesse, types conservés) et choisir le bon pour un usage ;
7. expliquer ce qu'apporte un format de table (**Delta Lake**, Iceberg) : transactions, suppression, historique.

---

## Avant la séance

### Côté enseignant

| ✔ | Action | Pourquoi |
|---|---|---|
| ☐ | Demander aux étudiants d'exécuter le **notebook 00 chez eux** | Il télécharge 68 Mo : trente téléchargements en même temps saturent le wifi de la salle |
| ☐ | Rappeler la configuration minimale : **8 Go de RAM** et **3 Go d'espace disque libre** | Le mois complet occupe environ 600 Mo en mémoire, et sa relecture en CSV demande davantage ; sur le disque, l'environnement Python pèse environ 0,8 Go et les fichiers produits environ 1,4 Go (dont le CSV du mois, 425 Mo) |
| ☐ | **Distribuer le `.env`** du TP « Sources de données » (mêmes identifiants), par un canal privé | Le notebook 07 se connecte à PostgreSQL et MongoDB ; le dépôt est public |
| ☐ | Vérifier que le réseau de la salle laisse passer les ports **6543** (PostgreSQL) et **27017** (MongoDB Atlas), et que l'IP de l'école est autorisée sur Atlas | Sinon, le notebook 07 échoue à la connexion |
| ☐ | Si la séance a lieu longtemps après octobre 2026 : relancer les notebooks | Les sorties enregistrées datent de leur dernière exécution |

### Côté étudiants (la veille)

1. Cloner le dépôt et installer les dépendances (`uv sync` ou `pip install -r requirements.txt`), voir le [README](../README.md).
2. Exécuter le **notebook 00** : toutes les bibliothèques doivent être **OK** et les deux fichiers présents dans `data/`.
3. Copier le `.env` reçu à la racine du dépôt (pour le notebook 07).

**Machine trop juste en mémoire (4 Go) ?** Dans les notebooks 01, 02 et 05, remplacez `courses = pd.read_parquet(...)` par `courses = pd.read_parquet(...).head(1_000_000)` : les chiffres changent, pas les conclusions. Le CSV du mois (notebook 02) n'a alors qu'un million de lignes : au notebook 06 (section 6), sa comparaison avec le Parquet complet n'est plus équitable.

---

## Déroulé de la séance

| Horaire | Bloc | Support | L'enseignant | Les étudiants |
|---|---|---|---|---|
| 0:00 – 0:05 | **Check-in** | notebook 00 | Fait le tour des check-lists | Montrent leur check-list OK |
| 0:05 – 0:25 | **A · Pandas 101** | notebook 01 | Démontre les sections 1 à 5 au vidéoprojecteur | Continuent seuls jusqu'à la réponse par *borough*, puis exercice 1 |
| 0:25 – 0:50 | **B · CSV** | notebook 02 | Lance la conversion Parquet → CSV et fait **parier** la salle sur la taille du fichier | Mesurent taille et vitesse, constatent la perte des types, font la partie « pièges » (séparateur, encodage) |
| 0:50 – 1:05 | **C · Excel ou JSON**, en binôme | notebook 03 ou 04 | Répartit la salle en deux moitiés | Une moitié fait Excel, l'autre JSON ; chaque binôme note **un chiffre** et **un piège** |
| 1:05 – 1:10 | Pause | | | |
| 1:10 – 1:30 | **D · Parquet** | notebook 05 | Commente les métadonnées (*row groups*, min/max) | Lecture de 2 colonnes, filtres, compressions, partitionnement |
| 1:30 – 1:45 | **E · Le grand comparatif** | notebook 06 | Refait le **pari** de la slide 3 avec les taxis, puis lance le notebook | Comparent leurs prédictions aux mesures ; exercice « Quel format pour quel usage ? » |
| 1:45 – 1:55 | **F · Atelier au choix** | notebook 07 ou 08 | Circule | **07** bases de données → fichiers · **08** Delta Lake (ajout, suppression, *time travel*) |
| 1:55 – 2:00 | **Restitution** | | Remplit la carte des formats au tableau (slide 24) | Chaque binôme donne un chiffre mesuré et un réflexe retenu |
| **Après** | Autonomie | notebooks 03/04, 07, 08 | | Le notebook C non choisi, l'atelier F non choisi, les exercices restants |

### Pourquoi ce découpage

- **Pandas d'abord** : les étudiants le découvrent ; sans les bases (lire, filtrer, grouper), les notebooks suivants deviennent du copier-coller.
- **CSV et Parquet en entier, en salle** : c'est le cœur du cours (le format universel et le format d'analyse), et les mesures sur 425 Mo marquent les esprits.
- **Excel et JSON en parallèle** : deux notebooks courts et indépendants. La restitution croisée (« combien pèse ton fichier ? ») fait le lien.
- **07 et 08 en atelier** : le 07 dépend du réseau et du `.env`, le 08 est l'ouverture vers le lakehouse. Les deux se terminent très bien en autonomie.

### Durées à prévoir sur un portable d'étudiant

Les sorties enregistrées viennent d'une machine rapide, où chaque notebook s'exécute en entier en 15 s au plus (00, 01, 04, 08), en moins d'une minute (03, 05) ou en 1 min 15 à 1 min 30 (06, 02). Comptez **2 à 3 fois plus** sur un portable. Les cellules longues sont signalées dans les notebooks : écriture du CSV complet (notebook 02), écriture Excel (notebooks 03 et 06), écriture des compressions (notebook 05).

### Si vous avez moins de temps

- Supprimez le bloc C en salle : Excel et JSON se font très bien seuls, à la maison.
- Faites le notebook 06 en **démonstration** au vidéoprojecteur (10 min) plutôt qu'en exécution par chacun.

## Évaluation (suggestion)

Le cours annonce un **TP noté** pendant la séance. Une solution simple : relever, pour chaque binôme, le notebook 06 exécuté et ses réponses à « Quel format pour quel usage ? » (six scénarios, une phrase de justification chacun, fiche d'identité à l'appui). La correction figure dans le notebook : exigez une justification qui cite un chiffre mesuré pendant le TP.
