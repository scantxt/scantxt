# Reporting Guidance

_Still drafting..._

Either plain `json` or `jwt` (using same key pair as the signing verfication).

## Reporting URI Forensics / Details

``` json
{
  "v": "SCANNER1",
  "type": "ruf",
  "iss": "domain|.well-known",
  "aud": "",
  "iat": "",
  "ruri":"",
  "discoveries": [
    {
      "id": "0",
      "title": "etc.",
      "summary": "",
      "scoring": {
        "cve": {
          "value": "10",
          "ref": ""
        }
      },
      "so": "",
      "muri": "",
      "refs": []
    }
  ]
}
```

## Reporting URI Aggregate
``` json
{
  "v": "SCANNER1",
  "type": "rua",
  "iss": "domain|.well-known",
  "aud": "",
  "iat": "",
  "from":"",
  "to":"",
  "stats": [
    {
      "id": "0",
      "st": "total",
      "key": "Total Scans",
      "count": 12
    },
    {
      "id": "1",
      "st": "count",
      "so": "",
      "key": "Unique Discoveries",
      "count": 9
    },
    {
      "id": "2",
      "st": "scores",
      "so": "",
      "range-ge": 9,
      "range-le": 10,
      "key": "Critical CVEs",
      "count": 3
    },
    {
      "id": "3",
      "st": "score",
      "so": "",
      "range-ge": 6,
      "range-lt": 9,
      "key": "High CVEs",
      "count": 3
    },
    {
      "id": "4",
      "st": "score",
      "so": "",
      "range-ge": 0,
      "range-lt": 6,
      "key": "Low CVEs",
      "count": 3
    },
    {
      "id": "5",
      "st": "other",
      "so": "",
      "key": "Key",
      "value": "value"
    }
  ]
}
```
