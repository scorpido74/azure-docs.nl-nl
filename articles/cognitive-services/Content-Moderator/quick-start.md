---
title: 'Snelstartgids: Probeer Content Moderator op het Web-Content Moderator'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u het hulp programma online Content Moderator beoordeling om de basis functionaliteit van Content Moderator te testen zonder dat u code hoeft te schrijven.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a641893fece37c759480ab31f505b1673f50e2b9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973608"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Snelstartgids: Probeer Content Moderator op Internet

In deze Quick Start gebruikt u het hulp programma online Content Moderator beoordeling om de basis functionaliteit van Content Moderator te testen zonder dat u code hoeft te schrijven. Als u deze service sneller wilt integreren in uw app, raadpleegt u de andere Quick starts in het gedeelte [volgende stappen](#next-steps) .

## <a name="prerequisites"></a>Vereisten

- Een webbrowser

## <a name="set-up-the-review-tool"></a>Het beoordelings programma instellen
Het hulp programma Content Moderator beoordeling is een webgebaseerd hulp programma waarmee mensen van Human reviewers de cognitieve service kunnen helpen bij het nemen van beslissingen. In deze hand leiding gaat u door de korte procedure voor het instellen van het controle programma, zodat u kunt zien hoe de Content Moderator-service werkt. Ga naar de site van het [Content moderator controle programma](https://contentmoderator.cognitive.microsoft.com/) en meld u aan.

![Start pagina van Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Maak vervolgens een beoordelings team. In een werkend scenario is dit de groep personen die hand matig de toezicht beslissingen van de service controleren. U hoeft alleen maar een team naam te maken. Als u collega's wilt uitnodigen voor het team, kunt u dit doen door hier hun e-mail adressen in te voeren.

![Teamlid uitnodigen](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Voorbeeld inhoud uploaden

U bent nu klaar om voorbeeld inhoud te uploaden. Selecteer **> afbeelding uitproberen**, **Probeer > tekst**uit of **Probeer de video >** .

![Afbeelding of tekst toezicht uitproberen](images/tryimagesortext.png)

Verzend uw inhoud voor toezicht. Intern roept het beoordelings programma de toezicht-Api's aan om uw inhoud te scannen. Zodra het scannen is voltooid, ziet u een bericht waarin wordt gemeld dat er resultaten zijn die wachten op uw beoordeling.

![Gemiddeld bestanden](images/submitted.png)

## <a name="review-moderation-tags"></a>Moderator Tags controleren

Controleer de toegepaste toezicht Tags. U kunt zien welke tags zijn toegepast op uw inhoud en wat de score in elke categorie heeft. Zie de onderwerpen over [afbeelding](image-moderation-api.md), [tekst](text-moderation-api.md)en [video](video-moderation-api.md) toezicht voor meer informatie over wat de verschillende inhouds Tags aangeven.

![Resultaat controleren](images/reviewresults_text.png)

In een project kunnen u of uw beoordelings team deze tags wijzigen of meer Tags toevoegen als dat nodig is. U verzendt deze wijzigingen met de knop **volgende** . Als uw zakelijke toepassing de moderator-Api's aanroept, wordt de gelabelde inhoud hier in de wachtrij geplaatst, zodat deze kan worden gecontroleerd door de teams van de menselijke beoordeling. Met deze aanpak kunt u snel grote volumes met inhoud bekijken.

Op dit moment hebt u het hulp programma Content Moderator beoordeling gebruikt om voor beelden te bekijken van wat de Content Moderator-service kan doen. Daarna kunt u meer te weten komen over het hulp programma voor beoordeling en hoe u dit integreert in een software project met behulp van de controle-Api's, of u kunt door gaan naar de sectie [volgende stappen](#next-steps) voor meer informatie over het gebruik van de toezicht api's zelf in uw app.

## <a name="learn-more-about-the-review-tool"></a>Meer informatie over het hulp programma beoordeling

Als u meer wilt weten over het gebruik van het hulp programma voor het evalueren van Content Moderator, raadpleegt u de hand leiding voor het [beoordelings programma](Review-Tool-User-Guide/human-in-the-loop.md) en bekijkt u de api's van het controle programma voor meer informatie over het afstemmen van de ervaring voor menselijke beoordeling:
- Met de [taak-API](try-review-api-job.md) wordt uw inhoud gescand met behulp van de moderator-api's en worden er beoordelingen gegenereerd in het beoordelings programma. 
- De [beoordelings-API](try-review-api-review.md) maakt rechtstreeks afbeeldings-, tekst-of video Beoordelingen voor menselijke moderators zonder eerst de inhoud te scannen. 
- De [werk stroom-API](try-review-api-workflow.md) maakt, vernieuwt en haalt Details over de aangepaste werk stromen die uw team maakt.

Of ga door met de volgende stappen om aan de slag te gaan met behulp van de toezicht-Api's in uw code.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de toezicht-Api's zelf in uw app.
- De afbeeldings toezicht wordt geïmplementeerd. Gebruik de [API-console](try-image-api.md) of volg de [Snelstartgids van de .NET SDK](dotnet-sdk-quickstart.md) om afbeeldingen te scannen en potentiële inhoud voor volwassenen en ongepaste te detecteren met behulp van tags, betrouwbaarheids scores en andere geëxtraheerde informatie.
- Implementeer tekst toezicht. Gebruik de [API-console](try-text-api.md) of gebruik de [SNELSTARTGIDS van .NET SDK](dotnet-sdk-quickstart.md) voor het scannen van tekst inhoud voor mogelijk scheld woorden, ongewenste tekst classificatie (preview) en persoonlijke gegevens.
- Implementeer de video toezicht. Volg de [instructies in de video-toezicht handleiding voor C# ](video-moderation-api.md) het scannen van Video's en het detecteren van potentiële inhoud voor volwassenen en ongepaste. 
