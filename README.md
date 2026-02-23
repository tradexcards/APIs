# TCGPlayer API Reference

(tcgplayer! If you are reading this, please grant me your API access so I wont have to rely on scraping)

Reference documentation for TCGPlayer APIs used by the TradeX backend to fetch Pokemon card pricing data.

## Headers

All requests require a browser-like User-Agent:

```
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:147.0) Gecko/20100101 Firefox/147.0
```

## Endpoints

### Price Archives (TCGCSV)

Historical price data archives, available daily since 2024-02-08.

```
GET https://tcgcsv.com/archive/tcgplayer/prices-YYYY-MM-DD.ppmd.7z
```

### Product Details

```
GET https://mp-search-api.tcgplayer.com/v2/product/{product_id}/details
```

### Product Image

```
GET https://tcgplayer-cdn.tcgplayer.com/product/{product_id}_in_1000x1000.jpg
```

### Active Listings

```
POST https://mp-search-api.tcgplayer.com/v1/product/{product_id}/listings
Content-Type: application/json

{
  "filters": {
    "term": {
      "sellerStatus": "Live",
      "channelId": 0,
      "language": ["English"]
    },
    "range": {
      "quantity": { "gte": 1 }
    },
    "exclude": {
      "channelExclusion": 0
    }
  },
  "from": 0,
  "size": 50,
  "sort": { "field": "price", "order": "asc" },
  "context": { "shippingCountry": "US", "cart": {} },
  "aggregations": ["listingType"]
}
```

### Recent Sales

```
POST https://mpapi.tcgplayer.com/v2/product/{product_id}/latestsales
Content-Type: application/json

{
  "conditions": [1],
  "languages": [1],
  "variants": [],
  "listingType": "All",
  "limit": 100
}
```

**Condition codes:**
- `1` = Near Mint

**Language codes:**
- `1` = English

### Price History

```
GET https://infinite-api.tcgplayer.com/price/history/{product_id}/detailed?range=month
GET https://infinite-api.tcgplayer.com/price/history/{product_id}/detailed?range=annual
```

## TCGCSV Catalog API

Product catalog data from TCGCSV.

```
# All categories
GET https://tcgcsv.com/tcgplayer/categories

# Category groups (Pokemon = category 3)
GET https://tcgcsv.com/tcgplayer/3/groups

# Group products
GET https://tcgcsv.com/tcgplayer/3/{group_id}/products

# Group prices
GET https://tcgcsv.com/tcgplayer/3/{group_id}/prices
```

## Rate Limiting

TCGPlayer APIs are not public and may block excessive requests. The TradeX backend uses:

- Rotating user agents
- Request delays
- Optional proxy support (Bright Data)
- Response caching

## Related

- **TradeX API Docs:** https://tradex.cards/docs
- **Backend Implementation:** [`../backend/src/services/tcgplayer.ts`](../backend/src/services/tcgplayer.ts)
