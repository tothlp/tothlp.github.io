---
title: Verziószám kinyerése git tagből
feed: show
date: 23-02-2024
---

Nyilván ez egy kézzel heggesztett regex, de bármi kinyerhető így a szövegből.

```bash
version=$(git tag --points-at master | grep -Eo '[0-9]\.[0-9]\.[0-9]')
```