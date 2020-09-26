---
title: Een Brands model aanpassen met de Video Indexer-website
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een merk model met de Video Indexer-website.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 5bd88493324867dc957922a732506f5cfb8bbc20
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361231"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Een Brands model aanpassen met de Video Indexer-website

Video Indexer ondersteunt merk detectie van spraak-en visuele tekst tijdens het indexeren en opnieuw indexeren van video-en audio-inhoud. De merk detectie functie identificeert vermeldingen van producten, services en bedrijven die worden voorgesteld door de merken database van Bing. Als micro soft bijvoorbeeld wordt vermeld in video-of audio-inhoud, of als het in visuele tekst in een video wordt weer gegeven, detecteert Video Indexer deze als een merk in de inhoud.

Met een aangepast merk model kunt u het volgende doen:

- Selecteer deze optie als u wilt dat Video Indexer Brands detecteert van de Bing Brands-data base.
- Selecteer deze optie als u wilt dat Video Indexer bepaalde merken uitsluiten van detectie (in feite het maken van een lijst met geweigerde merken).
- Selecteer deze optie als u wilt dat Video Indexer Brands bevat die deel moeten uitmaken van uw model en zich mogelijk niet in de data base van Bing Brands bevallen (in feite een acceptatie lijst met merken maken).

Zie dit [overzicht](customize-brands-model-overview.md)voor een gedetailleerd overzicht.

U kunt de Video Indexer-website gebruiken om aangepaste merk modellen te maken, te gebruiken en te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U kunt ook de API gebruiken, zoals beschreven in de instructies [model aanpassen met behulp van api's](customize-brands-model-with-api.md).

## <a name="edit-brands-model-settings"></a>Model instellingen voor Brands bewerken

U kunt instellen of u wilt dat de merken van de Bing Brands-Data Base worden gedetecteerd. Als u deze optie wilt instellen, moet u de instellingen van uw Brands model bewerken. Volg deze stappen:

1. Ga naar de [video indexer](https://www.videoindexer.ai/) -website en meld u aan.
2. Als u een model in uw account wilt aanpassen, selecteert u de knop **aanpassing van inhouds model** in de rechter bovenhoek van de pagina.

   ![Het inhouds model in Video Indexer aanpassen](./media/content-model-customization/content-model-customization.png)

3. Als u de Brands wilt bewerken, selecteert u het tabblad **Brands** .

    ![Scherm afbeelding toont het tabblad Brands van het dialoog venster aanpassing van het inhouds model.](./media/customize-brand-model/customize-brand-model.png)

4. Schakel de optie **Brands weer geven die door Bing worden voorgesteld** in als u wilt dat video indexer de door Bing Aanbevolen merken detecteert. laat de optie uitgeschakeld als dat niet het geval is.

## <a name="include-brands-in-the-model"></a>Brands in het model toevoegen

De sectie **inclusief Brands bevat** aangepaste merken die u video indexer wilt laten detecteren, zelfs als ze niet worden voorgesteld door Bing.  

### <a name="add-a-brand-to-include-list"></a>Een merk toevoegen aan de lijst met insluitingen

1. Selecteer **+ merk toevoegen**.

    ![Scherm afbeelding toont het dialoog venster voor het insluiten van Brands, waarin u Brands kunt toevoegen.](./media/customize-brand-model/add-brand.png)

    Geef een naam op (vereist), categorie (optioneel), beschrijving (optioneel) en Referentie-URL (optioneel).
    Het veld categorie is bedoeld om u te helpen labels voor uw Brands te geven. Dit veld wordt weer gegeven als *Tags* van het merk bij gebruik van de video indexer-api's. Het merk "Azure" kan bijvoorbeeld worden gelabeld of gecategoriseerd als "Cloud".

    Het veld Referentie-URL kan een referentie website zijn voor het merk (zoals een koppeling naar de Wikipedia-pagina).

2. Selecteer **merk toevoegen** en u ziet dat het merk is toegevoegd aan de lijst met **include Brands** .

### <a name="edit-a-brand-on-the-include-list"></a>Een merk bewerken in de lijst met insluitingen

1. Selecteer het potlood pictogram naast het merk dat u wilt bewerken.

    U kunt de categorie, beschrijving of Referentie-URL van een merk bijwerken. U kunt de naam van een merk niet wijzigen, omdat de namen van merken uniek zijn. Als u de merk naam moet wijzigen, verwijdert u het gehele merk (Zie de volgende sectie) en maakt u een nieuw merk met de nieuwe naam.

2. Selecteer de knop **bijwerken** om het merk bij te werken met de nieuwe informatie.

### <a name="delete-a-brand-on-the-include-list"></a>Een merk verwijderen uit de lijst met insluitingen

1. Selecteer het prullenbak pictogram naast het merk dat u wilt verwijderen.
2. Selecteer **verwijderen** en het merk wordt niet meer weer gegeven in de lijst *brandss opnemen* .

## <a name="exclude-brands-from-the-model"></a>Merken uitsluiten van het model

De sectie **Brands uitsluiten** vertegenwoordigt de merken die u niet video indexer wilt laten detecteren.

### <a name="add-a-brand-to-exclude-list"></a>Een merk toevoegen aan de uitsluitings lijst

1. Selecteer **+ merk toevoegen.**

    Geef een naam op (vereist), categorie (optioneel).

2. Selecteer **merk toevoegen** en u ziet dat het merk is toegevoegd aan de lijst met *uitzonde ringen* .

### <a name="edit-a-brand-on-the-exclude-list"></a>Een merk bewerken in de uitsluitings lijst

1. Selecteer het potlood pictogram naast het merk dat u wilt bewerken.

    U kunt de categorie van een merk alleen bijwerken. U kunt de naam van een merk niet wijzigen, omdat de namen van merken uniek zijn. Als u de merk naam moet wijzigen, verwijdert u het gehele merk (Zie de volgende sectie) en maakt u een nieuw merk met de nieuwe naam.

2. Selecteer de knop **bijwerken** om het merk bij te werken met de nieuwe informatie.

### <a name="delete-a-brand-on-the-exclude-list"></a>Een merk verwijderen uit de uitsluitings lijst

1. Selecteer het prullenbak pictogram naast het merk dat u wilt verwijderen.
2. Selecteer **verwijderen** en het merk wordt niet meer weer gegeven in de lijst *exclude Brands* .

## <a name="next-steps"></a>Volgende stappen

[Het merk model aanpassen met behulp van Api's](customize-brands-model-with-api.md)
