# 🚚 CORRECTIF : Frais de livraison par code postal

## 🔴 PROBLÈME IDENTIFIÉ

L'ancien système de calcul des frais de livraison utilisait :
- Une formule mathématique complexe (distance en km via coordonnées GPS)
- Un géocodage via API externe (qui pouvait échouer)
- Des fourchettes de distance qui ne correspondaient PAS aux tarifs souhaités

**Résultat** : Les frais de livraison ne s'appliquaient pas correctement selon les villes.

---

## ✅ SOLUTION APPLIQUÉE

Remplacement complet du système par un **système de tarifs fixes par code postal**.

### Nouveau barème (modifiable facilement) :

| Ville / Zone | Codes postaux | Tarif |
|--------------|--------------|-------|
| **Antibes** | 06600, 06160 | **GRATUIT** ✅ |
| **Cannes** | 06400, 06150, 06110, 06210, 06220, 06590 | **30 €** |
| **Nice** | 06000, 06100, 06200, 06300 | **50 €** |
| **Monaco** | 98000, 98xxx | **50 €** |
| **Autres 06** | Tous les autres codes 06xxx | **80 €** |
| **Var** | Tous les codes 83xxx | **100 €** |
| **Autres dépts** | Hors 06/83 | **200 €** |

### Avantages :
✅ **Tarifs fixes et prévisibles** - plus de calculs hasardeux  
✅ **Reconnaissance intelligente** - par code postal ET par nom de ville  
✅ **Aucune dépendance externe** - pas d'API, pas de risque de panne  
✅ **Facilement modifiable** - tout est centralisé dans une seule fonction  
✅ **Affichage clair** - libellé personnalisé selon la zone  

---

## 📝 COMMENT MODIFIER LES TARIFS

Ouvrez le fichier `snippet general` et trouvez la fonction `eauservice_get_tarif_livraison()` (vers la ligne 950).

### Exemple : Ajouter Grasse à 40 €

```php
// ===== GRASSE = 40 € ===== (à ajouter après Cannes)
$codes_grasse = array( '06130', '06520' );
if ( in_array( $cp5, $codes_grasse, true ) ) {
    return array( 'montant' => 40, 'libelle' => 'Frais de livraison & installation (Grasse)' );
}
```

### Exemple : Modifier le tarif de Cannes (passer à 35 €)

Trouvez la ligne :
```php
return array( 'montant' => 30, 'libelle' => 'Frais de livraison & installation (Cannes)' );
```

Remplacez par :
```php
return array( 'montant' => 35, 'libelle' => 'Frais de livraison & installation (Cannes)' );
```

---

## 🧪 COMMENT TESTER

1. **Aller sur votre boutique** → ajouter un produit au panier
2. **Aller au panier** → cliquer sur "Commander"
3. **Remplir l'adresse de facturation** avec :
   - Code postal : `06600` → Doit afficher "Livraison offerte (Antibes)" à 0 €
   - Code postal : `06400` → Doit afficher "Frais... (Cannes)" à 30 €
   - Code postal : `06000` → Doit afficher "Frais... (Nice)" à 50 €
   - Code postal : `98000` → Doit afficher "Frais... (Monaco)" à 50 €
   - Code postal : `83000` → Doit afficher "Frais... (Var)" à 100 €
   - Code postal : `75001` → Doit afficher "Frais de livraison..." à 200 €

4. **Vérifier dans le récapitulatif de commande** que les frais s'affichent correctement

---

## 📂 FICHIER MODIFIÉ

- `snippet general` (Section 15 - Frais de livraison)

## ⚠️ IMPORTANT

- **Testez d'abord sur un environnement de test / staging**
- **Sauvegardez l'ancien code** si vous voulez pouvoir revenir en arrière
- **Videz le cache WooCommerce** après modification : WooCommerce → État → Outils → Vider les caches

---

## 🎯 PROCHAINES ÉTAPES

1. ✅ Copier le contenu du fichier `snippet general` corrigé
2. ✅ Coller dans Code Snippets (ou functions.php)
3. ✅ Tester avec différents codes postaux
4. ✅ Valider sur une vraie commande test
5. ✅ Commit + Push sur GitHub

---

**Fait par Kiro AI** 🤖  
Date : Décembre 2024
