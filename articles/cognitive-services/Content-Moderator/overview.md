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
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 82dc81c540115f08e57f87e63184e1e895c5e4fe
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87834538"
---
# <a name="what-is-azure-content-moderator"></a>Wat is Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator is een cognitieve service waarmee tekst, afbeeldingen en video-inhoud van materiaal wordt gecontroleerd op mogelijk aanstootgevende, riskante of anderszins ongewenst inhoud. Wanneer dit materiaal wordt gevonden, past de service de relevante labels (vlaggen) op de inhoud toe. Uw app kan gelabelde inhoud vervolgens afhandelen om te voldoen aan de regelgeving of om een beoogde omgeving voor gebruikers te beheren. Zie de sectie [Beheer-API's](#moderation-apis) voor meer informatie over wat de verschillende inhoudsvlaggen betekenen.

## <a name="where-its-used"></a>Waar het wordt gebruikt

Hieronder vindt u enkele scenario's waarin een softwareontwikkelaar of -team Content Moderator kan gebruiken:

- Onlinemarktplaatsen die toezicht willen houden op productcatalogi en andere door gebruikers gegenereerde inhoud.
- Gamingbedrijven die toezicht willen houden op door gebruikers gegenereerde gameartefacten en chatruimten.
- Platforms voor sociale berichten die toezicht willen houden op afbeeldingen, tekst en video's die door gebruikers worden toegevoegd.
- Grote mediabedrijven die gecentraliseerd toezicht implementeren voor hun inhoud.
- Leveranciers van oplossingen voor lager en voortgezet onderwijs die willen filteren op inhoud die ongepast is voor leerlingen en docenten.

> [!NOTE]
> U kunt Content Moderator niet gebruiken om illegale afbeeldingen van kindermisbruik te detecteren. Gekwalificeerde organisaties kunnen echter het scherm [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") gebruiken voor dit type inhoud.

## <a name="what-it-includes"></a>Samenstelling van Content Moderator

De Content Moderator-service bestaat uit verschillende webservice-API's die beschikbaar zijn via zowel REST-aanroepen als een SDK voor .NET. Hiertoe behoort ook het Beoordelingsprogramma, waarmee menselijke revisoren de service kunnen verbeteren of de toezichtfunctie kunnen aanpassen.

## <a name="moderation-apis"></a>Beheer-API's

De Content Moderator-service omvat Beheer-API’s, waarmee inhoud wordt gecontroleerd op materiaal dat mogelijk ongeschikt of aanstootgevend is.

![Blokdiagram voor Beheer-API's van Content Moderator](images/content-moderator-mod-api.png)

In de volgende tabel worden de verschillende typen Beheer-API's beschreven.

| API-groep | Beschrijving |
| ------ | ----------- |
|[**Beheer van tekst**](text-moderation-api.md)| Hiermee wordt tekst gescand op aanstootgevende inhoud, expliciet of suggestief seksuele inhoud, grof taalgebruik en persoonlijk gegevens.|
|[**Aangepaste terminologielijsten**](try-terms-list-api.md)| Hiermee wordt tekst vergeleken met een aangepaste lijst met termen, naast de ingebouwde termen. U kunt aangepaste lijsten gebruiken om inhoud te blokkeren of toe te staan volgens uw eigen beleidsregels voor inhoud.|  
|[**Beheer van afbeeldingen**](image-moderation-api.md)| Hiermee worden afbeeldingen gescand op erotische of racistische inhoud, wordt tekst in afbeeldingen gedetecteerd met de OCR-functie (optische tekenherkenning) en worden gezichten gedetecteerd.|
|[**Aangepaste afbeeldingslijsten**](try-image-list-api.md)| Hiermee worden afbeeldingen vergeleken met een aangepaste lijst van afbeeldingen. U kunt aangepaste afbeeldingslijsten gebruiken om te filteren op exemplaren van veelvoorkomende terugkerende inhoud die u niet opnieuw wilt classificeren.|
|[**Beheer van video**](video-moderation-api.md)| Hiermee worden video's gescand op erotische of racistische inhoud en worden tijdmarkeringen voor deze inhoud geretourneerd.|

## <a name="review-apis"></a>Beoordelings-API's

Met de Beoordelings-API’s kunt u uw beheerpijplijn integreren met menselijke revisoren. Gebruik de bewerkingen [Taken](review-api.md#jobs), [Beoordelingen](review-api.md#reviews) en [Werkstroom](review-api.md#workflows) om werkstromen voor menselijke beoordeling te maken en te automatiseren met het [Beoordelingsprogramma](#review-tool) (hieronder).

> [!NOTE]
> De werkstroom-API is nog niet beschikbaar in de .NET SDK, maar kan worden gebruikt met het REST-eindpunt.

![Blokdiagram voor Beoordelings-API's van Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Beoordelingsprogramma

De Content Moderator-service omvat ook het webgebaseerde [Beoordelingsprogramma](Review-Tool-User-Guide/human-in-the-loop.md) dat als host fungeert voor de inhoudsbeoordelingen van menselijke moderators. De service wordt niet getraind door menselijke invoer, maar de combinatie van het werk van de service en de menselijke beoordelingsteams zorgt voor een juiste balans tussen de efficiëntie en de nauwkeurigheid. Het Beoordelingsprogramma biedt ook een gebruikersvriendelijke front-end voor verschillende Content Moderator-resources.

![Startpagina voor het Beoordelingsprogramma van Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Content Moderator-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de Content Moderator-service door de instructies in [Content Moderator op het web proberen](quick-start.md) te volgen.
