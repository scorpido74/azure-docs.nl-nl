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
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211413"
---
## <a name="general-entity-types"></a>Algemene entiteits typen:

### <a name="person"></a>Person

Herken namen van personen in de tekst.

Talen:
* Open bare Preview: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` en `Turkish`

| Naam subtype | Beschrijving                                                      | Beschikbaar vanaf model versie |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N.v.t.          | Herken bare namen van personen, bijvoorbeeld `Bill Gates`, `Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Het taak type of de rol van een persoon.

Talen:
* Open bare Preview: `English`

| Naam subtype | Beschrijving                                                                                | Beschikbaar vanaf model versie |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | Taak typen bijvoorbeeld `civil engineer`, `salesperson`, `chef`, `librarian`, `nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Locatie

Natuurlijke en door de mens gemaakte bezienswaardigheden, structuren, geografische functies en geopolitieke entiteiten.

Talen:

* Open bare Preview: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` en `Turkish`

| Naam subtype              | Beschrijving                                                                              | Beschikbaar vanaf model versie |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N.v.t.                       | locaties, bijvoorbeeld `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty`  | `2019-10-01`                           |
| Geopolitieke entiteit (GPE)-alleen Engelstalig| Steden, landen, statussen bijvoorbeeld `Seattle`, `Pennsylvania`, `South Africa`, `Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organisatie  

Erkende organisaties, ondernemingen, agentschappen en andere groepen personen. Bijvoorbeeld: bedrijven, politieke groepen, muziek banden, sport clubs, overheids instanties en open bare organisaties. Nationale en religions zijn niet opgenomen in dit entiteits type. 

Talen: 

* Open bare Preview: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` en `Turkish`

| Naam subtype | Beschrijving                                                                                             | Beschikbaar vanaf model versie |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | organisaties, bijvoorbeeld `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration`,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Gebeurtenis  

Historische, sociale en natuurlijke gebeurtenissen.  

Talen: 

* Open bare Preview: `English`

| Naam subtype | Beschrijving                                                            | Beschikbaar vanaf model versie |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | Gebeurtenissen zoals `wedding`, `hurricane`, `car accident`, `solar eclipse`, `American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Product  

Fysieke objecten van verschillende categorieën.  

Talen: 

* Open bare Preview: `English`

| Naam subtype | Beschrijving                                                                        | Beschikbaar vanaf model versie |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | Bijvoorbeeld `Microsoft Surface laptop`, `sunglasses`, `motorcycle`, `bag`, `Xbox` | `2020-02-01`                           |
| Berekenen    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Eigen  

Een entiteit die een functie of expertise beschrijft.  

Talen: 

* Open bare Preview: `English`

| Naam subtype | Beschrijving                                                                 | Beschikbaar vanaf model versie |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N.v.t.          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefoonnummer

Telefoon nummers (alleen Amerikaanse telefoon nummers). 

Talen:

* Open bare Preview: `English`

| Naam subtype | Beschrijving                                    | Beschikbaar vanaf model versie |
|--------------|------------------------------------------------|----------------------------------------|
| N.v.t.          | Telefoon nummers (VS), bijvoorbeeld `(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

E-mailadres. 

Talen:

* Open bare Preview: `English`

| Naam subtype | Beschrijving                                      | Beschikbaar vanaf model versie |
|--------------|--------------------------------------------------|----------------------------------------|
| N.v.t.          | E-mail adres, bijvoorbeeld `support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

Internet-Url's.

Talen:

* Open bare Preview: `English`

| Naam subtype | Beschrijving                                          | Beschikbaar vanaf model versie |
|--------------|------------------------------------------------------|----------------------------------------|
| N.v.t.          | Url's naar websites, bijvoorbeeld `https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP-adres

Internet Protocol adres

Talen:

* Open bare Preview: `English`

| Naam subtype | Beschrijving                              | Beschikbaar vanaf model versie |
|--------------|------------------------------------------|----------------------------------------|
| N.v.t.          | Netwerk adres bijvoorbeeld `10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Datum-en tijd entiteiten. 

* Beschikbaar vanaf model versie `2019-10-01`

Talen:

* Open bare Preview: `Chinese-Simplified`, `English`, `French`, `German` en `Spanish`

| Naam subtype    | Voorbeelden                     |
|-------------|------------------------------|
| N.v.t.         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Duur | `2.5 minutes`, `one and a half hours`         |
| Instellen | `every Saturday`         |

###  <a name="quantity"></a>Aantal

Cijfers en numerieke aantallen. 

* Beschikbaar vanaf model versie `2019-10-01`

Talen:

* Open bare Preview: `Chinese-Simplified`, `English`, `French`, `German` en `Spanish`

| Naam subtype    | Voorbeelden                     |
|-------------|------------------------------|
| Aantal         | `6`, `six`                   |
| Percentage  | `50%`, `fifty percent`       |
| Rang telwoord     | `2nd`, `second`              |
| Leeftijd         | `90 day old`, `30 years old` |
| Valuta    | `$10.99`, `€30.00`           |
| Dimensielideigenschap   | `10 miles`, `40 cm`          |
| Temperatuur | `32 degrees`, `10°C`         |
