---
title: Een kennisarchief (voorbeeld) maken in de Azure-portal
titleSuffix: Azure Cognitive Search
description: Gebruik de wizard Gegevens importeren om een kennisarchief te maken dat wordt gebruikt voor aanhoudende verrijkte inhoud. Maak verbinding met een kennisarchief voor analyse van andere apps of stuur verrijkte inhoud naar downstreamprocessen. Deze functie is momenteel beschikbaar als openbare preview-versie.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472363"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Snelstart: een Azure Cognitive Search-kennisarchief maken in de Azure-portal

> [!IMPORTANT] 
> Knowledge store is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. 

Knowledge store is een functie van Azure Cognitive Search die de uitvoer van een pijplijn voor cognitieve vaardigheden voor latere analyses of downstream-verwerking voortduurt. 

Een pijplijn accepteert ongestructureerde tekst en afbeeldingen als ruwe inhoud, past AI toe via Cognitive Services (zoals OCR, beeldanalyse en verwerking van natuurlijke taal), haalt informatie uit en produceert nieuwe structuren en informatie. Een van de fysieke artefacten die door een pijplijn zijn gemaakt, is een [kenniswinkel,](knowledge-store-concept-intro.md)die u openen via tools om inhoud te analyseren en te verkennen.

In deze quickstart combineert u services en gegevens in de Azure-cloud om een kennisarchief te maken. Zodra alles op zijn plaats is, voert u de wizard **Gegevens importeren** in de portal uit om alles bij elkaar te brengen. Het eindresultaat is originele tekstinhoud plus door AI gegenereerde inhoud die u bekijken in de portal[(Storage explorer).](knowledge-store-view-storage-explorer.md)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-services-and-load-data"></a>Services maken en gegevens laden

Deze quickstart maakt gebruik van Azure Cognitive Search, Azure Blob-opslag en [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. 

Omdat de werklast zo klein is, wordt Cognitive Services achter de schermen afgetapt om gratis verwerking te bieden voor maximaal 20 transacties per dag. Omdat de gegevensset zo klein is, u het maken of koppelen van een resource voor Cognitive Services overslaan.

1. [Download HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Deze gegevens zijn hotelbeoordelinggegevens die zijn opgeslagen in een CSV-bestand (afkomstig van Kaggle.com) en bevatten 19 stukjes feedback van klanten over één hotel. 

1. [Maak een Azure-opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) aan of [zoek een bestaand account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) onder uw huidige abonnement. U gebruikt Azure-opslag voor zowel de te importeren ruwe inhoud als de kennisopslag die het eindresultaat is.

   Kies het accounttype **StorageV2 (V2 voor algemeen gebruik).**

1. Open de blobservicespagina's en maak een container met de naam *hotelbeoordelingen.*

1. Klik op **Uploaden**.

    ![De gegevens uploaden](media/knowledge-store-create-portal/upload-command-bar.png "Upload de hotelrecensies")

1. Selecteer het **HotelReviews-Free.csv-bestand** dat u in de eerste stap hebt gedownload.

    ![De Azure Blob-container maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "De Azure Blob-container maken")

1. U bent bijna klaar met deze bron, maar voordat u deze pagina's verlaat, gebruikt u een koppeling in het linkernavigatiedeelvenster om de pagina **Toegangssleutels** te openen. Ontvang een verbindingstekenreeks om gegevens uit blob-opslag op te halen. Een verbindingstekenreeks lijkt op het volgende voorbeeld:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Nog steeds in de portal, overschakelen naar Azure Cognitive Search. [Maak een nieuwe service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U gebruik maken van een gratis service voor deze quickstart.

U bent nu klaar om verder te gaan met de wizard Gegevens importeren.

## <a name="run-the-import-data-wizard"></a>De wizard Gegevens importeren uitvoeren

Klik op de pagina Overzicht van de zoekservice op **Gegevens importeren** op de opdrachtbalk om in vier stappen een kennisarchief te maken.

  ![Opdracht Gegevens importeren](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken

1. In **Verbinden met uw gegevens** kiest u **Azure Blob Storage** en selecteert u het account dat en de container die u hebt gemaakt. 
1. Voer **Name**voor de `hotel-reviews-ds`naam .
1. Schakel voor **de parsing-modus** **De beperkte tekst**in en schakel het selectievakje Eerste regel bevat **koptekst** in. Controleer of het **teken Delimiter** een komma (,) is.
1. Plak in **Verbindingstekenreeks**de verbindingstekenreeks die u hebt gekopieerd van de pagina **Toegangssleutels** in Azure Storage.
1. Voer in **Containers**de naam in van de blobcontainer met de gegevens.

    Uw pagina moet er vergelijkbaar uitzien met de volgende schermafbeelding.

    ![Een gegevensbronobject maken](media/knowledge-store-create-portal/hotel-reviews-ds.png "Een gegevensbronobject maken")

1. Ga door naar de volgende pagina.

### <a name="step-2-add-cognitive-skills"></a>Stap 2: cognitieve vaardigheden toevoegen

In deze wizardstap maakt u een skillset met cognitieve vaardigheidsverrijkingen. De brongegevens bestaan uit klantbeoordelingen in verschillende talen. Vaardigheden die relevant zijn voor deze gegevensset zijn onder andere key phrase extraction, sentiment detection en tekstvertaling. In een latere stap worden deze verrijkingen 'geprojecteerd' in een kennisarchief als Azure-tabellen.

1. Cognitieve **services koppelen**uitbreiden . **Gratis (Beperkte verrijkingen)** is standaard geselecteerd. U deze bron gebruiken omdat het aantal records in HotelReviews-Free.csv 19 is en deze gratis resource maximaal 20 transacties per dag mogelijk maakt.
1. **Verrijkingen toevoegen uitbreiden**.
1. Voer **voor skillset-naam**. `hotel-reviews-ss`
1. Selecteer **reviews_text**voor **het veld Brongegevens**.
1. Selecteer **Pagina's (segmenten van 5000 tekens)** voor **het granulariteitsniveau**van verrijking
1. Selecteer deze cognitieve vaardigheden:
    + **Belangrijke woordgroepen herkennen**
    + **Tekst vertalen**
    + **Sentiment detecteren**

      ![Een set vaardigheden maken](media/knowledge-store-create-portal/hotel-reviews-ss.png "Een set vaardigheden maken")

1. Breid **Verrijkingen opslaan uit in kenniswinkel.**
1. Selecteer deze **Azure-tabelprojecties:**
    + **Documenten**
    + **Pagina's**
    + **Sleutelzinnen**
1. Voer de **verbindingstekenreeks voor opslagaccount in** die u in een vorige stap hebt opgeslagen.

    ![Kennisarchief configureren](media/knowledge-store-create-portal/hotel-reviews-ks.png "Kennisarchief configureren")

1. Download optioneel een Power BI-sjabloon. Wanneer u de sjabloon vanuit de wizard opent, wordt het lokale .pbit-bestand aangepast aan de vorm van uw gegevens.

1. Ga door naar de volgende pagina.

### <a name="step-3-configure-the-index"></a>Stap 3: de index configureren

In deze wizardstap configureert u een index voor optionele zoekopdrachten met volledige tekst. De wizard zal een voorbeeld van uw gegevensbron gebruiken om velden en gegevenstypen af te leiden. U hoeft alleen de kenmerken voor uw gewenste gedrag te selecteren. Met het kenmerk **Opvraagbaar** kan de zoekservice bijvoorbeeld een veldwaarde retourneren, terwijl met **de zoekbare** zoekopdracht en volledige tekst zoeken in het veld wordt ingeschakeld.

1. Voer voor **Indexnaam**. `hotel-reviews-idx`
1. Voor kenmerken accepteert u de standaardselecties: **Opvraagbaar** en **Doorzoekbaar** voor de nieuwe velden die de pijplijn maakt.

    Uw index moet er hetzelfde uitzien als de volgende afbeelding. Omdat de lijst lang is, zijn niet alle velden zichtbaar in de afbeelding.

    ![Een index configureren](media/knowledge-store-create-portal/hotel-reviews-idx.png "Een index configureren")

1. Ga door naar de volgende pagina.

### <a name="step-4-configure-the-indexer"></a>Stap 4: de indexeerfunctie configureren

In deze wizardstap configureert u een indexer die de gegevensbron, vaardigheden en de index die u in de vorige wizardstappen hebt gedefinieerd, samenbrengt.

1. Voer **Name**voor `hotel-reviews-idxr`Naam .
1. Houd **bij Planning**de **standaardeenmaal**.
1. Klik **op Verzenden** om de indexer uit te voeren. Gegevensextractie, indexering, toepassing van cognitieve vaardigheden gebeuren allemaal in deze stap.

## <a name="monitor-status"></a>Monitorstatus

Het indexeren van cognitieve vaardigheden duurt langer om te voltooien dan typische tekstindexering. De lijst van de indexeerfunctie moet door de wizard worden geopend op de overzichtspagina, zodat u de voortgang kunt volgen. Voor zelfnavigatie gaat u naar de overzichtspagina en klikt u op **Indexeerfuncties**.

In de Azure-portal u ook het activiteitslogboek meldingen controleren voor een klikbare statuskoppeling **azure cognitive search.In** the Azure portal, you can also monitor the Notifications activity log for a clickable Azure Cognitive Search notification status link. De uitvoering kan enkele minuten in beslag nemen.

## <a name="next-steps"></a>Volgende stappen

Nu u uw gegevens hebt verrijkt met behulp van Cognitive Services en de resultaten in een kennisarchief hebt geprojecteerd, u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

U inhoud bekijken in Storage Explorer of een stap verder gaan met Power BI om inzichten te verkrijgen door middel van visualisatie.

> [!div class="nextstepaction"]
> [Weergave met Storage Explorer](knowledge-store-view-storage-explorer.md)
> [Verbinding maken met Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Als u deze oefening wilt herhalen of een andere AI-verrijkingswalkthrough wilt proberen, verwijdert u de *hotel-reviews-idxr-indexeerder.* Als u de indexer verwijderen, wordt de gratis dagelijkse transactieteller teruggezet naar nul voor verwerking van Cognitive Services.
