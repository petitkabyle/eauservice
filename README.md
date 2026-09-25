# EauService — où coller quoi

Ce dépôt ne contient pas un site installable : il contient les **morceaux de code**
à coller dans WordPress. Ce fichier dit, pour chacun, sa destination exacte.

## Tableau de correspondance

| Fichier du dépôt | Destination dans WordPress | Type |
|---|---|---|
| `snippet general` | Code Snippets → snippet `eauservice-function` | Functions (PHP), *Exécuter partout* |
| `snippet devis acceuil` | Code Snippets → snippet `devis acceuil` | Functions (PHP), *Exécuter partout* |
| `snippet SEO` | Code Snippets → snippet données structurées produit | Functions (PHP), *Exécuter partout* |
| `snippet ajouter monaco` | Code Snippets → snippet Monaco / checkout | Functions (PHP), *Exécuter partout* |
| `snippet traduction` | Code Snippets → snippet traduction EN | Functions (PHP), *Exécuter partout* |
| `snippet nombre article panier` | Code Snippets → snippet pastille panier | Functions (PHP), *Exécuter partout* |
| `suprimer bouton panier` | Code Snippets → snippet masquage panier accueil | Functions (PHP), *Exécuter partout* |
| `snippet facture pdf` | Code Snippets → snippet CSS facture PDF | Functions (PHP), *Exécuter partout* |
| `CSS add` | Apparence → Personnaliser → **CSS additionnel** | CSS |
| `code accueil` | Page d'accueil → bloc **HTML personnalisé** | HTML |
| `import-code-snippets.json` | Code Snippets → Importer *(secours uniquement)* | JSON |

## Règles à ne pas oublier

**Pour les snippets PHP :** ne collez **jamais** la ligne `<?php`. L'extension
Code Snippets l'ajoute elle-même ; la remettre provoque une erreur fatale.
Collez le contenu du fichier tel quel, de la première à la dernière ligne.

**Pour `code accueil` :** remplacez **tout** le contenu du bloc HTML personnalisé.
Ne fusionnez pas à la main avec l'ancienne version : le fichier contient à la fois
le HTML, son CSS et son JavaScript, et ils dépendent les uns des autres.

**Pour `CSS add` :** remplacez **tout** le contenu de « CSS additionnel ».

**Pour `import-code-snippets.json` :** il ne sert qu'à réinstaller les deux gros
snippets d'un coup en cas de problème. Deux points de vigilance : les snippets
arrivent **désactivés** (il faut les activer à la main après l'import), et un
import crée des doublons si les snippets existent déjà — supprimez les anciens
avant. Dans la vie normale, préférez le copier-coller fichier par fichier.

## Ordre de collage recommandé

L'ordre importe peu techniquement, sauf pour un point : `snippet SEO` appelle une
fonction définie dans `snippet general`. Il sait s'en passer (il refait le test
lui-même), mais autant coller `snippet general` en premier.

1. `snippet general` — c'est le cœur : zones de livraison, champs de commande,
   données structurées, e-mails, polices du site.
2. Les autres snippets, dans n'importe quel ordre.
3. `CSS add`.
4. `code accueil`.

## Après avoir tout collé — 6 vérifications

1. **Une commande de test complète**, de l'ajout au panier jusqu'au paiement.
   Vérifiez que les frais de livraison correspondent bien à la zone choisie.
2. **Choisissez une zone qui ne correspond pas à l'adresse** (par exemple zone
   « Antibes » avec une adresse à Saint-Tropez) : la commande doit être refusée
   avec un message qui nomme la bonne zone.
3. **Laissez vide « Adresse exacte de livraison »** : la commande doit être
   refusée. Puis cliquez sur « Reprendre mon adresse de facturation » : le champ
   doit se remplir sous vos yeux.
4. **Les polices s'affichent bien** sur l'accueil, la boutique, le panier et la
   page de commande. Elles ne sont plus chargées que par le snippet : si elles
   manquent quelque part, c'est que `snippet general` n'est pas actif.
5. **Le compteur du panier** sur l'accueil : ajoutez un article, revenez à
   l'accueil, la pastille doit afficher le bon nombre.
6. **Une fiche produit sur** `search.google.com/test/rich-results` : il doit y
   avoir **un seul** bloc `Product`.

## Vérifications propres aux optimisations de vitesse

7. **Le rideau d'entrée** (le calque bleu qui se retire) doit se jouer **une seule
   fois** : à l'arrivée sur le site. Naviguez ensuite vers la boutique puis le
   panier — plus de rideau. Fermez l'onglet, revenez : il se rejoue.
8. **Les polices** s'affichent bien partout (c'est la même URL Google, mais en
   version variable). Si un texte apparaît dans une police système, `snippet
   general` n'est pas actif.
9. **La page d'accueil** doit être visuellement inchangée, alors que la feuille de
   style de la boutique n'y est plus envoyée. Comparez avec une capture d'avant si
   vous en avez une.
10. **Les mentions légales / CGV** (pages hors boutique) doivent conserver
    l'en-tête et le pied de page stylés : elles reçoivent toujours le CSS.
11. **Le survol d'un lien** vers la boutique doit rendre le clic quasi instantané.
    Vérifiez surtout qu'un survol n'ajoute **jamais** de produit au panier — les
    adresses avec paramètres sont exclues du préchargement.

Si un doute subsiste sur l'apparence, le plus simple est de désactiver
temporairement `snippet general` : le site revient à son comportement d'avant.

## Ce qui reste à faire de votre côté

Trois choses que le code ne peut pas faire à votre place :

- **Créer les trois pages légales**, avec exactement ces adresses, sinon les liens
  du pied de page renverront une erreur 404 :
  `/mentions-legales/`, `/conditions-generales-de-vente/`,
  `/politique-de-confidentialite/`.
- **Trancher la question de la TVA sur les frais de livraison** avec votre
  comptable. Le réglage est à un seul endroit : `snippet general`, section 15c.
  Il est laissé sur le comportement actuel (frais non taxables).
- **Décider du sort des avis clients** et des chiffres affichés sur l'accueil.
  Les explications sont dans `code accueil`, juste avant la section « AVIS
  CLIENTS », et dans `AUDIT.md` au point 2.8.

## Repères techniques

- **Une seule source de vérité par sujet**, dans `snippet general` :
  tarifs et zones → `eauservice_delivery_zones()` ; codes postaux par zone →
  `eauservice_zone_codes_postaux()` ; adresse de contact →
  `eauservice_email_contact()` ; villes desservies → `eauservice_villes_desservies()`.
  Modifiez à cet endroit et la valeur se propage partout : menu déroulant, calcul
  des frais, simulateur du panier, e-mails, back-office, données structurées.
- **Rank Math reste le seul à décrire l'entreprise à Google.** Les snippets
  complètent sa fiche au lieu de la dupliquer (`snippet general`, section 21).
  L'adresse, le téléphone et les horaires se modifient dans Rank Math, pas ici.
- **Le tunnel de commande dépend du checkout « shortcode »** (celui qui utilise
  `[woocommerce_checkout]`). Si la page Commande est un jour recréée avec le
  **bloc** Checkout, les champs de livraison événementielle et le calcul des
  frais cesseront de fonctionner. Voir `AUDIT.md`, point 1.7.

---

`AUDIT.md` contient l'audit complet du site : ce qui a été corrigé, ce qui reste
ouvert, et ce qui relève d'une décision de votre part.
