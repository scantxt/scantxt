# Reporting Guidance

_Still drafting..._

Looking to align fields to both [JWT (RFC 7519)](https://www.rfc-editor.org/rfc/rfc7519) and [OCSF (Open Cybersecurity Schema Framework)](https://github.com/ocsf/).

Either plain `json` or `jwt` (using same key pair as the signing verfication).

## Reporting URI Forensics / Details

``` json
{
  "v": "SCANNER1",
  "type": "ruf",
  "iss": "scantxt.app",
  "aud": "",
  "iat": 1669841288,
  "ruri": "",
  "items": [
    {
      "activity_id": -1,
      "category_uid": 2,
      "class_uid": 2001,
      "time": 1669841288123,
      "metadata": {
        "product": "scantxt.app",
        "version": "0.0.1"
      },
      "finding": {
        "title": "scantxt",
        "uid": "abc123",
        "src_url": "https://www.scantxt.org"
      },
      "severity_id": 1,
      "additional_scoring": {
        "pagespeed_insights": {
          "value": "99",
          "ref": "https://pagespeed.web.dev/report?url=https%3A%2F%2Fwww.scantxt.org%2F"
        }
      },
      "status_id": 1,
      "type_uid": 200099
    }
  ]
}
```

As a JWT:

`eyJhbGciOiJFUzI1NiIsImtpZCI6ImVmYWJkOTJhIiwidHlwIjoiSldUIn0.eyJ2IjoiU0NBTk5FUjEiLCJ0eXBlIjoicnVmIiwiaXNzIjoic2NhbnR4dC5hcHAiLCJhdWQiOiIiLCJpYXQiOjE2Njk4NDEyODgsInJ1cmkiOiIiLCJpdGVtcyI6W3siYWN0aXZpdHlfaWQiOi0xLCJjYXRlZ29yeV91aWQiOjIsImNsYXNzX3VpZCI6MjAwMSwidGltZSI6MTY2OTg0MTI4ODEyMywibWV0YWRhdGEiOnsicHJvZHVjdCI6InNjYW50eHQuYXBwIiwidmVyc2lvbiI6IjAuMC4xIn0sImZpbmRpbmciOnsidGl0bGUiOiJzY2FudHh0IiwidWlkIjoiYWJjMTIzIiwic3JjX3VybCI6Imh0dHBzOi8vd3d3LnNjYW50eHQub3JnIn0sInNldmVyaXR5X2lkIjoxLCJhZGRpdGlvbmFsX3Njb3JpbmciOnsicGFnZXNwZWVkX2luc2lnaHRzIjp7InZhbHVlIjoiOTkiLCJyZWYiOiJodHRwczovL3BhZ2VzcGVlZC53ZWIuZGV2L3JlcG9ydD91cmw9aHR0cHMlM0ElMkYlMkZ3d3cuc2NhbnR4dC5vcmclMkYifX0sInN0YXR1c19pZCI6MSwidHlwZV91aWQiOjIwMDA5OX1dfQ.0OvaQ3Hb3dnE0IwyJZb4cj8qMlYzBInMAbrc8S2hT50mxiMtx35KsjV4TO9Hg470Yw115V1LSObgnyyF-mRe5A`

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
