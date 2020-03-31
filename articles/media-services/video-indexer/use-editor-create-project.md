---
title: De video-indexereditor gebruiken om projecten te maken
titleSuffix: Azure Media Services
description: In dit onderwerp wordt uitgelegd hoe u de video-indexereditor gebruiken om projecten te maken.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839168"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>De video-indexereditor gebruiken om projecten te maken

Video Indexer website, stelt u in staat om de diepgaande inzichten van uw video's te gebruiken om: vind de juiste media-inhoud, zoek de onderdelen waarin u geïnteresseerd bent, en gebruik de resultaten om een geheel nieuw project te maken. Eenmaal gemaakt, kan het project worden gerenderd en gedownload van Video Indexer en worden gebruikt in uw eigen bewerkingstoepassingen of downstream workflows.

Sommige scenario's waarin u deze functie nuttig vindt, zijn: 

* Filmhoogtepunten maken voor trailers.
* Met behulp van oude clips van video's in nieuwscasts.
* Het creëren van kortere inhoud voor sociale media.

In dit artikel ziet u hoe u een project helemaal opnieuw maken en hoe u een project maakt op basis van een video in uw account.

## <a name="create-new-project-and-manage-videos"></a>Nieuw project maken en video's beheren

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
1. Selecteer het tabblad **Projecten.** Als u al eerder projecten hebt gemaakt, ziet u al uw andere projecten hier.
1. Klik **op Nieuw project maken**.  

    ![Nieuw project](./media/video-indexer-view-edit/new-project.png)
1. Geef uw project een naam door op het potloodpictogram te klikken. Vervang de tekst met de tekst 'Zonder titel project' door uw projectnaam en klik op de cheque.

    ![Nieuw project](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Video's toevoegen aan het project

> [!NOTE]
> Momenteel mogen projecten alleen video's bevatten die in dezelfde taal zijn geïndexeerd. Zodra je een video in één taal hebt geselecteerd, kun je de video's in je account niet meer toevoegen die in een andere taal zijn.

1. Voeg video's toe waarmee u in dit project wilt werken door **video's toevoegen**te selecteren.

    U ziet alle video's in uw account en een zoekvak met de tekst 'Zoeken naar tekst, zoekwoorden of visuele inhoud'. Zoeken naar video's met een opgegeven persoon, label, merk, trefwoord of gebeurtenis in het transcript en OCR.
    
    In de onderstaande afbeelding zijn we bijvoorbeeld op zoek naar video's met vermelding van "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    U uw resultaten verder filteren door **Filterresultaten te**selecteren. U filteren om video's weer te geven die een bepaalde persoon in zich hebben of om op te geven dat u alleen videoresultaten wilt zien die in een bepaalde taal zijn of een specifieke eigenaar hebben. <br/> U ook het bereik van uw query opgeven. Als u bijvoorbeeld in de OCR op 'GitHub' wilt zoeken, selecteert u **Visuele tekst**.

    ![Filteren](./media/video-indexer-view-edit/visual-text.png)

    U meerdere filters aan uw query laag. Gebruik **+** / **-** de knoppen om filters toe te voegen/verwijderen. Gebruik **Filters wissen** om alle filters te verwijderen.
1. Als u video's wilt toevoegen, selecteert u ze en selecteert u **Toevoegen**.
1. Nu zie je alle video's die je hebt gekozen. Dit zijn de video's waaruit je clips gaat selecteren voor je project.

    U de volgorde van de video's opnieuw rangschikken door te slepen en te laten vallen of door de knop lijstmenu te selecteren en **Omlaag** of **Omhoog gaan te**selecteren. In het lijstmenu u de video ook uit dit project verwijderen. 

    ![Herschikken](./media/video-indexer-view-edit/rearrange.png)
    
    Je hebt de mogelijkheid om op elk gewenst moment meer video's aan dit project toe te voegen door **Video's toevoegen te**selecteren. U ook meerdere exemplaren van dezelfde video aan uw project toevoegen. U dit doen als u een clip van de ene video wilt weergeven en vervolgens een clip van een andere en vervolgens een andere clip uit de eerste video. 

### <a name="select-clips-to-use-in-your-project"></a>Clips selecteren die u in uw project wilt gebruiken

Als u op de neerwaartse pijl aan de rechterkant van elke video klikt, opent u de inzichten in de video op basis van tijdstempels (clips van de video). 

1. Selecteer **Insights weergeven** om aan te passen welke inzichten u wilt zien en welke u niet wilt zien. 

    ![Inzichten weergeven](./media/video-indexer-view-edit/insights.png)
1. Als u query's voor specifieke clips wilt maken, gebruikt u het zoekvak met de tekst 'Zoeken in transcriptie, visuele tekst, personen en labels'.
1. Voeg filters toe om meer details op te geven over welke scènes u zoekt door **Filteropties te**selecteren.

    ![Filteropties](./media/video-indexer-view-edit/filter-options.png)

    U bijvoorbeeld clips zien waarin GitHub wordt genoemd terwijl Donovan Brown op het scherm staat. Hiervoor moet u een filter 'opnemen' toevoegen met 'Personen' als het type inzicht. U moet dan "Donovan Brown" in het zoekvak naar het filter typen.
    
    ![Opnemen](./media/video-indexer-view-edit/include.png)
    
    Als u clips wilt waar GitHub wordt genoemd terwijl Donovan Brown _niet_ op het scherm is, zou u gewoon het filter "opnemen" wijzigen in een "uitsluiten" filter met behulp van de vervolgkeuzelijst. 

1. Voeg een clip toe aan uw project door het segment te selecteren dat u wilt toevoegen. U de selectie van deze clip opheffen door opnieuw op het segment te klikken.
    
    Voeg alle segmenten van een video toe door op de optie lijstmenu naast de video te klikken en **Alle segmenten selecteren**te selecteren . 

    ![Alles toevoegen](./media/video-indexer-view-edit/add-all.png)

    U al uw selectie wissen door Duidelijke selectie te selecteren.

> [!TIP]
> Terwijl u uw clips selecteert en bestelt, u een voorbeeld van de video bekijken in de speler aan de rechterkant van de pagina. 

![Preview](./media/video-indexer-view-edit/preview.png)

Vergeet niet uw project op te slaan wanneer u wijzigingen aanbrengt door **Project opslaan te**selecteren . 

### <a name="render-and-download-the-project"></a>Het project renderen en downloaden

> [!NOTE]
> Voor betaalde accounts van Video Indexer heeft het renderen van uw project coderingskosten. Video Indexer trial accounts zijn beperkt tot 5 uur rendering.

1. Zodra u klaar bent, moet u ervoor zorgen dat uw project is opgeslagen. U dit project nu weergeven. Selecteer **Renderen en downloaden**. 

    ![Opslaan](./media/video-indexer-view-edit/save.png)

    Er zal een pop-up zijn die je vertelt dat video-indexer een bestand zal renderen en vervolgens de downloadlink naar je e-mail wordt verzonden. Selecteer Doorgaan. 
    
    U ziet ook een melding dat het project boven op de pagina wordt weergegeven. Zodra het is voltooid wordt weergegeven, ziet u een nieuwe melding dat het project is weergegeven. Klik op de melding om het project te downloaden. Het zal het project downloaden in mp4-formaat.

    ![Rendering gedaan](./media/video-indexer-view-edit/rendering-done.png)

1. U hebt toegang tot opgeslagen projecten via het tabblad **Projecten.** 

    Als u dit project selecteert, ziet u alle inzichten en de tijdlijn van dit project. Als u **Video-editor**selecteert, u doorgaan met het bewerken van dit project. Bewerkingen omvatten het toevoegen of verwijderen van video's en clips of het hernoemen van de naam van het project.

    ![Video-editor](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Een project maken op basis van uw video

U een nieuw project rechtstreeks maken vanuit een video in uw account. 

1. Ga naar het tabblad **Bibliotheek** van de website Video-indexer.
1. Open de video die u wilt gebruiken om uw project te maken. Selecteer op de pagina Inzichten en tijdlijn de knop **Videoeditor.**

    Dit brengt u naar dezelfde pagina die u hebt gebruikt om een nieuw project te maken. In tegenstelling tot het nieuwe project ziet u de segmenten van de tijdstempel inzicht van de video, die u eerder was begonnen met bewerken.

## <a name="see-also"></a>Zie ook

[Overzicht van Video Indexer](video-indexer-overview.md)

