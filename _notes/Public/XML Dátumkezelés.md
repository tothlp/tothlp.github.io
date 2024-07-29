---
title: XML Dátumkezelés
feed: show
slug: xml-datumkezeles
date: 29-07-2024
---

XML szerializálás (marshalling) vagy deszerilizálás (unmarshalling) során a dátumok egyedi formátumának kezeléséhez szükséges pár plusz lépést megtennünk.

Az adatmodellben annotálnunk kell a dátum mezőket a `@XmlJavaTypeAdapter` annotációval, amelynek értéke egy `XmlAdapter` implementáció lesz.

```kotlin
@field:XmlJavaTypeAdapter(ShortDateAdapter::class)
var createdDate: LocalDate? = null,
```

Ehhez nyilvánvalóan létre kell hoznunk a `ShortDateAdapter` osztályt is.

```kotlin
class ShortDateAdapter(
    dateFormat: String = "yyyy-MM-dd"
) : XmlAdapter<String, LocalDate>() {

    private val dateFormatter: DateTimeFormatter = DateTimeFormatter.ofPattern(dateFormat)

    override fun unmarshal(xml: String): LocalDate? {
        val parse = LocalDate.parse(xml, dateFormatter)
        return parse
    }

    override fun marshal(date: LocalDate): String {
        return dateFormatter.format(date)
    }
}
```

Ezzel a megoldással az alkalmazásunk képes lesz a megfelelő formátumban kezelni a dátumokat az XML műveletek során.

Ahogyan látható, itt a dátum formátuma egy fix, alapértelmezett értéket kapott. Ha ez az érték nem egy osztály-beli fix érték, hanem konstruktorban adjuk át, akkor felül is tudjuk írni. Ha konstruktorban adjuk át a formátumot, akkor fordításkor hibát kaphatunk, ugyanis az osztályunk nem rendelkezik default konstruktorral, amit az @XmlJavaTypeAdapter annotáció használhatna.

Ennek megoldására érdemes lehet létrehozni egy "központi" MarshallerFactory-t, aminek át tudjuk adni azokat az adaptereket, amiket használni akarunk:

```kotlin
class MarshallerFactory {
	inline fun <reified T> createMarshaller(adapters: List<XmlAdapter<String, *>>? = null): Marshaller =
		JAXBContext.newInstance(T::class.java).createMarshaller().apply {
			setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true)
			adapters?.forEach { setAdapter(it) }
		}
}
```

Bekötéshez:

```kotlin
private val customMarshaller = marshallerFactory.createMarshaller<MyCustomDataModel>(listOf(shortDateAdapter))
```

A pozitívum, hogy ezzel a kis plusz munkával a dátumformátumokat egyszerűen kivezethetjük konfigurációba is, pl. `application.yml` fájlba.