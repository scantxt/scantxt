# scan

This record and format is for website and infrastructure operators to implement opt-outs and/or verifications for scanning services and designate reporting and notification endpoints.

## Proposed Structure

- Root record (root) = `_scan.{domain}`
- Scanner subdomain (scsd) = `{scanner domain}.{root record}`

|code|applicability|description|
|-|-|-|
|v|required|Version, set to "SCAN1"|
|sr|required in scsd<br/>ignored in root|Source scanner - must match the scanner subdomain|
|p|optional in scsd<br/>ignored in root|Policy - "opt-out" or "none", default is "none"|
|sp|optional in scsd<br/>ignored in root|Subdomain Policy - "opt-out" or "none", default is "none"|
|ruh|optional|Reporting URI(s) for humans|
|rua|optional|Reporting URI(s) for aggregate scan statistics or results|
|ruf|optional|Reporting URI(s) for detailed scan results|
|rue|optional in scsd<br/>ignored in root|Report URIs encrypted; used scanner public key to encrypt `ruh`/`rua`/`ruf` - "yes" or "no", default is "no"|
|ri|optional|Reporting interval for aggregate reports|
|rf|optional|Report format for `rua` and `ruf` - "jwt" or "json", default is "json"|
|vf|optional in scsd|Verification, a hash or other alphanumeric value for scanners to verify ownership|
|al|optional|Alternative domain hint, an FQDN to potentially scan as well or instead, where another scantxt lookup is required|
|nb|optional|A timestamp from when the record applies - rfc3339 format|
|ex|optional|An expiry timestamp when the record no longer applies - rfc3339 format|
|rq|optional|Require scanning signature - "yes" or "no", default is "no"|
|es|optional|Expected signature attibute|

Human report URI (ruh) can be either:
- email `mailto:...@...`
- URL `https://example.com/contact` 

Aggregate (rua) or details (rud) can be either:
- [report-to](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/report-to) `report-to:https://...`
- email `mailto:...@...`

Requiring scanning signatures is only currently recommended on HTTP(S) only scanners. If `rq=yes` then any scanning without an `X-Scanning-Token` (or field set with `es`) should be dropped.

Expected signature attribute specifies what the target will be checking (format `type:field`, for example `http_header:X-ST`).

### DNS TXT Examples

Simple report example:
```
_scan 3600 TXT "v=SCAN1; ruh=mailto:security@example.com;"
```

Time-based verification example for a pentest:
```
pentest-company.example.com._scan 3600 TXT "v=SCAN1; vf=abc123; sr=pentest-company.example.com; nbd=2022-12-22T23:45:00Z; exp=2023-01-22T23:45:00Z;"
```

Complex example:

|subdomain|TXT record|
|-|-|
|\_scan|v=SCAN1; ruh=https://example.com/contact,mailto:security@example.com;|
|\_scan.mail|v=SCAN1; ruh=mailto:post@example.com;|
|\_scan.servers|v=SCAN1; ruh=mailto:infra@example.com; ruf=report-to:https://infra-report-to.example.com;|
|scanner.example.com.\_scan|v=SCAN1; rq=yes; es=http_header:scanner-token; sr=scanner.example.com;|
|scantxt.app.\_scan|v=SCAN1; p=opt-out; sp=opt-out;|

### scan.json Examples

Simple:
``` json
[
  {
    "v": "SCAN1",
    "ruh": "https://example.com/.well-known/security.txt"
  }
]
```

Multiple Scanners:
``` json
[
  {
    "v": "SCAN1",
    "ruh": "https://example.com/.well-known/security.txt"
  },
  {
    "v": "SCAN1",
    "sr": "scanner.example.com",
    "rq": "yes"
  },
  {
    "v": "SCAN1",
    "sr": "scantxt.app",
    "p": "opt-out",
    "sp": "opt-out"
  }
]
```
