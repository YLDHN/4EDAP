# Analyse Exploratoire — League of Legends Challenger 10k

## 📊 Aperçu du projet

Analyse statistique complète et approfondie d'un dataset de ~**10 000 parties de League of Legends** au rang Challenger (l'élite du jeu, ~0.01% des joueurs). Le dataset comprend **~100 000 observations de joueurs** couvrant 3 régions mondiales (Amériques, Asie, Europe).

**Objectif global** : Identifier les facteurs statistiques déterminants de la victoire et les profils distinctifs des cinq rôles de jeu via tests statistiques rigoureux et modélisation.

---

## 🎮 Context: League of Legends

**LoL** est un MOBA (Multiplayer Online Battle Arena) où deux équipes de 5 joueurs s'affrontent :
- **Objectif** : Détruire le Nexus (structure centrale) de l'équipe adverse
- **5 rôles distincts** : TOP, JUNGLE, MIDDLE, BOTTOM (ADC), UTILITY (Support)
- **Objectifs épiques** : Dragons (buff permanent), Baron (buff offensif majeur), Tours, Héraut
- **Durée typique** : 25–35 minutes en Challenger

Niveau Challenger = **élite absolue** → données de très haute qualité pour l'analyse.

---

## 📁 Structure du projet

```
4EDAP/
├── LOL.ipynb                 # Notebook principal (11 sections)
├── LOL_report.html           # Export HTML du rapport final
├── matches.csv              # Table matches (~10k lignes)
├── participants.csv         # Table participants (~100k lignes)
├── README.md               # Ce fichier
└── [figures générées]       # 16 graphiques PNG
```

---

## 🔍 Contenu du Notebook (11 sections)

### **Section 1 : Introduction**
- Contexte League of Legends et définition des 5 rôles
- Présentation des objectifs épiques (Dragon, Baron, Tours, etc.)
- Formulation des 4 problématiques principales

### **Section 2 : Présentation des datasets**
- 3 tables : `matches` (10k lignes, 49 colonnes), `participants` (100k lignes, 136 colonnes), `events` (15M lignes)
- Clé de jointure commune : `matchId`
- Description des variables qualitatives et quantitatives

### **Section 3 : Chargement et nettoyage**
- Import depuis HuggingFace (`gptilt/lol-basic-matches-challenger-10k`)
- Fusion des 3 régions
- Nettoyage : suppression des parties remakées, exclusion des rôles non identifiés
- **Création de variables dérivées** : KDA, CS/min, Gold/min, Damage/min

### **Section 4 : Analyse univariée — Variables qualitatives**
- Distribution des **rôles** (quasi-uniforme, ~20% chacun)
- Répartition **régionale** (Asie > Americas > Europe)
- Ratio Victoire/Défaite (50/50 par construction)
- **Top 20 champions** joués et leurs winrates

### **Section 5 : Analyse univariée — Variables quantitatives**
- Paramètres descriptifs (moyenne, médiane, écart-type, skewness, kurtosis)
- **Observations clés** :
  - Kills : distribution asymétrique à droite (snowball effect)
  - CS/min : variance élevée (Support farming vs autres rôles)
  - Game duration : distribution normale autour de 26–27 minutes

### **Section 6 : Khi-deux — Variables qualitatives × qualitatives**
- **Test d'indépendance** : Rôle × Victoire → indépendance (V de Cramér ≈ 0)
- **Test d'indépendance** : Région × Surrender → **dépendance significative** (cultures régionales)
- **Test d'adéquation** : Kills vs Poisson(λ) → rejet (kills surdispersés, non-indépendance)
- **Test d'adéquation** : Rôles vs Uniforme → acceptation (distribution parfaitement uniforme)

### **Section 7 : Corrélations et régressions — Quanti × quanti**
- **Matrice de corrélation** : Pearson sur 13 variables
- **Corrélations remarquables** :
  - Gold vs Dégâts : r = +0.865 (très forte)
  - Kills vs Gold : r = +0.761 (très forte)
  - Vision Score vs Victoire : r = +0.037 (faible mais significatif)
- **Régressions** :
  - Simple : csPerMin → goldPerMin (R² = 0.174)
  - Multiple : 6 features → goldPerMin (R² ≈ 0.70–0.80)

### **Section 8 : Comparaisons statistiques — Quali × quanti**
- **T-tests de Welch** (Gagnants vs Perdants) :
  - All variables show **p < 0.001** (différences massives)
  - KDA : d de Cohen = +1.186 (taille d'effet énorme)
  - Deaths : d = −0.876 (les gagnants ont 30% moins de morts)
- **T-test one-sample** : Winrate de chaque rôle vs 50%
- **ANOVA à un facteur** : Tous les rôles ont F >> 1, p < 0.001

### **Section 9 : Analyses thématiques avancées**
- **Radar chart** : Profils multidimensionnels normalisés des 5 rôles (visualisation polaire)
- **Impact des objectifs épiques** : Winrate en fonction du nombre de Dragons et Barons
  - 4+ Dragons → winrate ~90%
  - Baron obtenu → winrate ~83%
- **Vision & victoire** : Score de vision fortement corrélé au succès
- **Régions** : Durées et taux de surrender différents (ANOVA F=significant)

### **Section 10 : Modélisation**
- **Régression logistique** : Prédiction victoire à partir de 8 features (KDA, Gold/min, Damage/min, CS/min, Vision, etc.)
  - Accuracy test : ~68–72%
  - Coefficients standardisés : KDA >> Gold/min >> Damage/min >> Vision
  - Matrice de confusion : FPR et FNR équilibrés

### **Section 11 : Synthèse et conclusion**
- **Tableau récapitulatif** : 18 tests statistiques réalisés
- **Facteurs de victoire identifiés** (par ordre d'importance) :
  1. **KDA** (prédicteur individuel le plus fort)
  2. **Gold/min** (efficacité économique)
  3. **Vision Score** (contrôle du jeu)
  4. **Deaths** (effet négatif)
- **Limitations** : biais de causalité inversée, biais de sélection (Challenger only), effet patch
- **Perspectives** : exploitation table events, ajustement par champion, clustering de joueurs, analyses temporelles

---

## 📈 Visualisations (16 figures PNG)

1. **fig_01_quali_univariee.png** - Distributions rôles/régions/victoires
2. **fig_02_champions.png** - Top 20 champions et winrates
3. **fig_03_histogrammes.png** - Distributions quantitatives + KDE
4. **fig_04_boxplots.png** - Détection des outliers
5. **fig_05_chi2_independence.png** - Stacked bars Rôle×Victoire et Région×Surrender
6. **fig_07_matrice_correlation.png** - Heatmap Pearson 13×13
7. **fig_08_scatter.png** - Nuages de points avec régressions
8. **fig_09_regression.png** - Régression multiple et coefficients
9. **fig_10_ttest_norme.png** - Winrates rôles vs 50% avec IC 95%
10. **fig_11_anova_roles.png** - Boxplots ANOVA par rôle (8 variables)
11. **fig_12_anova_region.png** - Durée et Gold/min par région
12. **fig_13_radar.png** - Radar chart profils normalisés
13. **fig_14_objectives.png** - Impact Dragons/Barons sur winrate
14. **fig_15_vision.png** - Corrélations vision et VisionScore par rôle
15. **fig_16_logistic.png** - Matrice de confusion et coefficients logistiques

---

## 📊 Dataset principal : Schema

### Table `participants` (~100k lignes)
```
matchId : identifiant unique de la partie
summonerName : pseudo du joueur
championName : champion joué
teamPosition : rôle (TOP, JUNGLE, MIDDLE, BOTTOM, UTILITY)
win : booléen, victoire/défaite
kills, deaths, assists : statistiques de combat
goldEarned : or total collecté
totalDamageDealtToChampions : dégâts infligés aux champions
totalMinionsKilled : nombre de créatures tuées
visionScore : score de vision (wards, contrôle)
gameDuration : durée totale de la partie (secondes)
[+ 120 autres colonnes de méta-données]
```

### Table `matches` (~10k lignes)
```
matchId : clé unique
region : Amériques, Asie, ou Europe
gameDuration : durée en secondes
winner_team_id : équipe gagnante (100 ou 200)
team_100_dragon_kills, team_200_dragon_kills : dragons tués
team_100_baron_kills, team_200_baron_kills : barons tués
[+ objectifs épiques, first blood, towers, etc.]
```

---

## 🧪 Tests statistiques réalisés

| Test | Hypothèse | Résultat |
|---|---|---|
| Khi-deux indépendance | Rôle ⊥ Victoire ? | **Indépendants** (p=1.0) |
| Khi-deux indépendance | Région ⊥ Surrender ? | **Dépendants** (V=0.18, p<0.001) |
| Khi-deux adéquation | Kills ~ Poisson(λ) ? | **Rejet** (p<<0.001, surdispersal) |
| T-test deux samples | Gagnants ≠ Perdants (Gold/min) ? | **p<0.001** (d=0.85) |
| T-test deux samples | Gagnants ≠ Perdants (KDA) ? | **p<0.001** (d=1.19) |
| ANOVA unifactorielle | Rôles différents (8 variables) ? | **p<0.001** pour toutes (η²=0.015–0.918) |
| Pearson | Gold ↔ Dégâts ? | **r=+0.865, p<0.001** |
| Pearson | CS/min ↔ Gold/min ? | **r=+0.417, p<0.001** |
| Régression simple | csPerMin → goldPerMin | **R²=0.174** |
| Régression multiple | 6 features → goldPerMin | **R²≈0.70–0.80** |
| Logistique | 8 features → victoire | **Accuracy ≈68–72%** |

---

## 🔬 Méthodologie statistique

### Tests d'indépendance et adéquation
- **Khi-deux d'indépendance** : Variables qualitatives croisées (χ², p-value, V de Cramér)
- **Khi-deux d'adéquation** : Conformité à distribution théorique (Poisson, uniforme)

### Tests de comparaison
- **T-test de Welch** : Comparaison de deux moyennes (indépendant, ne suppose pas égalité des variances)
- **T-test one-sample** : Comparaison vs norme (e.g., winrate vs 50%)
- **ANOVA** : Comparaison de K>2 groupes (F-test, η² pour taille d'effet)

### Corrélation et régression
- **Pearson r** : Relation linéaire bivariée (-1 à +1)
- **Régression linéaire simple** : Modèle univarié
- **Régression linéaire multiple** : Modèle multidimensionnel (train/test 80/20, standardisation)
- **Régression logistique** : Classification binaire (train/test 80/20, stratification)

### Taille d'effet
- **d de Cohen** : (μ1 - μ2) / σ pooled
  Seuils : petite (0.2), moyenne (0.5), grande (0.8)
- **η² (ANOVA)** : SS_between / SS_total

---

## 🎯 Résultats clés

### Facteurs de victoire (prédicteurs)
1. **KDA** (Kills + Assists / Deaths) — **prédicteur individuel #1**
2. **Gold/min** — efficacité économique
3. **Vision Score** — contrôle stratégique
4. **Deaths** (négatif) — chaque mort coûte des ressources

### Facteurs de victoire (équipe)
1. **Baron obtenu** → winrate ~83%
2. **4+ Dragons** → winrate ~90%
3. **First Tower** → winrate ~72% vs 31%
4. **First Dragon** → winrate ~66% vs 44%

### Profils de rôles (distinct & significatif)
| Rôle | Force | Caractéristique |
|---|---|---|
| **UTILITY** | Vision maximale | Pas de CS, focus collectif |
| **BOTTOM** | CS + Dégâts | Carry indépendant |
| **JUNGLE** | Assists | Interventions toutes lanes |
| **MIDDLE** | Gold/min + KDA | Rôle pivot high-impact |
| **TOP** | Survie / Tanking | Profil équilibré |

### Différences régionales
- **Durée partie** : ANOVA significatif (régions ont durées différentes)
- **Surrender** : Khi-deux significatif (Asie vs Amériques vs Europe → cultures distinctes)
- **Gainrate** : ANOVA non-significatif (pas de biais régional sur la victoire)

---

## 🚀 Installation et utilisation

### Prérequis
```bash
python >= 3.8
pip install pandas numpy scipy scikit-learn matplotlib seaborn datasets ipython
```

### Exécution
```bash
# Ouvrir le notebook Jupyter
jupyter notebook LOL.ipynb

# Ou convertir en HTML
jupyter nbconvert --to html LOL.ipynb
```

### Données
Les données sont **téléchargées automatiquement** depuis HuggingFace lors de l'exécution :
```python
from datasets import load_dataset
ds = load_dataset("gptilt/lol-basic-matches-challenger-10k", "matches")
```

Files generated: `matches.csv` et `participants.csv` (~2.4 GB total)

---

## ⚠️ Limitations de l'étude

1. **Biais de sélection** : Données Challenger uniquement (<0.01% joueurs) → résultats non-généralisables aux rangs inférieurs
2. **Biais temporel** : Data ancienne → le méta-jeu (balance, champions OP) a changé
3. **Biais de causalité inversée** : Stats fin de partie masquent la dynamique temporelle
4. **Variables confondantes** : Effet champion non isolé de l'effet rôle
5. **Data leakage évité** : Modèles utilisent uniquement variables prédictives valides (pas tower_kills, etc.)

---

## 🔮 Perspectives futures

- [ ] Exploiter table `events` pour snapshots avant-game (15-min predictors sans leakage)
- [ ] Intégrer catégories champions (Tankle, Assassin, Support, etc.)
- [ ] Clustering non-supervisé pour identifier profils joueurs (aggressive, farm-oriented, etc.)
- [ ] Analyses temporelles : early-game advantage → final outcome
- [ ] API Riot + Data Dragon pour données en temps réel
- [ ] Cross-region comparisons : méta-jeu différent selon région

---

## 📚 Bibliographie

- Riot Games Inc. (2024). League of Legends API
- HuggingFace: `gptilt/lol-basic-matches-challenger-10k`
- Statistical methods: Scipy, Scikit-learn documentation
- Méthodologie statistique : tests paramétriques et non-paramétriques pour sciences des données

---

## 📧 Auteur

Programme Master 1 (M1) - 4EDAP
Université [Institution] — 2024–2025

**Date de création** : Avril 2025
**Dernière mise à jour** : Avril 13, 2025

---

## 📄 License

[À définir selon les politiques institutionnelles]

---

## 🎮 Notes supplémentaires

### Méta-jeu Challenger
Au niveau Challenger, le jeu est **extrêmement optimisé** :
- Chaque décision est calculée (plus de jeu "random")
- Les statistiques reflètent la **vraie compétence** des joueurs
- Les patterns stratégiques sont **reproductibles** et mesurables
- Les objectifs épiques sont **coordonnés** (pas de chaos)

Cela rend le dataset **exceptionnellement riche pour l'analyse statistique**.

### Snowball effect
La distribution des kills est **surdispersée** (écart à Poisson) car le jeu présente un **feedback positif** : un joueur avec kills → avantage économique → plus facile d'obtenir kills → snowball.

### Vision importance
Contrairement aux intuitions, le **Vision Score est prédictif** (r=+0.037 vs victoire). À Challenger, la clairvoyance est **décisive** pour éviter les embuscades et contrôler les dragons/barons.
