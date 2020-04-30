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
ms.openlocfilehash: 3e7c9606a17736ea45b09a4d6981b4d55fa6dee6
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515557"
---
# <a name="azure-front-door-rules-engine-actions"></a>Engine acties voor regels van de Azure front deur

In de [afd-regels-engine](front-door-rules-engine.md) bestaat een regel uit nul of meer match voorwaarden en acties. In dit artikel vindt u gedetailleerde beschrijvingen van de acties die u kunt gebruiken in de engine voor de AFD-regels.

Een actie definieert het gedrag dat wordt toegepast op het aanvraag type dat overeenkomt met de voor waarde of set van match-voor waarden. In de engine voor de AFD-regels kan een regel Maxi maal vijf acties bevatten. er kan slechts één van de actie voor het overschrijven van de route configuratie (voorwaarts of omleiden) zijn. 

De volgende acties zijn beschikbaar voor gebruik in de Azure front deur-regel engine.  

## <a name="modify-request-header"></a>Aanvraag header wijzigen

Gebruik deze actie om kopteksten te wijzigen die aanwezig zijn in aanvragen die worden verzonden naar uw oorsprong.

### <a name="required-fields"></a>Vereiste velden

Bewerking | Naam van HTTP-header | Waarde
-------|------------------|------
Toevoegen | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de naam van de **header** toegevoegd aan de aanvraag met de opgegeven waarde. Als de header al aanwezig is, wordt de waarde toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de naam van de **header** toegevoegd aan de aanvraag met de opgegeven waarde. Als de header al aanwezig is, overschrijft de opgegeven waarde de bestaande waarde. | Tekenreeks
Verwijderen | Als deze optie is geselecteerd, de regel komt overeen met de opgegeven koptekst in de regel, wordt de header uit de aanvraag verwijderd. | Tekenreeks

## <a name="modify-response-header"></a>Antwoord header wijzigen

Gebruik deze actie om kopteksten te wijzigen die aanwezig zijn in antwoorden die aan uw clients worden geretourneerd.

### <a name="required-fields"></a>Vereiste velden

Bewerking | Naam van HTTP-header | Waarde
-------|------------------|------
Toevoegen | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de **header naam** , aan het antwoord toegevoegd met behulp van de opgegeven **waarde**. Als de header al aanwezig is, wordt de **waarde** aan de bestaande waarde toegevoegd. | Tekenreeks
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de **header naam** , aan het antwoord toegevoegd met behulp van de opgegeven **waarde**. Als de header al aanwezig is, overschrijft de **waarde** de bestaande waarde. | Tekenreeks
Verwijderen | Als deze optie is geselecteerd, de regel komt overeen met de opgegeven koptekst in de regel, wordt de header uit het antwoord verwijderd. | Tekenreeks

## <a name="route-configuration-overrides"></a>Overschrijvingen van route configuratie 

### <a name="route-type-redirect"></a>Route type: omleiden

Gebruik deze actie om clients om te leiden naar een nieuwe URL. 

#### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Type omleiding | Selecteer het antwoord type dat u wilt terugsturen naar de aanvrager: gevonden (302), verplaatst (301), tijdelijke omleiding (307) en permanente omleiding (308).
Protocol omleiden | Match-aanvraag, HTTP, HTTPS.
Doelhost | Selecteer de hostnaam waarvan u de aanvraag wilt omleiden. Laat het veld leeg om de binnenkomende host te bewaren.
Doelpad | Definieer het pad dat in de omleiding moet worden gebruikt. Laat het veld leeg als u het binnenkomende pad wilt behouden.  
Queryreeks | Definieer de query teken reeks die in de omleiding wordt gebruikt. Laat het veld leeg om de binnenkomende query reeks te bewaren. 
Doel fragment | Definieer het fragment dat in de omleiding moet worden gebruikt. Laat het veld leeg om het binnenkomende fragment te bewaren. 


### <a name="route-type-forward"></a>Route type: door sturen

Gebruik deze actie om clients door te sturen naar een nieuwe URL. Deze actie bevat ook sub-acties voor het herschrijven van URL'S en het opslaan in de cache. 

Veld | Beschrijving 
------|------------
Back-endpool | Selecteer de back-end-groep om de aanvragen van te overschrijven en te verwerken. Hiermee worden alle vooraf geconfigureerde back-endservers weer gegeven die momenteel in uw front-deur profiel staan. 
Doorstuur Protocol | Match-aanvraag, HTTP, HTTPS.
URL opnieuw genereren | Gebruik deze actie om het pad van een aanvraag die naar uw oorsprong is doorgestuurd, opnieuw te schrijven. Als deze functie is ingeschakeld, raadpleegt u hieronder voor aanvullende velden vereist
Caching | Ingeschakeld, Uitgeschakeld. Zie hieronder voor aanvullende velden die vereist zijn als deze functie is ingeschakeld. 

#### <a name="url-rewrite"></a>URL opnieuw genereren

Gebruik deze instelling om een optioneel **aangepast doorstuur traject** te configureren dat moet worden gebruikt bij het maken van de aanvraag om door te sturen naar de back-end.

Veld | Beschrijving 
------|------------
Aangepast doorstuur traject | Definieer het pad voor het door sturen van de aanvragen naar. 

#### <a name="caching"></a>Caching

Gebruik deze instellingen om te bepalen hoe bestanden worden opgeslagen in de cache voor aanvragen die query reeksen bevatten en of de inhoud moet worden opgeslagen in de cache op basis van alle para meters of op geselecteerde para meters. U kunt extra instellingen gebruiken om de TTL-waarde (time to Live) te overschrijven om te bepalen hoe lang inhoud in de cache blijft, voor aanvragen die voldoen aan de voor waarden van regels. Als u het opslaan in de cache wilt afdwingen als actie, stelt u het veld opslaan in op ingeschakeld. Als u dit doet, worden de volgende opties weer gegeven: 

Cache gedrag |  Beschrijving              
---------------|----------------
Queryreeksen negeren | Zodra het activum in de cache is opgeslagen, negeert alle volgende aanvragen de query teken reeksen totdat het activum in de cache verloopt.
Elke unieke URL in de cache opslaan | Elke aanvraag met een unieke URL, met inbegrip van de query reeks, wordt behandeld als een unieke Asset met een eigen cache.
Opgegeven query reeksen negeren | De query teken reeksen van de aanvraag-URL die worden vermeld in de instelling query parameters, worden genegeerd voor caching.
Opgegeven query reeksen toevoegen | Query-URL-aanvraag teken reeksen die worden vermeld in de instelling query parameters, worden gebruikt voor het opslaan in cache.

Aanvullende velden |  Beschrijving 
------------------|---------------
Dynamische compressie | De voor deur kan inhoud op de rand dynamisch comprimeren, wat resulteert in een kleiner en sneller antwoord.
Queryparameters | Een door komma's gescheiden lijst met toegestane (of niet-toegestane) para meters die moeten worden gebruikt als basis voor het opslaan in de cache.
Cache duur | Verval tijd van de cache in dagen, uren, minuten en seconden. Alle waarden moeten int zijn. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het instellen van de configuratie van uw eerste [regel engine](front-door-tutorial-rules-engine.md). 
- Meer informatie over de [matching voorwaarden](front-door-rules-engine-match-conditions.md) van de regels-engine
- Meer informatie over de regels van de [Azure front deur-engine](front-door-rules-engine.md)
