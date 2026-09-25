# Audit du site EauService — constats, corrections, et ce qui reste ouvert

Dépôt `petitkabyle/eauservice` · audit initial le 25 septembre 2026 sur le commit
`cd94956`, corrections appliquées le même jour.

---

## Avant tout : la mise au point importante

**Rien dans cet audit ne cassait le site, et rien ne sabotait le référencement.**
Le site fonctionnait. Il n'y avait aucune erreur PHP, aucun écran blanc, aucune
pénalité Google en place. La première version de ce document classait les
constats par *impact potentiel*, ce qui donnait une impression d'urgence
généralisée qui ne correspondait pas à la réalité. Ce classement-ci est honnête.

Trois niveaux, et rien d'autre :

| Niveau | Ce que ça signifie | Nombre |
|---|---|---|
| **A** | Ça vous coûte de l'argent, ou vous expose juridiquement | 5 |
| **B** | Ça ne coûte rien aujourd'hui, mais ça se paiera plus tard | 7 |
| **C** | Confort de maintenance, propreté du code | 9 |

Une précision sur la portée : l'analyse porte sur le **code** du dépôt. Le site en
ligne n'a pas été exploré (pas d'accès réseau depuis l'environnement d'analyse).
Versions d'extensions, TVA réglée dans WooCommerce, SPF/DKIM, Lighthouse mesuré,
Search Console, sauvegardes : voir le chapitre « À vérifier hors dépôt ».

---

## Niveau A — conséquence concrète

### A1. Zone de livraison jamais recoupée avec l'adresse — **CORRIGÉ**

Les frais dépendaient uniquement du menu déroulant (0 € Antibes → 150 €
Saint-Tropez), sans aucun contrôle. Un client pouvait cocher « Antibes — offerte »
et faire livrer à Saint-Tropez : 150 € de perte, invisible en back-office. Le cas
le plus fréquent n'était même pas la mauvaise foi mais l'hésitation : quelqu'un qui
livre à Vallauris ne sait pas si cela relève d'Antibes ou de Cannes.

Ajout d'une table `code postal → zone` (`eauservice_zone_codes_postaux()`), d'une
extraction du code postal depuis l'adresse libre, et d'un contrôle à la validation.
La règle retenue : **on ne bloque que si la zone choisie coûte moins cher** que
celle du code postal saisi. Si le client a choisi une zone plus chère, il passe —
ce n'est pas au code de refuser qu'on le paie davantage. Si le code postal est
absent ou inconnu de la table, la commande passe aussi, mais elle est **marquée**
(`_es_zone_a_verifier`) et l'avertissement apparaît en orange dans le back-office
et dans votre e-mail de notification — pas dans celui du client.

Testé : 6 scénarios, dont « Antibes choisi pour Saint-Tropez » (bloqué),
« Cannes choisi pour Antibes » (laissé passer), adresse sans code postal (signalée).

### A2. L'adresse de livraison était écrasée en silence — **CORRIGÉ**

`snippet ajouter monaco` recopiait l'adresse de facturation dans « Adresse exacte
de livraison » quand le champ était vide, en priorité 1, donc **avant** la
validation. Le champ marqué obligatoire ne pouvait donc jamais afficher son
erreur : il était de fait facultatif. Et un client qui le sautait voyait son
adresse de facturation — souvent le siège social — devenir le lieu de livraison.
C'était le seul constat de tout l'audit capable de gâcher une prestation : un
camion envoyé à Paris au lieu du Palais des Festivals.

Le préremplissage automatique est supprimé. À la place, un bouton visible
« ↳ Reprendre mon adresse de facturation » remplit le champ **sous les yeux du
client**, qui peut ensuite préciser le hall ou le stand. La validation obligatoire
retrouve son rôle.

### A3. `$errors->remove()` effaçait d'autres erreurs — **CORRIGÉ**

`WP_Error::remove( $code )` supprime *tous* les messages portant ce code. Or
WooCommerce range sous le même code `validation` le format d'e-mail, celui du
téléphone et celui du code postal. Dès qu'une adresse monégasque déclenchait une
erreur de code postal, les autres erreurs de la requête disparaissaient avec elle.

La liste est maintenant reconstruite : seuls les messages parlant de code postal
sont retirés, les autres sont réinjectés avec leurs données.

### A4. TVA sur les frais de livraison — **LAISSÉ EN L'ÉTAT, VOLONTAIREMENT**

Les frais étaient ajoutés en non taxable (`add_fee( …, false )`). Si vos prix sont
saisis HT — et l'accueil affiche « Tarifs indicatifs HT » — les frais devraient
porter la TVA sur la facture.

**Je n'ai pas basculé ce réglage, et c'est délibéré.** Passer à `true` modifie le
total payé par vos clients (100 € de frais deviennent 120 €) et change les factures
PDF. C'est une décision comptable, pas technique. Le comportement actuel est donc
conservé à l'identique : **rien n'a changé sur le site**.

Ce qui a changé : le réglage est désormais à **un seul endroit** et documenté —
`eauservice_frais_livraison_taxables()`, section 15c de `snippet general`. Quand
votre comptable aura tranché, il y a une ligne à modifier.

### A5. Mentions légales, CGV et politique de confidentialité — **LIENS AJOUTÉS, PAGES À CRÉER**

Aucun de ces liens n'existait, ni au pied de page, ni sous le formulaire de devis
qui collecte nom, e-mail et téléphone. C'est le seul point de l'audit avec un
risque de sanction réelle (information précontractuelle du code de la
consommation, articles 12 à 14 du RGPD).

Une quatrième colonne « Informations légales » a été ajoutée au pied de page, et
une mention RGPD explicite sous le formulaire, avec lien vers la politique de
confidentialité.

> **À faire de votre côté :** créer les trois pages WordPress avec exactement ces
> adresses, sinon les liens renverront une 404 :
> `/mentions-legales/` · `/conditions-generales-de-vente/` ·
> `/politique-de-confidentialite/`

---

## Niveau B — ça se paiera plus tard

### B1. Schema `Product` publié en double avec Rank Math — **CORRIGÉ**

`snippet SEO` annonçait dans son commentaire qu'il se mettait en pause si « Yoast
**ou Rank Math** » publiait déjà un schema Product. Le code ne testait que Yoast.
Rank Math étant l'outil installé, chaque fiche produit exposait deux blocs.

Pour être exact sur la gravité : ce n'est **pas** une pénalité. Deux blocs
décrivant le même produit avec les mêmes données, Google en choisit un. Le risque
réel apparaît si les deux annoncent des prix différents — le vôtre utilise
`wc_get_price_to_display`, Rank Math peut se baser sur un autre réglage HT/TTC —
et dans ce cas Google peut supprimer l'affichage du prix.

La garde Rank Math est ajoutée, avec repli si `snippet general` est absent.

### B2. Prix figés dans les données structurées — **CORRIGÉ**

Les schemas `Service` annonçaient 140 €, 180 €, 125 € et un `AggregateOffer`
140-180 € écrits en dur. Justes aujourd'hui peut-être, faux à la première hausse
de tarif — et un visiteur qui a lu « 140 € » dans Google puis découvre autre chose
sur le site, c'est une déception évitable.

Tous les prix sont retirés de ces schemas. Les prix ne vivent plus que sur les
fiches produit, où ils sont lus directement dans WooCommerce.

### B3. Compteur du panier bloqué sur « 0 » — **CORRIGÉ**

L'en-tête sur mesure de l'accueil code la valeur en dur (`<span class="badge">0</span>`)
et ne peut pas exécuter de PHP. Le snippet « pastille panier » ne pouvait pas
l'aider : il agit sur les menus WordPress, ceux que l'accueil masque. Et la
section 14a retirait `wc-cart-fragments` hors pages WooCommerce, supprimant le
seul mécanisme de mise à jour. Un visiteur ajoutait trois articles et lisait
« Panier 0 ».

`wc-cart-fragments` est conservé, et la pastille est déclarée comme fragment
WooCommerce : elle se met à jour toute seule, cache inclus.

### B4. Focus clavier invisible — **CORRIGÉ**

`outline:none !important` s'appliquait à tous les liens, boutons et champs de
l'accueil. Plus aucun repère en navigation au clavier, formulaire de devis
compris. Échec direct au critère WCAG 2.4.7 / RGAA 10.7 — et un obstacle concret
pour un site qui vise aussi des acheteurs publics.

Remplacé par `:focus-visible`, qui ne se déclenche **qu'au clavier** : l'aspect du
site ne change pas au clic de souris. Même traitement ajouté côté boutique.

### B5. Animations permanentes sans échappatoire — **CORRIGÉ**

Bandeau défilant en boucle infinie, carrousel d'avis, machine à café qui flotte,
halos qui pulsent. Une seule règle `prefers-reduced-motion` existait, et elle ne
couvrait que le rideau d'entrée.

Un bloc global respecte maintenant ce réglage système : le contenu reste
identique, il devient immobile.

### B6. Le tri du catalogue annulait le choix du visiteur — **CORRIGÉ**

Le filtre `the_posts` re-triait après la requête : « Trier par prix croissant »
était exécuté par la base, puis écrasé. Le menu de tri semblait cassé.

Le tri personnalisé s'efface dès que le visiteur demande explicitement un tri.
Reste une limite documentée dans le code : ce tri ne réordonne que la page
affichée. La solution durable ne passe pas par du code mais par WooCommerce →
Produits → onglet « Tri », qui enregistre l'ordre en base et fonctionne avec la
pagination.

### B7. Polices chargées trois fois, dont un `@import` bloquant — **CORRIGÉ**

Les mêmes deux polices étaient demandées par un `<link>` dans l'accueil, un
`@import` dans « CSS additionnel » et un `preconnect` dans le snippet. Le
`@import` était le plus coûteux : il bloque l'affichage et ne démarre qu'après le
téléchargement de la feuille de style.

Un seul chargement désormais, via `wp_enqueue_style` dans `snippet general`
(section 14d). Le `@import` et le `<link>` ont été retirés.

> Reste ouvert : les polices sont toujours servies par Google. La CNIL considère
> cet appel comme un transfert d'IP vers un tiers ; l'auto-hébergement des deux
> fichiers serait plus propre. Non fait pour ne pas risquer de casser
> l'affichage sans pouvoir tester sur le site réel.

---

## Niveau C — confort et propreté

| # | Constat | État |
|---|---|---|
| C1 | Formulaire de devis sans limite de débit : `admin-post.php` pouvait être sollicité en boucle. Ajout d'une limite de 4 envois par 30 min et par IP (empreinte seulement, pas d'IP en clair), referer devenu obligatoire, contrôles de fond sur les champs. Un nonce est impossible ici : le formulaire est dans un bloc HTML statique, qui n'exécute pas de PHP — l'explication est dans le code. | **Corrigé** |
| C2 | `document_title_parts` ne neutralisait que Yoast, pas Rank Math : deux codes écrivaient le titre de la boutique. | **Corrigé** |
| C3 | `contactOption => 'TollFree'` déclarait un numéro gratuit pour un mobile payant. Retiré. `knowsAbout` ramené aux domaines de compétence réels : les noms de lieux (Palais des Festivals, Grimaldi Forum…) suggéraient un lien officiel. | **Corrigé** |
| C4 | Le filtre `woocommerce_gallery_thumbnail_size` faisait télécharger des images de 600 px pour des vignettes de 100 px, sur chaque fiche produit. Retiré. | **Corrigé** |
| C5 | Écran de chargement mort : HTML présent, masqué par `display:none`, JavaScript disparu. Supprimé, HTML et CSS. | **Corrigé** |
| C6 | Neutralisation de l'autocomplétion Google par **clonage** du champ adresse : tous les écouteurs étaient perdus, et l'opération reposait sur des minuteries (300/1000/600 ms). Remplacée par une approche non destructive. | **Corrigé** |
| C7 | 9 règles CSS mortes `.cf-*`, héritage d'un ancien formulaire de contact dont plus aucune classe n'existe dans le HTML. Supprimées. | **Corrigé** |
| C8 | Les deux langues ne partageaient pas leur état : EN choisi sur l'accueil, puis retour au français sur la boutique. Les deux systèmes écrivent maintenant sur la même clé `es_lang`. | **Corrigé** |
| C9 | `import-code-snippets.json` dupliquait deux snippets : toute correction créait un écart silencieux. Régénéré depuis les fichiers, vérifié identique. | **Corrigé** |

---

## Ce qui reste ouvert

### Décisions qui vous appartiennent

**Les avis clients et les chiffres affichés.** Je n'ai pas touché aux 9
témoignages : je n'ai aucun moyen de savoir s'ils correspondent à de vrais
clients. Si ce sont des textes de maquette, il faut les remplacer — depuis la
directive Omnibus, vous devez pouvoir démontrer que les avis affichés viennent de
clients réels et indiquer comment vous les vérifiez. Même question pour « +250
clients », « +500 événements », « +20 ans », et pour la liste des 31 événements
nommés sous « nous équipons », qui suggère un statut de fournisseur officiel.

Vous avez déjà une fiche Google Business liée dans le code : un widget d'avis
Google réglerait la conformité **et** serait plus convaincant qu'un témoignage
anonyme. Les trois options sont détaillées en commentaire dans `code accueil`,
juste avant la section « AVIS CLIENTS ».

**La TVA sur les frais** (A4) et **les trois pages légales** (A5).

### Chantiers non traités

**Le checkout en blocs.** Toute la personnalisation du tunnel repose sur les hooks
du checkout « shortcode » (`woocommerce_after_order_notes`,
`woocommerce_review_order_before_payment`…). Sur le **bloc** Checkout, activé par
défaut sur les installations récentes, aucun ne s'exécute : les champs de
livraison et le calcul des frais disparaîtraient d'un coup, sans erreur visible.
Ce n'est pas un problème aujourd'hui — c'en sera un le jour où quelqu'un refait la
page Commande. À vérifier : la page Commande contient-elle `[woocommerce_checkout]`
ou le bloc ? La migration passerait par
`woocommerce_register_additional_checkout_field()`.

**L'anglais.** Les deux mécanismes partagent maintenant leur état, mais la
traduction reste côté navigateur : aucune URL distincte, aucun `hreflang`, donc
zéro bénéfice en référencement. Ce n'est pas un malus, c'est du travail sans
retour. Si l'anglais compte commercialement, il faut de vraies URL (`/en/…`) via
Polylang ou TranslatePress. Par ailleurs le dictionnaire associe des phrases
françaises entières à leur traduction : la moindre retouche de texte côté FR fait
silencieusement retomber la phrase en français.

**Images en WebP.** Les 8 images de l'accueil sont des PNG, dont des
`-removebg-preview.png`. Le lazy-loading et la priorisation du visuel héros sont
en place, mais la conversion en WebP se fait dans la médiathèque, pas dans le
code. Précision par rapport à ma première version de cet audit : il n'y avait
**quasiment pas de CLS**, contrairement à ce que j'avais écrit — les conteneurs
d'images ont déjà des hauteurs fixes (172 px et 220 px). Seul le visuel héros
pouvait décaler, et une `min-height` a été ajoutée.

**`CSS add`.** 1 468 lignes et plus de 800 `!important`, avec des sélecteurs
répétés. Chaque correctif futur exigera un `!important` de plus. Une
restructuration serait utile, mais c'est un chantier à part entière, avec un vrai
risque de régression visuelle — à ne pas mener à l'aveugle sans pouvoir comparer
sur le site réel.

**Fichiers sans extension.** `snippet general`, `CSS add`… aucun outil ne peut les
analyser automatiquement. Les renommer en `.php` / `.css` permettrait un contrôle
de syntaxe en intégration continue. Non fait pour ne pas perturber votre
copier-coller ni l'historique Git — à décider.

---

## Ce qui était déjà solide

À dire clairement : ce code est nettement plus soigné que la moyenne des
personnalisations WooCommerce faites par snippets.

- **Sécurité des entrées et sorties** : `sanitize_*` et `wp_unslash`
  systématiques, `esc_html` / `esc_attr` / `esc_url` à l'affichage,
  `wp_json_encode` pour passer les données au JavaScript, garde `ABSPATH` partout.
- **Injection d'en-têtes e-mail neutralisée** : le nom du prospect est nettoyé des
  retours chariot et des `<>";:` avant d'entrer dans le `Reply-To`. C'est la
  faille qu'on trouve dans la quasi-totalité des formulaires artisanaux.
- **Sources de vérité uniques** pour les zones, l'e-mail de contact, les villes,
  les moyens de paiement. Le simulateur du panier lit les mêmes tarifs que le
  checkout : la divergence « 120 € affiché / 100 € facturé » ne peut plus revenir.
- **Compatible HPOS** : les métadonnées passent par `$order->update_meta_data()`.
- **Délivrabilité traitée sérieusement** : un envoi par destinataire, journalisation
  via `wp_mail_failed`, absence volontaire d'en-tête `From` pour laisser YaySMTP
  utiliser son domaine authentifié. Le commentaire qui documente le diagnostic MX
  est exemplaire.
- **Anti-doublon Rank Math bien conçu** : le filtre `rank_math/json_ld` *complète*
  le schema existant au lieu de le réécrire, avec repli automatique.
- **Aucun faux avis structuré** : `aggregateRating` n'est publié que s'il existe de
  vrais avis. Rigueur rare.
- **La qualité des commentaires**, qui expliquent le *pourquoi* et gardent
  l'historique des bugs corrigés. C'est ce qui rend ce code reprenable.

---

## Vérifications effectuées sur les corrections

- `php -l` sur les 8 snippets (PHP 8.4) : aucune erreur de syntaxe.
- Aucune fonction PHP déclarée deux fois entre snippets (82 fonctions
  `eauservice_*` uniques) : pas de risque d'erreur fatale « cannot redeclare ».
- Logique métier **exécutée** avec des doublures de WordPress : 24 assertions,
  0 échec — déduction de zone sur 12 codes postaux, extraction du code postal sur
  4 adresses libres, 6 scénarios de blocage, cohérence des dates, 4 blocs JSON-LD
  valides et sans prix en dur.
- 16 blocs JavaScript (5 dans l'accueil, 11 dans les snippets) : syntaxe validée.
- `code accueil` : balises équilibrées (233 `div`, 10 `section`, 8 `ul`, 30 `li`),
  accolades CSS équilibrées, un seul `<h1>`.
- Cohérence croisée : la clé de fragment correspond au HTML, le code de retour
  anti-spam a bien son message, une seule source de police, et l'ancienne fonction
  de préremplissage n'est plus ni définie ni appelée.

Ce qui n'a **pas** pu être vérifié : le comportement réel dans WordPress. Aucun de
ces contrôles ne remplace une commande de test sur le site. La liste des 6
vérifications à faire après le collage est dans `README.md`.

---

## À vérifier hors dépôt

- Versions et mises à jour de WordPress, WooCommerce, Astra, Rank Math, Code
  Snippets, WP Overnight PDF, YaySMTP ; extensions abandonnées.
- SPF, DKIM et DMARC sur `eau-service-events.fr`, et test réel de réception d'une
  demande de devis et d'une confirmation de commande.
- Réglages TVA WooCommerce (prix saisis HT ou TTC, taux appliqué aux frais) — lié
  à A4.
- La passerelle « Paiement à la livraison » est-elle bien active ? Les bandeaux du
  site l'annoncent à quatre endroits ; promettre une option introuvable est pire
  que se taire.
- `robots.txt`, sitemap, indexation réelle de `/boutique/`, couverture Search
  Console.
- Lighthouse mobile mesuré sur l'accueil, la boutique et une fiche produit.
- Sauvegardes automatiques et restauration testée.
- Bandeau cookies : présence et conformité si des traceurs sont chargés avant
  consentement.
- HTTPS forcé, en-têtes de sécurité, pare-feu applicatif.
