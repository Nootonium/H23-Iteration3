[← Back](../README.md)

# Connexion à une base de données externe

## Variables d'environnement

:warning: Ces variables d'environnement sont **requises** et seront utilisées pour la correction automatique. Ce sont vos environnements locaux et remote qui devront mettre en place les bonne variables d'environnement pour utiliser les bons URLs et bases de données.

| Nom                        | Description                                                                                                                                                          |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `FLOPPA_MONGO_CLUSTER_URL` | URL complet utilisé pour la connexion au cluster de bases de données. Contient le protocole, le username, le password, le host, le port et possiblement les options. |
| `FLOPPA_MONGO_DATABASE`    | Base de donnée à laquelle se connecter.                                                                                                                              |

## Description

Afin de conserver l'information à plus long terme, vous devez vous connecter à une base de données externe. Pour ce faire, le service en ligne [MongoDB Atlas](https://www.mongodb.com/atlas) sera utilisé. Celui-ci utilise des bases de données [MongoDB](https://www.mongodb.com/) basées sur des *documents*. L'utilisation de l'ODM (Object Document Mapper) [Morphia](https://morphia.dev/morphia/2.3/index.html) permettra de faciliter et d'automatiser plusieurs tâches reliées à l'utilisation de la BD. Vous devrez y stocker l'ensemble de vos données.

## Installation

Vous aurez besoin des dépendances Maven de Morphia ainsi que possiblement le [driver Java pour MongoDB](https://mvnrepository.com/artifact/org.mongodb/mongodb-driver-sync).

Vous aurez également besoin de vous créer un [compte MongoDB Atlas](https://www.mongodb.com/) (gratuit). Si vous voulez tester votre application locallement, vous pouvez également [installer une instance locale de Mongo](https://docs.mongodb.com/manual/installation/) ou **encore mieux**, mettre en place une configuration `docker-compose`, que vous verrez dans le laboratoire.

## Connexion

La connexion à la base de données (cluster MongoDB) demande d'abord la création d'un client Mongo (`com.mongodb.client.MongoClients`), puis d'un datastore Morphia (`dev.morphia.Datastore`). Les documentations à cet effet devraient suffir. 

- Documentation [MongoDB Driver](https://docs.mongodb.com/drivers/java/sync/v4.5/fundamentals/connection/connect/#std-label-connect-to-mongodb)
- Documentation [Morphia](https://morphia.dev/morphia/2.2/quicktour.html#_setting_up_morphia)

Où :

- `applyConnectionString` indique l'URL du cluster de base de données (adresse Atlas ou locale).
- `serverSelectionTimeout` et `maxConnectionIdleTime` permettent de largement réduire les temps de timeout (utilse pour le [`healthcheck`](../features/0.healtv2.md), 1 seconde au lieu de 30).
- `mongoClient`
- `mongoUrl` représente l'URL du cluster d'Atlas ou local.
  - L'URL locale est `mongodb://localhost`
  - Le port par défault est souvent `27017`
  - Pour l'**URL** d'Atlas, **vous n'avez pas besoin d'indiquer la base de donnée** après le dernier `/`. On vous conseille également de **conserver les options par défaut** qui sont incluses dans l'URL, soit `?retryWrites=true&w=majority`.

## ⚠️ Configuration (IMPORTANT!) ⚠️

Puisque vous [déployez votre application](./deploy.md) sur plusieurs environnements, vous devrez naturellement utiliser des base de données différentes pour chacun! En effet, `staging` représente un *playground* pour les développeurs, où n'importe quelle donnée peut être crée sans soucis d'affecter les clients. Les données de l'environnement de `production` doivent donc être séparées. Finalement, les **tests** ne devraient **jamais** s'exécuter sur les bases de données Atlas (en ligne), mais plutôt localement, sinon ça coûtera cher! Les bases de données de test et pour `staging` peuvent donc être effacées n'importe quand sans aucun problème.

Voici donc les configurations que vous aurez besoin pour chaque environnement :

- `staging` : base de données `floppa-staging` sur Atlas
- `production` : base de données `floppa-production` sur Atlas
- `test` / `local` (non déployé, dans le CI ou poste local) : base de données `floppa-dev` locale

## Morphia

Morphia permet de *mapper* vos classes Java vers des objets représentatifs simples ("documents") pour la base de données. Il sera donc primordial de respecter le même principe que pour vos réponses du UI, c'est-à-dire de créer des classes propres aux documents MongoDB qui contiendront des champs de **primitives seulement** ainsi que les annotations Morphia. La documentation contient beaucoup d'informations pertinantes.

## Conseils et astuces

### Modifier le timeout de connexion

Dans le cas d'une connexion non-fonctionnelle, le driver de MongoDB attendra 30 secondes avant d'échouer, ce qui peut être particulièrement long. Il est donc possible de configurer le client MongoDB à l'aide du `MongoClientSettings` builder. Afin de réduire le timeout, vous pouvez modifier les paramètres de `serverSelectionTimeout` (dans les options de `cluster`) et de `maxConnectionIdleTime` (dans les options de `connectionPool`). Vous aurez alors également besoin de configurer l'URL à l'aide du builder. Le builder supporte également les options incluses directement dans l'URL, que vous pouvez alors conserver tel que MongoDB Atlas vous fournis. Faites attention à ne pas trop réduire ce timeout, car la connexion peut parfois prendre quelques secondes.

### Healthcheck au démarrage

Parfois, la connexion à MongoDB se fait de façon *lazy*, c'est-à-dire que le driver attendra qu'une requête soit effectuée avant de vérifier l'état de la connexion. Il est donc utile de vouloir vérifier cet état à l'aide d'une requête simple (ex : `.listCollectionNames().first()`), ce qui forcera la connexion, et donc l'échec du healthcheck lors du déploiement. Il est important de faire `.first()` puisque l'itérateur retourné est lui aussi *lazy*, donc la requête n'est pas lancée tant que l'itérateur n'est pas itéré.

### Tests

Puisque la base de données est maintenant persistante, il arrivera probablement que les tests échoueront ou deviendront fautifs. Afin de corriger le problème, on peut sans problème effacer la base de données (`.dropDatabase()`) ou des collections entières (`.dropCollection()`) après les tests qui utilisent la base de données (tests e2e, d'intégration, etc.). D'où l'importance d'utiliser des bases de données différentes selon les environnement de déploiement!

### Protection de la base de données de production

Afin de protéger les bases de données importantes (ex : production), il est important de soignement choisir les permissions. Être le plus restrictif possible permet de :

- Prévenir les dégâts en cas de piratage
- Prévenir les erreurs de développement (ex : effaçace de la base de données de production!)
- Découper les permissions selon les rôles dans l'entreprise (ex : CTO vs. stagiaire)
