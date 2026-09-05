# Ma trame

La trame d'une semaine ordinaire, par catégories.

Dans un tissu, la trame est la structure fixe sur laquelle tout le reste vient se
poser. C'est exactement le rapport entre cette application et un agenda : ici la
semaine qui se répète, là les exceptions qui s'y ajoutent.

Ce n'est donc pas un agenda. Un agenda dit « mardi 14h, atelier informatique,
salle 2 » — le calendrier le fait déjà, et mieux. Celui-ci dit **comment la semaine
est faite** : où sont les blocs de travail, où sont les temps personnels, et surtout
où sont les creux. On le regarde pour décider où caser quelque chose, ou pour
constater qu'on a trois soirées prises d'affilée.

D'où la règle qui le tient à distance du calendrier : **aucun intitulé précis, aucun
lieu**. Rien que des catégories et des plages. C'est ce qui l'empêche de diverger
d'un agenda qu'il ne remplace pas.

Fichier unique, stockage local, aucune dépendance, aucun serveur.

## Fonctionnement

Une semaine générique, du lundi au dimanche, de 8h30 à 20h30, au pas de la
demi-heure. Pas de dates : cette semaine-là ne se périme pas.

On définit ses **catégories** — un nom, une couleur — puis on pose des **blocs**,
jour par jour. La trame du haut les affiche proportionnellement, chacun à la couleur
de sa catégorie. La légende ne liste que les catégories réellement présentes.

Un bloc se modifie ou se retire depuis la liste. Il n'y a pas d'états, pas de suivi,
pas de purge : c'est un document de référence, qu'on change trois ou quatre fois par
an.

## Saisie en liste, affichage en grille

Même décision que dans *Sous réserve*, et pour une raison mesurable ici.

Sur un iPhone, sept colonnes laissent environ 46 px de large, et vingt-cinq lignes
d'une demi-heure donnent des cases de l'ordre de 24 px de haut — la moitié de la
cible tactile recommandée. Une saisie par appui direct raterait souvent, en
particulier pour le second appui qui étend un bloc.

La grille est donc un affichage pur, sans interaction. On saisit dans une feuille :
catégorie, heure de début, heure de fin. Deux sélecteurs sont nécessaires puisqu'ici,
contrairement à *Sous réserve*, la durée ne découle pas de la catégorie — une
permanence peut faire deux heures un jour et quatre un autre. Si la fin passe avant
le début, elle est repoussée automatiquement et le bouton reste bloqué.

## Modèles

Un horaire professionnel est *figé sans être fixe dans le temps* : rien ne bouge à
l'intérieur d'une période, tout peut changer d'une période à l'autre. D'où plusieurs
modèles — rentrée, vacances, été — dont un seul est affiché.

**Dupliquer** copie le modèle actif avec ses blocs, ce qui évite de tout ressaisir
quand une nouvelle période ressemble à la précédente. **Modifier** ne change que le
nom. Le dernier modèle ne peut pas être supprimé : il en faut toujours un.

Les catégories, elles, sont communes à tous les modèles. C'est voulu : ce sont les
mêmes activités qui se répartissent autrement selon la période.

## Couleurs

Palette fermée de huit teintes, choisies pour rester distinguables en petits blocs :

| Teinte | Code |
|---|---|
| Violet | `#7C4DCC` |
| Rouge | `#D4453E` |
| Bleu clair | `#4FA3D1` |
| Terracotta | `#D97A45` |
| Vert | `#5FAE7C` |
| Ocre | `#E3A92F` |
| Rose | `#D471A6` |
| Gris chaud | `#A79B8A` |

Plus vives que la charte habituelle, volontairement : la carte se lit en blocs de
quelques pixels de haut, et le contraste y compte plus que la retenue. Le gris chaud
est le seul qui recule au lieu d'avancer, ce qui en fait le bon candidat pour une
catégorie « Réserve », qui doit se voir sans attirer l'œil.

Pas de choix libre : deux teintes trop proches rendraient la carte illisible, or
c'est sa seule fonction. **Deux catégories ne peuvent pas partager une couleur** —
celles déjà prises sont estompées dans la palette. Huit couleurs laissent de la
marge ; en vouloir une neuvième signalerait qu'on dérive vers l'agenda.

Pas d'emoji non plus, contrairement à *Sous réserve* : un emoji serait illisible dans
un bloc d'une demi-heure, et la couleur suffit puisque la liste nomme chaque bloc.

## Suppression d'une catégorie

Refusée tant que des blocs l'emploient — **dans n'importe quel modèle**, pas
seulement dans celui qui est affiché. Sans cela, changer de modèle ferait apparaître
des blocs orphelins.

Rien ne se purge ici, donc le blocage ne se lève pas tout seul : il faut retirer les
blocs concernés.

## Stockage

```
matrame.categories   [ { id, nom, coul } ]
matrame.modeles      [ { id, nom, blocs: [ { id, jour, cat, deb, fin } ] } ]
matrame.actif        id du modèle affiché
```

`jour` de 0 (lundi) à 6, `deb` et `fin` en minutes depuis minuit. L'ordre des
tableaux est l'ordre d'affichage, il n'est jamais retrié. Le préfixe compte : toutes
les apps de `azertida.github.io` partagent la même origine et le même quota.

## Export et import

Contrairement à *Sous réserve*, dont la semaine s'efface d'elle-même, les données
sont ici durables : une carte complète perdue lors d'un changement d'appareil
représente une vraie ressaisie. D'où un export.

**Exporter** télécharge un `.json` nommé `ma-trame-AAAA-MM-JJ.json`, contenant les
catégories, tous les modèles et l'identifiant du modèle affiché.

**Importer** remplace intégralement ce qui est en place, après confirmation. Pas de
fusion : réunir deux sauvegardes produirait des doublons de catégories et des
modèles en double sans qu'on sache lequel fait foi.

Le fichier porte `"app": "ma-trame"` et `"format": 1`. Les sauvegardes antérieures
au changement de nom, marquées `"ma-semaine"`, sont acceptées elles aussi. L'import refuse un fichier
d'une autre application, un JSON invalide, ou une sauvegarde sans aucun modèle.
Incrémenter `FORMAT` si la structure change, et prévoir alors une lecture des
anciens formats plutôt qu'un rejet.

## Bornes

`DEB` et `FIN` en tête du script, en minutes depuis minuit, et `PAS` pour la
granularité. Élargir l'amplitude allonge la carte sans rien casser ; descendre `PAS`
sous 30 minutes multiplierait les options des sélecteurs sans gain réel.

## PWA

Installation sans danger : rien n'arrive de l'extérieur. Une seule précaution,
**installer avant de saisir** — ce qui aurait été rempli dans l'onglet du navigateur
ne suivrait pas dans l'application installée.

Le service worker sert la page en réseau d'abord et les icônes en cache d'abord.
Pour forcer un renouvellement complet, incrémenter `CACHE` dans `service-worker.js`.

## Fichiers

```
index.html
manifest.json
service-worker.js
icon-180.png            apple-touch-icon (fichier externe obligatoire, pas de base64)
icon-192.png
icon-512.png
icon-512-maskable.png
```

## Licence

CC0.
