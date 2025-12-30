# 4. API Calls & JSON Response Parsing

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

**Parse a JSON response from a threat-intelligence API and extract only malicious IP addresses along with their confidence scores for further security analysis.**

</aside>

### Input

```python
api_response = {
    "data": [
        {"ip": "10.0.0.5", "malicious": True, "confidence": 85},
        {"ip": "192.168.1.10", "malicious": False, "confidence": 5},
        {"ip": "172.16.0.3", "malicious": True, "confidence": 70}
    ]
}
```

### Logic of the code

The input has a Dictionary → list → dictionaries

```python
for i in api_response["data"]:
    if i["malicious"]==True:
        result_dict[i["ip"]]=i["confidence"]
    else:
        continue
```

### Final Code

```python
result_dict={}

api_response = {
    "data": [
        {"ip": "10.0.0.5", "malicious": True, "confidence": 85},
        {"ip": "192.168.1.10", "malicious": False, "confidence": 5},
        {"ip": "172.16.0.3", "malicious": True, "confidence": 70}
    ]
}

for i in api_response["data"]:
    if i["malicious"]==True:
        result_dict[i["ip"]]=i["confidence"]
    else:
        continue
        
print(result_dict)
```

## Screenshot

![image.png](4%20API%20Calls%20&%20JSON%20Response%20Parsing/image.png)