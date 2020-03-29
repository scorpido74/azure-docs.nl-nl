---
title: Algemene benoemde entiteiten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211413"
---
## <a name="general-entity-types"></a>Algemene entiteitstypen:

### <a name="person"></a>Person

Persoonsnamen herkennen in tekst.

Talen:
* `Arabic`Openbare voorvertoning: `Chinese-Simplified`, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , , `Danish`, , , , , , , , , , , , , , , , en `Czech``Turkish`

| Subtypenaam | Beschrijving                                                      | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N.v.t.          | Herkende persoonsnamen, `Bill Gates`bijvoorbeeld ,`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Functietype of rol in handen van een persoon.

Talen:
* Openbare preview:`English`

| Subtypenaam | Beschrijving                                                                                | Beschikbaar vanaf modelversie |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | Functietypen bijvoorbeeld `civil engineer` `salesperson`, `chef` `librarian`, ,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Locatie

Natuurlijke en door de mens gemaakte oriëntatiepunten, structuren, geografische kenmerken en geopolitieke entiteiten.

Talen:

* `Arabic`Openbare voorvertoning: `Chinese-Simplified`, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , , `Danish`, , , , , , , , , , , , , , , , en `Czech``Turkish`

| Subtypenaam              | Beschrijving                                                                              | Beschikbaar vanaf modelversie |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N.v.t.                       | locaties, bijvoorbeeld `Atlantic Ocean`, `library` `Eiffel Tower`,`Statue of Liberty`  | `2019-10-01`                           |
| Geopolitieke Entiteit (GPE) - Alleen Engels| Steden, landen, staten `Seattle` `Pennsylvania`bijvoorbeeld `South Africa`, ,`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organisatie  

Erkende organisaties, bedrijven, agentschappen en andere groepen mensen. Bijvoorbeeld: bedrijven, fracties, muziekgroepen, sportclubs, overheidsinstanties en overheidsorganisaties. Nationaliteiten en religies zijn niet opgenomen in dit entiteitstype. 

Talen: 

* `Arabic`Openbare voorvertoning: `Chinese-Simplified`, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , , `Danish`, , , , , , , , , , , , , , , , en `Czech``Turkish`

| Subtypenaam | Beschrijving                                                                                             | Beschikbaar vanaf modelversie |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | organisaties, bijvoorbeeld `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration`,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Gebeurtenis  

Historische, sociale en natuurlijk voorvallen.  

Talen: 

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                                            | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | Gebeurtenissen zoals `wedding` `hurricane`, `car accident` `solar eclipse`, , ,`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Product  

Fysieke objecten van verschillende categorieën.  

Talen: 

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                                                        | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | Bijvoorbeeld , `Microsoft Surface laptop` `sunglasses`, `motorcycle` `bag`, , ,`Xbox` | `2020-02-01`                           |
| Berekenen    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Behendigheid  

Een entiteit die een vermogen of expertise beschrijft.  

Talen: 

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                                                 | Beschikbaar vanaf modelversie |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefoonnummer

Telefoonnummers (alleen telefoonnummers in de VS). 

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                    | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------------|----------------------------------------|
| N.v.t.          | Amerikaanse telefoonnummers, bijvoorbeeld`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

E-mailadres. 

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                      | Beschikbaar vanaf modelversie |
|--------------|--------------------------------------------------|----------------------------------------|
| N.v.t.          | E-mailadres, bijvoorbeeld`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

Internet-URL's.

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                          | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------------------|----------------------------------------|
| N.v.t.          | URL's naar websites, bijvoorbeeld`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP-adres

Adres van internetprotocol

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                              | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------|----------------------------------------|
| N.v.t.          | Netwerkadres bijvoorbeeld`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Datum- en tijdentiteiten. 

* Beschikbaar vanaf modelversie`2019-10-01`

Talen:

* Openbare `Chinese-Simplified`preview: `English` `French`, `German` , en`Spanish`

| Subtypenaam    | Voorbeelden                     |
|-------------|------------------------------|
| N.v.t.         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Duur | `2.5 minutes`, `one and a half hours`         |
| Instellen | `every Saturday`         |

###  <a name="quantity"></a>Aantal

Getallen en numerieke hoeveelheden. 

* Beschikbaar vanaf modelversie`2019-10-01`

Talen:

* Openbare `Chinese-Simplified`preview: `English` `French`, `German` , en`Spanish`

| Subtypenaam    | Voorbeelden                     |
|-------------|------------------------------|
| Aantal         | `6`, `six`                   |
| Percentage  | `50%`, `fifty percent`       |
| Ordinale     | `2nd`, `second`              |
| Leeftijd         | `90 day old`, `30 years old` |
| Valuta    | `$10.99`, `€30.00`           |
| Dimensie   | `10 miles`, `40 cm`          |
| Temperatuur | `32 degrees`, `10°C`         |
