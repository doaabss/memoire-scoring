# Notes d'exploration — Dataset Home Credit Default Risk

Ce fichier centralise les observations issues du Bloc 1 d'exploration.
Il servira de matière première pour la section 1.2 du Chapitre III.

---

## 1. Structure du dataset

- Fichier principal : `application_train.csv`
- Nombre de lignes : 307 511 demandes de crédit
- Nombre de colonnes : 122 variables
- Variable cible : `TARGET` (int64, binaire {0, 1})
- Variables numériques : 106 (65 float64, 41 int64)
- Variables catégorielles : 16
- Mémoire occupée en RAM : ~286 Mo

### Cardinalité des variables catégorielles

- Faible (2-5 modalités) : NAME_CONTRACT_TYPE (2), FLAG_OWN_CAR (2), 
  FLAG_OWN_REALTY (2), CODE_GENDER (3, dont "XNA" anormal)
- Moyenne (6-20 modalités) : NAME_EDUCATION_TYPE, NAME_FAMILY_STATUS, 
  NAME_INCOME_TYPE, NAME_HOUSING_TYPE, OCCUPATION_TYPE (~18)
- Élevée (>20 modalités) : ORGANIZATION_TYPE (~58)

---

## 2. Variable cible et déséquilibre des classes

- Taux de défaut global : **8,07 %**
- Nombre de défauts : 24 825 clients
- Nombre de non-défauts : 282 686 clients
- Ratio approximatif : 92/8

### Implications méthodologiques

- L'accuracy est inutilisable comme métrique (un modèle naïf "tout le monde 
  rembourse" aurait 92 % d'accuracy)
- Métriques adaptées obligatoires : AUC-ROC, Gini, KS
- Découpage train/test stratifié pour préserver le ratio 92/8
- Stratégie de rééquilibrage à discuter : class_weight='balanced', SMOTE, 
  ou undersampling

### Comparaison avec l'industrie

- Banque de détail française (crédit conso) : 1-3 %
- Home Credit (dataset) : ~8 %
- Organismes subprime US (2007-2008) : 15-25 %
- Le taux élevé est cohérent avec le positionnement de Home Credit 
  (populations peu bancarisées, Europe de l'Est et Asie)

---

## 3. Valeurs manquantes

- **67 colonnes sur 122** présentent des valeurs manquantes
- **55 colonnes** sont complètes
- **17 colonnes ont plus de 60 % de manquants** — toutes liées aux 
  caractéristiques du logement/immeuble :
  - COMMONAREA (_AVG, _MODE, _MEDI) : 69,9 %
  - NONLIVINGAPARTMENTS (_AVG, _MODE, _MEDI) : 69,4 %
  - FONDKAPREMONT_MODE : 68,4 %
  - LIVINGAPARTMENTS (_AVG, _MODE, _MEDI) : 68,4 %
  - FLOORSMIN (_AVG, _MODE, _MEDI) : 67,8 %
  - YEARS_BUILD (_AVG, _MODE, _MEDI) : 66,5 %
  - OWN_CAR_AGE : 66,0 % (cas particulier : non-applicable pour les 
    clients sans voiture)
- Les variables LANDAREA_* sont à 59,4 %, juste sous le seuil de 60 %

### Stratégie prévue au Bloc 2

- Suppression des colonnes avec >60 % de manquants (17 colonnes)
- Imputation par la médiane pour les numériques restantes
- Imputation par le mode pour les catégorielles restantes
- OWN_CAR_AGE : création d'une variable binaire "possède une voiture"

---

## 4. Valeurs aberrantes

### DAYS_EMPLOYED

- La valeur `365243` (~1000 ans) apparaît **55 374 fois** (18 % du dataset)
- Code caché pour "sans emploi" ou "non-applicable"
- Toutes les valeurs normales sont négatives (convention Home Credit : 
  jours avant la demande)
- Traitement prévu : remplacer par NaN puis imputer, ou créer une 
  variable binaire "sans emploi"

### CODE_GENDER

- 4 occurrences de la modalité "XNA" (code pour "inconnu")
- Traitement prévu : supprimer ces 4 lignes (impact négligeable 
  sur 307 511 observations)

### AMT_INCOME_TOTAL

- Max à 117 000 000 alors que la médiane est à 147 150
- Outlier extrême (800× la médiane)
- Traitement prévu : winsorization ou transformation logarithmique

---

## 5. Variables numériques clés

| Variable | Min | Médiane | Max | Observation |
|---|---|---|---|---|
| AMT_INCOME_TOTAL | 25 650 | 147 150 | 117 000 000 | Outliers extrêmes |
| AMT_CREDIT | 45 000 | 513 531 | 4 050 000 | Distribution étalée |
| AMT_ANNUITY | 1 616 | 24 903 | 258 026 | 12 valeurs manquantes |
| AMT_GOODS_PRICE | 40 500 | 450 000 | 4 050 000 | 278 manquants |
| AGE (dérivée) | 20,5 ans | 43,2 ans | 69,1 ans | Aucune anomalie |
| CNT_CHILDREN | 0 | 0 | 19 | 75 % ont 0 ou 1 enfant |

---

## 6. Corrélations avec TARGET

### Variables les plus prédictives (en valeur absolue)

| Variable | Corrélation | Sens |
|---|---|---|
| EXT_SOURCE_3 | -0,179 | Protecteur (score externe anonymisé) |
| EXT_SOURCE_2 | -0,161 | Protecteur (score externe anonymisé) |
| EXT_SOURCE_1 | -0,155 | Protecteur (score externe anonymisé) |
| DAYS_BIRTH | +0,078 | Risque (clients plus jeunes = plus de défaut) |
| REGION_RATING_CLIENT_W_CITY | +0,061 | Risque (notation région) |
| DAYS_LAST_PHONE_CHANGE | +0,055 | Risque |
| DAYS_ID_PUBLISH | +0,052 | Risque |
| DAYS_EMPLOYED | -0,045 | Protecteur (ancienneté professionnelle) |

### Observations clés

- Les corrélations individuelles restent faibles (max ~0,18)
- Les EXT_SOURCE dominent largement mais leur nature est anonymisée 
  (limite du dataset à mentionner)
- Aucune variable seule ne suffit à prédire le défaut → justifie 
  l'approche multivariée (RF, XGBoost)

---

## 7. Variables catégorielles et taux de défaut

| Variable | Modalité à risque | Taux | Modalité protectrice | Taux |
|---|---|---|---|---|
| CODE_GENDER | M | 10,14 % | F | 7,00 % |
| NAME_EDUCATION_TYPE | Lower secondary | 10,93 % | Academic degree | 1,83 % |
| NAME_FAMILY_STATUS | Civil marriage | 9,94 % | Widow | 5,82 % |
| NAME_INCOME_TYPE | Working | 9,59 % | Pensioner | 5,39 % |
| NAME_CONTRACT_TYPE | Cash loans | 8,35 % | Revolving loans | 5,48 % |

### Modalités à faible effectif (à regrouper au Bloc 2)

- Maternity leave : 40 % de défaut mais 5 clients seulement
- Unemployed : 36 % de défaut mais 22 clients
- Businessman : 0 % mais 10 clients
- Student : 0 % mais 18 clients

### Point éthique

- Le genre (CODE_GENDER) est prédictif du défaut (+3 points entre M et F)
- Le niveau d'éducation est fortement discriminant
- Ces variables devront être discutées dans le Chapitre III au regard 
  des enjeux de biais algorithmiques et de conformité RGPD

---

## 8. Figures produites

1. `01_distribution_target.png` — Distribution de la variable TARGET
2. `02_valeurs_manquantes_top20.png` — Top 20 des colonnes incomplètes
3. `03_days_employed_aberrant.png` — DAYS_EMPLOYED avant/après nettoyage
4. `04_distributions_variables_cles.png` — Âge, revenu, crédit, enfants
5. `05_top_correlations_target.png` — Top 20 corrélations avec TARGET
6. `05b_matrice_correlation.png` — Matrice de corrélation variables clés
7. `06_taux_defaut_categoriel.png` — Taux de défaut par genre, éducation, statut

---

## 9. Enjeux identifiés pour le Bloc 2 (Prétraitement)

1. Déséquilibre des classes (92/8)
2. 17 colonnes à supprimer (>60 % manquants)
3. Valeurs aberrantes à nettoyer (DAYS_EMPLOYED, CODE_GENDER)
4. Modalités catégorielles rares à regrouper
5. Outliers extrêmes sur les revenus
6. Cardinalité élevée d'ORGANIZATION_TYPE (~58 modalités)
7. Variables sensibles (genre, âge) à discuter éthiquement