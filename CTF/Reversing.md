#### Ghidra
.data contains hardcoded values like variables and arrays.
Look for values there.

#### Keygens
##### Find key with length and ASCII-sum
```python
import random
import string

ASCII_SUM = 0x539
KEY_LENGTH = 0x10

def check_key(key):
    sum = 0
    for char in key:
        sum += ord(char)
    return sum

key = ""
while True:
    key += random.choice(string.ascii_lowercase + string.ascii_uppercase + string.digits + string.punctuation)
    sum = check_key(key)
    if sum > ASCII_SUM:
        key = ""
    elif sum == ASCII_SUM and len(key) == KEY_LENGTH:
        print(f"Found valid key: {key}")
```


### Brute local program
```python
from itertools import product
import subprocess


values = "delrsu"


for chars in product(values, repeat=6):
    flag = r"EPT{" + chars[0] + "8" + chars[1] + "1" + chars[2] + "5" + chars[3] + "3" + chars[4] + "4" + chars[5] + "7}"
    
    output = subprocess.run(f"echo {flag} | ./one", shell=True, stdout=subprocess.PIPE)
    print(output)

    if b"one true flag" in output.stdout:
        print(flag)
        break           
    
```
