## Créer la base de données Airtable

### 1. Structurer son modèle de données

Commencez par identifier les entités de votre application de gestion : **Clients**, **Commandes**, **Produits**. Chaque entité devient une table Airtable.  
Dans la vue *Grid* de chaque table, ajoutez les champs suivants :

| Table | Champ | Type | Exemple d’usage |
|-------|-------|------|-----------------|
| Clients | `Nom` | Single line text | « Mamadou Diop » |
| Clients | `Email` | Email | mamadou@exemple.com |
| Clients | `Téléphone` | Phone number | +221 77 123 45 67 |
| Clients | `Pays` | Single select (Senegal, Côte d’Ivoire, Mali…) | Sénégal |
| Produits | `Référence` | Single line text | PRD‑001 |
| Produits | `Nom` | Single line text | « Mango Juice » |
| Produits | `Prix` | Currency | 2 500 FCFA |
| Commandes | `Client` | Linked record → Clients | (relation) |
| Commandes | `Produit` | Linked record → Produits | (relation) |
| Commandes | `Quantité` | Number | 3 |
| Commandes | `Statut` | Single select (En cours, Expédiée, Annulée) | En cours |
| Commandes | `Date de création` | Created time | (automatique) |

**Astuce IA** – Utilisez le *ChatGPT* intégré à Airtable (via le bouton *Assistants* dans la barre latérale) : tapez « Propose‑moi les champs indispensables pour gérer une boutique en ligne en Afrique francophone ». L’IA vous renvoie une liste de champs que vous pouvez copier‑coller directement.

### 2. Créer des vues adaptées aux utilisateurs

Airtable permet de filtrer, trier et masquer les colonnes selon le contexte :

- **Vue « Clients actifs »** : filtre `Pays = "Sénégal"` et `Statut ≠ "Inactif"`.  
- **Vue « Commandes à expédier »** : filtre `Statut = "En cours"` et trie par `Date de création` décroissant.  
- **Vue « Catalogue produits »** : masque les colonnes internes (`ID interne`, `Créateur`) et montre seulement `Référence`, `Nom`, `Prix`.

Ces vues seront directement exploitées par Softr pour afficher les listes ou les tableaux de bord.

### 3. Automatiser la saisie de données avec l’IA

#### 3.1. Script Airtable + OpenAI

Dans le *Scripting block* d’Airtable, créez un script qui, à chaque création d’un enregistrement `Clients`, génère une description courte à partir du nom et du pays :

```javascript
// script.js – à placer dans le bloc Scripting d’Airtable
let table = base.getTable("Clients");
let result = await table.selectRecordsAsync({ fields: ["Nom", "Pays"] });

for (let record of result.records) {
    if (!record.getCellValue("Description IA")) {
        let prompt = `Rédige une courte description (max 30 mots) d'un client nommé ${record.getCellValue("Nom")} vivant au ${record.getCellValue("Pays")}.`;
        // Appel à l'API OpenAI (clé stockée dans les variables d'environnement d'Airtable)
        let response = await fetch("https://api.openai.com/v1/completions", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                "Authorization": `Bearer ${process.env.OPENAI_API_KEY}`
            },
            body: JSON.stringify({
                model: "gpt-3.5-turbo",
                prompt: prompt,
                max_tokens: 60,
                temperature: 0.6
            })
        });
        let data = await response.json();
        let description = data.choices[0].text.trim();

        await table.updateRecordAsync(record.id, {
            "Description IA": description
        });
    }
}
```

Ce script s’exécute manuellement ou via une **Automation** : déclencheur *When record created*, action *Run script*. Chaque nouveau client obtient automatiquement une description générée, enrichissant la base sans effort supplémentaire.

#### 3.2. Formulaire Web avec suggestions IA

Dans Softr, ajoutez un **Formulaire** lié à la table `Clients`. Activez l’option *Pre‑fill with AI* (disponible dans le module *Custom Code*). Le champ `Nom` déclenchera, à la frappe du premier caractère, une requête vers l’API d’OpenAI :

```javascript
// custom-code.js – inséré dans le widget Custom Code de Softr
document.querySelector('#input-nom').addEventListener('input', async (e) => {
    const nom = e.target.value;
    if (nom.length < 3) return; // attendre 3 caractères
    const response = await fetch('https://api.openai.com/v1/completions', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${window.SOFTR_OPENAI_KEY}`
        },
        body: JSON.stringify({
            model: 'gpt-3.5-turbo',
            prompt: `Quel pays francophone d'Afrique serait le plus logique pour un client nommé ${nom} ?`,
            max_tokens: 15,
            temperature: 0.3
        })
    });
    const data = await response.json();
    const suggestion = data.choices[0].text.trim();
    document.querySelector('#suggestion-pays').innerText = `Suggestion : ${suggestion}`;
});
```

L’utilisateur voit instantanément la proposition de pays, ce qui réduit les erreurs de saisie et accélère le processus d’onboarding.

## Construire l’interface utilisateur avec Softr

### 1. Connecter Airtable à Softr

1. Dans le tableau de bord Softr, créez un nouveau **Project** et choisissez le template *Data Management* (ou un template vierge).  
2. Accédez à *Data Sources* → *Add new source* → sélectionnez **Airtable**.  
3. Copiez l’*API Key* et le *Base ID* depuis votre compte Airtable (voir chapitre 4 pour la génération d’API Keys).  
4. Sélectionnez les tables que vous souhaitez exposer : `Clients`, `Produits`, `Commandes`. Softr crée automatiquement les **Collections** correspondantes.

### 2. Configurer les pages principales

| Page | Objectif | Composants Softr |
|------|----------|------------------|
| Dashboard | Vue d’ensemble pour l’administrateur | **List** (Commandes à expédier), **Statistiques** (chiffre d’affaires, nombre de clients) |
| Clients | Gestion des contacts | **Table** (vue « Clients actifs »), **Formulaire** d’ajout/modif, **Detail Page** |
| Produits | Catalogue interne | **Card Grid** (affichage produit), **Formulaire** d’ajout, **Search Bar** |
| Commandes | Suivi des ventes | **List** filtrée par statut, **Formulaire** de mise à jour du statut, **Automation** de notification (voir section suivante) |

Chaque composant possède un *Data Source* qui pointe vers la collection Airtable correspondante. Utilisez les **Filtres** intégrés pour reproduire les vues créées dans Airtable (ex. `Statut = "En cours"`).

### 3. Personnaliser le design pour le marché africain

- **Couleurs** : privilégiez des teintes chaudes (orange, vert citron) qui résonnent avec les palettes utilisées dans les marques africaines.  
- **Typographies** : choisissez des fontes compatibles avec les caractères accentués (Montserrat, Lato).  
- **Images** : intégrez des visuels libres de droits provenant de *Canva IA* ou *Unsplash* avec des mots‑clés comme “marché africain”, “artisanat”.  
- **Langue** : activez le **multilingue** de Softr et créez une version française (FR) et, le cas échéant, une version en anglais (EN) pour les partenaires internationaux.

### 4. Mettre en place les permissions et la sécurité

Softr propose trois niveaux d’accès :

| Rôle | Accès | Exemple d’usage |
|------|-------|-----------------|
| **Admin** | Lecture/écriture sur toutes les tables | Gestion complète, export CSV |
| **Gestionnaire** | Lecture/écriture sur `Commandes` et `Produits` | Responsable logistique |
| **Client** | Lecture seule sur son propre enregistrement `Clients` | Portail client auto‑service |

Dans *Settings → Permissions*, créez les rôles et associez‑les aux pages. Utilisez la fonction *Row‑level security* d’Airtable (voir chapitre 4) pour que chaque client ne voie que ses propres commandes.

### 5. Automatiser les notifications via Zapier + IA

#### 5.1. Scénario Zapier

1. **Trigger** : *New record in Airtable* → table `Commandes`.  
2. **Action 1** : *Run JavaScript* – appelle l’API OpenAI pour générer un message de confirmation personnalisé :  

```javascript
const prompt = `Rédige un SMS de confirmation de commande pour ${inputData.clientName} qui a commandé ${inputData.productName} (x${inputData.quantity}). Le statut actuel est "${inputData.status}".`;
return fetch('https://api.openai.com/v1/completions', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${process.env.OPENAI_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    model: 'gpt-3.5-turbo',
    prompt: prompt,
    max_tokens: 60,
    temperature: 0.7
  })
})
.then(res => res.json())
.then(data => ({ message: data.choices[0].text.trim() }));
```

3. **Action 2** : *Send SMS* via Twilio (ou via un opérateur local comme **Orange Money SMS**).  
4. **Action 3** : *Update record* dans Airtable – coche la case `SMS envoyé`.

#### 5.2. Retour dans Softr

Ajoutez un **Alert** dynamique sur la page *Detail Commande* : si le champ `SMS envoyé` est vrai, affichez « Message de confirmation envoyé ». Cela rassure l’utilisateur et montre que le processus est automatisé.

## Optimiser l’expérience utilisateur

### 1. Chargement différé des listes

Dans les composants **List** et **Card Grid**, activez l’option *Lazy Load* (chargement au scroll). Cela réduit le temps de chargement initial, essentiel pour les connexions mobiles souvent limitées en Afrique.

### 2. Recherche intelligente

Utilisez le **Search Bar** de Softr avec la fonction *Fuzzy Search* : les utilisateurs peuvent taper « Mango » et retrouver « Mango Juice » même avec une faute d’orthographe. Combinez cela avec le champ `Tags` (ex. `Boisson, Fruit`) pour affiner les résultats.

### 3. Export des données hors ligne

Pour les zones où la connectivité est intermittente, ajoutez un bouton **Export CSV** sur la page *Clients*. Softr génère le fichier à la volée grâce à l’API Airtable :

```javascript
// custom-code-export.js
async function exportClients() {
  const response = await fetch(`https://api.airtable.com/v0/${BASE_ID}/Clients?api_key=${API_KEY}`);
  const data = await response.json();
  const csv = Papa.unparse(data.records.map(r => r.fields));
  const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
  const link = document.createElement('a');
  link.href = URL.createObjectURL(blob);
  link.download = 'clients.csv';
  link.click();
}
document.querySelector('#export-btn').addEventListener('click', exportClients);
```

Le module **Custom Code** de Softr accepte ce script, offrant aux utilisateurs la possibilité de travailler hors‑ligne puis de ré‑importer les modifications via le formulaire.

## Tester, déployer et itérer

1. **Mode Preview** – Vérifiez chaque page en mode « Preview » de Softr, testez les formulaires avec des données factices.  
2. **Test d’utilisabilité mobile** – Utilisez l’outil *Responsive Design Mode* de Chrome pour simuler les smartphones courants en Afrique (Samsung Galaxy A12, Tecno Camon).  
3. **Contrôle des quotas Airtable** – Surveillez le nombre d’enregistrements et les limites d’API (5 req/s). Si vous prévoyez plus de 1 000 enregistrements, envisagez le plan *Pro* d’Airtable.  
4. **Déploiement** – Dans *Settings → Domain*, associez votre domaine personnalisé (ex. `app.maboutique.africa`). Activez le **SSL** fourni par Softr pour sécuriser les échanges.  
5. **Collecte de feedback** – Intégrez le widget **Hotjar** (ou un équivalent local comme **Meltwater Insights**) pour analyser le comportement des utilisateurs et ajuster les champs IA qui posent problème.

## Points clés

- **Modélisation** : chaque entité (Clients, Produits, Commandes) devient une table Airtable ; les relations sont créées via *Linked records*.  
- **Vues filtrées** : préparez des vues spécifiques (clients actifs, commandes à expédier) qui seront directement consommées par Softr.  
- **IA intégrée** : utilisez l’assistant ChatGPT d’Airtable pour suggérer des champs, puis automatisez la génération de texte avec un script OpenAI.  
- **Connexion Softr‑Airtable** : importez les tables comme collections, puis configurez listes, formulaires et détails en fonction des vues créées.  
- **Personnalisation locale** : adaptez couleurs, typographies et images aux goûts du marché africain francophone.  
- **Permissions granulaires** : combinez les rôles Softr et la sécurité au niveau des lignes d’Airtable pour protéger les données clients.  
- **Automatisation IA** : via Zapier (ou les Automations natives d’Airtable) créez des notifications SMS personnalisées générées par OpenAI.  
- **Performance mobile