---
title: Azure CDN van voldoen aan de voor waarden van de micro soft Standard Rules engine | Microsoft Docs
description: Referentie documentatie voor Azure Content Delivery Network van de overeenkomst voor waarden van de micro soft Standard Rules engine.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615960"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>Azure CDN van voldoen aan de voor waarden van de micro soft Standard Rules engine

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare Matchings voorwaarden voor de Azure Content Delivery Network (CDN) van de micro soft [Standard Rules engine](cdn-standard-rules-engine.md).

Het eerste deel van een regel is een set match-voor waarden. Elke regel kan voldoen aan Maxi maal vier match voorwaarden. Een match-voor waarde identificeert specifieke typen aanvragen waarvoor de acties worden uitgevoerd die in de regel zijn gedefinieerd. Als u meerdere match-voor waarden gebruikt, worden deze gegroepeerd met en logica.

U kunt bijvoorbeeld een match-voor waarde gebruiken voor het volgende:

- Aanvragen filteren die zijn gegenereerd op basis van een bepaald IP-adres of specifieke land/regio.
- Aanvragen filteren op header-informatie.
- Aanvragen van mobiele of desktop apparaten filteren.

## <a name="match-conditions"></a>Overeenkomst voorwaarden

De volgende overeenkomende voor waarden zijn beschikbaar voor gebruik. 

### <a name="device-type"></a>Apparaattype 

De voor waarde matching van het apparaattype identificeert aanvragen die zijn gemaakt van een mobiel of desktop apparaat op basis van de eigenschappen.  

**Vereiste velden**

Operator | Ondersteunde waarde
---------|----------------
Is gelijk aan, niet gelijk aan | Mobiel, bureau blad


### <a name="http-version"></a>HTTP-versie

De voor waarde matching HTTP-versie identificeert aanvragen op basis van de HTTP-versie waarvan de aanvraag arriveert.

**Vereiste velden**

Operator | Ondersteunde waarde
---------|----------------
Is gelijk aan, niet gelijk aan | 2,0, 1,1, 1,0, 0,9, alle


### <a name="request-cookies"></a>Cookies aanvragen

De voor waarde voor het vergelijken van aanvraag cookies identificeert aanvragen op basis van cookie-informatie in de binnenkomende aanvraag.

**Vereiste velden**

Cookie naam | Operator | Cookie waarde | Case-trans formatie
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

Belang rijke informatie
- Joker tekens, inclusief sterretjes (*), worden niet ondersteund bij het opgeven van een cookie naam, maar alleen exacte cookie naam overeenkomsten komen in aanmerking voor vergelijking.
- Er kan slechts één cookie naam worden opgegeven per exemplaar van deze match-voor waarde.
- Cookie naam vergelijkingen zijn niet hoofdletter gevoelig.
- Geef meerdere cookie waarden op door één enkele spatie te scheiden. 
- Cookie waarden kunnen gebruikmaken van Joker teken waarden.
- Als er geen joker teken waarde is opgegeven, voldoet alleen een exacte overeenkomst aan deze match-voor waarde. Als u bijvoorbeeld ' waarde ' opgeeft, komt dit overeen met ' waarde ', maar niet ' waarde1 '. 

### <a name="post-argument"></a>Post-argument

**Vereiste velden**

Argument naam | Operator | Argument waarde | Case-trans formatie
--------------|----------|----------------|---------------
Tekenreeks | [Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="query-string"></a>Queryreeksen

De voor waarden van de query reeks overeenkomst identificeren aanvragen met een opgegeven query teken reeks parameter. Deze para meter wordt ingesteld op een waarde die overeenkomt met een opgegeven patroon. Query reeks parameters (bijvoorbeeld para meter = waarde) in de aanvraag-URL bepalen of aan deze voor waarde wordt voldaan. Deze match-voor waarde identificeert een query reeks parameter met de naam en accepteert een of meer waarden voor de waarde van de para meter.

**Vereiste velden**

Operator | Queryreeksen | Case-trans formatie
---------|--------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="remote-address"></a>Extern adres

De voor waarde voor het afstemmen van het externe adres identificeert aanvragen op basis van de locatie of het IP-adres van de aanvrager.

**Vereiste velden**

Operator | Ondersteunde waarden
---------|-----------------
Alle | N.v.t.
Geo-overeenkomst | Land codes
Overeenkomende IP | IP-adressen (ruimte gescheiden)
Geen | N.v.t.
Geen geo-overeenkomst | Land codes
Geen overeenkomst met het IP-adres | IP-adressen (ruimte gescheiden)

Belang rijke informatie:

- Gebruik CIDR-notatie.
- Geef meerdere IP-adressen en/of IP-adres blokken op door één spatie te scheiden. Bijvoorbeeld:
  - **IPv4-voor beeld**: 1.2.3.4 10.20.30.40 komt overeen met alle aanvragen die binnenkomen bij een van de adressen 1.2.3.4 of 10.20.30.40.
  - **IPv6-voor beeld**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 komt overeen met aanvragen die afkomstig zijn van een van de adressen 1:2:3:4:5:6:7:8 of 10:20:30:40:50:60:70:80.
- De syntaxis voor een IP-adres blok is het basis-IP-adres, gevolgd door een slash en de grootte van het voor voegsel. Bijvoorbeeld:
  - **IPv4-voor beeld**: 5.5.5.64/26 komt overeen met de aanvragen die afkomstig zijn van 5.5.5.64 via 5.5.5.127.
  - **IPv6-voor beeld**: 1:2:3:/48 komt overeen met aanvragen die afkomstig zijn van de adressen 1:2:3:0:0:0:0:0 tot en met 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.

### <a name="request-body"></a>Aanvraagtekst

**Vereiste velden**

Operator | Aanvraagtekst | Case-trans formatie
---------|--------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="request-header"></a>Aanvraagheader

**Vereiste velden**
Header naam | Operator | Header waarde | Case-trans formatie
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="request-method"></a>Aanvraag methode

**Vereiste velden**

Operator | Ondersteunde waarde
---------|----------------
Is gelijk aan, niet gelijk aan | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

Belang rijke informatie:

- Alleen de GET-aanvraag methode kan inhoud in de cache in het CDN genereren. Alle andere aanvraag methoden worden via het netwerk geproxyeerd. 

### <a name="request-protocol"></a>Aanvraag Protocol

**Vereiste velden**

Operator | Ondersteunde waarde
---------|----------------
Is gelijk aan, niet gelijk aan | HTTP, HTTPS

### <a name="request-url"></a>Aanvraag-URL

**Vereiste velden**

Operator | Aanvraag-URL | Case-trans formatie
---------|-------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

Belang rijke informatie:

- Wanneer u een aanvraag-URL opgeeft, moet u protocol gegevens toevoegen. Bijvoorbeeld 'https://www. [webdomeine]. com "

### <a name="url-file-extension"></a>URL-bestands extensie

**Vereiste velden**

Operator | Toestelnummer | Case-trans formatie
---------|-----------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

Belang rijke informatie:

- Voor uitbrei ding geldt geen voorloop periode. Gebruik bijvoorbeeld HTML in plaats van HTML.

### <a name="url-file-name"></a>URL-bestands naam

**Vereiste velden**

Operator | Bestandsnaam | Case-trans formatie
---------|-----------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

Belang rijke informatie:

- Als u meerdere bestands namen wilt opgeven, moet u elke bestands naam scheiden met één spatie. 

### <a name="url-path"></a>URL-pad

**Vereiste velden**

Operator | Waarde | Case-trans formatie
---------|-------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

Belang rijke informatie:

- De waarde van een bestands naam kan gebruikmaken van joker tekens. Elk bestands naam patroon kan bijvoorbeeld bestaan uit een of meer sterretjes (*), waarbij elk sterretje overeenkomt met een reeks van een of meer tekens.

## <a name="reference-for-rules-engine-match-conditions"></a>Naslag informatie voor de matching voorwaarden van de regels-engine

### <a name="standard-operator-list"></a>Lijst met standaard operators

Voor regels die de lijst met standaard operators bevatten, zijn de volgende Opera tors geldig:

- Alle
- Is gelijk aan 
- Contains 
- Begint met 
- Eindigt op 
- Kleiner dan
- Kleiner dan of gelijk aan
- Groter dan
- Groter dan of gelijk aan
- Geen
- Bevat niet
- Begint niet met 
- Eindigt niet met 
- Niet kleiner dan
- Niet kleiner dan of gelijk aan
- Niet groter dan
- Niet groter dan of gelijk aan

Voor numerieke Opera Tors, zoals ' kleiner dan ' of ' groter dan of gelijk aan ', wordt de gebruikte vergelijking gebaseerd op lengte. In dit geval moet de waarde in de overeenkomst voor waarde een geheel getal zijn, gelijk aan de lengte die u wilt vergelijken. 

---

[Terug naar boven](#match-conditions)

</br>

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Content Delivery Network](cdn-overview.md)
- [Verwijzing regelengine](cdn-standard-rules-engine-reference.md)
- [Engine acties voor regels](cdn-standard-rules-engine-actions.md)
- [HTTPS afdwingen met de standaard regels-engine](cdn-standard-rules-engine.md)
