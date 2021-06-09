# Apollo REST Data Source

[![CI](https://github.com/StarpTech/apollo-datasource-rest/actions/workflows/ci.yml/badge.svg)](https://github.com/StarpTech/apollo-datasource-rest/actions/workflows/ci.yml)

Optimized REST Data Source for Apollo Server

- Optimized for JSON REST
- HTTP/1 [Keep-alive agents](https://github.com/node-modules/agentkeepalive) for socket reuse
- HTTP/2 support (requires Node.js 15.10.0 or newer)
- Uses [Got](https://github.com/sindresorhus/got) a modern HTTP Client shipped with:
  - Retry mechanism
  - Request cancellation
  - Timeout handling
  - RFC 7234 compliant HTTP caching
- LRU Cache with ttl to memoize GET requests within the same graphql request
- Support for [Apollo Cache Storage backend](https://www.apollographql.com/docs/apollo-server/data/data-sources/#using-memcachedredis-as-a-cache-storage-backend)

## Documentation

View the [Apollo Server documentation for data sources](https://www.apollographql.com/docs/apollo-server/features/data-sources/) for more details.

## Usage

To get started, install the `apollo-ds-rest` package:

```bash
npm install apollo-ds-rest
```

To define a data source, extend the [`RESTDataSource`](https://github.com/apollographql/apollo-server/tree/main/packages/apollo-datasource-rest) class and implement the data fetching methods that your resolvers require. Data sources can then be provided via the `dataSources` property to the `ApolloServer` constructor, as demonstrated in the _Accessing data sources from resolvers_ section below.

Your implementation of these methods can call on convenience methods built into the [RESTDataSource](./src/RESTDataSource.ts) class to perform HTTP requests, while making it easy to build up query, header, caching parameters, parse JSON results, and handle errors.

```ts
const { RESTDataSource } = require("apollo-ds-rest");

class MoviesAPI extends RESTDataSource {
  constructor() {
    // global client options
    super({
      timeout: 2000,
      http2: true,
      headers: {
        "X-Client": "client",
      },
    });
    this.baseURL = "https://movies-api.example.com";
  }

  cacheKey() {}

  // lifecycle hooks for logging, tracing and request manipulation
  didEncounterError() {}
  async willSendRequest() {}
  async didReceiveResponse() {}

  async getMovie(id) {
    return this.get(`/movies/${id}`, {
      headers: {
        "X-Foo": "bar",
      },
      timeout: 3000,
    });
  }
}
```
