---
title: 'Snelstart: Probeer Content Moderator op het web - Content Moderator'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruik je de online Content Moderator Review tool om de basisfunctionaliteit van Content Moderator uit te testen zonder code te hoeven schrijven.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 03/13/2020
ms.author: pafarley
ms.openlocfilehash: 666b70ba8b632cb2cadf20de384e3e615acb2b3d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203551"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Snelstart: Inhoudsmoderator op het web uitproberen

In deze quickstart gebruik je de online Content Moderator Review tool om de basisfunctionaliteit van Content Moderator uit te testen zonder code te hoeven schrijven. Als u deze service sneller in uw app wilt integreren, raadpleegt u de andere snelstarts in de sectie [Volgende stappen.](#next-steps)

## <a name="prerequisites"></a>Vereisten

- Een webbrowser

## <a name="set-up-the-review-tool"></a>Het controleprogramma instellen
De Content Moderator Review tool is een web-based tool waarmee menselijke reviewers om de cognitieve dienst te helpen bij het nemen van beslissingen. In deze handleiding ondergaat u het korte proces van het opzetten van de beoordelingstool, zodat u zien hoe de Content Moderator-service werkt. Ga naar de site van het [hulpprogramma Inhoudsmoderator](https://contentmoderator.cognitive.microsoft.com/) en meld u aan.

![Startpagina van inhoudsmoderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Maak vervolgens een beoordelingsteam. In een werkscenario is dit de groep mensen die handmatig de moderatiebeslissingen van de service zal controleren. Als u een team wilt maken, moet u een **regio**selecteren en een **teamnaam** en een **team-id**opgeven. Als u collega's wilt uitnodigen voor het team, u dit doen door hier hun e-mailadressen in te voeren.

> [!NOTE]
> **Teamname** is een vriendelijke naam voor je beoordelingsteam. Dit is de naam die wordt weergegeven in de Azure-portal. De **team-id** is wat wordt gebruikt om uw beoordelingsteam programmatisch te identificeren.

> [!div class="mx-imgBorder"]
> ![Teamlid uitnodigen](images/create-team.png)

Als u ervoor kiest om gegevens te versleutelen met een door de klant beheerde sleutel (CMK), wordt u gevraagd om de **Resource-ID** voor uw contentmoderatorbron in de prijscategorie E0. De bron die u verstrekt, moet nieuw zijn. 

> [!div class="mx-imgBorder"]
> ![Teamlid uitnodigen met CMK](images/create-team-cmk.png)

Als u een bron voor inhoudsmoderator opnieuw probeert te gebruiken, ziet u de waarschuwing: 

> [!div class="mx-imgBorder"]
> ![CMK-storing](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Voorbeeldinhoud uploaden

Nu ben je klaar om voorbeeldinhoud te uploaden. Selecteer **Probeer > afbeelding,** **probeer > tekst**of probeer **> video**.

![Afbeelding of tekstbeheer uitproberen](images/tryimagesortext.png)

Dien uw inhoud in voor moderatie. Intern zal de beoordelingstool de moderatie-API's aanroepen om uw inhoud te scannen. Zodra het scannen is voltooid, ziet u een bericht waarin u wordt geïnformeerd dat er resultaten wachten op uw beoordeling.

![Bestanden beheren](images/submitted.png)

## <a name="review-moderation-tags"></a>Moderatietags controleren

Bekijk de toegepaste moderatietags. U zien welke tags zijn toegepast op uw inhoud en wat de score was in elke categorie. Zie de onderwerpen [Afbeelding,](image-moderation-api.md) [tekst](text-moderation-api.md)en [videomoderatie](video-moderation-api.md) voor meer informatie over wat de verschillende inhoudstags aangeven.

![Resultaat controleren](images/reviewresults_text.png)

In een project u of uw beoordelingsteam deze tags wijzigen of indien nodig meer tags toevoegen. U verzendt deze wijzigingen met de knop **Volgende.** Terwijl uw bedrijfstoepassing de Moderator-API's aanroept, staat de gelabelde inhoud hier in de wachtrij, klaar om te worden beoordeeld door de menselijke beoordelingsteams. U snel grote hoeveelheden inhoud bekijken met behulp van deze aanpak.

Op dit moment hebt u de tool Inhoudsmoderator-beoordeling gebruikt om voorbeelden te zien van wat de service Content Moderator kan doen. Vervolgens u meer te weten komen over de beoordelingstool en hoe u deze integreren in een softwareproject met behulp van de Beoordelings-API's, of u naar de sectie [Volgende stappen](#next-steps) gaan om te leren hoe u de moderatie-API's zelf in uw app gebruiken.

## <a name="learn-more-about-the-review-tool"></a>Meer informatie over de beoordelingstool

Voor meer informatie over het gebruik van de tool Inhoudsmoderator, bekijk de tool [guide review](Review-Tool-User-Guide/human-in-the-loop.md) en raadpleeg de API's van de beoordelingstool om te leren hoe u de ervaring voor menselijke beoordeling verfijnen:
- De [Job API](try-review-api-job.md) scant uw inhoud met behulp van de moderatie API's en genereert beoordelingen in de beoordelingstool. 
- Met [de Review API](try-review-api-review.md) worden direct beeld-, tekst- of videobeoordelingen gemaakt voor menselijke moderators zonder eerst de inhoud te scannen. 
- De [Workflow-API](try-review-api-workflow.md) maakt, werkt bij en krijgt details over de aangepaste werkstromen die uw team maakt.

Of ga verder met de volgende stappen om aan de slag te gaan met de moderatie-API's in uw code.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de moderatie-API's zelf in uw app.
- Beeldmoderatie implementeren. Gebruik de [API-console](try-image-api.md) of volg de [.NET SDK quickstart](dotnet-sdk-quickstart.md) om afbeeldingen te scannen en potentiële inhoud voor volwassenen en racy te detecteren met behulp van tags, betrouwbaarheidsscores en andere geëxtraheerde informatie.
- Tekstmoderatie implementeren. Gebruik de [API-console](try-text-api.md) of gebruik de [.NET SDK quickstart](dotnet-sdk-quickstart.md) om tekstinhoud te scannen op mogelijke godslastering, door machines ondersteunde ongewenste tekstclassificatie (preview) en persoonlijke gegevens.
- Implementeer videomoderatie. Volg de [handleiding videomoderatie voor C#](video-moderation-api.md) om video's te scannen en potentiële inhoud voor volwassenen en racy te detecteren. 
