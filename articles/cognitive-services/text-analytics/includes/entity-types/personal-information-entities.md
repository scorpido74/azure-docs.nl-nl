---
title: Benoemde entiteiten voor persoonlijke gegevens
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 57be24142a8504347f420e5780e9621cd2eac91d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778239"
---
## <a name="personal-information-entity-types"></a>Entiteit typen persoonlijke informatie:

### <a name="person"></a>Person
Herken namen van personen in de tekst.

Talen:
* Open bare Preview:`English`

| Naam subtype | Beschrijving                                               | Beschikbaar vanaf model versie |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N.v.t.          | Herkende namen `Bill Gates` , bijvoorbeeld`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organisatie  

Organisaties, ondernemingen, agentschappen, bedrijven, klaveren en andere groepen mensen te herkennen.

Talen: 

* Open bare Preview:`English`

| Naam subtype | Beschrijving                                                                                       | Beschikbaar vanaf model versie|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N.v.t.          | organisaties `Microsoft` , bijvoorbeeld, `NASA``National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Telefoonnummer

Telefoon nummers (alleen Amerikaanse telefoon nummers). 

Talen:

* Open bare Preview:`English`

| Naam subtype | Beschrijving                                    | Beschikbaar vanaf model versie |
|--------------|------------------------------------------------|----------------------------------------|
| N.v.t.          | Telefoon nummers van VS, bijvoorbeeld`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

E-mailadres. 

Talen:

* Open bare Preview:`English`

| Naam subtype | Beschrijving                                      | Beschikbaar vanaf model versie |
|--------------|--------------------------------------------------|----------------------------------------|
| N.v.t.          | E-mail adres, bijvoorbeeld`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

Internet-Url's.

Talen:

* Open bare Preview:`English`

| Naam subtype | Beschrijving                                          | Beschikbaar vanaf model versie |
|--------------|------------------------------------------------------|----------------------------------------|
| N.v.t.          | Url's naar websites, bijvoorbeeld`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>IP-adres

Internet Protocol adres

Talen:

* Open bare Preview:`English`

| Naam subtype | Beschrijving                              | Beschikbaar vanaf model versie |
|--------------|------------------------------------------|----------------------------------------|
| N.v.t.          | Netwerk adres bijvoorbeeld`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Aantal 

Numerieke aantallen

Talen:

* Open bare Preview:`English`

| Naam subtype | Beschrijving                   | Beschikbaar vanaf model versie |
|--------------|-------------------------------|----------------------------------------|
| Leeftijd          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Datum-en tijd entiteiten

Talen:

* Open bare Preview:`English`

| Naam subtype | Beschrijving                   | Beschikbaar vanaf model versie |
|--------------|-------------------------------|----------------------------------------|
| Datum         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>EU GPS-coördinaten

 GPS-coördinaten voor locaties binnen de Europese Unie. 

Talen:

* Open bare Preview:`English`

| Naam subtype | Beschrijving                               | Beschikbaar vanaf model versie |
|--------------|-------------------------------------------|----------------------------------------|
| N.v.t.          | GPS-coördinaten binnen de Europese Unie | `2019-10-01`                           |

### <a name="azure-information"></a>Azure-gegevens

Identificeer bare Azure-informatie, inclusief verificatie gegevens en verbindings reeksen. 

* Beschikbaar vanaf model versie `2019-10-01` .

Talen:

* Open bare Preview:`English`

| Naam subtype                          | Beschrijving                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Verificatie sleutel voor Azure DocumentDB             | Autorisatie sleutel voor een Azure DocumentDB-server.                           |
| Verbindings reeks voor Azure IAAS-data base | Verbindings reeks voor een Azure Infrastructure as a service-data base (IaaS). |
| Azure SQL-verbindings reeks           | De verbindings reeks voor een Azure-SQL database.                                |
| Azure IoT-verbindings reeks           | De verbindings reeks voor Azure Internet der dingen (IoT).                        |
| Wacht woord voor de publicatie-instelling van Azure        | Wacht woord voor Azure Publish-instellingen.                                        |
| Verbindings reeks Azure Redis Cache   | Verbindings reeks voor een Azure-cache voor redis.                             |
| Azure SAS                             | Verbindings reeks voor Azure-software als een service (SAS).                     |
| Verbindings reeks Azure Service Bus   | Verbindings reeks voor een Azure service bus.                                |
| Sleutel van Azure Storage-account             | Account sleutel voor een Azure-opslag account.                                   |
| Sleutel van Azure Storage-account (algemeen)   | Algemene account sleutel voor een Azure-opslag account.                           |
| Verbindings reeks SQL Server          | Verbindings reeks voor een SQL-Server.                                         |

### <a name="identification"></a>Identificatie

* Beschikbaar vanaf model versie `2019-10-01` .

Talen:

* Open bare Preview:`English`

#### <a name="financial-account-identification"></a>Id van financiële rekening

| Naam subtype               | Beschrijving                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-route nummers        | Transit routerings nummers van de Amerikaanse Bank (ABA).                  |
| SWIFT-code                 | SWIFT-codes voor informatie over de betalings instructie.                           |
| Creditcard                | Creditcard nummers.                                                       |
| IBAN-code                  | IBAN-codes voor informatie over de betalings instructie.                            |

#### <a name="government-and-countryregion-specific-identification"></a>Sofi en land/regio-specifieke identificatie

De onderstaande entiteiten worden gegroepeerd en weer gegeven op land/regio:

Argentinië
* Sofi-nummer (National Identity)

Australië
* BTW-bestands nummer 
* Licentie-ID van stuur programma
* Pass Port-ID
* Medisch account nummer
* bankrekening nummers (bijvoorbeeld controle, besparingen en debetrekening)

België
* Nationaal nummer

Brazilië
* Nummer van juridische entiteit (CNPJ)
* CPF-nummer
* National ID-kaart (RG)

Canada
* Pass Port-ID
* Licentie-ID van stuur programma
* Ziektekosten nummer
* ID-nummer persoonlijke status (PHIN)
* Sociaal-fiscaal nummer
* bankrekening nummers (bijvoorbeeld controle, besparingen en debetrekening)

Chili
* Nummer van de identiteits kaart 

China
* Nummer van de identiteits kaart
* Nummer van residente ID-kaart (PRC)

Kroatië
* ID-kaart nummer
* Persoonlijk ID-nummer (OIB)

Tsjechische Republiek
* Nummer van nationale ID-kaart

Denemarken
* Persoonlijk ID-nummer

Europese Unie (EU)
* Sofi-nummer
* Pass Port-ID
* Licentie-ID van stuur programma
* Sofinummer (sofi-nummer) of gelijkwaardige ID
* BTW-identificatie nummer van de EU
* Nummer ICL-betaalpas

Finland
* Sofi-nummer
* Pass Port-ID

Frankrijk
* National ID Card (CNI)
* Sociaal-fiscaal nummer (INSEE)
* Pass Port-ID
* Licentie-ID van stuur programma

Duitsland
* ID-kaart nummer
* Pass Port-ID
* Licentie-ID van stuur programma

Griekenland 
* Nummer van nationale ID-kaart

Hongkong
* Nummer van ID-kaart (HKID)

India
* Permanent account nummer (PAN)
* Uniek ID-nummer (Aadhaar)

Indonesië
* ID-kaart nummer (KTP)

Ierland
* Nummer van persoonlijke open bare service (PPS)

Israël
* Nationale ID
* bankrekening nummers (bijvoorbeeld controle, besparingen en debetrekening)

Italië
* Licentie-ID van stuur programma

Japan
* Resident registratie nummer
* Nummer van verblijfs kaart
* Licentie-ID van stuur programma
* Sofi-nummer (SIN)
* Pass Port-ID
* bankrekening nummers (bijvoorbeeld controle, besparingen en debetrekening)

Maleisië
* ID-kaart nummer

Nederland
* BSN-nummer (burger service)

Nieuw-Zeeland
* Ministerie van gezondheids nummer

Noorwegen
* ID-kaart nummer

Filipijnen
* Uniform ID-nummer voor meerdere doel einden

Polen
* ID-kaart nummer
* National ID (PESEL)
* Pass Port-ID

Portugal 
* Kaart nummer burger

Saoedi-Arabië
* Nationale ID

Singapore
* Nummer van de nationale registratie-ID-kaart (NRIC)

Zuid-Afrika
* ID-nummer
* Resident registratie nummer

Zuid-Korea
* Resident registratie nummer

Spanje 
* Sociaal-fiscaal nummer (SSN)

Zweden
* Nationale ID
* Pass Port-ID

Taiwan 
* Nationale ID
* Nummer van Resident certificaat (ARC/TARC)
* Pass Port-ID

Thailand
* Identificatie code van populatie

Verenigd Koninkrijk
* Pass Port-ID
* Licentie-ID van stuur programma
* Nationaal verzekerings nummer (NINO)
* National Health Service (NHS)-nummer

Verenigde Staten
* Sociaal-fiscaal nummer (SSN)
* Licentie-ID van stuur programma
* Pass Port-ID
* Aantal kiezers
* ID-nummer van afzonderlijke belasting (ITIN)
* DEA-nummer (medicijn Enforcement Agency)
* bankrekening nummers (bijvoorbeeld controle, besparingen en debetrekening)
