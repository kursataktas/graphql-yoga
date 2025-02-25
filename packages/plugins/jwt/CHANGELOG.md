# @graphql-yoga/plugin-jwt

## 3.3.0

### Patch Changes

- [#3426](https://github.com/dotansimha/graphql-yoga/pull/3426)
  [`076d25c`](https://github.com/dotansimha/graphql-yoga/commit/076d25c4143e009d73e77ef99deca317ab19633f)
  Thanks [@EmrysMyrddin](https://github.com/EmrysMyrddin)! - Fix typo of the option
  `singingKeyProviders` => `signingKeyProviders`.

- Updated dependencies
  [[`2523d9f`](https://github.com/dotansimha/graphql-yoga/commit/2523d9fa954b82e11412918aab2ae6fe7e7611d6),
  [`87ee333`](https://github.com/dotansimha/graphql-yoga/commit/87ee333724c0c6e0b9f72aa50e38a0a8a080593f)]:
  - graphql-yoga@5.9.0

## 3.2.0

### Patch Changes

- Updated dependencies
  [[`18fe916`](https://github.com/dotansimha/graphql-yoga/commit/18fe916853fc6192b8b2a607f91b67f3a7cae7bc),
  [`6bb19ed`](https://github.com/dotansimha/graphql-yoga/commit/6bb19edf5b103d6d9b6088e2e22cfa71a85f26f7)]:
  - graphql-yoga@5.8.0

## 3.1.0

### Patch Changes

- Updated dependencies
  [[`5dae4ab`](https://github.com/dotansimha/graphql-yoga/commit/5dae4abeb6a4aa82f396a19d31d0155fe10bc752),
  [`5dae4ab`](https://github.com/dotansimha/graphql-yoga/commit/5dae4abeb6a4aa82f396a19d31d0155fe10bc752),
  [`5dae4ab`](https://github.com/dotansimha/graphql-yoga/commit/5dae4abeb6a4aa82f396a19d31d0155fe10bc752),
  [`5dae4ab`](https://github.com/dotansimha/graphql-yoga/commit/5dae4abeb6a4aa82f396a19d31d0155fe10bc752)]:
  - graphql-yoga@5.7.0

## 3.0.2

### Patch Changes

- Updated dependencies
  [[`0866c1b`](https://github.com/dotansimha/graphql-yoga/commit/0866c1be8868eb891a0a62e36c9270d87f205330)]:
  - graphql-yoga@5.6.3

## 3.0.1

### Patch Changes

- Updated dependencies
  [[`b7bf47b`](https://github.com/dotansimha/graphql-yoga/commit/b7bf47bf72f3c04de6de7866aa68cdd5eac90566),
  [`81a736b`](https://github.com/dotansimha/graphql-yoga/commit/81a736be76cb91049fc9ef54f536ce79e0c90e16)]:
  - graphql-yoga@5.6.2

## 3.0.0

### Major Changes

- [#3366](https://github.com/dotansimha/graphql-yoga/pull/3366)
  [`057ad06`](https://github.com/dotansimha/graphql-yoga/commit/057ad0678cc25f2f0dac403280c2882dfe532edb)
  Thanks [@dotansimha](https://github.com/dotansimha)! - Re-write for the JWT plugin. This plugin
  can be configured now with multiple providers, lookup locations, token verification, and more.

  The version has better version coverage, and it provides an improved API for configuring provider
  and custom behaviors.

  ## Breaking Change: New Plugin Configuration

  ### Signing key providers

  ❌ The `signingKey` option has be removed. ❌ The `jwksUri` + `jwksOpts` options has been removed.
  ✅ Multiple signing key providers and support for fallbacks (`singingKeyProviders[]`). ✅ Improved
  API for defining signing key configuration. ✅ Better defaults for caching and rate-limiting for
  remote JWKS providers.

  #### Before

  ```ts
  useJWT({
    signingKey: "...",
    // or
    jwksUri: "http://example.com/..."
    jwksOpts: {
      // ...
    }
  })
  ```

  #### After

  ```ts
  import {
    createInlineSigningKeyProvider,
    createRemoteJwksSigningKeyProvider,
    useJWT
  } from '@graphql-yoga/plugin-jwt'

  useJWT({
    // Pass one or more providers
    singingKeyProviders: [
      createRemoteJwksSigningKeyProvider({
        // ...
      })
      // This one also acts as a fallback in case of a fetching issue with the 1st provider
      createInlineSigningKeyProvider({ signingKey: "..."})
    ]
  })
  ```

  ### Improved Token Lookup

  ❌ Removed `getToken` option from the root config. ✅ Added support for autmatically extracting
  the JWT token from cookie or header. ✅ Easier setup for extracting from multiple locations. ✅
  `getToken` is still available for advanced use-cases, you can pass a custom function to
  `lookupLocations`.

  #### Before

  ```ts
  useJWT({
    getToken: payload => payload.request.headers.get('...')
  })
  ```

  #### After

  With built-in extractors:

  ```ts
  imoprt { extractFromHeader, extractFromCookie, useJWT } from '@graphql-yoga/plugin-jwt'

  const yoga = createYoga({
    // ...
    plugins: [
      useCookies(), // Required if "extractFromCookie" is used.
      useJWT({
        lookupLocations: [
          extractFromHeader({ name: 'authorization', prefix: 'Bearer' }),
          extractFromHeader({ name: 'x-legacy-auth' }),
          extractFromHeader({ name: 'x-api-key', prefix: 'API-Access' }),
          extractFromCookie({ name: 'browserAuth' })
        ]
      })
    ]
  })
  ```

  With a custom `getToken`:

  ```ts
  useJWT({
    lookupLocations: [payload => payload.request.headers.get('...')]
  })
  ```

  ### Improved Verification Options

  ❌ Removed root-level config `algorithms` + `audience` + `issuer` flags. ✅ Easy API for
  customizing token verifications (based on `jsonwebtoken` library). ✅ Better defaults for token
  algorithm verification (before: `RS256`, after: `RS256` and `HS256`)

  #### Before

  ```ts
  useJWT({
    algorithms: ['RS256'],
    audience: 'my.app',
    issuer: 'http://my-issuer'
  })
  ```

  #### After

  ```ts
  useJWT({
    tokenVerification: {
      algorithms: ['RS256', 'HS256'],
      audience: 'my.app',
      issuer: 'http://my-issuer'
      // You can pass more options to `jsonwebtoken.verify("...", options)` here
    }
  })
  ```

  ### Customized Token Rejection

  ✅ New config flag `reject: { ... }` for configuring how to handle a missing or invalid tokens
  (enbaled by default).

  ```ts
  useJWT({
    reject: {
      missingToken: true,
      invalidToken: true
    }
  })
  ```

  ### Flexible Context Injection

  ❌ Removed root-level config `extendContextField` flags. ✅ Added root-level config
  `extendContext` (`boolean` / `string`) ✅ Token and payload are injected now to the context
  (structure: `{ payload: {}, token: { value, prefix }}`)

  #### Before

  ```ts
  useJWT({
    reject: {
      extendContextField: true
    }
  })
  ```

  #### After

  ```ts
  // Can be a boolean. By default injects to "context.jwt" field
  useJWT({
    reject: {
      extendContext: true
    }
  })

  // Or an object to customize the field name
  useJWT({
    reject: {
      extendContext: 'myJwt'
    }
  })
  ```

### Patch Changes

- [#3366](https://github.com/dotansimha/graphql-yoga/pull/3366)
  [`057ad06`](https://github.com/dotansimha/graphql-yoga/commit/057ad0678cc25f2f0dac403280c2882dfe532edb)
  Thanks [@dotansimha](https://github.com/dotansimha)! - dependencies updates:
  - Added dependency
    [`@whatwg-node/server-plugin-cookies@1.0.2` ↗︎](https://www.npmjs.com/package/@whatwg-node/server-plugin-cookies/v/1.0.2)
    (to `dependencies`)
- Updated dependencies
  [[`4252e3d`](https://github.com/dotansimha/graphql-yoga/commit/4252e3d0e664e3c247c709cd47a0645c68dc527a)]:
  - graphql-yoga@5.6.1

## 2.6.0

### Patch Changes

- Updated dependencies
  [[`9f3f945`](https://github.com/dotansimha/graphql-yoga/commit/9f3f94522a9e8a7a19657efdd445a360ec244d55)]:
  - graphql-yoga@5.6.0

## 2.5.0

### Patch Changes

- Updated dependencies
  [[`0208024`](https://github.com/dotansimha/graphql-yoga/commit/02080249adb8b120d44a89126571145dc3be8e4e)]:
  - graphql-yoga@5.5.0

## 2.4.0

### Minor Changes

- [#3182](https://github.com/dotansimha/graphql-yoga/pull/3182)
  [`8663e78`](https://github.com/dotansimha/graphql-yoga/commit/8663e78a4a0129d084e5a81b043f278e4b7fa84e)
  Thanks [@bgentry](https://github.com/bgentry)! - Add the possibility to customize JwksClient
  options

- [#3275](https://github.com/dotansimha/graphql-yoga/pull/3275)
  [`25886fa`](https://github.com/dotansimha/graphql-yoga/commit/25886fa194b38d195fbec5c2989a6063398c9583)
  Thanks [@EmrysMyrddin](https://github.com/EmrysMyrddin)! - Update type to allow passing every
  jsonwebtoken.verify options

### Patch Changes

- [#3300](https://github.com/dotansimha/graphql-yoga/pull/3300)
  [`fdd902c`](https://github.com/dotansimha/graphql-yoga/commit/fdd902c2a713c6bd951e1b1e6570164b6ff2d546)
  Thanks [@EmrysMyrddin](https://github.com/EmrysMyrddin)! - dependencies updates:
  - Updated dependency
    [`graphql-yoga@workspace:^` ↗︎](https://www.npmjs.com/package/graphql-yoga/v/workspace:^) (from
    `^5.3.1`, in `peerDependencies`)
- Updated dependencies
  [[`4cd43b9`](https://github.com/dotansimha/graphql-yoga/commit/4cd43b9ff56ad9358dc897f4bb87a6a94f953047),
  [`fdd902c`](https://github.com/dotansimha/graphql-yoga/commit/fdd902c2a713c6bd951e1b1e6570164b6ff2d546),
  [`d5dfe99`](https://github.com/dotansimha/graphql-yoga/commit/d5dfe99af030a5afac26968ba8dd81dee6df0dc2),
  [`7335a82`](https://github.com/dotansimha/graphql-yoga/commit/7335a82a4b0696c464311a5027a43b16c7f68156),
  [`f9aa1cd`](https://github.com/dotansimha/graphql-yoga/commit/f9aa1cdc968816a9f83f054dbd24799c85f71a2c)]:
  - graphql-yoga@5.4.0

## 2.3.1

### Patch Changes

- Updated dependencies
  [[`3324bbab`](https://github.com/dotansimha/graphql-yoga/commit/3324bbabf1f32e8b4ee95ea8700acfb06f87f8ca),
  [`3324bbab`](https://github.com/dotansimha/graphql-yoga/commit/3324bbabf1f32e8b4ee95ea8700acfb06f87f8ca)]:
  - graphql-yoga@5.3.1

## 2.3.0

### Patch Changes

- Updated dependencies
  [[`f775b341`](https://github.com/dotansimha/graphql-yoga/commit/f775b341729145cee68747ab966aa9f4a9ea0389),
  [`f775b341`](https://github.com/dotansimha/graphql-yoga/commit/f775b341729145cee68747ab966aa9f4a9ea0389),
  [`f89a1aa2`](https://github.com/dotansimha/graphql-yoga/commit/f89a1aa2a0bd6efc145627a674370b1b22e231fa)]:
  - graphql-yoga@5.3.0

## 2.2.0

### Patch Changes

- Updated dependencies
  [[`71db7548`](https://github.com/dotansimha/graphql-yoga/commit/71db754876612bb9a1df496f478eaf1b94f342cf),
  [`71db7548`](https://github.com/dotansimha/graphql-yoga/commit/71db754876612bb9a1df496f478eaf1b94f342cf)]:
  - graphql-yoga@5.2.0

## 2.1.2

### Patch Changes

- Updated dependencies
  [[`3ef877a7`](https://github.com/dotansimha/graphql-yoga/commit/3ef877a75c5b19e082121ece08981183422618f0)]:
  - graphql-yoga@5.1.1

## 2.1.1

### Patch Changes

- [#3149](https://github.com/dotansimha/graphql-yoga/pull/3149)
  [`b9d2afcc`](https://github.com/dotansimha/graphql-yoga/commit/b9d2afcc0ef65246f0a3c181daf00f886bac7404)
  Thanks [@EmrysMyrddin](https://github.com/EmrysMyrddin)! - Fix unauthorized error resulting in an
  response with 500 status or in a server crash (depending on actual HTTP server implementation
  used).

## 2.1.0

### Patch Changes

- Updated dependencies
  [[`b1f0e3a2`](https://github.com/dotansimha/graphql-yoga/commit/b1f0e3a2986956c6791a251df908e3f8b50ec966)]:
  - graphql-yoga@5.1.0

## 2.0.2

### Patch Changes

- Updated dependencies
  [[`77d107fe`](https://github.com/dotansimha/graphql-yoga/commit/77d107fe1a01044f4ba017ca960bb1bd58407ed7)]:
  - graphql-yoga@5.0.2

## 2.0.1

### Patch Changes

- Updated dependencies
  [[`3fea19f2`](https://github.com/dotansimha/graphql-yoga/commit/3fea19f2a01c85b7d837163d763fae107e8f5a53)]:
  - graphql-yoga@5.0.1

## 2.0.0

### Major Changes

- [#3063](https://github.com/dotansimha/graphql-yoga/pull/3063)
  [`01430e03`](https://github.com/dotansimha/graphql-yoga/commit/01430e03288f072a9cb09b0b898316b1f5b58a5f)
  Thanks [@EmrysMyrddin](https://github.com/EmrysMyrddin)! - **Breaking Change:** Drop support of
  Node.js 16

### Patch Changes

- Updated dependencies
  [[`01430e03`](https://github.com/dotansimha/graphql-yoga/commit/01430e03288f072a9cb09b0b898316b1f5b58a5f),
  [`5b615478`](https://github.com/dotansimha/graphql-yoga/commit/5b6154783957874281bdf180575cdf57fadb75bf),
  [`350bb851`](https://github.com/dotansimha/graphql-yoga/commit/350bb85195c01cc5b5721f7a90f6cfbe1af36aff)]:
  - graphql-yoga@5.0.0

## 1.1.0

### Minor Changes

- [#3029](https://github.com/dotansimha/graphql-yoga/pull/3029)
  [`2d0cd188`](https://github.com/dotansimha/graphql-yoga/commit/2d0cd1882742ddf6550cc2c6451062062df82ccc)
  Thanks [@EmrysMyrddin](https://github.com/EmrysMyrddin)! - Allow getToken to return a promise.

### Patch Changes

- Updated dependencies
  [[`bf602edf`](https://github.com/dotansimha/graphql-yoga/commit/bf602edf790590de1db26b5f3fc39f895104055c)]:
  - graphql-yoga@4.0.5

## 1.0.1

### Patch Changes

- Updated dependencies
  [[`5f182006`](https://github.com/dotansimha/graphql-yoga/commit/5f1820066e8a340ad214b55232fcf439793f91bf)]:
  - graphql-yoga@4.0.4

## 1.0.0

### Major Changes

- [#1921](https://github.com/dotansimha/graphql-yoga/pull/1921)
  [`43771514`](https://github.com/dotansimha/graphql-yoga/commit/437715143cfd0bd31a08a283c58b8ea8f49938a2)
  Thanks [@ardatan](https://github.com/ardatan)! - New Generic JWT Plugin

### Patch Changes

- [#2933](https://github.com/dotansimha/graphql-yoga/pull/2933)
  [`cb47a72c`](https://github.com/dotansimha/graphql-yoga/commit/cb47a72ced15878495da03d11fa587a391a25ae1)
  Thanks [@renovate](https://github.com/apps/renovate)! - dependencies updates:

  - Updated dependency
    [`jsonwebtoken@^9.0.0` ↗︎](https://www.npmjs.com/package/jsonwebtoken/v/9.0.0) (from `^8.5.1`,
    in `dependencies`)

- [#2935](https://github.com/dotansimha/graphql-yoga/pull/2935)
  [`1c89cfd5`](https://github.com/dotansimha/graphql-yoga/commit/1c89cfd512b2e0fe0fc4f2897c7d2c871020d040)
  Thanks [@renovate](https://github.com/apps/renovate)! - dependencies updates:
  - Updated dependency [`jwks-rsa@^3.0.0` ↗︎](https://www.npmjs.com/package/jwks-rsa/v/3.0.0) (from
    `^2.1.5`, in `dependencies`)
