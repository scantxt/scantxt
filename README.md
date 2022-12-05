# scantxt

scantxt is a common way to opt-out and/or verify ownership to scanners. It's similar in structure to `dmarc` with features built-in like ownership verification, notification endpoints and scanner verifications.
```
    +-----------------------------+                   +---------+       +---------+
    |                             |                   | scanner +------>| scanner |
+---+     Reporting Endpoint      |<-------+          | private |       | public  +---------+
|   |                             |        |          | key     |<------+ key     |         |
|   +-----------------------------+        |          +----+----+       +---------+         |
|                                          |               |                                |
|                                          |               v                                |
|   +-----------------------------+    +---+--------------------------------------------+   |
|   |                             |    |                                                |   |
+---+       Target / Asset        |<---+                    Scanner                     |   |
|   |                             |    |                                                |   |
v   +-----------------------------+    +--------------------------------+               |   |
|   |       "scan" records        |    |        "scanner" records       |               |   |
|   +-------------+---------------+    +----------------+---------------+---------------+   |
|   | "_scan" DNS | /.well-known/ |    | "_scanner" DNS | /.well-known/ | /.well-known/ |<--+
|   | TXT records | scan.json     |    | TXT record     | scanner.json  | jwks.json     |
|   +-------------+---------------+    +----------------+---------------+---------------+
|                                                                               ^
|                                                                               |
+-------------------------------------------------------------------------------+
```
(see [overview-diagram.png](overview-diagram.png) if this doesn't render correctly)

## `scan` records

The recommended method is to use the `_scan` subdomain for DNS TXT records. Alternatively, `/.well-known/scan.json` (with the mime type `application/json`) can be used (`.json` extension optional).

See more info on `scan` records and format here: [SCAN.md](SCAN.md)

You can view examples of these at <https://www.scantxt.org/.well-known/scan.json> or by doing a DNS TXT lookup against `_scan.scantxt.org`

## `scanner` records

This is a mechanism for scanners to set contact details and signpost what type of scanning they perform.

The recommended method is to use the `_scanner` subdomain for DNS TXT records. Alternatively, `/.well-known/scanner.json` (with the mime type `application/json`) can be used (`.json` extension optional).

See about `scanner` records and format here: [SCANNER.md](SCANNER.md)

You can view an example of this at <https://www.scantxt.app/.well-known/scanner.json> or by doing a DNS TXT lookup against `_scanner.scantxt.app`

## Scanner guidance

See [scanner-guidance](scanner-guidance.md) for details on identification and verification.

## Reporting guidance

See [reporting-guidance](reporting-guidance.md) for details on reporting endpoints and mechanisms.
