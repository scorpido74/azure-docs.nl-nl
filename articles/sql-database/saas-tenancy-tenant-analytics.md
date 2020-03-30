---
title: Analyse van meerdere tenants met geëxtraheerde gegevens
description: Analysequery's met meerdere tenant's met gegevens die zijn geëxtraheerd uit meerdere Azure SQL Database-databases in één tenant-app.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
ms.date: 12/18/2018
ms.openlocfilehash: c589d9619da8b5150d0fb4752625571c48393552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826385"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Analyse van meerdere tenants met behulp van geëxtraheerde gegevens - app met één tenant
 
In deze zelfstudie loopt u door een volledig analysescenario voor één tenantimplementatie. Het scenario laat zien hoe analytics bedrijven in staat kan stellen om slimme beslissingen te nemen. Met behulp van gegevens uit elke tenantdatabase gebruikt u analyses om inzicht te krijgen in het gedrag van huurders, inclusief het gebruik van de voorbeeld-Wingtip Tickets SaaS-toepassing. Dit scenario omvat drie stappen: 

1.  **Haal** gegevens uit elke tenantdatabase en **laden in** een analysearchief.
2.  **Transformeer de geëxtraheerde gegevens** voor analyseverwerking.
3.  Gebruik **business intelligence-tools** om nuttige inzichten te verzamelen, die de besluitvorming kunnen sturen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Maak het tenantanalytics-archief om de gegevens in te extraheren.
> - Gebruik elastische taken om gegevens uit elke tenantdatabase in het analysearchief te extraheren.
> - Optimaliseer de geëxtraheerde gegevens (reorganiseren in een sterschema).
> - Query de analytics database.
> - Gebruik Power BI voor gegevensvisualisatie om trends in tenantgegevens te markeren en aanbevelingte doen voor verbeteringen.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Offline tenantanalysepatroon

Multi-tenant SaaS-toepassingen hebben doorgaans een enorme hoeveelheid tenantgegevens die zijn opgeslagen in de cloud. Deze gegevens bieden een rijke bron van inzichten over de werking en het gebruik van uw toepassing en het gedrag van uw huurders. Deze inzichten kunnen de ontwikkeling van functies, bruikbaarheidsverbeteringen en andere investeringen in de app en het platform sturen.

Toegang tot gegevens voor alle tenants is eenvoudig wanneer alle gegevens zich in slechts één multi-tenantdatabase bevinden. Maar de toegang is complexer wanneer deze op schaal wordt verdeeld over mogelijk duizenden databases. Een manier om de complexiteit te temmen en de impact van analytics-query's op transactionele gegevens te minimaliseren, is door gegevens te extraheren in een speciaal ontworpen analysedatabase of datawarehouse.

Deze zelfstudie bevat een compleet analysescenario voor de SaaS-toepassing van Wingtip Tickets. Ten eerste wordt *Elastic Jobs* gebruikt om gegevens uit elke tenantdatabase te extraheren en in faseringstabellen in een analysearchief te laden. De analysewinkel kan een SQL-database of een SQL Data Warehouse zijn. Voor grootschalige gegevensextractie wordt [Azure Data Factory](../data-factory/introduction.md) aanbevolen.

Vervolgens worden de geaggregeerde gegevens omgezet in een reeks [sterschematabellen.](https://www.wikipedia.org/wiki/Star_schema) De tabellen bestaan uit een centrale feitentabel plus bijbehorende dimensietabellen.  Voor Wingtip Tickets:

- De centrale feitentabel in het sterschema bevat ticketgegevens.
- De dimensietabellen beschrijven locaties, evenementen, klanten en aankoopdatums.

Samen maken de centrale feiten- en dimensionaaltabellen een efficiënte analytische verwerking mogelijk. Het sterschema dat in deze zelfstudie wordt gebruikt, wordt weergegeven in de volgende afbeelding:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Ten slotte wordt de analysewinkel opgevraagd met **PowerBI** om inzicht te geven in het gedrag van huurders en het gebruik van de Wingtip Tickets-toepassing. U voert query's uit die:
 
- Toon de relatieve populariteit van elke locatie
- Patronen markeren in de kaartverkoop voor verschillende evenementen
- Toon het relatieve succes van verschillende locaties in het uitverkopen van hun evenement

Inzicht in hoe elke tenant de service gebruikt, wordt gebruikt om opties te verkennen voor het genereren van inkomsten met de service en het verbeteren van de service om huurders te helpen succesvoller te zijn. Deze zelfstudie bevat basisvoorbeelden van de soorten inzichten die kunnen worden verkregen uit tenantgegevens.

## <a name="setup"></a>Instellen

### <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet u ervoor zorgen dat aan de volgende vereisten is voldaan:

- De Wingtip Tickets SaaS Database Per Tenant applicatie wordt geïmplementeerd. Zie [De SaaS-toepassing Wingtip implementeren en verkennen](saas-dbpertenant-get-started-deploy.md) als u deze in minder dan vijf minuten wilt implementeren en verkennen
- De Wingtip Tickets SaaS Database Per Tenant scripts en applicatie [broncode](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) worden gedownload van GitHub. Zie downloadinstructies. Zorg ervoor dat *u het zip-bestand deblokkeert* voordat u de inhoud ervan extraheren. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren.
- Power BI Desktop is geïnstalleerd. [Power BI Desktop downloaden](https://powerbi.microsoft.com/downloads/)
- De partij van extra tenants is ingericht, zie de [**Provision tenants tutorial**](saas-dbpertenant-provision-and-catalog.md).
- Er zijn een vacatureaccount en vacatureaccountdatabase gemaakt. Bekijk de juiste stappen in de [**zelfstudie Schemabeheer**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Gegevens maken voor de demo

In deze zelfstudie wordt analyse uitgevoerd op de gegevens van de kaartverkoop. In de huidige stap genereert u ticketgegevens voor alle huurders.  Later worden deze gegevens geëxtraheerd voor analyse. *Zorg ervoor dat u de batch van tenants hebt ingericht zoals eerder beschreven, zodat u een zinvolle hoeveelheid gegevens hebt.* Een voldoende grote hoeveelheid gegevens kan een reeks verschillende ticketaankooppatronen blootleggen.

1. Open in PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*en stel de volgende waarde in:
    - **$DemoScenario** = **1** Koop tickets voor evenementen op alle locaties
2. Druk op **F5** om het script uit te voeren en maak de aankoopgeschiedenis van het ticket voor elk evenement in elke locatie.  Het script loopt enkele minuten om tienduizenden tickets te genereren.

### <a name="deploy-the-analytics-store"></a>De analysewinkel implementeren
Vaak zijn er tal van transactionele databases die samen alle tenantgegevens bevatten. U moet de tenantgegevens uit de vele transactionele databases samenvoegen tot één analysearchief. De aggregatie maakt een efficiënte query van de gegevens mogelijk. In deze zelfstudie wordt een Azure SQL Database-database gebruikt om de samengevoegde gegevens op te slaan.

In de volgende stappen implementeert u de analysewinkel, die **tenantanalytics**wordt genoemd. U implementeert ook vooraf gedefinieerde tabellen die later in de zelfstudie worden ingevuld:
1. Open in PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Stel de $DemoScenario variabele in het script in op uw keuze van de analytics-winkel:
    - Als u SQL-database zonder kolomarchief wilt gebruiken, stelt u **$DemoScenario** = **2** in
    - Als u SQL-database wilt gebruiken met kolomarchief, stelt u **$DemoScenario** = **3** in  
3. Druk op **F5** om het demoscript uit te voeren (dat het *Script\<Deploy-TenantAnalytics XX>.ps1* aanroept) waarmee de tenantanalysewinkel wordt gemaakt. 

Nu u de toepassing hebt geïmplementeerd en gevuld met interessante tenantgegevens, gebruikt u [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) om **tenants1-dpt-&lt;Gebruiker&gt; ** en **catalogus-dpt-&lt;Gebruikersservers&gt; ** met login = *ontwikkelaar*, Wachtwoord = *P\@zwaard1*te verbinden . Zie de [inleidende zelfstudie](saas-dbpertenant-wingtip-app-overview.md) voor meer begeleiding.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

Voer in de ObjectVerkenner de volgende stappen uit:

1. De *tenants1-dpt-&lt;&gt; gebruikersserver* uitbreiden.
2. Vouw het knooppunt Databases uit en bekijk de lijst met tenantdatabases.
3. Vouw de *server van&lt;&gt; de catalogus-dpt- Gebruiker* uit.
4. Controleer of u de analysewinkel en de jobaccountdatabase ziet.

Bekijk de volgende database-items in de SSMS Object Explorer door het knooppunt van het analysearchief uit te breiden:

- Tabellen **TicketsRawData** en **EventsRawData** bevatten ruwe geëxtraheerde gegevens uit de tenantdatabases.
- De tabellen van het sterschema worden **fact_Tickets,** **dim_Customers,** **dim_Venues,** **dim_Events**en **dim_Dates**.
- De opgeslagen procedure wordt gebruikt om de tabellen met sterschema's uit de ruwe gegevenstabellen te vullen.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Gegevensextractie 

### <a name="create-target-groups"></a>Doelgroepen maken 

Voordat u verdergaat, moet u ervoor zorgen dat u het taakaccount en de jobaccountdatabase hebt geïmplementeerd. In de volgende reeks stappen wordt Elastic Jobs gebruikt om gegevens uit elke tenantdatabase te extraheren en de gegevens op te slaan in het analysearchief. Vervolgens versnippert de tweede taak de gegevens en slaat deze op in tabellen in het sterschema. Deze twee taken lopen tegen twee verschillende doelgroepen, namelijk **TenantGroup** en **AnalyticsGroup.** De functie extraheren wordt uitgevoerd tegen de TenantGroep, die alle tenantdatabases bevat. De shreddertaak wordt uitgevoerd ten opzichte van de AnalyticsGroup, die alleen de analytics-winkel bevat. Maak de doelgroepen met behulp van de volgende stappen:

1. Maak in SSMS verbinding met de **jobaccountdatabase** in catalogus-dpt-&lt;Gebruiker.&gt;
2. Open in SSMS *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Wijzig @User de variabele boven aan het `<User>` script en vervang de gebruikerswaarde die wordt gebruikt toen u de Wingtip SaaS-app hebt geïmplementeerd.
4. Druk op **F5** om het script uit te voeren waarmee de twee doelgroepen zijn gemaakt.

### <a name="extract-raw-data-from-all-tenants"></a>Onbewerkte gegevens van alle tenants extraheren

Uitgebreide gegevenswijzigingen kunnen vaker voorkomen voor *ticket- en klantgegevens* dan voor *gebeurtenis- en locatiegegevens.* Overweeg daarom om ticket- en klantgegevens afzonderlijk en vaker te extraheren dan u gebeurtenis- en locatiegegevens veronttrekt. In deze sectie definieert en plant u twee afzonderlijke taken:

- Haal ticket- en klantgegevens uit.
- Uittreksel seinen gegevens over evenementen en locaties.

Elke taak haalt zijn gegevens eruit en plaatst deze in de analysewinkel. Daar versnippert een aparte taak de geëxtraheerde gegevens in het analytics-sterschema.

1. Maak in SSMS verbinding met de **jobaccountdatabase** in de server met catalogus-dpt-&lt;Gebruiker.&gt;
2. Open in SSMS *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Wijzigen @User boven aan het script `<User>` en vervangen door de gebruikersnaam die wordt gebruikt toen u de Wingtip SaaS-app hebt geïmplementeerd 
4. Druk op F5 om het script uit te voeren dat de taak maakt en uitvoert waarmee tickets en klantgegevens uit elke tenantdatabase worden geëxtraheerd. De taak slaat de gegevens op in de analysewinkel.
5. Vraag de tabel TicketsRawData in de tenantanalytics-database om ervoor te zorgen dat de tabel wordt gevuld met ticketgegevens van alle tenants.

![ticketExtracten](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Herhaal de voorgaande stappen, behalve deze keer **vervang \ExtractTickets.sql** met **\ExtractVenuesEvents.sql** in stap 2.

Als u de taak uitvoert, wordt de tabel EventsRawData in de analysewinkel gevuld met nieuwe evenementen en locatiegegevens van alle tenants. 

## <a name="data-reorganization"></a>Gegevensreorganisatie

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Geëxtraheerde gegevens versnipperen om sterschematabellen in te vullen

De volgende stap is het versnipperen van de geëxtraheerde ruwe gegevens in een set tabellen die zijn geoptimaliseerd voor analysequery's. Er wordt een sterrenschema gebruikt. Een centrale feitentabel bevat individuele ticketverkooprecords. Andere tabellen worden gevuld met gerelateerde gegevens over locaties, evenementen en klanten. En er zijn tijddimensietabellen. 

In dit gedeelte van de zelfstudie definieert en voert u een taak uit die de geëxtraheerde ruwe gegevens samenvoegt met de gegevens in de tabellen met het sterschema. Nadat de samenvoegtaak is voltooid, worden de ruwe gegevens verwijderd, zodat de tabellen klaar zijn om te worden ingevuld door de volgende tenantgegevensextractietaak.

1. Maak in SSMS verbinding met de **jobaccountdatabase** in catalogus-dpt-&lt;Gebruiker.&gt;
2. Open in SSMS *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Druk op **F5** om het script uit te voeren om een taak te definiëren die de sp_ShredRawExtractedData opgeslagen procedure in de analysewinkel aanroept.
4. Geef voldoende tijd voor de taak om succesvol uit te voeren.
    - Controleer de kolom **Levenscyclus** van jobs.jobs_execution tabel voor de status van taak. Zorg ervoor dat de taak **is geslaagd** voordat u verdergaat. In een geslaagde run worden gegevens weergegeven die vergelijkbaar zijn met de volgende grafiek:

![Shredder](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Gegevensverkenning

### <a name="visualize-tenant-data"></a>Tenantgegevens visualiseren

De gegevens in de tabel met het sterschema bieden alle gegevens over de kaartverkoop die nodig zijn voor uw analyse. Om het gemakkelijker te maken om trends in grote gegevenssets te zien, moet u deze grafisch visualiseren.  In deze sectie leert u hoe u **Power BI kunt** gebruiken om de tenantgegevens die u hebt geëxtraheerd en georganiseerd, te manipuleren en te visualiseren.

Gebruik de volgende stappen om verbinding te maken met Power BI en de weergaven te importeren die u eerder hebt gemaakt:

1. Start Power BI-bureaublad.
2. Selecteer op het lint Start de optie **Gegevens ophalen**en selecteer **Meer...** van het menu.
3. Selecteer Azure SQL Database in het venster **Gegevens opdoen.**
4. Voer in het aanmeldingsvenster van de database&lt;&gt;uw servernaam in (catalogus-dpt- Gebruiker .database.windows.net). Selecteer **Importeren** voor **de modus Gegevensconnectiviteit**en klik op OK. 

    ![signinpowerbi (signinpowerbi)](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Selecteer **Database** in het linkerdeelvenster en voer vervolgens de gebruikersnaam in = *ontwikkelaar*en voer wachtwoord in = *\@P zwaard1*. Klik op **Verbinden**.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Selecteer in het deelvenster **Navigator** onder de analysedatabase de tabellen met het sterschema: fact_Tickets, dim_Events, dim_Venues, dim_Customers en dim_Dates. Selecteer vervolgens **Laden**. 

Gefeliciteerd! U hebt de gegevens in Power BI geladen. Nu u beginnen met het verkennen van interessante visualisaties om inzicht te krijgen in uw huurders. Vervolgens doorloopt u hoe analytics u in staat stelt om datagestuurde aanbevelingen te doen aan het business team van Wingtip Tickets. De aanbevelingen kunnen helpen om het bedrijfsmodel en de klantervaring te optimaliseren.

U begint met het analyseren van ticketverkoopgegevens om de variatie in gebruik tussen de locaties te zien. Selecteer de volgende opties in Power BI om een staafdiagram te plotten van het totale aantal tickets dat per locatie wordt verkocht. Als gevolg van willekeurige variatie in de ticketgenerator kunnen uw resultaten anders zijn.
 
![TotalTicketsByVenues TotalTicketsByVenues TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Het voorgaande plot bevestigt dat het aantal tickets dat per locatie wordt verkocht, varieert. Locaties die meer tickets verkopen, gebruiken uw service zwaarder dan locaties die minder tickets verkopen. Er kan hier een mogelijkheid zijn om de toewijzing van resources af te stemmen op basis van verschillende tenantbehoeften.

U de gegevens verder analyseren om te zien hoe de kaartverkoop in de loop van de tijd varieert. Selecteer de volgende opties in Power BI om het totale aantal verkochte tickets per dag voor een periode van 60 dagen te plotten.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

De voorgaande grafiek geeft die ticketverkoop piek voor sommige locaties. Deze pieken versterken het idee dat sommige locaties mogelijk onevenredig gebruik maken van systeembronnen. Tot nu toe is er geen duidelijk patroon in wanneer de spikes optreden.

Vervolgens wilt u de betekenis van deze piekverkoopdagen verder onderzoeken. Wanneer ontstaan deze pieken nadat tickets in de verkoop gaan? Als u tickets wilt plotten die per dag worden verkocht, selecteert u de volgende opties in Power BI.

![SaleDayDistributie](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

De voorgaande plot laat zien dat sommige locaties verkopen veel tickets op de eerste dag van de verkoop. Zodra tickets gaan op de verkoop op deze locaties, lijkt er een gekke rush. Deze uitbarsting van activiteit door een paar locaties kan invloed hebben op de service voor andere huurders.

U opnieuw inzoomen op de gegevens om te zien of deze gekke rush geldt voor alle evenementen die worden gehost door deze locaties. In eerdere percelen merkte je op dat Contoso Concert Hall veel tickets verkoopt, en dat Contoso ook een piek heeft in de kaartverkoop op bepaalde dagen. Speel met Power BI-opties om de cumulatieve kaartverkoop voor Contoso Concert Hall te plotten, met de nadruk op verkooptrends voor elk van zijn evenementen. Volgen alle gebeurtenissen hetzelfde verkooppatroon?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

De voorgaande plot voor Contoso Concert Hall laat zien dat de gekke rush niet gebeurt voor alle evenementen. Speel met de filteropties om verkooptrends voor andere locaties te zien.

De inzichten in ticketverkooppatronen kunnen Wingtip Tickets ertoe brengen hun bedrijfsmodel te optimaliseren. In plaats van alle tenants gelijk te laden, moet Wingtip misschien servicelagen met verschillende rekengroottes introduceren. Grotere locaties die meer tickets per dag moeten verkopen, kunnen een hoger niveau worden aangeboden met een hogere servicelevelovereenkomst (SLA). Deze locaties kunnen hun databases in de pool plaatsen met hogere resourcelimieten per database. Elke servicelaag kan een verkooptoewijzing per uur hebben, waarbij extra kosten in rekening worden gebracht voor het overschrijden van de toewijzing. Grotere locaties met periodieke verkoopuitbarstingen zouden profiteren van de hogere lagen en Wingtip Tickets kunnen efficiënter inkomsten genereren met hun service.

Ondertussen klagen sommige Wingtip Tickets-klanten dat ze moeite hebben om genoeg tickets te verkopen om de servicekosten te rechtvaardigen. Misschien is er in deze inzichten een mogelijkheid om de kaartverkoop voor slecht presterende locaties te stimuleren. Hogere verkopen zouden de waargenomen waarde van de dienst verhogen. Klik met de rechtermuisknop op fact_Tickets en selecteer **Nieuwe meting**. Voer de volgende expressie in voor de nieuwe maatregel genaamd **AverageTicketsSold:**

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Selecteer de volgende visualisatieopties om het percentage tickets dat door elke locatie wordt verkocht, in kaart te brengen om hun relatieve succes te bepalen.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

De voorgaande plot blijkt dat, hoewel de meeste locaties verkopen meer dan 80% van hun tickets, sommige worstelen om meer dan de helft van de zetels te vullen. Speel met de Waarden Goed om maximaal of minimaal percentage verkochte tickets voor elke locatie te selecteren.

Eerder verdiepte u uw analyse om te ontdekken dat de kaartverkoop de neiging heeft om voorspelbare patronen te volgen. Deze ontdekking kan Wingtip Tickets helpen slecht presterende locaties te stimuleren ticketverkoop door het aanbevelen van dynamische prijzen. Deze ontdekking zou een kans kunnen onthullen om machine learning-technieken in te zetten om de kaartverkoop voor elk evenement te voorspellen. Er kunnen ook voorspellingen worden gedaan voor de impact op de inkomsten van het aanbieden van kortingen op de kaartverkoop. Power BI Embedded kan worden geïntegreerd in een toepassing voor eventmanagement. De integratie kan helpen bij het visualiseren van voorspelde verkopen en het effect van verschillende kortingen. De applicatie kan helpen bij het bedenken van een optimale korting die rechtstreeks vanuit de analytics-display moet worden toegepast.

U hebt trends in tenantgegevens waargenomen vanuit de WingTip-toepassing. U overwegen op andere manieren waarop de app zakelijke beslissingen kan nemen voor leveranciers van SaaS-toepassingen. Verkopers kunnen beter tegemoet te komen aan de behoeften van hun huurders. Hopelijk heeft deze zelfstudie u uitgerust met tools die nodig zijn om analyses uit te voeren op tenantgegevens om uw bedrijven in staat te stellen gegevensgestuurde beslissingen te nemen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - Een tenantanalysedatabase geïmplementeerd met vooraf gedefinieerde sterschematabellen
> - Gebruikte elastische taken om gegevens uit alle tenantdatabase te extraheren
> - De geëxtraheerde gegevens samenvoegen in tabellen in een sterschema dat is ontworpen voor analyse
> - Een analysedatabase opvragen 
> - Power BI gebruiken voor gegevensvisualisatie om trends in tenantgegevens te observeren 

Gefeliciteerd!

## <a name="additional-resources"></a>Aanvullende bronnen

- Extra [tutorials die voortbouwen op de Wingtip SaaS applicatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastische taken](elastic-jobs-overview.md).
- [Analyse van meerdere tenants met behulp van geëxtraheerde gegevens - multi-tenant-app](saas-multitenantdb-tenant-analytics.md)