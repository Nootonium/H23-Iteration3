[← Back](../README.md)

# Tests

## Tests d'intégration

Toutes vos bases de données doivent être testées par des tests d'**intégration**, c'est-à-dire des tests qui utilisent les vraies implémentations **sans aucun mock**. Cela vous permet de mieux tester votre intégration avec la technologie. Assurez-vous de bien utiliser les `@BeforeEach`, `@BeforeAll`, `@AfterEach` et `@AfterAll` au besoin.

## Tests e2e

Afin de vérifier que votre application répond aux exigences fonctionnelles, vous devez effectuer des tests e2e, c'est-à-dire **sans aucun mock** et **tel qu'un utilisateur l'utiliserait**.

Voici la liste des tests à effectuer :

1. Création d'un vendeur :
   1. Avec succès
   2. Avec 1 champ manquant (n'importe quel)
   3. Avec 1 champ vide (n'importe quel)
   4. Avec date de naissance invalide
2. Création d'un produit :
   1. Avec succès
   2. Avec 1 champ manquant (n'importe quel)
   3. Avec 1 champ vide (n'importe quel)
   4. Avec prix invalide
3. Obtention d'un vendeur avec produit :
   1. Qui existe
   2. Qui n'existe pas

Pour chacun des tests, vous devez vérifier :

- Le statut de retour
- Le body de réponse
- Les headers, s'il y a lieu

### Détails additionnels

Vous pouvez utiliser la librairie de votre choix, mais ça doit être en Java. Vous devez demander la permission si vous utilisez une librairie autre que assertj, google-truth, hamcrest, junit, rest-assured, jersey-client ou jersey-test.
