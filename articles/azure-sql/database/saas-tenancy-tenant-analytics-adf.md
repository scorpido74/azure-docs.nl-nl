---
title: Analytics-query's uitvoeren op Tenant databases
description: Cross-Tenant Analytics-query's die gebruikmaken van gegevens die zijn geëxtraheerd uit Azure SQL Database, Azure Synapse Analytics, Azure Data Factory of Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: fff308f241a29cbf40bf2884fc412acf5942497b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048803"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>Bekijk SaaS Analytics met Azure SQL Database, Azure Synapse Analytics, Data Factory en Power BI
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelf studie gaat u een end-to-end-analyse scenario door lopen. In het scenario ziet u hoe analyse via Tenant gegevens software leveranciers kan stimuleren om slimme beslissingen te nemen. Met behulp van gegevens die zijn geëxtraheerd uit elke Tenant database, gebruikt u Analytics om inzicht te krijgen in het gedrag van de Tenant, inclusief het gebruik van de Wingtip tickets SaaS-toepassing. Dit scenario bestaat uit drie stappen:

1. Gegevens uit elke Tenant database **extra heren** in een analytische opslag, in dit geval een SQL Data Warehouse.
2. **Optimaliseer de geëxtraheerde gegevens** voor analyse verwerking.
3. Gebruik hulpprogram ma's voor **bedrijfs informatie** om nuttige inzichten uit te tekenen, waarmee u besluit vorming kunt doen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Maak de Tenant-analyse opslag voor het laden van.
> - Gebruik Azure Data Factory (ADF) om gegevens van elke Tenant database in het Analytics-Data Warehouse te extra heren.
> - Optimaliseer de geëxtraheerde gegevens (Organiseer opnieuw in een ster schema).
> - Query's uitvoeren op het Analytics-Data Warehouse.
> - Gebruik Power BI voor gegevens visualisatie om trends in Tenant gegevens te markeren en aanbevelingen te doen voor verbeteringen.

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analytics over geëxtraheerde Tenant gegevens

SaaS-toepassingen bevatten een mogelijk grote hoeveelheid Tenant gegevens in de Cloud. Deze gegevens kunnen een uitgebreide bron van inzichten bieden over de werking en het gebruik van uw toepassing, en het gedrag van uw tenants. Met deze inzichten kunt u de ontwikkeling van functies, verbetering van de bruikbaarheid en andere investeringen in de apps en het platform begeleiden.

Het is eenvoudig om toegang te krijgen tot de gegevens voor alle tenants wanneer alle gegevens zich in slechts één data base met meerdere tenants bevinden. Access is echter ingewik kelder wanneer ze worden gedistribueerd op schaal in duizenden data bases. Een manier om de complexiteit te beheersenen is het extra heren van de gegevens naar een Analytics-Data Base of een Data Warehouse voor query's.

Deze zelf studie bevat een end-to-end-analyse scenario voor de Wingtip tickets-toepassing. First, [Azure Data Factory (ADF)](../../data-factory/introduction.md) wordt gebruikt als het Orchestration-hulp programma voor het uitpakken van kaarten verkoop en gerelateerde gegevens uit elke Tenant database. Deze gegevens worden in een analyse archief geladen in faserings tabellen. Het analyse archief kan een SQL Database of een SQL Data Warehouse zijn. In deze zelf studie wordt [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) gebruikt als het Analytics-archief.

Vervolgens worden de geëxtraheerde gegevens getransformeerd en geladen in een set [ster-schema](https://www.wikipedia.org/wiki/Star_schema) tabellen. De tabellen bestaan uit een centrale feiten tabel plus gerelateerde dimensie tabellen:

- De centrale feiten tabel in het ster schema bevat ticket gegevens.
- De dimensie tabellen bevatten gegevens over locaties, gebeurtenissen, klanten en aankoop datums.

De centrale en dimensie tabellen kunnen samen een efficiënte analyse verwerking hebben. Het ster schema dat in deze zelf studie wordt gebruikt, wordt weer gegeven in de volgende afbeelding:

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

Ten slotte worden de tabellen van het ster schema opgevraagd. Query resultaten worden visueel weer gegeven met Power BI om inzicht te krijgen in het Tenant gedrag en het gebruik van de toepassing. Met dit ster schema voert u query's uit die beschikbaar zijn:

- Wie is verantwoordelijk voor het kopen van tickets en van welke locaties.
- Patronen en trends in de verkoop van tickets.
- De relatieve populariteit van elke locatie.

In deze zelf studie vindt u eenvoudige voor beelden van inzichten die kunnen worden afgelezen uit de Wingtip tickets-gegevens. Als u wilt weten hoe elke locatie de service gebruikt, kan de leverancier van de Wingtip tickets nadenken over verschillende service plannen die gericht zijn op meer of minder actieve locaties.

## <a name="setup"></a>Instellen

### <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet u ervoor zorgen dat aan de volgende vereisten is voldaan:

- De Wingtip tickets SaaS-data base per Tenant toepassing wordt geïmplementeerd. Zie [de Wingtip SaaS-toepassing implementeren en verkennen](../../sql-database/saas-dbpertenant-get-started-deploy.md)om in minder dan vijf minuten te implementeren.
- De Wingtip tickets SaaS-data base per Tenant scripts en toepassings [bron code](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) worden gedownload van github. Zie Download instructies. Zorg ervoor dat u *het zip-bestand deblokkeren* voordat u de inhoud uitpakt.
- Power BI Desktop is geïnstalleerd. [Down load Power bi Desktop](https://powerbi.microsoft.com/downloads/).
- De batch met aanvullende tenants is ingericht. Raadpleeg de [**zelf studie voor het inrichten van tenants**](../../sql-database/saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Gegevens maken voor de demo

In deze zelf studie worden de verkoop gegevens van Analytics over ticket verkend. In deze stap genereert u ticket gegevens voor alle tenants. In een latere stap worden deze gegevens geëxtraheerd voor analyse. _Zorg ervoor dat u de batch met tenants hebt ingericht_ (zoals eerder beschreven), zodat u over voldoende gegevens beschikt om een aantal verschillende inkopende patronen voor tickets beschikbaar te maken.

1. Open *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*in Power shell ISE en stel de volgende waarde in:
    - **$DemoScenario**  =  **1** koop tickets voor gebeurtenissen op alle locaties
2. Druk op **F5** om het script uit te voeren en een ticket-inkoop geschiedenis te maken voor alle locaties. Met 20 tenants genereert het script tien duizenden tickets en kan dit 10 minuten of langer duren.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>SQL Data Warehouse, Data Factory en Blob Storage implementeren

In de Wingtip tickets-app worden de transactionele gegevens van de tenants gedistribueerd over veel data bases. Azure Data Factory (ADF) wordt gebruikt om de extract, Load en Transform (ELT) van deze gegevens in het Data Warehouse te organiseren. Voor het laden van gegevens naar SQL Data Warehouse meest efficiënte, laadt ADF gegevens naar tussenliggende BLOB-bestanden en gebruikt [poly base](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) om de gegevens in het Data Warehouse te laden.

In deze stap implementeert u de extra resources die worden gebruikt in de zelf studie: een SQL Data Warehouse met de naam _tenantanalytics_, een Azure Data Factory _met \<user\> _de naam dbtodwload en een Azure Storage-account met de naam _wingtipstaging \<user\> _. Het opslag account wordt gebruikt voor het tijdelijk vasthouden van geëxtraheerde gegevens bestanden als blobs voordat ze in het Data Warehouse worden geladen. Met deze stap wordt ook het Data Warehouse-schema geïmplementeerd en worden de ADF-pijp lijnen gedefinieerd waarmee het ELT-proces wordt georganiseerd.

1. Open in Power shell ISE *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* en stel het volgende in:
    - **$DemoScenario**  =  **2** Tenant-Analytics-Data Warehouse, Blob-opslag en Data Factory implementeren
1. Druk op **F5** om het demo script uit te voeren en de Azure-resources te implementeren.

Bekijk nu de Azure-resources die u hebt geïmplementeerd:

#### <a name="tenant-databases-and-analytics-store"></a>Tenant databases en analyse opslag

Gebruik [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) om verbinding te maken met **tenants1-dpt- &lt; gebruikers &gt; ** -en **catalogus-dpt- &lt; gebruikers &gt; ** servers. Vervang de &lt; gebruiker &gt; door de waarde die wordt gebruikt bij het implementeren van de app. Gebruik aanmelden = *ontwikkel aars* en wacht woord = *P \@ ssword1*. Raadpleeg de [inleidende zelf studie](../../sql-database/saas-dbpertenant-wingtip-app-overview.md) voor meer informatie.

![Verbinding maken met SQL Database van SSMS](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

In de Objectverkenner:

1. Vouw de *tenants1-dpt- &lt; User &gt; * server uit.
1. Vouw het knoop punt data bases uit en Bekijk de lijst met Tenant databases.
1. Vouw de *catalogus-dpt- &lt; gebruikers &gt; * server uit.
1. Controleer of u de Analytics Store ziet met de volgende objecten:
    1. Tabellen **raw_Tickets**, **raw_Customers**, **raw_Events** en **raw_Venues** onbewerkte gegevens uit de Tenant databases bevatten.
    1. De tabellen in het ster schema zijn **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**en **dim_Dates**.
    1. De opgeslagen procedure **sp_transformExtractedData** wordt gebruikt om de gegevens te transformeren en te laden in de ster-schema tabellen.

![DWtables](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage

1. Navigeer in het [Azure Portal](https://ms.portal.azure.com)naar de resource groep die u hebt gebruikt voor het implementeren van de toepassing. Controleer of er een opslag account met de naam **wingtipstaging \<user\> ** is toegevoegd.

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. Klik **op \<user\> wingtipstaging** opslag account om de aanwezige objecten te verkennen.
1. Klik op de tegel **blobs**
1. Klik op de container **configfile**
1. Controleer of **configfile** een JSON-bestand met de naam **TableConfig. json**bevat. Dit bestand bevat de namen van de bron-en doel tabel, kolom namen en de naam van de tracker-kolom.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)

Controleer in de [Azure Portal](https://ms.portal.azure.com) in de resource groep of een Azure Data Factory met de naam _ \<user\> dbtodwload_ is toegevoegd.

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

In deze sectie worden de data factory gemaakt.
Volg de onderstaande stappen om de data factory te starten:

1. Klik in de portal op de data factory **met de naam \<user\> dbtodwload-**.
2. Klik op de tegel **& monitor** om de Data Factory Designer op een afzonderlijk tabblad te openen.

## <a name="extract-load-and-transform-data"></a>Gegevens uitpakken, laden en transformeren

Azure Data Factory wordt gebruikt voor het organiseren van extractie, het laden en het transformeren van gegevens. In deze zelf studie haalt u gegevens op uit vier verschillende SQL-weer gaven van elk van de Tenant databases: **rawTickets**, **rawCustomers**, **rawEvents**en **rawVenues**. Deze weer gaven bevatten de locatie-ID, zodat u gegevens kunt van elke locatie in het Data Warehouse. De gegevens worden geladen in de bijbehorende faserings tabellen in het Data Warehouse: **raw_Tickets**, **raw_customers**, **raw_Events** en **raw_Venue**. In een opgeslagen procedure worden de onbewerkte gegevens getransformeerd en worden de ster-schema tabellen ingevuld: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**en **dim_Dates**.

In de vorige sectie hebt u de benodigde Azure-resources geïmplementeerd en geïnitialiseerd, met inbegrip van de data factory. De geïmplementeerde data factory omvat de pijp lijnen, gegevens sets, gekoppelde services, enzovoort, die vereist zijn om de Tenant gegevens uit te pakken, te laden en te transformeren. Laten we deze objecten verder verkennen en vervolgens de pijp lijn activeren om gegevens van Tenant databases naar het Data Warehouse te verplaatsen.

### <a name="data-factory-pipeline-overview"></a>Overzicht van Data Factory-pijp lijn

In deze sectie vindt u een onderzoek van de objecten die zijn gemaakt in de data factory. In de volgende afbeelding wordt de algehele werk stroom van de ADF-pijp lijn beschreven die in deze zelf studie wordt gebruikt. Als u de pijp lijn later wilt verkennen en de resultaten eerst wilt bekijken, gaat u naar de volgende sectie **activeren van de pijplijn uitvoering**.

![adf_overview](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

Ga op de pagina overzicht naar het tabblad **Auteur** in het linkerdeel venster en kijk of er drie [pijp lijnen](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) en drie [gegevens sets](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) zijn gemaakt.
![adf_author](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

De drie geneste pijp lijnen zijn: SQLDBToDW, DBCopy en TableCopy.

**Pijp lijn 1-SQLDBToDW** zoekt de namen van de Tenant databases op die zijn opgeslagen in de catalogus database (tabel naam: [__ShardManagement]. [ ShardsGlobal]) en voor elke Tenant database voert de **DBCopy** -pijp lijn uit. Wanneer het is voltooid, wordt het gegeven **sp_TransformExtractedData** opgeslagen procedure schema uitgevoerd. Met deze opgeslagen procedure worden de geladen gegevens in de faserings tabellen getransformeerd en worden de ster-schema tabellen ingevuld.

**Pijp lijn 2-DBCopy** zoekt de namen van de bron tabellen en kolommen op uit een configuratie bestand dat is opgeslagen in Blob Storage.  De **TableCopy** -pijp lijn wordt vervolgens uitgevoerd voor elk van de vier tabellen: TicketFacts, CustomerFacts, EventFacts en VenueFacts. De **[foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** -activiteit wordt parallel uitgevoerd voor alle 20 data bases. Bij ADF kunnen Maxi maal 20 loop-iteraties parallel worden uitgevoerd. Overweeg meerdere pijp lijnen te maken voor meer data bases.

**Pijp lijn 3-TableCopy** gebruikt rij-versie nummers in SQL database (_rowversion_) om rijen te identificeren die zijn gewijzigd of bijgewerkt. Deze activiteit zoekt naar de begin-en eind versie van de rij voor het extra heren van rijen uit de bron tabellen. De **CopyTracker** -tabel die is opgeslagen in elke Tenant database, houdt de laatste rij bij die is geëxtraheerd uit elke bron tabel in elke uitvoering. Nieuwe of gewijzigde rijen worden gekopieerd naar de bijbehorende faserings tabellen in het Data Warehouse: **raw_Tickets**, **raw_Customers**, **raw_Venues**en **raw_Events**. Ten slotte wordt de laatste rij-versie opgeslagen in de **CopyTracker** -tabel die moet worden gebruikt als de eerste rij-versie voor de volgende extractie.

Er zijn ook drie para meters gekoppelde services die de data factory koppelen aan de SQL-bron database, het doel SQL Data Warehouse en de tussenliggende Blob-opslag. Klik op het tabblad **Auteur** op **verbindingen** om de gekoppelde services te verkennen, zoals wordt weer gegeven in de volgende afbeelding:

![adf_linkedservices](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

Met betrekking tot de drie gekoppelde services zijn er drie gegevens sets die verwijzen naar de gegevens die u in de pijplijn activiteiten gebruikt als invoer of uitvoer. Verken elk gegevens sets om verbindingen en gebruikte para meters te observeren. _AzureBlob_ verwijst naar het configuratie bestand dat de bron-en doel tabellen en kolommen bevat, evenals de kolom tracker in elke bron.
  
### <a name="data-warehouse-pattern-overview"></a>Overzicht van het Data Warehouse-patroon

Azure Synapse (voorheen Azure SQL Data Warehouse) wordt gebruikt als de analyse opslag voor het uitvoeren van aggregatie op de gegevens van de Tenant. In dit voor beeld wordt poly base gebruikt voor het laden van gegevens in het Data Warehouse. Onbewerkte gegevens worden in faserings tabellen geladen die een identiteits kolom hebben voor het bijhouden van rijen die zijn getransformeerd naar de ster-schema tabellen. De volgende afbeelding toont het laad patroon: ![ loadingpattern](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

Langzaam veranderende dimensie (SCD) type 1 dimensie tabellen worden gebruikt in dit voor beeld. Elke dimensie heeft een surrogaat sleutel die is gedefinieerd met behulp van een identiteits kolom. Als best practice is de datum dimensie tabel vooraf ingevuld om tijd te besparen. Voor de andere dimensie tabellen, een CREATE TABLE als selecteren... (CTAS) wordt gebruikt voor het maken van een tijdelijke tabel die de bestaande gewijzigde en niet-gewijzigde rijen bevat, samen met de surrogaat sleutels. Dit wordt gedaan met IDENTITY_INSERT = op. Nieuwe rijen worden vervolgens ingevoegd in de tabel met IDENTITY_INSERT = uit. Voor een gemakkelijk terugdraaien wordt de naam van de bestaande dimensie tabel gewijzigd en wordt de naam van de tijdelijke tabel gewijzigd in de nieuwe dimensie tabel. Voor elke uitvoering wordt de oude dimensie tabel verwijderd.

Dimensie tabellen worden vóór de feiten tabel geladen. Deze volg orde zorgt ervoor dat alle dimensies waarnaar wordt verwezen al bestaan voor elk nakomend feit. Wanneer de feiten worden geladen, wordt de bedrijfs sleutel voor elke overeenkomende dimensie vergeleken en worden de bijbehorende surrogaat sleutels aan elk feit toegevoegd.

De laatste stap van de trans formatie verwijdert de faserings gegevens die gereed zijn voor de volgende uitvoering van de pijp lijn.

### <a name="trigger-the-pipeline-run"></a>De pijplijn uitvoering activeren

Volg de onderstaande stappen om de pijp lijn volledige uitpakken, laden en transformeren uit te voeren voor alle Tenant databases:

1. Op het tabblad **Auteur** van de gebruikers interface van ADF selecteert u **SQLDBToDW** -pijp lijn in het linkerdeel venster.
1. Klik op **trigger** en klik in het menu omlaag op **nu activeren**. Met deze actie wordt de pijp lijn onmiddellijk uitgevoerd. In een productie scenario definieert u een tijd schema voor het uitvoeren van de pijp lijn voor het vernieuwen van de gegevens op basis van een schema.
  ![adf_trigger](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. Klik op de pagina **pijplijn uitvoering** op **volt ooien**.

### <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. In de gebruikers interface van ADF gaat u naar het tabblad **monitor** in het menu aan de linkerkant.
1. Klik op **vernieuwen** totdat de status van de SQLDBToDW-pijp lijn is **geslaagd**.
  ![adf_monitoring](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. Maak verbinding met het data warehouse met SSMS en zoek de tabellen in het ster schema om te controleren of de gegevens in deze tabellen zijn geladen.

Zodra de pijp lijn is voltooid, bevat de feiten tabel ticket verkoop gegevens voor alle locaties en worden de dimensie tabellen gevuld met de overeenkomstige locaties, gebeurtenissen en klanten.

## <a name="data-exploration"></a>Gegevens verkennen

### <a name="visualize-tenant-data"></a>Tenant gegevens visualiseren

De gegevens in het ster schema bevatten alle verkoop gegevens van tickets die nodig zijn voor uw analyse. Door gegevens grafisch te visualiseren, kunt u sneller trends in grote gegevens sets bekijken. In deze sectie gebruikt u **Power bi** om de Tenant gegevens in het Data Warehouse te bewerken en visualiseren.

Gebruik de volgende stappen om verbinding te maken met Power BI en om de weer gaven te importeren die u eerder hebt gemaakt:

1. Start Power BI bureau blad.
2. Selecteer op het lint start de optie **gegevens ophalen**en selecteer **meer...** in het menu.
3. Selecteer **Azure SQL database**in het venster **gegevens ophalen** .
4. Voer in het venster Data Base-aanmeldingen de naam van uw server in (**Catalog-dpt- &lt; User &gt; . database.Windows.net**). Selecteer **importeren** voor de **gegevens connectiviteits modus**en klik vervolgens op **OK**.

    ![aanmelden bij Power bi](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. Selecteer **Data Base** in het linkerdeel venster, voer gebruikers naam = *ontwikkelaar*in en voer wacht woord = *P \@ ssword1*in. Klik op **Verbinden**.  

    ![data base-aanmelden](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. Selecteer in het deel venster **Navigator** onder de Analytics-Data Base de ster-schema tabellen: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** en **dim_Dates**. Selecteer vervolgens **laden**.

Gefeliciteerd De gegevens zijn geladen in Power BI. Bekijk nu interessante visualisaties om inzicht te krijgen in uw tenants. Laten we eens kijken hoe analyses een aantal gegevensgestuurde aanbevelingen kan bieden aan het Business team van de Wingtip tickets. De aanbevelingen kunnen helpen het bedrijfs model en de klant ervaring te optimaliseren.

Begin met het analyseren van de verkoop gegevens van het ticket om de variatie in het gebruik over de locaties te bekijken. Selecteer de opties in Power BI om een staaf diagram af te zetten van het totale aantal tickets dat door elke locatie wordt verkocht. (Vanwege wille keurige variatie in de ticket generator, kunnen de resultaten afwijken.)

![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

In het voor gaande waarnemings punt wordt bevestigd dat het aantal tickets dat door elke locatie wordt verkocht, varieert. Locaties waarbij meer tickets worden verkocht, maken gebruik van uw service meer sterk dan locaties die minder tickets verkopen. Er is mogelijk een kans om de toewijzing van resources aan te passen op basis van de verschillende behoeften van de Tenant.

U kunt de gegevens verder analyseren om te zien hoe de ticket omzet gedurende een periode verschilt. Selecteer de opties die worden weer gegeven in de volgende afbeelding in Power BI om het totale aantal tickets te zetten dat elke dag gedurende een periode van 60 dagen is verkocht.

![SaleVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

In het voor gaande diagram ziet u dat de verkoop piek van het ticket voor sommige locaties. Deze pieken versterken het idee dat sommige locaties mogelijk systeem bronnen onevenredig verbruiken. Tot nu toe is er geen duidelijk patroon in wanneer de pieken optreden.

Nu gaan we de significantie van deze piek verkoop dagen onderzoeken. Wanneer komen deze pieken voor nadat tickets aan de verkoop gaan? Als u kaarten wilt uitzetten die per dag worden verkocht, selecteert u de opties die worden weer gegeven in de volgende afbeelding in Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

In dit waarnemings punt ziet u dat sommige locaties een groot aantal tickets verkopen op de eerste dag van de verkoop. Zodra tickets op deze locaties aan de verkoop gaan, lijkt het alsof er sprake is van een Made spoed. Deze burst-activiteit door enkele locaties kan van invloed zijn op de service voor andere tenants.

U kunt opnieuw inzoomen op de gegevens om te zien of deze Mad spoed waar is voor alle gebeurtenissen die worden gehost door deze locaties. In de vorige waarnemings punten zag u dat contoso concert een groot aantal tickets verkoopt en dat contoso ook over bepaalde dagen een piek in de ticket verkopen heeft. U kunt met behulp van Power BI opties spelen om de cumulatieve ticket verkoop voor contoso-concert zaal uit te zetten, zodat u zich kunt concentreren op trends in de verkoop voor elk van de gebeurtenissen. Volgen alle gebeurtenissen hetzelfde verkoop patroon? Probeer een tekening te maken zoals hieronder.

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

In deze fase van de cumulatieve ticket verkoop gedurende de loop tijd voor elke gebeurtenis blijkt dat de Mad spoed niet voor alle gebeurtenissen plaatsvindt. U kunt met de filter opties spelen om de verkoop trends voor andere evenementen te verkennen.

De inzichten in de verkoop patronen van het ticket kunnen leiden tot Wingtip tickets om hun bedrijfs model te optimaliseren. In plaats van alle tenants gelijkmatig te laden, kunnen Wingtip mogelijk service lagen introduceren met verschillende reken grootten. Grotere locaties die meer tickets per dag moeten verkopen, kunnen een hogere laag met een hoger service level agreement (SLA) worden aangeboden. Deze locaties kunnen hun data bases in een pool plaatsen met meer resource limieten per data base. Elke servicelaag kan een verkoop toewijzing per uur hebben, waarbij extra kosten in rekening worden gebracht voor het overschrijden van de toewijzing. Grotere locaties met periodieke pieken in de verkoop zouden profiteren van de hogere lagen en Wingtip tickets kunnen hun service efficiënter geld verdienen.

In de tussen tijd kunnen klanten met een Wingtip-tickets een klacht doen om voldoende tickets te verkopen om de service kosten te rechtvaardigen. Misschien is er in deze inzichten een kans om de verkoop van tickets te stimuleren voor het uitvoeren van locaties. Bij een hogere verkoop wordt de waargenomen waarde van de service verhoogd. Klik met de rechter muisknop op fact_Tickets en selecteer **nieuwe meting**. Voer de volgende expressie in voor de nieuwe meting met de naam **AverageTicketsSold**:

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecteer de volgende visualisatie opties om het percentage tickets af te zetten dat door elke locatie wordt verkocht om het relatieve succes te bepalen.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

In het bovenstaande diagram ziet u dat hoewel de meeste locaties meer dan 80% van hun tickets verkopen, sommige lastig meer dan de helft van hun stoelen vullen. Speel met de waarden goed om het maximum of minimum percentage van tickets te selecteren dat voor elke locatie wordt verkocht.

## <a name="embedding-analytics-in-your-apps"></a>Analyses in uw apps insluiten

Deze zelf studie is gericht op cross-Tenant analyse die wordt gebruikt om de inzichten van de software leverancier van hun tenants te verbeteren. Analytics kan ook inzicht geven in de _tenants_, om hen te helpen hun bedrijf effectiever te beheren.

In het voor beeld van Wingtip-tickets hebt u eerder ontdekt dat de ticket verkoop vaak voorspel bare patronen volgt. Deze inzichten kunnen worden gebruikt om locaties te helpen bij het uitvoeren van een hogere ticket verkoop. Misschien kunt u machine learning technieken gebruiken om de verkoop van tickets voor gebeurtenissen te voors pellen. De gevolgen van prijs wijzigingen kunnen ook worden gemodelleerd, zodat de impact van het aanbieden van kortingen kan worden voor speld. Power BI Embedded kan worden geïntegreerd in een toepassing voor gebeurtenis beheer om voorspellende omzet te visualiseren, met inbegrip van de impact van kortingen op het totale aantal verkochte seats en omzet bij gebeurtenissen met een lage verkoop. Met Power BI Embedded kunt u zelfs de korting op de prijzen van het ticket, direct in de visualisatie-ervaring, integreren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> - Maak de Tenant-analyse opslag voor het laden van.
> - Gebruik Azure Data Factory (ADF) om gegevens van elke Tenant database in het Analytics-Data Warehouse te extra heren.
> - Optimaliseer de geëxtraheerde gegevens (Organiseer opnieuw in een ster schema).
> - Query's uitvoeren op het Analytics-Data Warehouse.
> - Gebruik Power BI voor gegevens visualisatie om trends in Tenant gegevens te markeren en aanbevelingen te doen voor verbeteringen.

Gefeliciteerd

## <a name="additional-resources"></a>Aanvullende bronnen

- Aanvullende [zelf studies die voortbouwen op de Wingtip SaaS-toepassing](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
