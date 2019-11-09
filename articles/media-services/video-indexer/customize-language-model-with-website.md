---
title: De Video Indexer-website gebruiken om een taal model aan te passen-Azure
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u een taal model kunt aanpassen met de Video Indexer-website.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 5d92cb02a0ac52b317cf9d4b6c8e0278f9291910
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838447"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Een taal model aanpassen met de Video Indexer-website

Met Video Indexer kunt u aangepaste taal modellen maken om spraak herkenning aan te passen door de aanpassings tekst te uploaden, namelijk de tekst van het domein waarvan u wilt dat de engine aan de woorden lijst voldoet. Zodra u het model hebt getraind, worden nieuwe woorden in de aanpassings tekst herkend. 

Zie [een taal model aanpassen met video indexer](customize-language-model-overview.md)voor een gedetailleerd overzicht en aanbevolen procedures voor aangepaste taal modellen.

U kunt de Video Indexer-website gebruiken om aangepaste taal modellen in uw account te maken en te bewerken, zoals wordt beschreven in dit onderwerp. U kunt ook de API gebruiken, zoals beschreven in [taal model aanpassen met behulp van api's](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Een taal model maken

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
2. Als u een model in uw account wilt aanpassen, klikt u op de knop **aanpassing van inhouds model** in de rechter bovenhoek van de pagina.

   ![Inhouds model aanpassen](./media/content-model-customization/content-model-customization.png)

3. Selecteer het tabblad **taal** .

    U ziet een lijst met ondersteunde talen. 

    ![Taal model aanpassen](./media/customize-language-model/customize-language-model.png)

4. Klik onder de gewenste taal op **model toevoegen**.
5. Typ de naam voor het taal model en druk op ENTER.

    Hiermee maakt u het model en biedt de optie om tekst bestanden naar het model te uploaden.
6. Klik op **bestand toevoegen**om een tekst bestand toe te voegen. Hiermee opent u de bestanden Verkenner.

7. Navigeer naar en selecteer het tekst bestand. U kunt meerdere tekst bestanden toevoegen aan een taal model.

    U kunt ook een tekst bestand toevoegen door te klikken op de knop **...** aan de rechter kant van het taal model en vervolgens **bestand toevoegen**te selecteren.
8. Wanneer u klaar bent met het uploaden van de tekst bestanden, klikt u op de groene **trein** optie.

    ![Model voor Train taal](./media/customize-language-model/train-model.png)

Het trainings proces kan een paar minuten duren. Zodra de training is voltooid, ziet u **getraind** naast het model. U kunt het bestand bekijken, downloaden en verwijderen uit het model.

![Getraind taal model](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Een taal model gebruiken in een nieuwe video

Ga op een van de volgende manieren te werk om uw taal model te gebruiken in een nieuwe video:

* Klik op de knop **uploaden** boven aan de pagina 

    ![Uploaden](./media/customize-language-model/upload.png)
* Uw audio-of video bestand in de cirkel neerzetten of naar uw bestand bladeren

    ![Uploaden](./media/customize-language-model/upload2.png)

Hiermee krijgt u de mogelijkheid om de taal van de **video bron**te selecteren. Klik op de vervolg keuzelijst en selecteer een taal model dat u hebt gemaakt in de lijst. U moet de taal van uw taal model en de naam die u hebt opgegeven tussen haakjes zeggen.

Klik onder aan de pagina op de optie **uploaden** en uw nieuwe video wordt geïndexeerd met uw taal model.

### <a name="using-a-language-model-to-reindex"></a>Een taal model gebruiken om opnieuw te indexeren

Als u uw taal model wilt gebruiken om een video in uw verzameling opnieuw te indexeren, gaat u naar uw **account Video's** op de start pagina van [video indexer](https://www.videoindexer.ai/) en houdt u de muis aanwijzer over de naam van de video die u opnieuw wilt indexeren.

U ziet opties voor het bewerken van uw video, het verwijderen van de video en het opnieuw indexeren van uw video. Klik op de optie om uw video opnieuw te indexeren.

![REINDEX](./media/customize-language-model/reindex1.png)

Dit geeft u de mogelijkheid om de taal van de **video bron** te selecteren waarmee u uw video opnieuw wilt indexeren. Klik op de vervolg keuzelijst en selecteer een taal model dat u hebt gemaakt in de lijst. U moet de taal van uw taal model en de naam die u hebt opgegeven tussen haakjes zeggen.

![REINDEX](./media/customize-language-model/reindex.png)

Klik op de knop **opnieuw indexeren** en uw video wordt opnieuw geïndexeerd met behulp van het taal model.

## <a name="edit-a-language-model"></a>Een taal model bewerken

U kunt een taal model bewerken door de naam ervan te wijzigen, bestanden toe te voegen en er bestanden van te verwijderen.

Als u bestanden uit het taal model toevoegt of verwijdert, moet u het model opnieuw trainen door te klikken op de groene **trein** optie.

### <a name="rename-the-language-model"></a>De naam van het taal model wijzigen

U kunt de naam van het taal model wijzigen door op **...** aan de rechter kant van het taal model te klikken en **naam wijzigen**te selecteren. 

Typ de nieuwe naam en druk op ENTER.

### <a name="add-files"></a>Bestanden toevoegen

Klik op **bestand toevoegen**om een tekst bestand toe te voegen. Hiermee opent u de bestanden Verkenner.

Navigeer naar en selecteer het tekst bestand. U kunt meerdere tekst bestanden toevoegen aan een taal model.

U kunt ook een tekst bestand toevoegen door te klikken op de knop **...** aan de rechter kant van het taal model en vervolgens **bestand toevoegen**te selecteren.

### <a name="delete-files"></a>Bestanden verwijderen

Als u een bestand uit het taal model wilt verwijderen, klikt u op de knop **...** aan de rechter kant van het tekst bestand en selecteert u **verwijderen**. Hiermee wordt een nieuw venster geopend waarin staat dat het verwijderen niet ongedaan kan worden gemaakt. Klik op de optie **verwijderen** in het nieuwe venster.

Met deze actie verwijdert u het bestand volledig uit het taal model.

## <a name="delete-a-language-model"></a>Een taal model verwijderen

Als u een taal model uit uw account wilt verwijderen, klikt u op de knop **..** . aan de rechter kant van het taal model en selecteert u **verwijderen**.

Hiermee wordt een nieuw venster geopend waarin staat dat het verwijderen niet ongedaan kan worden gemaakt. Klik op de optie **verwijderen** in het nieuwe venster.

Met deze actie wordt het taal model volledig verwijderd uit uw account. Alle Video's die het verwijderde taal model gebruiken, blijven dezelfde index totdat u de video opnieuw indexeert. Als u de video opnieuw indexeert, kunt u een nieuw taal model toewijzen aan de video. Anders wordt het standaard model van Video Indexer gebruikt om de video opnieuw te indexeren. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Taal modellen aanpassen door transcripten te corrigeren

Video Indexer ondersteunt automatische aanpassing van taal modellen op basis van de daad werkelijke correcties die gebruikers aanbrengen in de transcripties van hun Video's.

1. Als u correcties wilt aanbrengen in een transcript, opent u de video die u wilt bewerken vanuit uw account Video's. Selecteer het tabblad **tijd lijn** .

    ![Taal model aanpassen](./media/customize-language-model/timeline.png)
1. Klik op het potlood pictogram om de transcriptie van uw transcriptie te bewerken. 

    ![Taal model aanpassen](./media/customize-language-model/edits.png)

    Video Indexer alle regels die door u zijn gecorrigeerd, worden vastgelegd in de transcriptie van uw video en worden ze automatisch toegevoegd aan een tekst bestand met de naam ' van transcripten bewerken '. Deze bewerkingen worden gebruikt voor het opnieuw trainen van het specifieke taal model dat is gebruikt voor het indexeren van deze video. 
    
    Als u tijdens het indexeren van deze video geen taal model hebt opgegeven, worden alle bewerkingen voor deze video opgeslagen in een standaard taal model met de naam account aanpassingen binnen de gedetecteerde taal van de video. 
    
    Als er meerdere bewerkingen op dezelfde regel zijn aangebracht, wordt alleen de laatste versie van de gecorrigeerde regel gebruikt voor het bijwerken van het taal model.  
    
    > [!NOTE]
    > Voor de aanpassing worden alleen tekstuele correcties gebruikt. Dit betekent dat correcties die geen werkelijke woorden (bijvoorbeeld lees tekens of spaties) bevatten, niet zijn opgenomen. 
    
1. Er worden afschrift correcties weer gegeven op het tabblad taal van de pagina aanpassing van het inhouds model.

    ![Taal model aanpassen](./media/customize-language-model/customize.png)

   Als u wilt kijken naar het bestand ' van transcripten bewerken ' voor elk van uw taal modellen, klikt u erop om het te openen. 

    ![Van transcript bewerkingen](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Volgende stappen

[Taal model aanpassen met behulp van Api's](customize-language-model-with-api.md)
