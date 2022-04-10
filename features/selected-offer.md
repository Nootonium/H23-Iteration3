[← Back](../README.md)

# Offre sélectionnée

## Description

En tant que vendeur, je vois les informations de vente sur mes produits.

## Critères de succès

- Si le produit n'est pas vendu, aucune information sur la vente n'est visible.

## Détails techniques

### Réponse

**Route**

`GET /sellers/{sellerId}"`

**Body**

```ts
{
  ...,
  selectedOffer: null | {
    username: String,
    amount: Amount,
  }
}
```

**Example - vendu**

```json
{
  ...,
  "selectedOffer": {
    "username": "abc",
    "amount": 54.24,
  }
}
```

**Example - non vendu**

```json
{
  ...,
  "selectedOffer": null
}
```
