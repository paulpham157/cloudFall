# REST API Patterns (Node.js)

## Use when
- Calling REST endpoints, handling auth, pagination, rate limits, or retries.

## Fetch wrapper with timeout and JSON parsing

```js
export async function fetchJson(url, {
  method = 'GET',
  headers = {},
  body,
  timeoutMs = 10000,
} = {}) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeoutMs);

  try {
    const res = await fetch(url, { method, headers, body, signal: controller.signal });
    const contentType = res.headers.get('content-type') || '';
    const payload = contentType.includes('application/json')
      ? await res.json()
      : await res.text();

    if (!res.ok) {
      const error = new Error(`HTTP ${res.status} ${res.statusText}`);
      error.status = res.status;
      error.headers = res.headers;
      error.body = payload;
      throw error;
    }

    return payload;
  } finally {
    clearTimeout(timeoutId);
  }
}
```

## Retry and backoff helper

```js
const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

export async function fetchWithRetry(url, options, {
  retries = 3,
  backoffMs = 500,
} = {}) {
  let attempt = 0;
  while (true) {
    try {
      return await fetchJson(url, options);
    } catch (err) {
      attempt += 1;
      const status = err.status || 0;
      const retryable = status === 429 || status >= 500 || status === 0;
      if (!retryable || attempt > retries) throw err;

      const retryAfterHeader = err?.headers?.get?.('retry-after');
      const retryAfterSeconds = retryAfterHeader ? Number(retryAfterHeader) * 1000 : 0;
      const retryAfterDate = retryAfterHeader && Number.isNaN(Number(retryAfterHeader))
        ? Math.max(0, new Date(retryAfterHeader).getTime() - Date.now())
        : 0;
      const delay = Math.max(retryAfterSeconds, retryAfterDate, backoffMs * 2 ** (attempt - 1));
      await sleep(delay);
    }
  }
}
```

## Authentication patterns

- **API key (header)**: `headers['Authorization'] = 'Bearer ' + process.env.API_KEY`
- **API key (query)**: `params.set('api_key', process.env.API_KEY)`
- **Basic auth**: `headers['Authorization'] = 'Basic ' + Buffer.from(user + ':' + pass).toString('base64')`
- **OAuth**: Ask for token refresh flow or client credentials details.

## Pagination patterns

- **Page-based**: `?page=1&per_page=50`
- **Offset-based**: `?offset=0&limit=100`
- **Cursor-based**: `?cursor=abc123`
- **Link header**: Parse `Link: <url>; rel="next"`

## Rate limit handling

- Honor `Retry-After` when present.
- Watch `X-RateLimit-Remaining` and `X-RateLimit-Reset`.
- Backoff on 429 or 503; log retry attempts.

## Output and schema

- Normalize records to a consistent shape before returning.
- Return both raw API response and normalized data when helpful.
