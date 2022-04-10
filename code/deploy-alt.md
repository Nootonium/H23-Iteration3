[← Back](../README.md)

# Déploiement - Alternative

:warning: **Effectuez ces étapes si vous ne voulez pas utiliser fly.io en raison de la carte de crédit obligatoire.**

Afin de préparer le partage public officiel du produit, on vous demande de préparer votre application à un déploiement en utilisant le packaging Maven ainsi que Docker.

## Maven

Vous devez utiliser le `maven-assembly-plugin` afin de créer un fichier `.jar` qui contient toutes les dépendances (`jar-with-dependencies`). La configuration est assez simple, regardez des exemples sur le Web! 

:warning: **Important**: Afin de bien tester que votre `.jar` fonctionne, testez-le avec docker ou directement avec Java. Si vous le testez avec Java, assurez-vous de soit exécuter `mvn clean` avant, soit changer le `.jar` de dossier avant l'exécution, car s'il est mal configuré, il pourrait se lier dynamiquement aux dépendances externes.

## Docker

Vous devez fournir une configuration Docker multi-stage, c'est-à-dire qui contient les deux étapes suivantes :

1. Packaging avec une image contenant Maven
2. Exécution avec une image contenant une version minimal de la JRE (ou JDK) de Corretto (avec la bonne version)

Ainsi, la version finale de votre container doit être la plus minimale possible.

:warning: **Important**: Assurez-vous qu'on puisse y passer des variables d'environnement pour la base de donnée!

## Environnements

Afin de pouvoir tester l'application avant de l'offrir aux clients, on désire utiliser 2 environnements de déploiement, soit **staging** et **production**.

- **staging** représente une version de test permettant de vérifier que tout est fonctionnel et que l'intégration technologique s'est bien effectuée. Normallement, on cherche à rendre cet environnement le plus près possible de la production afin d'éviter toute surprise lors des mises en production.
- **production** représente la version officielle de votre application (qui est partagée au grand public). Lorsqu'on juge que staging fonctionne bien, on migre l'application vers cet environnement.

## Staging

1. Votre application devra de packager de façon automatique pour l'environnement staging à partir de la branche commune de votre répertoire Git, par exemple `dev` ou `main`, selon votre stratégie de branchage.
2. On devrait également pouvoir (re)packager manuellement n'importe quel **commit**, **branche** ou **tag** à *tout* moment. Vous devrez donc ajouter un *manual trigger* prenant en *input* le commit, la branche ou le tag à packager (voir l'action officielle [`checkout`](https://github.com/actions/checkout)). **Attention** : l'option `Use workflow from` du UI ne correspond **pas** à ce critère et change seulement la version du *fichier de pipeline* et **non** du *code à packager*!.
3. Finalement, l'étape de packaging devra seulement s'effectuer si vous tests passent. Vous pouvez utiliser la stratégie que vous voulez pour séparer les étapes de packaging de celles d'intégration (plusieurs workflow, 1 seul avec plusieurs jobs, 1 seule job, etc.).

## Production

Vous devrez donc créer un workflow différent pour le déploiement en production, qui ne pourra s'exécuter que **manuellement**, et comprends les mêmes jobs et critères de checkout que pour staging.

## Résumé

Vous devriez donc vous retrouver avec les éléments essentiels suivants :

- Configuration de packaging `.jar` ave dépendance dans le `pom.xml`
- Fichier `Dockerfile` pour docker, aec multi-stage build
- Configuration du CI pour packaging automatique de staging et manuel pour les 2 environnements
