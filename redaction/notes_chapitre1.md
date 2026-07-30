# Notes de lecture — Chapitre I (cadre réglementaire)

---

## RGPD - Article 22

**Source** : Règlement (UE) 2016/679, article 22 + page explicative CNIL

**Principe général** : toute personne a le droit de ne pas faire l'objet d'une décision fondée exclusivement sur un traitement automatisé produisant des effets juridiques ou l'affectant de manière significative. Un refus de crédit basé uniquement sur un score automatique entre dans ce cadre.

**Exceptions** : la décision automatisée est autorisée si elle est nécessaire à la conclusion d'un contrat, autorisée par le droit de l'Union ou d'un État membre, ou fondée sur le consentement explicite de la personne.

**Droits de la personne concernée** :
- Obtenir une intervention humaine de la part du responsable du traitement
- Exprimer son point de vue
- Contester la décision
- Obtenir une explication sur la logique sous-jacente à la décision

**Application au scoring de crédit** : un client dont la demande de crédit est refusée par un modèle automatisé peut exiger une explication. Cela impose que le modèle utilisé soit suffisamment interprétable pour produire une justification compréhensible. Un modèle de type XGBoost sans outil d'interprétabilité ne permet pas de satisfaire cette exigence, d'où l'intérêt de SHAP.

**Nuance importante** : l'article 22 n'interdit pas l'usage du ML, il impose des garde-fous. La question n'est pas "peut-on utiliser le ML ?" mais "peut-on expliquer ses décisions ?".

**Pour mon mémoire** : cet article justifie directement la pertinence de ma problématique. L'arbitrage performance/interprétabilité n'est pas un débat purement académique, c'est une obligation juridique.

---

## AI Act - Systèmes à haut risque

**Source** : Règlement européen sur l'intelligence artificielle (AI Act), adopté en 2024, entrée en application progressive 2025-2027

**Classification par niveau de risque** :
- Risque inacceptable : systèmes interdits (notation sociale, manipulation subliminale, etc.)
- Haut risque : systèmes soumis à des obligations strictes avant mise sur le marché
- Risque limité : obligations de transparence (chatbots, deepfakes)
- Risque minimal : libre utilisation

**Le scoring de crédit est classé "haut risque"** : l'Annexe III, point 5(b) du règlement classe explicitement les systèmes d'IA utilisés pour évaluer la solvabilité des personnes physiques parmi les systèmes à haut risque. C'est le cas des modèles de scoring.

**Obligations pour les systèmes à haut risque** :
- Système de gestion des risques documenté et mis à jour
- Gouvernance des données : les données d'entraînement doivent être pertinentes, représentatives et exemptes d'erreurs dans la mesure du possible
- Documentation technique détaillée du modèle
- Transparence : le fournisseur doit fournir des informations suffisantes pour que l'utilisateur puisse interpréter les résultats du système
- Supervision humaine : le système doit pouvoir être supervisé efficacement par des personnes physiques
- Exactitude, robustesse et cybersécurité

**Calendrier d'entrée en vigueur** : les obligations pour les systèmes à haut risque s'appliquent progressivement à partir de 2026-2027. Les banques et organismes de crédit doivent anticiper.

**Pour mon mémoire** : l'AI Act renforce et formalise les exigences d'explicabilité déjà posées par le RGPD. Il transforme la question de l'interprétabilité d'un enjeu de bonnes pratiques en une obligation réglementaire contraignante. C'est un argument clé pour justifier que l'adoption du ML en scoring ne peut pas se faire sans outils d'interprétabilité type SHAP.

---

## EBA - Discussion paper on machine learning for IRB models (2021)

**Source** : European Banking Authority, "Discussion paper on machine learning for IRB models", novembre 2021

**Contexte** : l'EBA reconnaît que les banques explorent l'usage du ML pour leurs modèles de notation interne (approche IRB sous Bâle III). Ce document pose les principes que les banques doivent respecter.

**Position sur les modèles "boîte noire"** :
- L'EBA ne les interdit pas formellement
- Mais elle rappelle que les exigences réglementaires existantes (CRR, orientations EBA sur les modèles PD/LGD) imposent que les banques comprennent et puissent expliquer les modèles qu'elles utilisent
- Un modèle que la banque ne peut pas expliquer à son superviseur est de fait non-conforme, même s'il est performant

**Exigences d'interprétabilité selon l'EBA** :
- Interprétabilité globale : la banque doit comprendre quelles variables portent le modèle et dans quel sens
- Interprétabilité locale : la banque doit pouvoir expliquer une décision individuelle
- Cohérence métier : les relations apprises par le modèle doivent être cohérentes avec la connaissance métier (un revenu élevé doit réduire le risque prédit, pas l'augmenter)
- Stabilité : le modèle doit être stable dans le temps, avec des mécanismes de monitoring de la dérive

**Position sur le model risk management** :
- L'usage du ML augmente le risque de modèle (model risk) par rapport aux modèles traditionnels
- Les banques doivent mettre en place une gouvernance renforcée : validation indépendante, documentation complète, backtesting régulier
- Le comité de validation doit être capable de challenger le modèle — ce qui suppose qu'il le comprenne

**Points spécifiques mentionnés** :
- Le risque de surapprentissage (overfitting) est identifié comme un risque majeur du ML en scoring
- L'EBA met en garde contre l'utilisation de variables proxy pour des caractéristiques protégées (genre, origine) — lien direct avec le RGPD et les enjeux de biais
- Les méthodes d'interprétabilité post-hoc (SHAP, LIME) sont mentionnées comme des pistes mais l'EBA ne se prononce pas sur leur suffisance

**Pour mon mémoire** : ce document est central pour ma discussion en section 3.3 du Chapitre III. L'EBA pose exactement le cadre de mon arbitrage : le ML est autorisé à condition de pouvoir l'expliquer et le gouverner. La question reste ouverte de savoir si SHAP suffit à remplir ces conditions — c'est précisément ce que mon étude empirique contribue à éclairer.

---

## ACPR — Positions sur l'IA en finance

**Source** : ACPR/Banque de France, documents de réflexion sur la gouvernance de l'IA dans le secteur financier (2020 et suivants)

**Rôle de l'ACPR** : autorité de supervision française des banques et assurances, elle transpose et applique les cadres européens (EBA, BCE) au contexte français.

**Positions principales** :
- L'ACPR reconnaît le potentiel de l'IA pour améliorer la gestion des risques
- Elle insiste sur le principe de proportionnalité : plus le modèle est complexe, plus la gouvernance doit être renforcée
- Elle souligne le risque de "course à la performance" au détriment de la compréhension des modèles
- Elle rappelle que la responsabilité de la décision reste humaine, même quand le modèle est automatisé

**Exigences spécifiques** :
- Traçabilité des décisions algorithmiques
- Auditabilité des modèles par les superviseurs
- Prévention des biais discriminatoires
- Information du client en cas de décision défavorable

**Pour mon mémoire** : l'ACPR ancre mon sujet dans le contexte français. Même si mon dataset est international, le cadre réglementaire que je discute s'applique aux banques françaises. Les positions ACPR me permettent de montrer que la tension performance/interprétabilité n'est pas théorique — elle est au cœur des préoccupations du superviseur national.

---

## Synthèse — Ce que ces lectures m'apportent pour le Chapitre I

Le cadre réglementaire converge sur trois exigences :
1. Le client a droit à une explication (RGPD art. 22)
2. Le scoring de crédit est un système à haut risque soumis à des obligations strictes (AI Act)
3. La banque doit comprendre, expliquer et gouverner ses modèles (EBA/ACPR)

Ces trois exigences ne rendent pas le ML impossible en scoring, mais elles imposent des conditions que les modèles "boîte noire" ne remplissent pas nativement. C'est exactement l'espace dans lequel s'inscrit mon mémoire : SHAP peut-il combler cet écart ?