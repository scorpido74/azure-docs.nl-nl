---
title: Wat is Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Leer hoe u met Content Moderator ongepaste inhoud in door gebruikers gegenereerd materiaal kunt traceren, markeren, beoordelen en filteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f28f2bcf5d04c9a6354b8135bd39546b9d8b9bf3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404303"
---
# <a name="what-is-azure-content-moderator"></a>Wat is Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator is een cognitieve service waarmee tekst-, afbeeldings-en video-inhoud wordt gecontroleerd op materiaal dat mogelijk aanstootgevend, riskant of anderszins ongewenst is. Wanneer dit materiaal wordt gevonden, past de service toepasselijke labels (vlaggen) toe op de inhoud. Uw app kan gelabelde inhoud vervolgens afhandelen om te voldoen aan de regelgeving of om een beoogde omgeving voor gebruikers te beheren. Zie de sectie [toezicht api's](#moderation-apis) voor meer informatie over wat de verschillende inhouds vlaggen aangeven.

## <a name="where-its-used"></a>Waar wordt gebruikt?

Hieronder vindt u enkele scenario's waarin een softwareontwikkelaar of -team Content Moderator kan gebruiken:

- Online markt plaatsen die gematige product catalogi en andere door de gebruiker gegenereerde inhoud zijn.
- Gaming maatschappijen die door de gebruiker gegenereerde Game artefacten en chat ruimten worden gematigd.
- Social Messa ging-platforms die gematige afbeeldingen, tekst en Video's toevoegen door hun gebruikers.
- Zakelijke media bedrijven die gecentraliseerde toezicht op hun inhoud implementeren.
- Met K-12-onderwijs oplossingen worden inhoud die niet geschikt is voor studenten en docenten, gefilterd.

> [!NOTE]
> U kunt Content Moderator niet gebruiken om illegale installatie kopieën van onderliggende licenties te detecteren. Gekwalificeerde organisaties kunnen de [PhotoDNA-Cloud service](https://www.microsoft.com/photodna "Micro soft PhotoDNA-Cloud service") echter gebruiken voor het scherm voor dit type inhoud.

## <a name="what-it-includes"></a>Samenstelling van Content Moderator

De Content Moderator-service bestaat uit verschillende webservice-API's die beschikbaar zijn via zowel REST-aanroepen als een SDK voor .NET. Het bevat ook het hulp programma voor het beoordelen, waarmee mensen van de mens de service kunnen helpen en de functie van de controle kan verbeteren of verfijnen.

## <a name="moderation-apis"></a>Beheer-API's

De Content Moderator-service omvat moderator-Api's, die inhoud controleren op materiaal dat mogelijk ongeschikt of onbruikbaar is.

![Blok diagram voor Api's voor Content Moderator toezicht](images/content-moderator-mod-api.png)

In de volgende tabel worden de verschillende typen toezicht-Api's beschreven.

| API-groep | Beschrijving |
| ------ | ----------- |
|[**Beheer van tekst**](text-moderation-api.md)| Hiermee wordt tekst gescand op aanstootgevende inhoud, seksueel expliciete of suggestieve inhoud, groveheid en persoonlijke gegevens.|
|[**Aangepaste terminologielijsten**](try-terms-list-api.md)| Hiermee wordt tekst gescand op basis van een aangepaste lijst met voor waarden en de ingebouwde voor waarden. U kunt aangepaste lijsten gebruiken om inhoud te blokkeren of toe te staan volgens uw eigen beleidsregels voor inhoud.|  
|[**Beheer van afbeeldingen**](image-moderation-api.md)| Hiermee worden afbeeldingen gescand op erotische of racistische inhoud, wordt tekst in afbeeldingen gedetecteerd met de OCR-functie (optische tekenherkenning) en worden gezichten gedetecteerd.|
|[**Aangepaste afbeeldingslijsten**](try-image-list-api.md)| Hiermee worden afbeeldingen vergeleken met een aangepaste lijst van afbeeldingen. U kunt aangepaste afbeeldingslijsten gebruiken om te filteren op exemplaren van veelvoorkomende terugkerende inhoud die u niet opnieuw wilt classificeren.|
|[**Beheer van video**](video-moderation-api.md)| Hiermee worden video's gescand op erotische of racistische inhoud en worden tijdmarkeringen voor deze inhoud geretourneerd.|

## <a name="review-apis"></a>Beoordelings-API's

Met de controle-Api's kunt u uw toezicht pijplijn integreren met menselijke revisoren. Gebruik de [taken](review-api.md#jobs), [Recensies](review-api.md#reviews)en [werk stroom](review-api.md#workflows) bewerkingen voor het maken en automatiseren van Human-in-the-loop-werk stromen met het [hulp programma voor beoordeling](#review-tool) (hieronder).

> [!NOTE]
> De werk stroom-API is nog niet beschikbaar in de .NET SDK, maar kan worden gebruikt met het REST-eind punt.

![Blok diagram voor Api's voor Content Moderator-controle](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Beoordelingsprogramma

De Content Moderator-service bevat ook het op het web gebaseerde [revisie programma](Review-Tool-User-Guide/human-in-the-loop.md), dat als host fungeert voor de inhouds recensies die personeel moet verwerken. De menselijke invoer traint de service niet, maar het gecombineerde werk van de service en de personeels teams kunnen ontwikkel aars de juiste balans tussen efficiëntie en nauw keurigheid halen. Het hulp programma voor controle biedt ook een gebruikers vriendelijke front-end voor verschillende Content Moderator-resources.

![Start pagina van Content Moderator-controle programma](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Content Moderator-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de Content Moderator-service door de instructies in [Content Moderator op het web proberen](quick-start.md) te volgen.
