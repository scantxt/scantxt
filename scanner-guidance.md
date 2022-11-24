# Scanner Guidance

## Scanning Verification

See [SCAN_VERIFICATION.md](SCAN_VERIFICATION.md).

## Identification
Scanners MUST identify themselves to servers. This is to ensure that servers can make decisions about allowing, rejecting or dropping activity and perform any verification of the scanning. It also allows server operators to know who to contact in the event of any queries or abuse.

For some protocols being scanned, such as HTTP, this can be achieved using request headers. Other protocols may not have a mechanism for direct identification, so in that case indirect identification is necessary using DNS PTR records.

## Direct Identification

### HTTP

For scanners primarily using the `/.well-known/scanner.json` endpoint the `Referer` should be used, for example:

> Referer: https://www.scantxt.app/.well-known/scanner.json

For scanners primarily using DNS TXT records, the `User-Agent` should be used, for example:

> User-Agent: _scanner.scantxt.app

Additionally, scanners MAY also set the `X-Scanner` header to either the `.well-known` endpoint, `_scanner` DNS record, or just the domain name. 
Scanners MAY set all headers, for example:

> Referer: https://www.scantxt.app/.well-known/scanner.json  
> User-Agent: _scanner.scantxt.app  
> X-Scanner: scantxt.app

## Indirect Identification 

The scanning IP should have a DNS PTR record pointing to the `_scanner` subdomain. Additionally, that subdomain MAY set the A, AAAA or CNAME records such that the scanning IP can be resolved.

If, for example, the scanning IP was `192.168.0.1`:

```
dig +short -x 192.168.0.1
# _scanner.scantxt.app.

dig +short _scanner.scantxt.app
# 192.168.0.1

dig +short _scanner.scantxt.app TXT
# "v=SCANNER1; ..."
```
