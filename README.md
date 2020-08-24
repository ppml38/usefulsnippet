## Convert `xml` to `json`
Try below code to convert `xml` files into `json`. this also convert xml attributes.
### Code *(python)*
```python
import re
import json

def getdict(content):
    res=re.findall("<(?P<var>\S*)(?P<attr>[^>]*)>(?P<val>.*?)</(?P=var)>",content)
    if len(res)>=1:
        attreg="(?P<avr>\S+?)(?:(?:=(?P<quote>['\"])(?P<avl>.*?)(?P=quote))|(?:=(?P<avl1>.*?)(?:\s|$))|(?P<avl2>[\s]+)|$)"
        if len(res)>1:
            return [{i[0]:[{"@attributes":[{j[0]:(j[2] or j[3] or j[4])} for j in re.findall(attreg,i[1].strip())]},{"$values":getdict(i[2])}]} for i in res]
        else:
            return {res[0]:[{"@attributes":[{j[0]:(j[2] or j[3] or j[4])} for j in re.findall(attreg,res[1].strip())]},{"$values":getdict(res[2])}]}
    else:
        return content

with open("test.xml","r") as f:
    print(json.dumps(getdict(f.read().replace('\n',''))))
```
### Sample input
```xml
<details class="4b" count=1 boy>
    <name type="firstname">John</name>
    <age>13</age>
    <hobby>Coin collection</hobby>
    <hobby>Stamp collection</hobby>
    <address>
        <country>USA</country>
        <state>CA</state>
    </address>
</details>
<details class="4a" count=2 girl>
    <name type="firstname">Samantha</name>
    <age>13</age>
    <hobby>Fishing</hobby>
    <hobby>Chess</hobby>
    <address current="no">
        <country>Australia</country>
        <state>NSW</state>
    </address>
</details>
```
### Output
```json
[
  {
    "details": [
      {
        "@attributes": [
          {
            "class": "4b"
          },
          {
            "count": "1"
          },
          {
            "boy": ""
          }
        ]
      },
      {
        "$values": [
          {
            "name": [
              {
                "@attributes": [
                  {
                    "type": "firstname"
                  }
                ]
              },
              {
                "$values": "John"
              }
            ]
          },
          {
            "age": [
              {
                "@attributes": []
              },
              {
                "$values": "13"
              }
            ]
          },
          {
            "hobby": [
              {
                "@attributes": []
              },
              {
                "$values": "Coin collection"
              }
            ]
          },
          {
            "hobby": [
              {
                "@attributes": []
              },
              {
                "$values": "Stamp collection"
              }
            ]
          },
          {
            "address": [
              {
                "@attributes": []
              },
              {
                "$values": [
                  {
                    "country": [
                      {
                        "@attributes": []
                      },
                      {
                        "$values": "USA"
                      }
                    ]
                  },
                  {
                    "state": [
                      {
                        "@attributes": []
                      },
                      {
                        "$values": "CA"
                      }
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  },
  {
    "details": [
      {
        "@attributes": [
          {
            "class": "4a"
          },
          {
            "count": "2"
          },
          {
            "girl": ""
          }
        ]
      },
      {
        "$values": [
          {
            "name": [
              {
                "@attributes": [
                  {
                    "type": "firstname"
                  }
                ]
              },
              {
                "$values": "Samantha"
              }
            ]
          },
          {
            "age": [
              {
                "@attributes": []
              },
              {
                "$values": "13"
              }
            ]
          },
          {
            "hobby": [
              {
                "@attributes": []
              },
              {
                "$values": "Fishing"
              }
            ]
          },
          {
            "hobby": [
              {
                "@attributes": []
              },
              {
                "$values": "Chess"
              }
            ]
          },
          {
            "address": [
              {
                "@attributes": [
                  {
                    "current": "no"
                  }
                ]
              },
              {
                "$values": [
                  {
                    "country": [
                      {
                        "@attributes": []
                      },
                      {
                        "$values": "Australia"
                      }
                    ]
                  },
                  {
                    "state": [
                      {
                        "@attributes": []
                      },
                      {
                        "$values": "NSW"
                      }
                    ]
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  }
]
```