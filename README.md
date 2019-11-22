# HTTP Link Header

Parse & format HTTP link headers according to [RFC 8288].

This library is a port of [node-http-link-header] to an ES6 module which can be imported directly into the Browser or into a Deno script.

```html
<script type="module">
  import {Link} from 'https://raw.githubusercontent.com/matey-jack/http-link-header/master/lib/link.js';

  // TODO: find a publicly available API that has paging and can be used without authorization
  let url = "https://server.rest/some/paged/ressource/123";
  while (url) {
      const restResponse = await fetch(url, {headers});
      const body = await jobsResponse.json());
      // do something with 'body'
      url = getNextLink(jobsResponse);
  }

  function getNextLink(response) {
      const next = Link.parse(response.headers.get("link")).rel('next');
      if (!next.length) return null;
      return next[0].uri;
  }
</script>
```

If you need an NPM import, please use the upstream project. This project tracks all features and aims to provide just the EcmaScript module for Browsers and Deno. (Maybe in the future both can merge again.)

[RFC 8288]: https://tools.ietf.org/html/rfc8288
[node-http-link-header]: https://github.com/jhermsmeier/node-http-link-header

## Usage

### Parsing a HTTP link header

```js
var link = LinkHeader.parse(
  '<example.com>; rel="example"; title="Example Website", ' +
  '<example-01.com>; rel="alternate"; title="Alternate Example Domain"'
)

> Link {
  refs: [
    { uri: 'example.com', rel: 'example', title: 'Example Website' },
    { uri: 'example-01.com', rel: 'alternate', title: 'Alternate Example Domain' },
  ]
}
```

### Checking whether it has a reference with a given attribute & value

```js
link.has( 'rel', 'alternate' )
> true
```

### Retrieving a reference with a given attribute & value

```js
link.get( 'rel', 'alternate' )
> [
  { uri: 'example-01.com', rel: 'alternate', title: 'Alternate Example Domain' }
]
```
```js
// Shorthand for `rel` attributes
link.rel( 'alternate' )
> [
  { uri: 'example-01.com', rel: 'alternate', title: 'Alternate Example Domain' }
]
```

### Setting references

```js
link.set({ rel: 'next', uri: 'http://example.com/next' })
> Link {
  refs: [
    { uri: 'example.com', rel: 'example', title: 'Example Website' },
    { uri: 'example-01.com', rel: 'alternate', title: 'Alternate Example Domain' },
    { rel: 'next', uri: 'http://example.com/next' }
  ]
}
```

### Parsing multiple headers

```js
var link = new LinkHeader()

link.parse( '<example.com>; rel="example"; title="Example Website"' )
> Link {
  refs: [
    { uri: 'example.com', rel: 'example', title: 'Example Website' },
  ]
}

link.parse( '<example-01.com>; rel="alternate"; title="Alternate Example Domain"' )
> Link {
  refs: [
    { uri: 'example.com', rel: 'example', title: 'Example Website' },
    { uri: 'example-01.com', rel: 'alternate', title: 'Alternate Example Domain' },
  ]
}

link.parse( '<example-02.com>; rel="alternate"; title="Second Alternate Example Domain"' )
> Link {
  refs: [
    { uri: 'example.com', rel: 'example', title: 'Example Website' },
    { uri: 'example-01.com', rel: 'alternate', title: 'Alternate Example Domain' },
    { uri: 'example-02.com', rel: 'alternate', title: 'Second Alternate Example Domain' },
  ]
}
```

### Handling extended attributes

```js
link.parse( '</extended-attr-example>; rel=start; title*=UTF-8\'en\'%E2%91%A0%E2%93%AB%E2%85%93%E3%8F%A8%E2%99%B3%F0%9D%84%9E%CE%BB' )
```

```js
> Link {
  refs: [
    { uri: '/extended-attr-example', rel: 'start', 'title*': { language: 'en', encoding: null, value: '①⓫⅓㏨♳𝄞λ' } }
  ]
}
```

### Stringifying to HTTP header format

```js
link.toString()
> '<example.com>; rel=example; title="Example Website", <example-01.com>; rel=alternate; title="Alternate Example Domain"'
```

## Development

Aiming to be compatible with upstream. Please merge all feature-PRs there, we then port them here.

Development environment for this project is [Deno](https://deno.land/). Currently in progress of migrating all tests.

The following are working already:

    deno test --allow-net test/index.js
