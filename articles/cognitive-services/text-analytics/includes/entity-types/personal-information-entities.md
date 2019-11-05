---
title: Benoemde entiteiten voor persoonlijke gegevens
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 6e46d58b3f1ef33902ab44043e954d9718d865af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508043"
---
## <a name="personal-information-entity-types"></a>Entiteit typen persoonlijke informatie:

### <a name="person"></a>Person
Herkende namen en andere personen in de tekst.
Talen:
* Open bare Preview: `English`

| Naam subtype | Beschrijving             |
|--------------|-------------------------|
| N.v.t.          | Herkende namen, bijvoorbeeld `Bill Gates`, `Marie Curie` |

### <a name="organization"></a>Organisatie  

Erkende organisaties, ondernemingen, agentschappen en andere groepen personen. Bijvoorbeeld: bedrijven, politieke groepen, muziek banden, sport clubs, overheids instanties en open bare organisaties. Nationale en religions zijn niet opgenomen in dit entiteits type. Talen: 

* Open bare Preview: `English`

| Naam subtype | Beschrijving                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N.v.t.          | organisaties, bijvoorbeeld `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Telefoonnummer

Telefoon nummers. 

Talen:

* Open bare Preview: `English`

| Naam subtype           | Beschrijving                                           |
|------------------------|-------------------------------------------------------|
| N.v.t.                    | Telefoon nummers, bijvoorbeeld `+1 123-123-123`.          |
| EU-telefoon nummer        | Telefoon nummers die specifiek zijn voor de Europese Unie.         |
| Mobiele telefoon nummer van de EU | Mobiele telefoon nummers die specifiek zijn voor de Europese Unie. |

### <a name="eu-gps-coordinates"></a>EU GPS-coördinaten

 GPS-coördinaten voor locaties binnen de Europese Unie. 

Talen:

* Open bare Preview: `English`

| Naam subtype | Beschrijving                               |
|--------------|-------------------------------------------|
| N.v.t.          | GPS-coördinaten binnen de Europese Unie |

### <a name="azure-information"></a>Azure-gegevens

Identificeer bare Azure-informatie, inclusief verificatie gegevens en verbindings reeksen. 

Talen:

* Open bare Preview: `English`

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

### <a name="identification"></a>Aanduiding

Talen:

* Open bare Preview: `English`

#### <a name="financial-account-identification"></a>Id van financiële rekening

| Naam subtype               | Beschrijving                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-route nummers        | Transit routerings nummers van de Amerikaanse Bank (ABA).                  |
| SWIFT-code                 | SWIFT-codes voor informatie over de betalings instructie.                           |
| Creditcard                | Creditcard nummers.                                                       |
| IBAN-code                  | IBAN-codes voor informatie over de betalings instructie.                            |

#### <a name="government-and-country-specific-identification"></a>Overheid en land-specifieke identificatie

De onderstaande entiteiten worden gegroepeerd en per land weer gegeven:

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

Tsjechië
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

Hongkong SAR
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