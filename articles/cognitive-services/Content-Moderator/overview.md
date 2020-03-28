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
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 570d55c8523e1c1deca3242a8841b0cc34322786
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053879"
---
# <a name="what-is-azure-content-moderator"></a>Wat is Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator is een cognitieve service die tekst-, beeld- en video-inhoud controleert op materiaal dat mogelijk aanstootgevend, riskant of anderszins ongewenst is. Wanneer dit materiaal wordt gevonden, past de service de juiste labels (vlaggen) toe op de inhoud. Uw app kan gelabelde inhoud vervolgens afhandelen om te voldoen aan de regelgeving of om een beoogde omgeving voor gebruikers te beheren. Zie de sectie [Moderatie-API's](#moderation-apis) voor meer informatie over wat de verschillende inhoudsvlaggen aangeven.

## <a name="where-its-used"></a>Waar het wordt gebruikt

Hieronder vindt u enkele scenario's waarin een softwareontwikkelaar of -team Content Moderator kan gebruiken:

- Online marktplaatsen die productcatalogi en andere door gebruikers gegenereerde inhoud beheren.
- Gaming bedrijven die door gebruikers gegenereerde game artefacten en chatrooms matigen.
- Sociale berichtenplatforms die afbeeldingen, tekst en video's beheren die door hun gebruikers zijn toegevoegd.
- Enterprise mediabedrijven die gecentraliseerde moderatie implementeren voor hun inhoud.
- Aanbieders van onderwijsoplossingen voor het basisonderwijs filteren inhoud uit die niet geschikt is voor studenten en docenten.

> [!NOTE]
> U Inhoudsmoderator niet gebruiken om illegale afbeeldingen van kinderuitbuiting op te sporen. Gekwalificeerde organisaties kunnen de [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") echter gebruiken om te screenen op dit soort inhoud.

## <a name="what-it-includes"></a>Samenstelling van Content Moderator

De Content Moderator-service bestaat uit verschillende webservice-API's die beschikbaar zijn via zowel REST-aanroepen als een SDK voor .NET. Hiertoe behoort ook het hulpprogramma voor menselijke beoordeling, waarmee menselijke revisoren de service kunnen verbeteren of de toezichtfunctie kunnen aanpassen.

## <a name="moderation-apis"></a>Beheer-API's

De Content Moderator-service bevat moderatie-API's, die inhoud controleren op materiaal dat mogelijk ongepast of aanstootgevend is.

![blokdiagram voor moderatie-API's voor inhoudsmoderator](images/content-moderator-mod-api.png)

In de volgende tabel worden de verschillende typen moderatie-API's beschreven.

| API-groep | Beschrijving |
| ------ | ----------- |
|[**Tekstmatigheid**](text-moderation-api.md)| Scant tekst op aanstootgevende inhoud, seksueel expliciete of suggestieve inhoud, godslastering en persoonlijke gegevens.|
|[**Aangepaste terminologielijsten**](try-terms-list-api.md)| Scant tekst op een aangepaste lijst met termen, samen met de ingebouwde termen. U kunt aangepaste lijsten gebruiken om inhoud te blokkeren of toe te staan volgens uw eigen beleidsregels voor inhoud.|  
|[**Beeldmatiging**](image-moderation-api.md)| Hiermee worden afbeeldingen gescand op erotische of racistische inhoud, wordt tekst in afbeeldingen gedetecteerd met de OCR-functie (optische tekenherkenning) en worden gezichten gedetecteerd.|
|[**Aangepaste afbeeldingslijsten**](try-image-list-api.md)| Hiermee worden afbeeldingen vergeleken met een aangepaste lijst van afbeeldingen. U kunt aangepaste afbeeldingslijsten gebruiken om te filteren op exemplaren van veelvoorkomende terugkerende inhoud die u niet opnieuw wilt classificeren.|
|[**Videomoderatie**](video-moderation-api.md)| Hiermee worden video's gescand op erotische of racistische inhoud en worden tijdmarkeringen voor deze inhoud geretourneerd.|

## <a name="review-apis"></a>Beoordelings-API's

Met de beoordelings-API's u uw moderatiepijplijn integreren met menselijke beoordelaars. Gebruik de [bewerkingen Taken,](review-api.md#jobs) [Beoordelingen](review-api.md#reviews)en [Werkstroom](review-api.md#workflows) om workflows voor mensen in de loop te maken en te automatiseren met het [hulpprogramma Controleren](#the-review-tool) (hieronder).

> [!NOTE]
> De Workflow API is nog niet beschikbaar in de .NET SDK, maar kan worden gebruikt met het REST-eindpunt.

![blokdiagram voor API's voor inhoudsmoderator](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Het gereedschap Controleren

De Content Moderator-service bevat ook de webgebaseerde [beoordelingstool,](Review-Tool-User-Guide/human-in-the-loop.md)die de inhoudsbeoordelingen host die menselijke moderators kunnen verwerken. De menselijke input traint de service niet, maar het gecombineerde werk van de service- en human reviewteams stelt ontwikkelaars in staat om de juiste balans te vinden tussen efficiëntie en nauwkeurigheid. De review tool biedt ook een gebruiksvriendelijke front-end voor verschillende Content Moderator bronnen.

![Startpagina van het hulpprogramma voor menselijke beoordeling van Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Content Moderator-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de Content Moderator-service door de instructies in [Content Moderator op het web proberen](quick-start.md) te volgen.
