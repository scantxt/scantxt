# Scan Verification

The following mechanism is how a scanned entity or target can verify the authenticity of a scan. It may even be possible for edge infrastructure to perform this verification before allowing the request to continue to an origin.

There are three mechanisms to enable verification.
1. `SIGN` - Using asymmetric encryption to sign each request (recommended)
2. `HASH` - Generating a unique private hash per asset (simpler per asset)
3. `PRSH` - Using a pre-shared alphanumeric secret that the scanner sets per account or asset (simplest)

Using `SIGN` requires a scanner to have a ECDSA key pair, where the public key is available in either the `puk` field or a JWKS endpoint (specified in the `jku` - JWKS URI - field).
If using signing, the scanner MUST set `scm` to `sign`, set an attribute in the `esa` - enabled signature attribute, and set either `jku` or `puk`. Both `jku` and `puk` MAY be used, and at least one MUST be set.

Using `HASH` requires a scanner to use a HMAC utilising the asset and a private secret.
If using hashing, the scanner MUST set `scm` to `hash` and set an attribute in the `esa`. `jku` and `puk` MAY be set if the HMAC function can utilise a public key for out-of-band verification.

If using pre-shared secret, the scanner MUST set the `scm` to `prsh` and set an attribute in the `esa`. `jku` and `puk` MUST not be set. This method requires the user or system to interact with the scanner in order to verify the secret matches.

See the [scanner record](SCANNER.md) for more information on fields.

## Scanner signing steps

1. Scanner sets the `scm` to `sign` and configures a `jku` and/or `puk`
2. Scanner creates a header JSON, for example:
  - `{"typ": "JWT", "kid": "abc", "alg": "ES256"}`
  - Where:
    - `typ` is `JWT` (`scan` TBC)
    - `kid` is the key identifier to lookup from the JWKS endpoint
3. Scanner generates a JSON payload, for example:
  - `{"iss": "scantxt.app", "iat": 123, "aud": "example.com"}`
  - Where:
    - `iss` is the scanner
    - `iat` is the issued at time, or scan time, which is the Unix time integer in seconds
    - `aud` is the target 
4. Scanner uses its private key to generate a signature of the `Base64URL(header).Base64URL(payload)`
5. Scanner creates a JWT in the format of `Base64URL(header).Base64URL(payload).Base64URL(signature)`
6. Scanner sets the JWT in the attribute specified in the `esa` field when performing the scan

## Scan verification
1. Target receives a scan purporting to be from a scanner
2. Target looks up the `scanner` record and identifies the `scm`, `esa` and `jku` and/or `puk` fields
3. Target checks for the `esa` in the request - if missing, drop the request
4. Target extacts the JWT from the attribute as specified by `esa`
5. Target either uses the `puk` or checks for and fetches `kid` specified in the JWT header and pull from the `jku`
6. Target verifies the JWT signature using the scanners public key - if invalid, drop the request

# `SIGN` Example

`scantxt.app` has an EC key pair:

Key ID: `1a2b3c`

Private key:
```
-----BEGIN PRIVATE KEY-----
MIGHAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBG0wawIBAQQgt2w+b8mNigBWx8wi
5YkGg6JaIQfKNRlKDCH/+MZVlkahRANCAAQwDYA3okEJGV/52VuaOdSMhM42Xm3u
d2KtXzAT7OTDg/OeItUJWmxLbZqfxcX9qxKSwyzx+Te465pXg15V8h2x
-----END PRIVATE KEY-----
```

Public key:
```
-----BEGIN PUBLIC KEY-----
MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEMA2AN6JBCRlf+dlbmjnUjITONl5t
7ndirV8wE+zkw4PzniLVCVpsS22an8XF/asSksMs8fk3uOuaV4NeVfIdsQ==
-----END PUBLIC KEY-----
```

`scantxt.app` sets the public key in the JWKS at `https://scantxt.app/.well-known/scanner-jwks.json` and configures the `_scanner.scantxt.app` record with the `scm`, `jku` and `esa` fields:
```
"v=SCANNER1; scm=sign; jku=https://scantxt.app/.well-known/scanner-jwks.json; esa=http_header:x-scanner-token; info=https://www.scantxt.org; contacts=mailto:scantxt.app-scanner@olliejc.uk; type=banner_passive,crawler_passive,configuration_passive;"
```

## Scanner steps

1. `scantxt.app` initiates a scan to `scantxt.org`
2. `scantxt.app` creates a header and payload:
    - `{"typ": "JWT", "kid": "1a2b3c", "alg": "ES256"}` 
    - `{"iss": "scantxt.app", "iat": 1669165027, "aud": "scantxt.org"}`
3. `scantxt.app` signs `eyJ0eXAiOiJKV1QiLCJraWQiOiIxYTJiM2MiLCJhbGciOiJFUzI1NiJ9.eyJpc3MiOiJzY2FudHh0LmFwcCIsImlhdCI6MTY2OTE2NTAyNywiYXVkIjoic2NhbnR4dC5vcmcifQ` with its private key to create the JWT
4. `scantxt.app` sets HTTP request headers:
    - `x-scanner: _scanner.scantxt.app`
    - `x-scanner-token: eyJ0eXAiOiJKV1QiLCJraWQiOiIxYTJiM2MiLCJhbGciOiJFUzI1NiJ9.eyJpc3MiOiJzY2FudHh0LmFwcCIsImlhdCI6MTY2OTE2NTAyNywiYXVkIjoic2NhbnR4dC5vcmcifQ.I_b2AC0rwpjcz_7CM8Abr8aOn-HbKEWmU4HN9iayyMXyOk9bY-jXEji47mxMmFhRboGbCJYqlNJoHJNMbCeu-Q`


## Target steps

1. `scantxt.org` receives HTTP request header `x-scanner` with the value `_scanner.scantxt.app`
2. `scantxt.org` looks up the `scanner` record from `scantxt.app` and finds the `scm`, `jku` and `esa` fields
3. `scantxt.org` checks for the `esa` HTTP request header (`x-scanner-token: eyJ0eXAiOiJKV1QiLCJraWQiOiIxYTJiM2MiLCJhbGciOiJFUzI1NiJ9.eyJpc3MiOiJzY2FudHh0LmFwcCIsImlhdCI6MTY2OTE2NTAyNywiYXVkIjoic2NhbnR4dC5vcmcifQ.I_b2AC0rwpjcz_7CM8Abr8aOn-HbKEWmU4HN9iayyMXyOk9bY-jXEji47mxMmFhRboGbCJYqlNJoHJNMbCeu-Q`)
4. `scantxt.org` needs to fetch the public key, as it's not in the `puk`, so decodes the JWT to get the `kid` value (`1a2b3c`)
5. `scantxt.org` verifies and decodes the JWT using the key found in the `jku` endpoint (`https://scantxt.app/.well-known/scanner-jwks.json`)
6. `scantxt.org` asserts that the request comes from `scantxt.app`
