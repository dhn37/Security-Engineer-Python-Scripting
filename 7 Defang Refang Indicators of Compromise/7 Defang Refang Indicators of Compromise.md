# 7. Defang / Refang Indicators of Compromise

# Objective

Basically obfuscation and de-obfuscation

<aside>
<img src="https://www.notion.so/icons/bullseye_yellow.svg" alt="https://www.notion.so/icons/bullseye_yellow.svg" width="40px" />

Given a block of security text, **defang** IOCs (IPs, domains, URLs) so they are safe to share (won’t auto-link/click), and also be able to **refang** them back to normal form.

</aside>

### Input

```cpp
text = """
Investigate these IOCs:
- http://login-microsoft-secure.com/account/verify
- https://evil.co/path?x=1
- sub.evil.co
- 8.8.8.8
- 192.168.1.10
"""
```

### Logic

We need to replace the whole text `https://` and `http://` so that we don’t obfuscate the same word in a sentence. So, to make sure we only target URL’s we enter it with the colons and the forward slashes.

```cpp
def defang(text):
    text=text.replace("https://","hxxps://")
    text=text.replace("http://","hxxp://")
    text=text.replace(".","[.]")
    return text
    
def refang(text):
    text=text.replace("hxxps://","https://")
    text=text.replace("hxxp://","http://")
    text=text.replace("[.]",".")
    return text
```

### Final Code

```cpp
text = """
Investigate these IOCs:
- http://login-microsoft-secure.com/account/verify
- https://evil.co/path?x=1
- sub.evil.co
- 8.8.8.8
- 192.168.1.10
"""

def defang(text):
    text=text.replace("https://","hxxps://")
    text=text.replace("http://","hxxp://")
    text=text.replace(".","[.]")
    return text
    
def refang(text):
    text=text.replace("hxxps://","https://")
    text=text.replace("hxxp://","http://")
    text=text.replace("[.]",".")
    return text
    
defanged_text=defang(text)
refanged_text=refang(defanged_text)

print("======DEFANGED TEXT=======")
print(defanged_text)

print("======REFANGED TEXT=======")
print(refanged_text)
```

### Screenshot

![image.png](7%20Defang%20Refang%20Indicators%20of%20Compromise/image.png)