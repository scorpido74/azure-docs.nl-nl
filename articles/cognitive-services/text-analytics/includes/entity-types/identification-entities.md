---
title: Identificatie-entiteiten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 8e0798f75aaa79031ca7cc03814282daa049fbfe
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662987"
---
Deze entiteits categorie bevat financiële gegevens en officiële vormen van identificatie. Beschikbaar vanaf model versie `2019-10-01` . Subtypen worden hieronder weer gegeven. 

### <a name="financial-account-identification"></a>Id van financiële rekening

| Naam subtype               | Beschrijving                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-routenummer        | Transit routerings nummers van de Amerikaanse Bank (ABA).                  |
| SWIFT-code                 | SWIFT-codes voor informatie over de betalings instructie.                           |
| Creditcard                | Creditcard nummers.                                                       |
| IBAN (International Banking Account Number)                  | IBAN-codes voor informatie over de betalings instructie.                            |


### <a name="government-and-countryregion-specific-identification"></a>Sofi en land/regio-specifieke identificatie

> [!NOTE]
> De volgende financiële en land afhankelijke entiteiten worden niet geretourneerd met de `domain=phi` para meter:
> * Paspoort nummers
> * BTW-Id's

De volgende entiteiten worden gegroepeerd en weer gegeven op land:

Argentinië
* Sofi-nummer (Argentinië National Identity)

Oostenrijk
* Identiteits kaart Oosten rijk
* Identificatie nummer voor Oosten Rijks tarief
* BTW-nummer voor de toegevoegde waarde van de Oosten rijk

Australië
* Nummer van Australië-Bank account
* Australisch bedrijfs nummer
* Australisch bedrijfs nummer
* Licentie nummer van het Australia-stuur programma
* Medisch account nummer in Australië
* Australië-paspoort nummer
* Australia-BTW-bestands nummer

België
* België nationaal nummer
* BTW-nummer voor de Belgische toegevoegde waarde

Brazilië 
* Nummer van juridische entiteit in Brazilië (CNPJ)
* Brazilië CPF-nummer
* National ID-kaart (RG) Brazilië

Bulgarije
* Bulgarije, uniforme burger Number

Canada
* Rekening nummer Canada
* Rijbewijs nummer van het Canada-stuur programma
* Canada-Health Service nummer
* Canada paspoort nummer
* Privé-identificatie nummer voor Canada (PHIN)
* Sofi-nummer Canada

Chili
* Nummer van de identiteits kaart 

China
* China-nummer van de residente identiteits kaart (China)

Kroatië
* ID-kaart nummer van Kroatië
* ID-kaart nummer van Kroatië
* OIB-nummer (Kroatië Personal Identification)

Cyprus
* Cypriotische ID-kaart nummer
* Cypriotische BTW-identificatie nummer

Tsjechische Republiek
* Persoonlijk identiteits nummer Tsjechisch

Denemarken
* Privé-identificatie nummer Denemarken

Estland
* Persoonlijke identificatie code van Estland

Europese Unie (EU)
* Nummer ICL-betaalpas
* Licentie nummer van het EU-stuur programma
* Sofi-nummer van de EU
* EU-paspoort nummer
* Sofi-nummer van de EU of gelijkwaardige ID
* BTW-identificatie nummer van de EU

Finland
* Euro pees Health Insurance-nummer
* National ID Finland
* Finland paspoort nummer

Frankrijk
* Rijbewijs nummer van Frank rijk stuur programma
* Frank rijk-medische-verzekerings nummer
* Landse nationale ID-kaart (CNI)
* Frank rijk paspoort nummer
* Sofi-nummer Frank rijk (INSEE)
* Frank rijk-belasting identificatienummer (numéro SPI)
* Omzetbelasting nummer van toegevoegde waarde voor Frank rijk

Duitsland
* Rijbewijs nummer van het Duitse stuur programma
* Nummer van de Duitsland-identiteits kaart
* Duits paspoort nummer
* Nummer van de Duitse belasting-ID
* Omzetbelasting nummer van toegevoegde Duitse waarde

Griekenland 
* Sofi-nummer van het Grieken land
* Grieken land belasting identificatienummer

Hongkong
* HKID-nummer (Hong Kong Identity Card)

Hongarije
* Nationaal identificatie nummer Hongarije
* BTW-identificatie nummer Hongarije
* Omzetbelasting nummer toegevoegde waarde

India
* Permanent account nummer India (PAN)
* Aadhaar-nummer (India-unieke identificatie)

Indonesië
* KTP-nummer (Indonesië-identiteits kaart)

Ierland
* Het PPS-nummer (Personal public service) in Ierland

Israël
* Nationale ID Israël
* Israël-bankrekening nummer

Italië
* Licentie-ID van het Italië-stuur programma
* Italië-fiscale code
* Omzetbelasting nummer voor toegevoegde waarde van Italië

Japan
* Japans bankrekening nummer
* Rijbewijs nummer van het Japanse stuur programma
* Japans mijn nummer persoonlijk
* Japanse mijn faxnummer
* Japan Resident registratie nummer
* Japans nummer van verblijfs kaart
* Japans sociaal verzekerings nummer (SIN)
* Japans paspoort nummer

Letland
* Persoonlijke code voor Letland

Litouwen
* Persoonlijke code van Litouwen

Luxemburg
* Luxemburgs nationaal identificatie nummer (natuurlijke personen)
* Luxemburgs nationaal identificatie nummer (niet-natuurlijke personen)

Maleisië
* Nummer van de Maleisië-identiteits kaart

Malta
* Malta-ID-kaart nummer
* Identificatie nummer Malta-belasting

Nederland
* BSN-nummer (Nederland burger)
* Id-nummer Nederlandse belasting
* Omzetbelasting nummer voor toegevoegde waarde

Nieuw-Zeeland
* Bankrekening nummer Nieuw-Zeeland
* Rijbewijs nummer van Nieuw-Zeeland
* Nieuw-Zeeland land/-omzet nummer
* Nieuw-Zeelandse ministerie van status nummer
* Nieuw-Zeelandse sofinummer

Noorwegen
* Noor wegen-identiteits nummer

Filipijnen
* Unified Multi-Purpose ID-nummer Filipijnen

Polen
* Polen-identiteits kaart
* Polen National ID (PESEL)
* Polen Pass Port-nummer
* REGON nummer van Polen
* Polen belasting identificatienummer

Portugal 
* Portugal burger kaartnummer
* Portugal BTW-identificatie nummer

Roemenië
* Privé numerieke code van Roemenië (CNP)

Rusland
* Russisch Pass Port-nummer (binnenland)
* Russisch Pass Port-nummer (internationaal)

Saoedi-Arabië
* Land-ID Saudi-Arabië

Singapore
* NRIC-nummer (National Registration ID) Singapore

Slowakije 
* Persoonlijk nummer van Slowakije

Slovenië
* Het BTW-identificatie nummer voor Slovenië
* Het unieke hoofd Burgery-nummer van Slovenië

Zuid-Afrika
* Zuid-Afrika-identificatie nummer

Zuid-Korea
* Geresident registratie nummer Zuid-Korea

Spanje 
* Spanje DNI
* Sociaal-fiscaal nummer (SOFInummer) voor Spanje
* Spanje belasting identificatienummer

Zweden
* Zweden nationale ID
* Zweden Pass Port-nummer
* Zweden-identificatie nummer

Zwitserland
* Sociaal-fiscaal nummer voor Zwitser land AHV

Taiwan 
* Taiwanese nationale ID
* Certificaat in Taiwan (ARC/TARC)
* Taiwan Pass Port-nummer

Thailand
* Identificatie code Thaise populatie

Turkije
* Onderliggend nationaal identificatie nummer

Oekraïne
* Oekraïne paspoort nummer (binnenland)
* Oekraïne Pass Port-nummer (internationaal)

Verenigd Koninkrijk
* Engelse Rijbewijs nummer
* Engelse Aantal kiezers
* Engelse National Health Service (NHS)-nummer
* Engelse Nationaal verzekerings nummer (NINO)
* Engelse Paspoort nummer
* Engelse Uniek belastingplichtige-referentie nummer

Verenigde Staten
* Amerikaans sociaal-fiscaal nummer (SSN)
* Rijbewijs nummer van het Amerikaanse stuur programma
* Amerikaans paspoort nummer
* Amerikaans identificatie nummer voor de Amerikaanse belastingplichtige (ITIN)
* DEA-nummer (U.S. medicijn Enforcement Agency)
* Amerikaans bankrekening nummer
