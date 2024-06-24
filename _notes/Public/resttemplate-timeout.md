---
title: RestTemplate Timeout
feed: show
date: 07-03-2024
---
Külső rendszerrel való http kommunikációnál (pl. RESTTemplate) mindig legyen beállítva timeout, mert a Spring defaultja végtelen, szóval ha a távoli szerverrel gebasz van, nem fog elhasalni a kapcsolat. Ha ez nem több szálon fut, szerintem simán dögleszthet le scheduled taskokat is.