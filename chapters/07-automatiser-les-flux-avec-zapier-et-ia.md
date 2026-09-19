## Créer des scénarios d’automatisation avec Zapier  

Zapier fonctionne comme un **pont** entre les applications que vous utilisez quotidiennement (Google Forms, Airtable, Gmail, Slack, Paystack, …).  
Un *Zap* est composé de :

1. **Trigger** – l’événement qui déclenche le flux (ex. : soumission d’un formulaire).  
2. **Actions** – les opérations exécutées automatiquement (ex. : créer une ligne Airtable, envoyer un SMS).  

En combinant plusieurs actions, vous pouvez reproduire un processus métier complet sans écrire une seule ligne de code.  

### 1. Concevoir le flux avant de le créer  

Avant d’ouvrir Zapier, notez :

| Étape | Question à se poser |
|-------|---------------------|
| **Déclencheur** | Quel événement marque le début du processus ? (ex. : un client remplit le formulaire de devis). |
| **Données nécessaires** | Quelles informations doivent être récupérées ? (nom, email, montant, produit). |
| **Résultat attendu** | Que doit‑on faire avec ces données ? (enregistrement, notification, facturation). |
| **Points de contrôle** | Quels contrôles de qualité ou validations sont requis ? (validation du format du numéro de téléphone). |

Cette cartographie vous évite les allers‑retours dans Zapier et garantit que chaque action a un but précis.

---

## Intégrer l’IA dans vos Zaps  

Zapier propose deux leviers d’IA :  

* **Zapier AI** – un moteur natif qui exploite les modèles OpenAI (GPT‑4, GPT‑3.5) pour générer du texte, résumer, classifier, etc.  
* **Webhooks + API OpenAI** – vous donne le contrôle total sur les paramètres (temperature, max_tokens) et permet d’appeler des modèles spécialisés (ex. : `text-davinci-003` pour l’extraction d’entités).

### 2. Extraction d’informations à partir d’un texte libre  

**Cas d’usage** : un client envoie un email contenant son numéro de téléphone, son adresse et le produit souhaité. Vous devez extraire ces champs pour les stocker dans Airtable.

#### Étapes du Zap  

| Étape | Action Zapier | Description |
|------|----------------|-------------|
| 1 | **Trigger** – *New Email* (Gmail) | Capture chaque email reçu dans la boîte `devis@votreentreprise.af`. |
| 2 | **Action** – *Zapier AI – Extract Structured Data* | Prompt : “Extrais le **nom**, le **téléphone**, l’**adresse** et le **produit** de ce texte.” |
| 3 | **Action** – *Create Record* (Airtable) | Enregistre les champs extraits dans la table `Devis`. |
| 4 | **Action** – *Send Slack Message* | Notifie l’équipe vente avec un résumé du devis. |

#### Prompt d’extraction (Zapier AI)  

```text
Texte : {{Email.Body}}
Extrais les informations suivantes sous forme JSON : 
{
  "nom": "...",
  "telephone": "...",
  "adresse": "...",
  "produit": "..."
}
```

Zapier renvoie le JSON que vous pouvez mapper directement aux colonnes Airtable.  

### 3. Générer automatiquement des emails de suivi  

**Cas d’usage** : après qu’un prospect a rempli le formulaire de contact, vous devez envoyer un email de remerciement personnalisé, incluant le nom du prospect et le produit d’intérêt.

#### Flux simplifié  

| Étape | Action Zapier | Description |
|------|----------------|-------------|
| 1 | **Trigger** – *New Record* (Airtable) | Déclenché dès qu’une ligne est ajoutée dans la vue `Leads`. |
| 2 | **Action** – *Zapier AI – Generate Text* | Prompt : “Rédige un email de remerciement en français, en mentionnant le prénom {{Nom}} et le produit {{Produit}}.” |
| 3 | **Action** – *Send Email* (Gmail) | Corps de l’email = texte généré à l’étape 2. |

#### Exemple de prompt  

```text
Rédige un email de 120 mots en français, ton professionnel mais chaleureux, 
en remerciant {{Nom}} d’avoir manifesté son intérêt pour {{Produit}}. 
Inclut une invitation à planifier un appel via le lien Calendly suivant : https://calendly.com/votreentreprise/appel.
```

Le résultat est immédiatement utilisable dans l’action d’envoi d’email.

---

## Scénario complet : De la demande de devis à la facturation automatisée  

### 4. Architecture du processus  

1. **Formulaire web** (Google Forms ou Typeform) → collecte des informations client.  
2. **Zapier Trigger** – *New Form Entry* → démarre le flux.  
3. **Zapier AI – Classify Intent** → détermine si la demande est *devis* ou *support*.  
4. **Airtable** – création d’une ligne dans la table `Devis` ou `Support`.  
5. **Webhooks → OpenAI** – génération d’un **PDF de devis** contenant le prix calculé à partir d’une grille tarifaire.  
6. **Gmail** – envoi du PDF au client et copie à l’équipe commerciale.  
7. **Slack** – notification instantanée à #ventes.  
8. **Paystack** – création d’une facture Paylink si le client accepte le devis.  

### 5. Implémentation pas à pas  

#### 5.1. Trigger – Formulaire  

*Choix de l’outil* : Typeform est largement utilisé en Afrique grâce à son interface mobile‑friendly et à sa prise en charge du français.  

- Créez le formulaire `Demande de devis` avec les champs : **Nom**, **Email**, **Téléphone**, **Produit**, **Quantité**.  
- Dans Zapier, sélectionnez **Typeform → New Entry** comme trigger.  

#### 5.2. Classification de la demande (Zapier AI)  

Certaines entreprises reçoivent à la fois des demandes de devis et des requêtes de support. Utiliser l’IA évite de créer deux Zaps distincts.

- **Action** : *Zapier AI – Classify Text*  
- Prompt :  

```text
Classifie le texte suivant en une des deux catégories : "Devis" ou "Support". Retourne uniquement le mot choisi.
Texte : {{FormResponse.Answers}}
```

- **Filtrer** : ajoutez un filtre Zapier qui ne poursuit le flux que si la réponse est « Devis ».  

#### 5.3. Enregistrement dans Airtable  

- **Action** : *Create Record* (Airtable) → table `Devis`.  
- Mappez chaque champ du formulaire aux colonnes correspondantes.  

#### 5.4. Calcul du montant total via IA  

Supposons que vous avez une grille tarifaire stockée dans une table `Tarifs` (Produit – PrixUnitaire). Vous pouvez récupérer le prix avec un **Lookup** Airtable, puis laisser l’IA calculer le total.

1. **Action** – *Find Record* (Airtable) → recherche du prix unitaire du produit.  
2. **Action** – *Zapier AI – Generate Text* (ou Webhook) avec le prompt :  

```text
Calcule le montant total pour la commande suivante : 
Produit = {{Produit}}, Quantité = {{Quantité}}, PrixUnitaire = {{PrixUnitaire}}. 
Retourne uniquement le chiffre, sans texte.
```

Le résultat (ex. : `12450`) sera stocké dans la variable `MontantTotal`.

#### 5.5. Génération du PDF de devis (Webhooks + OpenAI)  

OpenAI propose la fonction **`/v1/completions`** pour générer du texte, mais pour un PDF, on combine le texte généré avec un service de conversion (ex. : PDFShift).  

**Webhook** → POST vers `https://api.openai.com/v1/chat/completions`

```json
{
  "model": "gpt-4o-mini",
  "messages": [
    {"role": "system", "content": "Tu es un assistant qui génère des devis professionnels en français."},
    {"role": "user", "content": "Rédige un devis pour {{Nom}} ({{Email}}) concernant {{Quantité}} x {{Produit}} au prix unitaire de {{PrixUnitaire}} FCFA. Montant total = {{MontantTotal}} FCFA. Inclure les conditions de paiement et la validité 30 jours."}
  ],
  "temperature": 0.2
}
```

Le texte retourné est ensuite envoyé à PDFShift :

```json
{
  "source": "{{response.choices[0].message.content}}",
  "landscape": false,
  "use_print": true
}
```

PDFShift renvoie l’URL du PDF, que vous stockez dans Airtable (`LienPDF`).

#### 5.6. Envoi du devis par email  

- **Action** – *Send Email* (Gmail)  
- Destinataire : `{{Email}}`  
- Objet : “Votre devis n°{{RecordID}} – {{Produit}}”  
- Corps : texte généré à l’étape 5.5 + lien PDF (`{{LienPDF}}`).  

#### 5.7. Notification interne  

- **Action** – *Send Channel Message* (Slack)  
- Message :  

```
🧾 Nouveau devis créé – {{Nom}} ({{Produit}} x {{Quantité}})
Montant : {{MontantTotal}} FCFA – <{{LienPDF}}|Voir le PDF>
```

#### 5.8. Création d’une facture Paystack (optionnelle)  

Si le client accepte le devis, vous pouvez automatiser la création d’un **Paylink** Paystack.

- **Trigger** – *New Record* (Airtable) → vue `Devis Acceptés`.  
- **Action** – *Webhooks – POST* vers `https://api.paystack.co/transaction/initialize`  

```json
{
  "email": "{{Email}}",
  "amount": {{MontantTotal}} * 100,   // Paystack attend les kobo/centimes
  "currency": "XOF",
  "reference": "DEVIS-{{RecordID}}"
}
```

- **Action** – *Send Email* avec le lien de paiement (`authorization_url` retourné).  

---

## Bonnes pratiques pour des Zaps fiables  

### 6. Gestion des erreurs et des limites  

| Situation | Solution |
|-----------|----------|
| **Taux de dépassement** (ex. : plus de 100 000 actions/mois) | Activez le **Plan “Professional”** ou limitez les déclencheurs aux heures de forte activité. |
| **Échec d’une action** (ex. : API Paystack hors service) | Ajoutez un **Step “Delay”** puis **Retry** ou créez un **Zap de secours** qui envoie une alerte à Slack. |
| **Données manquantes** (ex. : numéro de téléphone absent) | Utilisez **Zapier AI – Generate Text** avec un prompt de type “Demande le numéro de téléphone au client si absent”. |

### 7. Sécurité et conformité  

* **Masquage des clés API** – stockez vos clés dans les **Variables d’environnement** de Zapier (Settings → Secrets).  
* **RGPD & protection des données** – ne conservez pas les pièces d’identité dans des services non‑chiffrés ; utilisez Airtable **Field Encryption** ou un stockage dédié (ex. : AWS S3 avec chiffrement SSE).  
* **Consentement** – ajoutez un champ « J’accepte le traitement de mes données » dans le formulaire et utilisez‑le comme condition de déclenchement.  

### 8. Localisation pour le marché africain  

* **Formats monétaires** – utilisez toujours le franc CFA (XOF) ou le naira (NGN) selon le pays. Ajoutez le symbole `FCFA` ou `₦` dans les prompts pour que l’IA le conserve.  
* **Fuseaux horaires** – Zapier travaille en UTC ; convertissez les dates avec la fonction `{{Zapier.formatDate}}` en `Africa/Abidjan` ou `Africa/Lagos`.  

```text
{{Zapier.formatDate(trigger.created_at, "DD/MM/YYYY HH:mm", "Africa/Abidjan")}}
```

* **Langue** – précisez toujours « en français » dans les prompts pour éviter les réponses en anglais.  

---

## Exemples de Zaps prêts à être reproduits  

### 9. Zap “Rappel de paiement”  

| Étape | Action | Détail |
|------|--------|--------|
| 1 | **Trigger** – *New Record* (Airtable, vue `Factures en attente` > 7 jours) | Filtre sur `Date d’échéance` < `today + 7`. |
| 2 | **Zapier AI – Generate Text** | Prompt : “Écris un SMS poli rappelant à {{Nom}} de régler la facture n°{{FactureID}} d’un montant de {{Montant}} FCFA avant le {{DateEcheance}}.” |
| 3 | **Action** – *SMS by Twilio* | Envoi du texte généré au `{{Téléphone}}`. |
| 4 | **Action** – *Update Record* (Airtable) | Marque la facture comme `Rappel envoyé`. |

### 10. Zap “Analyse de sentiment des avis clients”  

| Étape | Action | Détail |
|------|--------|--------|
| 1 | **Trigger** – *New Record* (Airtable, table `Avis`) | Chaque avis laissé sur le site. |
| 2 | **Zapier AI – Sentiment Analysis** | Prompt : “Analyse le sentiment du texte suivant et répond avec ‘positif’, ‘neutre’ ou ‘négatif’.” |
| 3 | **Action** – *Update Record* (Airtable) | Stocke le résultat dans le champ `Sentiment`. |
| 4 | **Action** – *Send Slack Message* (si négatif) | Alertes l’équipe support. |

---

## Points clés à retenir  

- **Décomposer le processus** en déclencheur, actions et points de contrôle avant de créer le Zap.  
- **Zapier AI** permet d’extraire, classer, résumer ou générer du texte ; les prompts doivent être courts, explicites et toujours préciser la langue.  
- **Webhooks + OpenAI** offrent une flexibilité maximale (paramètres de modèle, appels multiples) – idéal pour la génération de documents (PDF, devis).  
- **Sécuriser les clés** via les variables d’environnement et respecter les exigences de protection des données (RGPD, consentement).  
- **Adapter les formats** (monnaie,