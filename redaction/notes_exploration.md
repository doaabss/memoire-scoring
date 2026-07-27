## Structure du dataset

- Nombre de lignes : 307 511
- Nombre de colonnes : 122
- Variables numériques : 106 (65 float64, 41 int64)
- Variables catégorielles : 16 (object)
- Mémoire occupée : ~286 Mo
- Variable cible : TARGET (int64), binaire {0, 1}

Cardinalités des variables catégorielles :
- 2 modalités : NAME_CONTRACT_TYPE, FLAG_OWN_CAR, FLAG_OWN_REALTY, EMERGENCYSTATE_MODE
- 3-10 modalités : CODE_GENDER (avec "XNA" anormal), NAME_EDUCATION_TYPE, etc.
- >20 modalités : ORGANIZATION_TYPE (~58), OCCUPATION_TYPE (~18)

Point d'attention : la modalité "XNA" pour CODE_GENDER est probablement 
un codage de valeur manquante ou aberrante.