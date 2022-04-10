[← Back](../README.md)

# Vente de produit

## Description

En tant que vendeur, je peux vendre un produit à un acheteur en sélectionant l'offre qui me conviens.

## Critères de succès

- L'acheteur de l'offre est spécifié pour la sélection.
- L'acheteur choisi a bel et bien fait une offre sur le produit.
- Auncune offre ne peut être fait sur un produit vendu.

## Détails techniques

### Requête

**Route**

`POST /products/{productId}/sell`

**Headers**

- `X-Seller-Id` : ID du vendeur qui créer le produit
  - type : `string`

**Body**

```ts
{
  username: string
}
```

**Example**

```json
{
  "username": "abc"
}
```

### Réponse

**Status**

`200 OK`

### Exceptions

- `MISSING_PARAMETER` si un champ de la requête est manquant.
- `NOT_PERMITTED` si le produit est déjà vendu.
- `ITEM_NOT_FOUND` si :
  - le vendeur associé à `X-Seller-Id` n'existe pas.
  - le produit associé à `productId` n'existe pas.
  - l'acheteur avec le nom d'utilisateur `username` n'a pas fait d'offre sur le produit.

## Changelog

- **12 mars 2023** : Changement de l'erreur `INVALID_PARAMETER` pour un `NOT_PERMITTED`.
