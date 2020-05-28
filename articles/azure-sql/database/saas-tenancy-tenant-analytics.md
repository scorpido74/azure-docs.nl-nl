---
title: Cross-Tenant analyse met geëxtraheerde gegevens
description: Cross-Tenant analyse query's die gebruikmaken van gegevens die zijn geëxtraheerd uit meerdere Azure SQL-data bases in één Tenant-app.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 3078cf7a7b16d5932e861f28a352002aa01cea45
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041992"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Cross-Tenant analyse met geëxtraheerde gegevens-app met één Tenant
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
In deze zelf studie gaat u een volledig analyse scenario door lopen voor een implementatie van één Tenant. In het scenario wordt gedemonstreerd hoe u met analyses slimme beslissingen kunt nemen. Met behulp van gegevens die zijn geëxtraheerd uit elke Tenant database, gebruikt u Analytics om inzicht te krijgen in het gedrag van de Tenant, inclusief het gebruik van de Wingtip tickets SaaS-toepassing. Dit scenario bestaat uit drie stappen: 

1.  Gegevens uit elke Tenant database **extra heren** en **laden** in een analytische opslag.
2.  **De geëxtraheerde gegevens** voor analyse verwerking transformeren.
3.  Gebruik **Business Intelligence** -hulpprogram ma's om nuttige inzichten uit te tekenen, waarmee u besluit vorming kunt doen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Maak de Tenant-analyse opslag voor het uitpakken van de gegevens in.
> - Gebruik elastische taken voor het extra heren van gegevens van elke Tenant database in de analyse opslag.
> - Optimaliseer de geëxtraheerde gegevens (Organiseer opnieuw in een ster schema).
> - Query's uitvoeren op de Analytics-Data Base.
> - Gebruik Power BI voor gegevens visualisatie om trends in Tenant gegevens te markeren en aanbevelingen te doen voor verbeteringen.

![architectureOverView](./media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Patroon voor offline Tenant analyse

SaaS-toepassingen met meerdere tenants hebben doorgaans een grote hoeveelheid Tenant gegevens die in de Cloud zijn opgeslagen. Deze gegevens bieden een uitgebreide bron van inzichten over de werking en het gebruik van uw toepassing en het gedrag van uw tenants. Met deze inzichten kunt u de ontwikkeling van functies, verbetering van de bruikbaarheid en andere investeringen in de app en het platform begeleiden.

Het is eenvoudig om toegang te krijgen tot gegevens voor alle tenants wanneer alle gegevens zich in slechts één multi tenant-data base bevinden. Maar de toegang is complexer wanneer deze op schaal wordt gedistribueerd over mogelijk duizenden data bases. Een manier om de complexiteit te beheersen en om de impact van analyse query's op transactionele gegevens te minimaliseren, is het extra heren van gegevens in een Data Base die is ontworpen voor analyse, of Data Warehouse.

In deze zelf studie wordt een volledig analyse scenario voor Wingtip tickets SaaS-toepassing weer gegeven. Eerst worden *elastische taken* gebruikt om gegevens uit elke Tenant database op te halen en te laden in faserings tabellen in een analytische opslag. Het analyse archief kan een SQL Database of een SQL Data Warehouse zijn. Voor grootschalige gegevens extractie wordt [Azure Data Factory](../../data-factory/introduction.md) aanbevolen.

Vervolgens worden de geaggregeerde gegevens omgezet in een set [ster-schema](https://www.wikipedia.org/wiki/Star_schema) tabellen. De tabellen bestaan uit een centrale feiten tabel plus gerelateerde dimensie tabellen.  Voor Wingtip-tickets:

- De centrale feiten tabel in het ster schema bevat ticket gegevens.
- De dimensie tabellen beschrijven locaties, gebeurtenissen, klanten en aankoop datums.

De centrale feiten-en dimensie tabellen hebben samen een efficiënte analyse verwerking. Het ster schema dat in deze zelf studie wordt gebruikt, wordt in de volgende afbeelding weer gegeven:
 
![architectureOverView](./media/saas-tenancy-tenant-analytics/StarSchema.png)

Ten slotte wordt het analyse archief opgevraagd met behulp van **Power bi** om inzicht te krijgen in het gedrag van de Tenant en het gebruik van de Wingtip tickets-toepassing. U voert query's uit die:
 
- De relatieve populariteit van elke locatie weer geven
- Patronen markeren in de verkoop van tickets voor verschillende gebeurtenissen
- Het relatieve succes van verschillende locaties weer geven bij het verkopen van de gebeurtenis

Meer informatie over hoe elke Tenant gebruikmaakt van de service wordt gebruikt om opties voor inkomsten de service te verkennen en de service te verbeteren om tenants te helpen slagen. In deze zelf studie vindt u eenvoudige voor beelden van inzichten die kunnen worden afgelezen uit Tenant gegevens.

## <a name="setup"></a>Instellen

### <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet u ervoor zorgen dat aan de volgende vereisten is voldaan:

- De Wingtip tickets SaaS-data base per Tenant toepassing wordt geïmplementeerd. Zie [de Wingtip SaaS-toepassing implementeren en verkennen](../../sql-database/saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren
- De Wingtip tickets SaaS-data base per Tenant scripts en toepassings [bron code](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) worden gedownload van github. Zie Download instructies. Zorg ervoor dat u *het zip-bestand deblokkeren* voordat u de inhoud uitpakt. Bekijk de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en blok keren van de Wingtip tickets SaaS-scripts.
- Power BI Desktop is geïnstalleerd. [Power BI Desktop downloaden](https://powerbi.microsoft.com/downloads/)
- De batch met aanvullende tenants is ingericht. Raadpleeg de [**zelf studie voor het inrichten van tenants**](../../sql-database/saas-dbpertenant-provision-and-catalog.md).
- Er is een taak account en een Data Base met taak accounts gemaakt. Zie de juiste stappen in de [**zelf studie schema beheer**](../../sql-database/saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Gegevens maken voor de demo

In deze zelf studie wordt de analyse uitgevoerd op de verkoop gegevens van het ticket. In de huidige stap genereert u ticket gegevens voor alle tenants.  Later worden deze gegevens geëxtraheerd voor analyse. *Zorg ervoor dat u de batch met tenants hebt ingericht zoals eerder beschreven, zodat u een zinvolle hoeveelheid gegevens hebt*. Een voldoende grote hoeveelheid gegevens kan een aantal verschillende inkopende patronen voor tickets bieden.

1. Open *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*in Power shell ISE en stel de volgende waarde in:
    - **$DemoScenario**  =  **1** koop tickets voor gebeurtenissen op alle locaties
2. Druk op **F5** om het script uit te voeren en een ticket-inkoop geschiedenis te maken voor elke gebeurtenis in elke locatie.  Het script wordt gedurende enkele minuten uitgevoerd om tien duizenden tickets te genereren.

### <a name="deploy-the-analytics-store"></a>De Analytics Store implementeren
Vaak bestaan er talrijke transactionele data bases die samen alle Tenant gegevens bevatten. U moet de Tenant gegevens van de vele transactionele data bases in één analyse archief samen voegen. De aggregatie maakt een efficiënte query van de gegevens mogelijk. In deze zelf studie wordt een Azure SQL Database gebruikt om de geaggregeerde gegevens op te slaan.

In de volgende stappen implementeert u de Analytics Store, die **tenantanalytics**wordt genoemd. U kunt ook vooraf gedefinieerde tabellen implementeren die verderop in de zelf studie worden ingevuld:
1. Open in Power shell ISE *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Stel de variabele $DemoScenario in het script in die overeenkomt met uw keuze uit Analytics Store:
    - Als u SQL database wilt gebruiken zonder kolom opslag, stelt u **$DemoScenario**  =  **2** in
    - Als u SQL database met een kolom archief wilt gebruiken, stelt u **$DemoScenario**  =  **3** in  
3. Druk op **F5** om het demo script uit te voeren (waarmee het script *Deploy-TenantAnalytics \<XX> . ps1* wordt aangeroepen) waarmee de Tenant-analyse opslag wordt gemaakt. 

Nu u de toepassing hebt geïmplementeerd en deze hebt gevuld met interessante Tenant gegevens, gebruikt [u SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) om verbinding te maken met **tenants1-dpt- &lt; &gt; gebruikers** -en **catalogus-dpt- &lt; gebruikers &gt; ** servers met behulp van login = *Developer*, password = *P \@ ssword1*. Raadpleeg de [inleidende zelf studie](../../sql-database/saas-dbpertenant-wingtip-app-overview.md) voor meer informatie.

![architectureOverView](./media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

Voer de volgende stappen uit in de Objectverkenner:

1. Vouw de *tenants1-dpt- &lt; User &gt; * server uit.
2. Vouw het knoop punt data bases uit en Bekijk de lijst met Tenant databases.
3. Vouw de *catalogus-dpt- &lt; gebruikers &gt; * server uit.
4. Controleer of u de analytische Store en de jobaccount-data base ziet.

Zie de volgende data base-items in de SSMS-Objectverkenner door het knoop punt Analytics Store uit te vouwen:

- Tabellen **TicketsRawData** en **EventsRawData** bevatten onbewerkte gegevens uit de Tenant-data bases.
- De tabellen in het ster schema zijn **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**en **dim_Dates**.
- De opgeslagen procedure wordt gebruikt voor het vullen van de ster-schema tabellen uit de onbewerkte gegevens tabellen.

![architectureOverView](./media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Gegevens extractie 

### <a name="create-target-groups"></a>Doel groepen maken 

Voordat u doorgaat, moet u ervoor zorgen dat u het taak account en de jobaccount-Data Base hebt geïmplementeerd. In de volgende reeks stappen wordt elastische taken gebruikt voor het extra heren van gegevens uit elke Tenant database en voor het opslaan van de gegevens in de analyse opslag. De tweede taak Shreds de gegevens en slaat deze op in de tabellen in het ster schema. Deze twee taken worden uitgevoerd op twee verschillende doel groepen, namelijk **TenantGroup** en **AnalyticsGroup**. De uitpak taak wordt uitgevoerd op de TenantGroup, die alle Tenant databases bevat. De verwerkings taak wordt uitgevoerd op de AnalyticsGroup, die alleen de analytische opslag bevat. Maak de doel groepen met behulp van de volgende stappen:

1. In SSMS maakt u verbinding met de **jobaccount** -data base in de catalogus-dpt- &lt; gebruiker &gt; .
2. Open in SSMS *. ..\Learning Modules\Operational Analytics\Tenant Analytics \ TargetGroups. SQL* 
3. Wijzig de @User variabele boven aan het script en vervang `<User>` door de gebruikers waarde die wordt gebruikt bij het implementeren van de Wingtip SaaS-app.
4. Druk op **F5** om het script uit te voeren dat de twee doel groepen maakt.

### <a name="extract-raw-data-from-all-tenants"></a>Onbewerkte gegevens uit alle tenants ophalen

Uitgebreide gegevens wijzigingen kunnen vaker voor komen voor *ticket-en klant* gegevens dan voor gegevens van *evenementen en locaties* . Overweeg daarom het extra heren van ticket-en klant gegevens en regel matig meer, dan u de gegevens van de gebeurtenis en de locatie wilt extra heren. In deze sectie definieert en plant u twee afzonderlijke taken:

- Het ticket en de klant gegevens ophalen.
- Gegevens van gebeurtenis en locatie ophalen.

Elke taak extraheert de gegevens en plaatst deze in het Analytics-archief. Er is een afzonderlijke taak Shreds de geëxtraheerde gegevens in het analyse ster-schema.

1. In SSMS maakt u verbinding met de **jobaccount** -data base in de catalogus-dpt- &lt; gebruikers &gt; server.
2. Open in SSMS *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.SQL*.
3. Wijzig @User boven aan het script en vervang door `<User>` de gebruikers naam die wordt gebruikt tijdens het implementeren van de Wingtip SaaS-app 
4. Druk op F5 om het script uit te voeren waarmee de taak wordt gemaakt en uitgevoerd voor het extra heren van tickets en klanten gegevens uit elke Tenant database. Met de taak worden de gegevens opgeslagen in de analyse opslag.
5. Query's uitvoeren op de tabel TicketsRawData in de tenantanalytics-data base om ervoor te zorgen dat de tabel is gevuld met ticketsgegevens van alle tenants.

![ticketExtracts](./media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Herhaal de voor gaande stappen, behalve deze keer **\ExtractTickets.SQL** vervangen door **\ExtractVenuesEvents.SQL** in stap 2.

Als de taak wordt uitgevoerd, wordt de EventsRawData-tabel in de analyse opslag gevuld met nieuwe gebeurtenissen en worden gegevens van alle tenants opgehaald. 

## <a name="data-reorganization"></a>Gegevens reorganisatie

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Shred geëxtraheerde gegevens voor het vullen van ster-schema tabellen

De volgende stap is het shred van de geëxtraheerde onbewerkte gegevens in een set tabellen die zijn geoptimaliseerd voor analyse query's. Er wordt een ster-schema gebruikt. Een centrale feiten tabel bevat afzonderlijke ticket omzet records. Andere tabellen worden gevuld met gerelateerde gegevens over locaties, gebeurtenissen en klanten. En er zijn tijd dimensie tabellen. 

In deze sectie van de zelf studie definieert en voert u een taak uit die de geëxtraheerde onbewerkte gegevens samenvoegt met de gegevens in de ster-schema tabellen. Nadat de samenvoeg taak is voltooid, worden de onbewerkte gegevens verwijderd, waardoor de tabellen klaar zijn om te worden gevuld met de volgende taak voor het uitpakken van Tenant gegevens.

1. In SSMS maakt u verbinding met de **jobaccount** -data base in de catalogus-dpt- &lt; gebruiker &gt; .
2. Open in SSMS *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.SQL*.
3. Druk op **F5** om het script uit te voeren om een taak te definiëren die de sp_ShredRawExtractedData opgeslagen procedure aanroept in het Analytics-archief.
4. Voldoende tijd om de taak te kunnen uitvoeren.
    - Controleer de kolom **levens duur** van taken. jobs_execution tabel voor de status van de taak. Zorg ervoor dat de taak **is voltooid** voordat u doorgaat. Bij een geslaagde uitvoering worden gegevens weer gegeven die vergelijkbaar zijn met de volgende grafiek:

![versnippering](./media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Gegevens verkennen

### <a name="visualize-tenant-data"></a>Tenant gegevens visualiseren

De gegevens in de ster-schema tabel bevatten alle gegevens van de ticket verkoop die nodig zijn voor uw analyse. Als u de trends in grote gegevens sets makkelijker wilt zien, moet u deze grafisch visualiseren.  In deze sectie leert u hoe u **Power bi** kunt gebruiken om de Tenant gegevens die u hebt geëxtraheerd en georganiseerd, te manipuleren en te visualiseren.

Gebruik de volgende stappen om verbinding te maken met Power BI en om de weer gaven te importeren die u eerder hebt gemaakt:

1. Start Power BI bureau blad.
2. Selecteer op het lint start de optie **gegevens ophalen**en selecteer **meer...** in het menu.
3. Selecteer Azure SQL Database in het venster **gegevens ophalen** .
4. Voer in het venster Data Base-aanmeldingen de naam van uw server in (Catalog-dpt- &lt; User &gt; . database.Windows.net). Selecteer **importeren** voor de **gegevens connectiviteits modus**en klik vervolgens op OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecteer **Data Base** in het linkerdeel venster, voer gebruikers naam = *ontwikkelaar*in en voer wacht woord = *P \@ ssword1*in. Klik op **Verbinden**.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Selecteer in het deel venster **Navigator** onder de Analytics-Data Base de ster-schema tabellen: fact_Tickets, dim_Events, dim_Venues, dim_Customers en dim_Dates. Selecteer vervolgens **laden**. 

Gefeliciteerd De gegevens zijn geladen in Power BI. U kunt nu interessante visualisaties gaan verkennen om inzicht te krijgen in uw tenants. In de volgende stap leert u hoe u met analyses gegevensgestuurde aanbevelingen kunt leveren aan het Business team van de Wingtip tickets. De aanbevelingen kunnen helpen het bedrijfs model en de klant ervaring te optimaliseren.

U begint met het analyseren van de verkoop gegevens van het ticket om de variatie in het gebruik over de locaties te bekijken. Selecteer de volgende opties in Power BI om een staaf diagram te tekenen van het totale aantal tickets dat per locatie is verkocht. Als gevolg van een wille keurige variatie in de ticket generator, kunnen de resultaten afwijken.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

In het voor gaande waarnemings punt wordt bevestigd dat het aantal tickets dat door elke locatie wordt verkocht, varieert. Locaties waarbij meer tickets worden verkocht, maken gebruik van uw service meer sterk dan locaties die minder tickets verkopen. Er is mogelijk een kans om de toewijzing van resources aan te passen op basis van de verschillende behoeften van de Tenant.

U kunt de gegevens verder analyseren om te zien hoe de ticket omzet gedurende een periode verschilt. Selecteer de volgende opties in Power BI om het totale aantal tickets te zetten dat elke dag gedurende een periode van 60 dagen is verkocht.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

In het voor gaande diagram wordt de verkoop piek van het ticket voor sommige locaties weer gegeven. Deze pieken versterken het idee dat sommige locaties mogelijk systeem bronnen onevenredig verbruiken. Tot nu toe is er geen duidelijk patroon in wanneer de pieken optreden.

Vervolgens wilt u de significantie van deze piek verkoop dagen verder onderzoeken. Wanneer komen deze pieken voor nadat tickets aan de verkoop gaan? Als u kaarten wilt uitzetten die per dag worden verkocht, selecteert u de volgende opties in Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

In het voor gaande waarnemings punt ziet u dat sommige locaties op de eerste dag van de verkoop veel tickets verkopen. Zodra tickets op deze locaties aan de verkoop gaan, lijkt het alsof er sprake is van een Made spoed. Deze burst-activiteit door enkele locaties kan van invloed zijn op de service voor andere tenants.

U kunt opnieuw inzoomen op de gegevens om te zien of deze Mad spoed waar is voor alle gebeurtenissen die worden gehost door deze locaties. In vorige grafieken heeft u geconstateerd dat contoso concert hal veel tickets verkoopt en dat contoso ook over bepaalde dagen een piek in de ticket verkopen heeft. U kunt met behulp van Power BI opties spelen om de cumulatieve ticket verkoop voor contoso-concert zaal uit te zetten, zodat u zich kunt concentreren op trends in de verkoop voor elk van de gebeurtenissen. Volgen alle gebeurtenissen hetzelfde verkoop patroon?

![ContosoSales](./media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

In het voor gaande teken voor contoso concert zaal ziet u dat de spoed van de Mad niet voor alle gebeurtenissen plaatsvindt. U kunt met de filter opties spelen om de verkoop trends voor andere evenementen te bekijken.

De inzichten in de verkoop patronen van het ticket kunnen leiden tot Wingtip tickets om hun bedrijfs model te optimaliseren. In plaats van alle tenants gelijkmatig te laden, kunnen Wingtip mogelijk service lagen introduceren met verschillende reken grootten. Grotere locaties die meer tickets per dag moeten verkopen, kunnen een hogere laag met een hoger service level agreement (SLA) worden aangeboden. Deze locaties kunnen hun data bases in een pool plaatsen met meer resource limieten per data base. Elke servicelaag kan een verkoop toewijzing per uur hebben, waarbij extra kosten in rekening worden gebracht voor het overschrijden van de toewijzing. Grotere locaties met periodieke pieken in de verkoop zouden profiteren van de hogere lagen en Wingtip tickets kunnen hun service efficiënter geld verdienen.

In de tussen tijd kunnen klanten met een Wingtip-tickets een klacht doen om voldoende tickets te verkopen om de service kosten te rechtvaardigen. Misschien is er in deze inzichten een kans om de verkoop van tickets te stimuleren voor het uitvoeren van locaties. Bij een hogere verkoop wordt de waargenomen waarde van de service verhoogd. Klik met de rechter muisknop op fact_Tickets en selecteer **nieuwe meting**. Voer de volgende expressie in voor de nieuwe meting met de naam **AverageTicketsSold**:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Selecteer de volgende visualisatie opties om het percentage tickets af te zetten dat door elke locatie wordt verkocht om het relatieve succes te bepalen.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

In het voor gaande waarnemings punt ziet u dat hoewel de meeste locaties meer dan 80% van hun tickets verkopen, sommige lastig meer dan de helft van de stoelen vullen. Speel met de waarden goed om het maximum of minimum percentage van tickets te selecteren dat voor elke locatie wordt verkocht.

Eerder hebt u uw analyse verdiept om te ontdekken dat de ticket verkoop veel voorspel bare patronen volgen. Deze detectie kan Wingtip tickets helpen bij het uitvoeren van locaties om de verkoop van tickets te stimuleren door dynamische prijzen aan te bevelen. Deze Discover kan een kans onthullen om machine learning technieken te gebruiken om de verkoop van tickets voor elke gebeurtenis te voors pellen. Er kunnen ook voor spellingen worden gemaakt voor de impact van de omzet van kortingen op ticket verkopen. Power BI Embedded kan worden geïntegreerd in een toepassing voor gebeurtenis beheer. De integratie kan helpen bij het visualiseren van voorspelde verkoop en het effect van verschillende kortingen. De toepassing kan u helpen een optimale korting te maken die rechtstreeks vanuit de analyse weergave wordt toegepast.

U hebt geobserveerde trends in de Tenant gegevens van de WingTip-toepassing. U kunt andere manieren voor het indelen van de app Toep assen op zakelijke beslissingen bij leveranciers van SaaS-toepassingen. Leveranciers kunnen beter voldoen aan de behoeften van hun tenants. Hopelijk is deze zelf studie voorzien van hulpprogram ma's die nodig zijn voor het uitvoeren van analyses op Tenant gegevens om uw bedrijven in staat te stellen om op gegevens gebaseerde beslissingen te nemen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> - Een Tenant Analytics-Data Base met vooraf gedefinieerde ster schema tabellen geïmplementeerd
> - Gebruikte elastische taken voor het extra heren van gegevens uit de Tenant database
> - De geëxtraheerde gegevens samen voegen in tabellen in een ster schema dat is ontworpen voor analyse
> - Query's uitvoeren op een Analytics-Data Base 
> - Gebruik Power BI voor gegevens visualisatie om trends in Tenant gegevens te observeren 

Gefeliciteerd

## <a name="additional-resources"></a>Aanvullende bronnen

- Aanvullende [zelf studies die voortbouwen op de Wingtip SaaS-toepassing](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastische taken](../../sql-database/elastic-jobs-overview.md).
- [Cross-Tenant analyse met geëxtraheerde gegevens-multi tenant-app](../../sql-database/saas-multitenantdb-tenant-analytics.md)