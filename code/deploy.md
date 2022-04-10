[← Back](../README.md)

# Déploiement

Afin de préparer le partage public officiel du produit, on vous demande de déployer votre application sur la plateforme [Fly.io](https://fly.io/).

## Environnements de déploiement

Afin de pouvoir tester l'application avant de l'offrir aux clients, on désire utiliser 2 environnements de déploiement, soit **staging** et **production**.

- **staging** représente une version de test permettant de vérifier que tout est fonctionnel et que l'intégration technologique s'est bien effectuée. Normallement, on cherche à rendre cet environnement le plus près possible de la production afin d'éviter toute surprise lors des mises en production.
- **production** représente la version officielle de votre application (qui est partagée au grand public). Lorsqu'on juge que staging fonctionne bien, on migre l'application vers cet environnement.

## Déploiement automatique

1. Votre application devra déployer de façon automatique vers l'environnement staging à partir de la branche commune de votre répertoire Git, par exemple `dev` ou `main`, selon votre stratégie de branchage.
2. On devrait également pouvoir (re)déployer manuellement n'importe quel **commit**, **branche** ou **tag** à *tout* moment. Vous devrez donc ajouter un *manual trigger* prenant en *input* le commit, la branche ou le tag à déployer (voir l'action officielle [`checkout`](https://github.com/actions/checkout)). **Attention** : l'option `Use workflow from` du UI ne correspond **pas** à ce critère et change seulement la version du *fichier de pipeline* et **non** du *code à déployer*!.
3. Finalement, l'étape de déploiement devra seulement s'effectuer si vous tests passent. Vous pouvez utiliser la stratégie que vous voulez pour séparer les étapes de déploiement de celles d'intégration (plusieurs workflow, 1 seul avec plusieurs jobs, 1 seule job, etc.).

La configuration de base auto-générée par fly.io devrait être suffisante.

## Déploiement en production

Vous devrez donc créer un workflow différent pour le déploiement en production, qui ne pourra s'exécuter que **manuellement**, et comprends les mêmes jobs et critères de checkout que pour staging.

## Docker

Malheureusement, fly.io n'offre pas de runtime spécifique pour Java. Vous devez-donc mettre en place un container Docker pour la configuration de votre environnement de compilation et d'exécution. Cette configuration doit être en mesure d'exécuter votre code avec la commande maven `exec:java`.

Si vous le désirez, vous pouvez également mettre en place du packaging Java (`.jar`) qui n'a pas besoin de Maven pour l'exécution, ou encore un multi-stage build. Ces options sont beaucoup plus optimisées mais sont également plus complèxes à mettre en place.

## Résumé

Vous devriez donc vous retrouver avec les éléments essentiels suivants :

- Fichier `fly.toml` (possiblement un autre `.toml` pour staging) pour fly.io
- Fichier `Dockerfile` pour docker
- Configuration du CI pour déploiement automatique de staging et manuel pour les 2 environnements

## ⚠️ Requis additionnels (IMPORTANT!) ⚠️

Afin de tester votre déploiement, vous **devez** fournir vos URLS de déploiement fly.io pour les deux environnements (staging et production) dans votre fichier d'exercice.
