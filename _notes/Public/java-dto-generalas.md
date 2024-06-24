---
title: Java DTO generálás CLI-ből (XSD / WSLT)
feed: show
date: 23-02-2024
---

## XSD
Intellij IDEA Ultimate-ből elég egyszerűen lehet .xsd-ből java classokat generálni, csakhogy nem mindig mindenkinél van ultimate, for obvious reasons.

Fel kell hozzá tenni a jaxb-t, JDK 11 óta nem része a JDK-nak.

[https://github.com/eclipse-ee4j/jaxb-ri](https://github.com/eclipse-ee4j/jaxb-ri)

[Jakarta XML Binding](https://eclipse-ee4j.github.io/jaxb-ri/)

**Példa futtatás:**

```bash
xjc -d out -npa -no-header -p hu.tothlp.proba data.xsd
```

Ha a generálandó fájlok több sémában vannak szétosztva, előfordulhat, hogy nincsenek felvéve a hivatkozások, körkörös hivatkozásokat pedig nem akarunk. Ilyenkor egy schema catalog-ot kell létrehozni, például:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<catalog xmlns='urn:oasis:names:tc:entity:xmlns:xml:catalog' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance' xsi:schemaLocation='urn:oasis:names:tc:entity:xmlns:xml:catalog Catalog.xsd' prefer="public">
<!--reference as name & filepath-->
	<public publicId="http://schemas.tothlp.hu/common" uri="./common.xsd"/>
	<public publicId="http://schemas.tothlp.hu/base" uri="./invoiceBase.xsd"/>
</catalog>
```

Ha ezt lementjük `catalog.xml` néven, akkor a következővel tudjuk érvényesíteni generáláskor:

```bash
xjc -d out -npa -no-header -p hu.tothlp.proba -catalog catalog.xml data.xsd
```

Ha egy adott element több fájlban is hivatkozva van, collide-olhatnak generáláskor. Pl:
>  A class/interface with the same name "hu.tothlp.proba.SampleDTO" is already in use. Use a class customization to resolve this conflict.

Volt erre egy olyan megoldás, hogy egy egyedi jaxb bindingot készítek:

```xml
<jxb:bindings version="3.0"
              xmlns:jxb="https://jakarta.ee/xml/ns/jaxb"
              xmlns:xjc="http://java.sun.com/xml/ns/jaxb/xjc">

    <jxb:globalBindings>
        <xjc:simple/>
    </jxb:globalBindings>

</jxb:bindings>
```

Ezzel viszont az a gond, hogy duplikációk jönnek létre. Szóval, ha van egy `Person` osztályom és egy `Company` osztályom, és mindkettő külön xsd-ből hívatkozik az `Address` osztályra, akkor bindiggal létrejön egy Address és egy Address2 osztály, nyilván egyik egyikre, másik másikra hivatkozik. Ez nem igazán elegáns, sőt.

Egyelőre úgy tudtam megoldani a problémát, hogy feladtam az egyedi package nevet, és a következő paraméterrel generáltattam:

```bash
xjc -d out -npa -no-header -XautoNameResolution -catalog catalog.xml data.xsd
```

Ezzel a megoldással minden legenerálódik, ugyanabba a package-be, amit az xsd-ből kitalál az xjc. Legtökéletesebb megoldás nyilván az lenne, ha saját package-embe lehetne generálni, viszont ahhoz vszg. egy binding-ot kellene létrehozni, amiben minden egyes ütközésre megadjuk, hogy a 2 generálandó osztályból melyik legyen használva.

## WSDL

WSDL mehet intellij-ből, de fel kell tenni, pl `C:\jaxws-ri` alá:

[Maven Repository: com.sun.xml.ws » jaxws-ri » 4.0.2](https://mvnrepository.com/artifact/com.sun.xml.ws/jaxws-ri/4.0.2)

Ezt beállítani a Settings/Tools/WebServices alatt.
