# Lucky-flag write-up
### Team: UIT.4KySiNguLam

![image](https://github.com/user-attachments/assets/fe4cdbe6-a2a8-4e5b-9803-6dd83f69d8db)
## Overview
In this challenge, player need to find out flag hidden in a web. Flag only appear when user click one in a lot of box defined in site.
## Vulnerability
**Client-Side Secret Disclosure**: Flag is completely stored and mapulated on browser. This mean everyone can open Dev Tools (pressing F12), look into source code and analyze logic to get flag.
## Exploitation
1. **Overlook**
- We can see a lot of box named flag. When we click a box, we receive an alert “no flag here” in most case but if you are a lucky boy, you can get flag just one click:)).
![image](https://github.com/user-attachments/assets/8b55a1af-a6de-4e33-9236-7a1cb48d629d)
- Let’s take a look in page source.
![image](https://github.com/user-attachments/assets/75c4541a-0ad4-4a5d-80e7-cd447c5781e5)
- We can ignore style.css because it just a css file, main.js is more doubtful. Click it!
![image](https://github.com/user-attachments/assets/c652240d-214e-4cf5-901a-427e4215149f)
2. **Anal yzation**
- Let analyze it.
```javascript
const boxes = document.querySelectorAll('.box');
let flagbox = boxes[Math.floor(Math.random() * boxes.length)];
```
- One box is randomly selected then saved into flagbox.
```javascript
let enc = `"\\u000e\\u0003\\u0001\\u0016\\u0004\\u0019\\u0015V\\u0011=\\u000bU=\\u000e\\u0017\\u0001\\t=R\\u0010=\\u0011\\t\\u000bSS\\u001f"`;
```
- This is a string included Unicode charset
```javascript
for (let i = 0; i < enc.length; ++i) {
  try {
    enc = JSON.parse(enc);
  } catch (e) { }
}
```
- The loop repeatedly calls JSON.parse on the string enc. This is done because the string might have been encoded multiple times . By repeatedly parsing it, the code attempts to fully "unpack" or convert the string into its proper format. If a call to JSON.parse fails , the error is caught and ignored, and the loop continues.
```javascript
let rw = [];
for (const e of enc) {
  rw['\x70us\x68'](e['\x63har\x43ode\x41t'](0) ^ 0x62);
}
const x = rw['\x6dap'](x => String['\x66rom\x43har\x43ode'](x));
```
- The for loop iterates over each element (e) in the enc string. For each character e, the code retrieves its ASCII value using e.charCodeAt(0). It then applies an XOR operation with the constant 0x62 (which is 98 in decimal). The result of the XOR operation (an integer representing a decoded ASCII code) is pushed into an array rw. The code then maps each number in rw to its corresponding character using String.fromCharCode. inally, the mapped characters are joined together into a single string (using the obfuscated join method '\x6dap'), resulting in the final flag.

3. **Get flag**

- By the logic we have analyze above, i write a python program to solve this challenge.
```python
import json

enc = "\"\\u000e\\u0003\\u0001\\u0016\\u0004\\u0019\\u0015V\\u0011=\\u000bU=\\u000e\\u0017\\u0001\\t=R\\u0010=\\u0011\\t\\u000bSS\\u001f\""

for i in range(len(enc)):
    try:
        enc = json.loads(enc)
    except Exception:
        pass
decoded_chars = [chr(ord(ch) ^ 0x62) for ch in enc]
flag = "".join(decoded_chars)
print(flag)
```
- Just get it:
![image](https://github.com/user-attachments/assets/1cd72b6e-37ee-4435-bd4b-4a013f06b071)

