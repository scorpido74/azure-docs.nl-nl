---
title: Verbinding maken met een kennis archief (preview) met Power BI
titleSuffix: Azure Cognitive Search
description: Verbind een Azure Cognitive Search Knowledge Store (preview) met Power BI voor analyse en onderzoek.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: d1e836e0f463d1d2ce2b71d689ed590239cfb607
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406595"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Verbinding maken met een kennis archief met Power BI

> [!IMPORTANT] 
> Het kennis archief is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt preview-functies. Er is momenteel beperkte ondersteuning voor portals en geen .NET SDK-ondersteuning.

In dit artikel leert u hoe u verbinding kunt maken met een kennis archief en hoe u deze kunt verkennen met behulp van Power Query in de Power BI Desktop-app. U kunt sneller aan de slag met sjablonen of een aangepast dash board maken.

+ Volg de stappen in [een kennis archief maken in de Azure Portal](knowledge-store-create-portal.md) of [maak een Azure Cognitive Search Knowledge Store door rest te gebruiken](knowledge-store-create-rest.md) om het voor beeld van het kennis archief te maken dat wordt gebruikt in dit overzicht. U hebt ook de naam nodig van het Azure Storage-account dat u hebt gebruikt om het kennis archief te maken, samen met de toegangs sleutel van de Azure Portal.

+ [Power BI Desktop installeren](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Voor beeld-Power BI sjabloon-alleen Azure Portal

Als u [uw kennis archief hebt gemaakt met behulp van de Azure Portal](knowledge-store-create-portal.md), kunt u het voor beeld van de [Azure Cognitive Search Power bi-sjabloon](https://github.com/Azure-Samples/cognitive-search-templates) gebruiken om Power bi visualisaties weer te geven en te experimenteren. Deze sjabloon kan ook worden gedownload wanneer u de wizard **gegevens importeren** stapsgewijs doorloopt.

De voorbeeld sjabloon voert automatisch de installatie stappen uit die in de rest van dit artikel worden beschreven. Als u echter de REST API hebt gebruikt om uw kennis archief te maken, kunt u de sjabloon overs Laan en de resterende secties in dit artikel gebruiken om uw kennis archief te koppelen aan Power BI. Begin met [verbinden met Power bi](#connect-with-power-bi).

De voorbeeld sjabloon bevat verschillende visualisaties, zoals WordCloud en Network Navigator. Voor sommige visualisaties in de sjabloon, zoals het kaarten overzicht en de entiteit-Graph-viewer, worden geen gegevens weer gegeven voor het voor beeld van het kennis archief dat is gemaakt in [een kennis archief maken in de Azure Portal](knowledge-store-create-portal.md). Dit komt omdat er in de wizard **gegevens importeren** slechts een subset van de AI-verrijkingen beschikbaar is.

![Voor beeld van Azure Cognitive Search Power BI sjabloon](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Voor beeld Power BI sjabloon")

## <a name="connect-with-power-bi"></a>Verbinden met Power BI

1. Start Power BI Desktop en klik op **gegevens ophalen**.

1. Selecteer in het venster **gegevens ophalen** de optie **Azure**en selecteer vervolgens **Azure Table Storage**.

1. Klik op **Verbinding maken**.

1. Voer bij **account naam of-URL**de naam in van uw Azure Storage account (de volledige URL wordt voor u gemaakt).

1. Als u hierom wordt gevraagd, voert u de sleutel voor het opslag account in.

1. Selecteer de tabellen *hotelReviewsSsDocument*, *hotelReviewsSsKeyPhrases*en *hotelReviewsSsPages* . Deze tabellen zijn Azure Table-projecties van het Hotel bekijkt voorbeeld gegevens en bevatten de AI-verrijkingen die zijn geselecteerd toen het kennis archief werd gemaakt.

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

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over het verkennen van dit kennis archief met behulp van Storage Explorer.

> [!div class="nextstepaction"]
> [Weer geven met Storage Explorer](knowledge-store-view-storage-explorer.md)

Zie het volgende artikel voor meer informatie over het maken van een kennis archief met behulp van de REST-Api's en postman.  

> [!div class="nextstepaction"]
> [Een kennis archief maken in REST](knowledge-store-howto.md)
