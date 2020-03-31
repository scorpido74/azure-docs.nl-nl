---
title: Azure Cosmos DB-wijzigingsfeed gebruiken om realtime gegevensanalyses te visualiseren
description: In dit artikel wordt beschreven hoe een wijzigingsfeed door een retailbedrijf kan worden gebruikt om gebruikerspatronen te begrijpen, realtime gegevensanalyse en visualisatie uit te voeren
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: c0c1a28dc399d3f176f92e656621fec1bc92dbfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513489"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Azure Cosmos DB-wijzigingsfeed gebruiken om realtime gegevensanalyses te visualiseren

De Azure Cosmos DB-wijzigingsfeed is een mechanisme om een continue en incrementele feed van records uit een Azure Cosmos-container te krijgen terwijl deze records worden gemaakt of gewijzigd. Feedsupport wijzigen werkt door naar de container te luisteren voor eventuele wijzigingen. Als output verschijnt er vervolgens een gesorteerde lijst met gewijzigde documenten op volgorde van wijziging. Zie Werken met het [feedartikel wijzigen](change-feed.md) voor meer informatie over het wijzigen van de feed. 

In dit artikel wordt beschreven hoe de change feed door een e-commercebedrijf kan worden gebruikt om gebruikerspatronen te begrijpen, real-time gegevensanalyse en visualisatie uit te voeren. U analyseert gebeurtenissen zoals een gebruiker die een item bekijkt, een item toevoegt aan zijn winkelwagentje of een item koopt. Wanneer een van deze gebeurtenissen optreedt, wordt een nieuwe record gemaakt en wordt de feedlogboeken die record record wijzigen. Wijzig vervolgens de feed en activeert een reeks stappen die resulteren in visualisatie van statistieken die de bedrijfsprestaties en -activiteit analyseren. Voorbeeldstatistieken die u visualiseren, zijn inkomsten, unieke sitebezoekers, populairste items en de gemiddelde prijs van de objecten die worden bekeken versus toegevoegd aan een winkelwagen versus gekocht. Deze voorbeeldstatistieken kunnen een e-commercebedrijf helpen zijn sitepopulariteit te evalueren, zijn advertentie- en prijsstrategieën te ontwikkelen en beslissingen te nemen over welke voorraad u moet investeren.

Zie de volgende video als u een video over de oplossing wilt bekijken voordat u aan de slag gaat:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Oplossingsonderdelen
Het volgende diagram geeft de gegevensstroom en componenten die bij de oplossing betrokken zijn, weer:

![Projectvisueel](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Datageneratie:** Gegevenssimulator wordt gebruikt om retailgegevens te genereren die gebeurtenissen vertegenwoordigen, zoals een gebruiker die een item bekijkt, een item aan zijn winkelwagentje toevoegt en een item koopt. U grote set voorbeeldgegevens genereren met behulp van de gegevensgenerator. De gegenereerde voorbeeldgegevens bevatten documenten in de volgende indeling:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Kosmos DB:** De gegenereerde gegevens worden opgeslagen in een Azure Cosmos-container.  

3. **Feed wijzigen:** In de wijzigingsfeed wordt geluisterd naar wijzigingen in de Azure Cosmos-container. Elke keer dat een nieuw document aan de verzameling wordt toegevoegd (dat wil zeggen wanneer een gebeurtenis plaatsvindt, zoals een gebruiker die een item bekijkt, een item toevoegt aan zijn winkelwagentje of een item koopt), activeert de wijzigingsfeed een [Azure-functie](../azure-functions/functions-overview.md).  

4. **Azure, functie:** De Azure-functie verwerkt de nieuwe gegevens en stuurt deze naar een [Azure Event Hub.](../event-hubs/event-hubs-about.md)  

5. **Gebeurtenishub:** De Azure Event Hub slaat deze gebeurtenissen op en stuurt ze naar [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) om verdere analyses uit te voeren.  

6. **Azure Stream Analytics:** Azure Stream Analytics definieert query's om de gebeurtenissen te verwerken en realtime gegevensanalyse uit te voeren. Deze gegevens worden vervolgens verzonden naar [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI wordt gebruikt om de gegevens te visualiseren die door Azure Stream Analytics worden verzonden. U een dashboard maken om te zien hoe de statistieken in realtime veranderen.  

## <a name="prerequisites"></a>Vereisten

* Microsoft .NET Framework 4.7.1 of hoger

* Microsoft .NET Core 2.1 (of hoger)

* Visual Studio met universal Windows Platform development, .NET desktop development en ASP.NET en web development workloads

* Microsoft Azure-abonnement

* Microsoft Power BI-account

* Download het [Azure Cosmos DB change feed lab](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) van GitHub. 

## <a name="create-azure-resources"></a>Azure-resources maken 

Maak de Azure-resources - Azure Cosmos DB, Storage-account, Event Hub, Stream Analytics die vereist zijn door de oplossing. U implementeert deze resources via een Azure Resource Manager-sjabloon. Gebruik de volgende stappen om deze resources te implementeren: 

1. Stel het uitvoeringsbeleid van Windows PowerShell in op **Onbeperkt**. Open **hiervoor Windows PowerShell als beheerder** en voer de volgende opdrachten uit:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Vanuit de GitHub-opslagplaats die u in de vorige stap hebt gedownload, navigeert u naar de map **Azure Resource Manager** en opent u het bestand **parameters.json.**  

3. Geef waarden op voor cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parameters zoals aangegeven in het bestand **parameters.json.** U moet later de namen gebruiken die u aan elk van uw bronnen geeft.  

4. Navigeer vanuit **Windows PowerShell**naar de map **Azure Resource Manager** en voer de volgende opdracht uit:

   ```powershell
   .\deploy.ps1
   ```
5. Voer desgevraagd uw Azure **Subscription ID**in, **wijzigt feedlab** voor de naam van de brongroep en **voert 1** uit voor de implementatienaam. Zodra de resources beginnen te implementeren, kan het tot 10 minuten duren voordat de resources zijn voltooid.

## <a name="create-a-database-and-the-collection"></a>Een database en de verzameling maken

U maakt nu een verzameling voor het houden van e-commerce site-evenementen. Wanneer een gebruiker een item bekijkt, een item toevoegt aan zijn winkelwagentje of een item koopt, ontvangt de verzameling een record met de actie ('bekeken', 'toegevoegd' of 'gekocht'), de naam van het betrokken object, de prijs van het betrokken object en het ID-nummer van de gebruikerswinkelwagen Betrokken.

1. Ga naar [Azure portal](https://portal.azure.com/) en zoek het Azure **Cosmos DB-account** dat is gemaakt door de sjabloonimplementatie.  

2. Selecteer in het deelvenster **Gegevensverkenner** de optie **Nieuwe verzameling** en vul het formulier in met de volgende details:  

   * Selecteer voor het veld **Database-id** de optie **Nieuw maken**en voer vervolgens **changefeedlabdatabase**in . Laat het **doorvoervak voor de gegevensstroomvoorziening** niet ingeschakeld.  
   * Voer voor het veld **Verzamelingen-id** **de verzameling changefeedlabin .**  
   * Voer voor het veld **Partitiesleutel** **/Item**in . Dit is hoofdlettergevoelig, dus zorg ervoor dat u het correct invoert.  
   * Voer voor het veld **Doorvoer** **10000**in .  
   * Selecteer de knop **OK**.  

3. Maak vervolgens een andere verzameling met de naam **leases** voor wijzigingfeedverwerking. De leases collectie coördineert de verwerking van de wijziging feed over meerdere werknemers. Een aparte collectie wordt gebruikt om de leases op te slaan met één lease per partitie.  

4. Ga terug naar het deelvenster **Gegevensverkenner** en selecteer **Nieuwe verzameling** en vul het formulier in met de volgende details:

   * Selecteer voor het veld **Database-id** de optie **Bestaand gebruiken**en voer vervolgens **changefeedlabdatabase in**.  
   * Voer **huurovereenkomsten**in voor het veld **Ophalen van id.**  
   * Selecteer **Vast**voor **opslagcapaciteit**.  
   * Laat het veld **Doorvoer** instellen op de standaardwaarde.  
   * Selecteer de knop **OK**.

## <a name="get-the-connection-string-and-keys"></a>De verbindingstekenreeks en toetsen ophalen

### <a name="get-the-azure-cosmos-db-connection-string"></a>De tekenreeks Azure Cosmos DB-verbinding

1. Ga naar [Azure portal](https://portal.azure.com/) en zoek het Azure **Cosmos DB-account** dat is gemaakt door de sjabloonimplementatie.  

2. Navigeer naar het deelvenster **Sleutels,** kopieer de tekenreeks primaire verbinding en kopieer deze naar een notitieblok of een ander document waar toe u in het hele lab toegang hebt. U moet het **Cosmos DB Connection String labelen.** U moet de tekenreeks later in uw code kopiëren, dus neem een notitie en onthoud waar u deze opslaat.

### <a name="get-the-storage-account-key-and-connection-string"></a>De opslagaccountsleutel en verbindingstekenreeks oppakken

Met Azure Storage-accounts kunnen gebruikers gegevens opslaan. In dit lab gebruikt u een opslagaccount om gegevens op te slaan die worden gebruikt door de Azure-functie. De Azure-functie wordt geactiveerd wanneer de verzameling wordt gewijzigd.

1. Ga terug naar uw resourcegroep en open het opslagaccount dat u eerder hebt gemaakt  

2. Selecteer **Toegangstoetsen** in het menu aan de linkerkant.  

3. Kopieer de waarden onder **toets 1** naar een notitieblok of een ander document waar toe u toegang hebt in het hele lab. U moet de **sleutel labelen** als **opslagsleutel** en de **verbindingstekenreeks** als **tekenreeks voor opslagverbinding**. U moet deze tekenreeksen later in uw code kopiëren, dus neem een notitie en onthoud waar u ze opslaat.  

### <a name="get-the-event-hub-namespace-connection-string"></a>De verbindingstekenreeks voor de naamruimte van de gebeurtenishub opgebruiken

Een Azure Event Hub ontvangt de gebeurtenisgegevens, slaat, verwerkt en stuurt de gegevens door. In dit lab ontvangt de Azure Event Hub een document telkens wanneer een nieuwe gebeurtenis plaatsvindt (d.w.z. een item wordt bekeken door een gebruiker, wordt toegevoegd aan het winkelwagentje van een gebruiker of is gekocht door een gebruiker) en stuurt dat document vervolgens door naar Azure Stream Analytics.

1. Keer terug naar uw resourcegroep en open de **naamruimte van de gebeurtenishub** die u in het prelab hebt gemaakt en benoemd.  

2. Selecteer **Beleid voor gedeelde toegang** in het menu aan de linkerkant.  

3. selecteer **RootManageSharedAccessKey**. Kopieer de **primaire verbindingstoets** naar een notitieblok of een ander document waar toe u toegang hebt in het hele lab. U moet de verbindingstekenreeks **Gebeurtenishubnaamruimte** labelen. U moet de tekenreeks later in uw code kopiëren, dus neem een notitie en onthoud waar u deze opslaat.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Azure-functie instellen om de wijzigingsfeed te lezen

Wanneer een nieuw document wordt gemaakt of een huidig document wordt gewijzigd in een Cosmos-container, wordt dat gewijzigde document automatisch toegevoegd aan de geschiedenis van verzamelingswijzigingen. U bouwt en voert nu een Azure-functie uit die de wijzigingsfeed verwerkt. Wanneer een document wordt gemaakt of gewijzigd in de verzameling die u hebt gemaakt, wordt de Azure-functie geactiveerd door de wijzigingsfeed. Vervolgens stuurt de Azure-functie het gewijzigde document naar de gebeurtenishub.

1. Keer terug naar de opslagplaats die je op je apparaat hebt gekloond.  

2. Klik met de rechtermuisknop op het bestand met de naam **ChangeFeedLabSolution.sln** en selecteer **Openen met Visual Studio**.  

3. Navigeer naar **local.settings.json** in Visual Studio. Gebruik vervolgens de waarden die u eerder hebt geregistreerd om de spaties in te vullen.  

4. Navigeer naar **ChangeFeedProcessor.cs**. Voer in de parameters voor de functie **Uitvoeren** de volgende acties uit:  

   * Vervang de tekst **UW COLLECTIENAAM HIER** door de naam van uw verzameling. Als u eerdere instructies hebt opgevolgd, wordt de naam van uw verzameling gewijzigdfeedlabcollectie.  
   * Vervang hier de naam **YOUR LEASES COLLECTION** door de naam van uw leasecollectie. Als u eerdere instructies hebt opgevolgd, is de naam van uw leasecollectie **huurovereenkomsten.**  
   * Controleer boven aan Visual Studio of het vak Project opstarten links van de groene pijl **ChangeFeedFunction**staat.  
   * Selecteer **Start** boven aan de pagina om het programma uit te voeren  
   * U bevestigen dat de functie wordt uitgevoerd wanneer de console-app 'Jobhost is gestart' zegt.

## <a name="insert-data-into-azure-cosmos-db"></a>Gegevens invoegen in Azure Cosmos DB 

Als u wilt zien hoe de feed voor wijzigingen nieuwe acties op een e-commercesite verwerkt, moet u gegevens simuleren die gebruikers vertegenwoordigen die items uit de productcatalogus bekijken, deze items toevoegen aan hun winkelwagentjes en de artikelen in hun winkelwagentjes kopen. Deze gegevens zijn willekeurig en met het oog op het repliceren van welke gegevens op een e-commercesite eruit zouden zien.

1. Navigeer terug naar de opslagplaats in Verkenner en klik met de rechtermuisknop op **ChangeFeedFunction.sln** om deze opnieuw te openen in een nieuw Visual Studio-venster.  

2. Navigeer naar het **bestand App.config.** Voeg `<appSettings>` binnen het blok het eindpunt en de unieke **primaire sleutel** toe die van uw Azure Cosmos DB-account dat u eerder hebt opgehaald.  

3. Voeg de **verzamelings-** en **databasenamen** toe. (Deze namen moeten **changefeedlabcollection** en **changefeedlabdatabase** zijn, tenzij u ervoor kiest om de jouwe anders een naam te geven.)

   ![Verbindingstekenreeksen bijwerken](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Sla de wijzigingen op in alle bestanden die zijn bewerkt.  

5. Zorg er boven aan Visual Studio voor dat het vak **Opstarten project** links van de groene pijl **DataGenerator**zegt. Selecteer vervolgens **Start** boven aan de pagina om het programma uit te voeren.  
 
6. Wacht tot het programma wordt uitgevoerd. De sterren betekenen dat de gegevens binnenkomen! Houd het programma draaiende - het is belangrijk dat veel gegevens worden verzameld.  

7. Als u naar [azure-portal](https://portal.azure.com/) navigeert en vervolgens naar het Cosmos DB-account binnen uw brongroep en vervolgens naar **Gegevensverkenner,** ziet u de gerandomiseerde gegevens die zijn geïmporteerd in uw **changefeedlabcollectie.**
 
   ![Gegevens gegenereerd in portal](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Een streamanalysetaak instellen

Azure Stream Analytics is een volledig beheerde cloudservice voor realtime verwerking van streaminggegevens. In dit lab gebruikt u streamanalyses om nieuwe gebeurtenissen vanuit de Gebeurtenishub te verwerken (d.w.z. wanneer een item wordt bekeken, toegevoegd aan een winkelwagentje of gekocht), deze gebeurtenissen op te nemen in realtime gegevensanalyse en deze naar Power BI te verzenden voor visualisatie.

1. Navigeer vanuit de [Azure-portal](https://portal.azure.com/)naar uw brongroep en **streamtaak1** (de streamanalysetaak die u in het prelab hebt gemaakt).  

2. Selecteer **Invoer** zoals hieronder gedemonstreerd.  

   ![Invoer maken](./media/changefeed-ecommerce-solution/create-input.png)

3. Selecteer **+ Streaminvoer toevoegen**. Selecteer **vervolgens Gebeurtenishub** in het vervolgkeuzemenu.  

4. Vul het nieuwe invoerformulier in met de volgende details:

   * Voer in het veld **Invoeralias** **invoer**in .  
   * Selecteer de optie voor **Gebeurtenishub selecteren in uw abonnementen**.  
   * Stel het veld **Abonnement** in op uw abonnement.  
   * Voer in het **veld Naamruimte voor gebeurtenishub** de naam in van de naamruimte van de gebeurtenishub die u tijdens het voorvoeglab hebt gemaakt.  
   * Selecteer in het **veld Naam van gebeurtenishub** de optie Bestaand **gebruiken** en kies **gebeurtenishub1** in het vervolgkeuzemenu.  
   * Laat **het veld Beleidsnaam van gebeurtenishub** instellen op de standaardwaarde.  
   * Laat **de serialisatie-indeling van gebeurtenissen** als **JSON .**  
   * Laat **het veld Codering** in- en weer staan ingesteld op **UTF-8**.  
   * Het veld **compressietype gebeurtenis** laten ingesteld op **Geen**.  
   * Selecteer de knop **Opslaan**.

5. Navigeer terug naar de vacaturepagina voor streamanalyse en selecteer **Uitvoer.**  

6. Selecteer **+ Toevoegen**. Selecteer **vervolgens Power BI** in het vervolgkeuzemenu.  

7. Als u een nieuwe Power BI-uitvoer wilt maken om de gemiddelde prijs te visualiseren, voert u de volgende acties uit:

   * Voer in het veld **Uitvoeralias** **de gemiddelde Prijsuitvoer**invoeren .  
   * Laat het veld **Groepswerkruimte** instellen op **Verbinding autoriseren voor het laden van werkruimten**.  
   * Voer in het veld **Naam van gegevensset** **de gemiddelde prijs**in .  
   * Voer in het veld **Tabelnaam** **de gemiddelde prijs in**.  
   * Selecteer de knop **Autoriseren** en volg de instructies om de verbinding met Power BI te autoriseren.  
   * Selecteer de knop **Opslaan**.  

8. Ga vervolgens terug naar **streamjob1** en selecteer **Query bewerken**.

   ![Query bewerken](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Plak de volgende query in het queryvenster. De **query GEMIDDELDE PRIJS** berekent de gemiddelde prijs van alle artikelen die door gebruikers worden bekeken, de gemiddelde prijs van alle artikelen die aan winkelwagentjes van gebruikers worden toegevoegd en de gemiddelde prijs van alle artikelen die door gebruikers zijn gekocht. Deze statistiek kan e-commercebedrijven helpen beslissen welke prijzen artikelen moeten verkopen en in welke voorraad ze moeten investeren. Als de gemiddelde prijs van bekeken artikelen bijvoorbeeld veel hoger is dan de gemiddelde prijs van gekochte artikelen, kan een bedrijf ervoor kiezen om minder dure artikelen aan zijn voorraad toe te voegen.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Selecteer **vervolgens Opslaan** in de linkerbovenhoek.  

11. Ga nu terug naar **streamjob1** en selecteer de knop **Start** boven aan de pagina. Azure Stream Analytics kan een paar minuten duren om op te starten, maar uiteindelijk ziet u het veranderen van 'Starten' naar 'Uitvoeren'.

## <a name="connect-to-power-bi"></a>Verbinding maken met Power BI

Power BI is een pakket business analytics-hulpprogramma's waarmee u gegevens kunt analyseren en inzichten kunt delen. Het is een geweldig voorbeeld van hoe je de geanalyseerde gegevens strategisch visualiseren.

1. Meld u aan bij Power BI en navigeer naar **Mijn werkruimte** door het menu aan de linkerkant van de pagina te openen.  

2. Selecteer **+ Maak** in de rechterbovenhoek en selecteer **Dashboard** om een dashboard te maken.  

3. Selecteer **+ Voeg tegel toe** in de rechterbovenhoek.  

4. Selecteer **Aangepaste streaminggegevens**en selecteer vervolgens de knop **Volgende.**  
 
5. Selecteer **gemiddeldePrijs** in **uw gegevenssets**en selecteer **Volgende**.  

6. Kies in het veld **Visualisatietype** de optie **Geclusterd staafdiagram** in het vervolgkeuzemenu. Voeg **onder As**actie toe. Sla **Legenda** over zonder iets toe te voegen. Voeg vervolgens onder de volgende sectie **waarde**toe, **voeg avg**toe . Selecteer **Volgende,** plaats vervolgens het diagram en selecteer **Toepassen**. U ziet een nieuwe grafiek op uw dashboard!  

7. Als u nu meer statistieken wilt visualiseren, u teruggaan naar **stream1** en nog drie uitvoermaken met de volgende velden.

   a. **Uitvoeralias:** inkomendeRevenueOutput, naam van gegevensset: inkomende inkomsten, tabelnaam: binnenkomende inkomsten  
   b. **Uitvoeralias:** top5Output, naam van gegevensset: top5, tabelnaam: top5  
   c. **Uitvoeralias:** unieke VisitorCountOutput, naam van dataset: uniekBezoekersaantal, tabelnaam: uniekBezoekersaantal

   Selecteer vervolgens **Query bewerken** en plak de volgende query's **boven** de query die u al hebt geschreven.

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
   
   De TOP 5-query berekent de top 5-items, gerangschikt op het aantal keren dat ze zijn gekocht. Deze statistiek kan e-commercebedrijven helpen evalueren welke artikelen het populairst zijn en de advertentie-, prijs- en voorraadbeslissingen van het bedrijf kunnen beïnvloeden.

   De opbrengstquery berekent de opbrengst door de prijzen van alle gekochte artikelen per minuut op te tellen. Deze statistiek kan e-commercebedrijven helpen de financiële prestaties te evalueren en ook te begrijpen op welke momenten van de dag bijdragen aan de meeste inkomsten. Dit kan van invloed zijn op de algemene bedrijfsstrategie, met name marketing.

   De unieke bezoekersquery berekent hoeveel unieke bezoekers er elke 5 seconden op de site zijn door unieke winkel-ID's te detecteren. Deze statistiek kan e-commercebedrijven helpen hun siteactiviteit te evalueren en te strategiseren hoe ze meer klanten kunnen werven.

8. U nu ook tegels voor deze gegevenssets toevoegen.

   * Voor Top 5 is het zinvol om een geclusterd kolomdiagram te maken met de items als as en de telling als waarde.  
   * Voor inkomsten zou het zinvol zijn om een lijndiagram te maken met de tijd als de as en de som van de prijzen als de waarde. Het tijdvenster om weer te geven moet zo groot mogelijk zijn om zoveel mogelijk informatie te kunnen leveren.  
   * Voor unieke bezoekers, zou het zinvol zijn om een kaart visualisatie te doen met het aantal unieke bezoekers als de waarde.

   Zo ziet een voorbeelddashboard er met deze grafieken uit:

   ![visualisaties](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Optioneel: visualiseren met een e-commercesite

U zult nu observeren hoe u uw nieuwe data-analyse tool gebruiken om verbinding te maken met een echte e-commerce site. Gebruik een Azure Cosmos-database om de lijst met productcategorieën (dames, heren, Unisex), de productcatalogus en een lijst met de populairste items op te slaan om de e-commercesite te bouwen.

1. Navigeer terug naar de [Azure-portal](https://portal.azure.com/)en vervolgens naar uw **Cosmos DB-account**en vervolgens naar **Data Explorer.**  

   Voeg twee verzamelingen toe onder **changefeedlabdatabase-producten** - **products** en **-categorieën** met vaste opslagcapaciteit.

   Voeg een andere verzameling toe onder **changefeedlabdatabase** met de naam **topItems** en **/Item** als partitiesleutel.

2. Selecteer de **topItems-verzameling** en stel onder **Schalen en Instellingen** de tijd in op **Live** op **30 seconden,** zodat topItems elke 30 seconden wordt bijgewerkt.

   ![Time To Live](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Als u de **topItems-verzameling** wilt vullen met de meest gekochte items, navigeert u terug naar **streamjob1** en voegt u een nieuwe **uitvoer toe.** Selecteer **Cosmos DB**.

4. Vul de vereiste velden in zoals hieronder afgebeeld.

   ![Cosmos-uitvoer](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Als u de optionele TOP 5-query in het vorige deel van het lab hebt toegevoegd, gaat u verder met deel 5a. Zo niet, ga dan naar deel 5b.

   5a. Selecteer in **streamjob1** **Query bewerken** en plak de volgende query in uw Azure Stream Analytics-queryeditor onder de TOP 5-query, maar boven de rest van de query's.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. Selecteer in **streamjob1** **Query bewerken** en plak de volgende query in uw Azure Stream Analytics-queryeditor boven alle andere query's.

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

6. Open **EcommerceWebApp.sln** en navigeer naar het **web.config-bestand** in de **Solution Explorer.**  

7. Voeg `<appSettings>` binnen het blok de **URI-** en **primaire sleutel toe** die u eerder hebt opgeslagen waar hier uw **URI** en uw primaire **sleutel staat.** Voeg vervolgens de naam en **verzamelnaam** van uw **database** toe zoals aangegeven. (Deze namen moeten **changefeedlabdatabase** en **changefeedlabcollection** zijn, tenzij u ervoor kiest om de jouwe anders een naam te geven.)

   Vul de naam van de collectie van uw **producten,** **de naam van de categorieën collectie**en de naam van de verzameling van **topitems** in zoals aangegeven. (Deze namen moeten **producten, categorieën en topItems zijn,** tenzij u ervoor kiest om de jouwe anders te noemen.)  

8. Navigeer naar en open de **map Afhandeling** in **EcommerceWebApp.sln.** Open vervolgens het **bestand Web.config** in die map.  

9. Voeg `<appSettings>` in het blok de **URI-** en **primaire sleutel toe** die u eerder hebt opgeslagen, waar aangegeven. Voeg vervolgens de naam en **verzamelnaam** van uw **database** toe zoals aangegeven. (Deze namen moeten **changefeedlabdatabase** en **changefeedlabcollection** zijn, tenzij u ervoor kiest om de jouwe anders een naam te geven.)  

10. Druk boven aan de pagina op **Start** om het programma uit te voeren.  

11. Nu u spelen op de e-commerce site. Wanneer u een item bekijkt, een item aan uw winkelwagentje toevoegt, de hoeveelheid van een item in uw winkelwagentje wijzigt of een item koopt, worden deze gebeurtenissen doorgegeven via de Feed Cosmos DB change naar Event Hub, ASA en vervolgens Naar Power BI. We raden u aan DataGenerator te blijven uitvoeren om belangrijke webverkeersgegevens te genereren en een realistische set "Hot Products" op de e-commercesite te bieden.

## <a name="delete-the-resources"></a>De bronnen verwijderen

Als u de resources wilt verwijderen die u tijdens dit lab hebt gemaakt, navigeert u naar de brongroep op [azure-portal](https://portal.azure.com/)en selecteert u **Resourcegroep verwijderen** in het menu boven aan de pagina en volgt u de opgegeven instructies.

## <a name="next-steps"></a>Volgende stappen 
  
* Zie Werken met ondersteuning [voor change feed in Azure Cosmos DB](change-feed.md) voor meer informatie over wijzigingsfeed 
