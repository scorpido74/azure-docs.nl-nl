---
title: Taalmodel aanpassen met website video-indexer
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een taalmodel met de website Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128076"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Een taalmodel aanpassen met de website Video-indexer

Met Video Indexer u aangepaste taalmodellen maken om spraakherkenning aan te passen door aanpassingstekst te uploaden, namelijk tekst uit het domein waarvan u de woordenschat wilt waaraan de engine zich wilt aanpassen. Zodra u uw model traint, worden nieuwe woorden in de aanpassingstekst herkend.

Zie [Een taalmodel aanpassen met videoindexer](customize-language-model-overview.md)voor een gedetailleerd overzicht en aanbevolen procedures voor aangepaste taalmodellen.

U de website Video Indexer gebruiken om aangepaste taalmodellen in uw account te maken en te bewerken, zoals beschreven in dit onderwerp. U de API ook gebruiken, zoals beschreven in [het taalmodel aanpassen met API's](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Een taalmodel maken

1. Ga naar de [website van Video Indexer](https://www.videoindexer.ai/) en meld u aan.
2. Als u een model in uw account wilt aanpassen, selecteert u de knop **Inhoudsmodel aanpassen** in de rechterbovenhoek van de pagina.

   ![Inhoudsmodel aanpassen in videoindexer](./media/content-model-customization/content-model-customization.png)

3. Selecteer het tabblad **Taal.**

    U ziet een lijst met ondersteunde talen.

    ![Lijst met talenmodellen in Video Indexer](./media/customize-language-model/customize-language-model.png)

4. Selecteer **model toevoegen**onder de gewenste taal .
5. Typ de naam voor het taalmodel en druk op enter.

    Met deze stap wordt het model gemaakt en u tekstbestanden uploaden naar het model.

6. Als u een tekstbestand wilt toevoegen, selecteert u **Bestand toevoegen**. De verkenner wordt geopend.

7. Navigeer naar en selecteer het tekstbestand. U meerdere tekstbestanden toevoegen aan een taalmodel.

    U ook een tekstbestand toevoegen door de knop **...** aan de rechterkant van het taalmodel te selecteren en **bestand toevoegen te**selecteren.

8. Zodra u klaar bent met het uploaden van de tekstbestanden, selecteert u de groene **optie Trein.**

    ![Taalmodel trainen in Video Indexer](./media/customize-language-model/train-model.png)

Het trainingsproces kan enkele minuten duren. Zodra de training is gedaan, zie je **Getraind** naast het model. U het bestand uit het model bekijken, downloaden en verwijderen.

![Getraind taalmodel in Video Indexer](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Een taalmodel gebruiken voor een nieuwe video

Voer een van de volgende acties uit om uw taalmodel in een nieuwe video te gebruiken:

* Selecteer de knop **Uploaden** boven aan de pagina.

    ![Video-indexeren van de knop uploaden](./media/customize-language-model/upload.png)

* Zet uw audio- of videobestand in de cirkel of blader naar uw bestand.

    ![Video-indexer voor mediabestand uploaden](./media/customize-language-model/upload2.png)

U krijgt de optie om de **brontaal Video**te selecteren. Selecteer de vervolgkeuzelijst en selecteer een taalmodel dat u in de lijst hebt gemaakt. Het zou de taal van uw taalmodel en de naam moeten zeggen die u het tussen haakjes gaf.

Selecteer de optie **Uploaden** onder aan de pagina en je nieuwe video wordt geïndexeerd met behulp van je taalmodel.

### <a name="using-a-language-model-to-reindex"></a>Een taalmodel gebruiken om opnieuw te indexeren

Als u uw taalmodel wilt gebruiken om een video in uw verzameling opnieuw te indexeren, gaat u naar uw **accountvideo's** op de startpagina [van Video Indexer](https://www.videoindexer.ai/) en houdt u de plaats in van de naam van de video die u opnieuw wilt indexeren.

Je ziet opties om je video te bewerken, je video te verwijderen en je video opnieuw te indexeren. Selecteer de optie om uw video opnieuw te indexeren.

![Reindex met video-indexer](./media/customize-language-model/reindex1.png)

Je krijgt de optie om de **brontaal Video** te selecteren waarmee je je video opnieuw wilt indexeren. Selecteer de vervolgkeuzelijst en selecteer een taalmodel dat u in de lijst hebt gemaakt. Het zou de taal van uw taalmodel en de naam moeten zeggen die u het tussen haakjes gaf.

![Videobrontaal kiezen— Een video opnieuw indexeren met Video-indexer](./media/customize-language-model/reindex.png)

Selecteer de knop **Opnieuw indexeren** en uw video wordt opnieuw geïndexeerd met behulp van uw taalmodel.

## <a name="edit-a-language-model"></a>Een taalmodel bewerken

U een taalmodel bewerken door de naam te wijzigen, er bestanden aan toe te voegen en er bestanden uit te verwijderen.

Als u bestanden toevoegt of verwijdert uit het taalmodel, moet u het model opnieuw trainen door de groene **optie Trein** te selecteren.

### <a name="rename-the-language-model"></a>De naam van het taalmodel wijzigen

U de naam van het taalmodel wijzigen door de knop ellips (**... )** aan de rechterkant van het taalmodel te selecteren en **Naam wijzigen**te selecteren.

Typ de nieuwe naam en druk op enter.

### <a name="add-files"></a>Bestanden toevoegen

Als u een tekstbestand wilt toevoegen, selecteert u **Bestand toevoegen**. De verkenner wordt geopend.

Navigeer naar en selecteer het tekstbestand. U meerdere tekstbestanden toevoegen aan een taalmodel.

U ook een tekstbestand toevoegen door de knop ellips (**...**) aan de rechterkant van het taalmodel te selecteren en **bestand toevoegen te**selecteren.

### <a name="delete-files"></a>Bestanden verwijderen

Als u een bestand uit het taalmodel wilt verwijderen, selecteert u de knop ellips (**...**) aan de rechterkant van het tekstbestand en selecteert **u Verwijderen**. Er verschijnt een nieuw venster waarin staat dat de verwijdering niet ongedaan kan worden gemaakt. Selecteer de optie **Verwijderen** in het nieuwe venster.

Met deze actie wordt het bestand volledig uit het taalmodel verwijderd.

## <a name="delete-a-language-model"></a>Een taalmodel verwijderen

Als u een taalmodel uit uw account wilt verwijderen, selecteert u de knop ellips (**...**) aan de rechterkant van het taalmodel en selecteert **u Verwijderen**.

Er verschijnt een nieuw venster waarin staat dat de verwijdering niet ongedaan kan worden gemaakt. Selecteer de optie **Verwijderen** in het nieuwe venster.

Met deze actie wordt het taalmodel volledig uit uw account verwijderd. Elke video die het verwijderde taalmodel heeft gebruikt, houdt dezelfde index bij totdat u de video opnieuw indexeert. Als u de video opnieuw indexeert, u een nieuw taalmodel aan de video toewijzen. Anders gebruikt Video Indexer het standaardmodel om de video opnieuw te indexeren.

## <a name="customize-language-models-by-correcting-transcripts"></a>Taalmodellen aanpassen door transcripties te corrigeren

Video Indexer ondersteunt automatische aanpassing van taalmodellen op basis van de werkelijke correcties die gebruikers maken in de transcripties van hun video's.

1. Als u correcties wilt aanbrengen in een transcript, opent u de video die u wilt bewerken vanuit uw accountvideo's. Selecteer het tabblad **Tijdlijn.**

    ![Tabblad Tijdlijn van taalmodel aanpassen—Video-indexer](./media/customize-language-model/timeline.png)

1. Selecteer het potloodpictogram om de transcriptie van uw transcriptie te bewerken.

    ![Taalmodel bewerken van taalmodel aanpassen— Video-indexer](./media/customize-language-model/edits.png)

    Video Indexer legt alle regels vast die door u worden gecorrigeerd in de transcriptie van uw video en voegt deze automatisch toe aan een tekstbestand met de naam 'Van transcriptiebewerkingen'. Deze bewerkingen worden gebruikt om het specifieke taalmodel dat is gebruikt om deze video te indexeren, om te scholen.
    
    Als u bij het indexeren van deze video geen taalmodel hebt opgegeven, worden alle bewerkingen voor deze video opgeslagen in een standaard taalmodel met de naam 'Accountaanpassingen' in de gedetecteerde taal van de video.
    
    Als er meerdere bewerkingen op dezelfde regel zijn aangebracht, wordt alleen de laatste versie van de gecorrigeerde regel gebruikt voor het bijwerken van het taalmodel.  
    
    > [!NOTE]
    > Alleen tekstuele correcties worden gebruikt voor de aanpassing. Correcties die geen werkelijke woorden bevatten (bijvoorbeeld leestekens of spaties) worden niet opgenomen.
    
1. U ziet transcriptiecorrecties worden weergegeven op het tabblad Taal van de pagina Inhoudsmodel aanpassen.

    ![Taalmodel aanpassen—Video-indexer](./media/customize-language-model/customize.png)

   Als u het bestand 'Van transcriptiebewerkingen' voor elk van uw taalmodellen wilt bekijken, selecteert u het bestand om het te openen.

    ![Van transcriptiebewerkingen— Video-indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Volgende stappen

[Taalmodel aanpassen met API's](customize-language-model-with-api.md)
