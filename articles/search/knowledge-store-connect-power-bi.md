---
title: Verbinding maken met een kenniswinkel (preview) met Power BI
titleSuffix: Azure Cognitive Search
description: Verbind een Azure Cognitive Search knowledge store (preview) met Power BI voor analyse en verkenning.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 4fd71a7f322cb2672eb485f17e4de2619a7c2d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270031"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Sluit een kenniswinkel aan met Power BI

> [!IMPORTANT] 
> Knowledge store is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt preview functies. Er is momenteel beperkte portalondersteuning en geen .NET SDK-ondersteuning.

In dit artikel leert u hoe u verbinding maken met een kennisarchief en een kenniswinkel verkennen met Power Query in de Power BI Desktop-app. U sneller aan de slag met sjablonen of een aangepast dashboard maken.

+ Volg de stappen in [Een kennisarchief maken in de Azure-portal](knowledge-store-create-portal.md) of [Maak een Azure Cognitive Search-kennisarchief met REST](knowledge-store-create-rest.md) om de voorbeeldkenniswinkel te maken die in deze walkthrough wordt gebruikt. U hebt ook de naam nodig van het Azure Storage-account dat u hebt gebruikt om de kenniswinkel te maken, samen met de toegangssleutel van de Azure-portal.

+ [Installer Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Voorbeeld van Power BI-sjabloon - alleen Azure-portal

Wanneer u een [kennisarchief maakt met de Azure-portal,](knowledge-store-create-portal.md)u een [Power BI-sjabloon](https://github.com/Azure-Samples/cognitive-search-templates) downloaden op de tweede pagina van de wizard **Gegevens importeren.** Deze sjabloon biedt u verschillende visualisaties, zoals WordCloud en Network Navigator, voor inhoud op basis van tekst. 

Klik **op Power BI-sjabloon ophalen** op de pagina Cognitieve vaardigheden **toevoegen** om de sjabloon op te halen en te downloaden van de openbare GitHub-locatie. De wizard wijzigt de sjabloon om de vorm van uw gegevens aan te passen, zoals vastgelegd in de projecties voor kennisarchiefdie in de wizard zijn opgegeven. Om deze reden varieert de sjabloon die u downloadt elke keer dat u de wizard uitvoert, uitgaande van verschillende gegevensinvoer en vaardigheidsselecties.

![Voorbeeld van Azure Cognitive Search Power BI-sjabloon](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Voorbeeld van Power BI-sjabloon")

> [!NOTE]
> Hoewel de sjabloon wordt gedownload terwijl de wizard zich halverwege de vlucht bevindt, moet u wachten tot de kenniswinkel daadwerkelijk is gemaakt in Azure Table-opslag voordat u deze gebruiken.

## <a name="connect-with-power-bi"></a>Verbinden met Power BI

1. Start Power BI Desktop en klik op **Gegevens opvragen**.

1. Selecteer **Azure**en selecteer **azure-tabelopslag**in het venster **Gegevens opdoen** en selecteer vervolgens Azure Table Storage .

1. Klik op **Verbinden**.

1. Voer **voor accountnaam of URL**de naam van uw Azure Storage-account in (de volledige URL wordt voor u gemaakt).

1. Voer de sleutel van het opslagaccount in als u daarom wordt gevraagd.

1. Selecteer de tabellen met de gegevens van hotelbeoordelingen die zijn gemaakt door de vorige walkthroughs. 

   + Voor de portal walkthrough zijn tabelnamen *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases*en *hotelReviewsSsPages*. 
   
   + Voor de REST walkthrough zijn tafelnamen *hotelReviewsDocument,* *hotelReviewsPages,* *hotelReviewsKeyPhrases*en *hotelReviewsSentiment*.

1. Klik **op Laden**.

1. Klik op het bovenste lint op **Query's bewerken** om de Power Query Editor te **openen.**

   ![Power Query openen](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query openen")

1. Selecteer *hotelReviewsSsDocument*en verwijder vervolgens de kolommen *PartitionKey,* *RowKey*en *Timestamp.* 
   ![Tabellen bewerken](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Tabellen bewerken")

1. Klik op het pictogram met tegengestelde pijlen aan de rechterbovenzijde van de tabel om de *inhoud*uit te vouwen. Wanneer de lijst met kolommen wordt weergegeven, selecteert u alle kolommen en schakelt u kolommen uit die beginnen met 'metagegevens'. Klik op **OK** om de geselecteerde kolommen weer te geven.

   ![Tabellen bewerken](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Inhoud uitbreiden")

1. Wijzig het gegevenstype voor de volgende kolommen door linksboven in de kolom op het pictogram ABC-123 te klikken.

   + Selecteer **Decimalnumber**voor *content.latitude* en *Content.longitude*.
   + Selecteer **Datum/tijd**voor *Content.reviews_date* en *Content.reviews_dateAdded*.

   ![Gegevenstypen wijzigen](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Gegevenstypen wijzigen")

1. Selecteer *hotelReviewsSsPages*en herhaal vervolgens stap 9 en 10 om de kolommen te verwijderen en de *inhoud*uit te vouwen.
1. Wijzig het gegevenstype voor *Content.SentimentScore* in **Decimal Number**.
1. Selecteer *hotelReviewsSsKeyPhrases* en herhaal stap 9 en 10 om de kolommen te verwijderen en de *inhoud*uit te breiden. Er zijn geen wijzigingen in het gegevenstype voor deze tabel.

1. Klik op de opdrachtbalk op **Sluiten en Toepassen**.

1. Klik op de tegel Model in het linkernavigatiedeelvenster en valideer of Power BI relaties tussen alle drie de tabellen weergeeft.

   ![Relaties valideren](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Relaties valideren")

1. Dubbelklik op elke relatie en zorg ervoor dat de **richting Kruisfilter** is ingesteld op **Beide**.  Hierdoor kunnen uw visuals worden vernieuwd wanneer een filter wordt toegepast.

1. Klik op de tegel Rapport in het linkernavigatiedeelvenster om gegevens te verkennen via visualisaties. Voor tekstvelden zijn tabellen en kaarten nuttige visualisaties. U velden kiezen uit elk van de drie tabellen om de tabel of kaart in te vullen. 

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

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over het verkennen van deze kenniswinkel met Storage Explorer.

> [!div class="nextstepaction"]
> [Weergeven met Storage Explorer](knowledge-store-view-storage-explorer.md)