---
title: Verbinding maken met een kennis archief met Power BI-Azure Search
description: Verbind een Azure Search kennis archief met Power BI voor analyse en onderzoek.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fa6187dc270b18f513b5ee4046d0a6c085f9ae12
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963048"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Verbinding maken met een kennis archief met Power BI

> [!Note]
> Het kennis archief is in Preview en mag niet worden gebruikt in de productie omgeving. De [Azure Search rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>
In dit artikel leert u hoe u verbinding maakt en een kennis archief kunt verkennen met behulp van Power Query in de Power BI Desktop-app. Zie [een kennis archief maken in azure Portal](knowledge-store-create-portal.md)voor het maken van het voor beeld van het kennis archief dat in dit scenario wordt gebruikt.

## <a name="prerequisites"></a>Vereisten

+ Volg de stappen in [een kennis archief maken in de Azure Portal](knowledge-store-create-portal.md) om het voor beeld van het kennis archief te maken dat wordt gebruikt in dit overzicht. U hebt ook de naam nodig van het Azure Storage-account dat u hebt gebruikt om het kennis archief te maken, samen met de toegangs sleutel van de Azure Portal.

+ [Power BI Desktop installeren](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>Verbinden met Power BI

1. Start Power BI Desktop en klik op **gegevens ophalen**.

1. Selecteer in het venster **gegevens ophalen** de optie **Azure**en selecteer vervolgens **Azure Table Storage**.

1. Klik op**Verbinden**.

1. Voer bij **account naam of-URL**de naam in van uw Azure Storage account (de volledige URL wordt voor u gemaakt).

1. Als u hierom wordt gevraagd, voert u de sleutel voor het opslag account in.

1. Selecteer de tabellen *hotelReviewsSsDocument*, *hotelReviewsSsKeyPhrases*en *hotelReviewsSsPages* . Deze tabellen zijn Azure Table-projecties van het Hotel bekijkt voorbeeld gegevens en omvatten de verrijkingen van de cognitieve service die waren geselecteerd toen het kennis archief werd gemaakt.

1. Klik op **laden**.

1. Klik op het bovenste lint op **Query's bewerken** om de **Power query editor**te openen.

   ![Power query openen](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power query openen")

1. Selecteer *hotelReviewsSsDocument*en verwijder vervolgens de kolommen *PartitionKey*, *RowKey*en *Time Stamp* . 

   ![Tabellen bewerken](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Tabellen bewerken")

1. Klik op het pictogram met tegengestelde pijlen aan de rechter kant van de tabel om de *inhoud*uit te vouwen. Wanneer de lijst met kolommen wordt weer gegeven, selecteert u alle kolommen en vervolgens selecteert u de kolommen die beginnen met ' meta data '. Klik op **OK** om de geselecteerde kolommen weer te geven.

   ![Tabellen bewerken](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Inhoud uitvouwen")

1. Wijzig het gegevens type voor de volgende kolommen door te klikken op het pictogram ABC-123 linksboven in de kolom.

   + Voor *content. Latitude* en *content. lengte graad*, selecteert u **decimaal getal**.
   + Selecteer **datum/tijd**voor *content. reviews_date* en *content. reviews_dateAdded*.

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

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Search to use larger data sets. 

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
