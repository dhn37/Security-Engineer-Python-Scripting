# 11. IOC Extraction, Enrichment, Scoring & Alerting

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

Build a single Python script that ingests raw security logs, extracts IOCs, enriches them with threat intel, scores risk, and outputs alerts for analyst review.

</aside>

### Input

In this question, we use the same function as the previous one. But we have to make the input from the `log_text` and `threat_intel`. 

```python
log_text = """
User login failed from 8.8.8.8
Suspicious request to http://evil.co/reset
Encoded payload observed: YWRtaW46cGFzc3dvcmQ=
Internal scan from 10.0.0.5
Another hit from 1.1.1.1
"""

threat_intel = {
    "8.8.8.8": {"confidence": 90, "source": "threat_intel"},
    "1.1.1.1": {"confidence": 20, "source": "threat_intel"},
    "10.0.0.5": {"confidence": 80, "source": "internal_log"}
}
```

### Making the input for the function

```python
ip_pattern = r"\b\d{1,3}(?:\.\d{1,3}){3}\b"
ips=re.findall(ip_pattern,log_text)

for i in ips:
    dict={}
    dict["ip"]=i
    if ipaddress.ip_address(i).is_private==True:
        dict["public"]=False
    else:
        dict["public"]=True
    
    if i in threat_intel:
        dict["confidence"]=threat_intel[i]["confidence"]
        dict["source"]=threat_intel[i]["source"]
    else:
        dict["confidence"]=0
        dict["source"]="unknown"
    
    iocs.append(dict)
```

### Final Code

```python
import ipaddress
import re

log_text = """
User login failed from 8.8.8.8
Suspicious request to http://evil.co/reset
Encoded payload observed: YWRtaW46cGFzc3dvcmQ=
Internal scan from 10.0.0.5
Another hit from 1.1.1.1
"""

threat_intel = {
    "8.8.8.8": {"confidence": 90, "source": "threat_intel"},
    "1.1.1.1": {"confidence": 20, "source": "threat_intel"},
    "10.0.0.5": {"confidence": 80, "source": "internal_log"}
}

ips=[]
iocs=[]
result=[]

ip_pattern = r"\b\d{1,3}(?:\.\d{1,3}){3}\b"
ips=re.findall(ip_pattern,log_text)

for i in ips:
    dict={}
    dict["ip"]=i
    if ipaddress.ip_address(i).is_private==True:
        dict["public"]=False
    else:
        dict["public"]=True
    
    if i in threat_intel:
        dict["confidence"]=threat_intel[i]["confidence"]
        dict["source"]=threat_intel[i]["source"]
    else:
        dict["confidence"]=0
        dict["source"]="unknown"
    
    iocs.append(dict)
        
    

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

![image.png](11%20IOC%20Extraction,%20Enrichment,%20Scoring%20&%20Alerting/image.png)