---
title: Verbinding maken met een kennis archief (preview) met Power BI
titleSuffix: Azure Cognitive Search
description: Verbind een Azure Cognitive Search Knowledge Store (preview) met Power BI voor analyse en onderzoek.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: e67bd688d720ed3d7549a86c9cff752bc19fb2fc
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858814"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Verbinding maken met een kennis archief met Power BI

> [!IMPORTANT] 
> Het kennis archief is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt preview-functies. Er is momenteel beperkte ondersteuning voor portals en geen .NET SDK-ondersteuning.

In dit artikel leert u hoe u verbinding kunt maken met een kennis archief en hoe u deze kunt verkennen met behulp van Power Query in de Power BI Desktop-app. U kunt sneller aan de slag met sjablonen of een aangepast dash board maken. In deze korte video hieronder ziet u hoe u uw ervaring met uw gegevens kunt verrijken met behulp van Azure Cognitive Search in combi natie met Power BI.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ Volg de stappen in [een kennis archief maken in de Azure Portal](knowledge-store-create-portal.md) of [maak een Azure Cognitive Search Knowledge Store door rest te gebruiken](knowledge-store-create-rest.md) om het voor beeld van het kennis archief te maken dat wordt gebruikt in dit overzicht. U hebt ook de naam nodig van het Azure Storage-account dat u hebt gebruikt om het kennis archief te maken, samen met de toegangs sleutel van de Azure Portal.

+ [Installer Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Voor beeld-Power BI sjabloon-alleen Azure Portal

Wanneer u een [kennis archief maakt met behulp van de Azure Portal](knowledge-store-create-portal.md), kunt u een [Power bi sjabloon](https://github.com/Azure-Samples/cognitive-search-templates) downloaden op de tweede pagina van de wizard **gegevens importeren** . Met deze sjabloon krijgt u verschillende visualisaties, zoals WordCloud en Network Navigator, voor inhoud op basis van tekst. 

Klik op **Power bi sjabloon ophalen** op de pagina **cognitieve vaardig heden toevoegen** om de sjabloon op te halen en te downloaden van de open bare github locatie. De wizard wijzigt de sjabloon om de vorm van uw gegevens aan te passen, zoals vastgelegd in de Knowledge Store-projecties die zijn opgegeven in de wizard. Daarom is de sjabloon die u downloadt, afhankelijk van elke keer dat u de wizard uitvoert, ervan uitgaande dat er verschillende gegevens invoer en vaardigheids selecties zijn.

![Voor beeld van Azure Cognitive Search Power BI sjabloon](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Voor beeld Power BI sjabloon")

> [!NOTE]
> Hoewel de sjabloon wordt gedownload terwijl de wizard halverwege de vlucht is, moet u wachten totdat het kennis archief daad werkelijk is gemaakt in azure Table Storage voordat u het kunt gebruiken.

## <a name="connect-with-power-bi"></a>Verbinden met Power BI

1. Start Power BI Desktop en klik op **gegevens ophalen**.

1. Selecteer in het venster **gegevens ophalen** de optie **Azure**en selecteer vervolgens **Azure Table Storage**.

1. Klik op **Verbinden**.

1. Voer bij **account naam of-URL**de naam in van uw Azure Storage account (de volledige URL wordt voor u gemaakt).

1. Als u hierom wordt gevraagd, voert u de sleutel voor het opslag account in.

1. Selecteer de tabellen die het Hotel bevatten beoordelingen gegevens die zijn gemaakt door de vorige scenario's. 

   + Voor de portal-walkthrough zijn tabel namen *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases*en *hotelReviewsSsPages*. 
   
   + Voor de REST-instructies zijn tabel namen *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases*en *hotelReviewsSentiment*.

1. Klik op **laden**.

1. Klik op het bovenste lint op **Query's bewerken** om de **Power query editor**te openen.

   ![Power Query openen](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query openen")

1. Selecteer *hotelReviewsSsDocument*en verwijder vervolgens de kolommen *PartitionKey*, *RowKey*en *Time Stamp* . 
   ![Tabellen bewerken](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Tabellen bewerken")

1. Klik op het pictogram met tegengestelde pijlen aan de rechter kant van de tabel om de *inhoud*uit te vouwen. Wanneer de lijst met kolommen wordt weer gegeven, selecteert u alle kolommen en vervolgens selecteert u de kolommen die beginnen met ' meta data '. Klik op **OK** om de geselecteerde kolommen weer te geven.

   ![Tabellen bewerken](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Inhoud uitvouwen")

1. Wijzig het gegevens type voor de volgende kolommen door te klikken op het pictogram ABC-123 linksboven in de kolom.

   + Voor *content. Latitude* en *content. lengte graad*, selecteert u **decimaal getal**.
   + Voor *content. reviews_date* en *content. Reviews_dateAdded*selecteert u **datum en tijd**.

   ![Gegevens typen wijzigen](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Gegevens typen wijzigen")

1. Selecteer *hotelReviewsSsPages*en herhaal stap 9 en 10 om de kolommen te verwijderen en de *inhoud*uit te vouwen.
1. Wijzig het gegevens type voor *content. SentimentScore* naar een **decimaal getal**.
1. Selecteer *hotelReviewsSsKeyPhrases* en herhaal stap 9 en 10 om de kolommen te verwijderen en de *inhoud*uit te vouwen. Er zijn geen wijzigingen in het gegevens type voor deze tabel.

1. Klik op de opdracht balk op **sluiten en Toep assen**.

1. Klik op de tegel model in het navigatie deel venster links en controleer of Power BI relaties tussen alle drie de tabellen weergeeft.

   ![Relaties valideren](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Relaties valideren")

1. Dubbel klik op elke relatie en zorg ervoor dat de **Kruis filter richting** is ingesteld op **beide**.  Hierdoor kunnen uw visuals worden vernieuwd wanneer een filter wordt toegepast.

1. Klik op de tegel rapport in het navigatie deel venster aan de linkerkant om gegevens te verkennen met behulp van visualisaties. Voor tekst velden zijn tabellen en kaarten nuttige visualisaties. U kunt de velden in elk van de drie tabellen kiezen om de tabel of de kaart op te vullen. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Opruimen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over het verkennen van dit kennis archief met behulp van Storage Explorer.

> [!div class="nextstepaction"]
> [Weergeven met Storage Explorer](knowledge-store-view-storage-explorer.md)