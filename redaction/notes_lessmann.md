# Notes de lecture — Lessmann et al. (2015)

## Référence
Lessmann, S., Baesens, B., Seow, H.-V., & Thomas, L. C. (2015). 
Benchmarking state-of-the-art classification algorithms for credit scoring: 
An update of research. European Journal of Operational Research, 247(1), 124-136.

## Idées clés

- La littérature sur le scoring de crédit souffrait d'un manque de comparaisons 
  rigoureuses entre algorithmes (datasets différents, métriques différentes, 
  protocoles différents d'une étude à l'autre.
- Les auteurs proposent un benchmark unifié : mêmes datasets, mêmes métriques, 
  même protocole de validation, tests statistiques de significativité.
- L'étude actualise un benchmark antérieur (Baesens et al., 2003) en intégrant 
  les algorithmes apparus depuis : Random Forest, gradient boosting, SVM, 
  réseaux de neurones profonds, etc.

## Résultats principaux

- Nombre d'algorithmes testés : 41
- Nombre de datasets : 8 (provenant de différents pays et organismes de crédit)
- Modèles en tête : les méthodes ensemblistes à base d'arbres (Random Forest, 
  gradient boosting) arrivent systématiquement dans le peloton de tête
- Performance de la régression logistique : correcte mais significativement 
  inférieure aux meilleures méthodes ensemblistes
- Différence statistiquement significative : oui, confirmée par des tests 
  statistiques rigoureux (tests de Friedman et Nemenyi)
- Les gains de performance restent modestes en valeur absolue (quelques points 
  d'AUC) mais sont robustes et cohérents à travers les datasets
- Les modèles les plus complexes (réseaux de neurones profonds, SVM à noyaux) 
  ne surpassent pas systématiquement les méthodes ensemblistes plus simples

## Ce que j'en retiens pour mon mémoire

- Justification empirique de ma problématique : le ML fait mieux, mais le gain est modeste ce qui  rend la question "est-ce que ça vaut le coup 
  au regard de la perte d'interprétabilité ?" d'autant plus pertinente
- Mon choix de 3 modèles (logistique, RF, XGBoost) est cohérent avec les 
  conclusions de Lessmann 
- Le protocole de validation de Lessmann (validation croisée, tests statistiques) 
  est un modèle à suivre pour mon propre protocole, même en version simplifiée
- Les auteurs ne traitent PAS la question de l'interprétabilité. C'est 
  exactement le "trou" dans la littérature que mon mémoire contribue à combler
- L'article note que la régression logistique reste compétitive malgré sa 
  simplicité
  . Si je trouve un résultat similaire, c'est un argument en faveur 
  de son maintien dans un cadre prudentiel exigeant

## Citations potentielles (à paraphraser dans le mémoire)

- L'idée que les études antérieures manquaient de rigueur comparative 
  → intro du Chapitre II, section 1.1
- La conclusion sur la supériorité des ensembles d'arbres 
  → Chapitre II, section 1.1 pour poser le fait empirique
- L'observation que les gains sont modestes mais significatifs 
  → Chapitre III, section 3.3 pour contextualiser mes propres résultats
- Le silence de l'article sur l'interprétabilité 
  → transition entre Chapitre II section 1 et section 2