# 2. Log Correlation Using Hashmaps (IP → Requests)

# Objective

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

**Correlate web request logs by IP address and group all requested URL paths per IP to understand user behavior and detect potentially malicious access patterns.**

</aside>

### Input

Assume input as a list of logs

```python
logs = [
    "10.0.0.5 GET /login",
    "192.168.1.10 GET /",
    "10.0.0.5 POST /login",
    "10.0.0.5 GET /admin",
    "192.168.1.10 GET /dashboard",
    "10.0.0.5 GET /login"
]
```

### Getting IP and the path

```python
for i in logs:
    each_log = i.split()
    ip = each_log[0]
    path = each_log[-1]
```

### Putting the path in a list, so that we can append values to it

Dictionary values can also be lists

```python
    if ip in result_dict:
        if path not in result_dict[ip]:
            result_dict[ip].append(path)
    else:
        result_dict[ip]=[path]
```

### Final Code

```python
result_dict={}

logs = [
    "10.0.0.5 GET /login",
    "192.168.1.10 GET /",
    "10.0.0.5 POST /login",
    "10.0.0.5 GET /admin",
    "192.168.1.10 GET /dashboard",
    "10.0.0.5 GET /login"
]

for i in logs:
    each_log = i.split()
    ip = each_log[0]
    path = each_log[-1]
    if ip in result_dict:
        if path not in result_dict[ip]:
            result_dict[ip].append(path)
    else:
        result_dict[ip]=[path]

print(result_dict)
	
```

### Screenshot

![image.png](2%20Log%20Correlation%20Using%20Hashmaps%20(IP%20%E2%86%92%20Requests)/image.png)