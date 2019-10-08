---
title: Een kennis archief maken in de Azure Portal-Azure Search
description: Maak een Azure Search kennis Archief voor persistentie van verrijkingen van de cognitieve Zoek pijplijn met behulp van de wizard gegevens importeren in de Azure Portal.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fb979a7ff4144694aecad0985c5bce9be2de05bd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265197"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Een Azure Search kennis archief maken in de Azure Portal

> [!Note]
> Het kennis archief is in Preview en mag niet worden gebruikt in de productie omgeving. De [Azure Search rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>

Het kennis archief is een functie in Azure Search die de uitvoer van een AI-verrijkings pijplijn persistent maakt voor latere analyses of andere downstream-verwerking. Een AI-verrijkte pijp lijn accepteert afbeeldings bestanden of ongestructureerde tekst bestanden, indexeert deze met behulp van Azure Search, maakt AI-verrijkingen van Cognitive Services (zoals afbeeldings analyse en natuurlijke taal verwerking) en slaat de resultaten op in een kennis archief in azure opslagpad. U kunt vervolgens hulpprogram ma's als Power BI of Storage Explorer gebruiken om het kennis archief te verkennen.

In dit artikel gebruikt u de wizard gegevens importeren op de Azure Portal om AI-verrijkingen op te nemen, te indexeren en toe te passen op een aantal functionerings gesprekken met hotels. De Hotel beoordelingen worden geïmporteerd in Azure-blog opslag en de resultaten worden opgeslagen als een Knowledge Store in azure Table Storage.

Nadat u het kennis archief hebt gemaakt, kunt u meer informatie over toegang tot dit kennis archief krijgen met behulp van Storage Explorer of Power BI.

## <a name="prerequisites"></a>Vereisten

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service.

+ [Maak een Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) om de voorbeeld gegevens en het kennis archief op te slaan. Uw opslag account moet dezelfde locatie gebruiken (zoals US-WEas uw Azure Search-service en het *account type* moet *StorageV2 (standaard versie v2)* of *opslag (algemeen gebruik v1)* zijn.

## <a name="load-the-data"></a>De gegevens laden

Laadt het het CSV-bestand van het Hotel in Azure Blob-opslag, zodat het toegankelijk is voor een Azure Search indexer en door de AI-verrijkings pijplijn kan worden ingevoerd.

### <a name="create-an-azure-blob-container-with-the-data"></a>Een Azure Blob-container maken met de gegevens

1. [Down load de gegevens voor de Hotel beoordeling die zijn opgeslagen in een CSV-bestand (HotelReviews_Free. CSV)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Deze gegevens zijn afkomstig van Kaggle.com en bevatten feedback van klanten over hotels.
1. [Meld u aan bij de Azure Portal](https://portal.azure.com)en navigeer naar uw Azure Storage-account.
1. [Een BLOB-container maken](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) Als u dit wilt doen, klikt u in de linkernavigatiebalk voor uw opslag account op **blobs**en klikt u vervolgens op **+ container** op de opdracht balk.
1. Voer`hotel-reviews`in voor de nieuwe container **naam**.
1. Selecteer een **openbaar toegangs niveau**. We hebben de standaard waarde gebruikt.
1. Klik op **OK** om de Azure Blob-container te maken.
1. Open de nieuwe `hotels-review` container, klik op **uploaden**en selecteer het bestand **HotelReviews-Free. CSV** dat u in de eerste stap hebt gedownload.

    ![De gegevens uploaden](media/knowledge-store-create-portal/upload-command-bar.png "De Hotels-beoordelingen uploaden")

1. Klik op **uploaden** om het CSV-bestand te importeren in Azure Blob Storage. De nieuwe container wordt weer gegeven.

    ![De Azure Blob-container maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "De Azure Blob-container maken")

### <a name="get-the-azure-storage-account-connection-string"></a>Het Azure Storage-account ophalen connection string

1. Navigeer in de portal naar uw Azure Storage-account.
1. Klik op **toegangs sleutels**in het linkernavigatievenster van de service.
1. Kopieer de *verbindings reeks*onder **sleutel 1**en sla deze op. De teken reeks begint `DefaultEndpointsProtocol=https`met. De naam en sleutel van uw opslag account zijn Inge sloten in de teken reeks. Bewaar deze teken reeks handig. U hebt deze nodig in de volgende stappen.

## <a name="create-and-run-ai-enrichments"></a>AI-verrijkingen maken en uitvoeren

Gebruik de wizard gegevens importeren om het kennis archief te maken. Maakt u een gegevens bron, kiest u verrijkingen, configureert u een kennis archief en een index en voert u uit.

### <a name="start-the-import-data-wizard"></a>De wizard Gegevens importeren starten

1. [Zoek uw zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)op het Azure Portal.

1. Klik op **gegevens importeren** op de opdracht balk om de wizard Importeren te starten.

### <a name="connect-to-your-data-import-data-wizard"></a>Verbinding maken met uw gegevens (wizard gegevens importeren)

In deze stap van de wizard maakt u een gegevens bron van de Azure-Blob met uw Hotels-gegevens.

1. Selecteer in de lijst **gegevens bron** de optie **Azure Blob Storage**.
1. Voer`hotel-reviews-ds`in bij **naam**.
1. Voor de **modus voor parseren**selecteert u **tekst met scheidings tekens**en selecteert u vervolgens de **eerste regel bevat selectie vakje voor koptekst** . Controleer of het **scheidings teken** een komma (,) is.
1. Voer de **verbindings reeks** voor de opslag service in die u in een vorige stap hebt opgeslagen.
1. Voer`hotel-reviews`in bij **container naam**.
1. Klik op **Next: Cognitieve zoek opdracht toevoegen (optioneel**).

      ![Een gegevens bron object maken](media/knowledge-store-create-portal/hotel-reviews-ds.png "Een gegevens bron object maken")

## <a name="add-cognitive-search-import-data-wizard"></a>Cognitieve zoek opdracht toevoegen (wizard gegevens importeren)

In deze wizardstap maakt u een vaardig heden met cognitieve vaardigheids verrijkingen. Met de vaardig heden die in dit voor beeld worden gebruikt, worden sleutel zinnen geëxtraheerd en worden de taal-en sentiment gedetecteerd. Deze verrijkingen worden "geprojecteerd" in een kennis archief als Azure-tabellen.

1. Vouw **Cognitive Services toevoegen**uit. **Gratis (beperkte verrijkingen)** is standaard geselecteerd. U kunt deze resource gebruiken omdat het aantal records in HotelReviews-Free. CSV 19 is en deze gratis resource Maxi maal 20 trans acties per dag toestaat.
1. Vouw **verrijkingen toevoegen**uit.
1. Voer`hotel-reviews-ss`in bij naam van de **kwalificatieset**.
1. Selecteer voor **Bron gegevens veld** * *reviews_text*.
1. Selecteer voor uitgebreid **granulatie niveau**de optie **pagina's (segmenten van 5000 tekens)**
1. Selecteer deze cognitieve vaardig heden:
    + **Sleuteltermen extraheren**
    + **Taal detecteren**
    + **Sentiment detecteren**

      ![Een vaardig heden maken](media/knowledge-store-create-portal/hotel-reviews-ss.png "Een vaardig heden maken")

1. Vouw **verrijkingen in het kennis archief**uit.
1. Voer de **verbindings reeks voor het opslag account** in die u in een vorige stap hebt opgeslagen.
1. Selecteer deze **Azure-tabel projecties**:
    + **Faxdocumenten**
    + **Pagina's**
    + **Sleutel zinnen**

    ![Kennis archief configureren](media/knowledge-store-create-portal/hotel-reviews-ks.png "Kennis archief configureren")

1. Klik op **Next: Doel index**aanpassen.

### <a name="import-data-import-data-wizard"></a>Gegevens importeren (wizard gegevens importeren)

In deze wizardstap gaat u een index configureren voor optionele Zoek opdrachten in volledige tekst. De wizard maakt een voor beeld van de gegevens bron voor het afleiden van velden en gegevens typen. U hoeft alleen de kenmerken voor het gewenste gedrag te selecteren. Als u bijvoorbeeld het kenmerk **ophalenable** krijgt, kan de zoek service een veld waarde Retour neren terwijl **Zoeken in** volledige tekst in het veld wordt ingeschakeld.

1. Voer`hotel-reviews-idx`bij **index naam**in.
1. Voor kenmerken selecteert u de volgende opties:
    + Selecteer **ophalen** mogelijk voor alle velden.
    + Selecteer **filterbaar** en **facetable** voor deze velden: *Sentiment*, *taal*, *woordgroepen*
    + Selecteer **Doorzoek** bare waarden voor deze velden: *plaats*, *naam*, *reviews_text*, *taal*, *woordgroepen*

    De index moet er ongeveer uitzien als de volgende afbeelding. Omdat de lijst lang is, zijn niet alle velden zichtbaar in de afbeelding.

    ![Een index configureren](media/knowledge-store-create-portal/hotel-reviews-idx.png "Een index configureren")

1. Klik op **Next: Maak een Indexeer functie**.

### <a name="create-an-indexer"></a>Een indexeerfunctie maken

In deze wizard stap gaat u een Indexeer functie configureren die de gegevens bron, vaardig heden en de index die u in de vorige wizard hebt gedefinieerd, ophaalt.

1. Voer`hotel-reviews-idxr`in bij **naam**.
1. Voor **schema**, behoud **de standaard instelling**.
1. Klik op **verzenden** om de Indexeer functie uit te voeren. Gegevens extractie, indexering, toepassing van cognitieve vaardig heden gebeurt allemaal in deze stap.

### <a name="monitor-the-notifications-queue-for-status"></a>De meldingen wachtrij voor status controleren

1. Controleer in het Azure Portal het activiteiten logboek voor meldingen op een klikable **Azure Search meldings** status koppeling. Het kan enkele minuten duren voordat de uitvoering is voltooid.

## <a name="next-steps"></a>Volgende stappen

Nu u uw gegevens hebt verrijkt met behulp van cognitieve Services en de resultaten in een kennis archief hebt geprojecteerd, kunt u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

Zie de volgende walkthrough voor meer informatie over het verkennen van dit kennis archief met behulp van Storage Explorer.

> [!div class="nextstepaction"]
> [Weer geven met Storage Explorer](knowledge-store-view-storage-explorer.md)

Zie de volgende walkthrough voor meer informatie over het verbinden van dit kennis archief met Power BI.

> [!div class="nextstepaction"]
> [Verbinden met Power BI](knowledge-store-connect-power-bi.md)

Als u deze oefening wilt herhalen of een andere AI-verrijkings scenario wilt uitproberen, verwijdert u de *idxr* indexer. Als u de Indexeer functie verwijdert, wordt de gratis dagelijkse transactie teller weer ingesteld op nul.
