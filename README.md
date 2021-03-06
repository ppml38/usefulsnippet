## Convert `xml` to `json`
Try below code to convert `xml` files into `json`, including xml attributes.
* Can parse empty tags`<tag/>` and tags with only attributes`<tag var=val/>`
* Files with more than one xml, can also be parsed to result `list` of jsons
    
```python
import re
import json

def getdict(content):
    res=re.findall("<(?P<var>\S*)(?P<attr>[^/>]*)(?:(?:>(?P<val>.*?)</(?P=var)>)|(?:/>))",content)
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

## Convert `csv` data into `xml` data
```python
with open("input.csv","r") as f:
    head=[i.strip() for i in f.readline().split(",")]
    line=f.readline()
    while line:
        print("".join(["<%s>%s</%s>"%(i,a.strip(),i) for (i,a) in zip(head,line.split(","))]))
        line=f.readline()
```

## Convert python `string`, that also include `hex` notation, into `byte array`
**Code**
```python
output=[ord(i) for i in re.findall(".",input)]
```
**Sample input**
```
Hello\xad
```
**Output**
```
[72, 101, 108, 108, 111, 173]
```

## Get user input until user gives valid input
Below code may help.
```python
age=(lambda i,f: f(i,f))(input("Please enter your age: "),lambda i,f: i if i.isdigit() else f(input("Please enter your age: "),f))
print("You are able to vote in the united states" if int(age)>=18 else "You are not able to vote in the united states",end='')
```
If you want to have maximum tries, say 3, use below code
```python
age=(lambda i,n,f: f(i,n,f))(input("Please enter your age: "),1,lambda i,n,f: i if i.isdigit() else (None if n==3 else f(input("Please enter your age: "),n+1,f)))
print("You are able to vote in the united states" if age and int(age)>=18 else "You are not able to vote in the united states",end='')
```

## Handling `blur` event triggered when `Launch application` confirmation pops up in browser
Generally this can be solved by handling `visibilitychange` event instead of `blur` as below.
```javascript
document.addEventListener("visibilitychange", function() {
    //Change your view here
});
```   
More details on [here](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API). Please note there are browser specific events too. 
```
"visibilitychange"
"msvisibilitychange"
"webkitvisibilitychange"
```


In case, you want to handle the situation when user clicks out of browser keeping the site visible, as well. Handle `blur` event instead. 

But now, 'launch application pop-up' which also blurs the window, to be handled specifically. It does not fire any event to handle  

we can have a quick work around to handle it.   
The idea is based on below 2 behaviors,
1. The link that fires this popup, will not start with http or https. for example, zoom uses url that starts with `zoommtg://` to launch native application.
2. `onclick` events fired before `onblur` event.   

Hence, Introducing a flag on `click` event, will help us handle this popup.
```javascript
changeViewFlag=true;

window.onblur=function(event){
        if(changeViewFlag==true)
        {
            //Change your view here
        } 
        changeViewFlag=true;
    }

window.onclick=function(event){
        if(event.target.href!=null &&
           event.target.href.split("://").length>1 &&
           ["http","https"].indexOf(event.target.href.split("://")[0])==-1)
           {
             changeViewFlag=false;
           }
    }
```
## Convert `epoch` time into Day of the week
```python
import calendar
import datetime
import time
dateproc=1597753682
day=calendar.day_name[datetime.datetime.strptime(time.strftime('%d %m %Y', time.localtime(dateproc)),'%d %m %Y').weekday()]

print(day)
```
## Regex to match variable declaration in javascript
```regex
(?:var|const|let)\s*\S*\s*=\s*((['"`])|(?=\d))(.*)(?(2)(?=\2).|(?<=\d)(?:;|\s))
```
Captured group \3 will have value
## Python basic sniffer
https://gist.github.com/carcigenicate/dfb7e367f5d01e100305eb93955a7521
