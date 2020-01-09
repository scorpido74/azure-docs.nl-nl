---
title: Gebruik Azure Cosmos DB-wijzigingenfeed voor het visualiseren van realtime gegevensanalyse
description: In dit artikel wordt beschreven hoe wijzigings invoer door een retail bedrijf kan worden gebruikt om gebruikers patronen te begrijpen, analyse en visualisatie in realtime-gegevens uit te voeren
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 50517db6a5bb1fc458ab2f563e905fca34f70cf4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442074"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Gebruik Azure Cosmos DB-wijzigingenfeed voor het visualiseren van realtime gegevensanalyse

De Azure Cosmos DB wijzigings feed is een mechanisme voor het verkrijgen van een doorlopende en incrementele feed van records uit een Azure Cosmos-container wanneer die records worden gemaakt of gewijzigd. Wijzigingenfeed ondersteuning werkt door te luisteren naar de container voor eventuele wijzigingen. Als output verschijnt er vervolgens een gesorteerde lijst met gewijzigde documenten op volgorde van wijziging. Zie voor meer informatie over de wijzigingenfeed [werken met change feed](change-feed.md) artikel. 

Dit artikel wordt beschreven hoe wijzigingenfeed kan worden gebruikt door een e-commerce-bedrijf Gebruikerspatronen begrijpen, voert u gegevens in realtime analyses en visualisatie. U kunt gebeurtenissen, zoals een gebruiker een item weergeven, een item toe te voegen aan hun winkelwagen of aanschaffen van een item wordt analyseren. Wanneer een van deze gebeurtenissen zich voordoet, een nieuwe record wordt gemaakt en de wijzigingenfeed logboeken die record. Wijzigingenfeed vervolgens triggers een reeks stappen, wat resulteert in een visualisatie van metrische gegevens die de bedrijfsprestaties van het en de activiteit analyseert. Voorbeeld van metrische gegevens die u kunt visualiseren omzet, unieke bezoekers, meest populaire artikelen bevatten en gemiddelde prijs van de items die worden weergegeven ten opzichte van toegevoegd aan een winkelwagen ten opzichte van die zijn aangeschaft. Deze metrische voorbeeldgegevens kunt een e-commerce-bedrijf evalueren de populariteit van de site, de reclame- en winsten te ontwikkelen en beslissingen met betrekking tot welke voorraad te investeren in.

Geïnteresseerd in een video over de oplossing voordat u aan de slag, Zie de volgende video:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Oplossingsonderdelen
Het volgende diagram staat voor de gegevensstroom en de onderdelen die betrokken zijn bij de oplossing:

![Project visual](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Gegevens genereren:** gegevenssimulator wordt gebruikt voor het genereren van retailgegevens die gebeurtenissen, zoals een gebruiker een item bekijken, een item toe te voegen aan hun winkelwagen en het aanschaffen van een item vertegenwoordigt. U kunt grote verzameling voorbeeldgegevens genereren met behulp van de gegevensgenerator. De gegenereerde voorbeeldgegevens bevat documenten in de volgende indeling:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** De gegenereerde gegevens worden opgeslagen in een Azure Cosmos-container.  

3. **Wijzigings feed:** De wijzigings feed wordt geluisterd naar wijzigingen in de Azure Cosmos-container. Telkens wanneer een nieuw document wordt toegevoegd aan de verzameling (dat is wanneer een gebeurtenis optreedt die een gebruiker een item bekijkt een item toe te voegen aan hun winkelwagen of aanschaffen van een item), de wijzigingenfeed wordt activeren een [Azure-functie](../azure-functions/functions-overview.md).  

4. **Azure-functie:** de Azure-functie verwerkt de nieuwe gegevens en verzendt ze naar een [Azure Event Hub](../event-hubs/event-hubs-about.md).  

5. **Event Hub:** deze gebeurtenissen worden opgeslagen in de Azure Event Hub en stuurt naar [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) voor verdere analyse.  

6. **Azure Stream Analytics:** Azure Stream Analytics query's uit om de gebeurtenissen verwerken en realtime gegevensanalyses uitvoeren definieert. Deze gegevens wordt dan gezonden naar [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI wordt gebruikt voor het visualiseren van de gegevens die door Azure Stream Analytics worden verzonden. U kunt een dashboard om te zien hoe de metrische gegevens wijzigen in realtime kunt bouwen.  

## <a name="prerequisites"></a>Vereisten

* Microsoft .NET Framework 4.7.1 of hoger

* Microsoft .NET Core 2.1 (of hoger)

* Visual Studio met Universal Windows Platform-ontwikkelings-, .NET-desktopontwikkeling- en ASP.NET- en werkvolumes voor ontwikkeling

* Microsoft Azure-abonnement

* Microsoft Power BI-Account

* Download de [Azure Cosmos DB-wijzigingenfeed lab](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) vanuit GitHub. 

## <a name="create-azure-resources"></a>Azure-resources maken 

De Azure-resources - Azure Cosmos DB, Storage-account maken, Event Hub, Stream Analytics is vereist voor de oplossing. U kunt deze resources via een Azure Resource Manager-sjabloon wilt implementeren. Gebruik de volgende stappen uit om deze resources te implementeren: 

1. De Windows PowerShell-uitvoeringsbeleid ingesteld op **onbeperkt**. Om dit te doen, opent u **Windows PowerShell als beheerder** en voer de volgende opdrachten uit:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Vanuit de GitHub-opslagplaats u in de vorige stap hebt gedownload, navigeert u naar de **Azure Resource Manager** map en open het bestand met de naam **parameters.json** bestand.  

3. Geef waarden op voor cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parameters zoals aangegeven in **parameters.json** bestand. U moet de namen die u toewijst aan elk van uw resources worden later gebruikt.  

4. Van **Windows PowerShell**, gaat u naar de **Azure Resource Manager** map en voer de volgende opdracht:

   ```powershell
   .\deploy.ps1
   ```
5. Wanneer u hierom wordt gevraagd, voert u uw Azure **abonnements-ID**, **changefeedlab** voor de naam van de resourcegroep, en **run1** voor de implementatienaam van de. Zodra de resources die begint met het implementeren, wordt het duurt maximaal tien minuten worden voltooid.

## <a name="create-a-database-and-the-collection"></a>Een database en de verzameling maken

U maakt nu een verzameling voor het opslaan van gebeurtenissen voor e-commerce-site. Wanneer een gebruiker een item bekijkt, wordt een item toegevoegd aan hun winkelwagen of koopt van een item, de verzameling ontvangen een record met de actie ('weergegeven', 'toegevoegd' of 'gekocht'), de naam van het item is betrokken, de prijs van de betrokken item en de ID-nummer van de gebruiker winkelwagen i nvolved.

1. Ga naar [Azure Portal](https://portal.azure.com/) en zoek de **Azure Cosmos DB-Account** die door de sjabloonimplementatie wordt gemaakt.  

2. Uit de **Data Explorer** venster **nieuwe verzameling** en vul het formulier met de volgende details:  

   * Voor de **Database-id** veld **nieuw**, voert u **changefeedlabdatabase**. Laat de **inrichten database doorvoer** selectievakje uitgeschakeld.  
   * Voor de **verzameling** id veld **changefeedlabcollection**.  
   * Voor de **partitiesleutel** veld **/Item**. Dit is hoofdlettergevoelig, dus zorg ervoor dat u deze goed.  
   * Voor de **doorvoer** veld **10000**.  
   * Selecteer de knop **OK**.  

3. Maak vervolgens een andere verzameling met de naam **leases** voor change feed verwerken. De verzameling leases coördineert de verwerking van de feed over meerdere werknemers wijzigen. Een verzameling afzonderlijke wordt gebruikt voor het opslaan van de leases met één lease per partitie.  

4. Ga terug naar de **Data Explorer** deelvenster en selecteer **nieuwe verzameling** en vul het formulier met de volgende details:

   * Voor de **Database-id** veld **gebruik bestaande**, voert u **changefeedlabdatabase**.  
   * Voor de **verzamelings-id** veld **leases**.  
   * Voor **opslagcapaciteit**, selecteer **vaste**.  
   * Laat de **doorvoer** veld ingesteld op de standaardwaarde.  
   * Selecteer de knop **OK**.

## <a name="get-the-connection-string-and-keys"></a>De verbindingsreeks en de sleutels ophalen

### <a name="get-the-azure-cosmos-db-connection-string"></a>De Azure Cosmos DB-verbindingsreeks ophalen

1. Ga naar [Azure Portal](https://portal.azure.com/) en zoek de **Azure Cosmos DB-Account** die door de sjabloonimplementatie wordt gemaakt.  

2. Navigeer naar de **sleutels** deelvenster, Kopieer de PRIMARY CONNECTION STRING en kopieer het naar een Kladblok of een ander document dat u toegang tot in de testomgeving hebt. U moet deze labelen **Cosmos DB-verbindingsreeks**. U moet de tekenreeks die later in uw code kopiëren, dus let en onthouden waar u deze opslaat.

### <a name="get-the-storage-account-key-and-connection-string"></a>De storage-account-sleutel en de verbindingsreeks ophalen

Azure Storage-Accounts kunnen gebruikers gegevens opslaan. In dit lab gebruikt u een opslagaccount voor het opslaan van gegevens die worden gebruikt door de Azure-functie. De Azure-functie wordt geactiveerd wanneer een wijziging wordt aangebracht in de verzameling.

1. Ga terug naar de resourcegroep en open het storage-account dat u eerder hebt gemaakt  

2. Selecteer **toegangssleutels** in het menu aan de linkerkant.  

3. Kopieer de waarden onder **sleutel 1** Kladblok of een ander document dat u toegang tot in de testomgeving hebt. U moet een label de **sleutel** als **opslagsleutel** en de **Connection string** als **Storage Connection String**. U moet deze tekenreeksen later in uw code kopiëren, dus let en Onthoud waar u ze opslaat.  

### <a name="get-the-event-hub-namespace-connection-string"></a>De event hub-naamruimte-verbindingsreeks ophalen

Een Azure Event Hub ontvangt de gebeurtenisgegevens, winkels, processen, en verzendt de gegevens. In dit lab, de Azure Event Hub ontvangt een document telkens wanneer een nieuwe gebeurtenis wordt uitgevoerd (dat wil zeggen een item wordt weergegeven door een gebruiker, toegevoegd aan winkelwagen van een gebruiker of gekocht door een gebruiker) en vervolgens het document wordt doorgestuurd naar Azure Stream Analytics.

1. Ga terug naar de resourcegroep en open de **Event Hub Namespace** die u hebt gemaakt en met de naam in de prelab.  

2. Selecteer **beleid voor gedeelde toegang** in het menu aan de linkerkant.  

3. Selecteer **RootManageSharedAccessKey**. Kopieer de **verbindingsreeks-primaire sleutel** naar Kladblok of een ander document dat u toegang tot in de testomgeving hebt. U moet deze labelen **Event Hub Namespace** verbindingsreeks. U moet de tekenreeks die later in uw code kopiëren, dus let en onthouden waar u deze opslaat.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Azure-functie instellen om te lezen van de wijzigingenfeed

Wanneer een nieuw document wordt gemaakt of een huidig document is gewijzigd in een Cosmos-container, voegt de wijzigings feed het gewijzigde document automatisch toe aan de geschiedenis van verzamelings wijzigingen. U wordt nu ontwikkelen en uitvoeren van een Azure-functie die de wijzigingenfeed verwerkt. Wanneer een document wordt gemaakt of gewijzigd in de verzameling die u hebt gemaakt, wordt de Azure-functie wordt geactiveerd door een wijziging feed. De Azure-functie wordt vervolgens het gewijzigde document verzenden naar de Event Hub.

1. Ga terug naar de opslagplaats die u hebt gekloond op uw apparaat.  

2. Met de rechtermuisknop op het bestand met de naam **ChangeFeedLabSolution.sln** en selecteer **openen met Visual Studio**.  

3. Navigeer naar **local.settings.json** in Visual Studio. Gebruik vervolgens de waarden die u eerder hebt genoteerd om op te vullen in het aantal lege waarden.  

4. Navigeer naar **ChangeFeedProcessor.cs**. In de parameters voor de **uitvoeren** functie, de volgende acties uitvoeren:  

   * Vervang de tekst **hier de naam in uw verzameling** met de naam van uw verzameling. Als u de vorige instructies hebt gevolgd, is de naam van uw verzameling changefeedlabcollection.  
   * Vervang de tekst **uw LEASES verzameling naam hier** met de naam van uw verzameling leases. Als u de vorige instructies hebt gevolgd, is de naam van uw verzameling leases **leases**.  
   * Aan de bovenkant van Visual Studio, zorg ervoor dat het vak Startup Project aan de linkerkant van de groene pijl zegt **ChangeFeedFunction**.  
   * Selecteer **Start** aan de bovenkant van de pagina het programma uit te voeren  
   * U kunt bevestigen dat de functie wordt uitgevoerd wanneer de console-app zegt 'host van de taak is gestart'.

## <a name="insert-data-into-azure-cosmos-db"></a>Gegevens invoegen in Azure Cosmos DB 

Om te zien hoe wijzigingenfeed verwerkt nieuwe acties op een e-commerce-site, hebben voor het simuleren van gegevens die gebruikers bekijken van items uit de productcatalogus, deze items toevoegen aan hun winkelwagens en het aanschaffen van de items in hun winkelwagens vertegenwoordigt. Deze gegevens is willekeurig en voor het repliceren van welke gegevens op een e-commerce site zou er als volgt uitzien.

1. Ga terug naar de opslagplaats in Windows Verkenner en met de rechtermuisknop op **ChangeFeedFunction.sln** opnieuw openen in een nieuw Visual Studio-venster.  

2. Navigeer naar het bestand **app. config** . Voeg binnen het `<appSettings>` blok het eind punt en de unieke **primaire sleutel** toe van uw Azure Cosmos DB account dat u eerder hebt opgehaald.  

3. Voeg de **verzameling** en **database** namen. (Deze namen moeten **changefeedlabcollection** en **changefeedlabdatabase** , tenzij u ervoor kiest om de naam van uw anders.)

   ![Verbindingsreeksen bijwerken](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Sla de wijzigingen op alle bestanden die zijn bewerkt.  

5. Aan de bovenkant van Visual Studio, zorg ervoor dat de **Startup Project** vak aan de linkerkant van de groene pijl zegt **DataGenerator**. Selecteer vervolgens **Start** aan de bovenkant van de pagina het programma uit te voeren.  
 
6. Wacht totdat het programma om uit te voeren. De sterren betekent dat gegevens afkomstig is in! Houd het programma uitvoeren: het is belangrijk dat grote hoeveelheden gegevens worden verzameld.  

7. Als u de navigatiefunctie [Azure Portal](https://portal.azure.com/) , klikt u vervolgens naar de Cosmos DB-account binnen de resourcegroep, klikt u vervolgens aan **Data Explorer**, ziet u de willekeurige gegevens geïmporteerd uw  **changefeedlabcollection** .
 
   ![Gegevens die zijn gegenereerd in de portal](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Instellen van een stream analytics-taak

Azure Stream Analytics is een volledig beheerde cloudservice voor de verwerking van streaminggegevens in realtime. In dit lab gebruikt u stream analytics voor het verwerken van nieuwe gebeurtenissen van de Event Hub (dat wil zeggen wanneer een item wordt weergegeven, toegevoegd aan een winkelwagen of gekocht), nemen deze gebeurtenissen in realtime gegevensanalyse en ze verzenden naar Power BI voor visualisatie.

1. Uit de [Azure Portal](https://portal.azure.com/), gaat u naar de resourcegroep, klikt u vervolgens aan **streamjob1** (de stream analytics-taak die u hebt gemaakt in de prelab).  

2. Selecteer **invoer** zoals hieronder wordt gedemonstreerd.  

   ![Invoer maken](./media/changefeed-ecommerce-solution/create-input.png)

3. Selecteer **+ Stroominvoer toevoegen**. Selecteer vervolgens **Event Hub** uit de vervolgkeuzelijst.  

4. Vul het formulier voor de nieuwe invoer met de volgende details:

   * In de **invoer** alias en voer **invoer**.  
   * Selecteer de optie voor **Event Hub selecteren bij uw abonnementen**.  
   * Stel de **abonnement** veld aan uw abonnement.  
   * In de **Event Hub-naamruimte** en voer de naam van uw Event Hub-Namespace die u hebt gemaakt tijdens de prelab.  
   * In de **Event Hub-naam** veld, selecteert u de optie voor **Use existing** en kies **gebeurtenis hub1** uit de vervolgkeuzelijst.  
   * Laat **Event Hub-beleid** naamveld ingesteld op de standaardwaarde.  
   * Laat **serialisatie-indeling voor gebeurtenissen** als **JSON**.  
   * Laat **veld Encoding** ingesteld op **UTF-8**.  
   * Laat **gebeurteniscompressietype** veld ingesteld op **geen**.  
   * Selecteer de knop **Opslaan**.

5. Ga terug naar de pagina van de stream analytics-taak en selecteer **uitvoer**.  

6. Selecteer **+ Toevoegen**. Selecteer vervolgens **Power BI** uit de vervolgkeuzelijst.  

7. Voor het maken van een nieuwe Power BI-uitvoer voor de gemiddelde prijs visualiseren, moet u de volgende acties uitvoeren:

   * In de **uitvoeralias** veld **averagePriceOutput**.  
   * Laat de **groepswerkruimte** veld ingesteld op **verbinding met het laden van werkruimtes toestaan**.  
   * In de **gegevenssetnaam** veld **averagePrice**.  
   * In de **tabelnaam** veld **averagePrice**.  
   * Selecteer de **autoriseren** knop en volg de instructies voor het autoriseren van de verbinding met Power BI.  
   * Selecteer de knop **Opslaan**.  

8. Ga vervolgens terug naar **streamjob1** en selecteer **query bewerken**.

   ![Query bewerken](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Plak de volgende query in het queryvenster. De **gemiddelde prijs** query berekent de gemiddelde prijs van alle items die door gebruikers, de gemiddelde prijs van alle items die zijn toegevoegd aan de gebruikers winkelwagens en de gemiddelde prijs van alle items die zijn gekocht door gebruikers worden weergegeven. Met deze metriek kunt bepalen welke prijzen voor de verkoop items en wat voorraad te investeren in de e-commerce-bedrijven. Bijvoorbeeld, als de gemiddelde prijs van items die worden weergegeven veel hoger dan de gemiddelde prijs van artikelen die zijn gekocht is, een bedrijf mogelijk kiest u minder duur items toevoegen aan de inventaris.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Selecteer vervolgens **opslaan** in de linkerbovenhoek.  

11. Nu Ga terug naar **streamjob1** en selecteer de **Start** knop aan de bovenkant van de pagina. Azure Stream Analytics kunt duurt enkele minuten om opnieuw te starten, maar uiteindelijk ziet u het wijzigen van 'Starting' in 'Actief'.

## <a name="connect-to-power-bi"></a>Verbinding maken met Power BI

Power BI is een suite met hulpprogramma's voor business analytics voor het analyseren van gegevens en inzichten te delen. Er is een goed voorbeeld van hoe u strategisch de geanalyseerde gegevens kunt visualiseren.

1. Meld u aan bij Power BI en navigeer naar **mijn werkruimte** door het openen van het menu aan de linkerkant van de pagina.  

2. Selecteer **+ maken** in de rechterbovenhoek en selecteer vervolgens **Dashboard** om een dashboard te maken.  

3. Selecteer **+ tegel toevoegen** in de rechterbovenhoek.  

4. Selecteer **aangepaste Streaminggegevens**en selecteer vervolgens de **volgende** knop.  
 
5. Selecteer **averagePrice** van **uw GEGEVENSSETS**en selecteer vervolgens **volgende**.  

6. In de **Visualisatietype** Kies **gegroepeerde staafdiagram** uit de vervolgkeuzelijst. Onder **as**, actie toevoegen. Overslaan **legenda** zonder toe te voegen. Voeg vervolgens in de volgende sectie de naam **Value**toe: **Gem**. Selecteer **volgende**, geef een titel op voor de grafiek en selecteer **Toep assen**. U ziet een nieuwe grafiek op uw dashboard.  

7. Nu, als u meer metrische gegevens visualiseren wilt, u kunt teruggaan naar **streamjob1** en drie meer uitvoer te maken met de volgende velden.

   a. **Uitvoeralias:** incomingRevenueOutput, naam van gegevensset: incomingRevenue, tabelnaam: incomingRevenue  
   b. **Uitvoeralias:** top5Output, naam van gegevensset: top5, tabelnaam: top5  
   c. **Uitvoeralias:** uniqueVisitorCountOutput, naam van gegevensset: uniqueVisitorCount, tabelnaam: uniqueVisitorCount

   Selecteer vervolgens **query bewerken** en plak de volgende query's **hierboven** het u al hebt geschreven.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   De query TOP 5 berekent de top 5 artikelen, gerangschikt op het aantal keren dat ze hebben aangeschaft. Met deze metriek kunt e-commerce-bedrijven evalueren welke items populairst en kunnen invloed hebben op van het bedrijf reclame, prijzen, en inventaris beslissingen te nemen.

   De query Omzet berekent omzet door op te tellen van de prijzen voor alle items die zijn aangeschaft elke minuut. Met deze metriek kunt commerciële bedrijven evalueren van de financiële prestaties en tevens stemt ermee in tijden van de dag dragen bij aan de meeste omzet. Dit kan invloed hebben op de algehele strategie van het bedrijf in het bijzonder marketing.

   De unieke BEZOEKERS query berekent het aantal unieke bezoekers op de site zijn om de vijf seconden door detectie unieke winkelwagen-id's Met deze metriek kunt e-commerce-bedrijven hun siteactiviteit evalueren en over het verkrijgen van meer klanten ombouwen.

8. U kunt nu tegels weergegeven voor deze gegevenssets ook toevoegen.

   * Voor de Top 5, zou het zinvol zijn om te doen een gegroepeerd kolomdiagram met de items als de as en de telling als de waarde.  
   * Voor de omzet, zou het zinvol zijn om te doen van een lijndiagram met tijd als de as en de som van de prijzen als de waarde. Het tijdvenster om weer te geven moet de grootste mogelijke om zoveel mogelijk informatie leveren.  
   * Voor unieke bezoekers, zou het zinvol zijn om te doen van een kaartvisualisatie met het aantal unieke bezoekers van de als de waarde.

   Dit is het een voorbeelddashboard uiterlijk met deze grafieken:

   ![Visualisaties](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Optioneel: Visualiseren met een E-commerce-site

Nu ziet u hoe u de nieuwe gegevens analysis tool kunt gebruiken om te verbinden met een echte e-commerce-site. Als u de e-commerce-site wilt maken, gebruikt u een Azure Cosmos-data base om de lijst met product categorieën (vrouwen, heren, unisex), de product catalogus en een lijst met veelgebruikte items op te slaan.

1. Ga terug naar de [Azure Portal](https://portal.azure.com/), klikt u vervolgens aan uw **Cosmos DB-account**, klikt u vervolgens aan **Data Explorer**.  

   Toevoegen van twee verzamelingen onder **changefeedlabdatabase** - **producten** en **categorieën** met een vaste opslagcapaciteit.

   Toevoegen van een andere verzameling onder **changefeedlabdatabase** met de naam **topItems** en **/Item** als de partitiesleutel.

2. Selecteer de **topItems** verzameling, en klikt u onder **schaal en instellingen** instellen de **Time to Live** moet **30 seconden** , zodat deze topItems bijgewerkt elke 30 seconden.

   ![Time To Live](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Als u wilt vullen de **topItems** verzameling met de meest aangeschaft items, Ga terug naar **streamjob1** en toevoegen van een nieuwe **uitvoer**. Selecteer **Cosmos DB**.

4. Vul de vereiste velden als pictogrammen hieronder in.

   ![Cosmos-uitvoer](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Als u de optionele TOP 5-query in het vorige deel van het testlab hebt toegevoegd, gaat u verder met deel 5a. Als dat niet het geval is, gaat u verder met deel 5b.

   5a. In **streamjob1**, selecteer **query bewerken** en plak de volgende query in uw Azure Stream Analytics query-editor onder de query TOP 5 maar boven de rest van de query's.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. In **streamjob1**, selecteer **query bewerken** en plak de volgende query in uw Azure Stream Analytics query-editor boven alle andere query's.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Open **EcommerceWebApp.sln** en navigeer naar de **Web.config** -bestand in de **Solution Explorer**.  

7. Binnen de `<appSettings>` blokkeren, voegt de **URI** en **primaire sleutel** dat u eerder hebt opgeslagen, waarbij de status **hier uw URI** en **hier uw primaire sleutel**. Voeg uw **databasenaam** en **verzamelingsnaam** zoals wordt aangegeven. (Deze namen moeten **changefeedlabdatabase** en **changefeedlabcollection** tenzij u ervoor hebt gekozen om de naam van uw anders.)

   Vul uw **producten verzamelingsnaam**, **categorieën verzamelingsnaam**, en **bovenste items verzamelingsnaam** zoals wordt aangegeven. (Deze namen moeten **producten, categorieën en topItems** tenzij u ervoor hebt gekozen om de naam van uw anders.)  

8. Navigeer naar en open de **afhandeling map** binnen **EcommerceWebApp.sln.** Open vervolgens de **Web.config** bestand in die map.  

9. Binnen de `<appSettings>` blokkeren, voegt de **URI** en **primaire sleutel** dat u eerder hebt opgeslagen waar van toepassing. Voeg uw **databasenaam** en **verzamelingsnaam** zoals wordt aangegeven. (Deze namen moeten **changefeedlabdatabase** en **changefeedlabcollection** tenzij u ervoor hebt gekozen om de naam van uw anders.)  

10. Druk op **Start** aan de bovenkant van de pagina het programma uit te voeren.  

11. Nu kunt u experimenteren op het e-commerce-site. Wanneer u een item bekijken, een item aan de winkelwagen toevoegen, wijzigen van de hoeveelheid van een item in de winkelwagen, proefabonnement of een item, deze gebeurtenissen worden doorgegeven via de Cosmos DB-wijzigingenfeed Event Hub, ASA en vervolgens Power BI. Aangeraden DataGenerator aanzienlijke web verkeersgegevens genereren en bieden een realistische set '' hot 'producten' uitgevoerd op de e-commerce-site.

## <a name="delete-the-resources"></a>De resources verwijderen

Als u wilt verwijderen van de resources die u tijdens dit lab hebt gemaakt, gaat u naar de resourcegroep op [Azure Portal](https://portal.azure.com/)en selecteer vervolgens **resourcegroep verwijderen** in het menu aan de bovenkant van de pagina en volg de instructies opgegeven.

## <a name="next-steps"></a>Volgende stappen 
  
* Zie voor meer informatie over de wijzigingenfeed [werken met change feed support in Azure Cosmos DB](change-feed.md) 
* [Wijzigingenfeed oplossing notification](change-feed-hl7-fhir-logic-apps.md) voor gezondheidszorg organisatie met behulp van Azure Cosmos DB.
