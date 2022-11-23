# scantxt

scantxt is a common way to allow/disallow scanning activity. Similar to `robots.txt` and structured like `dmarc` with features built-in like ownership verification, notification endpoints and scanner verification.

```
┌─────────────────────────────┐      ┌────────────────────────────────────────────────┐      ┌─────────────┐
│                             │◄─────┤                                                │      │ scanner     │
│        target/asset         │      │                    scanner                     │◄─────┤ private key │
│                             ├─────►│                                                │      └───┬─────────┘
├─────────────────────────────┤      ├────────────────────────────────┐               │          │     ▲
│       "scan" records        │      │        "scanner" records       │               │          ▼     │
├─────────────┬───────────────┤      ├────────────────┬───────────────┼───────────────┤      ┌─────────┴───┐
│ "_scan" DNS │ /.well-known/ │      │ "_scanner" DNS │ /.well-known/ │ /.well-known/ │◄─────┤ scanner     │
│ TXT records │ scan.json     │      │ TXT record     │ scanner.json  │ jwks.json     │      │ public key  │
└─────────────┴───────────────┘      └────────────────┴───────────────┴───────────────┘      └─────────────┘
```

## `scan` records

The recommended method is to use the `_scan` subdomain for DNS TXT records. Alternatively, `/.well-known/scan.json` (with the mime type `application/json`) can be used (`.json` extension optional).

See more info on `scan` records and format here: [SCAN.md](SCAN.md)

## `scanner` records

This is a mechanism for scanners to set contact details and signpost what type of scanning they perform.

The recommended method is to use the `_scanner` subdomain for DNS TXT records. Alternatively, `/.well-known/scanner.json` (with the mime type `application/json`) can be used (`.json` extension optional).

See about `scanner` records and format here: [SCANNER.md](SCANNER.md)

## Scanning verification

This is a mechanism for how scan targets can verify a scan has come from a trusted scanner.

See more information about verification here: [SCAN_VERIFICATION.md](SCAN_VERIFICATION.md)
