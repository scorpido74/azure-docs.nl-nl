---
title: De prestaties van een geshard multitenant-database controleren
description: De prestaties van geshard azure SQL-database met meerdere tenant's bewaken en beheren in een SaaS-app met meerdere tenants
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
ms.openlocfilehash: 0af476b69f2effd836fe76d62059259076c16f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214164"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>De prestaties van geshard azure SQL-database met meerdere tenant's bewaken en beheren in een SaaS-app met meerdere tenants

In deze zelfstudie worden verschillende belangrijke prestatiebeheerscenario's onderzocht die worden gebruikt in SaaS-toepassingen. Met behulp van een load generator om activiteit te simuleren in geshard multi-tenant databases, de ingebouwde monitoring en waarschuwing en waarschuwing en van SQL Database worden aangetoond.

De Wingtip Tickets SaaS Multi-tenant Database-app maakt gebruik van een geshard multi-tenant datamodel, waarbij locatiegegevens (tenant) worden gedistribueerd door tenant-ID over mogelijk meerdere databases. Net als bij veel andere SaaS-toepassingen, is het verwachte tenantworkloadpatroon onvoorspelbaar en sporadisch. Met andere woorden, kaartverkoop kan op ieder moment plaatsvinden. Om te profiteren van dit typische databasegebruikspatroon, kunnen databases op en neer worden geschaald om de kosten van een oplossing te optimaliseren. Met dit type patroon is het belangrijk om het gebruik van databasebronnen te controleren om ervoor te zorgen dat de belastingen redelijk in evenwicht zijn in mogelijk meerdere databases. U moet er ook voor zorgen dat afzonderlijke databases over voldoende resources beschikken en hun [DTU-limieten](sql-database-purchase-models.md#dtu-based-purchasing-model) niet overschrijden. In deze zelfstudie wordt nagegaan hoe databases kunnen worden gecontroleerd en gecontroleerd en hoe u corrigerende maatregelen nemen als reactie op variaties in werkbelasting.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Het gebruik van een geshard multi-tenantdatabase simuleren door een meegeleverde laadgenerator uit te voeren
> * Controleer de database terwijl deze reageert op de toename van de belasting
> * De database opschalen naar aanleiding van de toegenomen databasebelasting
> * Een tenant ineenzetten in een database met één tenant

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets SaaS Multi-tenant Database app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Multi-tenant Database-toepassing implementeren en verkennen](saas-multitenantdb-get-started-deploy.md) als u deze in minder dan vijf minuten wilt implementeren.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-saas-performance-management-patterns"></a>Inleiding tot SaaS performance management patronen

Het beheren van de databaseprestaties bestaat uit het verzamelen en analyseren van prestatiegegevens en het reageren op deze gegevens door parameters aan te passen om een acceptabele reactietijd voor de toepassing te behouden. 

### <a name="performance-management-strategies"></a>Strategieën voor prestatiebeheer

* Om te voorkomen dat u de prestaties handmatig moet controleren, is het het meest effectief om waarschuwingen in te **stellen die worden geactiveerd wanneer databases buiten het normale bereik afwijken.**
* Om te reageren op kortetermijnschommelingen in de rekengrootte van een database, kan het **DTU-niveau worden opgeschaald of omlaag**. Als deze fluctuatie zich regelmatig of voorspelbaar voordoet, **kan het schalen van de database automatisch plaatsvinden.** U kunt bijvoorbeeld omlaag schalen wanneer de workload licht is, zoals 's nachts of tijdens het weekend.
* Om te reageren op schommelingen op langere termijn, of wijzigingen in de tenants, **kunnen individuele tenants worden verplaatst naar andere database**.
* Om te reageren op een toename van *de individuele* tenantbelasting op korte termijn, kunnen afzonderlijke tenants uit een database worden gehaald en een **individuele rekengrootte toegewezen.** Zodra de belasting is verminderd, kan de tenant vervolgens worden teruggestuurd naar de multi-tenant database. Wanneer dit van tevoren bekend is, kunnen huurders preventief worden verplaatst om ervoor te zorgen dat de database altijd over de resources beschikt die het nodig heeft, en om impact op andere tenants in de multi-tenantdatabase te voorkomen. Als het om een voorspelbare vereiste gaat, zoals bij een locatie die met een toename in kaartverkoop te maken krijgt voor een populair evenement, kan dit beheergedrag in de toepassing worden geïntegreerd.

[Azure Portal](https://portal.azure.com) biedt ingebouwde functionaliteit voor bewaking en waarschuwingen voor de meeste resources. Voor SQL Database is monitoring en waarschuwing beschikbaar in databases. Deze ingebouwde monitoring en waarschuwing is resource-specifiek, dus het is handig om te gebruiken voor kleine aantallen resources, maar is niet handig bij het werken met veel bronnen.

Voor scenario's met een hoog volume, waarbij u met veel resources werkt, kunnen [Azure Monitor-logboeken](https://azure.microsoft.com/services/log-analytics/) worden gebruikt. Dit is een aparte Azure-service die analyses biedt over uitgestraalde logboeken die zijn verzameld in een Log Analytics-werkruimte. Azure Monitor-logboeken kunnen telemetrie verzamelen van veel services en worden gebruikt om waarschuwingen op te vragen en in te stellen.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Download de broncode en scripts van de Wingtip Tickets SaaS Multi-tenant Database-toepassing

De Wingtip Tickets SaaS Multi-tenant Database scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub repo. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren.

## <a name="provision-additional-tenants"></a>Extra tenants inrichten

Voor een goed begrip van hoe prestatiebewaking en -beheer op schaal werkt, vereist deze zelfstudie dat u meerdere tenants in een geshard multi-tenantdatabase hebt.

Als u al een batch tenants in een eerdere zelfstudie hebt ingericht, gaat u naar het [gebruik simuleren op alle tenantdatabasessectie.](#simulate-usage-on-all-tenant-databases)

1. In de **PowerShell ISE**, open ... \\Learning\\Modules Performance\\Monitoring en Management*Demo-PerformanceMonitoringAndManagement.ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1.  = $DemoScenario **$DemoScenario****1**instellen , een partij _huurders inrichten_
1. Druk op **F5** om het script uit te voeren.

Het script implementeert 17 tenants in de multi-tenant database in een paar minuten. 

Het script *Nieuw-TenantBatch* maakt nieuwe tenants met unieke tenantsleutels in de gesharde multitenantdatabase en initialiseert deze met de tenantnaam en locatietype. Dit komt overeen met de manier waarop de app een nieuwe tenant indient. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Gebruik simuleren op alle tenantdatabases

Het *Demo-PerformanceMonitoringAndManagement.ps1-script* wordt geleverd dat een workload simuleert die wordt uitgevoerd ten opzichte van de multi-tenantdatabase. De belasting wordt gegenereerd met behulp van een van de beschikbare belastingsscenario's:

| Demo | Scenario |
|:--|:--|
| 2 | Normale intensiteitsbelasting genereren (ongeveer 30 DTU) |
| 3 | Load genereren met langere bursts per tenant|
| 4 | Laad genereren met hogere DTU-bursts per tenant (ongeveer 70 DTU)|
| 5 | Een hoge intensiteit (ongeveer 90 DTU) genereren op één tenant plus een normale intensiteitsbelasting op alle andere tenants |

De load-generator past een *synthetische* load alleen voor CPU toe op elke tenantdatabase. De generator start voor elke tenantdatabase een taak, die periodiek een opgeslagen procedure aanroept die de load genereert. De belastingsniveaus (in DTU's), duur en intervallen zijn gevarieerd in alle databases, waardoor onvoorspelbare tenantactiviteit wordt simuleren.

1. In de **PowerShell ISE**, open ... \\Learning\\Modules Performance\\Monitoring en Management*Demo-PerformanceMonitoringAndManagement.ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel **$DemoScenario** = **2**in , _Normale intensiteitsbelasting genereren_
1. Druk op **F5** om een belasting toe te passen op al uw huurders.

Wingtip Tickets SaaS Multi-tenant Database is een SaaS-app en de real-world belasting van een SaaS-app is meestal sporadisch en onvoorspelbaar. Om dit te simuleren, produceert de load-generator een willekeurige workload die over alle tenants wordt verdeeld. Er zijn enkele minuten nodig om het belastingspatroon te laten ontstaan, dus voer de laadgenerator 3-5 minuten uit voordat u probeert de belasting in de volgende secties te controleren.

> [!IMPORTANT]
> De laadgenerator wordt uitgevoerd als een reeks taken in een nieuw PowerShell-venster. Als u de sessie sluit, stopt de laadgenerator. De laadgenerator blijft in een *taak-inroepende* staat waar het belasting genereert op eventuele nieuwe tenants die worden ingericht nadat de generator is gestart. Gebruik *Ctrl-C* om geen nieuwe taken meer aan te roepen en het script te verlaten. De laadgenerator blijft draaien, maar alleen op bestaande tenants.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Brongebruik bewaken met de Azure-portal

Als u het resourcegebruik wilt controleren dat voortvloeit uit de toepassing van de belasting, opent u de portal naar de database met **meerdere tenants1**, met de tenants:

1. Open de [Azure-portal](https://portal.azure.com) en blader naar de *servertenants1-mt-USER.&lt;&gt;*
1. Scroll naar beneden en zoek databases en klik op **tenants1**. Deze gesharde multi-tenant database bevat alle tenants die tot nu toe zijn gemaakt.

![databasediagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Bekijk de **DTU-grafiek.**

## <a name="set-performance-alerts-on-the-database"></a>Prestatiewaarschuwingen instellen in de database

Stel een waarschuwing in op \>de database die wordt geactiveerd op 75% gebruik als volgt:

1. Open de *tenants1-database* (op de *gebruikersserver&lt;van tenants1 mt- gebruiker)&gt; * in de [Azure-portal.](https://portal.azure.com)
1. Klik op **Waarschuwingsregels** en klik vervolgens op **+ Waarschuwing toevoegen**:

   ![waarschuwing toevoegen](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Geef een naam op, bijvoorbeeld **hoog DTU**.
1. Stel de volgende waarden in:
   * **Metric = DTU-percentage**
   * **Conditie = groter dan**
   * **Drempel = 75**.
   * **Periode = In de laatste 30 minuten**
1. Voeg een e-mailadres toe aan het vak *E-mail(s) van de extra beheerder* en klik op **OK**.

   ![waarschuwing instellen](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Een drukke database opschalen

Als het laadniveau op een database toeneemt tot het punt dat deze de database maxiseert en 100% DTU-gebruik bereikt, wordt de databaseprestaties beïnvloed, waardoor de responstijden van query's mogelijk worden vertraagd.

**Korte termijn**, overweeg de database op te schalen om extra bronnen te bieden of tenants uit de multi-tenantdatabase te verwijderen (ze uit de multi-tenantdatabase verplaatsen naar een zelfstandige database).

**Op langere termijn**u overwegen query's of indexgebruik te optimaliseren om de databaseprestaties te verbeteren. Afhankelijk van de gevoeligheid van de toepassing voor prestaties geeft de beste praktijk om een database op te schalen voordat deze 100% DTU-gebruik bereikt. Gebruik een waarschuwing zodat u tijdig wordt gewaarschuwd.

U een drukke database simuleren door de belasting van de generator te verhogen. Waardoor de tenants vaker barsten, en langer, het verhogen van de belasting op de multi-tenant database zonder dat de eisen van de individuele tenants. Het opschalen van de database gebeurt eenvoudig in de portal of vanuit PowerShell. In deze oefening wordt de portal gebruikt.

1. Stel *$DemoScenario* = **3**in , Laad met langere _en frequentere bursts per database_ om de intensiteit van de totale belasting in de database te verhogen zonder de piekbelasting te wijzigen die elke tenant nodig heeft.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.
1. Ga naar de **tenants1-database** in de Azure-portal.

Monitor het toegenomen DTU-gebruik van de database in de bovenste grafiek. Het duurt een paar minuten voor de nieuwe hogere belasting te schoppen in, maar je moet snel zien de database beginnen te max gebruik hit, en als de belasting steadies in het nieuwe patroon, het snel overbelast de database.

1. Als u de database wilt opschalen, klikt u op **Prijslaag (schaal DTV's)** in het instellingenblad.
1. Pas de **DTU-instelling** aan op **100**. 
1. Klik **op Toepassen** om de aanvraag in te dienen om de database te schalen.

Ga terug naar **tenants1** > **Overzicht** om de controlegrafieken te bekijken. Monitor het effect van het verstrekken van de database met meer middelen (hoewel met weinig tenants en een gerandomiseerde belasting is het niet altijd gemakkelijk om definitief te zien totdat u voor enige tijd). Terwijl u op zoek bent naar de grafieken in gedachten houden dat 100% op de bovenste grafiek vertegenwoordigt nu 100 DTUs, terwijl op de onderste grafiek 100% is nog steeds 50 DTUs.

Databases blijven gedurende het proces online en volledig beschikbaar. Toepassingscode moet altijd worden geschreven om verbroken verbindingen opnieuw te proberen, en maakt dus opnieuw verbinding met de database.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Een nieuwe tenant inrichten in de eigen database 

Met het geshard multitenantmodel u kiezen of u een nieuwe tenant wilt inrichten in een multi-tenantdatabase naast andere tenants, of dat u de tenant in een eigen database indient. Door een tenant in te richten in de eigen database, profiteert het van de isolatie die inherent is aan de afzonderlijke database, zodat u de prestaties van die tenant onafhankelijk van anderen beheren, die tenant onafhankelijk van anderen herstellen, enz. U er bijvoorbeeld voor kiezen om gratis proefversie- of vaste klanten in een database met meerdere tenant's en premiumklanten in afzonderlijke databases te plaatsen.  Als geïsoleerde databases met één tenant worden gemaakt, kunnen ze nog steeds collectief worden beheerd in een elastische groep om resourcekosten te optimaliseren.

Als u al een nieuwe tenant in de eigen database hebt ingericht, slaat u de volgende stappen over.

1. In de **PowerShell ISE**, open ... \\Leermodules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Wijzigen **$TenantName = "Salix Salsa"** en **$VenueType = "dans"**
1. $Scenario**2**instellen , _een tenant ineenstellen in een nieuwe database met één tenant_ **$Scenario** = 
1. Druk op **F5** om het script uit te voeren.

Het script zal deze tenant in een aparte database, registreren van de database en de tenant met de catalogus, en open vervolgens de tenant gebeurtenissen pagina in de browser. Vernieuw de pagina Evenementenhub en je ziet dat "Salix Salsa" is toegevoegd als locatie.

## <a name="manage-performance-of-an-individual-database"></a>Prestaties van een afzonderlijke database beheren

Als een enkele tenant binnen een multi-tenantdatabase een aanhoudende hoge belasting ervaart, kan deze de databasebronnen domineren en andere tenants in dezelfde database beïnvloeden. Als de activiteit waarschijnlijk enige tijd zal worden voortgezet, kan de tenant tijdelijk uit de database worden verplaatst en in de eigen database met één tenant worden opgenomen. Hierdoor kan de huurder de extra middelen die het nodig heeft, en volledig isoleert het van de andere huurders.

Deze oefening simuleert het effect van Salix Salsa ervaren van een hoge belasting wanneer tickets gaan op de verkoop voor een populair evenement.

1. Open de ... \\ *Demo-PerformanceMonitoringAndManagement.ps1* script.
1. Stel **$DemoScenario = 5**, _Genereer een normale belasting plus een hoge belasting op een enkele tenant (ongeveer 90 DTU)._
1. Stel **$SingleTenantName = Salix Salsa**
1. Voer het script uit met **F5**.

Ga naar portal en navigeer naar **salixsalsa** > **Overzicht** om de monitoring grafieken te bekijken. 

## <a name="other-performance-management-patterns"></a>Andere prestatiemanagementpatronen

**Selfserviceschalen van tenant**

Omdat schalen een taak is die gemakkelijk via de beheer-API kan worden aangeroepen, u eenvoudig de mogelijkheid bouwen om tenantdatabases te schalen naar uw tenant-gerichte toepassing en deze aanbieden als een functie van uw SaaS-service. Laat tenants bijvoorbeeld zelf omhoog en omlaag schalen beheren, mogelijk gekoppeld aan hun facturering.

**Een database op en neer schalen op een schema om aan te passen aan gebruikspatronen**

Wanneer het totale tenantgebruik voorspelbare gebruikspatronen volgt, u Azure Automation gebruiken om een database op en neer te schalen volgens een planning. Schaal bijvoorbeeld een database omlaag na 18.00 uur en weer voor 6 uur op weekdagen wanneer u weet dat er een daling van de resourcevereisten is.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het gebruik van een geshard multi-tenantdatabase simuleren door een meegeleverde laadgenerator uit te voeren
> * Controleer de database terwijl deze reageert op de toename van de belasting
> * De database opschalen naar aanleiding van de toegenomen databasebelasting
> * Een tenant ineenzetten in een database met één tenant

## <a name="additional-resources"></a>Aanvullende bronnen

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure-automatisering](../automation/automation-intro.md)