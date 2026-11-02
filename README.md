<p align="center">
  <a href="https://www.piloterr.com"><img src="images/banner.png" alt="Inet Scraper API by Piloterr – scrape Inet data as JSON" width="100%"></a>
</p>

# Inet Scraper API

[![Discord](https://img.shields.io/badge/Discord-Join%20the%20community-5865F2?logo=discord&logoColor=white)](https://discord.gg/CFj3ph8aHU) [![Get API key](https://img.shields.io/badge/Piloterr-Get%20a%20free%20API%20key-111827)](https://app.piloterr.com) [![API docs](https://img.shields.io/badge/Docs-OpenAPI-0EA5E9)](https://app.piloterr.com/openapi) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

The **Piloterr Inet Scraper API** lets you extract public data from Inet as **clean, structured JSON** — no proxies, headless browsers or HTML parsing to maintain. Endpoints available in production:

- **Inet Product API** — Read one Inet.se product by article number or URL, including SEK price, specs, and warehouse stock.
- **Inet Search API** — Search Inet.se by keyword or category.

Use it for price monitoring, assortment tracking, competitor analysis, product research or catalog enrichment.

| Endpoint | Path | Credits | What you get |
|---|---|---|---|
| [Inet Product API](#inet-product-api) | `/v2/inet/product` | 1 | Read one Inet.se product by article number or URL, including SEK price, specs, and warehouse stock. |
| [Inet Search API](#inet-search-api) | `/v2/inet/search` | 1 | Search Inet.se by keyword or category. Prices are inc-VAT SEK, with the next page URL when more results exist. |

## Table of contents

- [How it works](#how-it-works)
- [Inet Product API](#inet-product-api)
- [Inet Search API](#inet-search-api)
- [Code examples in other languages](#code-examples-in-other-languages)
- [Practical use cases](#practical-use-cases)
- [Why Piloterr?](#why-piloterr)
- [FAQ](#faq)
- [Related scrapers](#related-scrapers)
- [Support](#support)

## How it works

1. **Create a free Piloterr account** and copy your API key from the [dashboard](https://app.piloterr.com).
2. **Send a `GET` (or `POST`) request** to `https://api.piloterr.com/v2/...` with the header `x-api-key: YOUR_API_KEY`.
3. **Receive structured JSON** — Piloterr handles rendering, proxies, retries and parsing.

```bash
export PILOTERR_API_KEY="your-api-key"
pip install requests        # Python
# TypeScript: Node.js 18+ (native fetch), run with `npx tsx file.ts`
```

> Every endpoint accepts both `GET` (query string) and `POST` (JSON body with the same fields).

## Inet Product API

Read one Inet.se product by article number or URL, including SEK price, specs, and warehouse stock.

`GET | POST https://api.piloterr.com/v2/inet/product` · **1 credit per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Article number or product URL. Example: 4306287. A search link returns 400. |
| `return_page_source` | boolean | — | Set to true to get the raw HTML in result. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/inet/product",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "4306287. A search link returns 400.",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type InetProduct = {
  sku?: string;
  url?: string;
  brand?: string;
  price?: number;
  specs?: {
    name?: string;
    value?: string;
  }[];
  title?: string;
  images?: string[];
  rating?: number;
  category?: string;
  currency?: string;
  in_stock?: boolean;
  image_url?: string;
  highlights?: string[];
  is_bargain?: boolean;
  product_id?: string;
  breadcrumbs?: {
    url?: string;
    title?: string;
  }[];
  description?: string;
  review_count?: number;
  price_display?: string;
  selling_point?: string;
  stock_quantity?: number;
  purchase_status?: string;
  reference_price?: unknown;
  reference_price_display?: unknown;
};

const params = new URLSearchParams({
  "query": "4306287. A search link returns 400.",
});

const response = await fetch(`https://api.piloterr.com/v2/inet/product?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as InetProduct;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/inet/product
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "4306287. A search link returns 400."}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "sku": "MZ-V9V1T0BW",
  "url": "https://www.inet.se/produkt/4306287/samsung-990-m-2-nvme-ssd-1tb",
  "brand": "Samsung",
  "price": 2590,
  "specs": [
    {
      "name": "Minnestyp",
      "value": "Samsung V-NAND"
    },
    {
      "name": "Kontroller",
      "value": "Samsung in-house Controller"
    }
  ],
  "title": "Samsung 990 M.2 NVMe SSD 1TB",
  "images": [
    "https://cdn.inet.se/product/1600x900/4306287_1mqir6.png",
    "https://cdn.inet.se/product/1600x900/4306287_fhbdom.png"
  ],
  "rating": 4,
  "category": "Datorkomponenter > Hårddisk SSD > M.2 > PCIe Gen 4 > Samsung",
  "currency": "SEK",
  "in_stock": true,
  "image_url": "https://cdn.inet.se/product/1600x900/4306287_1mqir6.png",
  "highlights": [
    "Formfaktor: M.2",
    "PCIe Gen4 x4 NVMe 2.0"
  ],
  "is_bargain": false,
  "product_id": "4306287",
  "breadcrumbs": [
    {
      "url": "https://www.inet.se/kategori/31/datorkomponenter",
      "title": "Datorkomponenter"
    },
    {
      "url": "https://www.inet.se/kategori/591/harddisk-ssd",
      "title": "Hårddisk SSD"
    }
  ],
  "description": "Samsung 990 är en PCIe 4.0 NVMe SSD som kombinerar höga överförings­hastigheter med energieffektiv drift och generös lagringskapacitet. M…",
  "review_count": 1,
  "price_display": "2 590 kr",
  "selling_point": "Hög prestanda med låg energiförbrukning",
  "stock_quantity": 52,
  "purchase_status": "Purchasable",
  "reference_price": null,
  "reference_price_display": null
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `sku` | string |  |
| `url` | string |  |
| `brand` | string |  |
| `price` | integer |  |
| `specs[]` | object[] |  |
| `specs[].name` | string |  |
| `specs[].value` | string |  |
| `title` | string |  |
| `images` | string[] |  |
| `rating` | integer |  |
| `category` | string |  |
| `currency` | string |  |
| `in_stock` | boolean |  |
| `image_url` | string |  |
| `highlights` | string[] |  |
| `is_bargain` | boolean |  |
| `product_id` | string |  |
| `breadcrumbs[]` | object[] |  |
| `breadcrumbs[].url` | string |  |
| `breadcrumbs[].title` | string |  |
| `description` | string |  |
| `review_count` | integer |  |
| `price_display` | string |  |
| `selling_point` | string |  |
| `stock_quantity` | integer |  |

## Inet Search API

Search Inet.se by keyword or category. Prices are inc-VAT SEK, with the next page URL when more results exist.

`GET | POST https://api.piloterr.com/v2/inet/search` · **1 credit per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | What to look for. A word such as ssd, or an Inet search or category link. A product link returns 400. |
| `page` | number | — | Page number, starting at 1. Page 2 uses page=2. |
| `return_page_source` | boolean | — | Set to true to get the raw HTML in result. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/inet/search",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "ssd",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type InetSearch = {
  results?: {
    url?: string;
    brand?: string;
    price?: number;
    title?: string;
    rating?: number;
    currency?: string;
    in_stock?: boolean;
    image_url?: string;
    highlights?: string[];
    is_bargain?: boolean;
    product_id?: string;
    review_count?: number;
    price_display?: string;
    selling_point?: string;
    stock_quantity?: number;
    purchase_status?: string;
    reference_price?: unknown;
    reference_price_display?: unknown;
  }[];
  pagination?: {
    next?: string;
    page?: number;
    per_page?: number;
    total_pages?: number;
    total_results?: number;
  };
};

const params = new URLSearchParams({
  "query": "ssd",
});

const response = await fetch(`https://api.piloterr.com/v2/inet/search?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as InetSearch;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/inet/search
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "ssd"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "results": [
    {
      "url": "https://www.inet.se/produkt/4306208/crucial-x10-portable-ssd-2tb",
      "brand": "Crucial",
      "price": 2999,
      "title": "Crucial X10 Portable SSD 2TB",
      "rating": 4.8,
      "currency": "SEK",
      "in_stock": true,
      "image_url": "https://cdn.inet.se/product/86x86/4306208_hirbw9.jpg",
      "highlights": [
        "Storlek: 2 TB",
        "Tillverkarens garanti: 3 år"
      ],
      "is_bargain": false,
      "product_id": "4306208",
      "review_count": 4,
      "price_display": "2 999 kr",
      "selling_point": "Snabb och pålitlig",
      "stock_quantity": 59,
      "purchase_status": "Purchasable",
      "reference_price": null,
      "reference_price_display": null
    }
  ],
  "pagination": {
    "next": "https://www.inet.se/hitta?q=ssd&page=2",
    "page": 1,
    "per_page": 60,
    "total_pages": 7,
    "total_results": 381
  }
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `results[]` | object[] |  |
| `results[].url` | string |  |
| `results[].brand` | string |  |
| `results[].price` | integer |  |
| `results[].title` | string |  |
| `results[].rating` | number |  |
| `results[].currency` | string |  |
| `results[].in_stock` | boolean |  |
| `results[].image_url` | string |  |
| `results[].highlights` | string[] |  |
| `results[].is_bargain` | boolean |  |
| `results[].product_id` | string |  |
| `results[].review_count` | integer |  |
| `results[].price_display` | string |  |
| `results[].selling_point` | string |  |
| `results[].stock_quantity` | integer |  |
| `results[].purchase_status` | string |  |
| `results[].reference_price` | object |  |
| `results[].reference_price_display` | object |  |
| `pagination` | object |  |
| `pagination.next` | string |  |
| `pagination.page` | integer |  |
| `pagination.per_page` | integer |  |
| `pagination.total_pages` | integer |  |
| `pagination.total_results` | integer |  |

## Code examples in other languages

Ready-to-run examples live in the [`examples/`](examples) folder. Each one reads your key from the `PILOTERR_API_KEY` environment variable.

| Language | Product | Search |
|---|---|---|
| cURL (Bash) | [`product`](examples/curl/product.sh) | [`search`](examples/curl/search.sh) |
| PHP | [`product`](examples/php/product.php) | [`search`](examples/php/search.php) |
| C# | [`product`](examples/csharp/Product.cs) | [`search`](examples/csharp/Search.cs) |
| Go | [`product`](examples/go/product.go) | [`search`](examples/go/search.go) |
| Java | [`product`](examples/java/ProductExample.java) | [`search`](examples/java/SearchExample.java) |
| Ruby | [`product`](examples/ruby/product.rb) | [`search`](examples/ruby/search.rb) |
| Node.js | [`product`](examples/nodejs/product.mjs) | [`search`](examples/nodejs/search.mjs) |

## Practical use cases

- **Price monitoring** — Track prices, promotions and availability over time to react to competitors.
- **Assortment & catalog tracking** — Follow new products, delisted items and category changes.
- **Competitor analysis** — Benchmark ranges, brands and positioning across retailers.
- **Product research** — Spot best-sellers and trends from search results.
- **Catalog enrichment** — Pull titles, images and attributes into your PIM or marketplace listings.

## Why Piloterr?

- **No infrastructure** — no proxies, headless browsers or CAPTCHA solving to run yourself.
- **Structured JSON** — stable, documented fields instead of brittle HTML selectors.
- **Pay per request** — transparent credit cost per endpoint, failed requests are retried for you.
- **One API key, hundreds of sources** — Inet plus many other e-commerce, search and company data endpoints in the [Piloterr library](https://www.piloterr.com/library).
- **Any language** — plain HTTPS + JSON, with examples for Python, TypeScript, cURL, PHP, C#, Go, Java, Ruby and Node.js.

## FAQ

### Is it legal to scrape Inet?

Collecting publicly available data is generally allowed in many jurisdictions, but rules depend on your country, the data you collect and how you use it. Avoid personal data unless you have a lawful basis, respect the website's terms where they apply, and get legal advice for your specific use case. This is not legal advice.

### Do I need proxies or a headless browser?

No. Piloterr handles rendering, proxy rotation and retries on its side. You only send an HTTPS request with your `x-api-key` header and receive JSON.

### How much does a request cost?

Credits per call: `/v2/inet/product` **1**, `/v2/inet/search` **1**. See the [Piloterr website](https://www.piloterr.com) for plans.

### What data does the Inet API return?

Each endpoint returns structured JSON: **Inet Product** — `sku`, `url`, `brand`, `price`, `specs[]`, `specs[].name`; **Inet Search** — `results[]`, `results[].url`, `results[].brand`, `results[].price`, `results[].title`, `results[].rating`.

### How do I paginate Inet results?

Use the `page` parameter and keep requesting while the response indicates more results.

### Can I get the raw HTML instead of JSON?

Yes. Set `return_page_source=true` and the response becomes `{"result": "..."}` with the page source.

### Which languages are supported?

Any language that can make an HTTP request. This README shows Python and TypeScript; the [`examples/`](examples) folder contains cURL, PHP, C#, Go, Java, Ruby and Node.js.

## Related scrapers

- [About You Scraper API](https://github.com/piloterr/aboutyou-scraper)
- [Action Scraper API](https://github.com/piloterr/action-scraper)
- [Aldi Scraper API](https://github.com/piloterr/aldi-scraper)
- [Alibaba Scraper API](https://github.com/piloterr/alibaba-scraper)
- [AliExpress Scraper API](https://github.com/piloterr/aliexpress-scraper)
- [Allegro Scraper API](https://github.com/piloterr/allegro-scraper)
- [All Piloterr scrapers on GitHub](https://github.com/piloterr)

## Support

- 💬 Join the [Piloterr Discord](https://discord.gg/CFj3ph8aHU) to ask questions and share what you build.
- 📚 Browse the [API reference (OpenAPI)](https://app.piloterr.com/openapi) and the [Piloterr library](https://www.piloterr.com/library).
- 🔑 [Get your API key](https://app.piloterr.com).

---

<p align="center">
  <a href="https://www.piloterr.com">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="images/logo-dark.svg">
      <img src="images/logo.svg" alt="Piloterr" height="32">
    </picture>
  </a>
  <br>
  <sub>Built by <a href="https://www.piloterr.com">Piloterr</a> — web scraping APIs for e-commerce, search, real estate and more, one API key.</sub>
</p>

<sub>Inet is a trademark of its respective owner. This repository is not affiliated with or endorsed by Inet. Examples are released under the MIT license.</sub>
