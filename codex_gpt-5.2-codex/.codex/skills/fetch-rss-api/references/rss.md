# RSS and Atom Patterns (Node.js)

## Use when
- Fetching RSS or Atom feeds, polling updates, or normalizing feed items.

## Parser choice
- Use `rss-parser` for standard RSS or Atom feeds and simple custom fields.
- Use `fast-xml-parser` when the feed has unusual XML namespaces or requires custom traversal.

## Example: fetch and parse with rss-parser (ESM)

```js
import Parser from 'rss-parser';

const parser = new Parser();

export async function fetchFeed(url, { etag, lastModified } = {}) {
  const headers = {};
  if (etag) headers['If-None-Match'] = etag;
  if (lastModified) headers['If-Modified-Since'] = lastModified;

  const res = await fetch(url, { headers });
  if (res.status === 304) {
    return { items: [], etag, lastModified, notModified: true };
  }
  if (!res.ok) {
    const body = await res.text().catch(() => '');
    throw new Error(`RSS fetch failed: ${res.status} ${res.statusText} ${body}`);
  }

  const xml = await res.text();
  const feed = await parser.parseString(xml);

  return {
    items: feed.items || [],
    title: feed.title,
    etag: res.headers.get('etag') || etag,
    lastModified: res.headers.get('last-modified') || lastModified,
  };
}
```

## Example: parse with fast-xml-parser

```js
import { XMLParser } from 'fast-xml-parser';

const parser = new XMLParser({
  ignoreAttributes: false,
  attributeNamePrefix: '',
});

export function parseFeedXml(xml) {
  const data = parser.parse(xml);
  const channel = data.rss?.channel || data.feed;
  const items = channel?.item || channel?.entry || [];
  return Array.isArray(items) ? items : [items];
}
```

## Caching and conditional requests
- Store `ETag` and `Last-Modified` per feed URL.
- Send `If-None-Match` and `If-Modified-Since` on subsequent requests.
- Treat 304 as a no-op fetch and skip downstream processing.

## Normalization checklist
- Map items to a stable shape: `id`, `title`, `link`, `publishedAt`, `summary`, `author`, `source`.
- Prefer `guid` or `id` for identity; fall back to canonical link.
- Parse dates to ISO strings and sort descending.
