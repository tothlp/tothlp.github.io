---
title: Angular keresési paraméterek
feed: show
date: 23-02-2024
---

Ez olyankor lehet hasznos, amikor query paramétereket kell a request-hez fűzni. Sokkal elegánsabb megoldás, mint végig nullcheck-elni az összes paramétert és összelegózni az url stringet.

```tsx
let url = 'users?';
let params = new URLSearchParams();
params.append('name', filter.name);
params.append('age', filter.age);
let fullUrl = url + params.toString();
return this.http.get<UserResponse[]>(fullUrl);
```

Pl.: `/users?name=aladár&age=42`