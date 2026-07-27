# Carnet de tables

Suivi personnel de restaurants : notes sur 10, quatre critères, carte et statistiques.
Application en fichier unique, hébergée sur GitHub Pages, sur le même modèle que
`bibliotheque`, `thes` et `flambee`.

## Mise en place

1. Créer un dépôt public `tables` sur le compte `gargouille88`.
2. Y déposer les quatre fichiers : `index.html`, `manifest.json`, `icon.svg`, `icon-maskable.svg`.
3. Activer Pages : *Settings → Pages → Source : deploy from a branch → main / (root)*.
4. L'app est en ligne sur `https://gargouille88.github.io/tables/`.

Si le dépôt porte un autre nom, le corriger dans l'onglet **Réglages** une fois connecté,
ou directement dans l'objet `CFG` en tête du bloc de données.

## Écriture

Sans jeton, l'application est en lecture seule : ni bouton d'ajout, ni onglet Réglages,
et les fiches s'ouvrent en consultation.

Pour écrire, cliquer sur **Connecter** dans le bandeau et coller un jeton GitHub à portée
`contents: write` sur ce dépôt (*Settings → Developer settings → Personal access tokens →
Fine-grained tokens*). Le jeton est conservé dans le `localStorage` du navigateur, sous la
clé `gh_token`, comme sur les autres projets. **Déconnecter** l'efface.

Le bandeau a trois états : gris en lecture seule, ambre quand des modifications attendent,
vert quand tout est publié, avec l'heure du dernier envoi.

## Fonctionnement des données

Les données vivent dans `index.html`, entre les deux marqueurs `__DATA_DEBUT__` et
`__DATA_FIN__`. Publier relit le fichier distant via l'API GitHub, remplace ce seul bloc
et le renvoie. Trois précautions déjà en place, héritées des ennuis rencontrés sur `flambee` :

- décodage par `TextDecoder('utf-8')`, sinon `atob` seul corrompt les accents ;
- ré-encodage base64 par blocs de 8 Ko, sinon `String.fromCharCode(...bytes)` déborde la pile ;
- remplacement par fonction, sinon les motifs `$&` présents dans un commentaire seraient interprétés.

En cas de conflit de `sha` (HTTP 409), la publication relit le fichier et retente une fois.

Ne jamais laisser traîner un deuxième couple de marqueurs dans le fichier : le remplacement
prendrait la première paire rencontrée.

## Champs

| Champ | Rôle |
|---|---|
| Nom, commune | Autocomplétés depuis les restaurants OpenStreetMap (API Photon) |
| Style | Classification unique, modifiable dans Réglages |
| Gamme de prix | € à €€€€ |
| Distinction | Bib Gourmand, étoile verte, 1 à 3 étoiles |
| Note | Sur 10, saisie directe et non calculée |
| Critères | Cuisine, cadre, service, rapport qualité-prix, sur 5, facultatifs |
| Statut | Table faite ou à faire |

Les coordonnées ne sont pas saisies : elles viennent de la suggestion choisie, ou à défaut
d'une géolocalisation de la commune au moment de l'enregistrement.

## Raccourcis

- `N` : nouvelle table (si connecté)
- Clic sur un en-tête de colonne : tri, puis inversion du sens
- Clic sur un repère de la carte : ouverture de la fiche

## Dépendances externes

- Leaflet 1.9.4 et tuiles OpenStreetMap pour la carte
- API Photon (Komoot) pour l'autocomplétion
- Google Fonts : Fraunces, IBM Plex Sans, IBM Plex Mono

Hors ligne, la carte affiche un message de repli et l'autocomplétion invite à saisir à la main :
le reste de l'application continue de fonctionner.
