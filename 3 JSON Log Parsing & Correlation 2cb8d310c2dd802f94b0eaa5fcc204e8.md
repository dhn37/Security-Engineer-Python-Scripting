# 3. JSON Log Parsing & Correlation

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

**Process authentication logs provided in JSON format and count the number of failed login attempts per user, ignoring successful logins.**

</aside>

### Input

Assume the input to be a list of python dictionaries

```python
logs = [
    {"user": "alice", "ip": "10.0.0.5", "status": "FAIL"},
    {"user": "bob", "ip": "192.168.1.10", "status": "SUCCESS"},
    {"user": "alice", "ip": "10.0.0.5", "status": "FAIL"},
    {"user": "alice", "ip": "10.0.0.8", "status": "SUCCESS"},
    {"user": "bob", "ip": "192.168.1.10", "status": "FAIL"},
    {"user": "bob", "ip": "192.168.1.10", "status": "FAIL"}
]
```

## Logic of the code

Getting single logs and checking. If FAIL and user present then +=1, else just +1.

```python
for i in logs:
    if i["status"]=="FAIL":
        if i["user"] in result_dict:
            result_dict[i["user"]]+=1
        else:
            result_dict[i["user"]]=1
```

### Final Code

```python
result_dict={}

logs = [
    {"user": "alice", "ip": "10.0.0.5", "status": "FAIL"},
    {"user": "bob", "ip": "192.168.1.10", "status": "SUCCESS"},
    {"user": "alice", "ip": "10.0.0.5", "status": "FAIL"},
    {"user": "alice", "ip": "10.0.0.8", "status": "SUCCESS"},
    {"user": "bob", "ip": "192.168.1.10", "status": "FAIL"},
    {"user": "bob", "ip": "192.168.1.10", "status": "FAIL"}
]

for i in logs:
    if i["status"]=="FAIL":
        if i["user"] in result_dict:
            result_dict[i["user"]]+=1
        else:
            result_dict[i["user"]]=1

print(result_dict)
```

### Screenshot

![image.png](3%20JSON%20Log%20Parsing%20&%20Correlation/image.png)