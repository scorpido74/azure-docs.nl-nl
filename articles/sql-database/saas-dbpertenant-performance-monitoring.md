---
title: 'SaaS-app: prestaties van veel Azure SQL-data bases bewaken '
description: Prestaties van Azure SQL-data bases en-Pools bewaken en beheren in een SaaS-app met meerdere tenants
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 4860766ddb4214591dc2c77f2746f958cb101741
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692130"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Prestaties van Azure SQL-data bases en-Pools bewaken en beheren in een SaaS-app met meerdere tenants

In deze zelf studie worden verschillende scenario's voor prestatie beheer in SaaS-toepassingen besproken. Het gebruik van een load generator voor het simuleren van activiteit in alle Tenant databases, de ingebouwde bewakings-en waarschuwings functies van SQL Database en elastische Pools worden gedemonstreerd.

De Wingtip tickets SaaS-data base per Tenant-app maakt gebruik van een gegevens model met één Tenant, waarbij elke locatie (Tenant) een eigen data base heeft. Net als bij veel andere SaaS-toepassingen, is het verwachte tenantworkloadpatroon onvoorspelbaar en sporadisch. Met andere woorden, kaartverkoop kan op ieder moment plaatsvinden. Als u gebruik wilt maken van dit typische database gebruiks patroon, worden de Tenant databases geïmplementeerd in elastische Pools. Elastische pools verlagen de kosten van een oplossing door resources over veel databases te verdelen. Bij dit type patroon is het belangrijk om het gebruik van database- en poolresources te controleren om ervoor te zorgen dat loads goed over pools zijn verdeeld. Ook moet u ervoor zorgen dat individuele databases voldoende resources hebben en dat pools niet hun [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)-limieten overschrijden. In deze zelfstudie worden manieren toegelicht om databases en pools te controleren en beheren. Ook wordt uitgelegd hoe u corrigerende maatregelen kunt nemen als reactie op wisselingen in de workload.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Gebruik op de tenantdatabases simuleren door het uitvoeren van een geleverde load-generator
> * De tenant-databases bewaken terwijl ze reageren op de toename in load
> * Omhoog schalen van de elastische pool als reactie op de verhoogde databaseload
> * Een tweede elastische pool inrichten om taken te verdelen voor de databaseactiviteit


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip tickets SaaS-data base per Tenant-app wordt geïmplementeerd. Zie [de SaaS-data base van Wingtip tickets implementeren en verkennen per Tenant toepassing](saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-saas-performance-management-patterns"></a>Inleiding tot SaaS Performance Management-patronen

Het beheren van de databaseprestaties bestaat uit het verzamelen en analyseren van prestatiegegevens en het reageren op deze gegevens door parameters aan te passen om een acceptabele reactietijd voor de toepassing te behouden. Bij het hosten van meerdere tenants zijn elastische Pools een rendabele manier om resources te bieden en te beheren voor een groep data bases met onvoorspelbare workloads. Met bepaalde workloadpatronen kan het al bij twee S3-databases voordelig zijn om ze door een pool te laten beheren.

![toepassings diagram](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pools en de data bases in Pools moeten worden bewaakt om ervoor te zorgen dat ze binnen aanvaard bare bereiken van prestaties blijven. Stem de pool configuratie af om te voldoen aan de behoeften van de cumulatieve werk belasting van alle data bases, zodat de pool Edtu's geschikt zijn voor de algehele werk belasting. Pas de minimale en maximale eDTU-waarden per database aan naar de geschikte waarden voor de vereisten van uw specifieke toepassing.

### <a name="performance-management-strategies"></a>Strategieën voor prestatiebeheer

* Om te voor komen dat de prestaties hand matig worden bewaakt, is het het meest effectief om waarschuwingen in te **stellen die activeren wanneer data bases of Pools uit normale bereiken**komen.
* Als u wilt reageren op schommelingen op de korte termijn in de cumulatieve reken grootte van een pool, kan het eDTU-niveau van de **pool worden uitgebreid of omlaag worden geschaald**. Als deze fluctuatie regelmatig of op voorspelbare momenten optreedt, **kan het schalen van de pool op automatisch worden ingesteld**. U kunt bijvoorbeeld omlaag schalen wanneer de workload licht is, zoals 's nachts of tijdens het weekend.
* U kunt ook **afzonderlijke databases naar andere pools verplaatsen** bij fluctuaties op de langere termijn of wijzigingen in het aantal databases.
* Om te reageren op de korte termijn verhogingen van *afzonderlijke* data bases **, kunnen afzonderlijke data bases uit een pool worden gehaald en kan er een afzonderlijke reken grootte worden toegewezen**. Wanneer de load is verlaagd, kan de database naar de pool worden teruggezet. Wanneer dit vooraf bekend is, kunnen data bases worden verplaatst vóór bekend om ervoor te zorgen dat de data base altijd de benodigde resources heeft en om te voor komen dat andere data bases in de pool worden beïnvloed. Als het om een voorspelbare vereiste gaat, zoals bij een locatie die met een toename in kaartverkoop te maken krijgt voor een populair evenement, kan dit beheergedrag in de toepassing worden geïntegreerd.

[Azure Portal](https://portal.azure.com) biedt ingebouwde functionaliteit voor bewaking en waarschuwingen voor de meeste resources. Voor SQL Database zijn bewaking en waarschuwingen beschikbaar voor databases en pools. Deze ingebouwde bewaking en waarschuwingen zijn specifiek voor resources, dus het is handig voor kleine aantallen resources, maar is niet heel handig wanneer u met veel resources werkt.

Voor scenario's met grote volumes, waar u met veel resources werkt, kunnen [Azure monitor logboeken](saas-dbpertenant-log-analytics.md) worden gebruikt. Dit is een afzonderlijke Azure-service die analyse van verzonden Diagnostische logboeken en telemetrie in een Log Analytics-werk ruimte biedt. Azure Monitor logboeken kunnen telemetrie verzamelen van veel services en worden gebruikt om waarschuwingen op te vragen en in te stellen.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De Wingtip tickets SaaS-data base ophalen per Tenant toepassings scripts

De Wingtip tickets SaaS multi-tenant database scripts en toepassings bron code zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github opslag plaats. Bekijk de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en blok keren van de Wingtip tickets SaaS-scripts.

## <a name="provision-additional-tenants"></a>Extra tenants inrichten

Hoewel pools al met twee S3-databases rendabel kunnen zijn, geldt dat hoe meer databases er in de pool zitten, hoe rendabeler het berekenen van gemiddelden wordt. U hebt voor deze zelfstudie ten minste 20 geïmplementeerde databases nodig om een goed inzicht te krijgen in de werking van het bewaken en beheren van prestaties op schaal.

Als u al een batch tenants in een vorige zelf studie hebt ingericht, gaat u naar de sectie [gebruik simuleren op alle Tenant databases](#simulate-usage-on-all-tenant-databases) .

1. \\open in de **Power shell ISE**-Learning modules\\prestatie bewaking en-beheer\\*demo-PerformanceMonitoringAndManagement. ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel het volgende in: **$DemoScenario** = **1**, **Batch met tenants inrichten**
1. Druk op **F5** om het script uit te voeren.

Het script implementeert 17 tenants in minder dan vijf minuten.

Het script *New-TenantBatch* maakt gebruik van een geneste of gekoppelde set [Resource Manager](../azure-resource-manager/index.yml) -sjablonen waarmee een batch tenants wordt gemaakt, die standaard de Data Base- **basetenantdb** op de catalogus server kopieert om de nieuwe Tenant databases te maken, en vervolgens registreert deze in de catalogus en initialiseert deze vervolgens met de naam en het type locatie van de Tenant. Dit is consistent met de manier waarop de app een nieuwe Tenant heeft ingericht. Alle wijzigingen die zijn aangebracht in *basetenantdb* , worden toegepast op alle nieuwe tenants die daarna worden ingericht. Raadpleeg de [zelf studie schema beheer](saas-tenancy-schema-management.md) om te zien hoe u schema wijzigingen kunt aanbrengen in *bestaande* Tenant databases (inclusief de *basetenantdb* -data base).

## <a name="simulate-usage-on-all-tenant-databases"></a>Gebruik simuleren op alle tenantdatabases

Het script *demo-PerformanceMonitoringAndManagement. ps1* wordt gebruikt voor het simuleren van een werk belasting die wordt uitgevoerd op alle Tenant databases. De belasting wordt gegenereerd met behulp van een van de beschik bare laad scenario's:

| Demo | Scenario |
|:--|:--|
| 2 | Normale intensiteit genereren (ongeveer 40 DTU) |
| 3 | Load genereren met langere en frequentere bursts per database|
| 4 | Genereren van belasting met hogere DTU-bursts per data base (circa 80 DTU)|
| 5 | Genereer een normale belasting plus een hoge belasting voor één Tenant (ongeveer 95 DTU)|
| 6 | Niet-gebalanceerde load genereren voor meerdere pools|

De load-generator past een *synthetische* load alleen voor CPU toe op elke tenantdatabase. De generator start voor elke tenantdatabase een taak, die periodiek een opgeslagen procedure aanroept die de load genereert. De load-niveaus (in eDTU's), duur en intervallen zijn verschillend voor alle databases. Dit simuleert onvoorspelbare tenantactiviteit.

1. \\open in de **Power shell ISE**-Learning modules\\prestatie bewaking en-beheer\\*demo-PerformanceMonitoringAndManagement. ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel **$DemoScenario** = **2**in, *Genereer normale intensiteits belasting*.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.

Wingtip tickets SaaS data base per Tenant is een SaaS-app en de werkelijke belasting van een SaaS-app is doorgaans sporadisch en onvoorspelbaar. Om dit te simuleren, produceert de load-generator een willekeurige workload die over alle tenants wordt verdeeld. Er zijn enkele minuten nodig om het laad patroon te laten zien, dus voer de laad Generator gedurende 3-5 minuten uit voordat u probeert de belasting in de volgende secties te bewaken.

> [!IMPORTANT]
> De load-generator wordt uitgevoerd als een reeks taken in uw lokale PowerShell-sessie. Houd het tabblad *Demo-PerformanceMonitoringAndManagement.ps1* geopend. Als u het tabblad sluit of de computer onderbreekt, stopt de load-generator. De load Generator blijft in de status van een *taak aanroepen* waarbij de belasting wordt gegenereerd voor nieuwe tenants die zijn ingericht nadat de generator is gestart. Gebruik *CTRL + C* om te stoppen met het aanroepen van nieuwe taken en het script af te sluiten. De load Generator blijft actief, maar alleen op bestaande tenants.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Resource gebruik bewaken met behulp van de Azure Portal

Als u het resource gebruik wilt bewaken dat het resultaat is van de belasting die wordt toegepast, opent u de portal naar de groep die de Tenant databases bevat:

1. Open de [Azure Portal](https://portal.azure.com) en blader naar de *tenants1-dpt-&lt;gebruiker&gt;* server.
1. Scrol naar beneden, zoek de elastische pools en klik op **Pool1**. Deze pool bevat alle tenantdatabases die tot nu toe zijn gemaakt.

Bekijk de bewakende grafieken voor **elastische groepen** en **elastische data bases** .

Het resource gebruik van de pool is het aggregatie database gebruik voor alle data bases in de pool. In het database diagram worden de vijf nieuwste data bases weer gegeven:

![database diagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Omdat er meer data bases in de pool zijn dan de top vijf, toont het pool gebruik activiteiten die niet worden weer gegeven in de bovenste vijf data bases grafiek. Klik voor meer informatie op **database resource gebruik**:

![resource gebruik van de data base](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Prestatiewaarschuwingen voor de pool instellen

Stel als volgt een waarschuwing in voor de pool die wordt geactiveerd \>75%-gebruik:

1. Open *Pool1* (op de *tenants1-dpt-\<gebruiker\>* -server) in de [Azure Portal](https://portal.azure.com).
1. Klik op **Waarschuwingsregels** en klik vervolgens op **+ Waarschuwing toevoegen**:

   ![waarschuwing toevoegen](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Geef een naam op, bijvoorbeeld **hoog DTU**.
1. Stel de volgende waarden in:
   * **Metriek = eDTU-percentage**
   * **Voor waarde = groter dan**
   * **Drempel waarde = 75**
   * **Period = de afgelopen 30 minuten**
1. Voeg een e-mail adres toe aan het vak *extra beheerders-e-mail (s)* en klik op **OK**.

   ![waarschuwing instellen](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Een zwaar belaste pool omhoog schalen

Als het cumulatieve loadniveau voor een pool in een dergelijke mate toeneemt dat de pool volledig wordt gebruikt en 100% eDTU-gebruik wordt bereikt, worden de prestaties van afzonderlijke databases beïnvloed. Hierdoor wordt de reactietijd voor query's mogelijk vertraagd voor alle databases in de pool.

**Kort**, overweeg om de pool te schalen om aanvullende resources te bieden of data bases uit de groep te verwijderen (naar andere groepen of naar een zelfstandige servicelaag te verplaatsen).

**Meer termijn**, overweeg het optimaliseren van query's of index gebruik om de database prestaties te verbeteren. Afhankelijk van de gevoeligheid voor prestatieproblemen van de toepassing, is de aanbevolen procedure om een pool op te schalen voordat 100% eDTU-gebruik wordt bereikt. Gebruik een waarschuwing zodat u tijdig wordt gewaarschuwd.

U kunt een zwaar belaste pool simuleren door de load die de generator produceert, te verhogen. Zorgt ervoor dat de data bases vaker worden opgesplitst, en langer de cumulatieve belasting van de groep verhogen zonder de vereisten van de afzonderlijke data bases te wijzigen. U kunt de pool eenvoudig opschalen in de portal of vanuit PowerShell. In deze oefening wordt de portal gebruikt.

1. Stel *$DemoScenario* = **3**, _Load genereren met langere en frequentere bursts per database_ in om de intensiteit van de cumulatieve load voor de pool te verhogen zonder de piekbelasting die is vereist voor elke database te wijzigen.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.

1. Ga naar **Pool1** in de Azure Portal.

Bewaak het toegenomen gebruik van de groeps-eDTU in het bovenste diagram. Het duurt een paar minuten voordat de nieuwe hogere belasting in de tijd begint, maar u moet snel de pool starten om het maximale gebruik te bereiken, en naarmate de belasting in het nieuwe patroon wordt gezet, wordt de groep snel overbelast.

1. Klik boven aan de pagina **Pool1** op **groep configureren** om de pool omhoog te schalen.
1. Pas de **groeps-eDTU** -instelling aan op **100**. Als u de groeps-eDTU wijzigt, wordt de instelling per database niet gewijzigd (deze is nog steeds maximaal 50 eDTU's per database). U kunt de instellingen per data base bekijken aan de rechter kant van de pagina **groep configureren** .
1. Klik op **Opslaan** om de aanvraag in te dienen om de groep te schalen.

Ga terug naar **Pool1** > **overzicht** om de bewakings grafieken weer te geven. Bewaak het effect van het leveren van de groep met meer resources (hoewel met een paar data bases en een wille keurige werk belasting het niet altijd goed is om op een bepaalde tijd goed te zien). Houd er rekening mee dat 100% bij de bovenste grafiek nu staat voor 100 eDTU's, terwijl voor de onderste grafiek 100% nog steeds 50 eDTU's betekent, omdat het maximum per database 50 eDTU's is.

Databases blijven gedurende het proces online en volledig beschikbaar. Op het laatste moment, wanneer elke database gereed is om te worden ingeschakeld met de nieuwe pool-eDTU's, worden alle actieve verbindingen verbroken. Om verbroken verbindingen opnieuw te proberen, moet altijd een toepassingscode worden geschreven zodat er opnieuw verbinding wordt gemaakt met de database in de omhoog geschaalde pool.

## <a name="load-balance-between-pools"></a>Taak verdeling tussen Pools

In plaats van de pool omhoog te schalen, kunt u ook een tweede pool maken en databases hiernaartoe verplaatsen om de load tussen de twee pools te verdelen. Om dit te doen, moet de nieuwe pool op dezelfde server als de eerste pool worden gemaakt.

1. Open in de [Azure Portal](https://portal.azure.com)de **tenants1-dpt-&lt;gebruiker&gt;** server.
1. Klik op **+ nieuwe pool** om een pool te maken op de huidige server.
1. Op de sjabloon voor **elastische Pools** :

   1. Stel **name** in op *Pool2*.
   1. Laat de prijscategorie op **Standaardpool** staan.
   1. Klik op **Groep configureren**.
   1. **Groeps-eDTU** instellen op *50 eDTU*.
   1. Klik op **data bases toevoegen** om een lijst met data bases op de server weer te geven die kunnen worden toegevoegd aan *Pool2*.
   1. Selecteer tien data bases om deze naar de nieuwe groep te verplaatsen en klik vervolgens op **selecteren**. Als u de load Generator hebt uitgevoerd, weet de service al dat uw prestatie profiel een grotere groep vereist dan de standaard grootte van 50 eDTU en wordt u aangeraden te beginnen met een 100-eDTU-instelling.

      ![Advies](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Voor deze zelf studie verlaat u de standaard waarde op 50 Edtu's en klikt u op opnieuw **selecteren** .
   1. Selecteer **OK** om de nieuwe groep te maken en de geselecteerde data bases ernaar te verplaatsen.

Het duurt enkele minuten om de groep te maken en de data bases te verplaatsen. Als data bases worden verplaatst, blijven deze online en volledig toegankelijk tot het laatste moment, waarbij alle open verbindingen worden gesloten. Als u enige poging hebt gedaan, worden clients vervolgens verbonden met de data base in de nieuwe groep.

Blader naar **Pool2** ( *tenants1-dpt-\<gebruiker\>* server) om de groep te openen en de prestaties te bewaken. Als u deze niet ziet, wacht u totdat de nieuwe groep is ingericht.

U ziet nu dat resource gebruik op *Pool1* is verwijderd en dat *Pool2* nu net zo is geladen.

## <a name="manage-performance-of-an-individual-database"></a>Prestaties van een afzonderlijke Data Base beheren

Als een afzonderlijke data base in een groep een aanhoudende hoge belasting ondervindt, afhankelijk van de pool configuratie, kan het de bronnen in de groep meestal domineren en van invloed zijn op andere data bases. Als de activiteit waarschijnlijk enige tijd wordt voortgezet, kan de data base tijdelijk uit de groep worden verwijderd. Op deze manier kan de data base de extra resources bevatten die nodig zijn en deze isoleren van de andere data bases.

Deze oefening simuleert het effect van een hoge load voor Contoso Concert Hall wanneer kaartjes voor een populair concert in de verkoop gaan.

1. Open in de **Power shell-ISE**het script...\\*demo-PerformanceMonitoringAndManagement. ps1* .
1. Stel **$DemoScenario = 5 in, Genereer een normale belasting plus een hoge belasting voor één Tenant (ongeveer 95 DTU).**
1. Stel **$SingleTenantDatabaseName = contosoconcerthall** in
1. Voer het script uit met **F5**.


1. Blader in het [Azure Portal](https://portal.azure.com)naar de lijst met data bases op de *tenants1-dpt-\<gebruiker\>* server. 
1. Klik op de **contosoconcerthall** -data base.
1. Klik op de pool waarin **contosoconcerthall** zich bevindt. Zoek de groep in de sectie **elastische pool** .

1. Inspecteer de **bewakings grafiek voor elastische Pools** en zoek naar het toegenomen gebruik van de groeps-eDTU. Na een paar minuten moet de hogere load actief worden en zou de pool snel een gebruik van 100% moeten weergeven.
2. Inspecteer de weer gave voor **Elastic data base-bewaking** , waarin de nieuwste data bases in het afgelopen uur worden weer gegeven. De *contosoconcerthall* -data base moet binnenkort worden weer gegeven als een van de vijf nieuwste data bases.
3. **Klik op de bewakings grafiek voor Elastic data base** en de pagina **database resource gebruik** wordt geopend, waar u de data bases kunt bewaken. Hiermee kunt u de weer gave voor de *contosoconcerthall* -data base isoleren.
4. Klik in de lijst met data bases op **contosoconcerthall**.
5. Klik op **prijs categorie (dtu's schalen)** om de pagina **prestaties configureren** te openen, waar u een zelfstandige reken grootte voor de Data Base kunt instellen.
6. Klik op het tabblad **Standard** om de schaalopties in de categorie Standard te openen.
7. Schuif de **DTU-schuif regelaar** naar rechts om **100** dtu's te selecteren. Opmerking: Dit komt overeen met de service doelstelling **S3**.
8. Klik op **Toep assen** om de data base uit de pool te verplaatsen en een *standaard S3* -data base te maken.
9. Wanneer het schalen is voltooid, controleert u het effect op de contosoconcerthall-data base en Pool1 op de Blade elastische pool en data base.

Zodra de hoge belasting voor de contosoconcerthall-data base-subkanten wordt weer gegeven, moet u deze onmiddellijk terugsturen naar de groep om de kosten te verlagen. Als het onduidelijk is wanneer dit gebeurt, kunt u een waarschuwing instellen voor de data base die wordt geactiveerd wanneer het DTU-gebruik het maximum van de gegevens per data base in de pool daalt. Het verplaatsen van een database naar een pool wordt beschreven in oefening 5.

## <a name="other-performance-management-patterns"></a>Andere patronen voor prestatie beheer

**Pre-preventieve schalen** In de bovenstaande oefening kunt u zien hoe u een geïsoleerde Data Base kunt schalen, wist u welke data base u zoekt. Als het beheer van de concert hal van Contoso heeft geknoeid met de Wingtips van de bedreigende ticket verkoop, zou de data base uit de bekend van de pool kunnen worden verwijderd. Anders was er waarschijnlijk een waarschuwing voor de pool of de database nodig geweest om te zien wat er aan de hand was. U wilt dit liever niet te weten komen doordat andere tenants in de pool klagen over verminderde prestaties. Als de tenant kan voorspellen hoe lang deze aanvullende resources nodig heeft, kunt u een Azure Automation-runbook instellen om de database uit de pool te plaatsen en later weer terug de plaatsen volgens een ingesteld schema.

**Self-service schalen voor tenants** Omdat schalen een taak is die eenvoudig via de Management-API kan worden aangeroepen, kunt u gemakkelijk de mogelijkheid om tenantdatabases te schalen inbouwen in uw tenantgerichte toepassing en deze als een functie van uw SaaS-service aanbieden. Laat tenants bijvoorbeeld zelf omhoog en omlaag schalen beheren, mogelijk gekoppeld aan hun facturering.

**Een pool omhoog en omlaag schalen volgens een schema dat overeenkomt met gebruiks patronen**

Wanneer het cumulatieve tenantgebruik een voorspelbaar patroon volgt, kunt u Azure Automation gebruiken om een pool volgens een schema omhoog en omlaag te schalen. Schaal een pool bijvoorbeeld omlaag na 18:00 uur en weer omhoog vóór 06:00 op dagen waarvan u weet dat er een afname in de benodigde resources is.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik op de tenantdatabases simuleren door het uitvoeren van een geleverde load-generator
> * De tenant-databases bewaken terwijl ze reageren op de toename in load
> * Omhoog schalen van de elastische pool als reactie op de verhoogde databaseload
> * Een tweede elastische pool inrichten om taken te verdelen voor de databaseactiviteit

[Zelfstudie Een individuele tenant herstellen](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Aanvullende bronnen

* Aanvullende [zelf studies die voortbouwen op de Wingtip tickets SaaS data base per Tenant toepassings implementatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastische SQL-pools](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Azure monitor logboeken](saas-dbpertenant-log-analytics.md) : zelf studie over het instellen en gebruiken van Azure monitor-logboeken
