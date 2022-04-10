[← Back](../README.md)

# Health V2

Afin de correctement signaler que l'application se porte bien, vous devez maintenant inclure une vérification de la connexion à la base de données.

## Détails techniques

### Requête

**Route**

`GET /health`

### Réponse

**Status**

- `200 OK` : tout est fonctionnel.
- `500 Server Error` : au moins un des services n'est pas fonctionnel.

**Body**

```ts
{
    api: boolean,
    db: boolean
}
```

**Exemple - Succès**

```json
{
    "api": true,
    "db": true
}
```

**Exemple - Échec db**

```json
{
    "api": true,
    "db": false
}
```
