# Dashboard Power BI — Marché immobilier en Île-de-France

## Source des données

[Statistiques DVF](https://www.data.gouv.fr/datasets/statistiques-dvf) — Demandes de Valeurs Foncières, publiées par data.gouv.fr (DGFiP / Etalab). Données réelles et officielles de transactions immobilières en France, agrégées par commune et département.

Filtré sur les 8 départements d'Île-de-France (75, 77, 78, 91, 92, 93, 94, 95), sur toute la période disponible (5 dernières années).

## Fichiers fournis

- `data/dvf_idf_departements.csv` — 8 lignes, une par département (vue d'ensemble)
- `data/dvf_idf_communes.csv` — 1269 communes IDF avec au moins 5 ventes (détail/drill-down)

Colonnes principales : `prix_m2_moyen_appartement`, `prix_m2_median_appartement`, `prix_m2_moyen_maison`, `prix_m2_median_maison`, `nb_ventes_*`.

**Point d'attention** : à Paris, très peu de maisons sont vendues (quasi tout est en appartement) — le prix moyen "maison" y est donc gonflé par une poignée d'hôtels particuliers de luxe. Bon point à mentionner si on te pose la question en entretien : ça montre que tu lis les données de façon critique plutôt que de sortir un chiffre sans le comprendre.

## Étapes de construction dans Power BI Desktop

### 1. Importer les données
- Accueil → Obtenir les données → Texte/CSV
- Importer `dvf_idf_departements.csv` puis `dvf_idf_communes.csv`
- Dans Power Query, vérifie que les colonnes `prix_m2_*` et `nb_ventes_*` sont bien typées en **Nombre décimal** (pas texte)

### 2. Créer une relation entre les deux tables
- Vue Modèle → relie `code_departement` (table communes) à `code_departement` (table départements)
- Cardinalité : plusieurs-à-un (plusieurs communes → un département)

### 3. Construire les visuels clés

**Carte des prix par département ou commune**
- Visuel "Carte" ou "Carte choroplèthe"
- Emplacement : `nom_departement` ou `commune`
- Couleur : `prix_m2_moyen_appartement`
→ Ça donne un visuel immédiat très parlant pour un recruteur (gradient de prix visible sur la carte IDF)

**Classement des communes les plus chères**
- Visuel "Graphique à barres horizontales"
- Axe Y : `commune`, Axe X : `prix_m2_moyen_total`
- Filtre Top N = 15, trié décroissant

**Comparaison appartement vs maison par département**
- Visuel "Graphique à barres groupées"
- Axe : `nom_departement`, Valeurs : `prix_m2_moyen_appartement` et `prix_m2_moyen_maison`

**Volume de ventes par département**
- Visuel "Graphique en anneau" ou "Barres"
- `nom_departement` / `nb_ventes_total`

**Cartes KPI en haut du dashboard**
- 3-4 visuels "Carte" (valeur unique) : prix moyen IDF global, nombre total de ventes, département le plus cher, commune la plus chère
- Utilise des mesures DAX simples, ex :
  ```
  Prix Moyen IDF = AVERAGE(dvf_idf_departements[prix_m2_moyen_total])
  ```

### 4. Ajouter un slicer (filtre interactif)
- Slicer sur `nom_departement` en haut du dashboard, pour que l'utilisateur filtre toute la page en cliquant sur un département

### 5. Mise en forme
- Thème cohérent (Affichage → Thèmes) — reste sobre, 2-3 couleurs max
- Titre clair : "Marché immobilier en Île-de-France — Prix au m² par zone (données DVF)"
- Ajoute une zone de texte avec la source et la période des données en bas de page

## Ce que ce projet démontre pour ton profil

- Manipulation de données réelles, officielles et volumineuses (nettoyage, filtrage géographique)
- Construction d'un dashboard interactif avec KPI, carte, comparaisons — exactement ce que "Power BI intermédiaire" doit couvrir sur ton CV
- Esprit critique sur la donnée (le point sur les maisons parisiennes)

## Pour aller plus loin (optionnel)
- Ajouter une mesure DAX de variation de prix si tu récupères aussi les fichiers "mensuels" DVF
- Croiser avec le prix au m² Paris par arrondissement (déjà présent dans le fichier communes)
