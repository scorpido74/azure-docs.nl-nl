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
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086901"
---
## <a name="personal-information-entity-types"></a>Typen entiteits voor persoonlijke gegevens:

### <a name="person"></a>Person
Persoonsnamen herkennen in tekst.

Talen:
* Openbare preview:`English`

| Subtypenaam | Beschrijving                                               | Beschikbaar vanaf modelversie |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N.v.t.          | Erkende namen, bijvoorbeeld `Bill Gates`,`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organisatie  

Herken organisaties, bedrijven, agentschappen, bedrijven, clubs en andere groepen mensen.

Talen: 

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                                                                       | Beschikbaar vanaf modelversie|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N.v.t.          | organisaties, bijvoorbeeld `Microsoft`, `NASA``National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Telefoonnummer

Telefoonnummers (alleen telefoonnummers in de VS). 

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                    | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------------|----------------------------------------|
| N.v.t.          | Amerikaanse telefoonnummers, bijvoorbeeld`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

E-mailadres. 

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                      | Beschikbaar vanaf modelversie |
|--------------|--------------------------------------------------|----------------------------------------|
| N.v.t.          | E-mailadres, bijvoorbeeld`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

Internet-URL's.

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                                          | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------------------|----------------------------------------|
| N.v.t.          | URL's naar websites, bijvoorbeeld`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>IP-adres

Adres van internetprotocol

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                              | Beschikbaar vanaf modelversie |
|--------------|------------------------------------------|----------------------------------------|
| N.v.t.          | Netwerkadres bijvoorbeeld`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Aantal 

Numerieke hoeveelheden

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                   | Beschikbaar vanaf modelversie |
|--------------|-------------------------------|----------------------------------------|
| Leeftijd          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Datum- en tijdentiteiten

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                   | Beschikbaar vanaf modelversie |
|--------------|-------------------------------|----------------------------------------|
| Date         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>EU-GPS-coördinaten

 GPS-coördinaten voor locaties binnen de Europese Unie. 

Talen:

* Openbare preview:`English`

| Subtypenaam | Beschrijving                               | Beschikbaar vanaf modelversie |
|--------------|-------------------------------------------|----------------------------------------|
| N.v.t.          | GPS-coördinaten binnen de Europese Unie | `2019-10-01`                           |

### <a name="azure-information"></a>Azure-gegevens

Identificeerbare Azure-informatie, waaronder verificatiegegevens en verbindingstekenreeksen. 

* Beschikbaar vanaf de `2019-10-01`modelversie .

Talen:

* Openbare preview:`English`

| Subtypenaam                          | Beschrijving                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB Auth-sleutel             | Autorisatiesleutel voor een Azure DocumentDB-server.                           |
| Azure IAAS-databaseverbindingstekenreeks | Verbindingstekenreeks voor een IaaS-database (Azure Infrastructure as a Service). |
| Azure SQL-verbindingstekenreeks           | Verbindingstekenreeks voor een Azure SQL-database.                                |
| Azure IoT-verbindingstekenreeks           | Verbindingstekenreeks voor Azure Internet of Things (IoT).                        |
| Wachtwoord voor azure-publicatie-instelling        | Wachtwoord voor Azure-publicatie-instellingen.                                        |
| Verbindingstekenreeks azure Redis-cache   | Verbindingstekenreeks voor een Azure-cache voor Redis.                             |
| Azure SAS                             | Verbindingstekenreeks voor Azure Software as a Service(SAS).                     |
| Tekenreeks Azure Service Bus-verbinding   | Verbindingstekenreeks voor een Azure-servicebus.                                |
| Azure-opslagaccountsleutel             | Accountsleutel voor een Azure-opslagaccount.                                   |
| Azure Storage Account Key (Algemeen)   | Algemene accountsleutel voor een Azure-opslagaccount.                           |
| SQL Server-verbindingstekenreeks          | Verbindingstekenreeks voor een SQL-server.                                         |

### <a name="identification"></a>Identificatie

* Beschikbaar vanaf de `2019-10-01`modelversie .

Talen:

* Openbare preview:`English`

#### <a name="financial-account-identification"></a>Identificatie van financiële rekening

| Subtypenaam               | Beschrijving                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-routeringsnummers        | American Banker Association (ABA) transit routing nummers.                  |
| SWIFT-code                 | SWIFT-codes voor betalingsinstructies.                           |
| Creditcard                | Creditcardnummers.                                                       |
| IBAN-code                  | IBAN-codes voor informatie over betalingsinstructies.                            |

#### <a name="government-and-country-specific-identification"></a>Overheid en land-specifiek Identificatie

De onderstaande entiteiten zijn gegroepeerd en per land vermeld:

Argentinië
* Nationale Identiteit (DNI) Aantal

Australië
* Fiscaal dossiernummer 
* Rijbewijs-ID
* Paspoort-ID
* Medisch rekeningnummer
* bankrekeningnummers (bijvoorbeeld controle-, spaar- en betaalrekeningen)

België
* Nationaal nummer

Brazilië
* Nummer van de rechtspersoon (CNPJ)
* CPF-nummer
* Nationale identiteitskaart (RG)

Canada
* Paspoort-ID
* Rijbewijs-ID
* Ziekteverzekering Nummer
* Persoonlijke gezondheids-ID-nummer (PHIN)
* Sofinummer
* bankrekeningnummers (bijvoorbeeld controle-, spaar- en betaalrekeningen)

Chili
* Identiteitskaartnummer 

China
* Identiteitskaartnummer
* Resident ID-kaart (PRC) nummer

Kroatië
* ID-kaartnummer
* Persoonlijk ID-nummer (OIB)

Tsjechië
* Nummer van de nationale identiteitskaart

Denemarken
* Persoonlijk ID-nummer

Europese Unie (EU)
* Nationaal ID-nummer
* Paspoort-ID
* Rijbewijs-ID
* SSN -nummer (SSN) of gelijkwaardig identiteitsbewijs
* EU-belastingidentificatienummer (TIN)
* EU-betaalkaartnummer

Finland
* Nationaal ID-nummer
* Paspoort-ID

Frankrijk
* Nationale identiteitskaart (CNI)
* Sofi-nummer (INSEE)
* Paspoort-ID
* Rijbewijs-ID

Duitsland
* ID-kaartnummer
* Paspoort-ID
* Rijbewijs-ID

Griekenland 
* Nummer van de nationale identiteitskaart

Hongkong
* HKID-nummer (ID-kaart)

India
* Permanent rekeningnummer (PAN)
* Uniek ID (Aadhaar) Nummer

Indonesië
* ID-kaartnummer (KTP)

Ierland
* Nummer van de persoonlijke openbare dienst (PPS)

Israël
* Nationale identiteitskaart
* bankrekeningnummers (bijvoorbeeld controle-, spaar- en betaalrekeningen)

Italië
* Rijbewijs-ID

Japan
* Kenteken
* Verblijfskaartnummer
* Rijbewijs-ID
* Burgerverzekeringnummer (SIN)
* Paspoort-ID
* bankrekeningnummers (bijvoorbeeld controle-, spaar- en betaalrekeningen)

Maleisië
* ID-kaartnummer

Nederland
* BSN-nummer (Citizen's Service)

Nieuw-Zeeland
* Ministerie van Volksgezondheid Nummer

Noorwegen
* ID-kaartnummer

Filipijnen
* Uniform multifunctioneel id-nummer

Polen
* ID-kaartnummer
* Nationale IDENTITEITSKAART (PESEL)
* Paspoort-ID

Portugal 
* Burgerkaartnummer

Saoedi-Arabië
* Nationale identiteitskaart

Singapore
* NRIC-nummer (National Registration ID)

Zuid-Afrika
* ID-nummer
* Kenteken

Zuid-Korea
* Kenteken

Spanje 
* SSN (SSN)

Zweden
* Nationale identiteitskaart
* Paspoort-ID

Taiwan 
* Nationale identiteitskaart
* Resident Certificate (ARC/TARC) nummer
* Paspoort-ID

Thailand
* Identificatiecode voor de bevolking

Verenigd Koninkrijk
* Paspoort-ID
* Rijbewijs-ID
* Nationale Verzekering nummer (NINO)
* National Health Service (NHS) nummer

Verenigde Staten
* SSN (SSN)
* Rijbewijs-ID
* Paspoort-ID
* Kieslijstnummer
* Individueel btw-nummer (ITIN)
* Drug Enforcement Agency (DEA) nummer
* bankrekeningnummers (bijvoorbeeld controle-, spaar- en betaalrekeningen)
