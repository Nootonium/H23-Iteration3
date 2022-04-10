[← Back](../README.md)

# Statut de vente

En tant qu'utilisateur, je peux voir le statut de vente des produits.

## Critères de succès

- Le statut est `ongoing` lorsque le produit est créé.
- Le status est `sold` lorsque le produit est vendu.

## Détails techniques

### Requête

**Routes**

- `GET /sellers/{sellerId}`
- `GET /products`

### Réponse

**Body**

```ts
{
  ...,
  saleStatus: SaleStatus
}
```

**Exemple**

```json
{
  ...,
  "saleStatus": "ongoing"
}
```
