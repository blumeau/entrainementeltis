# Entraînement ELTiS · Hodos

Plateforme d'entraînement au test ELTiS (listening + reading) pour les jeunes Hodos.
Site statique : `index.html` + `audio/` + `img/`. Aucune dépendance, aucun serveur.

## Mise en ligne (GitHub Pages)
1. Dépôt `entrainementeltis` sur GitHub, branche `main`, contenu de ce dossier à la racine.
2. Settings → Pages → Source : « Deploy from a branch », branche `main`, dossier `/ (root)`.
3. Adresse : `https://blumeau.github.io/entrainementeltis/`.

## Réglages (en haut du script, objet `CONFIG` dans index.html)
- `webhook` : URL du webhook Make qui reçoit les résultats. Vide = rien n'est envoyé.
- `lockKey` : clé secrète. Si renseignée, la page ne s'ouvre qu'avec `?key=<lockKey>` (fourni par le bloc Softr).
- `requireStudent` : `true` pour refuser l'ouverture sans `?student=<email>` (donc hors Softr).

## Intégration Softr
Coller `softr-embed.html` dans un bloc « Custom code » d'une page de l'espace élève (utilisateurs connectés).
Le bloc construit l'iframe avec l'email et le nom de l'élève connecté.

## Résultats envoyés au webhook (un envoi par exercice validé, POST formulaire)
```
student, name            identité Softr (email, nom complet)
test, testLabel, mode    tb1 / "Test blanc 1" / listening ou reading
part, partTitle          partie du test
exercise                 identifiant de l'exercice (ex. P1_1)
score, total             résultat de l'exercice
answers                  réponses choisies (texte JSON)
testDone                 "true" quand le test complet est terminé
testScore, testTotal     score cumulé du test
at                       date ISO
```
Scénario Make « ELTiS résultat » : Custom webhook → Airtable « Create a record » (table Résultats ELTiS).

## Progression récupérée (GET progressWebhook?student=email)
Scénario Make « ELTiS progression » : Custom webhook → Airtable « Search records » (filtre sur Élève = student)
→ Array aggregator (Test, Exercice, Score, Total, Réponses, Date) → Webhook response (JSON du tableau,
en-tête Access-Control-Allow-Origin: *). La page fusionne avec la progression locale (le plus récent gagne).
