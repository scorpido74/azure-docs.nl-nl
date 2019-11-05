---
title: Een kennis archief maken in de Azure Portal
titleSuffix: Azure Cognitive Search
description: Gebruik de wizard gegevens importeren om een kennis archief te maken dat wordt gebruikt voor het behouden van verrijkte inhoud. Verbinding maken met een kennis Archief voor analyse van andere apps of verrijkte inhoud verzenden naar downstream-processen.
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 16dd63e22182dedb4af97c2ef6238e6628778467
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485163"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Snelstartgids: een Azure Cognitive Search-kennis archief maken in de Azure Portal

> [!Note]
> Het kennis archief is in Preview en mag niet worden gebruikt in de productie omgeving. Zowel de Azure Portal als de [zoek rest API versie 2019-05-06-preview](search-api-preview.md) bieden deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>

Het kennis archief is een functie van Azure Cognitive Search die de uitvoer van een cognitieve vaardig heden-pijp lijn voor volgende analyses of downstream-verwerking persistent maakt. 

Een pijp lijn accepteert installatie kopieën en ongestructureerde tekst als onbewerkte inhoud, past AI toe op Cognitive Services (zoals afbeeldingen en natuurlijke taal verwerking) en maakt verrijkte inhoud (nieuwe structuren en informatie) als uitvoer. Een van de fysieke artefacten die zijn gemaakt door een pijp lijn is een [kennis archief](knowledge-store-concept-intro.md)dat u kunt openen via hulpprogram ma's voor het analyseren en verkennen van inhoud.

In deze Quick Start combineert u services en gegevens in de Azure-Cloud om een kennis archief te maken. Zodra alles aanwezig is, voert u de wizard **gegevens importeren** in de portal uit om het allemaal samen te halen. Het eind resultaat is oorspronkelijke en AI-gegenereerde inhoud die u kunt weer geven in de portal ([Storage Explorer](knowledge-store-view-storage-explorer.md)).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-services-and-load-data"></a>Services maken en gegevens laden

Deze Snelstartgids maakt gebruik van Azure Cognitive Search, Azure Blob Storage en [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. 

Omdat de werk belasting zo klein is, wordt Cognitive Services achter de schermen getikt om gratis Maxi maal 20 trans acties dagelijks te kunnen uitvoeren wanneer deze vanuit Azure Cognitive Search worden aangeroepen. Zolang u de door u geleverde voorbeeld gegevens gebruikt, kunt u het maken of koppelen van een Cognitive Services resource overs Laan.

1. [Down load HotelReviews_Free. CSV](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Deze gegevens zijn gegevens van een hotel beoordeling die zijn opgeslagen in een CSV-bestand (afkomstig van Kaggle.com) en bevat 19 delen van klanten feedback over één hotel. 

1. [Een Azure Storage-account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) of [een bestaand account vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) onder uw huidige abonnement. U gebruikt Azure Storage voor zowel de onbewerkte inhoud die u wilt importeren als het kennis archief dat het eind resultaat heeft.

   Er zijn twee vereisten voor dit account:

   + Kies dezelfde regio als Azure Cognitive Search. 
   
   + Kies het account type StorageV2 (algemeen gebruik v2). 

1. Open de pagina's van de BLOB Services en maak een container.  

1. Klik op **Uploaden**.

    ![De gegevens uploaden](media/knowledge-store-create-portal/upload-command-bar.png "De Hotels-beoordelingen uploaden")

1. Selecteer het bestand **HotelReviews-Free. CSV** dat u in de eerste stap hebt gedownload.

    ![De Azure Blob-container maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "De Azure Blob-container maken")

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. Get a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

1. [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder hetzelfde abonnement. U kunt een gratis service voor deze Quick Start gebruiken.

U bent nu klaar om door te gaan met de wizard gegevens importeren.

## <a name="run-the-import-data-wizard"></a>De wizard gegevens importeren uitvoeren

Klik op de pagina overzicht van de zoek service op **gegevens importeren** op de opdracht balk om een kennis archief in vier stappen te maken.

  ![Opdracht Gegevens importeren](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken

1. In **Verbinden met uw gegevens** kiest u **Azure Blob Storage** en selecteert u het account dat en de container die u hebt gemaakt. 
1. Voer `hotel-reviews-ds`in voor de **naam**.
1. Voor de **modus voor parseren**selecteert u **tekst met scheidings tekens**en selecteert u vervolgens de **eerste regel bevat selectie vakje voor koptekst** . Controleer of het **scheidings teken** een komma (,) is.
1. Voer de **verbindings reeks** voor de opslag service in die u in een vorige stap hebt opgeslagen.
1. Voer `hotel-reviews`in bij **container naam**.
1. Klik op **volgende: AI-verrijking toevoegen (optioneel)** .

      ![Een gegevens bron object maken](media/knowledge-store-create-portal/hotel-reviews-ds.png "Een gegevens bron object maken")

1. Ga door naar de volgende pagina.

### <a name="step-2-add-cognitive-skills"></a>Stap 2: cognitieve vaardigheden toevoegen

In deze wizardstap maakt u een vaardig heden met cognitieve vaardigheids verrijkingen. Met de vaardig heden die in dit voor beeld worden gebruikt, worden sleutel zinnen geëxtraheerd en worden de taal-en sentiment gedetecteerd. In een latere stap worden deze verrijkingen "geprojecteerd" in een kennis archief als Azure-tabellen.

1. Vouw **Cognitive Services toevoegen**uit. **Gratis (beperkte verrijkingen)** is standaard geselecteerd. U kunt deze resource gebruiken omdat het aantal records in HotelReviews-Free. CSV 19 is en deze gratis resource Maxi maal 20 trans acties per dag toestaat.
1. Vouw **cognitieve vaardig heden toevoegen**uit.
1. Voer `hotel-reviews-ss`in voor de naam van de **vakkennisset**.
1. Selecteer voor **Bron gegevens veld****reviews_text*.
1. Selecteer voor uitgebreid **granulatie niveau**de optie **pagina's (segmenten van 5000 tekens)**
1. Selecteer deze cognitieve vaardig heden:
    + **Sleuteltermen extraheren**
    + **Taal detecteren**
    + **Sentiment detecteren**

      ![Een vaardig heden maken](media/knowledge-store-create-portal/hotel-reviews-ss.png "Een set vaardigheden maken")

1. Vouw **verrijkingen in het kennis archief**uit.
1. Voer de **verbindings reeks voor het opslag account** in die u in een vorige stap hebt opgeslagen.
1. Selecteer deze **Azure-tabel projecties**:
    + **Faxdocumenten**
    + **Pagina's**
    + **Sleutel zinnen**

    ![Kennis archief configureren](media/knowledge-store-create-portal/hotel-reviews-ks.png "Kennis archief configureren")

1. Ga door naar de volgende pagina.

### <a name="step-3-configure-the-index"></a>Stap 3: de index configureren

In deze wizardstap gaat u een index configureren voor optionele Zoek opdrachten in volledige tekst. De wizard maakt een voor beeld van de gegevens bron voor het afleiden van velden en gegevens typen. U hoeft alleen de kenmerken voor het gewenste gedrag te selecteren. Als u bijvoorbeeld het kenmerk **ophalenable** krijgt, kan de zoek service een veld waarde Retour neren terwijl **Zoeken in** volledige tekst in het veld wordt ingeschakeld.

1. Voer `hotel-reviews-idx`in bij **index naam**.
1. Voor kenmerken selecteert u de volgende opties:
    + Selecteer **ophalen** mogelijk voor alle velden.
    + Selecteer **filterbaar** en **facetable** voor deze velden: *sentiment*, *taal*, *woordgroepen*
    + Selecteer **Doorzoek** bare waarden voor deze velden: *plaats*, *naam*, *reviews_text*, *taal*, *woordgroepen*

    De index moet er ongeveer uitzien als de volgende afbeelding. Omdat de lijst lang is, zijn niet alle velden zichtbaar in de afbeelding.

    ![Een index configureren](media/knowledge-store-create-portal/hotel-reviews-idx.png "Een index configureren")

1. Ga door naar de volgende pagina.

### <a name="step-4-configure-the-indexer"></a>Stap 4: de indexeerfunctie configureren

In deze wizard stap gaat u een Indexeer functie configureren die de gegevens bron, vaardig heden en de index die u in de vorige wizard hebt gedefinieerd, ophaalt.

1. Voer bij **naam**`hotel-reviews-idxr`in.
1. Voor **schema**, behoud **de standaard instelling**.
1. Klik op **verzenden** om de Indexeer functie uit te voeren. Gegevens extractie, indexering, toepassing van cognitieve vaardig heden gebeurt allemaal in deze stap.

## <a name="monitor-status"></a>Monitor status

Het indexeren van cognitieve vaardig heden neemt langer af dan typische indexering op basis van tekst. De lijst van de indexeerfunctie moet door de wizard worden geopend op de overzichtspagina, zodat u de voortgang kunt volgen. Voor zelfnavigatie gaat u naar de overzichtspagina en klikt u op **Indexeerfuncties**.

In de Azure Portal kunt u ook het activiteiten logboek van meldingen controleren op een klikable **Azure Cognitive Search meldings** status koppeling. Het kan enkele minuten duren voordat de uitvoering is voltooid.

## <a name="next-steps"></a>Volgende stappen

Nu u uw gegevens hebt verrijkt met Cognitive Services en de resultaten in een kennis archief hebt geprojecteerd, kunt u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

U kunt inhoud in Storage Explorer weer geven of een stap verder nemen met Power BI om inzicht te krijgen via visualisatie.

> [!div class="nextstepaction"]
> [Weer geven met Storage Explorer](knowledge-store-view-storage-explorer.md)
> [verbinding maken met Power bi](knowledge-store-connect-power-bi.md)

> [!Tip]
> Als u deze oefening wilt herhalen of een andere AI-verrijkings scenario wilt uitproberen, verwijdert u de *idxr* indexer. Als u de Indexeer functie verwijdert, wordt de gratis dagelijkse transactie teller weer ingesteld op nul voor Cognitive Services verwerking.
