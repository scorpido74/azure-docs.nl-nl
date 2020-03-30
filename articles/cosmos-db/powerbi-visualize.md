---
title: Power BI-zelfstudie voor Azure Cosmos DB-connector
description: Gebruik deze Power BI-zelfstudie om JSON te importeren, inzichtelijke rapporten te maken en gegevens te visualiseren met behulp van de Azure Cosmos DB- en Power BI-connector.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 1dbdd428a54ebf38c7b880bb9530935c0f748226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69616812"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Azure Cosmos DB-gegevens visualiseren met behulp van de Power BI-connector 

[Power BI](https://powerbi.microsoft.com/) is een online service waar u dashboards en rapporten maken en delen. Power BI Desktop is een bewerkingstool voor rapporten, waarmee u gegevens uit verschillende gegevensbronnen kunt ophalen. Azure Cosmos DB is een van de gegevensbronnen die u kunt gebruiken met Power BI Desktop. U kunt Power BI Desktop met het Azure Cosmos DB-account verbinden door middel van de Azure Cosmos DB Power BI-connector.  Nadat u de Azure Cosmos DB-gegevens naar Power BI hebt geïmporteerd, kun u deze transformeren, rapporten maken en de rapporten publiceren naar Power BI.   

Dit artikel beschrijft de stappen die nodig zijn om een Azure Cosmos DB-account te verbinden met Power BI Desktop. Nadat u verbinding hebt gemaakt, extraheert u de gegevens, zet u de JSON-gegevens om in tabelvorm en publiceert u een rapport naar Power BI.

> [!NOTE]
> De Power BI-connector voor Azure Cosmos DB maakt verbinding met Power BI Desktop. Rapporten die zijn gemaakt in Power BI Desktop kunnen worden gepubliceerd op PowerBI.com. Directe extractie van Azure Cosmos DB-gegevens kan niet worden uitgevoerd vanaf PowerBI.com. 

> [!NOTE]
> Verbinding maken met Azure Cosmos DB met de Power BI-connector wordt momenteel alleen ondersteund voor Azure Cosmos DB SQL API- en Gremlin API-accounts.

## <a name="prerequisites"></a>Vereisten
Controleer voordat u de instructies in deze Power BI-zelfstudie volgt, of u toegang hebt tot de volgende bronnen:

* [Download de nieuwste versie van Power BI Desktop.](https://powerbi.microsoft.com/desktop)

* Download de gegevens van de [voorbeeldvulkaan](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) van GitHub.

* [Maak een Azure Cosmos-databaseaccount](https://azure.microsoft.com/documentation/articles/create-account/) en importeer de vulkaangegevens met behulp van het [azure cosmos DB-hulpprogramma voor gegevensmigratie](import-data.md). Houd bij het importeren van gegevens de volgende instellingen voor de bron en bestemmingen in het hulpprogramma voor gegevensmigratie:

   * **Bronparameters** 

       * **Importeren uit:** JSON-bestand(en)

   * **Doelparameters** 

      * **Verbindingstekenreeks:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Partitiesleutel:** /Land 

      * **Collectiedoorvoer:** 1000 

Als u uw rapporten in PowerBI.com wilt delen, moet u een account hebben in PowerBI.com.  Zie [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)voor meer informatie over Power BI en Power BI Pro.

## <a name="lets-get-started"></a>Laten we aan de slag gaan
In deze tutorial, laten we ons voorstellen dat je een geoloog bestuderen vulkanen over de hele wereld. De vulkaangegevens worden opgeslagen in een Azure Cosmos DB-account en de JSON-documentindeling is als volgt:

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

U haalt de vulkaangegevens op uit het Azure Cosmos DB-account en visualiseert gegevens in een interactief Power BI-rapport.

1. Voer Power BI-bureaublad uit.

2. U **gegevens ophalen,** **recente bronnen**bekijken of **Andere rapporten** rechtstreeks openen vanaf het welkomstscherm. Selecteer de "X" in de rechterbovenhoek om het scherm te sluiten. De **rapportweergave** van Power BI Desktop wordt weergegeven.
   
   ![Power BI-bureaubladrapportweergave - Power BI-connector](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Selecteer het lint **Start** en klik op **Gegevens ophalen**.  Het venster **Gegevens verzamelen** moet worden weergegeven.

4. Klik op **Azure,** selecteer **Azure Cosmos DB (Beta)** en klik op **Verbinden**. 

    ![Power BI-bureaublad gegevens opdoen - Power BI-connector](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Klik op de pagina **Voorvertoningsconnector** op **Doorgaan**. Het venster **Azure Cosmos DB** wordt weergegeven.

6. Geef de URL van het Azure Cosmos DB-account-account op waaruit u de gegevens wilt ophalen, zoals hieronder wordt weergegeven, en klik op **OK**. Als u uw eigen account wilt gebruiken, u de URL ophalen uit het vak URI in het **sleutelsblad** van de Azure-portal. Optioneel u de naam van de database, de verzamelnaam of de navigator gebruiken om de database en verzameling te selecteren om te bepalen waar de gegevens vandaan komen.
   
7. Als u voor de eerste keer verbinding maakt met dit eindpunt, wordt u gevraagd om de accountsleutel. Haal de sleutel voor uw eigen account op uit het vak **Primaire sleutel** in het blad **Alleen-lezensleutels** van de Azure-portal. Voer de juiste sleutel in en klik op **Verbinding maken**.
   
   We raden u aan de alleen-lezen sleutel te gebruiken bij het bouwen van rapporten. Dit voorkomt onnodige blootstelling van de hoofdsleutel aan potentiële beveiligingsrisico's. De alleen-lezen sleutel is beschikbaar via het **sleutelsblad** van de Azure-portal. 
    
8. Wanneer het account is verbonden, wordt het deelvenster **Navigator** weergegeven. De **Navigator** toont een lijst met databases onder het account.

9. Klik en vouw uit op de database waar de gegevens voor het rapport vandaan komen, selecteer **volcanodb** (uw databasenaam kan anders zijn).   

10. Selecteer nu een verzameling die de gegevens bevat om op te halen, selecteer **volcano1** (uw verzamelingsnaam kan anders zijn).
    
    In het deelvenster Voorbeeld wordt een lijst **met recorditems** weergegeven.  Een document wordt weergegeven als **recordtype** in Power BI. Op dezelfde manier is een genest JSON-blok in een document ook een **record**.
    
    ![Power BI-zelfstudie voor Azure Cosmos DB Power BI-connector - Venster Navigator](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Klik **op Bewerken** om de queryeditor in een nieuw venster te starten om de gegevens te transformeren.

## <a name="flattening-and-transforming-json-documents"></a>Json-documenten afvlakken en transformeren
1. Overschakelen naar het venster Power BI Query Editor, waar de kolom **Document** in het middelste deelvenster.
   ![Query-editor in Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Klik op de uitvouwer aan de rechterkant van de **kolomkop Document.**  Het contextmenu met een lijst met velden wordt weergegeven.  Selecteer de velden die u nodig hebt voor uw rapport, bijvoorbeeld vulkaannaam, land, regio, locatie, hoogte, type, status en laatste uitbarsting. Schakel het selectievakje **Oorspronkelijke kolomnaam gebruiken als voorvoegsel uit** en klik op **OK**.
   
    ![Power BI-zelfstudie voor Azure Cosmos DB Power BI-connector - Documenten uitbreiden](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. In het middelste deelvenster wordt een voorbeeld van het resultaat weergegeven met de geselecteerde velden.
   
    ![Power BI-zelfstudie voor Azure Cosmos DB Power BI-connector - Resultaten afvlakken](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. In ons voorbeeld is de eigenschap Locatie een GeoJSON-blok in een document.  Zoals u zien, wordt locatie weergegeven als **recordtype** in Power BI-bureaublad.  
5. Klik op de uitvouwer aan de rechterkant van de kolomkop Document.Locatie.  Het contextmenu met tekst- en coördinatenvelden wordt weergegeven.  Laten we het veld coördinaten selecteren, ervoor zorgen dat **de oorspronkelijke kolomnaam als voorvoegsel** niet is geselecteerd en klik op **OK**.
   
    ![Power BI-zelfstudie voor Azure Cosmos DB Power BI-connector - Locatierecord](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. In het middelste deelvenster wordt nu een coördinatenkolom van **Lijsttype** weergegeven.  Zoals aan het begin van de zelfstudie wordt weergegeven, zijn de GeoJSON-gegevens in deze zelfstudie van punttype met latitude- en lengtewaarden die zijn vastgelegd in de matrix coördinaten.
   
    Het element coördinaten[0] vertegenwoordigt Lengte, terwijl coördinaten[1] Latitude vertegenwoordigt.
    ![Power BI-zelfstudie voor Azure Cosmos DB Power BI-connector - Lijst met coördinaten](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Als u de matrix van coördinaten wilt afvlakken, maakt u een **aangepaste kolom** met de naam LatLong.  Selecteer het lint **Kolom toevoegen** en klik op **Aangepaste kolom**.  Het venster **Aangepaste kolom** wordt weergegeven.
8. Geef een naam op voor de nieuwe kolom, bijvoorbeeld LatLong.
9. Geef vervolgens de aangepaste formule voor de nieuwe kolom op.  Voor ons voorbeeld zullen we de latitude- en lengtewaarden, gescheiden door een komma, zoals `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`hieronder weergegeven, samenbrengen met behulp van de volgende formule: . Klik op **OK**.
   
    Ga voor meer informatie over DAX(Data Analysis Expressions) inclusief DAX-functies naar [DAX Basics in Power BI Desktop.](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics)
   
    ![Power BI-zelfstudie voor Azure Cosmos DB Power BI-connector - Aangepaste kolom toevoegen](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. In het middelste deelvenster worden nu de nieuwe kolommen LatLong weergegeven die zijn gevuld met de waarden.
    
    ![Power BI-zelfstudie voor Azure Cosmos DB Power BI-connector - Aangepaste latLong-kolom](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Als u een fout in de nieuwe kolom ontvangt, controleert u of de toegepaste stappen onder Query-instellingen overeenkomen met de volgende afbeelding:
    
    ![Toegepaste stappen moeten Bron, Navigatie, Uitgevouwen document, Uitgevouwen document.Locatie, Aangepast](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Als uw stappen verschillend zijn, verwijdert u de extra stappen en probeert u de aangepaste kolom opnieuw toe te voegen. 

11. Klik **op Sluiten en toepassen** om het gegevensmodel op te slaan.
    
    ![Power BI-zelfstudie voor Azure Cosmos DB Power BI-connector - Sluiten & toepassen](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>De rapporten samenstellen
In de rapportweergave van Power BI-bureaublad u rapporten maken om gegevens te visualiseren.  U rapporten maken door velden naar het **canvas Rapport** te slepen en te laten vallen.

![Power BI-bureaubladrapportweergave - Power BI-connector](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

In de weergave Rapport vindt u het:

1. Het deelvenster **Velden,** hier ziet u een lijst met gegevensmodellen met velden die u gebruiken voor uw rapporten.
2. Het deelvenster **Visualisaties.** Een rapport kan één of meerdere visualisaties bevatten.  Kies de visuele typen die aan uw behoeften voldoen in het deelvenster **Visualisaties.**
3. Het **canvas rapport,** dit is waar u de visuals voor uw rapport bouwt.
4. De **pagina Rapport.** U meerdere rapportpagina's toevoegen in Power BI Desktop.

Het volgende toont de basisstappen van het maken van een eenvoudig interactief rapport met kaartweergave.

1. Voor ons voorbeeld maken we een kaartweergave met de locatie van elke vulkaan.  Klik in het deelvenster **Visualisaties** op het visuele type Kaart zoals gemarkeerd in de bovenstaande schermafbeelding.  U ziet het visuele type Kaart dat is geschilderd op het **canvas rapport.**  In het deelvenster **Visualisatie** moet ook een set eigenschappen worden weergegeven die betrekking hebben op het visuele type Kaart.
2. Sleep en drop het veld LatLong nu van het deelvenster **Velden** naar de eigenschap **Locatie** in het deelvenster **Visualisaties.**
3. Sleep vervolgens het veld Vulkaannaam naar de eigenschap **Legend.**  
4. Sleep en plaats het veld Hoogte vervolgens naar de eigenschap **Grootte.**  
5. U zou nu het visuele Overzicht moeten zien dat een reeks bellen toont die de plaats van elke vulkaan met de grootte van de bel aanwijzen die aan de verhoging van de vulkaan correleert.
6. U hebt nu een basisrapport gemaakt.  U het rapport verder aanpassen door meer visualisaties toe te voegen.  In ons geval hebben we een Volcano Type snijmachine toegevoegd om het rapport interactief te maken.  
   
7. Klik in het menu Bestand op **Opslaan** en sla het bestand op als PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Uw rapport publiceren en delen
Als je je rapport wilt delen, moet je een account hebben in PowerBI.com.

1. Klik op het Power BI-bureaublad op het lint **Start.**
2. Klik op **Publish**.  U wordt gevraagd de gebruikersnaam en het wachtwoord voor uw PowerBI.com-account in te voeren.
3. Zodra de referentie is geverifieerd, wordt het rapport gepubliceerd naar uw bestemming die u hebt geselecteerd.
4. Klik **op 'PowerBITutorial.pbix' openen in Power BI** om uw rapport te bekijken en te delen op PowerBI.com.
   
    ![Publiceren naar Power BI Succes! Zelfstudie openen in Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Een dashboard maken op PowerBI.com
Nu u een rapport hebt, u het delen op PowerBI.com

Wanneer u uw rapport publiceert van Power BI Desktop naar PowerBI.com, genereert het een **rapport** en een **gegevensset** in uw PowerBI.com tenant. Nadat u bijvoorbeeld een rapport met de naam **PowerBITutorial** hebt gepubliceerd om PowerBI.com, ziet u PowerBITutorial in zowel de secties **Rapporten** als **Gegevenssets** op PowerBI.com.

   ![Schermafbeelding van het nieuwe rapport en de gegevensset in PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Als u een sharable dashboard wilt maken, klikt u op de knop **Live pagina vastmaken** in uw PowerBI.com rapport.

   ![Schermafbeelding van het nieuwe rapport en de gegevensset in PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Volg vervolgens de instructies in [Een tegel vastmaken uit een rapport](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) om een nieuw dashboard te maken. 

U ook ad hoc wijzigingen aanbrengen om te rapporteren voordat u een dashboard maakt. Het wordt echter aanbevolen om Power BI Desktop te gebruiken om de wijzigingen uit te voeren en het rapport opnieuw te publiceren naar PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Volgende stappen
* Zie Aan de slag [met Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)voor meer informatie over Power BI.
* Zie de [bestemmingspagina azure cosmos DB-documentatie](https://azure.microsoft.com/documentation/services/cosmos-db/)voor meer informatie over Azure Cosmos DB.

