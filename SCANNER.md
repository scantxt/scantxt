# scanner

This record and format is for scanning operators to implement as a way to signpost the types of scanning they do.

See [scanner-guidance](scanner-guidance.md) for details on identification and verification.

## Proposed Structure

|code|required|description|
|-|-|-|
|v|yes|Version, set to "SCANNER1"|
|info|no|More information URI for humans|
|privacypolicy|no|Privacy policy URI for humans|
|terms|no|Terms and conditions URI for humans|
|documentation|no|Documentation URI for humans|
|login|no|Login URI for humans|
|abuse|no|Abuse reporting endpoint following the [SCAN.md](SCAN.md) `ruh` format|
|contacts|no|Contact(s) following the `ruh` format|
|types|no|List of [Scan Types](TYPES.md)|
|sgm|no|Signature mechanism(s); "sign", "hash", "prsh", or "none" - default is "none"|
|puk|no|Key ID and public key in PEM format without the header or footers (`-- START/END PRIVATE/PUBLIC KEY --`)|
|jku|no|JWKS URI where public keys can be configured|
|esa|no|Enabled signature attribute, this follows the format `attribute-type:attribute-name`, e.g. `http_header:X-Scanner-Token`|
|lut|no|Last updated time - rfc3339 format|

## scanner.json Example:

``` json
{
  "info": "https://www.scantxt.app",
  "contacts": [
    "mailto:scantxt.app-scanner.json@olliejc.uk"
  ],
  "documentation": "https://www.scantxt.txt",
  "abuse": "mailto:scantxt.app-abuse@olliejc.uk",
  "types": [
    "indexer_passive"
  ],
  "sgm": ["sign", "prsh"],
  "puk": {
    "abc": "MUt...="
  }
  "jku": "https://www.scantxt.app/.well-known/jwks.json",
  "esa": {
    "http_header": "X-Scanner-Token"
  },
  "lut": "2022-11-23T14:54:00Z"
}
```

### DNS TXT Example

```
_scanner "v=SCANNER1; info=https://www.scantxt.app; 
    contacts=mailto:scantxt.app-scanner.json@olliejc.uk;
    documentation=https://www.scantxt.txt;
    abuse=mailto:scantxt.app-abuse@olliejc.uk;
    types=indexer_passive;
    sgm=sign,prsh; puk=abc:MUt...=;
    jku=https://www.scantxt.app/.well-known/jwks.json;
    esa=http_header:X-Scanner-Token;
    lut=2022-11-23T14:54:00Z;"
```
