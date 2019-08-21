---
title: Verbinding maken met kennis archief met Power BI-Azure Search
description: Maak een kennis archief met behulp van de wizard gegevens importeren in Azure Portal en maak vervolgens verbinding met Power BI voor analyse en onderzoek.
author: heidisteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 1c7f297092760f2a92f524347a3c1a5e353d0965
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635550"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Een Azure Search Knowledge Store maken en verbinding maken met behulp van Power BI

> [!Note]
> Het kennis archief is in Preview en mag niet worden gebruikt in de productie omgeving. De [Azure Search rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>

Het kennis archief is een functie in Azure Search die de uitvoer van een AI-verrijkings pijplijn persistent maakt voor volgende analyses of andere downstream-verwerking. Een AI-verrijkte pijp lijn accepteert afbeeldings bestanden of ongestructureerde tekst bestanden uit een ondersteunde gegevens bron, stuurt deze naar Azure Search indexering, maakt AI-verrijkingen van Cognitive Services (zoals afbeeldings analyse en natuurlijke taal verwerking) en slaat de resultaten op naar een kennis archief in azure Storage. Vanuit het kennis archief kunt u hulp middelen als Power BI of Storage Explorer koppelen om de resultaten te verkennen.

In dit artikel maakt u een kennis archief in de portal en maakt u vervolgens verbinding en verkennen met behulp van Power Query in Power BI Desktop. 

In dit scenario wordt een gegevensset gebruikt die bestaat uit beoordelingen en beoordelingen van klanten, sentiment Score van Cognitive Services, en vervolgens Power Query om een query uit te stellen op uw documenten. De gegevens zijn afkomstig uit Hotel gegevens op Kaggle.com. 

## <a name="prerequisites"></a>Vereisten

+ [Down load het CSV-bestand met hotels (HotelReviews_Free. CSV)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Deze gegevensset bevat records van feedback van klanten over hotels.

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure Search](search-create-service-portal.md). U kunt voor deze walkthrough een gratis service gebruiken. 

+ [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Zorg ervoor dat het account ' algemeen gebruik ' is, ofwel *StorageV2 (algemeen gebruik v2)* , de standaard waarde of *opslag (algemeen gebruik v1)* . Kies dezelfde regio als Azure Search.
 
## <a name="prepare-data"></a>Gegevens voorbereiden 

Laad het CSV-bestand in Azure Blob Storage zodat het kan worden geopend door een Azure Search indexer.

1. [Meld u aan bij de Azure Portal](https://portal.azure.com), navigeer naar uw Azure Storage-account, klik op blobs en klik vervolgens op **+ container**.

1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) om voorbeeld gegevens te bevatten: 

   1. Geef de container `hotel-reviews`een naam. 
   
   1. Stel het niveau van open bare toegang in op een van de geldige waarden. We hebben de standaard waarde gebruikt.

1. Nadat de container is gemaakt, opent u deze en selecteert u **uploaden** op de opdracht balk.

1. Ga naar de map met het **HotelReviews-Free. CSV**, selecteer het bestand en klik op **uploaden**.

   ![Het CSV-bestand uploaden](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "Het CSV-bestand uploaden")

1. Terwijl u zich in azure Storage bevindt, moet u de naam van connection string en container ophalen.  U hebt beide teken reeksen nodig bij het maken van een gegevens bron object:

   1. Op de pagina overzicht klikt u op **toegangs sleutels** en kopieert u een *Connection String*. Deze begint met `DefaultEndpointsProtocol=https;` en eindigt met. `EndpointSuffix=core.windows.net` De naam en de sleutel van uw account zijn tussen. 

   1. De naam van de container `hotel-reviews` moet of de naam zijn die u hebt toegewezen. 

## <a name="create-and-run-ai-enrichments"></a>AI-verrijkingen maken en uitvoeren

Gebruik de wizard gegevens importeren om het kennis archief te maken. U importeert de gegevensset, kiest verrijkingen, configureert een kennis archief en een index en voert vervolgens uit.

1. Zoek [uw zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) op Azure Portal.

1. Klik op **gegevens importeren** op de opdracht balk.

1. Maak het gegevens bron object op de eerste pagina van de wizard.

   - Selecteer **Azure Blob Storage**.

   - Geef een naam op voor de gegevens bron, zoals *Hotel-Recensies-Active Directory*.

   - Omdat de gegevens een CSV-bestand zijn, selecteert u **tekst met scheidings tekens** voor de parserings modus, selecteert u de **eerste regel bevat header**en zorgt u ervoor dat het scheidings teken een komma is.

   - De verbindings reeks voor uw Azure Storage-account kan worden verkregen in de portal, onder **toegangs toetsen**.

   - Container naam kan ook worden opgehaald uit de portal in de lijst met Azure Storage blobs.

      ![Een gegevens bron object maken](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "Een gegevens bron object maken")

1. U kunt verrijkingen toevoegen en een kennis archief configureren op de tweede pagina van de wizard.

   - In **Cognitive Services koppelen**kunt u de gratis resource gebruiken waarmee Maxi maal 20 trans acties per dag worden toegestaan. Het aantal records in HotelReviews-Free. csv is kleiner dan 20.

   - Geef in verrijkingen **toevoegen**een naam op voor het hotel van de vaardig heden *-beoordelingen-SS*, kies het veld *reviews_text* , kies een granulatie niveau van *pagina's (segmenten van 5000 tekens)* en selecteer deze drie cognitieve vaardig heden: *Sleutel zinnen extra heren*, *taal detecteren*, *sentiment detecteren*.

      ![Een vaardig heden maken](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "Een vaardig heden maken")

   - Geef in verrijkingen in het **kennis archief**de Connection String op voor uw Azure Storage account voor algemeen gebruik. Een kennis archief wordt gemaakt in azure-tabel opslag wanneer u de opties voor *Azure-tabel projecten* selecteert in deze sectie.

      ![Kennis archief configureren](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "Kennis archief configureren")
   
   Klik op **Next: Pas de volgende** stap aan om de doel index aan te passen.

1. Een index configureren voor optionele Zoek query's in volledige tekst in Azure Search. Hoewel de focus van deze walkthrough Power BI verbonden met Azure Table Storage, kan de wizard **gegevens importeren** ook een index maken die wordt gebruikt voor Zoek opdrachten in volledige tekst in azure Search. 

   De wizard maakt voor beelden van de gegevens bron voor het afleiden van velden en gegevens typen. u hoeft alleen maar de benodigde kenmerken te selecteren om het gewenste gedrag te krijgen. Zo kunt u bijvoorbeeld een veld inhoud ophalen uit de-service, terwijl *Doorzoek* bare volledige tekst kan worden gezocht in geselecteerde velden.

   - Geef de index een naam, zoals *Hotel-Recensies-idx*.
   - Stel alle velden in als **ophalen**mogelijk.
   - *Plaats*, *naam*, *reviews_text*, *taal*, *zinsdelen* als doorzoekbaar.
   - Stel *sentiment*, *taal*, *woordgroepen* in als **filterbaar** enbruikbaarheid. 
   
    De index moet er ongeveer uitzien als de volgende afbeelding.

     ![Een index configureren](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "Een index configureren")

   Klik op **Next: Maak een Indexeer functie** om door te gaan naar de volgende stap.

1. Een Indexeer functie configureren door deze een naam en uitvoerings uitgebracht te geven. Voor dit scenario gebruikt u *Hotel-Recensies-idxr* als de naam van de Indexeer functie en gebruikt u het schema van de standaard tijd om de Indexeer functie direct uit te voeren.

   Bij de uitvoering van de Indexeer functie worden alle vorige configuraties in beweging gezet. Voor extra heren, verwerken en opnemen vindt u hier in deze stap.

1. Controleer de indexering in de wachtrij meldingen in de portal. Het duurt enkele minuten voordat de uitvoering is voltooid.

## <a name="connect-with-power-bi"></a>Verbinden met Power BI

1. Start Power BI Desktop en selecteer **gegevens ophalen**.

1. In gegevens ophalen selecteert u **Azure** en vervolgens **Azure Table Storage**. Klik op**Verbinden**.

1. Plak in account naam of-URL de naam van uw Azure Storage account (de volledige URL wordt voor u opgelost).

1. Voer de account sleutel in.

1. Selecteer document, zinsdelen en pagina's. Dit zijn de tabellen die worden gemaakt door de wizard gegevens importeren wanneer u dezelfde benoemde items in de kennis archief configuratie selecteert. Klik op **laden**.

1. Open Power Query door op de opdracht **Query's bewerken** te klikken.

   ![Power query openen](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Power query openen")

1. Voor documenten:

   - Verwijder de PartitionKey-, RowKey-en time stamp-kolommen die zijn gemaakt door Azure Table Storage. Het kennis archief bevat de relaties die in deze analyse worden gebruikt.

   - Vouw de kolom inhoud uit.

     ![Tabellen bewerken](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "Tabellen bewerken")

1. Selecteer alle velden en schakel de selectie vakjes uit met "meta gegevens".

1. Corrigeer het gegevens type voor de volgende kolommen met het pictogram ABC-123 voor elke kolom:

   - Datum kolommen moeten **DateTime** zijn
   - *Breedte graad* moet een **decimaal getal** zijn
   - De *lengte graad* moet een **decimaal getal** zijn

1. Herhaal de vorige stappen voor zinsdelen, het verwijderen van PartitionKey en andere kolommen, het uitbreiden van inhouds kolommen, het instellen van *SentimentScore* op een **decimaal getal**.

1. Herhaal deze stappen voor pagina's, het verwijderen van PartitionKey en andere kolommen, waardoor inhouds kolommen worden uitgebreid. Er zijn geen wijzigingen in het gegevens type voor deze tabel.

1. Klik op **sluiten en Toep assen** helemaal links in de Power query opdracht balk.

1. Controleer of Power BI de relaties herkent die zijn gemaakt in uw gegevens. Klik op de tegel relaties in het navigatie deel venster aan de linkerkant. Alle drie de tabellen moeten gerelateerd zijn. Bewerk de relaties en zorg ervoor dat de Kruis filter richting is ingesteld op beide. Dit zorgt ervoor dat alle visuele elementen worden vernieuwd wanneer een filter wordt toegepast.

   ![Relaties valideren](media/knowledge-store-howto-powerbi/powerbi-relationships.png "Relaties valideren")

## <a name="try-with-larger-data-sets"></a>Probeer met grotere gegevens sets

De gegevensset wordt zo klein bewaard dat er geen kosten in rekening worden gebracht voor een demo-scenario. Voor een realistischere ervaring kunt u een factureer bare Cognitive Services resource maken en vervolgens koppelen om een groter aantal trans acties in te scha kelen op basis van de sentiment Analyzer, extractie van zinsdelen en de taal detector-vaardig heden.

Nieuwe containers maken in Azure Blob-opslag en elk CSV-bestand uploaden naar een eigen container. Geef een van deze containers op in de stap voor het maken van de gegevens bron in de wizard gegevens importeren.

| Description | Koppelen |
|-------------|------|
| Free-laag   | [HotelReviews_Free. CSV](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Kleine (500 records) | [HotelReviews_Small. CSV](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Gemiddeld (6000 records)| [HotelReviews_Medium. CSV](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Groot (volledige gegevensset 35000 records) | [HotelReviews_Large. CSV](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Houd er rekening mee dat zeer grote gegevens sets kostbaar kunnen worden verwerkt. Dit is een kosten van ongeveer $1000 U. S dollar.|

Koppel in de verrijkings stap van de wizard een factureer bare [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, gemaakt in de *s0* -laag, in dezelfde regio als Azure Search om grotere gegevens sets te gebruiken. 

  ![Een Cognitive Services resource maken](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Een Cognitive Services resource maken")

## <a name="next-steps"></a>Volgende stappen

Als u deze oefening wilt herhalen of een andere AI-verrijkings scenario wilt uitvoeren, verwijdert u het *Hotel-Recensies-idx* Indexeer functie dat u zojuist hebt gemaakt. Als u de Indexeer functie verwijdert, wordt de gratis dagelijkse transactie teller weer ingesteld op nul. 

Ga verder met het volgende artikel waarin wordt uitgelegd hoe u een kennis archief maakt met behulp van REST-Api's en postman voor meer stapsgewijze instructies voor het demonstreren van kennis winkels.

> [!div class="nextstepaction"]
> [Aan de slag met kennis archief](knowledge-store-howto.md)
