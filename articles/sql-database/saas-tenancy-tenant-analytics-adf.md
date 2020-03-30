---
title: Analysequery's uitvoeren op tenantdatabases
description: Analysequery's met meerdere tenants met gegevens die zijn geëxtraheerd uit Azure SQL Database, SQL Data Warehouse, Azure Data Factory of Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4791cd3a6b6f72c5d9ee4ca828d66b0d361f356c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73816770"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>SaaS-analyses verkennen met Azure SQL Database, SQL Data Warehouse, Data Factory en Power BI

In deze zelfstudie doorloopt u een end-to-end analysescenario. Het scenario laat zien hoe analytics over tenantgegevens softwareleveranciers in staat kan stellen om slimme beslissingen te nemen. Met behulp van gegevens uit elke tenantdatabase gebruikt u analyses om inzicht te krijgen in het gedrag van huurders, inclusief het gebruik van de voorbeeld-Wingtip Tickets SaaS-toepassing. Dit scenario omvat drie stappen: 

1.  **Haal gegevens** uit elke tenantdatabase naar een analysearchief, in dit geval een SQL Data Warehouse.
2.  **Optimaliseer de geëxtraheerde gegevens** voor analyseverwerking.
3.  Gebruik **Business Intelligence-tools** om nuttige inzichten te verzamelen, die de besluitvorming kunnen sturen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Maak de tenant analytics store voor laden.
> - Gebruik Azure Data Factory (ADF) om gegevens uit elke tenantdatabase te extraheren naar het magazijn voor analysegegevens.
> - Optimaliseer de geëxtraheerde gegevens (reorganiseren in een sterschema).
> - Bevraag het magazijn voor analysegegevens.
> - Gebruik Power BI voor gegevensvisualisatie om trends in tenantgegevens te markeren en aanbevelingte doen voor verbeteringen.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analytics over geëxtraheerde tenantgegevens

SaaS-toepassingen bevatten een potentieel enorme hoeveelheid tenantgegevens in de cloud. Deze gegevens kunnen een rijke bron van inzichten bieden over de werking en het gebruik van uw toepassing en het gedrag van uw huurders. Deze inzichten kunnen de ontwikkeling van functies, bruikbaarheidsverbeteringen en andere investeringen in de apps en het platform sturen.

Toegang tot de gegevens voor alle tenants is eenvoudig wanneer alle gegevens zich in slechts één multi-tenantdatabase bevinden. Maar de toegang is complexer wanneer deze op schaal wordt verdeeld over duizenden databases. Een manier om de complexiteit te temmen is door de gegevens te extraheren naar een analysedatabase of een datawarehouse voor query's.

Deze zelfstudie bevat een end-to-end analysescenario voor de Wingtip Tickets-toepassing. Ten eerste wordt [Azure Data Factory (ADF)](../data-factory/introduction.md) gebruikt als orchestration-tool om de verkoop van tickets en gerelateerde gegevens uit elke tenantdatabase te halen. Deze gegevens worden geladen in faseringstabellen in een analysearchief. De analysewinkel kan een SQL-database of een SQL Data Warehouse zijn. Deze zelfstudie gebruikt [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) als analysearchief.

Vervolgens worden de geëxtraheerde gegevens omgezet en geladen in een reeks [sterschematabellen.](https://www.wikipedia.org/wiki/Star_schema) De tabellen bestaan uit een centrale feitentabel plus bijbehorende dimensietabellen:

- De centrale feitentabel in het sterschema bevat ticketgegevens.
- De dimensietabellen bevatten gegevens over locaties, evenementen, klanten en aankoopdatums.

Samen maken de centrale en dimensietabellen een efficiënte analytische verwerking mogelijk. Het sterschema dat in deze zelfstudie wordt gebruikt, wordt weergegeven in de volgende afbeelding:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Ten slotte worden de tabellen met het sterschema opgevraagd. Queryresultaten worden visueel weergegeven met Behulp van Power BI om inzicht in tenantgedrag en het gebruik van de toepassing te markeren. Met dit sterschema voert u query's uit die het als volgt laten zien:

- Wie koopt tickets en van welke locatie.
- Patronen en trends in de verkoop van tickets.
- De relatieve populariteit van elke locatie.

Deze zelfstudie bevat basisvoorbeelden van inzichten die kunnen worden verkregen uit de Gegevens van Wingtip Tickets. Als u begrijpt hoe elke locatie de service gebruikt, kan de wingtip-verkoper ervoor zorgen dat de verkoper van Wingtip Tickets bijvoorbeeld nadenkt over verschillende serviceplannen die zijn gericht op min of meer actieve locaties. 

## <a name="setup"></a>Instellen

### <a name="prerequisites"></a>Vereisten

> [!NOTE]
> In deze zelfstudie worden functies van de Azure Data Factory gebruikt die zich momenteel in een beperkte preview bevinden (parameterisatie van gekoppelde service). Als u deze zelfstudie wilt doen, geeft u hier uw abonnements-ID [op.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu) We sturen je een bevestiging zodra je abonnement is ingeschakeld.

Voor het voltooien van deze zelfstudie moet u ervoor zorgen dat aan de volgende vereisten is voldaan:
- De Wingtip Tickets SaaS Database Per Tenant applicatie wordt geïmplementeerd. Zie [De SaaS-toepassing Wingtip implementeren en verkennen](saas-dbpertenant-get-started-deploy.md)als u deze in minder dan vijf minuten wilt implementeren.
- De Wingtip Tickets SaaS Database Per Tenant scripts en applicatie [broncode](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) worden gedownload van GitHub. Zie downloadinstructies. Zorg ervoor dat *u het zip-bestand deblokkeert* voordat u de inhoud ervan extraheren.
- Power BI Desktop is geïnstalleerd. [Power BI-bureaublad downloaden](https://powerbi.microsoft.com/downloads/).
- De partij van extra tenants is ingericht, zie de [**Provision tenants tutorial**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Gegevens maken voor de demo

Deze zelfstudie onderzoekt analyses over de gegevens over de kaartverkoop. In deze stap genereert u ticketgegevens voor alle huurders. In een latere stap worden deze gegevens geëxtraheerd voor analyse. _Zorg ervoor dat u de batch van huurders_ (zoals eerder beschreven) hebt ingericht, zodat u over voldoende gegevens beschikt om een reeks verschillende aankooppatronen voor tickets bloot te leggen.

1. Open in PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*en stel de volgende waarde in:
    - **$DemoScenario** = **1** Koop tickets voor evenementen op alle locaties
2. Druk op **F5** om het script uit te voeren en maak de aankoopgeschiedenis van tickets voor alle locaties. Met 20 huurders genereert het script tienduizenden tickets en kan het 10 minuten of langer duren.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>SQL Data Warehouse, Data Factory en Blob Storage implementeren 
In de Wingtip Tickets-app worden de transactionele gegevens van de tenants over veel databases verspreid. Azure Data Factory (ADF) wordt gebruikt om het extract, de belasting en de transformatie (ELT) van deze gegevens te orkestreren in het gegevensmagazijn. Als u gegevens zo efficiënt mogelijk wilt laden in SQL Data Warehouse, haalt ADF gegevens uit in tussenliggende blobbestanden en gebruikt het [polybase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) vervolgens om de gegevens in het gegevensmagazijn te laden.   

In deze stap implementeert u de extra resources die worden gebruikt in de zelfstudie: een SQL Data Warehouse genaamd _tenantanalytics_, een Azure Data Factory genaamd _dbtodwload-gebruiker\<\>_ en een Azure-opslagaccount genaamd _wingtipstaging-gebruiker.\<\>_ Het opslagaccount wordt gebruikt om geëxtraheerde gegevensbestanden tijdelijk als blobs vast te houden voordat ze in het gegevensmagazijn worden geladen. Met deze stap wordt ook het schema voor gegevensmagazijnen geïmplementeerd en worden de ADF-pijplijnen gedefinieerd die het ELT-proces orkestreren.
1. Open in PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* en stel in:
    - **$DemoScenario** = **2** Tenant analytics data warehouse, blob storage en data factory implementeren 
1. Druk op **F5** om het demoscript uit te voeren en implementeer de Azure-resources. 

Bekijk nu de Azure-resources die u hebt geïmplementeerd:
#### <a name="tenant-databases-and-analytics-store"></a>Tenantdatabases en analysewinkel
Gebruik [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) om verbinding te maken met **tenants1-dpt-&lt;gebruikers-&gt; ** en **catalogus-dpt-gebruikersservers.&lt;&gt; ** Vervang &lt;&gt; de gebruiker door de waarde die wordt gebruikt toen u de app hebt geïmplementeerd. Login = *ontwikkelaar* en wachtwoord = *P\@zwaard1 gebruiken*. Zie de [inleidende zelfstudie](saas-dbpertenant-wingtip-app-overview.md) voor meer begeleiding.

![Verbinding maken met SQL Database-server vanuit SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

In de Object Verkenner:

1. Vouw de *server van&lt;tenants1-dpt- uit.&gt; *
1. Vouw het knooppunt Databases uit en bekijk de lijst met tenantdatabases.
1. Vouw de *server voor&lt;&gt; dpt-gebruikers uit.*
1. Controleer of u het analysearchief ziet met de volgende objecten:
    1. Tabellen **raw_Tickets,** **raw_Customers,** **raw_Events** en **raw_Venues** bevatten onbewerkte geëxtraheerde gegevens uit de tenantdatabases.
    1. De tabellen van het sterschema worden **fact_Tickets,** **dim_Customers,** **dim_Venues,** **dim_Events**en **dim_Dates**.
    1. De opgeslagen procedure, **sp_transformExtractedData** wordt gebruikt om de gegevens om te zetten en te laden in de ster-schema tabellen.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. Navigeer in de [Azure Portal](https://ms.portal.azure.com)naar de brongroep die u hebt gebruikt voor het implementeren van de toepassing. Controleer of een opslagaccount genaamd **wingtipstaging-gebruiker\<\> ** is toegevoegd.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Klik op het account voor gebruikersopslag van **wingtipstaging\<\> ** om de aanwezige objecten te verkennen.
1. Klik op de tegel **Klodders**
1. Klik op de **containerconfigfile**
1. Controleer of **configfile** een JSON-bestand bevat met de naam **TableConfig.json**. Dit bestand bevat de namen van de bron- en doeltabel, kolomnamen en de naam van de trackerkolom.

#### <a name="azure-data-factory-adf"></a>Azure-gegevensfabriek (ADF)
Controleer in de [Azure Portal](https://ms.portal.azure.com) in de brongroep of er een Azure Data Factory is toegevoegd die _\<dbtodwload-gebruiker\> _ wordt genoemd. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

In dit gedeelte wordt de gemaakte gegevensfabriek onderzocht. Volg de onderstaande stappen om de gegevensfabriek te starten:
1. Klik in de portal op de gegevensfabriek genaamd **dbtodwload-user.\<\>**
2. Klik **op & tegel Monitor maken** om de ontwerper van gegevensfabriek in een apart tabblad te starten. 

## <a name="extract-load-and-transform-data"></a>Gegevens extraheren, laden en transformeren
Azure Data Factory wordt gebruikt voor het orkestreren van extractie, laden en transformatie van gegevens. In deze zelfstudie haalt u gegevens uit vier verschillende SQL-weergaven uit elk van de tenantdatabases: **rawTickets,** **rawCustomers,** **rawEvents**en **rawVenues**. Deze weergaven omvatten locatie-id, zodat u gegevens van elke locatie in het gegevensmagazijn discrimineren. De gegevens worden geladen in overeenkomstige faseringstabellen in het gegevensmagazijn: **raw_Tickets**, **raw_customers**, **raw_Events** en **raw_Venue**. Een opgeslagen procedure transformeert vervolgens de ruwe gegevens en vult de tabellen voor het sterschema: **fact_Tickets**, **dim_Customers,** **dim_Venues**, **dim_Events**en **dim_Dates**.

In de vorige sectie hebt u de benodigde Azure-resources geïmplementeerd en geïnitialiseerd, inclusief de gegevensfabriek. De geïmplementeerde gegevensfabriek omvat de pijplijnen, gegevenssets, gekoppelde services, enz., die nodig zijn om de tenantgegevens te extraheren, te laden en te transformeren. Laten we deze objecten verder verkennen en vervolgens de pijplijn activeren om gegevens van tenantdatabases naar het gegevensmagazijn te verplaatsen.

### <a name="data-factory-pipeline-overview"></a>Overzicht van gegevensfabriekspijplijn
In dit gedeelte worden de objecten onderzocht die in de gegevensfabriek zijn gemaakt. In de volgende afbeelding wordt de algemene werkstroom van de ADF-pijplijn beschreven die in deze zelfstudie wordt gebruikt. Als u de pijplijn later wilt verkennen en eerst de resultaten wilt bekijken, gaat u naar de volgende sectie **Trigger the pipeline run**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Ga op de [overzichtspagina](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) over naar het tabblad **Auteur** in het linkerdeelvenster en kijk dat er drie pijplijnen en drie [gegevenssets](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) zijn gemaakt.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

De drie geneste pijplijnen zijn: SQLDBToDW, DBCopy en TableCopy.

**Pipeline 1 - SQLDBToDW** zoekt de namen op van de tenantdatabases die zijn opgeslagen in de catalogusdatabase (tabelnaam: [__ShardManagement].[ ShardsGlobal]) en voor elke tenantdatabase wordt de **DBCopy-pijplijn** uitgevoerd. Na voltooiing wordt het meegeleverde **sp_TransformExtractedData** opgeslagen procedureschema uitgevoerd. Met deze opgeslagen procedure worden de geladen gegevens in de faseringstabellen getransformeerd en worden de tabellen voor sterschema's gevuld.

**Pipeline 2 - DBCopy** zoekt de namen van de brontabellen en kolommen op uit een configuratiebestand dat is opgeslagen in blobopslag.  De **TableCopy-pijplijn** wordt vervolgens uitgevoerd voor elk van de vier tabellen: TicketFacts, CustomerFacts, EventFacts en VenueFacts. De **[Foreach-activiteit](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** wordt parallel uitgevoerd voor alle 20 databases. ADF maakt het mogelijk om maximaal 20 lusiteraties parallel uit te voeren. Overweeg meerdere pijplijnen te maken voor meer databases.    

**Pijplijn 3 - TableCopy** gebruikt rijversienummers in SQL Database _(rowversion)_ om rijen te identificeren die zijn gewijzigd of bijgewerkt. Met deze activiteit wordt de begin- en eindrijversie opgezocht voor het extraheren van rijen uit de brontabellen. De **CopyTracker-tabel** die in elke tenantdatabase is opgeslagen, houdt de laatste rij bij die uit elke brontabel in elke run is geëxtraheerd. Nieuwe of gewijzigde rijen worden gekopieerd naar de bijbehorende faseringstabellen in het gegevensmagazijn: **raw_Tickets,** **raw_Customers**, **raw_Venues**en **raw_Events**. Ten slotte wordt de laatste rijversie opgeslagen in de **CopyTracker-tabel** om te worden gebruikt als de eerste rijversie voor de volgende extractie. 

Er zijn ook drie geparameteriseerde gekoppelde services die de gegevensfabriek koppelen aan de bron SQL-databases, het doelSQL-gegevensmagazijn en de tussentijdse Blob-opslag. Klik op het tabblad **Auteur** op **Verbindingen** om de gekoppelde services te verkennen, zoals in de volgende afbeelding wordt weergegeven:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Als aanvulling op de drie gekoppelde services, zijn er drie gegevenssets die verwijzen naar de gegevens die u in de pijplijnactiviteiten gebruikt als invoer of uitvoer. Bekijk elk van de gegevenssets om de gebruikte verbindingen en parameters te observeren. _AzureBlob_ wijst naar het configuratiebestand met bron- en doeltabellen en -kolommen, evenals naar de trackerkolom in elke bron.
  
### <a name="data-warehouse-pattern-overview"></a>Overzicht van gegevensmagazijnen
SQL Data Warehouse wordt gebruikt als analysearchief om aggregatie uit te voeren op de tenantgegevens. In dit voorbeeld wordt PolyBase gebruikt om gegevens in het SQL Data-magazijn te laden. Ruwe gegevens worden geladen in faseringstabellen met een identiteitskolom om rijen bij te houden die zijn omgezet in de tabellen met sterschema's. De volgende afbeelding toont ![het laadpatroon: laadpatroon](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

In dit voorbeeld worden scd-dimensietabellen (SCD) langzaam wijzigen gebruikt. Elke dimensie heeft een surrogaatsleutel gedefinieerd met behulp van een identiteitskolom. Als aanbevolen praktijk is de tabel datumdimensie vooraf ingevuld om tijd te besparen. Voor de andere dimensietabellen wordt een TABEL MAKEN ALS SELECTEREN... (CTAS) instructie wordt gebruikt om een tijdelijke tabel met de bestaande gewijzigde en niet-gewijzigde rijen, samen met de surrogaat sleutels. Dit gebeurt met IDENTITY_INSERT=ON. Nieuwe rijen worden vervolgens ingevoegd in de tabel met IDENTITY_INSERT=UIT. Voor eenvoudige terugdraaiing wordt de bestaande dimensietabel hernoemd en wordt de tijdelijke tabel omgedoopt tot de nieuwe dimensietabel. Voor elke run wordt de oude dimensietabel verwijderd.

Dimensietabellen worden geladen vóór de feitentabel. Deze volgorde zorgt ervoor dat voor elk aankomend feit al alle genoemde dimensies bestaan. Als de feiten worden geladen, wordt de zakelijke sleutel voor elke overeenkomstige dimensie geëvenaard en worden de bijbehorende surrogaatsleutels aan elk feit toegevoegd.

De laatste stap van de transformatie verwijdert de faseringsgegevens die klaar zijn voor de volgende uitvoering van de pijplijn.
   
### <a name="trigger-the-pipeline-run"></a>De pijplijnrun activeren
Volg de onderstaande stappen om de volledige pijplijn voor het extraheren, laden en transformeren voor alle tenantdatabases uit te voeren:
1. Selecteer **SQLDBToDW-pijplijn** in het linkerdeelvenster op het tabblad **Auteur van** de ADF-gebruikersinterface.
1. Klik **op Trigger** en klik in het menu omlaag getrokken op Nu **activeren**. Met deze actie wordt de pijplijn onmiddellijk uitgevoerd. In een productiescenario definieert u een tijdschema voor het uitvoeren van de pijplijn om de gegevens op een planning te vernieuwen.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Klik op de pagina **Pijplijnrun** op **Voltooien**.
 
### <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.
1. Schakel in de ADF-gebruikersinterface over naar het tabblad **Monitor** in het menu aan de linkerkant.
1. Klik **op Vernieuwen** totdat de status van SQLDBToDW-pijplijn is **geslaagd.**
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Maak verbinding met het gegevensmagazijn met SSMS en query de sterschematabellen om te controleren of de gegevens in deze tabellen zijn geladen.

Zodra de pijplijn is voltooid, bevat de feitentabel ticketverkoopgegevens voor alle locaties en worden de dimensietabellen gevuld met de bijbehorende locaties, evenementen en klanten.

## <a name="data-exploration"></a>Gegevensverkenning

### <a name="visualize-tenant-data"></a>Tenantgegevens visualiseren

De gegevens in het sterschema bieden alle ticketverkoopgegevens die nodig zijn voor uw analyse. Door gegevens grafisch te visualiseren, u trends in grote gegevenssets gemakkelijker bekijken. In deze sectie gebruikt u **Power BI** om de tenantgegevens in het gegevensmagazijn te manipuleren en te visualiseren.

Gebruik de volgende stappen om verbinding te maken met Power BI en de weergaven te importeren die u eerder hebt gemaakt:

1. Start Power BI-bureaublad.
2. Selecteer op het lint Start de optie **Gegevens ophalen**en selecteer **Meer...** van het menu.
3. Selecteer **Azure SQL Database**in het venster Gegevens **opdoen** .
4. Voer in het aanmeldingsvenster van de database uw servernaam in **(catalogus-dpt-&lt;&gt;Gebruiker .database.windows.net**). Selecteer **Importeren** voor **de modus Gegevensconnectiviteit**en klik op **OK**. 

    ![aan-in-naar-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecteer **Database** in het linkerdeelvenster en voer vervolgens de gebruikersnaam in = *ontwikkelaar*en voer wachtwoord in = *\@P zwaard1*. Klik op **Verbinden**.  

    ![database-aanmelding](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Selecteer in het deelvenster **Navigator** onder de analysedatabase de tabellen met het sterschema: **fact_Tickets,** **dim_Events,** **dim_Venues,** **dim_Customers** en **dim_Dates**. Selecteer vervolgens **Laden**. 

Gefeliciteerd! U hebt de gegevens in Power BI geladen. Ontdek nu interessante visualisaties om inzicht te krijgen in uw huurders. Laten we eens kijken hoe analytics een aantal gegevensgestuurde aanbevelingen kan bieden aan het businessteam van Wingtip Tickets. De aanbevelingen kunnen helpen om het bedrijfsmodel en de klantervaring te optimaliseren.

Begin met het analyseren van ticketverkoopgegevens om de variatie in gebruik tussen de locaties te zien. Selecteer de opties in Power BI om een staafdiagram te plotten van het totale aantal tickets dat per locatie wordt verkocht. (Als gevolg van willekeurige variatie in de ticketgenerator kunnen uw resultaten anders zijn.)
 
![TotalTicketsByVenues TotalTicketsByVenues TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Het voorgaande plot bevestigt dat het aantal tickets dat per locatie wordt verkocht, varieert. Locaties die meer tickets verkopen, gebruiken uw service zwaarder dan locaties die minder tickets verkopen. Er kan hier een mogelijkheid zijn om de toewijzing van resources af te stemmen op basis van verschillende tenantbehoeften.

U de gegevens verder analyseren om te zien hoe de kaartverkoop in de loop van de tijd varieert. Selecteer de opties in de volgende afbeelding in Power BI om het totale aantal verkochte tickets per dag voor een periode van 60 dagen in kaart te brengen.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

De voorgaande grafiek laat zien dat de kaartverkoop piek voor sommige locaties. Deze pieken versterken het idee dat sommige locaties mogelijk onevenredig gebruik maken van systeembronnen. Tot nu toe is er geen duidelijk patroon in wanneer de spikes optreden.

Laten we vervolgens de betekenis van deze piekverkoopdagen onderzoeken. Wanneer ontstaan deze pieken nadat tickets in de verkoop gaan? Als u tickets wilt plotten die per dag worden verkocht, selecteert u de opties in de volgende afbeelding in Power BI.

![SaleDayDistributie](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Dit perceel toont aan dat sommige locaties verkopen grote aantallen tickets op de eerste dag van de verkoop. Zodra tickets gaan op de verkoop op deze locaties, lijkt er een gekke rush. Deze uitbarsting van activiteit door een paar locaties kan invloed hebben op de service voor andere huurders.

U opnieuw inzoomen op de gegevens om te zien of deze gekke rush geldt voor alle evenementen die worden gehost door deze locaties. In eerdere percelen zag je dat Contoso Concert Hall veel tickets verkoopt, en dat Contoso ook een piek heeft in de kaartverkoop op bepaalde dagen. Speel met Power BI-opties om de cumulatieve kaartverkoop voor Contoso Concert Hall te plotten, met de nadruk op verkooptrends voor elk van zijn evenementen. Volgen alle gebeurtenissen hetzelfde verkooppatroon? Probeer een plot te produceren zoals hieronder.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Dit perceel van cumulatieve kaartverkoop in de tijd voor Contoso Concert Hall voor elk evenement laat zien dat de gekke rush niet gebeurt voor alle evenementen. Speel met de filteropties om verkooptrends voor andere locaties te verkennen.

De inzichten in ticketverkooppatronen kunnen Wingtip Tickets ertoe brengen hun bedrijfsmodel te optimaliseren. In plaats van alle tenants gelijk te laden, moet Wingtip misschien servicelagen met verschillende rekengroottes introduceren. Grotere locaties die meer tickets per dag moeten verkopen, kunnen een hoger niveau worden aangeboden met een hogere servicelevelovereenkomst (SLA). Deze locaties kunnen hun databases in de pool plaatsen met hogere resourcelimieten per database. Elke servicelaag kan een verkooptoewijzing per uur hebben, waarbij extra kosten in rekening worden gebracht voor het overschrijden van de toewijzing. Grotere locaties met periodieke verkoopuitbarstingen zouden profiteren van de hogere lagen en Wingtip Tickets kunnen efficiënter inkomsten genereren met hun service.

Ondertussen klagen sommige Wingtip Tickets-klanten dat ze moeite hebben om genoeg tickets te verkopen om de servicekosten te rechtvaardigen. Misschien is er in deze inzichten een mogelijkheid om de kaartverkoop voor slecht presterende locaties te stimuleren. Hogere verkopen zouden de waargenomen waarde van de dienst verhogen. Klik met de rechtermuisknop op fact_Tickets en selecteer **Nieuwe meting**. Voer de volgende expressie in voor de nieuwe maatregel genaamd **AverageTicketsSold:**

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecteer de volgende visualisatieopties om het percentage tickets dat door elke locatie wordt verkocht, in kaart te brengen om hun relatieve succes te bepalen.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Het bovenstaande plot laat zien dat, hoewel de meeste locaties verkopen meer dan 80% van hun tickets, sommige worstelen om meer dan de helft van hun zetels te vullen. Speel met de Waarden Goed om maximaal of minimaal percentage verkochte tickets voor elke locatie te selecteren.

## <a name="embedding-analytics-in-your-apps"></a>Analytics insluiten in uw apps 
Deze zelfstudie is gericht op cross-tenant analyses die worden gebruikt om het begrip van de softwareleverancier van hun huurders te verbeteren. Analytics kan ook inzichten bieden aan de _huurders,_ om hen te helpen hun bedrijf zelf effectiever te beheren. 

In het voorbeeld van Wingtip Tickets ontdekte u eerder dat de kaartverkoop de neiging heeft om voorspelbare patronen te volgen. Dit inzicht kan worden gebruikt om slecht presterende locaties te helpen de kaartverkoop te stimuleren. Misschien is er een mogelijkheid om machine learning-technieken in te zetten om de kaartverkoop voor evenementen te voorspellen. De effecten van prijswijzigingen kunnen ook worden gemodelleerd, zodat de impact van het aanbieden van kortingen kan worden voorspeld. Power BI Embedded kan worden geïntegreerd in een toepassing voor eventmanagement om voorspelde verkopen te visualiseren, inclusief de impact van kortingen op het totale aantal verkochte stoelen en de inkomsten op laagverkopende evenementen. Met Power BI Embedded u zelfs de korting op de ticketprijzen daadwerkelijk integreren, direct in de visualisatie-ervaring.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Implementeer een SQL Data Warehouse gevuld met een sterschema voor tenantanalytics.
> * Gebruik Azure Data Factory om gegevens uit elke tenantdatabase te extraheren naar het magazijn voor analysegegevens.
> * Optimaliseer de geëxtraheerde gegevens (reorganiseren in een sterschema).
> * Bevraag het magazijn voor analysegegevens. 
> * Gebruik Power BI om trends in gegevens voor alle tenants te visualiseren.

Gefeliciteerd!

## <a name="additional-resources"></a>Aanvullende bronnen

- Extra [tutorials die voortbouwen op de Wingtip SaaS applicatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
