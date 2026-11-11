# Krups café : porter le modèle Lagostina, pas sa plateforme

*État au 11 novembre 2026. Synthèse de cinq notes de recherche : avis d'experts, benchmark de plus de 30 sites de marque, audits directs de lagostina.fr/.it, de rowenta.fr/krups et de krups.de réalisés le 11/11/2026, contexte Groupe SEB et marché du café.*

## TL;DR

1. **Sur la grille construite (8 dimensions, 31 critères, notes 0–3), la boutique Krups France actuelle obtient environ 60/100 et lagostina.fr environ 47/100.** Lagostina gagne sur la performance du front, la marque et la liberté éditoriale ; Krups gagne sur le commerce (avis, vente guidée, paiement) et surtout sur les services (réparation, pièces, reconditionné). Ces scores sont un jugement de l'auteur sur des constats observés le 11/11/2026, pas une mesure.
2. **Lagostina n'est pas « le gabarit groupe » : c'est une pile à part** (Next.js 16 sur Vercel, Contentful, Adobe Commerce en SaaS, Adyen), la seule de ce type parmi une quarantaine de domaines Groupe SEB sondés. Elle est très récente (mise en ligne entre août et début novembre 2026), encore mal finie (localisation, accessibilité non auditée, aucun avis client affiché) et sans aucune donnée de performance commerciale.
3. **Krups France n'a plus de site propre** : krups.fr redirige vers un onglet de la boutique mutualisée rowenta.fr (Rowenta + Calor + Krups) sur SAP Commerce Cloud et un front Angular/Spartacus. En mesure labo, ce front charge environ 3 à 5 fois plus de JavaScript et bloque le thread principal 11 à 16 fois plus longtemps que Lagostina. Krups ne vend ni café en grains, ni capsules, ni abonnement.
4. **La portabilité est élevée pour la présentation et le contenu, moyenne pour le commerce, faible pour les services.** Ce qui fait la valeur d'un DTC café (consommables, réachat, enregistrement, SAV) n'existe pas dans le modèle Lagostina. Une réplication à l'identique ferait régresser Krups.
5. **Option recommandée : un hybride par étapes.** Corriger d'abord la boutique actuelle (gains sans regret), puis poser le front et le modèle de contenu Lagostina sur le commerce SAP existant via son API, et ne décider d'une bascule complète qu'après preuve. Le re-platforming direct est l'option la plus risquée ; le simple re-skin SAP ne règle ni le poids du front ni la liberté éditoriale.
6. **L'espace libre est le récurrent** : aucun fabricant ne propose d'abonnement grains en France au 11/11/2026, et personne n'automatise le réassort d'entretien. Les risques majeurs sont la relation avec Nestlé, le conflit de canaux (SEB a été sanctionné de 189,5 M€ en décembre 2024 pour prix imposés), l'économie faible du café pour un fabricant et le contexte de rationalisation du groupe.

## Comment lire ce rapport : niveaux de preuve

Chaque constat clé porte une étiquette.

| Étiquette | Signification |
|---|---|
| **[O]** | Observé directement le 11/11/2026 (code source, en-têtes HTTP, appels réseau, API publique, rendu Chromium en émulation mobile, sonde située aux États-Unis) |
| **[R]** | Rapporté par un tiers (étude, presse, communiqué, agrégateur) ; « extrait » signale une source lue seulement par extrait de moteur de recherche |
| **[I]** | Inféré : déduction ou jugement de l'auteur à partir de constats [O] ou [R] |

Trois limites conditionnent toute la lecture : aucune donnée terrain Core Web Vitals n'a pu être obtenue (API PageSpeed en erreur de quota, CrUX sans clé) ; l'étape de paiement n'a été testée sur aucun site ; les parts de marché par marque et la part du DTC ne sont pas publiées. La liste complète figure en fin de rapport.

## 1. Une grille de 8 dimensions et 31 critères, notée de 0 à 3

Aucune grille publique ne couvre les sites DTC de marques fabricantes de biens durables : le cadre Baymard note l'UX par type de page, Lighthouse note la technique, BCG décrit des capacités d'organisation, et la méthodologie Gartner Digital IQ est réservée aux clients. La grille ci-dessous est donc composée **[I]** à partir de ces sources et du benchmark de sites observés le 11/11/2026.

### 1.1 Règles de notation

1. Chaque critère reçoit une note : **0** = absent ou bloquant ; **1** = partiel ; **2** = conforme au seuil ; **3** = meilleur de classe.
2. Un critère non testable de l'extérieur est noté « n.t. » et exclu de la moyenne.
3. Score d'une dimension = moyenne des critères testés (sur 3).
4. Score global = Σ (poids × score de dimension) ÷ 3 × 100.
5. La rareté sert de lecture complémentaire : un critère échoué par 80 à 95 % des sites du benchmark Baymard différencie ; un critère échoué par moins de 25 % est un prérequis.
6. Mobile d'abord : le mobile pèse 69,9 % du trafic et le desktop convertit 74 % de plus ([Contentsquare, 09/03/2026](https://contentsquare.com/guides/digital-experience-benchmark/conversions/)) **[R]**.

### 1.2 Dimensions et pondération

| # | Dimension | Poids | Justification du poids |
|---|---|---|---|
| A | Découverte et navigation | 15 % | 61 % des sites desktop et 65 % des sites mobiles sont « médiocres ou pires » en navigation ([Baymard, 13/10/2026](https://baymard.com/blog/ecommerce-navigation-best-practice)) |
| B | Fiche produit et contenu | 15 % | 62 % des fiches mobiles sont « médiocres ou pires » ([Baymard, 18/03/2026](https://baymard.com/blog/current-state-ecommerce-product-page-ux)) |
| C | Panier, checkout, paiement | 15 % | Abandon moyen 70,22 % ; +35,26 % de conversion atteignable par le seul design du checkout ([Baymard, 28/10/2026](https://baymard.com/lists/cart-abandonment-rate)) |
| D | Performance et socle technique | 10 % | Seuils Core Web Vitals au 75e percentile ; 55,9 % des origines les passent ([CrUX, release de septembre 2026](https://developer.chrome.com/docs/crux/release-notes)) |
| E | SEO, données structurées, IA | 10 % | Exigences Google pour les fiches marchandes ; trafic référé par les IA en hausse de 632 % sur un an ([Contentsquare](https://contentsquare.com/guides/digital-experience-benchmark/conversions/)) |
| F | Conformité | 10 % | EAA applicable depuis le 28 juin 2025 ; 486,8 M€ de sanctions CNIL en 2025 ([CNIL, extrait](https://www.cnil.fr/en/sanctions-and-corrective-measures-cnils-actions-2025)) |
| G | Proposition de valeur DTC de marque | 10 % | 32 % des acheteurs visitent le site du fabricant, 1,6 % y achètent ([McKinsey, août 2023, extrait](https://www.mckinsey.com/industries/consumer-packaged-goods/our-insights/direct-to-consumer-e-commerce-in-appliances-a-strategic-growth-opportunity)) |
| H | Relation, services et récurrence | 15 % | Assortiment distinct et abonnement différencient le site de marque ([McKinsey, extrait](https://www.mckinsey.com/industries/consumer-packaged-goods/our-insights/should-cpg-manufacturers-go-direct-to-consumer-and-if-so-how)) ; c'est la dimension qui porte la valeur client sur un bien acheté rarement |

Les poids sont un choix de l'auteur **[I]** : aucune source publique ne pondère les dimensions G et H. Un test de sensibilité à poids égaux est fourni en section 3.

### 1.3 Critères, seuils, sources et meilleurs de classe

| # | Critère | Test et seuil | Source experte | Meilleur de classe observé (11/11/2026) |
|---|---|---|---|---|
| A1 | Menu et taxonomie | Catégories au 1er niveau du menu mobile ; environ 10 entrées au plus par niveau ; attributs partagés traités en filtres | Baymard navigation (13/10/2026) : 79 % des sites sur-catégorisent | [Le Creuset FR](https://www.lecreuset.fr/fr_FR/) : entrées par usage, couleur, budget cadeau |
| A2 | Filtres et tris de liste | 5 filtres (prix, note, couleur, taille ou capacité, gamme) et 4 tris (prix, note, meilleures ventes, nouveautés) | [Baymard listes (09/09/2025)](https://baymard.com/blog/current-state-product-list-and-filtering) : filtres incomplets 51 %, tris incomplets 69 % | [De'Longhi FR](https://www.delonghi.com/fr-fr/c/cafe/machines-a-cafe/expresso-broyeurs) : filtres par usage (mousseur automatique, cold brew) |
| A3 | Recherche interne | Autocomplétion, résultats pertinents, aucun produit invendable remonté | [Baymard search](https://baymard.com/research/ecommerce-search) : 56 % des sites insuffisants (extrait) | [Tefal FR](https://www.tefal.fr/) : moteur Luigi's Box |
| A4 | Vente guidée | Quiz et comparateur accessibles depuis la liste ; conseil humain disponible | Benchmark : quiz chez Caraway, Made In, Tefal, Dyson | [De'Longhi FR](https://www.delonghi.com/fr-fr/e/help-me-choose-intro) (quiz récompensé + comparateur) ; [Jura FR](https://fr.jura.com/fr/conseil-achat/live-video-help) (conseil vidéo) |
| B1 | Bloc d'achat | 8 éléments NN/g ; coût total et livraison estimés près du bouton ; lien retours dans la zone d'achat | [NN/g (24/11/2019)](https://www.nngroup.com/articles/ecommerce-product-pages/) ; Baymard fiche : 67 % sans coût total, 44 % sans politique de retour | [KitchenAid FR](https://www.kitchenaid.fr/blenders/859711727180/blender-pure-power-5ksb2073-noir-reglisse) : stock chiffré, date de livraison avec compte à rebours |
| B2 | Avis et questions-réponses | Note et volume près du titre ; plateforme tierce ; réponses de la marque | Baymard fiche : 89 % des sites ne répondent pas aux avis négatifs ; 14 fiches sur 14 du benchmark affichent des avis tiers | [Our Place](https://fromourplace.co.uk/products/always-essential-cooking-pan) : 33 846 avis et résumé par IA ; [Zwilling](https://www.zwilling.com/fr/staub-la-cocotte-cocotte-28-cm-ronde-noir-mat-fonte-40500-281-0/40500-281-0.html) : questions-réponses |
| B3 | Médias et caractéristiques | Au moins 3 visuels, vidéo, image à l'échelle, caractéristiques structurées, contenu de la boîte, notice | Baymard fiche : 37 % sans image à l'échelle | [SharkNinja FR](https://www.sharkninja.fr/air-fryer-en-verre-crispi-pro-7-en-1-bleu-cyberspace/AS101EUCY.html) : 23 vidéos, onglet comparaison |
| B4 | Lots et ventes associées | Sets avec économie visible et accessoires ajoutables dans le bloc d'achat | Benchmark : présent chez 7 acteurs | [Ooni](https://eu.ooni.com/fr-fr/products/ooni-karu-2-pro) : pack composable avec total dynamique |
| C1 | Commande invitée | Option invité la plus visible ; création de compte proposée après confirmation | [Baymard checkout (03/11/2026)](https://baymard.com/blog/current-state-of-checkout-ux) : 47 % et 56 % d'échec | Non observé (tunnels du benchmark non testés) |
| C2 | Formulaire | 12 à 14 éléments, dont 7 à 8 champs ; téléphone justifié ; saisie d'adresse assistée | Baymard : moyenne 23,48 éléments et 14,88 champs | Non observé |
| C3 | Transparence coût et délai | Coût total dès le panier ; date de livraison plutôt qu'un délai ; seuil de franco affiché | Baymard : coûts additionnels = 40 % des abandons ; 48 % des sites affichent un délai au lieu d'une date | KitchenAid FR : « Commandez dans 10 heures, 31 min pour recevoir votre commande le 14 novembre 2026 » |
| C4 | Moyens de paiement | Carte, Apple Pay et Google Pay, PayPal, paiement fractionné | Baymard : 9 % d'abandons faute de moyens, 19 % par défiance ; 11 fiches sur 14 affichent le fractionné | [Jura FR](https://fr.jura.com/fr) : Visa, Mastercard, Amex, Apple Pay, Google Pay, PayPal, Klarna |
| D1 | Core Web Vitals terrain | p75 mobile : LCP ≤ 2,5 s, INP ≤ 200 ms, CLS ≤ 0,1 | [web.dev](https://web.dev/articles/vitals) | Origines Shopify : 76 % passent sur mobile ([Web Almanac 2025](https://almanac.httparchive.org/en/2025/ecommerce)) |
| D2 | Chargement en labo bridé | LCP ≤ 2,5 s et CLS ≤ 0,1 sur accueil, liste, fiche (seuils web.dev appliqués par analogie) ; temps de blocage comparé | web.dev | Non mesuré sur le panel |
| D3 | Poids JavaScript et tiers | Poids JS transféré et nombre d'hôtes tiers, comparés au panel (8 à 19 hôtes pour les plus sobres, 76 à 148 pour les plus chargés) | Benchmark du 11/11/2026 (indicatif) | KitchenAid FR (8 hôtes) ; [Caraway](https://www.carawayhome.com/products/cookware-sets) (13 hôtes) |
| D4 | Architecture et cache | Rendu serveur, HTML caché en bordure, images en formats modernes, vidéos transcodées | [Google](https://developers.google.com/search/docs/appearance/structured-data/merchant-listing) : balisage dans le HTML initial | [Made In](https://madeincookware.com/products/stainless-steel-frying-pan) : Next.js avec cache Vercel |
| E1 | Balisage produit | `Product` + `Offer` (prix, devise, disponibilité), GTIN, marque, `aggregateRating`, `shippingDetails`, `hasMerchantReturnPolicy`, dans le HTML servi | Google, fiches marchandes | Caraway : `ProductGroup`, `FAQPage`, `Brand`, `OnlineStore` |
| E2 | Balisage site | `Organization`, `WebSite`, `BreadcrumbList` valide, `ItemList` en liste, `FAQPage` | Google | [Tefal FR](https://www.tefal.fr/p/ingenio-excellence-ceramic-poeles-poignee-amovible-ceramique-induction-2428cm/2100143009) : `Product`, `VideoObject`, `FAQPage`, `ItemList` |
| E3 | International et canonical | hreflang réciproque avec `x-default` ; canonical auto-référent ; domaine de marque | [Google, versions localisées](https://developers.google.com/search/docs/specialty/international/localized-versions) (extrait) | Le Creuset (22–23 hreflang) ; Ooni (45–52) |
| E4 | Crawl, facettes, lisibilité IA | Politique de facettes cohérente ; sitemap propre ; contenu clé en texte ; FAQ | [Google, fonctionnalités IA](https://developers.google.com/search/docs/appearance/ai-features) : aucun fichier ni balisage spécial requis | Caraway, Ooni (FAQ balisée) |
| F1 | Accessibilité | Déclaration publiée ; audit WCAG 2.2 AA ; pas de violation critique aux tests automatiques | EAA applicable depuis le 28/06/2025 ([De Gaulle Fleurance](https://www.degaullefleurance.com/en/actualites/digital-accessibility-new-obligations-for-the-private-sector/)) | Aucun acteur du panel ne démontre un audit |
| F2 | Consentement | « Tout refuser » au 1er niveau et de même poids ; aucun traceur non exempté avant choix ; Consent Mode v2 à « denied » par défaut | CNIL : Fnac Darty 7 M€ le 22/10/2026 ([extrait](https://cnil.fr/en/node/169095)) | Lagostina.fr (voir section 2) |
| F3 | Affichages légaux | Prix le plus bas des 30 jours ; éco-participation ; caractéristiques environnementales ; CGV exactes | [DGCCRF](https://www.economie.gouv.fr/dgccrf/les-fiches-pratiques/annonces-de-reduction-de-prix-ce-que-vous-devez-savoir) (extrait) | Tefal FR : prix le plus bas 30 jours affiché |
| G1 | Raison d'acheter en direct | Avantage réservé au canal de marque, explicité sur la fiche | McKinsey (extrait) ; [BCG (16/11/2021)](https://www.bcg.com/publications/2021/direct-to-consumer-strategy-business-benefits) | Ooni : garantie 5 ans « uniquement sur ooni.com » |
| G2 | Offre différenciée | Lots web, éditions limitées, personnalisation, reconditionné | BCG : exclusivités en ligne, formats adaptés | [Tefal My Ingenio](https://www.tefal.fr/myingenio) ; KitchenAid Artisan By You |
| G3 | Cohérence de prix | Pas de sous-enchère systématique ; cohérence neuf/reconditionné et entre pays | McKinsey : conflit de canaux, 1re barrière | [Dyson](https://www.dyson.fr/aspirateurs/a-main/car-boat/argent), SharkNinja : garantie de prix |
| G4 | Autonomie et cohérence de marque | Domaine propre ; mentions, métadonnées et ton purs de la marque ; récit | BCG : contrôle de la vitrine | Le Creuset : couleur, savoir-faire, garantie à vie |
| H1 | Captation first-party | Incitation newsletter ; compte ; enregistrement produit récompensé | Benchmark : remise de bienvenue d'environ 10 % quasi universelle | [Melitta FR](https://www.melitta.fr/enregistrement-d-une-machine) : −10 % à l'enregistrement ; [Ooni](https://eu.ooni.com/fr-fr/pages/ooni-rewards) : 5 € |
| H2 | SAV, réparation, pièces | Réparation hors garantie tarifée ; pièces recherchables par appareil ; notices ; contact en direct | Cadre français : bonus réparation, droit à la réparation ([CEC France](https://www.europe-consommateurs.eu/en/topics/guarantees/after-purchase/repair/)) | Krups et Tefal (voir section 3) ; [Dyson](https://www.dyson.fr/produits-reconditionnes) |
| H3 | Consommables et récurrence | Consommables compatibles par machine ; abonnement modifiable ; résiliation en ligne | [Recharge (juillet 2026)](https://getrecharge.com/?p=27190) ; décret 2023-417 ([Deloitte Avocats](https://blog.avocats.deloitte.fr/la-resiliation-en-3-clics-nouvel-outil-au-service-des-consommateurs/?amp=1), extrait) | [Nespresso](https://www.nespresso.com/fr/fr/subscription) ; [De'Longhi DE](https://www.delonghi.com/de-de/e/promo/kaffee-abonnement) ; [Beanz](https://www.beanz.com/en-gb) |
| H4 | Après-achat et circularité | Portail de retours ; reconditionné ; contenus de prise en main liés au produit | Benchmark : différenciateur des industriels européens | [Sage FR](https://www.sageappliances.com/fr-fr) (MySage) ; Dyson (reconditionné, MyDyson) |

Le benchmark fixe aussi sept prérequis présents chez la grande majorité des 14 fiches produit rendues : bande de réassurance près du bouton (11 sur 14), avis tiers avec volume (14 sur 14), paiement fractionné dès la fiche (11 sur 14), incitation de bienvenue, lots avec économie visible, contenu d'usage relié au produit, outillage d'optimisation (CMP, test A/B, analytics comportemental). La 3D et la réalité augmentée ne sont observées sur aucune des 14 fiches **[O]**.

## 2. Lagostina : un DTC premium rapide en façade, encore inachevé (47/100)

### 2.1 Modèle business

| Élément | Constat | Preuve |
|---|---|---|
| Nature | DTC transactionnel en France et en Italie : panier, commande invitée, tunnel en 3 étapes ([panier](https://www.lagostina.fr/cart)) | [O] |
| Vendeur | Groupe SEB France SAS (France), Groupe SEB Italia S.p.A. (Italie) ; pièces et accessoires par SEB International Service ([mentions légales](https://www.lagostina.fr/blog/mentions-legales)) | [O] |
| Catalogue | 258 URL produit en France, 382 en Italie ; 11 catégories dont « Machines à café » (2 cafetières Moka à 114,99 € et 129,99 €) ([catégorie](https://www.lagostina.fr/categories-produits/machines-a-cafe)) | [O] |
| Leviers | 19 « sets exclusifs web » (lots virtuels), éditions limitées (Saltapasta Zanoni, 400 pièces), « Dernière chance », Black Friday jusqu'à −40 % ([sets](https://www.lagostina.fr/categories-produits/sets-exclusifs-web)) | [O] |
| Livraison et retours | Franco à 99 € (79 € en Italie), sinon 9,90 € ; FedEx ; retours gratuits 30 jours ; Corse et Monaco exclus ([CGV](https://www.lagostina.fr/blog/conditions-generales-de-vente)) | [O] |
| Garantie | 25 ans sur l'inox fabriqué en Italie, 10 ans hors UE, 5 ans sur certains antiadhésifs ([garantie](https://www.lagostina.fr/blog/garantie-commerciale)) | [O] |
| Prix | Parité avec Galeries Lafayette sur la poêle Accademia 20 cm (149,99 €) ; plus cher et en rupture sur l'autocuiseur Mia (145 € contre 106 à 107,50 €) ; échantillon de 4 produits | [O] site, [R] distributeurs |
| Résultats | Aucun chiffre public (chiffre d'affaires, conversion, panier) | Lacune |

L'Italie est plus mature que la France : pièces détachées en ligne, 480 recettes contre 23, newsletter à −10 %, affiliation **[O]**. Les avis de boutique sont contrastés : 4,2/5 sur 75 avis sollicités depuis le 13/08/2026 ([Avis Vérifiés](https://www.avis-verifies.com/avis-clients/lagostina.fr)) mais 1,3/5 sur 49 avis non sollicités, centrés sur le SAV ([Trustpilot](https://fr.trustpilot.com/review/lagostina.fr)) **[R]**.

### 2.2 Modèle UX

1. **Gabarit** mobile d'abord, sobre et cohérent : rouge profond `#b7170b`, fonds crème, titres en capitales condensées ([accueil](https://www.lagostina.fr/)) **[O]**.
2. **Fiche produit riche** : 15 médias dont une vidéo, variantes de taille, 2 accessoires cochables, caractéristiques, « ce qui est inclus », barre d'achat collante ([fiche Saltapasta](https://www.lagostina.fr/produits/saltapasta-salvaspazio-26-cm/8910000170)) **[O]**.
3. **Manques nets** : aucun avis ni question-réponse, pas de comparateur, liste d'envies en erreur 404, pas de chat, pas d'alerte de retour en stock, pas d'enregistrement produit, pas de bloc newsletter sur l'accueil français **[O]**.
4. **Finition** : libellés italiens et anglais sur le site français (« 15 RISULTATI », « Nessun filtro disponibile »), codes PIM bruts dans les caractéristiques (« Oven compatibility -7051_1 »), titre « Moulinex » sur le localisateur de magasins, CGV françaises citant « shop.lagostina.it », concours expiré depuis le 15 juin encore en ligne **[O]**.
5. **Disponibilité** : 6 autocuiseurs sur 10 en rupture en plein Black Friday ([catégorie](https://www.lagostina.fr/categories-produits/autocuiseurs)) **[O]**.

### 2.3 Modèle technique

| Couche | Lagostina FR/IT | Preuve |
|---|---|---|
| Front | Next.js 16.4.2 (App Router, composants serveur en streaming), React 19.3 canary, Tailwind 4.3 ; service nommé `lagostina-nwp` ([api/health](https://www.lagostina.fr/api/health)) | [O] |
| Hébergement | Vercel ; déploiements distincts France et Italie ; HTML en `no-store`, `x-vercel-cache: MISS` sur toutes les pages | [O] |
| CMS | Contentful (CSP `frame-ancestors … contentful.com`, endpoints `/api/preview/` et `/api/revalidate/` dans [robots.txt](https://www.lagostina.fr/robots.txt)) | [O] |
| Commerce | Adobe Commerce en SaaS (médias `eu1-static.api.commerce.adobe.com/<tenant>/`, événements `commerce.adobedc.net`, panier de forme Magento GraphQL) | [O] ; offre « as a Cloud Service » [I] |
| Couche d'intégration | Le navigateur n'appelle que des routes `/api/*` du même domaine (panier, checkout, recherche, recommandations, compte) | [O] |
| Paiement | Composant Adyen Checkout dans le bundle ; libellés prévus pour PayPal, Klarna, Alma, Oney, Scalapay ; CGV : carte et Klarna | [O] code ; étape non affichée |
| Thème | Jetons de design en variables CSS sémantiques ; routes internes génériques (`/products/[slug]/[sku]`) localisées par réécriture | [O] |
| Briques groupe | DAM `dam.groupeseb.com` + TwicPics, OneTrust, identité et formulaire Salesforce, plateforme recettes, Insider, Dynatrace, Uberall, portail de retours | [O] |
| Absents | Outil de test A/B, avis, chat | [O] |

Parmi une quarantaine de domaines de marques du groupe sondés, seuls lagostina.fr et lagostina.it répondent `server: Vercel` **[O]**. L'intégrateur, le coût et l'intention du groupe (pilote à généraliser ou exception premium) ne sont documentés nulle part.

La performance de premier rendu est bonne, puis se dégrade pour trois raisons corrigeables sans refonte **[O]** :

1. Le marketing tiers : le JavaScript passe de 545 Ko avant consentement à 1,85 Mo après « Tout accepter » (247 requêtes, 23 hôtes).
2. Les vidéos servies brutes depuis le DAM : 9,9 Mo sur la fiche Saltapasta, 84,9 Mo sur la page « Made in Italy ».
3. La bannière de consentement illustrée (image de 157 Ko) qui devient l'élément LCP à la première visite : 7,0 à 9,4 s sur liste et fiche en labo bridé, contre 1,4 s sur l'accueil.

### 2.4 Scores Lagostina.fr

Tous les scores sont **[I]**, fondés sur les constats **[O]** de la colonne de droite.

| # | Critère | Note | Preuve |
|---|---|---|---|
| A1 | Menu et taxonomie | 2 | 11 catégories, collections, promotions ; deux arborescences parallèles (`/categories-produits` et `/categories`) |
| A2 | Filtres et tris | 1 | Pas de filtre prix ; valeurs en doublon (« FIXE » et « Poignée fixe ») ; panneau vide en italien sur Autocuiseurs ([liste Poêles](https://www.lagostina.fr/categories-produits/poeles)) |
| A3 | Recherche | 1 | Suggestions présentes ; compteur en italien ; produits à prix 0 remontés par l'[API](https://www.lagostina.fr/api/search?q=autocuiseur) |
| A4 | Vente guidée | 1 | Un guide « Quelle collection choisir ? » ; ni quiz ni comparateur |
| B1 | Bloc d'achat | 2 | Prix, Klarna, réassurance, barre collante ; simple libellé « rupture » |
| B2 | Avis | 0 | Aucun script d'avis, aucun `AggregateRating` |
| B3 | Médias et caractéristiques | 2 | 15 médias, vidéo, « ce qui est inclus » ; codes PIM visibles |
| B4 | Lots et ventes associées | 2 | Accessoires cochables, « à associer avec », 19 sets ; unités de recommandation vides |
| C1 | Commande invitée | 2 | Deux boutons au panier, connexion en premier ([checkout](https://www.lagostina.fr/checkout/information)) |
| C2 | Formulaire | 2 | 9 champs contre 7 à 8 visés ; téléphone obligatoire ; pas de saisie d'adresse assistée |
| C3 | Coût et délai | 1 | Trois délais contradictoires (2 à 7 jours, 4 jours, 7 jours) ; pas de date |
| C4 | Paiement | 1 | Carte et Klarna selon les CGV ; PayPal et portefeuilles non confirmés (à revalider) |
| D1 | CWV terrain | n.t. | API en erreur de quota |
| D2 | Labo bridé | 2 | Accueil LCP 1,42 s ; liste 9,4 s et fiche 7,0 s (bannière) ; CLS ≤ 0,044 ; blocage 372 à 479 ms |
| D3 | Poids JS et tiers | 2 | 0,53 Mo de JS avant consentement, 1,85 Mo après ; 23 hôtes |
| D4 | Architecture et cache | 1 | Rendu serveur et TwicPics, mais HTML jamais caché et vidéos brutes |
| E1 | Balisage produit | 2 | `Product`, `Offer`, EAN, `ProductGroup`, `VideoObject` ; ni note, ni livraison, ni retours |
| E2 | Balisage site | 2 | `Organization`, `WebSite`, `ItemList`, `FAQPage`, `Recipe` ; logo et réseau social italiens sur le site français ; fil d'Ariane à 2 niveaux |
| E3 | International et canonical | 1 | hreflang complet sur l'accueil seulement ; canonical recopiant n'importe quel slug ([test](https://www.lagostina.it/prodotti/x/8900547841)) |
| E4 | Crawl et facettes | 1 | Facettes à la fois bloquées et en `noindex` ; routes de debug listées ; `lastmod` uniforme ; meta description avec HTML brut |
| F1 | Accessibilité | 1 | [Déclaration du 03/11/2026](https://www.lagostina.fr/blog/accessibilite) : « pas encore audité », audit prévu en novembre 2026 ; violations `target-size` et focus au test axe |
| F2 | Consentement | 2 | Trois boutons au 1er niveau (accepter mis en avant) ; aucun traceur tiers avant choix ; Consent Mode v2 à « denied » |
| F3 | Affichages légaux | 1 | CGV citant le mauvais site ; délais contradictoires ; caractéristiques environnementales présentes |
| G1 | Raison d'acheter en direct | 2 | Sets et éditions exclusifs ; pas de bloc « pourquoi acheter ici » |
| G2 | Offre différenciée | 2 | Lots virtuels, éditions numérotées ; ni personnalisation ni reconditionné |
| G3 | Cohérence de prix | 2 | Suit le prix conseillé, décroche en promotion ; écarts France/Italie (Diva 3,5 L : 179,99 € contre 129,00 €) |
| G4 | Marque | 2 | Domaine propre, récit 1901 et Made in Italy, chef ambassadeur ; reliquats (« 120 ans », titre Moulinex) |
| H1 | Captation first-party | 1 | Compte Salesforce ; pas de newsletter visible en France, pas d'enregistrement, pas de fidélité |
| H2 | SAV et pièces | 1 | [Annuaire de réparateurs](https://www.lagostina.fr/services/repairer-locator), FAQ de 16 questions ; pas de pièces en ligne en France, pas de chat |
| H3 | Consommables et récurrence | 0 | Aucune offre |
| H4 | Après-achat et circularité | 1 | Portail de retours gratuit ; ni reconditionné ni prise en main |

Calcul par dimension, puis global :

| Dimension | Notes | Moyenne | Poids | Contribution |
|---|---|---|---|---|
| A | 2, 1, 1, 1 | 5 ÷ 4 = 1,25 | 0,15 | 0,1875 |
| B | 2, 0, 2, 2 | 6 ÷ 4 = 1,50 | 0,15 | 0,2250 |
| C | 2, 2, 1, 1 | 6 ÷ 4 = 1,50 | 0,15 | 0,2250 |
| D | 2, 2, 1 | 5 ÷ 3 = 1,67 | 0,10 | 0,1667 |
| E | 2, 2, 1, 1 | 6 ÷ 4 = 1,50 | 0,10 | 0,1500 |
| F | 1, 2, 1 | 4 ÷ 3 = 1,33 | 0,10 | 0,1333 |
| G | 2, 2, 2, 2 | 8 ÷ 4 = 2,00 | 0,10 | 0,2000 |
| H | 1, 1, 0, 1 | 3 ÷ 4 = 0,75 | 0,15 | 0,1125 |
| **Total** | | | 1,00 | **1,40 sur 3 → 1,40 ÷ 3 × 100 = 46,7** |

## 3. Krups part d'une boutique mutualisée plus complète mais plus lourde (60/100)

### 3.1 Point de départ pays par pays

| Pays | Situation au 11/11/2026 | Plateforme | Preuve |
|---|---|---|---|
| France | [krups.fr](https://www.krups.fr/) → redirection 301 vers [rowenta.fr/krups](https://www.rowenta.fr/krups), onglet d'une boutique à trois marques (Rowenta, Calor, Krups), panier commun | SAP Commerce Cloud + front Angular 21 de type Spartacus, derrière Cloudflare | [O] |
| Allemagne | [krups.de](https://www.krups.de/), boutique autonome, héberge aussi Silit et Kaiser ; widget « où acheter » en complément | Même gabarit | [O] |
| Pays-Bas, Autriche | Boutiques autonomes transactionnelles, Klarna | Même gabarit | [O] |
| Royaume-Uni, Italie, Belgique, Portugal, États-Unis et autres | Site de marque avec « où acheter » ; seuls les accessoires sont vendus en direct | Ancien front SAP en JSP | [O] |
| Espagne | « Tienda Krups » renvoie vers la boutique Moulinex | Coin de marque | [O] |
| Canada, Australie, Brésil | Vitrine PHP, Shopify, VTEX (arno.com.br) | Piles locales | [O] |

La configuration renvoyée par l'API publique confirme le modèle français : `name: "Rowenta FR Store"`, `houseOfBrandConfig.brand = [ROWENTA, CALOR, KRUPS]`, `isDirectToConsumer: true`, `activateGuestCheckout: true` ([API](https://brandsites.api.groupe-seb.com/occ/v2/Rowenta-FR/basestores/Rowenta-FR?fields=FULL)) **[O]**. La bascule de krups.fr vers rowenta.fr date d'entre le 10/09/2025 et début 2026 **[I]**. L'ancienne boutique `Krups-FR` répond encore dans l'API **[O]**.

Le groupe va donc, pour Krups France, dans le sens inverse d'un site de marque autonome : il mutualise trafic, panier et coûts **[I]**. Le vendeur légal est SEB International Services en France comme en Allemagne ([CGV](https://www.rowenta.fr/conditions-de-ventes)), et non Groupe SEB France comme chez Lagostina **[O]**.

### 3.2 Offre et prix

| Élément | Constat (France sauf mention) | Preuve |
|---|---|---|
| Machines à grains | 21 références, 15 en stock, de 259,99 € (Intensity) à 999,99 € (Intuition Expérience+) ; Coffee Crush à 299,99 et 349,99 € ([API catalogue](https://brandsites.api.groupe-seb.com/occ/v2/Rowenta-FR/products/search?query=:relevance:allCategories:krups-expresso-avec-broyeur-a-grain&pageSize=100&fields=FULL&lang=fr_fr&curr=EUR&brand=krups)) | [O] |
| Nespresso | 3 machines vendues en direct (69,99 à 399,99 €) | [O] |
| Dolce Gusto | Pas de bouton panier : renvoi « Acheter sur le site Dolce Gusto », alors que l'API indique le produit achetable et 68 en stock ; vendues en direct en Allemagne | [O] |
| Café | Aucun café en grains, aucune capsule ; « Autour du café » = 4 produits | [O] |
| Entretien et pièces | Cartouche Claris 10,99 € ; pastilles, kit d'entretien et détartrant indisponibles le 11/11/2026 ; 18 accessoires et pièces rattachés à l'Intuition Préférence+ ([boutique accessoires](https://www.rowenta.fr/krups/boutique-accessoires/home)) | [O] |
| Reconditionné | 17 références Krups (199,99 à 769,99 €) | [O] |
| Lots web | Coffee Crush Experience + pot à lait à 349,99 € au lieu de 365,98 € ([packs](https://www.rowenta.fr/krups/packs-exclusifs-web)) | [O] |
| Services payants | [Forfaits de réparation](https://www.rowenta.fr/krups/forfait-reparation) hors garantie de 30,49 € à 169,99 € ; extension de garantie Evy dès 9,99 € | [O] |
| Livraison, retours | Franco dès 50 €, 3 à 5 jours ouvrés ; rétractation 30 jours, retour commercial jusqu'à 90 jours | [O] |
| Paiement | Carte, PayPal, Apple Pay, Google Pay, Oney 3x/4x/12x ; Klarna et Amex en Allemagne | [O] CGV |
| Flux logistiques | Deux entrepôts et deux politiques de retour : produits (prestataire PFS) et accessoires (SIS, retours à la charge du client) | [O] |

Le positionnement de prix face aux enseignes n'est ni neutre ni systématiquement agressif. Prix boutique **[O]**, prix enseignes **[R]** via l'agrégateur Bonne-Promo daté du 11/11/2026 :

| Modèle | Boutique Krups | Enseignes | Places de marché | Lecture |
|---|---|---|---|---|
| [Coffee Crush Experience SA403BE0](https://www.bonne-promo.com/prix/24243/krups-sa403be0-coffee-crush-experience-expresso-broyeur) | 349,99 | Boulanger, Fnac, Darty : 349,99 | 335,57–342,29 | Parité |
| [Intuition Experience Hot&Cold EA879NE0](https://www.bonne-promo.com/prix/23754/krups-ea879ne0-intuition-experience-expresso-broyeur) | 999,99 | Boulanger, Darty, Fnac : 999,99 | 1 003,86–1 078,15 | Parité |
| [Intensity EA5008](https://www.bonne-promo.com/prix/23807/krups-intensity-ea5008-noir-expresso-broyeur) | 259,99 | 299,99 | 281,27–293,14 | Direct 40 € moins cher |
| [Intensity Lait EA5028](https://www.bonne-promo.com/prix/23806/krups-intensity-lait-ea5028-noir-expresso-broyeur) | 379,99 | Darty, Fnac : 349,99 | 323,75–347,09 | Direct 30 € plus cher |
| [Intuition Préférence+ EA875E10](https://www.bonne-promo.com/prix/15150/krups-ea875e10-expresso-broyeur) | 599,99 | Boulanger : 899,99 | 525,00–676,60 | Direct 300 € moins cher |
| [Evidence Noire EA897B10](https://www.bonne-promo.com/prix/7778/expresso-broyeur-krups-ea897b10-evidence-eco-design) | 649,99 | Conforama : 520,40 | 459,00–518,00 | Direct 130 à 191 € plus cher |

Deux incohérences internes s'y ajoutent **[O]** :

1. Reconditionné plus cher que le neuf : l'Intuition Préférence+ reconditionnée est affichée 699,99 € (réf. -RA) quand le neuf est à 599,99 €.
2. Écarts France/Allemagne sur le même SKU ([catalogue allemand](https://brandsites.api.groupe-seb.com/occ/v2/Krups-DE/products/search?query=:relevance:allCategories:kaffeevollautomaten&pageSize=100&fields=FULL&lang=de&curr=EUR)) :

| SKU | France | Allemagne | Écart | Calcul |
|---|---|---|---|---|
| EA875E10 | 599,99 | 1 119,99 | Allemagne +520,00 € | 520,00 ÷ 599,99 = +87 % |
| EA877D10 | 999,99 | 669,99 | France +330,00 € | 330,00 ÷ 669,99 = +49 % |
| EA897B10 | 649,99 | 399,99 | France +250,00 € | 250,00 ÷ 399,99 = +63 % |

### 3.3 Stack et performance, côte à côte avec Lagostina

| Couche | Krups FR/DE (gabarit groupe) | Lagostina FR/IT |
|---|---|---|
| Commerce | SAP Commerce Cloud, API `brandsites.api.groupe-seb.com/occ/v2/` | Adobe Commerce SaaS |
| Front | Angular 21 en rendu serveur (`cx-storefront`, composants `wc-*`), bundle principal de 1,45 Mo non compressé en 78 morceaux | Next.js 16, composants serveur |
| CMS | CMS SAP (SmartEdit) | Contentful |
| Cache HTML | Cloudflare, `cf-cache-status: HIT` | Aucun (`no-store`) |
| Recherche | Luigi's Box (France) | Recherche Adobe probable [I] |
| Avis | Skeepers, syndiqués entre pays | Aucun affiché |
| Test A/B | Kameleoon | Aucun |
| Consentement et balises | Commanders Act (France), OneTrust (Allemagne) | OneTrust, GTM avec passerelle first-party |
| Vente guidée, conseil | Quiz Aiden, comparateur, iAdvize, SmartAgenda | Aucun |
| Paiement | Références Adyen, Oney, Klarna, PayPal dans le bundle | Adyen |
| Supervision | New Relic | Dynatrace |
| Commun aux deux | DAM groupe, TwicPics, Salesforce (identité, formulaires), Uberall, portail `*.returns.international`, API recettes `sebplatform.api.groupe-seb.com` | idem |

Toutes ces lignes sont **[O]**, sauf mention. Une note secondaire attribue le quiz Krups à Zoovu ; le script observé sur la page est celui d'Aiden. SQLI indique que la migration du groupe vers SAP Commerce Cloud est en production depuis juin 2024 et a tenu le Black Friday 2024 sans interruption ([SQLI](https://www.sqli.com/int-en/case-studies/seb-cloud-migration-sap-commerce)) **[R]**.

Mesure labo du 11/11/2026 **[O]** : un seul passage, Chromium en émulation Pixel 7, processeur ralenti 4 fois, réseau 1,6 Mbit/s et 150 ms de latence, cache froid, bannière de consentement non acceptée, sonde aux États-Unis.

| Page | TTFB | FCP | LCP (élément) | CLS | Blocage (TBT) | Requêtes | Poids | JS | DOM |
|---|---|---|---|---|---|---|---|---|---|
| Krups FR accueil | 0,21 s | 1,55 s | 24,7 s (texte de la bannière) | 0,002 | 7 310 ms | 237 | 3,84 Mo | 2,87 Mo | 2 816 |
| Krups FR liste grains | 0,22 s | 1,45 s | 1,60 s | 0,001 | 4 798 ms | 176 | 2,00 Mo | 1,43 Mo | 4 063 |
| Krups FR fiche EA875E10 | 0,22 s | 1,47 s | 14,3 s (image produit) | 0,021 | 5 779 ms | 202 | 2,63 Mo | 1,75 Mo | 4 921 |
| Krups DE accueil | 0,22 s | 1,58 s | 1,68 s | 0,181 | 4 100 ms | 171 | 2,29 Mo | 1,72 Mo | 2 699 |
| Krups DE liste | 0,16 s | 1,52 s | 1,83 s | 0,171 | 4 888 ms | 183 | 2,22 Mo | 1,50 Mo | 4 607 |
| Krups DE fiche EA897B10 | 3,27 s | 4,40 s | 18,3 s (image produit) | 0,187 | 5 632 ms | 215 | 2,46 Mo | 1,76 Mo | 4 658 |
| Lagostina FR accueil | 0,31 s | 0,95 s | 1,42 s | 0,044 | 479 ms | 81 | 1,06 Mo | 0,53 Mo | 1 081 |
| Lagostina FR liste café | 0,44 s | 1,14 s | 9,4 s (logo de la bannière) | 0,003 | 451 ms | 87 | 1,71 Mo | 0,53 Mo | 944 |
| Lagostina FR fiche Moka | 0,51 s | 1,04 s | 7,0 s (logo de la bannière) | 0 | 372 ms | 78 | 0,92 Mo | 0,51 Mo | 1 102 |

Ratios Krups FR ÷ Lagostina FR, page par page :

| Indicateur | Accueil | Liste | Fiche |
|---|---|---|---|
| JavaScript | 2,87 ÷ 0,53 = 5,4 | 1,43 ÷ 0,53 = 2,7 | 1,75 ÷ 0,51 = 3,4 |
| Temps de blocage | 7 310 ÷ 479 = 15,3 | 4 798 ÷ 451 = 10,6 | 5 779 ÷ 372 = 15,5 |
| Requêtes | 237 ÷ 81 = 2,9 | 176 ÷ 87 = 2,0 | 202 ÷ 78 = 2,6 |
| Nœuds DOM | 2 816 ÷ 1 081 = 2,6 | 4 063 ÷ 944 = 4,3 | 4 921 ÷ 1 102 = 4,5 |

Quatre réserves encadrent ces ratios. Ce sont des ordres de grandeur de laboratoire, pas des valeurs terrain. Les images produit de Lagostina n'ont pas chargé dans le navigateur de test (réponse 403 de TwicPics), ce qui sous-estime son poids. Les fiches comparées ne sont pas équivalentes : une cafetière Moka contre une machine à 37 médias. Enfin, l'écart se resserre après consentement : Lagostina passe à 1,85 Mo de JavaScript, et Krups n'a pas été mesuré dans cet état. Les deux sites partagent un même défaut : à la première visite, l'élément LCP est souvent la bannière de consentement ou une image tardive.

### 3.4 Scores Krups FR, côte à côte avec Lagostina

Scores **[I]** sur constats **[O]** ; L = Lagostina.fr, K = Krups sur rowenta.fr.

| # | Critère | L | K | Preuve Krups |
|---|---|---|---|---|
| A1 | Menu et taxonomie | 2 | 2 | Café par type de machine et par système ; trois marques dans un même en-tête ; `/krups/nos-magasins` renvoie à l'accueil |
| A2 | Filtres et tris | 1 | 1 | 7 tris, mais aucune facette (`facets: []`) sur 17 à 21 machines de 260 à 1 000 € ([liste](https://www.rowenta.fr/krups/Caf%C3%A9/Machines-%C3%A0-caf%C3%A9-%C3%A0-grains/c/krups-expresso-avec-broyeur-a-grain)) |
| A3 | Recherche | 1 | 2 | Luigi's Box ; moteur commun aux trois marques ; pertinence non testée en profondeur |
| A4 | Vente guidée | 1 | 3 | [Quiz Aiden](https://www.rowenta.fr/krups/product-finder-cafe/page1), bouton « Comparer », guides d'achat, chat et visio iAdvize, rendez-vous conseiller |
| B1 | Bloc d'achat | 2 | 2 | Prix et prix recommandé, éco-participation 2,10 €, stock, « Livraison en 3 jours », réassurance ; pas de date de livraison ([fiche](https://www.rowenta.fr/krups/p/intuition-preference-machine-a-cafe-a-grain-15-boissons-ecran-tactile-krups/8010000986)) |
| B2 | Avis | 0 | 2 | Skeepers : 4,5/5 sur 310 avis pour l'EA875E10, jusqu'à 587 avis par machine ; réponses aux avis négatifs non vérifiées |
| B3 | Médias et caractéristiques | 2 | 3 | 37 médias dont vidéos, 9 groupes de caractéristiques, contenu de la boîte, notice, FAQ avec vidéos |
| B4 | Lots et ventes associées | 2 | 2 | Carrousel « pièces, accessoires et services associés », lots web ; pas de composition dans le bloc d'achat |
| C1 | Commande invitée | 2 | 2 | Activée en configuration (`activateGuestCheckout`, `expressCheckoutEnabled`) ; tunnel non parcouru |
| C2 | Formulaire | 2 | n.t. | Validation d'adresse DQE activée en configuration ; formulaire non observé |
| C3 | Coût et délai | 1 | 2 | Franco 50 €, délai affiché, express payant ; deux colis possibles pour machine + consommables |
| C4 | Paiement | 1 | 3 | Carte, PayPal, Apple Pay, Google Pay, Oney 3x/4x/12x ; portefeuilles indisponibles pour l'entrepôt accessoires |
| D1 | CWV terrain | n.t. | n.t. | Aucune donnée |
| D2 | Labo bridé | 2 | 1 | LCP 1,60 s en liste mais 14,3 à 24,7 s sur fiche et accueil ; blocage 4,8 à 7,3 s |
| D3 | Poids JS et tiers | 2 | 1 | 1,43 à 2,87 Mo de JS avant tout consentement |
| D4 | Architecture et cache | 1 | 2 | HTML rendu serveur et caché par Cloudflare, TwicPics ; hydratation lourde |
| E1 | Balisage produit | 2 | 2 | `Product`, `Offer`, `aggregateRating`, `review`, `FAQPage` ; URL produit relative |
| E2 | Balisage site | 2 | 1 | Bloc vide `{}` sur l'accueil, `creator: ROWENTA`, pas d'`Organization`, fil d'Ariane malformé (`https://www.rowenta.fr8010000986`) |
| E3 | International et canonical | 1 | 0 | Aucun hreflang dans le HTML ni les sitemaps ; canonical sous le domaine d'une autre marque |
| E4 | Crawl et facettes | 1 | 2 | Facettes et recherche bloquées, sitemaps par marque (109 produits Krups), FAQ sur les fiches |
| F1 | Accessibilité | 1 | 0 | Aucun lien « Accessibilité » en pied de page, en France comme en Allemagne |
| F2 | Consentement | 2 | 1 | Consent Mode à « denied » par défaut ; une dizaine de tiers chargés sans consentement exprimé (Kameleoon, New Relic, YouTube, SDK PayPal…) ; dépôt de traceurs non vérifié |
| F3 | Affichages légaux | 1 | 2 | Prix le plus bas 30 jours, éco-participation, fiche environnementale détaillée ; CGV rédigées pour Rowenta et datées du 02/11/2022 |
| G1 | Raison d'acheter en direct | 2 | 2 | Lots web, retours 90 jours, reconditionné constructeur ; pas de bloc explicite |
| G2 | Offre différenciée | 2 | 2 | Lots web et 17 reconditionnés ; ni édition limitée ni personnalisation |
| G3 | Cohérence de prix | 2 | 1 | Voir 3.2 : écarts dans les deux sens, reconditionné plus cher que le neuf, écarts France/Allemagne jusqu'à 520 € |
| G4 | Marque | 2 | 1 | Pas de domaine propre ; « © 2021 Rowenta », lien YouTube vers MoulinexFR, « Prix le plus bas pratiqué sur Rowenta » |
| H1 | Captation first-party | 1 | 2 | Code newsletter 10/20/30 €, compte, espace `/mes-produits` ; enregistrement non récompensé, pas de club |
| H2 | SAV et pièces | 1 | 3 | Forfaits de réparation achetables, pièces 15 ans, centres d'aide par gamme avec diagnostic des codes d'erreur, extension de garantie |
| H3 | Consommables et récurrence | 0 | 1 | Consommables d'entretien vendus mais plusieurs indisponibles ; ni café ni abonnement |
| H4 | Après-achat et circularité | 1 | 2 | Portail de retours, reconditionné, pages « MaKrups… » de prise en main ; pas d'application en France |

| Dimension | Poids | Lagostina | Krups FR | Calcul Krups | Contribution Krups |
|---|---|---|---|---|---|
| A Découverte | 0,15 | 1,25 | 2,00 | (2+1+2+3) ÷ 4 | 0,3000 |
| B Fiche produit | 0,15 | 1,50 | 2,25 | (2+2+3+2) ÷ 4 | 0,3375 |
| C Checkout | 0,15 | 1,50 | 2,33 | (2+2+3) ÷ 3 | 0,3500 |
| D Performance | 0,10 | 1,67 | 1,33 | (1+1+2) ÷ 3 | 0,1333 |
| E SEO et données | 0,10 | 1,50 | 1,25 | (2+1+0+2) ÷ 4 | 0,1250 |
| F Conformité | 0,10 | 1,33 | 1,00 | (0+1+2) ÷ 3 | 0,1000 |
| G Valeur DTC | 0,10 | 2,00 | 1,50 | (2+2+1+1) ÷ 4 | 0,1500 |
| H Services et récurrence | 0,15 | 0,75 | 2,00 | (2+3+1+2) ÷ 4 | 0,3000 |
| **Global pondéré** | 1,00 | **1,40 → 46,7/100** | **1,80 → 59,9/100** | | 1,7958 ÷ 3 × 100 |

Test de sensibilité à poids égaux (moyenne simple des huit dimensions) : Lagostina 11,50 ÷ 8 = 1,44, soit 47,9/100 ; Krups 13,67 ÷ 8 = 1,71, soit 56,9/100. L'ordre ne dépend pas de la pondération.

Trois lectures :

1. **Lagostina devance Krups sur D, E, F et G** : front plus léger, balisage de site plus propre, conformité mieux engagée, marque autonome.
2. **Krups devance Lagostina sur A, B, C et H**, avec l'écart le plus fort sur les services (2,00 contre 0,75).
3. **Les deux échouent sur ce qui compte le plus pour le café** : H3 (consommables et récurrence) vaut 0 et 1.

Les scores C de Krups reposent en partie sur la configuration et les CGV, le tunnel n'ayant pas été parcouru. L'audit de Lagostina a été plus approfondi que celui de Krups sur E et F, ce qui peut pénaliser Lagostina à la marge.

### 3.5 Le cas krups.de

Le site allemand montre ce qu'apporte un domaine propre sur le même gabarit, et ce qu'il n'apporte pas **[O]** :

1. Marque autonome, OneTrust, Klarna et Amex, droit de rétractation porté à 90 jours, Dolce Gusto vendue en direct, franco à 49 €.
2. Performance voisine de la France, avec un CLS de 0,17 à 0,19 qui dépasse le seuil de 0,1 et un TTFB de 3,27 s sur la fiche testée.
3. Activité directe marginale : 710 avis de boutique depuis le 04/03/2023, dont 124 sur 12 mois ([Echte Bewertungen](https://www.echte-bewertungen.com/kundenmeinungen/krups.de)), contre 9 392 sur 12 mois pour rowenta.fr ([Avis Vérifiés](https://www.avis-verifies.com/avis-clients/rowenta.fr)), soit 9 392 ÷ 124 = 76 fois moins.

Ordre de grandeur des commandes krups.de **[I]** : avec un taux de réponse supposé de 5 à 10 % (hypothèse non sourcée), 124 ÷ 0,10 = 1 240 et 124 ÷ 0,05 = 2 480 commandes par an. Le domaine propre ne suffit donc pas à créer un DTC. L'ancien krups.fr n'avait cumulé que 2 105 avis depuis le 27/10/2022 ([Avis Vérifiés](https://www.avis-verifies.com/avis-clients/krups.fr)), soit environ 61 par mois sur 34,5 mois jusqu'à la dernière capture connue du site, quand tefal.fr en recueille 4 372 ÷ 12 = 364 par mois **[O]** pour les volumes, **[I]** pour le rythme.

## 4. La présentation se transpose, le commerce s'adapte, les services café restent à construire

### 4.1 Ce qui se transpose tel quel

| Brique Lagostina | Pourquoi elle se transpose | Preuve |
|---|---|---|
| Thème par jetons de design | Changer de marque revient à changer jetons, polices, logo et contenus, sans toucher aux composants | [O] variables CSS ; [I] conclusion |
| Routes et types de pages | Routes internes génériques localisées par réécriture ; deux pays servis par le même code | [O] |
| Modèle de contenu | Collection, campagne, histoire, guide de catégorie, recettes, FAQ : il se décline en gammes, « quel type de machine », recettes de boissons, entretien | [I] |
| Mécaniques marchandes | Accessoires cochables, « à associer avec », lots virtuels exclusifs web, éditions numérotées, blocs éditoriaux dans les listes | [O] |
| Briques groupe | DAM, TwicPics, OneTrust, identité Salesforce, Uberall, portail de retours, API recettes : déjà communes aux deux piles | [O] |
| Paiement | Adyen est présent des deux côtés | [O] code |
| Gabarit de données analytiques | dataLayer GA4 structuré avec dimensions groupe (`item_sis_category`) | [O] |

### 4.2 Ce qui doit être adapté

| Sujet | Écart | Adaptation |
|---|---|---|
| Modèle produit | Lagostina : quelques attributs. Krups : 9 groupes de caractéristiques, 37 médias, notice, fiche environnementale, statut de réparabilité, éco-participation | Étendre le modèle de fiche et l'alimentation PIM ; contrôler les traductions (codes PIM visibles chez Lagostina) |
| Entité vendeuse et logistique | Groupe SEB France contre SEB International Services ; deux entrepôts Krups | Paramétrer vendeur, flux et politiques de retour par type d'article |
| Prix et promotions | Krups affiche le prix le plus bas des 30 jours et applique des codes mutualisés aux trois marques | Reprendre l'affichage réglementaire et redéfinir les règles promotionnelles |
| Financement | Klarna (Lagostina) contre Oney (Krups France) | Choisir par pays ; les deux existent en libellés dans le code Lagostina |
| Cache | Aucun cache HTML chez Lagostina ; acceptable à faible trafic, pas pour un pic café de fin d'année | Régénération incrémentale ou cache en bordure |
| Médias | Vidéos brutes de 10 à 85 Mo | Chaîne de transcodage et diffusion adaptative |
| Consentement | Bannière illustrée qui devient l'élément LCP | Bannière sans image lourde |
| International | hreflang partiel chez Lagostina, absent chez Krups | Grappe hreflang France, Allemagne, Pays-Bas, Autriche |
| Partenaire Nestlé | Dolce Gusto renvoyée vers Nestlé en France | Règle de vente par gamme et par pays |
| Outillage | Pas de test A/B chez Lagostina ; Kameleoon chez Krups | Rebrancher l'outil groupe |
| Recette de mise en ligne | Défauts de localisation et contenus périmés chez Lagostina | Recette linguistique, données et accessibilité avant ouverture |

### 4.3 Ce qui manque pour le café

| Brique | Dans le modèle Lagostina | Chez Krups aujourd'hui | Référence de marché | Travail de portage |
|---|---|---|---|---|
| Avis clients | Absents, alors que la boutique collecte des avis | Skeepers, syndiqués | 14 fiches sur 14 du benchmark | Brancher Skeepers et `AggregateRating` dès le premier jour |
| Comparaison et vente guidée | Absentes | Quiz, comparateur, visio | De'Longhi (quiz récompensé), Jura (vidéo) | Réutiliser Aiden et iAdvize ; construire le comparateur et les facettes |
| Consommables par machine | Absents | 18 accessoires liés à une machine, mais ruptures | Sage : rayon nettoyage en accueil | Relation machine → consommables dans le catalogue ; stock |
| Abonnement grains | Absent | Absent | De'Longhi DE (jusqu'à −35 %, module sur mesure adossé à Adyen), Beanz, Lavazza | Moteur d'abonnement à créer ou partenaire ; résiliation en ligne obligatoire |
| Réassort d'entretien | Absent | Absent | Personne ne le propose en abonnement | Même moteur, périmètre plus simple |
| Enregistrement produit | Absent | Présent, non récompensé | Melitta : −10 % | Reprendre `/mes-produits` ; ajouter une contrepartie |
| Service et réparation | Annuaire de réparateurs | Forfaits, pièces 15 ans, extension de garantie, centres d'aide | Krups est la référence | À porter intégralement |
| Reconditionné | Absent | 17 références | De'Longhi, Melitta, Smeg | À porter |
| Chat, liste d'envies, alerte stock | Absents | Chat présent | Courants | À construire ou rebrancher |

Deux faits structurent le chantier abonnement. Aucun fabricant européen observé n'utilise un moteur d'abonnement tiers identifiable : De'Longhi a développé le sien sur Salesforce Commerce Cloud ([De'Longhi](https://www.delonghi.com/fr-fr)) **[O]**. Et la faisabilité d'un abonnement sur la plateforme SAP du groupe n'a pas été évaluée (lacune). Adyen, présent dans les deux piles, fournit le socle commun de paiement récurrent **[I]**.

### 4.4 Scénarios techniques

Les notes d'effort et de risque (1 = faible, 5 = élevé) sont des estimations relatives de l'auteur **[I]** : aucun budget, délai ni coût de licence n'est public.

| | S0 — Optimiser la boutique mutualisée | S1 — Re-skin sur le gabarit SAP/Spartacus | S2 — Re-platforming sur la pile Lagostina | S3 — Hybride : front Lagostina sur commerce SAP |
|---|---|---|---|---|
| Principe | Corriger rowenta.fr/krups sans changer d'architecture | Redonner à Krups France un site propre sur le gabarit groupe, habillé selon les codes Lagostina | Recréer Krups sur Next.js, Contentful, Adobe Commerce | Front Next.js et Contentful de Lagostina, branché sur l'API SAP existante |
| Appui factuel | Tout le socle existe | krups.de, .nl, .at tournent ainsi ; la boutique `Krups-FR` répond encore dans l'API | Pile en production sur 2 pays et 258 à 382 produits | Le front Lagostina ne parle qu'à sa couche `/api/*` ; l'API SAP est publique en lecture |
| Gains | Rapides, sans régression | Domaine et marque autonomes ; SEO de marque | Performance, liberté éditoriale, image premium, une seule pile cible | Performance et éditorial du front ; commerce et services conservés |
| Ce qui n'est pas réglé | Dilution de marque, poids du front | Poids du front (blocage 4,8 à 7,3 s), contraintes du CMS SAP | — | Coexistence de deux fronts ; adaptateur à écrire |
| À reconstruire | Rien | Peu | Avis, pièces, forfaits, reconditionné, Oney, enregistrement, quiz, chat, deux flux logistiques, règles Nestlé | Adaptateur catalogue, panier et compte ; choix pour le checkout |
| Effort | 1 | 2 | 5 | 3 à 4 |
| Risque | 1 | 2 | 4 à 5 | 3 |
| Réversibilité | Totale | Forte | Faible | Moyenne à forte |
| Principal risque | Gain plafonné | Va contre la mutualisation décidée en 2025–2026 | Régression fonctionnelle ; pile jeune, sans cache HTML, sans pic de charge démontré | Combinaison inédite dans le groupe |

Arguments en faveur de chaque option, y compris celles non retenues :

1. **S1 est défendable** : c'est l'option la moins chère pour un site de marque, sur un socle qui a tenu le Black Friday 2024 **[R]**. Elle échoue sur l'objectif implicite du « modèle Lagostina », qui est un front léger et éditorialisé.
2. **S2 est défendable si le groupe a choisi cette pile comme cible.** Une offre d'emploi évoque une « refonte stratégique de la plateforme e-commerce » ([jobup.ch](https://www.jobup.ch/en/jobs/detail/a2b5f84e-89c0-474e-a3fd-069aa7a5ba20), extrait) **[R]**, et le service s'appelle « nwp ». Mais rien de public ne le confirme, et la pile n'a ni historique commercial ni audit d'accessibilité.
3. **S3 conserve ce que Krups fait déjà mieux que Lagostina** (dimensions A, B, C, H) et prend ce que Lagostina fait mieux (D, G, contenu). Son point faible est la complexité : un quatrième assemblage technique dans un groupe qui en compte déjà trois générations.

### 4.5 Option recommandée : l'hybride par étapes, avec trois portes de décision

1. **Étape 0, sans regret (jusqu'au 31 mars 2027) : S0.** Corriger la boutique actuelle sur les critères notés 0 ou 1. Ces gains restent acquis quel que soit le scénario final.
2. **Étape 1, pilote (avril à septembre 2027) : S3 limité à l'univers café en France.** Accueil de marque, listes, fiches et contenus servis par le front Lagostina rethémé ; panier et tunnel conservés sur SAP dans un premier temps. Le pilote se mesure contre la boutique actuelle.
3. **Étape 2, décision (premier semestre 2028).** Selon les résultats et la trajectoire du groupe : étendre l'hybride au tunnel et aux autres pays, ou basculer le commerce (S2), ou revenir à S1.

Trois portes conditionnent le passage de 1 à 2 :

| Porte | Condition |
|---|---|
| Technique | Faisabilité de l'adaptateur vers l'API SAP confirmée par la DSI du groupe ; cache HTML et chaîne vidéo en place ; audit d'accessibilité passé |
| Plateforme | Le groupe a statué sur le rôle de la pile Lagostina (cible ou exception) et celle-ci a traversé au moins un pic de fin d'année avec des résultats mesurés |
| Économique | Le pilote montre un gain mesuré sur la conversion, le taux d'attache des consommables et l'enregistrement, face à la boutique mutualisée |

Cette recommandation est **[I]**. Elle repose sur un constat robuste (les forces des deux sites sont complémentaires, section 3.4) et sur une hypothèse à valider : que la couche d'intégration du front Lagostina accepte un autre moteur de commerce sans réécriture lourde.

## 5. SWOT d'un site Krups café bâti sur le modèle Lagostina

### Forces

| # | Force | Preuve |
|---|---|---|
| 1 | Front léger et rethémable : jetons de design, routes génériques, deux pays sur un même code | [O] |
| 2 | Briques groupe déjà communes aux deux piles (DAM, TwicPics, Salesforce, Uberall, retours, recettes, Adyen) | [O] |
| 3 | Triptyque de services Krups sans équivalent chez ses concurrents directs : réparabilité 15 ans, forfaits de 30,49 à 169,99 €, 17 reconditionnés | [O] |
| 4 | Retours à 90 jours contre 30 à 60 jours chez De'Longhi, Philips, Sage | [O] |
| 5 | Capital d'avis : 310 à 587 avis par machine phare, syndiqués entre pays ; 4,5/5 pour la boutique mutualisée rowenta.fr sur 32 160 avis | [O] |
| 6 | Vente guidée déjà outillée : quiz, comparateur, visio, rendez-vous | [O] |
| 7 | Fabrication à Mayenne : badge « Fabriqué en France » sur 13 des 21 machines à grains | [O] |
| 8 | Dynamique Coffee Crush : lancée le 27/03/2026 à 299,99–349,99 €, environ 50 pays en 2026, plus de 60 % des moyens sur les réseaux sociaux ([présentation S1 2026](https://www.groupeseb.com/sites/default/files/sites/default/files/GroupeSeb-Pr%C3%A9sentation_Ventes_et_R%C3%A9sultats_1er_semestre_2026-22072026.pdf)) | [R] source primaire |
| 9 | Mécaniques Lagostina directement utiles au café : accessoires cochables, lots virtuels, éditions numérotées | [O] |

### Faiblesses

| # | Faiblesse | Preuve |
|---|---|---|
| 1 | Aucun consommable à forte récurrence : ni grains, ni capsules ; le récurrent se limite à des filtres à 10,99 €, des pastilles et du détartrant, en partie en rupture | [O] |
| 2 | Pas de domaine de marque en France ; mentions, métadonnées et CGV « Rowenta » | [O] |
| 3 | Pile Lagostina immature : quelques semaines à quelques mois, localisation inachevée, accessibilité non auditée, pas de cache HTML, pas de test A/B | [O] |
| 4 | Écart fonctionnel du modèle Lagostina : avis, comparateur, chat, enregistrement, pièces, reconditionné absents | [O] |
| 5 | Aucune preuve que le modèle Lagostina performe commercialement ; site trop récent | Lacune |
| 6 | Front Krups actuel très lourd : 4,8 à 7,3 s de blocage en labo | [O] |
| 7 | Deux vendeurs logistiques et deux politiques de retour pour un même panier machine + consommables | [O] |
| 8 | Incohérences de prix : reconditionné plus cher que le neuf, écarts France/Allemagne de 49 à 87 % | [O] |
| 9 | Base DTC modeste : environ 61 avis par mois sur l'ancien krups.fr, 124 avis en 12 mois sur krups.de | [O] volumes, [I] lecture |
| 10 | Réputation SAV exposée : Trustpilot 1,3/5 pour lagostina.fr, 2/5 pour krups.co.uk | [R] |

### Opportunités

| # | Opportunité | Preuve |
|---|---|---|
| 1 | Aucun abonnement grains de fabricant en France : De'Longhi l'active dans 10 locales européennes mais pas en fr-FR ; Beanz n'a pas de locale française | [O] |
| 2 | Réassort automatique d'entretien (filtre, pastilles, détartrant) : aucun acteur observé ne le propose en abonnement | [O] constat, [I] opportunité |
| 3 | Argument économique du grain : café en hausse d'environ 25 % sur un an (baromètre Circana de mars 2026, via [Info.fr](https://info.fr/cafe-46-en-un-an-pourquoi-les-prix-ne-baissent-pas/)) ; capsules autour de 60 €/kg contre 20 €/kg pour le grain ([L'Essentiel de l'Éco](https://lessentieldeleco.fr/4731-inflation-le-cafe-devient-un-produit-de-luxe/), fiabilité moyenne) ; aucun site observé n'affiche le coût par tasse | [R] |
| 4 | Capter les acheteurs passés par les enseignes via l'enregistrement récompensé : 32 % visitent le site du fabricant, 1,6 % y achètent | [R] extrait, donnée 2023 |
| 5 | Vent porteur dans le groupe : ventes en ligne en hausse d'environ 10 % en 2025 « notamment portées par le DTC », « renforcement des ressources vers les ventes en ligne », base de consommateurs actifs à doubler ([communiqué du 25/02/2026](https://www.groupeseb.com/sites/default/files/sites/default/files/GroupeSEB-CP-R%C3%A9sultats_annuels_2025-25022026.pdf)) | [R] source primaire |
| 6 | Montée du « home barista » : expresso semi-automatiques avec broyeur en hausse de 53 % en volume en 2025 (NielsenIQ cité par [Labo Maison](https://labomaison.com/?p=57453)) | [R] extrait |
| 7 | Réparabilité comme différenciateur volontaire : les machines à café ne sont soumises à aucun indice obligatoire ([ministère de la Transition écologique](https://www.ecologie.gouv.fr/politiques-publiques/indice-reparabilite)) ; bonus réparation de 25 € pour une machine automatique ([Aide-Sociale.fr](https://www.aide-sociale.fr/bonus-reparation/)) | [R] |
| 8 | Effet de levier multi-pays : France, Allemagne, Pays-Bas, Autriche partagent le gabarit ; Royaume-Uni, Italie, Belgique, Portugal restent sur l'ancien front | [O] |
| 9 | Partenariat torréfacteurs : De'Longhi référence des torréfacteurs français sans les vendre ; les torréfacteurs de spécialité ont déjà leurs outils d'abonnement | [O] |

### Risques

| # | Risque | Preuve |
|---|---|---|
| 1 | Tension avec Nestlé : Krups distribue Nespresso depuis 1991 et Dolce Gusto depuis 2006 ; un site poussant le grain concurrence les capsules du partenaire ; les termes contractuels ne sont pas publics | [R] partenariat, [I] tension |
| 2 | Conflit de canaux sous contrainte de droit de la concurrence : sanction de 189,5 M€ infligée à SEB le 19/12/2024 pour prix de revente imposés ([LSA](https://www.lsa-conso.fr/electromenager-amende-record-infligee-a-10-fabricants-et-2-distributeurs-pour-entente-sur-les-prix,458627)) ; la boutique vend déjà une machine 300 € sous Boulanger | [R] sanction, [O] prix |
| 3 | Économie faible du café pour un fabricant sans café : marge brute de Beanz « relativement faible » ; cinq ans pour 300 000 clients cumulés ([rapport annuel Breville FY26](https://announcements.asx.com.au/asxpdf/20260819/pdf/072x54y8g6zpks.pdf)) | [R] |
| 4 | Attrition concentrée au départ : environ 61 % des résiliations volontaires aux deux premiers renouvellements ; 28,2 % par cycle en alimentaire aux cycles 2 et 3 ([Recharge](https://getrecharge.com/?p=27190), données à dominante américaine) | [R] |
| 5 | Riposte concurrente : activation possible de l'abonnement De'Longhi ou de Beanz en France ; Lavazza offre déjà −40 % sur la machine à grains avec abonnement ; prix barrés permanents chez De'Longhi et Melitta | [O] |
| 6 | Marché des automatiques en recul : −6 % en volume en 2025, −3,3 % sur janvier–avril 2026 (Labo Maison, extraits ; une autre page du même site donne −2,3 %) | [R] |
| 7 | Contexte de rationalisation : plan Rebond (200 M€ d'économies d'ici fin 2027, jusqu'à 2 100 postes, 25 à 30 % de références en moins), nouveau directeur général depuis le 01/10/2026 ; un site autonome doit prouver un gain face à l'option mutualisée | [R] source primaire, [I] lecture |
| 8 | Régression technique en cas de bascule : perte d'avis, de moyens de paiement, de services ; pile sans pic de charge démontré | [I] |
| 9 | Conformité : accessibilité (EAA en vigueur depuis le 28/06/2025), traceurs, résiliation en ligne des abonnements, directive sur les allégations environnementales, règlement déforestation applicable au café le 30/12/2026 | [R] |
| 10 | Fragmentation : trois générations de plateformes dans le groupe, et l'hybride en ajoute une combinaison | [O] constat, [I] risque |

Estimation de l'enjeu grains par foyer équipé **[I]**, sous l'hypothèse mal sourcée de 8 à 12 kg par an :

| Prix au kilo | Source du prix | 8 kg | 12 kg |
|---|---|---|---|
| ≈ 20 €/kg (grande distribution) | L'Essentiel de l'Éco [R] | 8 × 20 = 160 € | 12 × 20 = 240 € |
| ≈ 29–30 €/kg (boutique de marque) | [Melitta FR](https://www.melitta.fr/cafe/cafe-grains) [O] | 8 × 29 = 232 € | 12 × 30 = 360 € |
| ≈ 48–73 €/kg (spécialité) | [Beanz UK](https://www.beanz.com/en-gb) [O], converti à 1 £ ≈ 1,15 € | 8 × 48 = 384 € | 12 × 73 = 876 € |

La dépense annuelle en grains d'un foyer est donc du même ordre que le prix d'une machine d'entrée de gamme (259,99 à 349,99 €). Le rythme Beanz tempère : 2,6 millions de sacs ÷ 300 000 clients = 8,7 sacs par client en cumul sur cinq ans **[I]**.

## 6. Feuille de route priorisée et KPI

### 6.1 Recommandations

**Priorité 0 — sans regret, sur la plateforme actuelle (du 1er décembre 2026 au 31 mars 2027, hors gel de fin d'année)**

1. **Mesurer avant de décider.** Obtenir les Core Web Vitals terrain (CrUX avec clé, ou les outils New Relic et Dynatrace déjà installés) pour rowenta.fr, krups.de et lagostina.fr. Isoler le trafic et le chiffre d'affaires de l'onglet Krups. Critères D1 et toute la porte économique.
2. **Purger la marque.** Supprimer « © 2021 Rowenta », le lien YouTube Moulinex, « Prix le plus bas pratiqué sur Rowenta », les titres « Rowenta » des recettes ; rédiger des CGV exactes. Critères G4, F3.
3. **Réparer le balisage.** Fil d'Ariane, `Organization`, `WebSite`, `ItemList`, politiques de livraison et de retour ; grappe hreflang France, Allemagne, Pays-Bas, Autriche. Critères E2, E3.
4. **Publier une déclaration d'accessibilité et lancer un audit WCAG 2.2 AA.** Critère F1, noté 0.
5. **Auditer les tiers chargés avant consentement** et alléger le JavaScript des gabarits accueil et fiche. Critères F2, D2, D3.
6. **Ajouter des facettes à la liste des machines** : boissons lactées, taille, prix, fabrication française. Critère A2.
7. **Fiabiliser l'entretien.** Supprimer les ruptures sur pastilles, kit et détartrant ; proposer un kit d'entretien par machine et un lot machine + entretien. Critère H3.
8. **Récompenser l'enregistrement produit** par une contrepartie (remise sur consommables ou extension de garantie), y compris pour les machines achetées en enseigne. Critère H1.
9. **Fixer des règles de prix internes** : reconditionné toujours sous le neuf ; doctrine écrite sur les fins de série, validée juridiquement. Critère G3.

**Priorité 1 — pilote hybride café en France (avril à septembre 2027)**

10. **Valider la faisabilité technique** avec la DSI : adaptateur du front Lagostina vers l'API SAP, périmètre du tunnel, partage de session.
11. **Décliner le système de design** Krups (jetons, typographies) et le modèle de contenu café dans Contentful : gammes, guides de choix, recettes de boissons, entretien.
12. **Lancer avec les sept conditions tirées de Lagostina** : recette de localisation et de données PIM ; chaîne vidéo ; cache HTML ; bannière de consentement légère ; avis et `AggregateRating` dès le premier jour ; promesse de stock et de livraison cohérente entre fiche, panier et CGV ; audit d'accessibilité avant ouverture.
13. **Réassort automatique d'entretien**, première brique récurrente : fréquence modifiable, rappel avant envoi, résiliation en ligne directe.
14. **Bloc « Pourquoi acheter chez Krups »** sur chaque fiche : retours 90 jours, réparabilité 15 ans, forfaits, reconditionné. Critère G1.

**Priorité 2 — offre café et extension (octobre 2027 à juin 2028)**

15. **Offre grains en partenariat torréfacteur**, de préférence en modèle place de marché (le torréfacteur vend et expédie), ce qui limite le risque de marge et l'exposition au règlement déforestation **[I]**. Arbitrage préalable avec Nestlé.
16. **Afficher le coût par tasse** grains contre capsules et un pack de démarrage machine + grains.
17. **Soigner les trois premières livraisons** d'un abonnement, où se concentre l'attrition.
18. **Passer les portes de décision** (section 4.5) puis étendre à l'Allemagne, aux Pays-Bas et à l'Autriche, ou basculer, ou revenir au gabarit groupe.

**Priorité 3 — veille (2028)**

19. Programme de fidélité ou club ; flux produits pour le commerce par agents conversationnels, dont le format OpenAI cible encore les États-Unis ([OpenAI](https://developers.openai.com/commerce/specs/feed)) **[O]**.

Contrainte de calendrier : pas de mise en production entre octobre et décembre. En Allemagne, sept événements promotionnels font 37 % du chiffre d'affaires annuel des biens techniques en 15 semaines ([gfu, 18/08/2026](https://gfu.de/wp-content/uploads/2026/08/260818_Warmup-praesentation.pdf)) **[R]**.

### 6.2 KPI à suivre

| KPI | Définition | Repère externe (source, preuve) | Usage |
|---|---|---|---|
| Core Web Vitals | p75 mobile par gabarit | LCP ≤ 2,5 s, INP ≤ 200 ms, CLS ≤ 0,1 ; 55,9 % des origines passent ([CrUX, septembre 2026](https://developer.chrome.com/docs/crux/release-notes)) [R] | Porte technique |
| Taux de conversion | Commandes ÷ sessions, nouveaux et récurrents | 2,59 % en « cuisine et électroménager » en septembre 2026, panel britannique ([IRP](https://www.irpcommerce.com/us/en/ecommercemarketdata.aspx?Market=15)) ; nouveaux 1,7 %, récurrents 2,9 % (Contentsquare) [R] | Ordre de grandeur seulement : un site de fabricant convertit moins |
| Abandon de panier | Paniers non convertis | 70,22 % en moyenne (Baymard) [R] | Suivi du tunnel |
| Taux d'attache consommables | Machines suivies d'un achat d'entretien ou de café sous 90 jours | Aucun repère public | KPI central du modèle |
| Taux d'enregistrement | Machines enregistrées ÷ machines vendues, tous canaux | Aucun repère public | Captation des acheteurs en enseigne |
| Attrition par cycle | Résiliations au 2e, 3e, 6e renouvellement | 24,1 %, 27,1 %, 10,1 % (Recharge) [R] | Abonnements |
| Valeur client ÷ coût d'acquisition | CLV ÷ CAC | Au moins 2 avant passage à l'échelle ([McKinsey](https://www.mckinsey.com/industries/consumer-packaged-goods/our-insights/dtc-e-commerce-how-consumer-brands-can-get-it-right), extrait) [R] | Porte économique |
| Avis | Volume et note par machine ; part d'avis négatifs avec réponse | 89 % des sites ne répondent pas (Baymard) [R] | Fiches et SAV |
| Disponibilité | Références en stock ÷ références listées | Krups : 15 ÷ 21 = 71 % en machines à grains ; Lagostina : 4 autocuiseurs sur 10 [O] | Promesse commerciale |
| Écart de prix | Prix boutique − prix enseignes, par SKU | Section 3.2 | Pilotage du conflit de canaux, sous contrôle juridique |
| Conformité | Taux de conformité à l'audit WCAG ; tiers chargés avant consentement | Seuils de la grille | Critères F1, F2 |
| Score de grille | Note 0–3 par dimension, revue chaque trimestre | Lagostina 1,40 ; Krups 1,80 au 11/11/2026 | Cible indicative de 2,0 par dimension [I] |

## Limites de la recherche

1. **Performance** : aucune donnée terrain Core Web Vitals. Les mesures sont des passages uniques en laboratoire depuis une sonde américaine ; les images produit de Lagostina n'ont pas chargé dans le navigateur de test.
2. **Tunnel** : aucune commande, aucun formulaire soumis. L'étape de paiement, les frais de port réels, les points relais et les e-mails transactionnels n'ont pas été observés.
3. **Marché** : parts de marché par marque en machines à grains non publiées ; part du DTC non publiée par Groupe SEB ni par ses concurrents ; chiffres NielsenIQ lus par extraits, avec une incohérence non résolue (−6 % ou −2,3 %).
4. **Résultats commerciaux** : aucune donnée de conversion, de panier ou de trafic pour Lagostina ou Krups ; estimations Semrush anciennes ou partielles ; l'activité Krups n'est pas isolable dans rowenta.fr.
5. **Prix distributeurs** : Darty, Boulanger et Amazon étaient inaccessibles ; les prix d'enseignes viennent d'un agrégateur, et l'échantillon Lagostina se limite à 4 produits.
6. **Sources expertes** : les deux articles McKinsey n'ont été lus que par extraits, et le chiffre 32 % / 1,6 % date de 2023. Plusieurs repères sont à dominante américaine ou britannique (Baymard pour l'abandon, Recharge, IRP).
7. **Plateforme** : intégrateur, coûts, PIM, gestion des commandes et intention du groupe pour la pile Lagostina sont inconnus ; la date exacte de mise en ligne n'est pas établie.
8. **Faisabilité** : l'adaptateur entre le front Lagostina et l'API SAP, ainsi qu'un moteur d'abonnement sur SAP, n'ont pas été évalués.
9. **Juridique** : les termes des accords Nestlé–SEB ne sont pas publics ; les montants de sanction de l'EAA divergent selon les sources ; l'état de transposition de plusieurs directives au 11/11/2026 n'a pas été vérifié sur les textes.
10. **Scores** : ils reposent sur une seule journée d'observation, en période de pré-Black Friday, et sur le jugement de l'auteur.

## Conclusion

La question de départ supposait que Lagostina était le modèle et Krups l'élève. L'audit montre deux sites aux forces complémentaires : Lagostina a résolu la forme (un front rapide, une marque lisible, un contenu libre) sans le fond commercial ; Krups a le fond (avis, conseil, réparation, reconditionné) dans une forme lourde et diluée sous une autre marque. Porter le modèle revient donc à assembler les deux, pas à remplacer l'un par l'autre.

Le vrai sujet se situe hors des deux sites : aucun ne possède le mécanisme de réachat qui justifie un DTC café. Tant que Krups ne vend ni grains ni entretien récurrent, un nouveau site ne changera que marginalement l'équation économique qui a conduit le groupe à fondre krups.fr dans rowenta.fr. La séquence recommandée teste d'abord ce mécanisme à faible coût, sur la plateforme existante, avant d'engager l'architecture.
