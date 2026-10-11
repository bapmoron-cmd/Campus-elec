# MON CAMPUS ÉLEC — V1

Plateforme pédagogique statique adaptée à Android et ordinateur, avec Firebase Authentication et Cloud Firestore.

## Fonctionnalités

- Compte individuel apprenti : e-mail + mot de passe, choix de formation.
- 4 formations : BTS Électrotechnique, BTS ATI, Bac Pro MELEC et CAP Électricien.
- Cours, fiches de révision, quiz autocorrigés (réponses expliquées).
- Score personnel, validation à 70 %, XP (meilleur score), niveaux et badges.
- Espace enseignant : création, modification et suppression des modules, quiz avec éditeur JSON.
- Responsive mobile et manifeste d'installation.

## Mise en ligne pas à pas

### 1. Créer un projet Firebase

1. Allez sur https://console.firebase.google.com/ et cliquez « Ajouter un projet ».
2. Dans **Build > Authentication > Sign-in method**, activez **Adresse e-mail / Mot de passe**.
3. Dans **Build > Firestore Database**, créez une base (choisissez une région adaptée, par exemple européenne). **N'utilisez pas le mode test pour déployer durablement.**
4. Ouvrez l'onglet **Règles** de Firestore : copiez l'intégralité de `firestore.rules`, puis publiez.
5. Dans les paramètres du projet, ajoutez une application **Web** et copiez les valeurs du `firebaseConfig` dans `firebase-config.js`. La configuration Web n'est pas un secret, contrairement aux clés de compte de service.

### 2. Créer le site GitHub Pages

1. Allez sur https://github.com/new et créez un dépôt nommé `mon-campus-elec`, **Public** pour GitHub Pages avec GitHub Free.
2. Décompressez l'archive, puis chargez **tous les fichiers du dossier `mon-campus-elec-v1` à la racine** du dépôt (Add file > Upload files > Commit changes). Ne chargez pas seulement l'archive ZIP.
3. Ouvrez **Settings > Pages > Build and deployment**. Source : **Deploy from a branch** ; branche **main** ; dossier **/(root)** ; Save.
4. L'URL de l'application sera `https://VOTRE-PSEUDO.github.io/mon-campus-elec/` (remplacez VOTRE-PSEUDO).
5. Dans Firebase > Authentication > Settings > Authorized domains, ajoutez `VOTRE-PSEUDO.github.io` (domaine sans `https://` et sans chemin).
6. Ouvrez l'URL du site : vous devez voir la page de connexion.

### 3. Créer votre compte administrateur

1. Sur le site, cliquez **Créer mon compte** avec **votre propre adresse e-mail** et choisissez une formation temporaire.
2. Dans Firebase > Authentication > Users, cliquez sur ce compte et copiez son **User UID**.
3. Dans Firebase > Firestore Database > Data : démarrez une collection `admins`, puis créez un **document dont l'ID est exactement votre User UID**. Ajoutez un champ `role` de type string avec la valeur `teacher`.
4. Rechargez le site et reconnectez-vous si nécessaire : le menu **Administration** apparaît.
5. Dans Administration, cliquez **Importer les 4 modules d’exemple** pour obtenir un exemple par formation. Vous pourrez ensuite créer vos véritables cours.

Important : **aucun apprenti ne doit pouvoir écrire dans la collection `admins`**. Les règles fournies interdisent toutes les modifications depuis l'application ; seul un administrateur de Firebase disposant des autorisations console peut ajouter un enseignant.

### 4. Inviter les apprentis

- Envoyez l'URL GitHub Pages aux apprentis ; chacun crée son propre compte et sélectionne sa formation.
- Sur Android, dans Chrome, ouvrez le menu ⋮ puis **Ajouter à l'écran d'accueil** / **Installer l'application** selon le navigateur. Le mode « installation » dépend du support du navigateur.
- Conseillez des mots de passe distincts de ceux de leurs autres comptes.

## Créer un exercice

L'enseignant ouvre Administration, saisit le titre, la formation, la synthèse, le contenu et les questions au format :

```json
[
  {
    "q": "Quelle est l'unité de la résistance ?",
    "options": ["Volt", "Ohm", "Ampère"],
    "answer": 1,
    "explanation": "La résistance s'exprime en ohms (Ω)."
  }
]
```

`answer` correspond à l'index de la bonne réponse en commençant à 0. Une même ressource peut comporter plusieurs questions.

## Sécurité, confidentialité et limites V1

- Les leçons sont lisibles par les personnes authentifiées (les quatre formations) : l'interface filtre les modules selon la section, **mais la séparation par section n'est pas une barrière de sécurité dans cette V1**.
- Chaque apprenti ne peut lire et écrire que sa progression (les administrateurs ont un droit de lecture). Les règles Firestore limitent les écritures.
- **Les scores sont corrigés dans le navigateur** : un utilisateur technique pourrait les falsifier. N'utilisez pas les résultats pour des notes certificatives / CCF. Un service serveur est nécessaire pour l'évaluation sécurisée.
- L'inscription est libre. Pour un établissement, prévoir un contrôle des inscriptions (comptes précréés, invitations et vérification d'appartenance), une politique de conservation, information RGPD et minimisation des données.
- Les PDF/fichiers ne sont pas importés dans cette V1 : les contenus sont du texte et des quiz. Intégration sécurisée de documents à prévoir en V2.
- Le lien GitHub Pages est public, mais les données Firestore nécessitent une authentification. Ne stockez aucun mot de passe ni secret de serveur dans GitHub.
- Les comptes administrateurs se configurent uniquement dans la console Firebase (pas de promotion libre depuis l'interface).
- Pour les contenus volumineux, séparez les modules ou passez à un stockage de documents adapté.
- L'application nécessite une connexion Internet (pas de service worker hors ligne).

## Structure

- `index.html` : page d'entrée
- `styles.css` : design responsive
- `app.js` : logique, quiz, progression et administration
- `firebase-config.js` : configuration publique de Firebase à compléter
- `firestore.rules` : règles de sécurité à coller dans Firebase
- `manifest.webmanifest` et `icon.svg` : installation mobile
- `exemples-cours.json` : modèle de module

## Dépannage

- « Configuration requise » : complétez `firebase-config.js` avec les valeurs Firebase exactes.
- `auth/unauthorized-domain` : ajoutez votre domaine GitHub Pages dans les domaines autorisés Firebase Authentication.
- `permission-denied` : publiez `firestore.rules` et vérifiez le document `admins/VOTRE_UID`.
- Pas de menu Admin : vérifiez que le document ID est exactement le UID de votre compte Firebase.
- Page blanche : vérifiez que tous les fichiers, et pas uniquement `index.html`, ont été chargés à la racine du dépôt.

## Documentation

- Firebase Web : https://firebase.google.com/docs/web/setup
- Firestore et règles : https://firebase.google.com/docs/firestore/security/get-started
- GitHub Pages : https://docs.github.com/fr/pages/getting-started-with-github-pages/creating-a-github-pages-site
