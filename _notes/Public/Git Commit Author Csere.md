---
title: Git Commit Author Csere
feed: show
date: 24-02-2024
---

Ha esetleg elcsesződne a gitconfig, vagy elfelejtettünk profilt váltani (GitKrakenben, heti szinten) és rossz userrel megy fel a commit, akkor egyrészt:

* Mehet egy jóvágású interactive rebase, lsd: [[sudo git ctrl-z]]
* Utána pedig a következő:

```bash
git commit --amend --author="Author Name <email@address.com>" --no-edit
```

A rebase leírása amúgy elég user-friendly, elmondja szépen mit kell még csinálni.