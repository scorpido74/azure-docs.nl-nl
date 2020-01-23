---
title: De Video Indexer-website gebruiken om merk modellen aan te passen-Azure
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u een merk model kunt aanpassen met de Video Indexer-website.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 956ca7af055768398392045ecf9b383d2eb1060f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513895"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Een Brands model aanpassen met de Video Indexer-website

Video Indexer ondersteunt merk detectie van spraak-en visuele tekst tijdens het indexeren en opnieuw indexeren van video-en audio-inhoud. De merk detectie functie identificeert vermeldingen van producten, services en bedrijven die worden voorgesteld door de merken database van Bing. Als micro soft bijvoorbeeld wordt vermeld in een video-of audio-inhoud of als het in visuele tekst in een video wordt weer gegeven, detecteert Video Indexer deze als een merk in de inhoud. Met een aangepast merk model kunt u selecteren of u Video Indexer merken detecteert van de Bing Brands-data base, of u bepaalde merken wilt uitsluiten van detectie (in wezen een zwarte lijst met merken maakt) en op te nemen welke merken deel uitmaken van uw model Dit is mogelijk niet te zien in de merken database van Bing (in feite een witte lijst met merken maken).

Zie [overzicht](customize-brands-model-overview.md)voor een gedetailleerd overzicht.

U kunt de Video Indexer-website gebruiken om aangepaste merk modellen te maken, te gebruiken en te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U kunt ook de API gebruiken, zoals beschreven in de instructies [model aanpassen met behulp van api's](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>De instellingen van het Brands model bewerken  

U kunt instellen of u wilt dat de merken van de Bing Brands-Data Base worden gedetecteerd. Hiervoor moet u de instellingen van uw Brands model bewerken.

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
2. Als u een model in uw account wilt aanpassen, klikt u op de knop **aanpassing van inhouds model** in de rechter bovenhoek van de pagina.
 
   ![Inhouds model aanpassen](./media/content-model-customization/content-model-customization.png) 
3. Als u de Brands wilt bewerken, selecteert u het tabblad **Brands** .

    ![Brands model aanpassen](./media/customize-brand-model/customize-brand-model.png)
4. Schakel de optie **Brands weer geven die door Bing worden voorgesteld** in als u wilt dat video indexer de door Bing voorgestelde merken bevat. Laat de optie uitgeschakeld als u niet wilt dat Video Indexer de door Bing voorgestelde merken detecteert in uw inhoud. 

## <a name="include-brands-in-the-model"></a>Brands in het model toevoegen

De sectie **inclusief Brands bevat** aangepaste merken die u door video indexer wilt laten detecteren, zelfs als ze niet worden voorgesteld door Bing.  

### <a name="add-a-brand"></a>Een merk toevoegen

1. Klik op "+ merk toevoegen".

    ![Brands model aanpassen](./media/customize-brand-model/add-brand.png)

    Geef een naam op (vereist), categorie (optioneel), beschrijving (optioneel) en Referentie-URL (optioneel).
    Het veld categorie is bedoeld om u te helpen labels voor uw Brands te geven. Dit veld wordt weer gegeven als *Tags* van het merk bij gebruik van de video indexer-api's. Het merk "Azure" kan bijvoorbeeld worden gelabeld of gecategoriseerd als "Cloud".

    Het veld Referentie-URL kan een referentie website zijn voor het merk, zoals een koppeling naar de Wikipedia-pagina.
2. Klik op merk toevoegen en u ziet dat het merk is toegevoegd aan de lijst met **include Brands** .

### <a name="edit-a-brand"></a>Een merk bewerken

1. Klik op het potlood pictogram naast het merk dat u wilt bewerken.

    U kunt de categorie, beschrijving of Referentie-URL van een merk bijwerken. U kunt de naam van een merk niet wijzigen, omdat de namen van merken uniek zijn. Als u de merk naam moet wijzigen, verwijdert u het gehele merk (Zie de volgende sectie) en maakt u een nieuw merk met de nieuwe naam.
2. Klik op de knop **bijwerken** om het merk bij te werken met de nieuwe informatie.

### <a name="delete-a-brand"></a>Een merk verwijderen

1. Klik op het prullenbak pictogram naast het merk dat u wilt verwijderen.
2. Klik op verwijderen en het merk wordt niet meer weer gegeven in de lijst *brandss opnemen* .

## <a name="exclude-brands-from-the-model"></a>Merken uitsluiten van het model

De sectie **Brands uitsluiten** vertegenwoordigt de merken die u wilt video indexer niet detecteren.

### <a name="add-a-brand"></a>Een merk toevoegen

1. Klik op "+ merk toevoegen".

    Geef een naam op (vereist), categorie (optioneel).
2. Klik op merk toevoegen en u ziet dat het merk is toegevoegd aan de lijst met *uitzonde ringen* .

### <a name="edit-a-brand"></a>Een merk bewerken

1. Klik op het potlood pictogram naast het merk dat u wilt bewerken.

    U kunt de categorie van een merk alleen bijwerken. U kunt de naam van een merk niet wijzigen, omdat de namen van merken uniek zijn. Als u de merk naam moet wijzigen, verwijdert u het gehele merk (Zie de volgende sectie) en maakt u een nieuw merk met de nieuwe naam.
2. Klik op de knop **bijwerken** om het merk bij te werken met de nieuwe informatie.

### <a name="delete-a-brand"></a>Een merk verwijderen

1. Klik op het prullenbak pictogram naast het merk dat u wilt verwijderen.
2. Klik op verwijderen en het merk wordt niet meer weer gegeven in de lijst met *exclude-merken* .

## <a name="next-steps"></a>Volgende stappen

[Het merk model aanpassen met behulp van Api's](customize-brands-model-with-api.md)
