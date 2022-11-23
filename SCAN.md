# scan

This record and format is for website and infrastructure operators to implement as a way to signpost what scanning activities are acceptable or not.

## Proposed Structure

|code|required|description|
|-|-|-|
|v|yes|Version, set to "SCAN1"|
|p|yes*|Policy, either "allow", "disallow" or "none". Not required if `inc` is set|
|sp|no|Policy for subdomains, either "allow", "disallow" or "none" - default is "none"|
|po|no|Port/protocol(s) - default is "\*" (all)|
|src|no|Source scanner(s) - default is "\*" (all)|
|sbd|no*|Subdomain(s), required for `.well-known/scan.json` - default is "." (self)|
|ruh|no|Reporting URI(s) for humans|
|rua|no|Reporting URI(s) for aggregate scan statistics or results|
|ruf|no|Reporting URI(s) for detailed scan results|
|ri|no|Reporting interval for aggregate reports|
|rf|no|Report format for rua and ruf - default is "JSON"|
|so|no|Scan option(s) - "passive" (for all passive types), "active" (for all active types), list of [Scan Types](TYPES.md), or "\*" - default is "passive"|
|pr|no|Priority integer, lower the number, higher the precedence - default is 0|
|vf|no|Verification, a hash or other alphanumeric value for scanners to verify ownership|
|alt|no|Alternative domain, an FQDN to potentially scan instead, where another scantxt lookup is required|
|nbf|no|A timestamp from when the record applies - rfc3339 format|
|exp|no|An expiry timestamp when the record no longer applies - rfc3339 format|
|inc|no|Include `scan` records from another domain, either a HTTPS URL of another `/.well-known/scan.json` or a domain name (without the `_scan` suffix). Useful for applying across multiple domain names|
|rqs|no|Require scanning signature, "yes" or "no" - default is "no"|
|esa|no|Expected signature attibute|

Human report URI (ruh) can be either:
- email `mailto:...@...`
- URL `https://example.com/contact` 

Aggregate (rua) or details (rud) can be either:
- [report-to](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/report-to) `report-to:https://...`
- email `mailto:...@...`

Requiring scanning signatures is only currently recommended on HTTP(S) only scanners. If `rqs=yes` then any scanning without an `X-Scanning-Token` or `esa` should be dropped.

Expected signature attribute specifies what the target will be checking (format `type:field`, for example `http_header:X-ST`).

### DNS TXT Examples

Simple allow passive example:
```
v=SCAN1; p=allow; ruh=mailto:security@example.com;
    so=passive; pr=10;
```

Simple deny all example:
```
v=SCAN1; p=disallow; sp=disallow; pr=0;
```

Time-based active example for a pentest:
```
v=SCAN1; p=allow; sp=disallow; pr=20; src=pentest-company.example.com; po=*; nbd=2022-12-22T23:45:00Z; exp=2023-01-22T23:45:00Z;
v=SCAN1; p=disallow; sp=disallow; pr=100;
```

Redirect scanning to testing environment:
```
# allow passive scanning of "scantxt.org" and suggest assessing "test-env.scantxt.org"
_scan v=SCAN1; p=allow; 
    ruh=mailto:security@example.com;
    so=passive; pr=10;
    alt=test-env.scantxt.org; po=*;
    
# disallow all other scanning on "scantxt.org" where it doesn't match above
_scan v=SCAN1; p=disallow; sp=disallow; pr=100;

# allow active scanning for HTTP or HTTPS on "test-env.scantxt.org"
_scan.test-env v=SCAN1; p=allow;
    ruh=mailto:security@example.com;
    so=active; pr=10; po=http,https;
    
# disallow all other scanning on "test-env.scantxt.org" where it isn't HTTP or HTTPS
_scan.test-env v=SCAN1; p=disallow;
    sp=disallow; pr=100;
```

Complex example:

|subdomain|TXT record|
|-|-|
|\_scan|v=SCAN1; p=allow; sp=none; ruh=https://example.com/contact; so=active; pr=10; po=http,https;|
|\_scan.mail|v=SCAN1; p=allow; sp=none; ruh=mailto:post@example.com; so=passive; pr=15; po=http,https;|
|\_scan|v=SCAN1; p=allow; sp=allow; ruh=mailto:post@example.com; so=passive; pr=20; po=smtp,25;|
|\_scan|v=SCAN1; p=allow; sp=allow; ruh=mailto:infra@example.com; so=passive; pr=25; po=rdp,3389;|
|\_scan.servers|v=SCAN1; p=allow; sp=allow; ruh=mailto:infra@example.com; so=passive; pr=30; po=rdp,3389;|
|\_scan|v=SCAN1; p=allow; sp=allow; ruh=mailto:security@example.com; so=passive; pr=35;|
|scantxt.app.\_scan|v=SCAN1; p=allow; sp=allow; vf=2fd4e1c67a2d28fced849ee1bb76e7391b93eb12; so=\*; pr=50; src=scantxt.app;|
|scanner.example.com.\_scan|v=SCAN1; p=allow; sp=allow; rqs=yes; esa=http_header:scanner-token; so=\*; po=https,http; pr=50; src=scanner.example.com;|
|\_scan|v=SCAN1; p=disallow; sp=disallow; so=active; pr=100;|

### scan.json Examples

Simple disallow all:
``` json
[
  {
    "v": "SCAN1",
    "p": "disallow",
    "sp": "disallow",
    "sbd": [
      "*"
    ]
    "so": "*",
    "pr": 1000,
    "po": "*"
  }
]
```

Simple allow all passive on just the root domain, disallow everything else on both the root domain and subdomains:
``` json
[
  {
    "v": "SCAN1",
    "p": "allow",
    "ruh": [
      "mailto:security@example.com"
    ],
    "sbd": [
      "."
    ]
    "so": "passive",
    "pr": 5,
    "po": "*"
  }, {
    "v": "SCAN1",
    "p": "disallow",
    "sp": "disallow",
    "ruh": [
      "mailto:security@example.com"
    ],
    "sbd": [
      "*"
    ]
    "so": "*",
    "pr": 100,
    "po": "*"
  }
]
```

Include example:

_example.com/.well-known/scan.json_:
``` json
[
  {
    "v": "SCAN1",
    "p": "allow",
    "ruh": [
      "mailto:security@example.com"
    ],
    "sbd": [
      "."
    ]
    "so": "passive",
    "pr": 5,
    "po": "*"
  }, {
    "v": "SCAN1",
    "p": "disallow",
    "sp": "disallow",
    "sbd": [
      "*"
    ]
    "so": "active",
    "pr": 100,
    "po": "*"
  }
]
```

_example.net/.well-known/scan.json_ (use records in example.com):
``` json
[
  {
    "v": "SCAN1",
    "inc": "https://example.com/.well-known/scan.json"
  }
]
```
