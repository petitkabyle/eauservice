# 🚀 OPTIMISATION SEO & IA - EAUSERVICE

## 📋 TABLE DES MATIÈRES
1. [Optimisation pour l'IA (AI Search)](#optimisation-ia)
2. [Annuaires et Plateformes Essentiels](#annuaires)
3. [Actions Prioritaires](#actions-prioritaires)
4. [Optimisations Techniques](#optimisations-techniques)

---

## 🤖 OPTIMISATION POUR L'IA (ChatGPT, Perplexity, Google AI, Bing Copilot)

### ✅ CE QUE VOUS AVEZ DÉJÀ (EXCELLENT !)
- ✅ Schema.org LocalBusiness complet
- ✅ Schema.org FAQPage sur la boutique
- ✅ Schema.org Product sur chaque fiche produit
- ✅ Contenu structuré avec FAQ détaillées
- ✅ Descriptions longues et contextuelles

### 🔥 À AJOUTER IMMÉDIATEMENT POUR L'IA

#### 1. **Organisation Schema** (donne plus de crédibilité)
À ajouter dans `snippet general` après le LocalBusiness :

```php
/* SCHEMA ORGANIZATION pour reconnaissance IA */
add_action( 'wp_head', 'eauservice_organization_schema', 8 );
function eauservice_organization_schema() {
	if ( ! is_front_page() ) { return; }
	$data = array(
		'@context' => 'https://schema.org',
		'@type' => 'Organization',
		'name' => 'EauService by Events Café',
		'legalName' => 'EauService',
		'url' => 'https://eau-service-events.fr',
		'logo' => 'https://eau-service-events.fr/wp-content/uploads/2024/12/logo-eauservice-blanc.png',
		'foundingDate' => '2004',
		'description' => 'Location de machines à café professionnelles, fontaines à eau et matériel événementiel sur la Côte d\'Azur pour salons, congrès et événements. Livraison sous 24h à Cannes, Nice, Monaco, Antibes.',
		'slogan' => 'Votre partenaire location événementielle sur la Côte d\'Azur',
		'telephone' => '+33-6-63-24-08-43',
		'email' => 'contact@eau-service-events.fr',
		'address' => array(
			'@type' => 'PostalAddress',
			'streetAddress' => '2 chemin des Frères Garberro',
			'addressLocality' => 'Antibes',
			'postalCode' => '06600',
			'addressCountry' => 'FR'
		),
		'geo' => array(
			'@type' => 'GeoCoordinates',
			'latitude' => '43.5847',
			'longitude' => '7.1250'
		),
		'areaServed' => array(
			array('@type' => 'GeoCircle', 'geoMidpoint' => array('@type' => 'GeoCoordinates', 'latitude' => '43.5847', 'longitude' => '7.1250'), 'geoRadius' => '100000'),
			array('@type' => 'City', 'name' => 'Cannes'),
			array('@type' => 'City', 'name' => 'Nice'),
			array('@type' => 'City', 'name' => 'Monaco'),
			array('@type' => 'City', 'name' => 'Antibes'),
			array('@type' => 'City', 'name' => 'Grasse'),
			array('@type' => 'City', 'name' => 'Menton'),
			array('@type' => 'City', 'name' => 'Saint-Tropez')
		),
		'serviceType' => array(
			'Location de machines à café professionnelles',
			'Location de fontaines à eau',
			'Location de matériel événementiel',
			'Livraison et installation sur site',
			'Location pour salons et congrès'
		),
		'priceRange' => '€€',
		'knowsAbout' => array(
			'location matériel événementiel',
			'machines à café Nespresso',
			'fontaines à eau professionnelles',
			'événementiel Côte d\'Azur',
			'salons professionnels',
			'congrès Monaco',
			'Palais des Festivals Cannes'
		),
		'sameAs' => array(
			'https://maps.app.goo.gl/FqH9hm9FLBDLpN6F6'
		),
		'contactPoint' => array(
			'@type' => 'ContactPoint',
			'telephone' => '+33-6-63-24-08-43',
			'contactType' => 'customer service',
			'areaServed' => 'FR',
			'availableLanguage' => array('French', 'English'),
			'contactOption' => 'TollFree'
		)
	);
	echo "\n<script type=\"application/ld+json\">" . wp_json_encode( $data, JSON_UNESCAPED_UNICODE | JSON_UNESCAPED_SLASHES ) . "</script>\n";
}
```

#### 2. **Service Schema** (pour chaque service)
À ajouter pour lister vos services :

```php
/* SCHEMA SERVICE pour chaque type de location */
add_action( 'wp_head', 'eauservice_service_schema', 9 );
function eauservice_service_schema() {
	if ( ! is_front_page() ) { return; }
	
	$services = array(
		array(
			'@type' => 'Service',
			'serviceType' => 'Location de machines à café professionnelles',
			'provider' => array('@type' => 'Organization', 'name' => 'EauService'),
			'areaServed' => array('@type' => 'GeoCircle', 'geoMidpoint' => array('@type' => 'GeoCoordinates', 'latitude' => '43.5847', 'longitude' => '7.1250'), 'geoRadius' => '100000'),
			'hasOfferCatalog' => array(
				'@type' => 'OfferCatalog',
				'name' => 'Machines à café',
				'itemListElement' => array(
					array('@type' => 'Offer', 'itemOffered' => array('@type' => 'Product', 'name' => 'Machine Nespresso professionnelle')),
					array('@type' => 'Offer', 'itemOffered' => array('@type' => 'Product', 'name' => 'Machine Lavazza professionnelle')),
					array('@type' => 'Offer', 'itemOffered' => array('@type' => 'Product', 'name' => 'Machine Covim professionnelle'))
				)
			),
			'offers' => array(
				'@type' => 'Offer',
				'priceSpecification' => array('@type' => 'UnitPriceSpecification', 'price' => '140', 'priceCurrency' => 'EUR', 'unitText' => 'par événement')
			)
		),
		array(
			'@type' => 'Service',
			'serviceType' => 'Location de fontaines à eau professionnelles',
			'provider' => array('@type' => 'Organization', 'name' => 'EauService'),
			'areaServed' => array('@type' => 'GeoCircle', 'geoMidpoint' => array('@type' => 'GeoCoordinates', 'latitude' => '43.5847', 'longitude' => '7.1250'), 'geoRadius' => '100000')
		),
		array(
			'@type' => 'Service',
			'serviceType' => 'Livraison et installation sur site événementiel',
			'provider' => array('@type' => 'Organization', 'name' => 'EauService'),
			'areaServed' => array('@type' => 'GeoCircle', 'geoMidpoint' => array('@type' => 'GeoCoordinates', 'latitude' => '43.5847', 'longitude' => '7.1250'), 'geoRadius' => '100000')
		)
	);
	
	foreach ( $services as $service ) {
		echo "\n<script type=\"application/ld+json\">" . wp_json_encode( $service, JSON_UNESCAPED_UNICODE | JSON_UNESCAPED_SLASHES ) . "</script>\n";
	}
}
```

#### 3. **Breadcrumb Schema** (aide l'IA à comprendre la structure)

```php
/* SCHEMA BREADCRUMB pour navigation IA */
add_action( 'wp_head', 'eauservice_breadcrumb_schema', 10 );
function eauservice_breadcrumb_schema() {
	if ( is_front_page() ) { return; }
	
	$items = array(
		array('@type' => 'ListItem', 'position' => 1, 'name' => 'Accueil', 'item' => home_url('/'))
	);
	
	if ( function_exists('is_shop') && is_shop() ) {
		$items[] = array('@type' => 'ListItem', 'position' => 2, 'name' => 'Boutique', 'item' => get_permalink( wc_get_page_id('shop') ));
	} elseif ( is_product() ) {
		global $post;
		$items[] = array('@type' => 'ListItem', 'position' => 2, 'name' => 'Boutique', 'item' => get_permalink( wc_get_page_id('shop') ));
		$items[] = array('@type' => 'ListItem', 'position' => 3, 'name' => get_the_title(), 'item' => get_permalink());
	}
	
	if ( count($items) > 1 ) {
		$data = array(
			'@context' => 'https://schema.org',
			'@type' => 'BreadcrumbList',
			'itemListElement' => $items
		);
		echo "\n<script type=\"application/ld+json\">" . wp_json_encode( $data, JSON_UNESCAPED_UNICODE | JSON_UNESCAPED_SLASHES ) . "</script>\n";
	}
}
```

---

## 📍 ANNUAIRES ET PLATEFORMES ESSENTIELS

### ✅ DÉJÀ INSCRITS
- ✅ Events Planner
- ✅ Pages Jaunes

### 🔥 PRIORITÉ ABSOLUE (GRATUITS)

#### **1. GOOGLE (CRUCIAL)**
- ✅ **Google Business Profile** (Google Maps + Search)
  - URL: https://business.google.com
  - 🎯 PRIORITÉ #1 - Apparaît dans 90% des recherches locales
  - Ajouter photos, horaires, services, zone de livraison
  - Publier des posts régulièrement (nouveaux packs, salons équipés)

- **Google Search Console**
  - URL: https://search.google.com/search-console
  - Soumettre votre sitemap XML

#### **2. MICROSOFT/BING**
- **Bing Places** (Microsoft + Bing Maps)
  - URL: https://www.bingplaces.com
  - 🎯 Important pour Bing Copilot (IA Microsoft)

#### **3. APPLE**
- **Apple Maps**
  - URL: https://mapsconnect.apple.com
  - Utilisé par Siri et Apple Plans

#### **4. RÉSEAUX SOCIAUX PRO (signaux pour l'IA)**
- **LinkedIn Company Page**
  - URL: https://www.linkedin.com/company/setup/new/
  - 🎯 Essentiel pour événementiel B2B
  - Publier vos références salons/congrès

- **Facebook Business Page**
  - URL: https://www.facebook.com/business
  - Ajouter services, horaires, photos

- **Instagram Business**
  - Photos de matériel installé sur salons
  - Stories des événements équipés

---

### 🎯 ANNUAIRES ÉVÉNEMENTIEL (VOTRE SECTEUR)

#### **PLATEFORMES ÉVÉNEMENTIELLES**
1. **Eventplanner.fr** ✅ (déjà fait)
   - URL: https://www.eventplanner.fr

2. **Alyze Event**
   - URL: https://www.alyze-event.com
   - Annuaire des prestataires événementiels

3. **France Congrès**
   - URL: https://www.france-congres.org
   - Réseau des villes de congrès

4. **UNIMEV** (Union Française des Métiers de l'Événement)
   - URL: https://www.unimev.fr
   - Référence pour les organisateurs de salons

5. **ProEvent.fr**
   - URL: https://www.proevent.fr
   - Marketplace événementielle

6. **EventWed**
   - URL: https://www.eventwed.com
   - Annuaire prestataires mariage + événements pro

7. **L'Officiel des Loisirs**
   - URL: https://www.officiel-loisirs.com
   - Catégorie "traiteurs et location de matériel"

---

### 📱 ANNUAIRES LOCAUX CÔTE D'AZUR

#### **GÉNÉRIQUES LOCAUX**
1. **PagesJaunes** ✅ (déjà fait)
   - URL: https://www.pagesjaunes.fr

2. **118000.fr**
   - URL: https://www.118000.fr
   - Annuaire téléphonique + coordonnées

3. **Yelp France**
   - URL: https://biz.yelp.fr
   - Avis clients + référencement local

4. **Justacote.com**
   - URL: https://www.justacote.com
   - Annuaire local géolocalisé

5. **Annuaire.laposte.fr**
   - URL: https://annuaire.laposte.fr/entreprise
   - Très bien indexé par Google

6. **Hoodspot**
   - URL: https://www.hoodspot.fr
   - Annuaire local communautaire

#### **SPÉCIFIQUE CÔTE D'AZUR**
7. **Côte d'Azur France (Office de Tourisme)**
   - URL: https://www.cotedazur-france.fr
   - Section Pro/Entreprises

8. **Nice-Côte d'Azur Métropole**
   - Annuaire des entreprises locales

9. **CCI Nice Côte d'Azur**
   - URL: https://www.cote-azur.cci.fr
   - Annuaire des adhérents (si membre)

---

### 🏢 ANNUAIRES B2B & PROFESSIONNELS

1. **Kompass**
   - URL: https://fr.kompass.com
   - Base de données B2B mondiale

2. **Europages**
   - URL: https://www.europages.fr
   - Annuaire B2B européen

3. **Verif.com**
   - URL: https://www.verif.com
   - Informations légales entreprise

4. **Societe.com**
   - URL: https://www.societe.com
   - Fiche entreprise (vérifier infos à jour)

5. **Infogreffe**
   - URL: https://www.infogreffe.fr
   - Données officielles (auto-rempli)

---

### 🎪 PLATEFORMES SALONS/CONGRÈS

1. **Palais des Festivals Cannes - Annuaire prestataires**
   - Contacter directement le Palais

2. **Grimaldi Forum Monaco - Prestataires agréés**
   - https://www.grimaldiforum.com

3. **Acropolis Nice - Liste prestataires**
   - https://www.nice-acropolis.com

4. **Exposalons.com**
   - Calendrier des salons + annuaire

---

### 🔧 PLATEFORMES SPÉCIALISÉES

1. **Trustpilot**
   - URL: https://fr.business.trustpilot.com
   - Avis clients (crédibilité IA)

2. **Capterra** (si vous développez un service SaaS)
   - Pour logiciel de gestion événements

3. **Waze Local**
   - URL: https://www.waze.com/business
   - Apparaître dans GPS Waze

4. **Foursquare for Business**
   - URL: https://business.foursquare.com
   - Utilisé par de nombreuses apps

---

### 📊 AGRÉGATEURS DE DONNÉES (automatique)

Ces plateformes agrègent les données d'autres sources :
1. **Axciom** - Consolidateur de données
2. **Acxiom InfoBase**
3. **Neustar Localeze**
4. **Factual** (appartient à Foursquare)

> ⚠️ **Important** : Assurez-vous que vos NAP (Name, Address, Phone) soient **IDENTIQUES PARTOUT** !

---

## 🚀 ACTIONS PRIORITAIRES (PAR ORDRE)

### SEMAINE 1 : Les Indispensables
1. ✅ **Google Business Profile** - FAIT ? Si non → URGENT
2. 📝 Ajouter les 3 nouveaux schemas (Organization, Service, Breadcrumb)
3. 📝 S'inscrire sur **Bing Places**
4. 📝 Créer page **LinkedIn Company**
5. 📝 S'inscrire sur **Yelp**

### SEMAINE 2 : Annuaires événementiels
6. 📝 Alyze Event
7. 📝 ProEvent.fr
8. 📝 Officiel des Loisirs
9. 📝 Contacter Palais des Festivals (liste prestataires)
10. 📝 Contacter Grimaldi Forum Monaco

### SEMAINE 3 : Annuaires locaux
11. 📝 118000.fr
12. 📝 Justacote.com
13. 📝 Annuaire La Poste
14. 📝 Hoodspot
15. 📝 Apple Maps

### SEMAINE 4 : Professionnels
16. 📝 Kompass
17. 📝 Europages
18. 📝 Trustpilot
19. 📝 Waze Local
20. 📝 Foursquare Business

---

## 🎯 OPTIMISATIONS TECHNIQUES SUPPLÉMENTAIRES

### 1. **Fichier robots.txt optimisé**
```
User-agent: *
Allow: /
Allow: /boutique/
Allow: /produit/

User-agent: GPTBot
Allow: /

User-agent: ChatGPT-User
Allow: /

User-agent: PerplexityBot
Allow: /

User-agent: Claude-Web
Allow: /

Sitemap: https://eau-service-events.fr/sitemap_index.xml
```

### 2. **Meta descriptions optimisées pour l'IA**
Sur chaque page, inclure :
```html
<meta name="description" content="EauService : location de machines à café Nespresso, fontaines à eau et matériel événementiel sur la Côte d'Azur. Livraison 24h à Cannes, Nice, Monaco, Antibes. Devis gratuit 06 63 24 08 43">
<meta name="keywords" content="location machine café Cannes, fontaine eau événement Nice, matériel congrès Monaco, location Nespresso salon, prestataire événementiel Côte Azur">
```

### 3. **Contenu enrichi pour l'IA**
Créer une page **"À propos"** détaillée avec :
- Votre histoire (depuis quelle année)
- Vos équipements (marques, quantités)
- Vos références (salons équipés)
- Votre zone de livraison précise
- Vos engagements (délai, qualité, SAV)

### 4. **Page FAQ enrichie**
Ajouter plus de questions (objectif : 20-30) :
- "Combien coûte la location d'une machine à café ?"
- "Livrez-vous au Palais des Festivals de Cannes ?"
- "Proposez-vous du café bio ?"
- "Quelle est votre zone de livraison ?"
- etc.

### 5. **Blog événementiel**
Publier 1 article/mois sur :
- "Comment organiser un espace café sur un salon"
- "Guide des salons 2026 sur la Côte d'Azur"
- "Machine Nespresso vs Lavazza : laquelle choisir ?"
- "Checklist matériel pour un congrès de 500 personnes"

→ L'IA recommande les sites avec du contenu frais et expert

---

## 📈 SUIVI & MESURE

### Outils gratuits à installer :
1. **Google Search Console** - Performance SEO
2. **Google Analytics 4** - Trafic et conversions
3. **Bing Webmaster Tools** - Performance Bing
4. **Google Business Profile Insights** - Recherches locales

### Métriques à suivre :
- Nombre d'impressions Google Business
- Clics vers le site depuis Google Maps
- Appels téléphoniques depuis Google
- Demandes d'itinéraire
- Mots-clés de recherche
- Taux de conversion devis

---

## 💡 BONUS : DEMANDER DES AVIS CLIENTS

L'IA privilégie les entreprises avec des **avis positifs récents**.

**Systématiser la demande :**
1. Après chaque événement → Email avec lien Google Review
2. Sur la page "Merci" après commande → Lien avis
3. Dans la signature email → "Satisfait ? Laissez un avis ici"

**Templates email :**
```
Bonjour [Prénom],

Nous espérons que notre matériel a contribué au succès de votre événement !

Si vous êtes satisfait(e) de notre service, nous serions ravis que vous partagiez votre expérience en quelques secondes :
👉 [Lien Google Review]

Merci pour votre confiance,
L'équipe EauService
```

---

## 🎯 RÉSUMÉ ACTIONS IMMÉDIATES

### À FAIRE CETTE SEMAINE :
1. ✅ Vérifier Google Business Profile (ou le créer)
2. 📝 Ajouter les 3 nouveaux schemas dans `snippet general`
3. 📝 S'inscrire Bing Places
4. 📝 Créer LinkedIn Company Page
5. 📝 Mettre à jour Pages Jaunes avec nouveau tél

### OBJECTIF 1 MOIS :
- 20 annuaires actifs minimum
- 10 avis Google minimum
- 3 articles de blog publiés
- Schemas complets sur le site

### RÉSULTAT ATTENDU (3-6 mois) :
- Apparition dans les réponses ChatGPT/Perplexity
- Top 3 Google Maps "location machine café Cannes"
- 50% du trafic depuis recherche locale
- +30% de demandes de devis

---

**Besoin d'aide pour implémenter les schemas ? Je peux les ajouter directement à vos fichiers !** 🚀
