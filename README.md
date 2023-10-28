# Kahoot Captain Correct

Ce script vous permet d'obtenir systématiquement des réponses correctes sur le site Kahoot et d'accumuler un maximum de points, indépendamment de la rapidité de votre réponse ou du choix que vous faites. La seule condition est que vous soumettiez au moins une réponse.

## Table des matières

- [Installation](#installation)
- [Utilisation](#utilisation)
- [À venir](#à-venir)

## Installation

1. Téléchargez l'extension de navigateur [Requestly](https://requestly.io/downloads).

2. Créez une nouvelle règle de type `Modify Request Body`.

3. Dans la condition, précisez : `URL` `Contient` `https://kahoot.it/rest/challenges/`.

4. Dans la section `Programmatic (JavaScript)`, insérez le code suivant :

```javascript
function modifyRequestBody(args) {
  const { method, url, body, bodyAsJson } = args;
  let newBody;
  newBody = bodyAsJson;
  let tmpArray = [];

  if (method === "POST") {
    if (typeof newBody["question"]["answers"][0]["isCorrect"] !== "undefined") {
      for (let i = 0; i < newBody["question"]["choices"].length; i++) {
        if (newBody["question"]["choices"][i]["correct"] == true) {
          newBody["question"]["answers"][0]["text"] = newBody["question"]["choices"][i]["answer"];
          newBody["question"]["answers"][0]["choiceIndex"] = i;
          tmpArray.push(i);

          newBody["question"]["answers"][0]["isCorrect"] = true;

          // Génère un nombre aléatoire dans une intervalle (pour limiter les soupçons de triche)
          var max = 999;
          var min = 960;
          newBody["question"]["answers"][0]["points"] = ~~(new Date().getMilliseconds() / 1000 * (max - min) + min);
          // newBody["question"]["answers"][0]["points"] = 999;
        }
      }
      if (typeof newBody["question"]["answers"][0]["selectedChoices"] !== "undefined") {
        newBody["question"]["answers"][0]["selectedChoices"] = tmpArray;
        newBody["question"]["answers"][0]["choiceIndex"] = -5;
      }
    }
  }
  return newBody;
}
```

5. Cliquez sur l'icône de filtre avancé à droite et choisissez `POST` dans le champ `Méthode de la requête`.

6. Activez la nouvelle règle.

## Utilisation

Une fois que vous avez suivi les étapes d'installation, voici comment utiliser le script :

1. Allez sur Kahoot et rejoignez un jeu.

2. Lorsqu'une question est posée, choisissez n'importe quelle réponse.

3. Le script se chargera automatiquement de faire en sorte que votre réponse soit correcte et d'augmenter votre nombre de points.

Veuillez noter que votre affichage affichera probablement que vous avez répondu incorrectement, mais en réalité, le script a modifié avec succès la requête lors de son envoi.

## À venir

Adapter le code pour [Burp](https://portswigger.net/burp/communitydownload) car certaines fonctionnalités de Requestly sont devenues payantes.
