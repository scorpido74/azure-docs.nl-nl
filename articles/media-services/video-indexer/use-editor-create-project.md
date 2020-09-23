---
title: De Video Indexer editor gebruiken om projecten te maken
titleSuffix: Azure Media Services
description: In dit onderwerp ziet u hoe u de Video Indexer editor gebruikt om projecten te maken.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 5eaf17281fef32d682a2dac17b379faf7ec23a53
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90970089"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>De Video Indexer editor gebruiken om projecten te maken

Met Video Indexer website kunt u uw Video's uitgebreide inzichten gebruiken: Zoek de juiste media-inhoud, zoek de onderdelen die u wilt gebruiken en gebruik de resultaten om een volledig nieuw project te maken. Nadat het project is gemaakt, kan het worden gerenderd en gedownload van Video Indexer en worden gebruikt in uw eigen bewerkings toepassingen of downstream-werk stromen.

Enkele scenario's waarin deze functie nuttig is, zijn: 

* Film hooglichten voor aanhang wagens maken.
* Het gebruik van oude video clips in Nieuws casts.
* Kortere inhoud maken voor sociale media.

In dit artikel wordt beschreven hoe u een volledig nieuw project maakt en hoe u een project maakt op basis van een video in uw account.

## <a name="create-new-project-and-manage-videos"></a>Nieuw project maken en Video's beheren

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
1. Selecteer het tabblad **projecten** . Als u eerder projecten hebt gemaakt, worden hier al uw andere projecten weer gegeven.
1. Klik op **Nieuw project maken**.  

    ![Nieuw project](./media/video-indexer-view-edit/new-project.png)
1. Geef uw project een naam door te klikken op het potlood pictogram. Vervang de tekst "naamloos project" door de naam van uw project en klik op de controle.

    ![Nieuw project](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Video's toevoegen aan het project

> [!NOTE]
> Op dit moment kunnen projecten alleen Video's bevatten die in dezelfde taal zijn geïndexeerd. Zodra u een video in de ene taal selecteert, kunt u de Video's in uw account in een andere taal toevoegen.

1. Voeg Video's toe waarmee u in dit project wilt werken door **Video's toevoegen**te selecteren.

    U ziet alle Video's in uw account en een zoekvak dat "zoeken naar tekst, tref woorden of visuele inhoud" bevat. Zoeken naar Video's met een opgegeven persoon, label, merk, tref woord of voorval in Transcripten en OCR.
    
    In de onderstaande afbeelding zoeken we naar Video's waarin "GitHub" wordt vermeld.
    
    ![Scherm afbeelding toont het zoeken naar Video's waarin GitHub met twee resultaten wordt vermeld.](./media/video-indexer-view-edit/github.png)

    U kunt de resultaten verder filteren door **filter resultaten**te selecteren. U kunt filteren om Video's weer te geven die een bepaalde persoon bevatten of om op te geven dat u alleen video resultaten wilt zien die een in een bepaalde taal of een specifieke eigenaar hebben. <br/> U kunt ook het bereik van uw query opgeven. Als u bijvoorbeeld wilt zoeken in ' GitHub ' in de OCR, selecteert u **visuele tekst**.

    ![Filter](./media/video-indexer-view-edit/visual-text.png)

    U kunt meerdere filters laag met uw query. Gebruik de **+** / **-** knoppen om filters toe te voegen/te verwijderen. Gebruik **Clear filters** om alle filters te verwijderen.
1. Als u Video's wilt toevoegen, selecteert u deze en selecteert u vervolgens **toevoegen**.
1. U ziet nu alle Video's die u hebt gekozen. Dit zijn de Video's waaruit u clips gaat selecteren voor uw project.

    U kunt de volg orde van de Video's wijzigen door te slepen en neer te zetten of door de menu knop lijst te selecteren en **omlaag** of omhoog te **gaan**. In het menu lijst kunt u ook de video uit dit project verwijderen. 

    ![Scherm afbeelding toont Video Indexer met een context menu voor een van de Video's die u wilt verwijderen, het wissen van de selectie of het omlaag verplaatsen.](./media/video-indexer-view-edit/rearrange.png)
    
    U hebt de mogelijkheid om op elk gewenst moment meer Video's toe te voegen aan dit project door **Video's toevoegen**te selecteren. U kunt ook meerdere exemplaren van dezelfde video toevoegen aan uw project. U kunt dit doen als u een clip van de ene video wilt weer geven en vervolgens een clip van een andere clip en vervolgens in de eerste video wilt maken. 

### <a name="select-clips-to-use-in-your-project"></a>Clips selecteren die u in uw project wilt gebruiken

Als u op de pijl-omlaag aan de rechter kant van elke video klikt, wordt de inzichten in de video geopend op basis van tijds tempels (clips van de video). 

1. Selecteer **inzichten weer geven** om aan te passen welke inzichten u wilt zien en welke u niet wilt zien. 

    ![Inzichten weergeven](./media/video-indexer-view-edit/insights.png)
1. Als u query's voor specifieke clips wilt maken, gebruikt u het zoekvak ' zoeken in transcriptie, visuele tekst, personen en labels '.
1. Voeg filters toe om details op te geven van de scènes die u zoekt door **filter opties**te selecteren.

    ![Filteropties](./media/video-indexer-view-edit/filter-options.png)

    U kunt bijvoorbeeld clips zien waarin GitHub wordt genoemd terwijl Donovan bruin op het scherm wordt weer gegeven. Hiervoor moet u een ' include '-filter toevoegen dat "personen" als het type inzicht heeft. Vervolgens typt u ' Donovan Brown ' in het zoekvak voor het filter.
    
    ![Scherm afbeelding toont Video Indexer met personen die zijn geselecteerd voor een insluitings filter.](./media/video-indexer-view-edit/include.png)
    
    Als u clips wilt waar GitHub wordt vermeld terwijl Donovan bruin _niet_ op het scherm staat, wijzigt u het filter ' include ' in het filter ' exclude ' met behulp van de vervolg keuzelijst. 

1. Voeg een clip aan het project toe door het segment te selecteren dat u wilt toevoegen. U kunt de selectie van deze clip opheffen door opnieuw op het segment te klikken.
    
    U kunt alle segmenten van een video toevoegen door te klikken op de menu optie lijst naast de video en **selecteert u alle segmenten selecteren**. 

    ![Alles toevoegen](./media/video-indexer-view-edit/add-all.png)

    U kunt alle selecties wissen door selectie wissen te selecteren.

> [!TIP]
> Wanneer u uw clips selecteert en ordent, kunt u een voor beeld van de video in de speler aan de rechter kant van de pagina bekijken. 

![Scherm afbeelding toont Video Indexer met de preview-versie van een video aan de rechter kant van het venster.](./media/video-indexer-view-edit/preview.png)

Vergeet niet om uw project op te slaan wanneer u wijzigingen aanbrengt door **project opslaan**te selecteren. 

### <a name="render-and-download-the-project"></a>Het project weer geven en downloaden

> [!NOTE]
> Voor Video Indexer betaalde accounts bevat de rendering van het project coderings kosten. Video Indexer proef accounts zijn beperkt tot vijf uur aan de weer gave.

1. Wanneer u klaar bent, controleert u of uw project is opgeslagen. U kunt dit project nu weer geven. Selecteer **renderen en downloaden**. 

    ![Scherm afbeelding toont Video Indexer met de optie om uw project weer te geven en te downloaden.](./media/video-indexer-view-edit/save.png)

    Er wordt een pop-upvenster weer gegeven waarin wordt aangegeven dat video indexer een bestand weergeeft en de download koppeling naar uw e-mail bericht wordt verzonden. Selecteer Doorgaan. 
    
    U ziet ook een melding dat het project boven op de pagina wordt weer gegeven. Zodra de bewerking is uitgevoerd, wordt er een nieuwe melding weer gegeven dat het project is gegenereerd. Klik op de melding om het project te downloaden. Het project wordt gedownload in de indeling MP4.

    ![Rendering voltooid](./media/video-indexer-view-edit/rendering-done.png)

1. U kunt opgeslagen projecten openen via het tabblad **projecten** . 

    Als u dit project selecteert, worden alle inzichten en de tijd lijn van dit project weer geven. Als u **video-editor**selecteert, kunt u door gaan met het maken van wijzigingen aan dit project. Bewerkingen zijn onder andere het toevoegen of verwijderen van Video's en clips of het wijzigen van de naam van het project.

    ![Video-editor](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Een project maken op basis van uw video

U kunt rechtstreeks een nieuw project maken op basis van een video in uw account. 

1. Ga naar het tabblad **bibliotheek** van de video indexer-website.
1. Open de video die u wilt gebruiken om uw project te maken. Selecteer op de pagina inzichten en tijd lijn de knop **video-editor** .

    Hiermee gaat u naar de pagina die u hebt gebruikt om een nieuw project te maken. In tegens telling tot het nieuwe project ziet u de getimede Insights-segmenten van de video, die u eerder hebt bewerkt.

## <a name="see-also"></a>Zie ook

[Overzicht van Video Indexer](video-indexer-overview.md)

