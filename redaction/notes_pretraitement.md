# Notes de prétraitement — Dataset Home Credit Default Risk

Matière première pour la section 1.3 du Chapitre III.

---

## 1. Suppression des colonnes trop incomplètes

17 colonnes supprimées (seuil : >60 % de manquants).
Toutes liées aux caractéristiques immobilières (COMMONAREA, NONLIVINGAPARTMENTS, FLOORSMIN, YEARS_BUILD, LIVINGAPARTMENTS, FONDKAPREMONT, OWN_CAR_AGE), déclinées en 3 variantes (_AVG, _MODE, _MEDI).

Justification : forte incomplétude + caractère secondaire pour le scoring de crédit. Aucune de ces variables n'apparaissait dans le top 20 des corrélations avec TARGET.

Alternative écartée : imputer ces colonnes malgré >60 % de manquants. Risque d'introduire un bruit important dans les modèles — la médiane d'une variable à 70 % de manquants ne reflète pas grand-chose.

Dataset après suppression : 307 511 lignes × 105 colonnes.

---

## 2. Traitement des valeurs aberrantes

DAYS_EMPLOYED : 55 374 occurrences de la valeur 365243 (~1000 ans), soit 18 % du dataset. Code implicite pour "sans emploi". Remplacé par NaN, puis imputé par la médiane à l'étape suivante.

Alternative envisagée : créer une variable binaire FLAG_SANS_EMPLOI (1 si 365243, 0 sinon) puis remplacer par NaN. Non retenu pour cette version par souci de simplicité, mais mentionnable en perspective.

CODE_GENDER : 4 lignes avec la modalité "XNA". Supprimées. Impact négligeable (4 sur 307 511).

Dataset après nettoyage : 307 507 lignes × 105 colonnes.

---

## 3. Imputation des valeurs manquantes

Stratégie choisie :
- Variables numériques : imputation par la médiane (robuste aux outliers, contrairement à la moyenne)
- Variables catégorielles : imputation par le mode (valeur la plus fréquente)

Justification du choix de la médiane : le dataset contient des outliers extrêmes (AMT_INCOME_TOTAL max à 117 millions). La moyenne serait tirée vers le haut par ces valeurs extrêmes. La médiane est insensible à ce problème.

Alternatives écartées :
- Imputation par KNN (K-Nearest Neighbors) : plus sophistiquée mais beaucoup plus lente sur 307 000 lignes. Gain marginal non justifié vu les délais.
- Suppression des lignes avec manquants : écarté car trop de lignes seraient perdues (67 colonnes ont des manquants).

Valeurs manquantes après imputation : 0.

---

## 4. Features dérivées

Quatre variables créées :
- AGE = -DAYS_BIRTH / 365 → âge en années, plus lisible que des jours négatifs
- CREDIT_INCOME_RATIO = AMT_CREDIT / AMT_INCOME_TOTAL → poids du crédit par rapport au revenu, indicateur classique de risque
- ANNUITY_INCOME_RATIO = AMT_ANNUITY / AMT_INCOME_TOTAL → poids de la mensualité par rapport au revenu, indicateur de capacité de remboursement
- EMPLOYMENT_YEARS = -DAYS_EMPLOYED / 365 → ancienneté en années

Justification : ces ratios sont des indicateurs standards en analyse de crédit. Ils combinent des variables brutes en informations métier plus pertinentes. Le ratio dette/revenu est notamment un critère central dans les grilles de scoring bancaires.

---

## 5. Encodage des variables catégorielles

Méthode : one-hot encoding via pd.get_dummies(drop_first=True).

16 variables catégorielles encodées, générant 105 colonnes binaires supplémentaires.

drop_first=True supprime la première modalité de chaque variable pour éviter la multicolinéarité (problématique pour la régression logistique). L'information n'est pas perdue : si toutes les modalités sont à 0, le client appartient à la modalité supprimée.

Alternative écartée : label encoding (attribuer un chiffre à chaque modalité). Non retenu car il introduit un ordre artificiel entre les modalités (ex. "Married" = 1, "Single" = 2 implique que "Single" > "Married", ce qui n'a pas de sens).

Exception potentielle : ORGANIZATION_TYPE (~58 modalités) aurait pu être regroupé en catégories plus larges avant encodage. Non retenu par souci de simplicité, mais mentionnable comme limite.

---

## 6. Découpage train/test

Ratio : 70 % train / 30 % test.
Stratification : sur TARGET, pour préserver le taux de défaut de 8,07 % dans les deux sous-ensembles.
random_state=42 pour la reproductibilité.

Résultat :
- Train : 215 254 lignes (taux de défaut : 8,07 %)
- Test : 92 253 lignes (taux de défaut : 8,07 %)

Justification du 70/30 : ratio classique en scoring. Le 30 % de test donne un échantillon de ~92 000 clients, largement suffisant pour des métriques fiables.

---

## 7. Standardisation

Méthode : StandardScaler (moyenne = 0, écart-type = 1).
Appliqué sur 60 colonnes numériques. Les 152 colonnes binaires (issues du one-hot encoding et des FLAG) ne sont pas standardisées.

Fit sur le train uniquement, transform sur train et test. Pas de data leakage.

Justification : nécessaire pour la régression logistique (sensible aux échelles des variables). Sans effet sur Random Forest et XGBoost (insensibles aux échelles), mais appliqué par cohérence.

---

## Chiffres clés du prétraitement

- Dataset initial : 307 511 lignes × 122 colonnes
- Dataset final : 215 254 (train) + 92 253 (test) = 307 507 lignes × 212 colonnes
- Colonnes supprimées : 17
- Lignes supprimées : 4
- Features créées : 4
- Colonnes après encodage : 212
- Colonnes standardisées : 60
- Valeurs manquantes restantes : 0