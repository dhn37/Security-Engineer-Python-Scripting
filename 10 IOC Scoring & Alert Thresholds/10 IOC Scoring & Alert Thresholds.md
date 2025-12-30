# 10. IOC Scoring & Alert Thresholds

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

Given a set of Indicators of Compromise (IOCs) with contextual metadata, compute a **risk score** per IOC and decide whether it should trigger an **alert** based on defined thresholds.

</aside>

### Input

```python
iocs = [
    {"ip": "8.8.8.8", "confidence": 90, "source": "threat_intel", "public": True},
    {"ip": "1.1.1.1", "confidence": 20, "source": "threat_intel", "public": True},
    {"ip": "10.0.0.5", "confidence": 80, "source": "internal_log", "public": False},
    {"ip": "172.16.0.3", "confidence": 60, "source": "threat_intel", "public": False}
]
```

Rules for score:

Start with score = confidence

If public == True, add +20

If source == "threat_intel", add +10

If public == False, subtract −10

Rules for alert:

If score≥80; alert=True

else; alert=False

### Logic

We need to make sure that the dictionary is defined every time inside the loop itself to avoid duplicate values inside it.
Also make sure to make the dictionary as a separate entity and append it to the list. Makes things much simpler.

```python
for i in iocs:
    dict={}
    score=i["confidence"]
    if i["source"]=="threat_intel":
        score+=20
    if i["public"]==True:
        score+=10
    if i["public"]==False:
        score-=10
    
    dict["ip"]=i["ip"]
    dict["score"]=score
    
    if score>=80:
        dict["alert"]=True
    else:
        dict["alert"]=False
```

### Final Code

```python
iocs = [
    {"ip": "8.8.8.8", "confidence": 90, "source": "threat_intel", "public": True},
    {"ip": "1.1.1.1", "confidence": 20, "source": "threat_intel", "public": True},
    {"ip": "10.0.0.5", "confidence": 80, "source": "internal_log", "public": False},
    {"ip": "172.16.0.3", "confidence": 60, "source": "threat_intel", "public": False}
]

result=[]

for i in iocs:
    dict={}
    score=i["confidence"]
    if i["source"]=="threat_intel":
        score+=20
    if i["public"]==True:
        score+=10
    if i["public"]==False:
        score-=10
    
    dict["ip"]=i["ip"]
    dict["score"]=score
    
    if score>=80:
        dict["alert"]=True
    else:
        dict["alert"]=False
    
    result.append(dict)
    
print(result)
```

### Screenshot

![image.png](10%20IOC%20Scoring%20&%20Alert%20Thresholds/image.png)