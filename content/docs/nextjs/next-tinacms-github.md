---
title: next-tinacms-github
id: /docs/nextjs/next-tinacms-github
next: /docs/nextjs/overview
prev: /docs/nextjs/next-tinacms-json
consumes:
  - file:
    details:
---

This package provides helpers for managing the github auth token for requests, as well as
providing helpers for loading content from the Github API.

## Installation

```bash
yarn add next-tinacms-github
```

## Getting Started

Any functions in the `pages/api` directory are are mapped to `/api/*` endpoints.

### `createCreateAccessTokenFn`

Helper for creating a `createCreateAccessToken` server function.

**pages/api/create-github-access-token.ts**

```js
import { createAuthHandler } from 'next-tinacms-github'

export default createAuthHandler(
  process.env.GITHUB_CLIENT_ID,
  process.env.GITHUB_CLIENT_SECRET
)
```

_See [Next's documentation](https://nextjs.org/docs/api-reference/next.config.js/environment-variables) for adding environment variables_

[See below](#github-oauth-app) for instructions on creating a Github OAuth App to generate these **Client ID** & **Client Secret** variables.

### `apiProxy`

Proxies requests to GitHub, attaching the GitHub access token in the process

**pages/api/proxy-github.ts**

```ts
import { apiProxy } from 'next-tinacms-github'

export default apiProxy
```

### `previewHandler`

Handles setting the the Nextjs [preview data](https://nextjs.org/docs/advanced-features/preview-mode) from your cookie data.

**pages/api/preview.ts**

```ts
import { previewHandler } from 'next-tinacms-github'

export default previewHandler
```

### Loading content from Github

The `preview` data, which gets set by calling your [preview function](#previewhandler), will be accessible through `getStaticProps` throughout your app.

**/blog/slug.ts**

```ts
import {
  getGithubPreviewProps
  parseMarkdown,
} from 'next-tinacms-github'

// ...

export const getStaticProps: GetStaticProps = async function({
  preview,
  previewData,
  ...ctx
}) {
  if (preview) {
    return getGithubPreviewProps({
      ...previewData,
      fileRelativePath: 'src/content/home.json',
      parse: parseMarkdown
    });
  }
  return {
    props: {
      sourceProvider: null,
      error: null,
      preview,
      file: {
        fileRelativePath: 'src/content/home.json',
        data: (await import('../content/home.json')).default,
      },
    },
  };
}
```