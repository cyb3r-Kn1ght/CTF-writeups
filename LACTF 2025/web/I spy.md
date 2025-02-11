# I spy... write up
### Team: UIT.4KySiNguLam

![image](https://github.com/user-attachments/assets/c101b858-61a0-459b-9f85-24943925d21c)
## Overview
In this challenge, you need to have a knowledge about web structure, HTTP request, file on webpath, dig command to solve this challenge.
## Vulnerability
There are no vulnerability here, the author just ask us for each question about web. If you send a correct token, the web will goto next level.
## Exploitation
1. **Overlook**
- A website with a token input and next stage button.

![image](https://github.com/user-attachments/assets/f09fc9c6-0029-41a1-abad-f06df5f85f51)

2. **Analyzation**
- Lets type under token.

- Wow, It actually goes to the next level.

![image](https://github.com/user-attachments/assets/6eba60b1-d6d0-4a7a-b876-06ed794949cd)
- So we can guess if we reach final stage, we can get flag as well. Lets exploit!

3. **Exploitation**
- In current stage, the hint is "**A token in the HTML source code...**". Press F12 then click **Elements** to see **HTML source code**.

![image](https://github.com/user-attachments/assets/9b738275-d640-42b9-9d0e-43a9ec21ba70)
- Next stage, the hint is "**A token in the JavaScript console...**". Click **Console** in F12 mode.

![image](https://github.com/user-attachments/assets/2f7068ca-724f-4a91-a966-c6fa1218ffc3)
- Next stage, the hint is "**A token in the stylesheet...**". Click **Sources** then choose the file end with **.css**.

![image](https://github.com/user-attachments/assets/dffa43b1-deef-46c6-8e8c-d5f27edf42d7)
- Next stage, the hint is "**A token in javascript code...**". Choose the file end with **.js** in **Sources**.

![image](https://github.com/user-attachments/assets/97bc9a1f-e22c-493e-acf6-fff12ad005f5)
- Next stage, the hint is "**A token in a header...**". Open **Burp Suite** then look at response, we can see the token is in **X-token** header.

![image](https://github.com/user-attachments/assets/75d4b20b-5a33-4543-806a-5967650cec64)
- Next stage, the hint is "**A token in a cookie**". Keep current **Burp Suite** process then look at our request, we can see the token is in **a-token** value in **Cookie** header.

![image](https://github.com/user-attachments/assets/b56ed14a-44d4-431a-9e33-c89472ca63e5)
- Next stage, the hint is "**A token where the robots are forbidden from visiting...**". Lets try **/robots.txt** filepath.

![image](https://github.com/user-attachments/assets/65d055c5-2315-4d88-9197-051c78eb94f9)
- Hmmm.., There is no token here but we can see a doubtful filepath, try it~~!

![image](https://github.com/user-attachments/assets/5fe6ec5b-3ab6-484a-8617-cf903a144114)
- Next stage, the hint is "**A token where Google is told what pages to visit and index...**". Lets try **/sitemap.xml** filepath.

![image](https://github.com/user-attachments/assets/28248768-1041-4b1e-86cb-d8870acb91e3)
- Next stage, the hint is "**A token received when making a DELETE request to this page...**". Use **Repeat** tab in **Burp Suite** to edit and send **DELETE** request.

![image](https://github.com/user-attachments/assets/925ec376-70b4-4e66-8850-2a48acef1045)
- Final stage , the hint is "**A token in a TXT record at i-spy.chall.lac.tf...**". Lets try **dig** command for TXT file.
```bash
dig TXT i-spy.chall.lac.tf
```

![image](https://github.com/user-attachments/assets/9797d977-224c-4783-8ac9-c0ae480bbe05)
3. **Get Flag**
- Just get it.
![image](https://github.com/user-attachments/assets/7a47b995-7a0d-4f94-b8c6-cf53e79c48de)
