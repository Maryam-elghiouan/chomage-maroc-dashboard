# Tableau de bord du chômage au Maroc (HCP)

Tableau de bord Power BI sur le chômage au Maroc, construit à partir des données publiques du Haut-Commissariat au Plan (HCP). Il analyse le taux de chômage selon le **diplôme, le milieu de résidence (urbain/rural), le sexe, la tranche d'âge, la province/préfecture et le secteur**, avec le taux national annuel (2006-2025) comme référence.

![Aperçu du tableau de bord]
### 1. Vue d'ensemble et macro-conjoncture
![Vue d'ensemble](screenshots/01-vue-ensemble.png)

### 2. Dynamique territoriale et géographique
![Dynamique territoriale](screenshots/02-dynamique-territoriale.png)

### 3. Analyse démographique et sociale
![Analyse démographique](screenshots/03-analyse-demographique.png)

### 4. Analyse sectorielle
![Analyse sectorielle](screenshots/04-analyse-sectorielle.png)

## Objectifs

- Suivre l'évolution trimestrielle du chômage selon le niveau de diplôme.
- Comparer les profils : diplôme, sexe, âge, milieu.
- Situer chaque province par rapport au taux national.
- Décrire la répartition des chômeurs par secteur d'activité.

## Contenu du tableau de bord

| Page | Contenu |
|---|---|
| 1. Vue d'ensemble et conjoncture macroéconomique | Taux national, province la plus touchée, chômage par niveau de diplôme, évolution trimestrielle par diplôme, répartition par secteur, Top 10 des provinces face au taux national |
| 2. Dynamique territoriale et géographique | Carte du Maroc par province, taux par province, Top 10 des provinces |
| 3. Analyse démographique et sociale (diplômes et âge) | Chômage par tranche d'âge, comparaison par âge et milieu avec le taux national, taux selon le sexe |
| 4. Analyse sectorielle du chômage | Répartition des chômeurs par secteur et milieu, structure globale par secteur |

Segments (filtres) : **Année**, **Milieu de résidence**, **Niveau de diplôme**.

## Sources de données

Toutes les données proviennent du HCP (hcp.ma). Les fichiers bruts sont conservés tels quels dans `data/raw/`.

| Table | Contenu | Fréquence | Période |
|---|---|---|---|
| `Chomage_Diplome_Milieu` | Diplôme × milieu | Trimestrielle | à compléter |
| `Chomage_Diplome_Sexe` | Diplôme × sexe | Trimestrielle | à compléter |
| `Chomage_Age_Milieu` | Tranche d'âge × milieu | Trimestrielle | à compléter |
| `Chomage_Province_Milieu` | Province/préfecture × milieu | Annuelle | 2017-2025 |
| `Chomage_Secteur_Milieu` | Répartition des chômeurs par secteur × milieu | à compléter | 2006-2025 |
| `Chomage_Maroc` | Taux national | Annuelle | 2006-2025 |

## Méthode

### 1. Nettoyage (Power Query)

Chaque fichier est importé séparément (les structures diffèrent), puis nettoyé dans Power Query pour que la transformation soit **reproductible** :

- suppression de la ligne de titre et promotion des en-têtes ;
- **dépivotage** des colonnes de périodes (trimestres ou années) en une colonne verticale ;
- remplacement des valeurs absentes (« __ ») par `null`, puis typage en nombre décimal ;
- conversion des trimestres (« T4-2025 ») et des années en dates de début de période ;
- renommage cohérent des colonnes et des requêtes.

### 2. Modèle de données

Schéma en étoile avec des tables de dimension :

- `DimQuarter` : table des trimestres (date, année, trimestre, libellé), triée chronologiquement ;
- `DimMilieu` : National, Rural, Urbain.

Les tables de faits sont reliées aux dimensions en relation un-à-plusieurs, avec filtre dans un seul sens. Les tables de faits ne sont pas reliées entre elles.

### 3. Mesures DAX

Une mesure par table (taux moyen par province, par âge, par diplôme), un taux national, et la province la plus touchée. Les titres des visuels sont fixes, pour ne pas changer avec les filtres.

## Principaux constats (année 2025, valeurs affichées par le tableau de bord)

> Vérifiez chaque chiffre avec le segment Année sur 2025 et le milieu **National** avant de publier.

- Le taux de chômage national est de **13,0 %**.
- Les diplômés du supérieur sont nettement plus touchés (environ 25 %) que les personnes sans diplôme (environ 5 %).
- Chez les **15-24 ans**, le taux atteint 37,3 % au niveau national, avec un écart entre le milieu urbain (48,0 %) et le milieu rural (22,9 %).
- Les provinces les plus touchées sont **Laâyoune (35,5 %)**, **Es-Semara (32,9 %)** et **Jerada (27,4 %)**.
- Parmi les chômeurs, environ 91 % viennent du secteur privé et environ 9 % du secteur public ou semi-public, avec une part du privé encore plus forte en milieu rural (96,4 %).

## Limites

- **Niveau province, pas région** : seul un tableau par province/préfecture était disponible. Aucun taux régional n'est calculé, car une moyenne simple des provinces ne tient pas compte de la population active.
- **Périodes différentes** selon les tables : à lire avec le segment Année de la page concernée.
- **Les taux ne sont pas additifs** : il ne faut pas additionner ni moyenner National, Rural et Urbain, ni plusieurs niveaux de diplôme. Le segment Milieu doit rester sur **une seule valeur** (National par défaut).
- **Données annuelles et trimestrielles** : une comparaison directe entre une moyenne trimestrielle et un chiffre annuel officiel reste approximative.

## Structure du dépôt

```
chomage-maroc-dashboard/
├── data/
│   ├── raw/          # fichiers HCP d'origine, non modifiés
│   └── clean/        # exports CSV optionnels
├── dashboard/        # fichier .pbix
├── screenshots/      # captures de chaque page
└── README.md
```

## Outils

Power BI Desktop, Power Query (M), DAX, Excel.

## Utilisation

1. Cloner le dépôt.
2. Ouvrir `tableau de bord-Analyse du Chômage au Maroc/*.pbix` dans Power BI Desktop.
3. Si les chemins des sources ont changé : **Transformer les données** → **Paramètres de la source de données**, puis pointer vers `data_chômage`.
4. **Actualiser**.


*Source des données : Haut-Commissariat au Plan (HCP), Maroc.*
