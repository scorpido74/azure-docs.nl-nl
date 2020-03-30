---
title: 'Saas-app: controleer de prestaties van veel databases'
description: De prestaties van Azure SQL-databases en -pools bewaken en beheren in een SaaS-app met meerdere tenants
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 34c50795567615637e31446ad3dc51a5e1b355f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214468"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>De prestaties van Azure SQL-databases en -pools bewaken en beheren in een SaaS-app met meerdere tenants

In deze zelfstudie worden verschillende belangrijke prestatiebeheerscenario's onderzocht die worden gebruikt in SaaS-toepassingen. Met behulp van een load generator om activiteit te simuleren in alle tenant databases, de ingebouwde monitoring en waarschuwing en waarschuwing en waarschuwing en elastische pools worden aangetoond.

De Wingtip Tickets SaaS Database Per Tenant app maakt gebruik van een single-tenant data model, waarbij elke locatie (tenant) zijn eigen database heeft. Net als bij veel andere SaaS-toepassingen, is het verwachte tenantworkloadpatroon onvoorspelbaar en sporadisch. Met andere woorden, kaartverkoop kan op ieder moment plaatsvinden. Om te profiteren van dit typische databasegebruikspatroon, worden tenantdatabases geïmplementeerd in elastische pools. Elastische pools verlagen de kosten van een oplossing door resources over veel databases te verdelen. Bij dit type patroon is het belangrijk om het gebruik van database- en poolresources te controleren om ervoor te zorgen dat loads goed over pools zijn verdeeld. Ook moet u ervoor zorgen dat individuele databases voldoende resources hebben en dat pools niet hun [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)-limieten overschrijden. In deze zelfstudie worden manieren toegelicht om databases en pools te controleren en beheren. Ook wordt uitgelegd hoe u corrigerende maatregelen kunt nemen als reactie op wisselingen in de workload.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Gebruik op de tenantdatabases simuleren door het uitvoeren van een geleverde load-generator
> * De tenant-databases bewaken terwijl ze reageren op de toename in load
> * Omhoog schalen van de elastische pool als reactie op de verhoogde databaseload
> * Een tweede elastische pool inrichten om taken te verdelen voor de databaseactiviteit


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets SaaS Database Per Tenant app wordt geïmplementeerd. Zie [De SaaS-database van Wingtip Tickets per tenant](saas-dbpertenant-get-started-deploy.md) implementeren en verkennen in minder dan vijf minuten.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-saas-performance-management-patterns"></a>Inleiding tot SaaS performance management patronen

Het beheren van de databaseprestaties bestaat uit het verzamelen en analyseren van prestatiegegevens en het reageren op deze gegevens door parameters aan te passen om een acceptabele reactietijd voor de toepassing te behouden. Bij het hosten van meerdere tenants zijn Elastische pools een kosteneffectieve manier om resources te bieden en te beheren voor een groep databases met onvoorspelbare workloads. Met bepaalde workloadpatronen kan het al bij twee S3-databases voordelig zijn om ze door een pool te laten beheren.

![toepassingsdiagram](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pools en de databases in pools moeten worden gecontroleerd om ervoor te zorgen dat ze binnen aanvaardbare prestatiebereiken blijven. Stem de poolconfiguratie af op de behoeften van de totale werkbelasting van alle databases, zodat de eDT's van de groep geschikt zijn voor de algehele werkbelasting. Pas de minimale en maximale eDTU-waarden per database aan naar de geschikte waarden voor de vereisten van uw specifieke toepassing.

### <a name="performance-management-strategies"></a>Strategieën voor prestatiebeheer

* Om te voorkomen dat u de prestaties handmatig moet controleren, is het het meest effectief om waarschuwingen in te **stellen die worden geactiveerd wanneer databases of pools buiten de normale bereiken afwijken.**
* Om te reageren op kortetermijnschommelingen in de totale rekengrootte van een pool, kan het **eDTU-niveau**van de groep worden opgeschaald of omlaag . Als deze fluctuatie regelmatig of op voorspelbare momenten optreedt, **kan het schalen van de pool op automatisch worden ingesteld**. U kunt bijvoorbeeld omlaag schalen wanneer de workload licht is, zoals 's nachts of tijdens het weekend.
* U kunt ook **afzonderlijke databases naar andere pools verplaatsen** bij fluctuaties op de langere termijn of wijzigingen in het aantal databases.
* Om te reageren op korte termijn verhogingen in *individuele* database belasting **individuele databases kunnen worden genomen uit een pool en toegewezen een individuele compute size**. Wanneer de load is verlaagd, kan de database naar de pool worden teruggezet. Wanneer dit van tevoren bekend is, kunnen databases preventief worden verplaatst om ervoor te zorgen dat de database altijd over de middelen beschikt die het nodig heeft, en om impact op andere databases in de groep te voorkomen. Als het om een voorspelbare vereiste gaat, zoals bij een locatie die met een toename in kaartverkoop te maken krijgt voor een populair evenement, kan dit beheergedrag in de toepassing worden geïntegreerd.

[Azure Portal](https://portal.azure.com) biedt ingebouwde functionaliteit voor bewaking en waarschuwingen voor de meeste resources. Voor SQL Database zijn bewaking en waarschuwingen beschikbaar voor databases en pools. Deze ingebouwde monitoring en waarschuwing is resource-specifiek, dus het is handig om te gebruiken voor kleine aantallen resources, maar is niet erg handig bij het werken met veel middelen.

Voor scenario's met een hoog volume, waarbij u met veel resources werkt, kunnen [Azure Monitor-logboeken](saas-dbpertenant-log-analytics.md) worden gebruikt. Dit is een aparte Azure-service die analyses biedt over uitgestraalde logboeken die zijn verzameld in een Log Analytics-werkruimte. Azure Monitor-logboeken kunnen telemetrie verzamelen van veel services en worden gebruikt om waarschuwingen op te vragen en in te stellen.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Download de Wingtip Tickets SaaS Database Per Tenant applicatie scripts

De Wingtip Tickets SaaS Multi-tenant Database scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren.

## <a name="provision-additional-tenants"></a>Extra tenants inrichten

Hoewel pools al met twee S3-databases rendabel kunnen zijn, geldt dat hoe meer databases er in de pool zitten, hoe rendabeler het berekenen van gemiddelden wordt. U hebt voor deze zelfstudie ten minste 20 geïmplementeerde databases nodig om een goed inzicht te krijgen in de werking van het bewaken en beheren van prestaties op schaal.

Als u al een batch tenants in een eerdere zelfstudie hebt ingericht, gaat u naar het [gebruik simuleren in alle tenantdatabasessectie.](#simulate-usage-on-all-tenant-databases)

1. In de **PowerShell ISE**, open ... \\Learning\\Modules Performance\\Monitoring en Management*Demo-PerformanceMonitoringAndManagement.ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1.  = $DemoScenario **$DemoScenario****1**instellen , een partij **huurders inrichten**
1. Druk op **F5** om het script uit te voeren.

Het script implementeert 17 tenants in minder dan vijf minuten.

Het script *Nieuw-TenantBatch* maakt gebruik van een geneste of gekoppelde set [Resource Manager-sjablonen](../azure-resource-manager/index.yml) die een batch tenants maken, die standaard de **basistenantdb** van de database op de catalogusserver kopieert om de nieuwe tenantdatabases te maken, deze vervolgens in de catalogus registreert en deze uiteindelijk initialiseert met de tenantnaam en het type locatie. Dit komt overeen met de manier waarop de app een nieuwe tenant indient. Eventuele wijzigingen in *basetenantdb* worden toegepast op nieuwe huurders die daarna worden ingericht. Zie de [zelfstudie Schemabeheer](saas-tenancy-schema-management.md) om te zien hoe u schemawijzigingen aanbrengen in *bestaande* tenantdatabases (inclusief de *basetenantdb-database).*

## <a name="simulate-usage-on-all-tenant-databases"></a>Gebruik simuleren op alle tenantdatabases

Het *Demo-PerformanceMonitoringAndManagement.ps1-script* wordt geleverd dat een workload simuleert die wordt uitgevoerd ten opzichte van alle tenantdatabases. De belasting wordt gegenereerd met behulp van een van de beschikbare belastingsscenario's:

| Demo | Scenario |
|:--|:--|
| 2 | Normale intensiteitsbelasting genereren (ongeveer 40 DTU) |
| 3 | Load genereren met langere en frequentere bursts per database|
| 4 | Belasting genereren met hogere DTU-bursts per database (ongeveer 80 DTU)|
| 5 | Een normale belasting genereren plus een hoge belasting van een enkele tenant (ongeveer 95 DTU)|
| 6 | Niet-gebalanceerde load genereren voor meerdere pools|

De load-generator past een *synthetische* load alleen voor CPU toe op elke tenantdatabase. De generator start voor elke tenantdatabase een taak, die periodiek een opgeslagen procedure aanroept die de load genereert. De load-niveaus (in eDTU's), duur en intervallen zijn verschillend voor alle databases. Dit simuleert onvoorspelbare tenantactiviteit.

1. In de **PowerShell ISE**, open ... \\Learning\\Modules Performance\\Monitoring en Management*Demo-PerformanceMonitoringAndManagement.ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel **$DemoScenario** = **2**, *Genereer normale intensiteit belasting*.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.

Wingtip Tickets SaaS Database Per Tenant is een SaaS app, en de real-world belasting op een SaaS app is meestal sporadisch en onvoorspelbaar. Om dit te simuleren, produceert de load-generator een willekeurige workload die over alle tenants wordt verdeeld. Er zijn enkele minuten nodig om het belastingspatroon te laten ontstaan, dus voer de laadgenerator 3-5 minuten uit voordat u probeert de belasting in de volgende secties te controleren.

> [!IMPORTANT]
> De load-generator wordt uitgevoerd als een reeks taken in uw lokale PowerShell-sessie. Houd het tabblad *Demo-PerformanceMonitoringAndManagement.ps1* geopend. Als u het tabblad sluit of de computer onderbreekt, stopt de load-generator. De laadgenerator blijft in een *taak-inroepende* staat waar het belasting genereert op eventuele nieuwe tenants die worden ingericht nadat de generator is gestart. Gebruik *Ctrl-C* om geen nieuwe taken meer aan te roepen en het script te verlaten. De laadgenerator blijft draaien, maar alleen op bestaande tenants.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Brongebruik bewaken met de Azure-portal

Als u het resourcegebruik wilt controleren dat voortvloeit uit de toepassing van de belasting, opent u de portal naar de groep met de tenantdatabases:

1. Open de [Azure-portal](https://portal.azure.com) en blader naar de *server&lt;tenants1-dpt- USER.&gt; *
1. Scrol naar beneden, zoek de elastische pools en klik op **Pool1**. Deze pool bevat alle tenantdatabases die tot nu toe zijn gemaakt.

Let op de **elastic pool monitoring** en Elastic database **monitoring** charts.

Het resourcegebruik van de groep is het totale databasegebruik voor alle databases in de groep. De database grafiek toont de vijf heetste databases:

![databasediagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Omdat er extra databases in de groep buiten de top vijf staan, wordt in het gebruik van de groep activiteit weergegeven die niet wordt weergegeven in de top vijf van de databasesgrafiek. Klik voor meer informatie op **Databaseresourcegebruik:**

![gebruik van databasebronnen](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Prestatiewaarschuwingen voor de pool instellen

Stel een waarschuwing in op \>de groep die het volgende gebruik van 75% activeert:

1. Open *Pool1* (op de *server\<van\> de tenants1-dpt- gebruiker)* in de [Azure-portal.](https://portal.azure.com)
1. Klik op **Waarschuwingsregels** en klik vervolgens op **+ Waarschuwing toevoegen**:

   ![waarschuwing toevoegen](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Geef een naam op, bijvoorbeeld **hoog DTU**.
1. Stel de volgende waarden in:
   * **Metriek = eDTU-percentage**
   * **Conditie = groter dan**
   * **Drempelwaarde = 75**
   * **Periode = In de laatste 30 minuten**
1. Voeg een e-mailadres toe aan het vak *E-mail(s) van de extra beheerder* en klik op **OK**.

   ![waarschuwing instellen](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Een zwaar belaste pool omhoog schalen

Als het cumulatieve loadniveau voor een pool in een dergelijke mate toeneemt dat de pool volledig wordt gebruikt en 100% eDTU-gebruik wordt bereikt, worden de prestaties van afzonderlijke databases beïnvloed. Hierdoor wordt de reactietijd voor query's mogelijk vertraagd voor alle databases in de pool.

**Op korte termijn**u overwegen de groep op te schalen om extra resources te bieden of databases uit de groep te verwijderen (ze verplaatsen naar andere groepen of uit de groep naar een zelfstandige servicelaag).

**Op langere termijn**u overwegen query's of indexgebruik te optimaliseren om de databaseprestaties te verbeteren. Afhankelijk van de gevoeligheid voor prestatieproblemen van de toepassing, is de aanbevolen procedure om een pool op te schalen voordat 100% eDTU-gebruik wordt bereikt. Gebruik een waarschuwing zodat u tijdig wordt gewaarschuwd.

U kunt een zwaar belaste pool simuleren door de load die de generator produceert, te verhogen. Waardoor de databases vaker barsten en langer de totale belasting van de groep verhogen zonder de vereisten van de afzonderlijke databases te wijzigen. U kunt de pool eenvoudig opschalen in de portal of vanuit PowerShell. In deze oefening wordt de portal gebruikt.

1. Stel *$DemoScenario* = **3**in , Laad met langere _en frequentere bursts per database_ om de intensiteit van de totale belasting van de groep te verhogen zonder de piekbelasting te wijzigen die elke database vereist.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.

1. Ga naar **Pool1** in de Azure-portal.

Monitor het verhoogde eDTU-gebruik van de groep op de bovenste grafiek. Het duurt een paar minuten voor de nieuwe hogere belasting te schoppen in, maar je moet snel zien het zwembad beginnen te raken max gebruik, en als de belasting steadies in het nieuwe patroon, het snel overbelast het zwembad.

1. Als u de groep wilt opschalen, klikt u boven aan de pagina **Pool1** **configureren.**
1. Pas de **eDTU-instelling van de groep** aan op **100**. Als u de groeps-eDTU wijzigt, wordt de instelling per database niet gewijzigd (deze is nog steeds maximaal 50 eDTU's per database). U de instellingen per database aan de rechterkant van de **poolpagina configureren** bekijken.
1. Klik **op Opslaan** om de aanvraag in te dienen om de groep te schalen.

Ga terug naar **Zwembad1-overzicht** > **Overview** om de bewakingsgrafieken te bekijken. Monitor het effect van het verstrekken van de pool met meer middelen (hoewel met weinig databases en een gerandomiseerde belasting is het niet altijd gemakkelijk om definitief te zien totdat u voor enige tijd). Houd er rekening mee dat 100% bij de bovenste grafiek nu staat voor 100 eDTU's, terwijl voor de onderste grafiek 100% nog steeds 50 eDTU's betekent, omdat het maximum per database 50 eDTU's is.

Databases blijven gedurende het proces online en volledig beschikbaar. Op het laatste moment, wanneer elke database gereed is om te worden ingeschakeld met de nieuwe pool-eDTU's, worden alle actieve verbindingen verbroken. Om verbroken verbindingen opnieuw te proberen, moet altijd een toepassingscode worden geschreven zodat er opnieuw verbinding wordt gemaakt met de database in de omhoog geschaalde pool.

## <a name="load-balance-between-pools"></a>Load balance tussen pools

In plaats van de pool omhoog te schalen, kunt u ook een tweede pool maken en databases hiernaartoe verplaatsen om de load tussen de twee pools te verdelen. Om dit te doen, moet de nieuwe pool op dezelfde server als de eerste pool worden gemaakt.

1. Open in de [Azure-portal](https://portal.azure.com)de **server&lt;&gt; tenants1-dpt- USER.**
1. Klik **op + Nieuwe groep** om een groep op de huidige server te maken.
1. Ga als een voorbeeld van de **groep Elastisch:**

   1. **Naam** instellen op *Pool2*.
   1. Laat de prijscategorie op **Standaardpool** staan.
   1. Klik **op Groep configureren**,
   1. Stel **de eDTU van de groep** in op *50 eDTU*.
   1. Klik **op Databases toevoegen** om een lijst met databases op de server te zien die aan *Pool2*kunnen worden toegevoegd.
   1. Selecteer tien databases om deze naar de nieuwe groep te verplaatsen en klik op **Selecteren**. Als u de laadgenerator hebt uitgevoerd, weet de service al dat uw prestatieprofiel een grotere pool vereist dan de standaard 50 eDTU-grootte en raadt u aan te beginnen met een 100 eDTU-instelling.

      ![Aanbeveling](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Laat voor deze zelfstudie de standaardinstelling op 50 eDTU's staan en klik nogmaals op **Selecteren.**
   1. Selecteer **OK** om de nieuwe groep te maken en de geselecteerde databases erin te verplaatsen.

Het maken van de pool en het verplaatsen van de databases duurt een paar minuten. Als databases worden verplaatst blijven ze online en volledig toegankelijk tot het allerlaatste moment, waarna alle open verbindingen worden gesloten. Zolang u een aantal logica voor nieuwe apparaten hebt, maken clients verbinding met de database in de nieuwe groep.

Blader naar **Pool2** (op de server van de *tenants1-dpt-\<gebruiker)\> * om de pool te openen en de prestaties ervan te controleren. Als u het niet ziet, wacht op het inrichten van de nieuwe pool om te voltooien.

U ziet nu dat het resourcegebruik op *Pool1* is gedaald en dat *Pool2* nu op dezelfde manier is geladen.

## <a name="manage-performance-of-an-individual-database"></a>Prestaties van een afzonderlijke database beheren

Als een afzonderlijke database in een groep een aanhoudende hoge belasting ervaart, afhankelijk van de poolconfiguratie, kan deze de resources in de groep domineren en andere databases beïnvloeden. Als de activiteit waarschijnlijk enige tijd zal worden voortgezet, kan de database tijdelijk uit de groep worden verwijderd. Hierdoor kan de database de extra resources hebben die het nodig heeft, en isoleert het van de andere databases.

Deze oefening simuleert het effect van een hoge load voor Contoso Concert Hall wanneer kaartjes voor een populair concert in de verkoop gaan.

1. In de **PowerShell ISE,** open de ... \\ *Demo-PerformanceMonitoringAndManagement.ps1* script.
1. Stel **$DemoScenario = 5, Genereer een normale belasting plus een hoge belasting op een enkele tenant (ongeveer 95 DTU).**
1. Stel **$SingleTenantDatabaseName = contosoconcerthall** in
1. Voer het script uit met **F5**.


1. Blader in de [Azure-portal](https://portal.azure.com)naar de lijst met databases op de server met *\<tenants1-dpt- gebruikers.\> * 
1. Klik op de **contosoconcerthall** database.
1. Klik op het zwembad waar **contosoconcerthall** in zit. Zoek het zwembad in het **gedeelte Elastisch zwembad.**

1. Controleer de **elastic pool monitoring** chart en zoek naar het verhoogde eDTU-gebruik van de groep. Na een paar minuten moet de hogere load actief worden en zou de pool snel een gebruik van 100% moeten weergeven.
2. Controleer het **scherm Elastic database monitoring,** dat de populairste databases van het afgelopen uur weergeeft. De *contosoconcerthall* database moet binnenkort verschijnen als een van de vijf heetste databases.
3. Klik op de **grafiek** **Met elastische databasebewaking** en hiermee wordt de pagina **Databaseresourcegebruik** geopend waar u een van de databases controleren. Hiermee u het display voor de *contosoconcerthall-database* isoleren.
4. Klik in de lijst met databases op **contosoconcerthall**.
5. Klik **op Prijsniveau (schaal DTU)** om de **prestatiepagina configureren** te openen, waar u een zelfstandige rekengrootte voor de database instellen.
6. Klik op het tabblad **Standard** om de schaalopties in de categorie Standard te openen.
7. Schuif de **DTU-schuifregelaar** naar rechts om **100** DTU's te selecteren. Let op: dit komt overeen met de **servicedoelstelling, S3**.
8. Klik **op Toepassen** om de database uit de groep te verplaatsen en er een Standaard *S3-database* van te maken.
9. Zodra de schaling is voltooid, controleert u het effect op de contosoconcerthall-database en Pool1 op de elastische pool- en databasebladen.

Zodra de hoge belasting op de contosoconcerthall database afneemt, moet u deze onmiddellijk terugsturen naar het zwembad om de kosten te verlagen. Als het onduidelijk is wanneer dat zal gebeuren, u een waarschuwing instellen op de database die wordt geactiveerd wanneer het DTU-gebruik onder de max-database per database op de pool zakt. Het verplaatsen van een database naar een pool wordt beschreven in oefening 5.

## <a name="other-performance-management-patterns"></a>Andere prestatiemanagementpatronen

**Preventief schalen** In de oefening hierboven waar je onderzocht hoe je een geïsoleerde database schaal, wist je welke database te zoeken. Als het management van Contoso Concert Hall Wingtips op de hoogte had gesteld van de op handen zijnde kaartverkoop, had de database preventief uit het zwembad kunnen worden verplaatst. Anders was er waarschijnlijk een waarschuwing voor de pool of de database nodig geweest om te zien wat er aan de hand was. U wilt dit liever niet te weten komen doordat andere tenants in de pool klagen over verminderde prestaties. Als de tenant kan voorspellen hoe lang deze aanvullende resources nodig heeft, kunt u een Azure Automation-runbook instellen om de database uit de pool te plaatsen en later weer terug de plaatsen volgens een ingesteld schema.

**Self-service schalen voor tenants** Omdat schalen een taak is die eenvoudig via de Management-API kan worden aangeroepen, kunt u gemakkelijk de mogelijkheid om tenantdatabases te schalen inbouwen in uw tenantgerichte toepassing en deze als een functie van uw SaaS-service aanbieden. Laat tenants bijvoorbeeld zelf omhoog en omlaag schalen beheren, mogelijk gekoppeld aan hun facturering.

**Een pool omhoog en omlaag schalen volgens een schema om aan gebruikspatronen te voldoen**

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

* Aanvullende [zelfstudies die voortbouwen op de Wingtip Tickets SaaS Database Per Tenant-toepassing implementatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [SQL Elastic-groepen](sql-database-elastic-pool.md)
* [Azure-automatisering](../automation/automation-intro.md)
* [Azure Monitor-logboeken](saas-dbpertenant-log-analytics.md) - Zelfstudie Azure Monitor-logboeken instellen en gebruiken
