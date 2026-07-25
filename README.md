# Mémoire M2 MBFA - Scoring de crédit et machine learning

Étude comparative de modèles de scoring de crédit (régression logistique, 
Random Forest, XGBoost) sur le dataset Home Credit Default Risk, avec 
analyse d'interprétabilité par SHAP.

**Sujet** : Machine learning et scoring de crédit à la
consommation : Arbitrage entre performance et interprétabilité - Le
cas Home Credit

**Auteure** : Doâa
**Directrice** : Françoise Seyte
**Université de Montpellier**, Master 2 MBFA

## Structure du projet

- `data/` — données brutes (ignorées par Git, à télécharger depuis Kaggle)
- `notebooks/` — notebooks d'exploration et de modélisation
- `outputs/` — figures et modèles finaux
- `bibliographie/` — articles et documents de référence
- `redaction/` — brouillons de rédaction

## Environnement

Python 3.14, venv, dépendances dans `requirements.txt`.

## Reproductibilité

```
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
```