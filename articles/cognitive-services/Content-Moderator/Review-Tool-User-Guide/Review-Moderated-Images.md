---
title: Inhoudsbeoordelingen gebruiken via de tool Beoordelen - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Ontdek hoe met de beoordelingstool menselijke moderators afbeeldingen in een webportal kunnen bekijken.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044113"
---
# <a name="create-human-reviews"></a>Menselijke beoordelingen maken

In deze handleiding leert u hoe u [beoordelingen](../review-api.md#reviews) instelt op de website van de beoordelingstool. Beoordelingen slaan inhoud op en tonen inhoud die menselijke moderators kunnen beoordelen. Moderators kunnen de toegepaste tags wijzigen en hun eigen aangepaste tags toepassen. Wanneer een gebruiker een beoordeling voltooit, worden de resultaten naar een opgegeven callback-eindpunt verzonden en wordt de inhoud van de site verwijderd.

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account aan op de site van het [hulpprogramma Inhoudsmoderator.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="image-reviews"></a>Beoordelingen van afbeeldingen

1. Ga naar het [gereedschap Controleren,](https://contentmoderator.cognitive.microsoft.com/)selecteer het tabblad **Proberen** en upload enkele afbeeldingen om te bekijken.
1. Zodra de geüploade afbeeldingen klaar zijn met de verwerking, gaat u naar het tabblad **Controleren** en selecteert **u Afbeelding**.

    ![Chrome-browser met het controleprogramma met de optie Afbeelding controleren gemarkeerd](images/review-images-1.png)

    De afbeeldingen worden weergegeven met labels die zijn toegewezen door het automatische moderatieproces. De afbeeldingen die u via het gereedschap Controleren hebt verzonden, zijn niet zichtbaar voor andere revisoren.

1. Verplaats de **schuifregelaar 'Reviews** optioneel om de schuifregelaar (1) weer te geven om het aantal afbeeldingen dat op het scherm wordt weergegeven, aan te passen. Klik op de **gelabelde** of **niet-gelabelde** knoppen (2) om de afbeeldingen dienovereenkomstig te sorteren. Klik op een tagpaneel (3) om het in of uit te schakelen.

    ![Chrome-browser met het hulpprogramma Controleren met gelabelde afbeeldingen ter controle](images/review-images-2.png)

1. Als u meer informatie over een afbeelding wilt zien, klikt u op de ellips in de miniatuur en selecteert u **Details weergeven**. U een afbeelding toewijzen aan een subteam met de optie **Verplaatsen naar** (zie de [sectie Teams](./configure.md#manage-team-and-subteams) voor meer informatie over subteams).

    ![Een afbeelding met de optie Details weergeven gemarkeerd](images/review-images-3.png)

1. Blader door de informatie over het beheren van afbeeldingen op de detailpagina.

    ![Een afbeelding met moderatiedetails in een apart deelvenster](images/review-images-4.png)

1. Nadat u de tagtoewijzingen hebt beoordeeld en bijgewerkt, klikt u op **Volgende** om uw beoordelingen in te dienen. Nadat u hebt ingediend, hebt u ongeveer vijf seconden om op de **prev-knop** te klikken om terug te keren naar het vorige scherm en afbeeldingen opnieuw te bekijken. Daarna staan de afbeeldingen niet meer in de wachtrij Verzenden en is de **prev-knop** niet meer beschikbaar.

## <a name="text-reviews"></a>Beoordelingen van teksten

Tekstbeoordelingen werken op dezelfde manier als beeldrecensies. In plaats van inhoud te uploaden, schrijf of plak je gewoon tekst (tot 1.024 tekens). Vervolgens analyseert Content Moderator de tekst en past tags toe (naast andere moderatie-informatie, zoals godslastering en persoonlijke gegevens). In tekstrecensies u de toegepaste tags inschakelen en/of aangepaste tags toepassen voordat u de beoordeling indient.

![Schermafbeelding van het controleprogramma met gemarkeerde tekst in een Chrome-browservenster](../images/reviewresults_text.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding heb je geleerd hoe je beoordelingen instellen en gebruiken via de [tool Inhoudsmoderator.In](https://contentmoderator.cognitive.microsoft.com)this guide, you learned how to set up and use reviews from the Content Moderator Review tool. Zie vervolgens de [REST API-handleiding](../try-review-api-review.md) of de [.NET SDK snelstart](../dotnet-sdk-quickstart.md) om te leren hoe u programmatisch beoordelingen maken.