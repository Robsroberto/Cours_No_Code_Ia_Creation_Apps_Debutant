## 1. Créer le projet Webflow et choisir le bon template  

Webflow propose une bibliothèque de templates gratuits et payants. Pour une landing page qui servira à présenter un service local (ex. : service de livraison à domicile à Lagos ou de formation en ligne à Nairobi), le template **“Startup Landing”** est un bon point de départ : il inclut déjà les sections classiques (hero, features, témoignages, CTA).  

1. **Inscription / connexion** – Créez votre compte sur [webflow.com](https://webflow.com).  
2. **Nouveau projet** – Cliquez sur *New Project* → *Templates* → recherchez “Startup Landing”.  
3. **Nom du projet** – Donnez‑lui un nom explicite, par exemple `Landing-IA-AgriTech`.  
4. **Paramètres du site** – Dans *Project Settings* → *General* :  
   - **Slug du site** : `agritech.africa` (le domaine sera configuré plus loin).  
   - **Favicon** : téléversez un petit logo généré par IA (voir section 2).  

> **Astuce** : si vous avez besoin d’un point de départ totalement vierge, choisissez *Blank Site* et ajoutez les sections manuellement.  

## 2. Générer les contenus texte avec l’IA  

### 2.1. Définir le brief IA  

Utilisez ChatGPT (ou Jasper) pour créer des blocs de texte adaptés à votre audience locale. Exemple de prompt :  

```
Rédige un titre accrocheur de 8 mots pour une landing page qui propose une plateforme de mise en relation entre agriculteurs nigérians et acheteurs urbains. Le ton doit être enthousiaste, en français, avec un mot en langue locale (yoruba, haoussa ou swahili).  
```

### 2.2. Titres et sous‑titres  

| Élément | Résultat IA | Placement dans Webflow |
|---------|-------------|------------------------|
| Hero title | **« Cultivez votre avenir : Connectez les champs nigérians aux villes »** | *Section → Hero → Heading* |
| Hero subtitle | *« Une plateforme digitale qui simplifie la vente directe, sécurise les paiements et garantit la fraîcheur. »* | *Hero → Paragraph* |
| CTA button | **« Commencer gratuitement »** | *Button component* |
| Feature 1 title | **« Paiement mobile intégré (MTN Mobile Money) »** | *Features → Card → Heading* |
| Feature 1 description | *« Vos ventes sont payées instantanément, sans frais cachés. »* | *Feature card → Paragraph* |

Copiez chaque texte dans le champ correspondant du Designer Webflow (double‑clic sur le composant texte, collez).  

### 2.3. Témoignages et preuves sociales  

Demandez à l’IA :  

> “Écris trois témoignages courts (max 60 caractères) de fermiers nigérians qui utilisent la plateforme, en français avec un mot en haoussa.”  

Résultat :  

1. *« « Barka », je vends 200 kg de mil à Lagos chaque semaine – plus de perte ! »*  
2. *« « Aminata », mes tomates atteignent le marché en 24 h – frais comme au champ. »*  
3. *« « Kofi », le paiement arrive dès la livraison – confiance totale. »*  

Intégrez-les dans la section *Testimonials* du template.  

## 3. Créer les visuels avec l’IA (Canva IA, Midjourney, Stable Diffusion)  

### 3.1. Choisir le style visuel  

Pour toucher un public africain, privilégiez des couleurs chaudes (ocre, vert savane) et des images représentant des scènes locales (marchés, champs, smartphones).  

### 3.2. Prompt de génération d’image  

Exemple pour Midjourney :  

```
/imagine prompt: African farmer holding a smartphone, sunrise over a cocoa plantation, vibrant colors, realistic style, 4k --ar 16:9
```  

Générez :  

- **Hero background** : image de 1920 × 1080 px.  
- **Icones de features** : petites illustrations (150 × 150 px) représentant paiement mobile, logistique, suivi en temps réel.  

Exportez les fichiers en **WebP** pour un poids réduit (≤ 150 KB).  

### 3.3. Importer dans Webflow  

Dans le Designer, sélectionnez la *Section → Background* → *Image* → *Upload* → choisissez le fichier WebP. Répétez pour chaque composant visuel.  

> **Note** : Si vous avez besoin d’un texte superposé sur l’image, créez un *Div Block* avec un fond semi‑transparent (rgba(0,0,0,0.4)) et placez le titre à l’intérieur.  

## 4. Structurer la landing page dans le Designer Webflow  

### 4.1. Utiliser les Symboles (Symbols)  

Les *Symbols* permettent de réutiliser des blocs (ex. : le header et le footer).  

1. Sélectionnez le *Navbar* → *Create Symbol* → nommez `Header-Global`.  
2. Faites de même pour le *Footer* → `Footer-Global`.  

Ainsi, toute modification future (ajout d’un lien vers la politique de confidentialité) se répercute automatiquement.  

### 4.2. Configurer le formulaire de capture de leads  

1. Glissez‑déposez le composant *Form Block* dans la section *CTA*.  
2. Ajoutez les champs suivants :  
   - **Nom complet** (type : text)  
   - **E‑mail** (type : email)  
   - **Téléphone** (type : tel) – important pour les marchés où le SMS reste le canal principal.  
3. Dans le *Form Settings* → *Form Action* choisissez **Zapier** (voir chapitre 7) ou **Webflow Forms** pour stocker les soumissions.  

### 4.3. Ajouter du code personnalisé (optionnel)  

Pour intégrer le script de suivi Google Analytics (ou Matomo auto‑hébergé) :  

```html
<!-- Matomo Tracking -->
<script>
  var _paq = window._paq = window._paq || [];
  _paq.push(['trackPageView']);
  _paq.push(['enableLinkTracking']);
  (function() {
    var u="//analytics.africa.example.com/";
    _paq.push(['setTrackerUrl', u+'matomo.php']);
    _paq.push(['setSiteId', '5']);
    var d=document, g=d.createElement('script'), s=d.getElementsByTagName('script')[0];
    g.async=true; g.src=u+'matomo.js'; s.parentNode.insertBefore(g,s);
  })();
</script>
```  

Dans le Designer, allez dans *Project Settings → Custom Code → Head Code* et collez le bloc.  

## 5. Optimiser le SEO local  

### 5.1. Balises méta  

Dans *Project Settings → SEO* :  

| Champ | Valeur (exemple) |
|------|-------------------|
| **Title Tag** | `Agritech – Marketplace pour agriculteurs nigérians` |
| **Meta Description** | `Plateforme sécurisée pour vendre vos produits agricoles directement aux villes nigérianes. Paiement Mobile, Livraison Rapide.` |
| **OG Image** | Téléversez une version réduite de votre hero (1200 × 630 px). |

### 5.2. Structuration sémantique  

Utilisez les *Heading* de façon hiérarchique :  

- H1 : le titre principal (hero).  
- H2 : sections *Features*, *How it works*, *Testimonials*.  
- H3 : sous‑titres de chaque feature.  

Webflow crée automatiquement les balises `<h1>`, `<h2>`, etc., à condition de choisir le bon composant *Heading* dans le panneau de droite.  

### 5.3. Données structurées (Schema.org)  

Ajoutez un script JSON‑LD dans le *Footer* pour indiquer le type d’entreprise :  

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "AgriTech Nigeria",
  "url": "https://agritech.africa",
  "telephone": "+234-800-123-4567",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "12, Rue du Marché",
    "addressLocality": "Lagos",
    "addressRegion": "Lagos",
    "postalCode": "100001",
    "addressCountry": "NG"
  },
  "image": "https://agritech.africa/assets/hero.webp",
  "description": "Marketplace digitale pour les agriculteurs nigérians."
}
```  

Placez ce bloc dans *Project Settings → Custom Code → Footer Code*.  

### 5.4. Vitesse de chargement  

- **Images** : WebP, compression < 150 KB.  
- **Lazy loading** : activez l’option *Lazy Load* sur chaque image dans le Designer.  
- **Minification CSS/JS** : Webflow le fait automatiquement en mode *Publish*.  

Utilisez **Google PageSpeed Insights** (ou le service local *GTmetrix Africa* à `gtmetrix.africa`) pour vérifier que le score dépasse 90 %.  

## 6. Publier sur un domaine africain  

### 6.1. Acheter un domaine local  

Des registrars africains (ex. : *AfriDomain*, *Namecheap Africa*) proposent des extensions comme `.africa`, `.ke`, `.ng`.  
- Exemple : `agritech.africa` acheté via *AfriDomain*.  

### 6.2. Configurer les DNS  

1. Dans le tableau de bord du registrar, créez deux enregistrements :  
   - **A record** → `75.2.70.75` (IP de Webflow).  
   - **CNAME** pour `www` → `proxy-ssl.webflow.com`.  
2. Attendez la propagation (généralement 5–30 min).  

### 6.3. Attacher le domaine à Webflow  

Dans *Project Settings → Hosting* :  

- Cliquez sur *Add custom domain* → saisissez `agritech.africa`.  
- Cochez la case *Default domain* pour que le site s’affiche sans `www`.  
- Webflow affichera le statut **Connected** une fois les DNS propagés.  

### 6.4. SSL et HTTPS  

Activez le bouton *Enable SSL* dans la même page. Webflow provisionne automatiquement le certificat **Let's Encrypt**.  

### 6.5. Redirection géographique (optionnelle)  

Si vous ciblez plusieurs pays (ex. : Nigeria, Ghana, Côte d’Ivoire), créez des sous‑domains (`ng.agritech.africa`, `gh.agritech.africa`) et utilisez **Cloudflare Workers** pour rediriger les visiteurs selon l’adresse IP.  

## 7. Test final et itérations  

1. **Vérifier le rendu mobile** – Utilisez le mode *Responsive* du Designer (breakpoints : 768 px, 480 px).  
2. **Formulaire** – Soumettez un test et assurez‑vous que la donnée arrive dans Webflow Forms ou Zapier.  
3. **SEO** – Lancez une analyse avec *Screaming Frog SEO Spider* (version gratuite) pour détecter les balises manquantes.  
4. **Temps de chargement** – Re‑testez PageSpeed après chaque optimisation d’image.  

Une fois tous les points validés, cliquez sur *Publish* → choisissez le domaine `agritech.africa`.  

---

## À retenir  

- **Template + IA** : choisir un template adapté puis laisser l’IA générer titres, textes et visuels pour gagner du temps et rester pertinent localement.  
- **Symboles** : centraliser le header/footer pour des modifications globales rapides.  
- **SEO local** : balises méta, données structurées et optimisation de la vitesse sont indispensables pour se positionner sur les recherches africaines.  
- **Domaines africains** : privilégier les extensions `.africa`, `.ng`, `.ke` pour renforcer la confiance des utilisateurs et améliorer le référencement géographique.  
- **Boucle d’amélioration** : tester sur mobile, vérifier les formulaires et analyser la vitesse avant chaque publication définitive.  

En suivant ces étapes, vous obtenez une landing page professionnelle, prête à convertir des visiteurs africains en prospects qualifiés, le tout sans écrire une seule ligne de code — et avec l’appui de l’intelligence artificielle pour accélérer chaque phase du projet.