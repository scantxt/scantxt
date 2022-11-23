# scanner

This record and format is for scanning operators to implement as a way to signpost the types of scanning they do.

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
|scm|no|Signature mechanism, either "sign", "hash" or "none" - default is "none"|
|puk|no|Public key in PEM format without the header or footers (`-- START/END PRIVATE/PUBLIC KEY --`)|
|jku|no|JWKS URI where public keys can be configured|
|esa|no|Enabled signature attribute, this follows the format `attribute-type:attribute-name`, e.g. `http_header:X-Scanner-Token`|
|lut|no|Last updated time - rfc3339 format|

## Scanner indication

Scanners should insert/specify a header or other indicator into scans or packets that point to either the `_scanner` subdomain and/or `.well-known` resource.
For example:
```
X-Scanner: https://scantxt.app/.well-known/scanner.json
```

Scanners may additionally enable a signature (see [SCAN_VERIFICATION.md](SCAN_VERIFICATION.md) for more details), for example:
```
X-Scanner: _scanner.scantxt.app
X-Scanner-Token: eyJ0eXAiOiJK...oHJNMbCeu-Q
```

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
  "scm": "sign",
  "puk": "MUt...=",
  "jku": "https://www.scantxt.app/.well-known/jwks.json",
  "esa": "http_header:X-Scanner-Token",
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
    scm=sign; puk=MUt...=;
    jku=https://www.scantxt.app/.well-known/jwks.json;
    esa=http_header:X-Scanner-Token;
    lut=2022-11-23T14:54:00Z;"
```
