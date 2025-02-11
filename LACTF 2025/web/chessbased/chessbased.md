# Chessbase writeups
### Team: UIT.4KySiNguLam

![image](https://github.com/user-attachments/assets/a8e920da-4bd4-4130-a5e7-4d907c3eca0f)

## Overview
- Chessbased is a web application for browsing chess openings (inspired by ChessBase). There exist no injection vulnerabilities in this client-side challenge.

![image](https://github.com/user-attachments/assets/4519d0a3-5474-4765-9654-3b8b675f99a1)


## Vulnerability
- The backend code is as below:
```javascript
app.get('/render', (req, res) => {
  const id = req.query.id;
  const op = lookup.get(id);
  res.send(`
    <p>${op?.name}</p>
    <p>${op?.moves}</p>
  `);
});

app.post('/search', (req, res) => {
  if (req.headers.referer !== challdomain) {
    res.send('only challenge is allowed to make search requests');
    return;
  }
  const q = req.body.q ?? 'n/a';
  const hasPremium = req.cookies.adminpw === adminpw;
  for (const op of openings) {
    if (op.premium && !hasPremium) continue;
    if (op.moves.includes(q) || op.name.includes(q)) {
      return res.redirect(`/render?id=${encodeURIComponent(op.name)}`);
    }
  }
  return res.send('lmao nothing');
});
```

- There is no authentication for `/render` endpoint as well.
## Exploitation
- Type this URL to get flag
```URL
https://chessbased.chall.lac.tf/render?id=flag
```

![image](https://github.com/user-attachments/assets/372b3be9-6ee5-4677-88ce-cace8a8edd99)
