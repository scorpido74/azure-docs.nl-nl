---
title: Azure front-deur | Microsoft Docs
description: In dit artikel vindt u een overzicht van Azure Front Door. Uitzoeken of dit de juiste keuze is voor taak verdeling van gebruikers verkeer voor uw toepassing.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 77c0d68f507e09b315c912d1d91fdf9cf63db6fa
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515529"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Voldoen aan de voor waarden van de Azure front deur-regels

In de [afd-regels-engine](front-door-rules-engine.md) bestaat een regel uit nul of meer match voorwaarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de match-voor waarden die u kunt gebruiken in de engine voor de AFD-regels. 

Het eerste deel van een regel is een match-voor waarde of een set matching voorwaarden. Een regel kan bestaan uit Maxi maal 10 match-voor waarden. Een match-voor waarde identificeert specifieke typen aanvragen waarvoor gedefinieerde acties worden uitgevoerd. Als u meerdere match voorwaarden gebruikt, worden de match-voor waarden samen gegroepeerd met behulp van en logica. Voor alle match-voor waarden die ondersteuning bieden voor meerdere waarden (hieronder vermeld als ' door spaties gescheiden '), wordt ervan uitgegaan dat de ' OR '-operator wordt gebruikt. 

U kunt bijvoorbeeld een match-voor waarde gebruiken voor het volgende:

- Aanvragen filteren op basis van een specifiek IP-adres, land of regio.
- Aanvragen filteren op header-informatie.
- Aanvragen filteren op mobiele apparaten of desktop apparaten.

De volgende match-voor waarden zijn beschikbaar voor gebruik in de Azure front deur-regel engine.  

## <a name="device-type"></a>Apparaattype 

Hiermee worden aanvragen geïdentificeerd van een mobiel apparaat of desktop apparaat.  

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, niet gelijk aan | Mobiel, bureau blad

## <a name="post-argument"></a>Post-argument

Identificeert aanvragen op basis van argumenten die zijn gedefinieerd voor de POST-aanvraag methode die wordt gebruikt in de aanvraag. 

#### <a name="required-fields"></a>Vereiste velden

Argumentnaam | Operator | Argumentwaarde | Case-trans formatie
--------------|----------|----------------|---------------
Tekenreeks | [Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Kleine letters, hoofd letters, bijsnijden, witruimte verwijderen, URL-code ring, URL decoderen

## <a name="query-string"></a>Queryreeks

Hiermee worden aanvragen geïdentificeerd die een specifieke query teken reeks parameter bevatten. Deze para meter wordt ingesteld op een waarde die overeenkomt met een specifiek patroon. Query reeks parameters (bijvoorbeeld **para meter = waarde**) in de aanvraag-URL bepalen of aan deze voor waarde wordt voldaan. Deze match-voor waarde identificeert een query reeks parameter met de naam en accepteert een of meer waarden voor de waarde van de para meter.

#### <a name="required-fields"></a>Vereiste velden

Operator | Queryreeks | Case-trans formatie
---------|--------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Kleine letters, hoofd letters, bijsnijden, witruimte verwijderen, URL-code ring, URL decoderen

## <a name="remote-address"></a>Extern adres

Hiermee worden aanvragen geïdentificeerd op basis van de locatie of het IP-adres van de aanvrager.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|-----------------
Geo-overeenkomst | Landcode
Overeenkomende IP | IP-adres (door spaties gescheiden)
Geen geo-overeenkomst | Landcode
Geen overeenkomst met het IP-adres | IP-adres (door spaties gescheiden)

#### <a name="key-information"></a>Belang rijke informatie

- Gebruik CIDR-notatie.
- Als u meerdere IP-adressen en IP-adres blokken wilt opgeven, gebruikt u één spatie tussen de waarden:
  - **IPv4-voor beeld**: *1.2.3.4 10.20.30.40* komt overeen met alle aanvragen die binnenkomen bij een van de adressen 1.2.3.4 of 10.20.30.40.
  - **IPv6-voor beeld**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* komt overeen met aanvragen die afkomstig zijn van een van de adressen 1:2:3:4:5:6:7:8 of 10:20:30:40:50:60:70:80.
- De syntaxis voor een IP-adres blok is het basis-IP-adres, gevolgd door een slash en de grootte van het voor voegsel. Bijvoorbeeld:
  - **IPv4-voor beeld**: *5.5.5.64/26* komt overeen met de aanvragen die afkomstig zijn van 5.5.5.64 via 5.5.5.127.
  - **IPv6-voor beeld**: *1:2:3:/48* komt overeen met aanvragen die afkomstig zijn van de adressen 1:2:3:0:0:0:0:0 tot en met 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.

## <a name="request-body"></a>Aanvraagbody

Identificeert aanvragen op basis van specifieke tekst die wordt weer gegeven in de hoofd tekst van de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraagbody | Case-trans formatie
---------|--------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Kleine letters, hoofd letters, bijsnijden, witruimte verwijderen, URL-code ring, URL decoderen

## <a name="request-header"></a>Aanvraagheader

Identificeert aanvragen die gebruikmaken van een specifieke header in de aanvraag.

#### <a name="required-fields"></a>Vereiste velden

Headernaam | Operator | Headerwaarde | Case-trans formatie
------------|----------|--------------|---------------
Tekenreeks | [Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Kleine letters, hoofd letters, bijsnijden, witruimte verwijderen, URL-code ring, URL decoderen

## <a name="request-method"></a>Aanvraag methode

Identificeert aanvragen die gebruikmaken van de opgegeven aanvraag methode.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, niet gelijk aan | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Belang rijke informatie

- Alleen de GET-aanvraag methode kan inhoud in de cache in azure front-deur genereren. Alle andere aanvraag methoden worden via het netwerk geproxyeerd. 

## <a name="request-protocol"></a>Aanvraag Protocol

Identificeert aanvragen waarvoor het opgegeven protocol wordt gebruikt.

#### <a name="required-fields"></a>Vereiste velden

Operator | Ondersteunde waarden
---------|----------------
Is gelijk aan, niet gelijk aan | HTTP, HTTPS

## <a name="request-url"></a>Aanvraag-URL

Hiermee worden aanvragen geïdentificeerd die overeenkomen met de opgegeven URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Aanvraag-URL | Case-trans formatie
---------|-------------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Kleine letters, hoofd letters, bijsnijden, witruimte verwijderen, URL-code ring, URL decoderen

#### <a name="key-information"></a>Belang rijke informatie

- Wanneer u deze regel voorwaarde gebruikt, moet u protocol gegevens toevoegen. Bijvoorbeeld: *https://www.\<yourdomain\>.com*.

## <a name="request-file-extension"></a>Bestands extensie voor aanvraag

Hiermee worden aanvragen geïdentificeerd die de opgegeven bestands extensie bevatten in de bestands naam in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Toestelnummer | Case-trans formatie
---------|-----------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Kleine letters, hoofd letters, bijsnijden, witruimte verwijderen, URL-code ring, URL decoderen

#### <a name="key-information"></a>Belang rijke informatie

- Voor uitbrei ding geldt geen voorloop periode. Gebruik bijvoorbeeld *HTML* in plaats van *HTML*.

## <a name="request-file-name"></a>Bestands naam van aanvraag

Identificeert aanvragen met de opgegeven bestands naam in de aanvraag-URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Bestandsnaam | Case-trans formatie
---------|-----------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Kleine letters, hoofd letters, bijsnijden, witruimte verwijderen, URL-code ring, URL decoderen

#### <a name="key-information"></a>Belang rijke informatie

- Als u meerdere bestands namen wilt opgeven, moet u de bestands naam scheiden door op ENTER te drukken. 

## <a name="request-path"></a>Aanvraagpad

Hiermee worden aanvragen geïdentificeerd die het opgegeven pad bevatten in de aangevraagde URL.

#### <a name="required-fields"></a>Vereiste velden

Operator | Waarde | Case-trans formatie
---------|-------|---------------
[Lijst met standaard operators](#standard-operator-list) | Teken reeks, int | Kleine letters, hoofd letters, bijsnijden, witruimte verwijderen, URL-code ring, URL decoderen

## <a name="standard-operator-list"></a>Lijst met standaard operators

De volgende Opera tors zijn geldig voor regels die waarden accepteren van de standaard operator lijst:

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

Voor numerieke Opera tors zoals *kleiner dan* en *groter dan of gelijk aan*, wordt de gebruikte vergelijking gebaseerd op lengte. In dit geval moet de waarde in de overeenkomst voor waarde een geheel getal zijn dat gelijk is aan de lengte die u wilt vergelijken. 


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het instellen van de configuratie van uw eerste [regel engine](front-door-tutorial-rules-engine.md). 
- Meer informatie over [acties](front-door-rules-engine-actions.md) van de engine voor regels
- Meer informatie over de regels van de [Azure front deur-engine](front-door-rules-engine.md)
