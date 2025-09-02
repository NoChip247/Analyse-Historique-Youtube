# Documentation - Préparation des données

Les données ont été exportées (en .json) depuis Google Takeout, puis ont été intégralement manipulées et transformées dans le module Power Query de Power BI.

# Import du .json dans Power Query

- Création d’une “vue” **Référence** de la table watch-history afin de préserver l’intégrité des données d’origines. Tout le travail de nettoyage/transformation/enrichissement sera effectué sur la “vue” **Référence**
- Déployer toutes les colonnes*

Note* : dans le fichier .json d’origine, certaines valeurs sont imbriquées sous forme de tableau `[]` / objet `{}`

```json
[
  { "clé1": "valeur1", "clé2": "valeur2" },
  { "clé1": "valeur3", "clé2": "valeur4" }
]
```

---

# Vérification & nettoyage des données

## Nombre de lignes

- Sélection d'une colonne > onglet **Transformer** > **Compter les lignes**
- Résultat : **23.177**

## Valeurs *null* ou manquantes

- Certaines colonnes (URL de la vidéo, nom de la chaîne, URL de la vidéo, description, details.name) contiennent des valeurs *null*. Ces valeurs null correspondent aux vidéos publicitaires
- Les valeurs *null* de la colonne ‘nom de la chaine’ sont remplacée par “Publicité” afin de pouvoir identifier si le contenu est une vidéo ou une publicité.

## Entêtes de colonnes

- Renommage des entêtes pour une meilleure lisibilité

## Format des données

- Les colonnes qui ne contiennent que du texte sont passées au format **ABC**
- La colonne qui contient les données de dates et heures de visionnage est passée au format **Date Heure**

## Nombre de colonnes

- La colonne `product.0` n’apporte rien à l’analyse. Elle est supprimée

## Nettoyage des données

- Colonne `Titre de la vidéo` : suppression du texte “Vous avez regardé “ qui se trouve avant le titre de la vidéo

---

# Enrichissement des données

Extraction des informations de la colonne `Date heure` :

- Sélectionner la colonne `Date heure` dont on souhaite extraire les données
- Onglet **Ajouter une colonne** > zone Date et heure de début
- **Date** > **Mois** > **Nom du mois**
- **Date** > **Jour** > **Nom du jour**
- **Heure** > **Heure** > **Début de l’heure**

La colonne `Date heure` est passée au format **Date**, et est renommée `Date`

Création d’une table `Calendar` : 

- **Vue du modèle** > **Nouvelle table**
- Dans la barre de formule : `Calendar = CALENDAR(min('watch-history (Référence)'[Date]), max('watch-history (Référence)'[Date]))`
    - `Calendar =` : création de la table
    - Fonction `CALENDAR(start_date, end_date)` : génère une table de dates, allant d'une date de début (`start_date`) à une date de fin (`end_date`)
    - `MIN('watch-history (Référence)'[Date])` : récupère la date la plus ancienne de la colonne `[Date]` de la table `'watch-history (Référence)'`
    - `MAX('watch-history (Référence)'[Date])` : récupère la date la plus récente de la colonne `[Date]` de la table `'watch-history (Référence)'`

Création d’une mesure personnalisé afin de visualiser le jour de la semaine où les vidéos sont le plus regardées :

- Sélectionner la table `watch-history (Référence)` dans le menu de droite
- Onglet **Outil de table** > **Nouvelle mesure**
- Barre de formule : `Nombre_Videos_Par_Jour = COUNTROWS( 'watch-history (Référence)' )`

ℹ️ Cette mesure pourra également servir à calculer le moment de la journée (heure) où les vidéos sont le plus regardées
