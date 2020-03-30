---
title: Een Brands-model aanpassen met de website video-indexer
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een brands-model met de website Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128047"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Een Brands-model aanpassen met de website video-indexer

Video Indexer ondersteunt merkdetectie van spraak en visuele tekst tijdens het indexeren en opnieuw indexeren van video- en audio-inhoud. De merkdetectiefunctie identificeert vermeldingen van producten, services en bedrijven die worden voorgesteld door de merkendatabase van Bing. Als Microsoft bijvoorbeeld wordt vermeld in video- of audio-inhoud of als deze wordt weergegeven in visuele tekst in een video, detecteert Video Indexer het als een merk in de inhoud.

Met een custom Brands-model u:

- selecteer als u wilt dat Video Indexer merken uit de bing-merkendatabase detecteert.
- selecteer als u wilt dat Video Indexer bepaalde merken uitsluit van gedetecteerde merken (in wezen een lijst met merken weigeren).
- selecteer als u wilt dat Video Indexer merken opneemt die deel moeten uitmaken van uw model en die mogelijk niet in de merkendatabase van Bing staan (in wezen een lijst met merken accepteren).

Voor een gedetailleerd overzicht, zie dit [overzicht](customize-brands-model-overview.md).

U de website Video Indexer gebruiken om aangepaste merkenmodellen te maken, gebruiken en bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U de API ook gebruiken, zoals beschreven in [het model Merken aanpassen met API's.](customize-brands-model-with-api.md)

## <a name="edit-brands-model-settings"></a>Modelinstellingen voor merken bewerken

U instellen of u merken uit de bing-merkendatabase wilt detecteren. Als u deze optie wilt instellen, moet u de instellingen van uw Brands-model bewerken. Volg deze stappen:

1. Ga naar de [website van Video Indexer](https://www.videoindexer.ai/) en meld u aan.
2. Als u een model in uw account wilt aanpassen, selecteert u de knop **Inhoudsmodel aanpassen** in de rechterbovenhoek van de pagina.

   ![Inhoudsmodel aanpassen in videoindexer](./media/content-model-customization/content-model-customization.png)

3. Als u merken wilt bewerken, selecteert u het tabblad **Merken.**

    ![Merkenmodel aanpassen in Video Indexer](./media/customize-brand-model/customize-brand-model.png)

4. Controleer de **optie Merken weergeven die door Bing worden voorgesteld** als u wilt dat Video-indexer merken detecteert die door Bing worden voorgesteld, en laat de optie niet aangevinkt als u dat niet doet.

## <a name="include-brands-in-the-model"></a>Merken opnemen in het model

De sectie **Merken opnemen** vertegenwoordigt aangepaste merken die u wilt dat Video Indexer detecteert, zelfs als deze niet worden voorgesteld door Bing.  

### <a name="add-a-brand-to-include-list"></a>Een merk toevoegen om een lijst op te nemen

1. Selecteer **+ Merk toevoegen**.

    ![Merkenmodel aanpassen in Video Indexer](./media/customize-brand-model/add-brand.png)

    Geef een naam (vereist), categorie (optioneel), beschrijving (optioneel) en referentie-URL (optioneel) op.
    Het categorieveld is bedoeld om u te helpen uw merken te taggen. Dit veld wordt weergegeven als de *tags* van het merk bij het gebruik van de API's voor video-indexer. Het merk "Azure" kan bijvoorbeeld worden getagd of gecategoriseerd als "Cloud".

    Het referentie-URL-veld kan elke referentiewebsite voor het merk zijn (zoals een link naar de Wikipedia-pagina).

2. Selecteer **Merk toevoegen** en je ziet dat het merk is toegevoegd aan de lijst Met merken **opnemen.**

### <a name="edit-a-brand-on-the-include-list"></a>Een merk bewerken op de lijst met include

1. Selecteer het potloodpictogram naast het merk dat u wilt bewerken.

    U de categorie, beschrijving of referentie-URL van een merk bijwerken. Je de naam van een merk niet veranderen omdat namen van merken uniek zijn. Als u de merknaam wilt wijzigen, verwijdert u het hele merk (zie volgende sectie) en maakt u een nieuw merk met de nieuwe naam.

2. Selecteer de knop **Bijwerken** om het merk bij te werken met de nieuwe informatie.

### <a name="delete-a-brand-on-the-include-list"></a>Een merk verwijderen in de lijst met include

1. Selecteer het prullenbakpictogram naast het merk dat u wilt verwijderen.
2. Selecteer **Verwijderen** en het merk wordt niet meer weergegeven in de lijst *Met merk opnemen.*

## <a name="exclude-brands-from-the-model"></a>Merken uitsluiten van het model

De sectie **Merken uitsluiten** vertegenwoordigt de merken die u niet wilt dat Video Indexer detecteert.

### <a name="add-a-brand-to-exclude-list"></a>Een merk toevoegen om de lijst uit te sluiten

1. Selecteer **+ Merk toevoegen.**

    Geef een naam (vereist), categorie (optioneel).

2. Selecteer **Merk toevoegen** en je ziet dat het merk is toegevoegd aan de lijst Merken *uitsluiten.*

### <a name="edit-a-brand-on-the-exclude-list"></a>Een merk bewerken op de lijst met uitsluiting

1. Selecteer het potloodpictogram naast het merk dat u wilt bewerken.

    U alleen de categorie van een merk bijwerken. Je de naam van een merk niet veranderen omdat namen van merken uniek zijn. Als u de merknaam wilt wijzigen, verwijdert u het hele merk (zie volgende sectie) en maakt u een nieuw merk met de nieuwe naam.

2. Selecteer de knop **Bijwerken** om het merk bij te werken met de nieuwe informatie.

### <a name="delete-a-brand-on-the-exclude-list"></a>Een merk verwijderen op de lijst met uitsluiting

1. Selecteer het prullenbakpictogram naast het merk dat u wilt verwijderen.
2. Selecteer **Verwijderen** en het merk wordt niet meer weergegeven in de lijst *Met merkproducten uitsluiten.*

## <a name="next-steps"></a>Volgende stappen

[Merkenmodel aanpassen met API's](customize-brands-model-with-api.md)
