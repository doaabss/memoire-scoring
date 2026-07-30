# Notes d'exploration - Dataset Home Credit Default Risk

---

## Structure du dataset

Fichier principal : application_train.csv
307 511 demandes de crédit, 122 variables.
Variable cible : TARGET (0 = remboursé, 1 = défaut).
106 variables numériques (65 float64, 41 int64), 16 variables catégorielles.
Mémoire en RAM : ~286 Mo.

Les catégorielles vont de 2 modalités (NAME_CONTRACT_TYPE) à ~58 (ORGANIZATION_TYPE).
Anomalie : CODE_GENDER contient une modalité "XNA" (4 occurrences).

---

## Variable cible

Taux de défaut : 8,07 % (24 825 défauts sur 307 511 clients).
Fort déséquilibre 92/8.
Ce taux est élevé par rapport à la banque de détail française (1-3 %), cohérent avec le positionnement de Home Credit (populations peu bancarisées).

Conséquences : accuracy inutilisable, métriques adaptées obligatoires (AUC, Gini, KS), découpage stratifié, rééquilibrage à discuter (class_weight ou SMOTE).

---

## Valeurs manquantes

67 colonnes sur 122 ont des manquants. 55 sont complètes.
17 colonnes dépassent 60 % de manquants — toutes liées aux caractéristiques du logement (COMMONAREA, NONLIVINGAPARTMENTS, FLOORSMIN, YEARS_BUILD, etc.), déclinées en 3 variantes (_AVG, _MODE, _MEDI).
OWN_CAR_AGE (66 %) est un cas particulier : non-applicable pour les clients sans voiture.
LANDAREA_* à 59,4 %, juste sous le seuil de 60 %.

Stratégie prévue : suppression des colonnes >60 %, imputation médiane pour les numériques, mode pour les catégorielles.

---

## Valeurs aberrantes

DAYS_EMPLOYED : la valeur 365243 (~1000 ans) apparaît 55 374 fois (18 % du dataset). C'est un code pour "sans emploi". Toutes les valeurs normales sont négatives. À remplacer par NaN au Bloc 2.

CODE_GENDER : 4 clients avec la modalité "XNA". À supprimer (impact nul).

AMT_INCOME_TOTAL : max à 117 millions, médiane à 147 150. Outlier extrême à traiter par winsorization ou transformation log.

---

## Variables numériques clés

Âge des clients (dérivé de DAYS_BIRTH) : entre 20,5 et 69 ans, médiane à 43 ans. Aucune anomalie.
Revenus : médiane à 147 150, outliers extrêmes au-dessus.
Montant du crédit : médiane à 513 531, max à 4 050 000.
Nombre d'enfants : 75 % ont 0 ou 1 enfant, max à 19.

---

## Corrélations avec TARGET

Les corrélations individuelles restent faibles (max ~0,18 en valeur absolue). Aucune variable seule ne prédit bien le défaut.

Les plus prédictives :
- EXT_SOURCE_3 (-0,179), EXT_SOURCE_2 (-0,161), EXT_SOURCE_1 (-0,155) : scores externes anonymisés, de loin les plus importants. Leur nature exacte n'est pas documentée.
- DAYS_BIRTH (+0,078) : les clients plus jeunes font davantage défaut.
- DAYS_EMPLOYED (-0,045) : l'ancienneté professionnelle protège du défaut.

---

## Variables catégorielles et taux de défaut

CODE_GENDER : hommes 10,14 %, femmes 7,00 %. Écart de 3 points, variable sensible.
NAME_EDUCATION_TYPE : relation quasi-monotone — de 10,93 % (secondaire inférieur) à 1,83 % (diplôme académique).
NAME_FAMILY_STATUS : célibataires et unions civiles ~10 %, mariés 7,56 %, veufs 5,82 %.
NAME_INCOME_TYPE : "Maternity leave" à 40 % et "Unemployed" à 36 % mais effectifs minuscules (5 et 22 clients). À regrouper au Bloc 2.

Point éthique : le genre et le niveau d'éducation sont prédictifs. À discuter dans le Chapitre III (biais algorithmiques, RGPD).

---

## Figures produites

01_distribution_target.png
02_valeurs_manquantes_top20.png
03_days_employed_aberrant.png
04_distributions_variables_cles.png
05_top_correlations_target.png
05b_matrice_correlation.png
06_taux_defaut_categoriel.png

---

## Enjeux pour le prétraitement

1. Déséquilibre 92/8
2. 17 colonnes à supprimer (>60 % manquants)
3. Valeurs aberrantes (DAYS_EMPLOYED, CODE_GENDER)
4. Modalités rares à regrouper
5. Outliers revenus
6. ORGANIZATION_TYPE (~58 modalités) à encoder
7. Variables sensibles (genre, âge) à discuter éthiquement