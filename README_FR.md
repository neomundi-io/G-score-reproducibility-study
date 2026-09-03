# NeoMundi — Mission : Reproductibilité et invariance du G-score

## Présentation

Ce dépôt contient les données, le notebook d'analyse, les visualisations et les rapports produits dans le cadre de la **Mission 2 NeoMundi**.

L'étude porte sur la **reproductibilité, la stabilité et l'invariance du G-score** à travers plusieurs modèles, prompts et campagnes de mesure (baromètres).

L'objectif n'est pas de démontrer l'invariance du score, mais de **tester cette hypothèse à partir des données** et d'identifier les conditions dans lesquelles le comportement du G-score reste stable ou, au contraire, varie.

---

## Question de recherche

> **Dans quelle mesure le G-score présente-t-il un comportement stable et reproductible lorsque les répétitions, les modèles, les prompts et les campagnes de mesure varient ?**

L'étude cherche notamment à :

- caractériser la distribution et la dispersion du G-score ;
- évaluer la reproductibilité des répétitions d'un même prompt ;
- mesurer la variabilité entre prompts et entre modèles ;
- comparer les campagnes de mesure ;
- caractériser la rupture observée au baromètre **B5** ;
- étudier l'évolution du score après B5, notamment en **B7 et B8** ;
- examiner les relations entre le G-score et certaines variables d'exécution ;
- tester la robustesse des résultats aux observations atypiques ;
- évaluer dans quelle mesure les résultats soutiennent ou remettent en question l'hypothèse d'invariance.

---

## Données

Le corpus théorique comprend :

| Dimension | Nombre |
|---|---:|
| Campagnes / baromètres | 7 |
| Modèles | 12 |
| Prompts | 4 |
| Répétitions par modèle × baromètre × prompt | 100 |
| Combinaisons modèle × baromètre | 84 |
| Observations théoriques | **33 600** |

Les campagnes analysées sont :

**B1, B2, B3, B4, B5, B7 et B8.**

Le baromètre B6 n'est pas disponible dans le corpus.

Chaque combinaison modèle × baromètre × prompt comporte théoriquement 100 répétitions, soit 400 observations par combinaison modèle × baromètre.

Le dataset consolidé comprend **19 variables**, couvrant notamment :

- l'identification du modèle, du baromètre et du prompt ;
- l'indice de répétition ;
- le G-score et plusieurs scores associés ;
- la décision (`ALLOW`, `FLAG`) ;
- la latence ;
- le nombre de tokens ;
- les erreurs techniques.

### Contrôles qualité

Plusieurs contrôles sont effectués avant les analyses :

- couverture des combinaisons modèle × baromètre ;
- 400 observations par combinaison modèle × baromètre ;
- 100 répétitions par prompt ;
- absence de doublons expérimentaux ;
- absence de doublons exacts ;
- contrôle des valeurs manquantes ;
- contrôle des bornes du G-score ;
- cohérence entre erreurs techniques et absence de G-score.

Sur les 33 600 observations théoriques, **199 lignes présentent une erreur technique** et ne disposent pas de G-score exploitable. Les analyses portant sur le G-score utilisent donc les observations valides.

---

## Sélection des fichiers de référence

Certaines combinaisons modèle × baromètre peuvent être associées à plusieurs fichiers de résultats.

Lorsqu'une telle situation se présente, le notebook sélectionne le fichier de référence selon une règle déterministe :

1. **minimum d'erreurs techniques** ;
2. en cas d'égalité, **maximum de lignes complètes**.

Cette sélection est enregistrée dans un **journal de sélection** afin de conserver la traçabilité de la construction du corpus.

Une analyse de sensibilité a également été réalisée sur les différentes tentatives disponibles pour B5. Le choix du fichier de référence ne suffit pas à expliquer l'ampleur de la rupture observée.

---

## Structure du dépôt

```text
.
├── data/
│   ├── raw/              # données brutes par baromètre
│   └── processed/        # données consolidées et fichiers de préparation
│
├── notebooks/
│   └── Mission_2.ipynb   # analyse complète
│
├── figures/              # visualisations générées par l'analyse
│
├── output/
│   ├── ..._FR.pdf        # rapport final en français
│   ├── ..._EN.pdf        # rapport final en anglais
│   └── annexe_Dunn_Holm_Cliff_delta.csv
│
└── report/               # sources LaTeX des rapports FR/EN
```
**Remarque :** le dossier `data/` n'est pas inclus dans ce dépôt. Pour reproduire l'analyse, placez les fichiers de données brutes dans `data/raw/` et le dataset consolidé dans `data/processed/`, en suivant la structure indiquée ci-dessus.

Les données brutes peuvent être conservées dans un espace séparé lorsque leur redistribution n'est pas autorisée. Dans ce cas, le code d'analyse et les fichiers nécessaires à la reconstruction du dataset doivent rester documentés.

---

## Notebook

Le notebook principal est :

```text
notebooks/Mission_2.ipynb
```

Il regroupe l'ensemble de la démarche, depuis la préparation du corpus jusqu'aux résultats statistiques.

### Principales étapes du notebook

1. Import des bibliothèques ;
2. extraction et préparation des campagnes ;
3. inventaire des fichiers de résultats ;
4. contrôle des combinaisons modèle × baromètre ;
5. identification des fichiers multiples ;
6. sélection des fichiers de référence ;
7. construction du dataset canonique ;
8. contrôles qualité ;
9. statistiques descriptives ;
10. analyse des répétitions ;
11. évaluation de la reproductibilité ;
12. comparaison entre modèles ;
13. analyse de la rupture en B5 ;
14. modèles à effets mixtes ;
15. analyse du suivi B7–B8 ;
16. analyse des variables d'exécution ;
17. détection des valeurs atypiques ;
18. analyse de sensibilité ;
19. synthèse des résultats.

Le notebook constitue la **trace détaillée de la démarche analytique**. Il contient également les visualisations et les résultats intermédiaires ayant servi à construire les rapports.

---

## Méthodes statistiques

Les méthodes ont été choisies en fonction de la structure répétée des données et des propriétés observées du G-score.

### Statistiques descriptives

- moyenne ;
- médiane ;
- écart-type ;
- quartiles ;
- valeurs extrêmes ;
- intervalles de confiance à 95 % par bootstrap.

### Reproductibilité

- coefficient de variation (**CV**) ;
- coefficient de corrélation intraclasse **ICC(1,1)** ;
- autocorrélation entre répétitions successives ;
- décomposition de la variabilité intra-prompt / inter-prompt.

### Comparaison entre modèles

- test de **Kruskal–Wallis** ;
- comparaisons post-hoc de **Dunn** ;
- correction de **Holm** ;
- **delta de Cliff** comme mesure de taille d'effet.

### Analyse de B5

La période B5 est comparée à la période de référence B1–B4 à l'aide de :

- test de **Mann–Whitney** ;
- delta de Cliff ;
- intervalle de confiance bootstrap ;
- modèle linéaire mixte ;
- interaction **modèle × période**.

### Analyse B7–B8

Les campagnes B7 et B8 sont étudiées comme période de suivi afin d'évaluer si la variation observée en B5 persiste ou si le score revient vers les niveaux de référence.

Un modèle mixte permet également d'évaluer si l'évolution après B5 diffère selon les modèles.

### Variables d'exécution

Les associations entre le G-score et les variables quantitatives disponibles sont étudiées à l'aide de la **corrélation de Spearman**, notamment pour :

- la latence ;
- le nombre de tokens.

Ces analyses décrivent des associations statistiques et ne permettent pas, à elles seules, d'établir une relation causale.

### Robustesse et valeurs atypiques

Les valeurs atypiques sont identifiées avec la règle de l'**IQR**.

Elles ne sont pas supprimées automatiquement. Leur influence est examinée dans une analyse de sensibilité afin de déterminer si les conclusions dépendent principalement de quelques observations extrêmes ou si elles reflètent plus largement la structure du corpus.

---

## Principaux résultats

### 1. Stabilité sur la période de référence

Sur **B1–B4**, le G-score présente un niveau central très stable :

- moyenne comprise entre **0,5973 et 0,5989** ;
- médiane égale à **0,600** pour chacune des quatre campagnes.

Aucune dérive temporelle systématique n'est mise en évidence au cours des répétitions.

La variabilité observée sur la période de référence est principalement intra-prompt : **98,38 %** de la variabilité totale est attribuée à la composante intra-prompt, contre **1,62 %** à la composante inter-prompt.

Cette stabilité du niveau moyen ne signifie toutefois pas une reproductibilité parfaite de chaque répétition : la dispersion varie selon les configurations.

### 2. Différences entre modèles

Des différences statistiquement détectables existent entre certains modèles sur la période de référence.

Cependant, les tailles d'effet associées aux comparaisons post-hoc significatives restent **négligeables**. La significativité statistique doit donc être distinguée de l'importance pratique des écarts observés.

### 3. Rupture en B5

Le principal changement apparaît au baromètre **B5** :

- moyenne de référence B1–B4 : **0,5982** ;
- moyenne B5 : **0,5266** ;
- différence : **−0,0716**.

La baisse est observée pour l'ensemble des modèles, mais son amplitude varie selon eux.

Le modèle à effets mixtes met en évidence une interaction modèle × période très significative :

```text
χ² = 361,729
ddl = 11
p = 7,973 × 10⁻⁷¹
```

Cela indique que l'amplitude de la variation entre la période de référence et B5 n'est pas homogène entre les modèles.

### 4. Retour vers les niveaux de référence

Après B5, le score revient vers des niveaux proches de ceux observés pendant la période de référence :

- B7 : **0,5984** ;
- B8 : **0,6000**.

La récupération n'est toutefois pas identique pour tous les modèles. L'analyse de l'interaction modèle × période sur la trajectoire référence → B7 → B8 donne :

```text
χ² = 821,187
ddl = 22
p = 1,848 × 10⁻¹⁵⁹
```

Les résultats sont donc compatibles avec une rupture principalement **transitoire**, suivie d'un retour vers le niveau de référence, avec une amplitude de récupération qui varie selon les modèles.

### 5. Variables d'exécution

La corrélation globale entre le G-score et la latence est positive :

```text
ρ = 0,5662
```

mais elle disparaît lorsque les périodes sont considérées séparément :

```text
B1–B4 : ρ = −0,0011
B5    : ρ = −0,0169
```

La relation avec le nombre de tokens est également très faible :

```text
Ensemble : ρ = −0,0302
B1–B4   : ρ = −0,0362
B5      : ρ = −0,0226
```

Ces résultats ne fournissent donc pas d'élément en faveur d'une explication simple de la rupture de B5 fondée uniquement sur la latence ou le nombre de tokens.

### 6. Valeurs atypiques

La règle de l'IQR identifie **4 965 observations atypiques sur 33 401 observations valides**, dont **4 653 en B5**.

Cette forte concentration montre que la règle identifie principalement les observations appartenant à la distribution particulière de B5, plutôt que quelques anomalies isolées.

Les observations sont donc conservées dans l'analyse principale et leur influence est examinée séparément.

---

## Conclusion

Les résultats ne soutiennent pas une **invariance stricte** du G-score dans toutes les conditions étudiées.

Ils mettent plutôt en évidence une **stabilité conditionnelle** :

- le niveau du G-score est stable sur les campagnes de référence B1–B4 ;
- la reproductibilité varie selon les configurations ;
- une rupture nette apparaît en B5 ;
- le score revient ensuite vers des niveaux proches de la référence en B7 et B8 ;
- l'amplitude de la rupture et de la récupération varie selon les modèles.

Les données disponibles ne permettent pas d'identifier avec certitude la cause de la rupture observée en B5. Les analyses des variables d'exécution disponibles ne mettent pas en évidence d'explication simple reposant uniquement sur la latence, le nombre de tokens ou les erreurs techniques.

Dans le cadre de cette étude, le G-score est donc interprété comme un **signal de stabilité/comportement du dispositif étudié**, et non comme une mesure directe de factualité ou de qualité des réponses.

---

## Reproduire l'analyse

### Environnement

L'analyse a été réalisée en Python avec notamment :

```text
numpy
pandas
matplotlib
seaborn
scipy
scikit-posthocs
pingouin
statsmodels
jupyter
```

Installation :

```bash
pip install numpy pandas matplotlib seaborn scipy scikit-posthocs pingouin statsmodels jupyter
```

### Données d'entrée

Le notebook utilise les archives de résultats des différentes campagnes.

Dans la version actuelle du notebook, les archives sont recherchées depuis le répertoire de travail :

```text
5 Baromètres pour Fatima.zip
results_baromètre_7.zip
results_baromètre_8.zip
```

Le notebook extrait ensuite les données, inventorie les fichiers `*_results.csv`, sélectionne une version de référence pour chaque combinaison modèle × baromètre et construit le dataset consolidé.

### Exécution

Depuis la racine du dépôt :

```bash
jupyter notebook
```

Puis ouvrir :

```text
notebooks/Mission_2.ipynb
```

Les cellules doivent être exécutées dans l'ordre.

### Résultats générés

L'exécution du notebook produit notamment :

- le dataset canonique ;
- le journal de sélection des fichiers ;
- les statistiques descriptives ;
- les résultats des tests statistiques ;
- les analyses de reproductibilité ;
- les résultats des modèles mixtes ;
- les analyses de corrélation ;
- les analyses de sensibilité ;
- les visualisations utilisées dans les rapports.

---

## Rapports

Deux versions du rapport sont associées au dépôt :

- **version française** : rapport complet de l'étude ;
- **version anglaise** : version anglaise destinée à faciliter la diffusion et la lecture internationale.

Les sources LaTeX correspondantes sont conservées dans :

```text
report/
```

Les PDF finaux sont conservés dans :

```text
output/
```

---

## Traçabilité

Le dépôt est organisé afin de conserver les différentes étapes de l'analyse :

**données brutes → données préparées → notebook → figures / résultats → rapports**

Le notebook constitue la trace principale de la démarche analytique. Les fichiers intermédiaires permettent de suivre la construction du corpus et les rapports présentent une synthèse structurée des résultats.

### Accès aux données brutes

Afin de favoriser la transparence et de permettre à des chercheurs ou scientifiques d'approfondir, vérifier ou reproduire les analyses, les données brutes peuvent être mises à disposition **sur demande auprès de NeoMundi**.

L'accès aux données est soumis aux conditions définies par NeoMundi et peut, lorsque cela est nécessaire, être conditionné à la **signature d'un accord de confidentialité (NDA)**.

Lorsque les données brutes ne peuvent pas être diffusées publiquement, elles restent dans l'environnement autorisé. Le dépôt conserve alors autant que possible le code, la documentation du protocole et les fichiers dérivés nécessaires à la reproductibilité.

---

## Limites

Les principales limites de l'étude sont :

- quatre prompts seulement par combinaison modèle × baromètre ;
- observations répétées et donc non totalement indépendantes ;
- sensibilité de certains indicateurs, notamment l'ICC, au faible nombre de prompts ;
- règle de l'IQR peu discriminante lorsque les quartiles sont concentrés à 0,600 ;
- certaines analyses à caractère exploratoire ;
- impossibilité d'établir une causalité concernant la rupture de B5 à partir des variables disponibles.

Une étude ultérieure pourrait notamment augmenter le nombre de prompts, de modèles, de répétitions et de campagnes, et documenter plus finement les conditions expérimentales susceptibles d'être associées aux changements de régime.

---

## Références méthodologiques

Les méthodes statistiques sont détaillées dans le rapport associé. Les références utilisées pour les analyses de taille d'effet et de reproductibilité sont également indiquées dans la bibliographie du rapport.

---

**Auteur :** Fatima Ezzahrae GOUARAB  
**NeoMundi Recherche — 2026**
