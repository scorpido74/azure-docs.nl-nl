---
title: Voldoen aan de voor waarden in de standaard regels-engine voor Azure CDN | Microsoft Docs
description: Referentie documentatie voor de matching voorwaarden in de Standard Rules engine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900178"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Voldoen aan de voor waarden in de standaard regels-engine voor Azure CDN

In de [standaard regels-engine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer match-voor waarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de match-voor waarden die u kunt gebruiken in de Standard Rules engine voor Azure CDN.

Het eerste deel van een regel is een match-voor waarde of een set matching voorwaarden. In de standaard regels-engine voor Azure CDN kan elke regel Maxi maal vier match voorwaarden hebben. Een match-voor waarde identificeert specifieke typen aanvragen waarvoor gedefinieerde acties worden uitgevoerd. Als u meerdere match voorwaarden gebruikt, worden de match-voor waarden samen gegroepeerd met behulp van en logica.

U kunt bijvoorbeeld een match-voor waarde gebruiken voor het volgende:

- Aanvragen filteren op basis van een specifiek IP-adres, land of regio.
- Aanvragen filteren op header-informatie.
- Aanvragen filteren op mobiele apparaten of desktop apparaten.

## <a name="match-conditions"></a>Overeenkomst voorwaarden

De volgende match-voor waarden zijn beschikbaar voor gebruik in de standaard regels-engine voor Azure CDN. 

### <a name="device-type"></a>Apparaattype 

Hiermee worden aanvragen geïdentificeerd van een mobiel apparaat of desktop apparaat.  

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, niet gelijk aan | Mobiel, bureau blad

### <a name="http-version"></a>HTTP-versie

Identificeert aanvragen op basis van de HTTP-versie van de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, niet gelijk aan | 2,0, 1,1, 1,0, 0,9, alle

### <a name="request-cookies"></a>Cookies aanvragen

Identificeert aanvragen op basis van cookie-informatie in de binnenkomende aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Naam van cookie | Operator | Cookie waarde | Case-trans formatie
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- U kunt geen joker tekens (inclusief sterretjes (\*)) gebruiken wanneer u een cookie naam opgeeft. u moet een exacte cookie naam gebruiken.
- U kunt slechts één cookie naam per exemplaar van deze match-voor waarde opgeven.
- Cookie naam vergelijkingen zijn niet hoofdletter gevoelig.
- Als u meerdere cookie waarden wilt opgeven, gebruikt u één spatie tussen elke cookie waarde. 
- Cookie waarden kunnen gebruikmaken van Joker teken waarden.
- Als er geen joker teken waarde is opgegeven, voldoet alleen een exacte overeenkomst aan deze match-voor waarde. ' Waarde ' komt bijvoorbeeld overeen met ' waarde ', maar niet ' waarde1 '. 

### <a name="post-argument"></a>Post-argument

Identificeert aanvragen op basis van argumenten die zijn gedefinieerd voor de POST-aanvraag methode die wordt gebruikt in de aanvraag. 

#### <a name="required-fields"></a>Vereiste velden

Argumentnaam | Operator | Argumentwaarde | Case-trans formatie
--------------|----------|----------------|---------------
Tekenreeks | [Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="query-string"></a>Querytekenreeks

Hiermee worden aanvragen geïdentificeerd die een specifieke query teken reeks parameter bevatten. Deze para meter wordt ingesteld op een waarde die overeenkomt met een specifiek patroon. Query reeks parameters (bijvoorbeeld **para meter = waarde**) in de aanvraag-URL bepalen of aan deze voor waarde wordt voldaan. Deze match-voor waarde identificeert een query reeks parameter met de naam en accepteert een of meer waarden voor de waarde van de para meter.

#### <a name="required-fields"></a>Vereiste velden

Operator | Querytekenreeks | Case-trans formatie
---------|--------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="remote-address"></a>Extern adres

Hiermee worden aanvragen geïdentificeerd op basis van de locatie of het IP-adres van de aanvrager.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|-----------------
Alle | N/A
Geo-overeenkomst | Landnummer
Overeenkomende IP | IP-adres (door spaties gescheiden)
Geen | N/A
Geen geo-overeenkomst | Landnummer
Geen overeenkomst met het IP-adres | IP-adres (door spaties gescheiden)

#### <a name="key-information"></a>Belangrijke informatie

- Gebruik CIDR-notatie.
- Als u meerdere IP-adressen en IP-adres blokken wilt opgeven, gebruikt u één spatie tussen de waarden:
  - **IPv4-voor beeld**: *1.2.3.4 10.20.30.40* komt overeen met alle aanvragen die binnenkomen bij een van de adressen 1.2.3.4 of 10.20.30.40.
  - **IPv6-voor beeld**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* komt overeen met aanvragen die afkomstig zijn van een van de adressen 1:2:3:4:5:6:7:8 of 10:20:30:40:50:60:70:80.
- De syntaxis voor een IP-adres blok is het basis-IP-adres, gevolgd door een slash en de grootte van het voor voegsel. Bijvoorbeeld:
  - **IPv4-voor beeld**: *5.5.5.64/26* komt overeen met de aanvragen die afkomstig zijn van 5.5.5.64 via 5.5.5.127.
  - **IPv6-voor beeld**: *1:2:3:/48* komt overeen met aanvragen die afkomstig zijn van de adressen 1:2:3:0:0:0:0:0 tot en met 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.

### <a name="request-body"></a>Aanvraagbody

Identificeert aanvragen op basis van specifieke tekst die wordt weer gegeven in de hoofd tekst van de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraagbody | Case-trans formatie
---------|--------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="request-header"></a>Aanvraagheader

Identificeert aanvragen die gebruikmaken van een specifieke header in de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Headernaam | Operator | Headerwaarde | Case-trans formatie
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

### <a name="request-method"></a>Aanvraag methode

Identificeert aanvragen die gebruikmaken van de opgegeven aanvraag methode.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, niet gelijk aan | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Belangrijke informatie

- Alleen de GET-aanvraag methode kan inhoud in de cache in Azure CDN genereren. Alle andere aanvraag methoden worden via het netwerk geproxyeerd. 

### <a name="request-protocol"></a>Aanvraag Protocol

Identificeert aanvragen waarvoor het opgegeven protocol wordt gebruikt.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, niet gelijk aan | HTTP, HTTPS

### <a name="request-url"></a>Aanvraag-URL

Hiermee worden aanvragen geïdentificeerd die overeenkomen met de opgegeven URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraag-URL | Case-trans formatie
---------|-------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- Wanneer u deze regel voorwaarde gebruikt, moet u protocol gegevens toevoegen. Bijvoorbeeld: *https://www.\<yourdomain\>.com* .

### <a name="url-file-extension"></a>URL-bestands extensie

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestands extensie bevatten in de bestands naam in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Extensie | Case-trans formatie
---------|-----------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- Voor uitbrei ding geldt geen voorloop periode. Gebruik bijvoorbeeld *HTML* in plaats van *HTML*.

### <a name="url-file-name"></a>URL-bestands naam

Identificeert aanvragen met de opgegeven bestands naam in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Bestandsnaam | Case-trans formatie
---------|-----------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- Als u meerdere bestands namen wilt opgeven, moet u elke bestands naam scheiden met één spatie. 

### <a name="url-path"></a>URL-pad

Hiermee worden aanvragen geïdentificeerd die het opgegeven pad bevatten in de aangevraagde URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Waarde | Case-trans formatie
---------|-------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Geen trans formatie, naar hoofd letters, naar kleine letters

#### <a name="key-information"></a>Belangrijke informatie

- De waarde van een bestands naam kan gebruikmaken van joker tekens. Elk bestands naam patroon kan bijvoorbeeld bestaan uit een of meer sterretjes (*), waarbij elk sterretje overeenkomt met een reeks van een of meer tekens.

## <a name="reference-for-rules-engine-match-conditions"></a>Naslag informatie voor de matching voorwaarden van de regels-engine

### <a name="standard-operator-list"></a>Lijst met standaard operators

De volgende Opera tors zijn geldig voor regels die waarden accepteren van de standaard operator lijst:

- Alle
- Is gelijk aan 
- Contains 
- Begint met 
- eindigt met 
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

Voor numerieke Opera tors zoals *kleiner dan* en *groter dan of gelijk aan*, wordt de gebruikte vergelijking gebaseerd op lengte. In dit geval moet de waarde in de overeenkomst voor waarde een geheel getal zijn dat gelijk is aan de lengte die u wilt vergelijken. 

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslag informatie voor standaard regels-engine](cdn-standard-rules-engine-reference.md)
- [Acties in de standaard regels-engine](cdn-standard-rules-engine-actions.md)
- [HTTPS afdwingen met behulp van de standaard regels-engine](cdn-standard-rules-engine.md)
