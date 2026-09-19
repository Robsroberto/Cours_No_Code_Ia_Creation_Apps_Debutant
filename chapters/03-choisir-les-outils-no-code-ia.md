## Cartographier son besoin avant de choisir  

### Type de projet et exigences fonctionnelles  
| Projet | Fonctionnalités clés | Priorité IA | Exemple africain |
|--------|----------------------|------------|------------------|
| Site vitrine (ex. ONG, PME) | SEO, blog, formulaire de contact | Génération de texte & visuels | ONG de santé au Burkina Faso veut publier des rapports mensuels en français et en anglais |
| Marketplace (ex. produits agricoles) | Catalogue, paiement, gestion des vendeurs, recherche avancée | Chatbot d’assistance, recommandations produit | Plateforme de vente de maïs au Kenya, paiement via **Paystack** |
| Application mobile (ex. suivi formation) | Authentification, notifications push, tableau de bord | Analyse de réponses, génération de certificats | Application de formation continue pour les enseignants en Côte d’Ivoire |
| Dashboard interne (ex. suivi logistique) | Tableaux, filtres, export CSV | Synthèse de données, alertes IA | Startup logistique à Lagos veut visualiser les flux de livraison en temps réel |

### Contraintes budgétaires  
- **Freemium** : idéal pour tester le concept (ex. Webflow Starter, Bubble Free).  
- **Abonnement mensuel** : prévoir une fourchette de **10 – 30 USD** pour un MVP fonctionnel en Afrique (coût internet + hébergement).  
- **Coût IA** : les appels API (ChatGPT, Jasper) sont facturés à la requête ; un petit projet peut rester sous **5 USD**/mois avec le plan « Pay‑as‑you‑go ».  

### Connectivité et latence en Afrique  
- La plupart des plateformes sont **cloud‑first** (hébergées aux USA/EU).  
- Utiliser des **CDN** (Cloudflare, Cloudinary) pour servir les assets statiques depuis des points de présence proches (Abidjan, Nairobi, Lagos).  
- Privilégier les outils qui offrent **caching côté client** (ex. Glide, qui stocke les données dans le navigateur) afin de garantir une expérience fluide même avec du 3G/4G intermittent.  

---

## Panorama des plateformes no‑code  

### Webflow – Designer visuel et CMS intégré  
- **Points forts** : contrôle précis du design (CSS, interactions), hébergement performant, SEO natif.  
- **Limites** : logique back‑end limitée (pas de base de données relationnelle native).  
- **Coût** : plan « Basic » à 12 USD/mois (site statique), « CMS » à 16 USD/mois (contenu dynamique).  

### Softr – Applications SaaS sur Airtable  
- **Points forts** : création d’applications à partir d’une base Airtable, interface « drag‑and‑drop », authentification intégrée.  
- **Limites** : dépendance à Airtable (quota de lignes, tarif).  
- **Coût** : plan gratuit (max 1 000 lignes), plan Pro à 24 USD/mois (10 000 lignes, domaine custom).  

### Bubble – Plateforme full‑stack visuelle  
- **Points forts** : logique conditionnelle avancée, création d’API, gestion de bases de données relationnelles.  
- **Limites** : courbe d’apprentissage plus élevée, performances variables selon le plan.  
- **Coût** : plan Personal à 25 USD/mois (capacité serveur suffisante pour un MVP).  

### Adalo – Apps mobiles natives (iOS/Android)  
- **Points forts** : génération d’apps natives, composants mobiles (listes, cartes), intégration de paiements (Stripe, Paystack).  
- **Limites** : personnalisation UI moins fine que Webflow, limites de stockage (500 objets dans le plan gratuit).  
- **Coût** : plan Pro à 50 USD/mois (publier sur stores, 20 000 objets).  

### Glide – Prototypage ultra‑rapide à partir de Google Sheets  
- **Points forts** : mise en place d’une app en quelques minutes, fonctionne hors‑ligne grâce au cache, idéal pour des projets à budget ultra‑limité.  
- **Limites** : logique conditionnelle basique, dépendance à Google Sheets (latence sur gros volumes).  
- **Coût** : plan Free (limité à 500 lignes), plan Pro à 29 USD/mois (10 000 lignes, domaine custom).  

#### Tableau comparatif rapide  

| Critère | Webflow | Softr | Bubble | Adalo | Glide |
|---------|---------|-------|--------|-------|-------|
| Design pixel‑perfect | ★★★★★ | ★★☆☆☆ | ★★☆☆☆ | ★★★☆☆ | ★★☆☆☆ |
| Base de données relationnelle | – | ★★☆☆☆ | ★★★★★ | ★★★☆☆ | ★★☆☆☆ |
| Apps mobiles natives | – | – | – | ★★★★★ | ★★★☆☆ |
| Courbe d’apprentissage | ★★☆☆☆ | ★☆☆☆☆ | ★★★★☆ | ★★☆☆☆ | ★☆☆☆☆ |
| Prix (plan de base) | 12 USD | 24 USD | 25 USD | 50 USD | 29 USD |
| Fonction offline | – | – | – | – | ★★★★★ |
| Connectivité Africa | CDN intégré | CDN Cloudflare (via Softr) | CDN Cloudflare (via Bubble) | CDN Cloudflare | CDN Cloudflare |

---

## Panorama des services d’intelligence artificielle  

### ChatGPT (OpenAI) – Génération de texte & assistance conversationnelle  
- **API** : `POST https://api.openai.com/v1/chat/completions`  
- **Tarif** : $0.002 / 1 000 tokens (GPT‑3.5) – très économique pour du contenu marketing ou des réponses de chatbot.  
- **Cas d’usage** : rédaction de fiches produit, FAQ dynamique, aide à la rédaction de rapports.  

### Jasper – Copywriting orienté marketing  
- **Interface** : prompts pré‑définis (AIDA, PAS) et suggestions de titres.  
- **Tarif** : à partir de 29 USD/mois (plan Starter).  
- **Cas d’usage** : génération de copies publicitaires en français, adaptation de slogans pour plusieurs langues locales (swahili, haoussa).  

### Canva IA – Design assisté et génération d’images  
- **Fonctionnalités** : « Text‑to‑Image », mise en page automatique, redimensionnement intelligent.  
- **Tarif** : plan Pro à 12,99 USD/mois, incluant 100 images IA/mois.  
- **Cas d’usage** : créer des visuels de campagne pour les réseaux sociaux, flyers d’événement local, infographies de données sanitaires.  

### Zapier AI – Automatisations intelligentes  
- **Fonction** : « AI Builder » qui transforme un texte en logique Zapier (ex. extraire date d’un email et créer un événement Google Calendar).  
- **Tarif** : inclus à partir du plan Professional (49 USD/mois) ou en add‑on IA à 20 USD/mois.  
- **Cas d’usage** : enrichir les leads capturés via un formulaire Webflow avec un résumé généré par ChatGPT, puis les envoyer à Airtable.  

#### Tableau comparatif IA  

| Service | Type | Points forts | Limites | Prix de base |
|---------|------|--------------|---------|--------------|
| ChatGPT | Texte | Flexibilité, multilingue, API simple | Nécessite gestion du contexte | $0.002/1k tokens |
| Jasper | Copywriting | Templates marketing, UI dédiée | Moins personnalisable que GPT | 29 USD/mois |
| Canva IA | Visuel | Génération d’images, design auto | Qualité variable selon prompt | 12,99 USD/mois |
| Zapier AI | Automatisation | Intégration directe, workflow IA | Coût supplémentaire, dépend de Zapier | 20 USD/mois add‑on |

---

## Méthode de sélection pas à pas  

### Étape 1 : Lister les fonctions essentielles  
1. **Contenu dynamique** (blog, fiches produit) → besoin CMS.  
2. **Gestion de données** (inventaire, utilisateurs) → base de données relationnelle.  
3. **Interaction mobile** (notifications, offline) → app native ou PWA.  
4. **Assistance IA** (chatbot, génération de texte) → API texte ou image.  

### Étape 2 : Aligner le budget avec le modèle de paiement  
- **Freemium** → idéal pour le prototype.  
- **Abonnement mensuel** → calculez le coût total (plateforme + IA) et comparez avec le chiffre d’affaires prévisionnel.  
- **Facturation à l’usage** → privilégiez ChatGPT ou Zapier AI si le volume d’appels est incertain.  

### Étape 3 : Tester la connectivité et la latence  
1. Créez une page simple sur chaque plateforme (ex. Webflow, Softr).  
2. Mesurez le **Time‑to‑First‑Byte (TTFB)** depuis Lagos avec **webpagetest.org** (choisissez un serveur africain).  
3. Si le TTFB > 2 s, ajoutez un CDN ou choisissez une alternative plus légère (Glide).  

### Étape 4 : Réaliser un proof‑of‑concept (PoC) de 48 h  
- **Objectif** : valider la chaîne complète (front → IA → base de données).  
- Exemple : un formulaire de contact Webflow → Zapier AI (résumé) → Airtable → notification Slack.  
- Si le PoC fonctionne sans erreurs et reste sous le budget prévu, passez à la version MVP.  

---

## Cas pratiques de choix d’outils  

### Cas A : Site vitrine d’une ONG au Burkina Faso  
- **Besoins** : pages multilingues (français/anglais), blog, formulaire de dons.  
- **Choix** : **Webflow** (design professionnel + hébergement CDN) + **ChatGPT** pour générer les descriptions de projets.  
- **Budget** : 12 USD (Webflow) + 5 USD (ChatGPT) ≈ 17 USD/mois.  

### Cas B : Marketplace de produits agricoles au Kenya  
- **Besoins** : catalogue produit, paiement Paystack, tableau de bord vendeur, recommandations IA.  
- **Choix** : **Bubble** (logique de paiement, base de données relationnelle) + **Jasper** (copywriting des fiches) + **Zapier AI** (synchronisation des stocks).  
- **Budget** : 25 USD (Bubble) + 29 USD (Jasper) + 20 USD (Zapier AI) ≈ 74 USD/mois – viable grâce aux commissions sur ventes.  

### Cas C : Application de suivi de formation en Côte d’Ivoire  
- **Besoins** : authentification, suivi de progression, certificats PDF, offline.  
- **Choix** : **Adalo** (apps mobiles natives) + **Canva IA** (génération de certificats) + **ChatGPT** (FAQ dynamique).  
- **Budget** : 50 USD (Adalo) + 13 USD (Canva IA) + 5 USD (ChatGPT) ≈ 68 USD/mois.  

Ces trois scénarios illustrent comment la **triade** *type de projet – budget – connectivité* guide le choix de la stack no‑code + IA.  

---

## Astuces pour optimiser la connectivité en Afrique  

1. **Activer le CDN Cloudflare** sur chaque domaine (Webflow, Softr, Bubble) – le point d’entrée le plus proche de Lagos ou Nairobi réduit le temps de chargement de 30 % en moyenne.  
2. **Compresser les images** avec TinyPNG ou le module intégré de **Canva IA** avant le téléchargement.  
3. **Limiter les appels IA** : mettre en cache les réponses de ChatGPT pendant 24 h dans Airtable ou Bubble (field “Cache → Last Response”).  
4. **Utiliser les formats WebP** pour les visuels mobiles afin de réduire la bande passante de 40 %.  
5. **Pré‑charger les scripts** critiques (ex. `loader.js` de Glide) en les hébergeant sur un serveur local (OVH Afrique).  

---

## Intégrer les IA dans les plateformes no‑code  

### Exemple 1 : Appeler ChatGPT depuis Bubble (API Connector)  

1. **Créer un endpoint** dans le plugin *API Connector* :  

```json
{
  "name": "ChatGPT Completion",
  "url": "https://api.openai.com/v1/chat/completions",
  "method": "POST",
  "headers": {
    "Authorization": "Bearer