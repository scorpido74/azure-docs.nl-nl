---
title: De prestaties van een Shard multi tenant-data base bewaken
description: Prestaties van Shard multi-tenant Azure SQL database bewaken en beheren in een SaaS-app met meerdere tenants
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
ms.openlocfilehash: cc8ccbbde56b57af684ad47840002a846bdcd8c0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827953"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Prestaties van Shard multi-tenant Azure SQL database bewaken en beheren in een SaaS-app met meerdere tenants

In deze zelf studie worden verschillende scenario's voor prestatie beheer in SaaS-toepassingen besproken. Het gebruik van een load-generator voor het simuleren van activiteit in meerdere Shard multi tenant-data bases, worden de ingebouwde bewakings-en waarschuwings functies van SQL Database toegelicht.

De Wingtip tickets SaaS multi-tenant data base-app maakt gebruik van een Shard multi tenant-gegevens model, waarbij locatie gegevens (Tenant) worden gedistribueerd door de Tenant-ID over mogelijk meerdere data bases. Net als bij veel andere SaaS-toepassingen, is het verwachte tenantworkloadpatroon onvoorspelbaar en sporadisch. Met andere woorden, kaartverkoop kan op ieder moment plaatsvinden. Als u gebruik wilt maken van dit typische database gebruiks patroon, kunnen data bases omhoog en omlaag worden geschaald om de kosten van een oplossing te optimaliseren. Met dit type patroon is het belang rijk om het gebruik van database bronnen te bewaken om ervoor te zorgen dat de belasting redelijkerwijs kan worden verdeeld over mogelijk meerdere data bases. U moet er ook voor zorgen dat de afzonderlijke data bases voldoende resources hebben en niet voldoen aan de [DTU](sql-database-purchase-models.md#dtu-based-purchasing-model) -limieten. In deze zelf studie leert u hoe u data bases kunt bewaken en beheren, en hoe u corrigerende maat regelen kunt nemen in reactie op variaties in de werk belasting.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Gebruik simuleren op een Shard multi tenant-data base door een meegeleverde laad Generator uit te voeren
> * De data base bewaken terwijl deze reageert op de toename van de belasting
> * De data base omhoog schalen als reactie op de verbeterde laad capaciteit van de data base
> * Een Tenant inrichten in een Data Base met één Tenant

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip tickets SaaS multi-tenant-data base-app wordt geïmplementeerd. Zie [de toepassing Wingtip tickets SaaS multi-tenant data base implementeren en verkennen](saas-multitenantdb-get-started-deploy.md) voor meer informatie over de implementatie in minder dan vijf minuten
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-saas-performance-management-patterns"></a>Inleiding tot SaaS Performance Management-patronen

Het beheren van de databaseprestaties bestaat uit het verzamelen en analyseren van prestatiegegevens en het reageren op deze gegevens door parameters aan te passen om een acceptabele reactietijd voor de toepassing te behouden. 

### <a name="performance-management-strategies"></a>Strategieën voor prestatiebeheer

* Om te voor komen dat de prestaties hand matig worden bewaakt, is het het meest effectief om waarschuwingen in te **stellen die activeren wanneer data bases uit normale bereiken ontvallen**.
* Om te reageren op schommelingen op de korte termijn in de reken grootte van een Data Base, **kan het DTU-niveau omhoog of omlaag worden geschaald**. Als deze schommeling op een regel matige of voorspel bare basis plaatsvindt, **kan het schalen van de data base automatisch worden uitgevoerd**. U kunt bijvoorbeeld omlaag schalen wanneer de workload licht is, zoals 's nachts of tijdens het weekend.
* **Individuele tenants kunnen worden verplaatst naar andere data bases**om te reageren op schommelingen op de langere termijn of wijzigingen in de tenants.
* Om te reageren op de korte termijn verhogingen in de *individuele* Tenant belasting, **kunnen afzonderlijke tenants uit een Data Base worden gehaald en een afzonderlijke reken grootte worden toegewezen**. Zodra de belasting is gereduceerd, kan de Tenant vervolgens worden teruggestuurd naar de multi tenant-data base. Wanneer dit vooraf bekend is, kunnen tenants vooraf worden verplaatst, om ervoor te zorgen dat de data base altijd beschikt over de benodigde resources en om te voor komen dat andere tenants in de data base met meerdere tenants worden beïnvloed. Als het om een voorspelbare vereiste gaat, zoals bij een locatie die met een toename in kaartverkoop te maken krijgt voor een populair evenement, kan dit beheergedrag in de toepassing worden geïntegreerd.

[Azure Portal](https://portal.azure.com) biedt ingebouwde functionaliteit voor bewaking en waarschuwingen voor de meeste resources. Voor SQL Database zijn bewaking en waarschuwingen beschikbaar in data bases. Deze ingebouwde bewaking en waarschuwingen zijn specifiek voor resources, dus het is handig voor kleine aantallen resources, maar is niet handig wanneer u met veel resources werkt.

Voor scenario's met grote volumes, waar u met veel resources werkt, kunnen [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) worden gebruikt. Dit is een afzonderlijke Azure-service die analyse van verzonden Diagnostische logboeken en telemetrie in een Log Analytics-werk ruimte biedt. Azure Monitor logboeken kunnen telemetrie verzamelen van veel services en worden gebruikt om waarschuwingen op te vragen en in te stellen.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>De Wingtip tickets SaaS multi-tenant database toepassings bron code en scripts ophalen

De Wingtip tickets SaaS multi-tenant database scripts en toepassings bron code zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) github opslag plaats. Bekijk de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en blok keren van de Wingtip tickets SaaS-scripts.

## <a name="provision-additional-tenants"></a>Extra tenants inrichten

Voor een goed beeld van de werking van prestatie bewaking en-beheer op schaal, moet u voor deze zelf studie meerdere tenants hebben in een Shard multi tenant-data base.

Als u al een batch tenants in een vorige zelf studie hebt ingericht, gaat u naar de sectie [gebruik simuleren op alle Tenant databases](#simulate-usage-on-all-tenant-databases) .

1. \\open in de **Power shell ISE**-Learning modules\\prestatie bewaking en-beheer\\*demo-PerformanceMonitoringAndManagement. ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel het volgende in: **$DemoScenario** = **1**, _Batch met tenants inrichten_
1. Druk op **F5** om het script uit te voeren.

Het script implementeert 17 tenants in een paar minuten in de multi tenant-data base. 

Met het script *New-TenantBatch* worden nieuwe tenants met unieke Tenant sleutels in de Shard-data base met meerdere tenants gemaakt en geïnitialiseerd met de Tenant naam en het type locatie. Dit is consistent met de manier waarop de app een nieuwe Tenant heeft ingericht. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Gebruik simuleren op alle tenantdatabases

Het script *demo-PerformanceMonitoringAndManagement. ps1* wordt gebruikt voor het simuleren van een werk belasting die wordt uitgevoerd op de multi tenant-data base. De belasting wordt gegenereerd met behulp van een van de beschik bare laad scenario's:

| Demo | Scenario |
|:--|:--|
| 2 | Normale intensiteit genereren (ongeveer 30 DTU) |
| 3 | Belasting genereren met langere bursts per Tenant|
| 4 | Genereren van belasting met hogere DTU-bursts per Tenant (ongeveer 70 DTU)|
| 5 | Genereer een hoge intensiteit (ongeveer 90 DTU) op één Tenant plus een normale intensiteits belasting voor alle andere tenants |

De load-generator past een *synthetische* load alleen voor CPU toe op elke tenantdatabase. De generator start voor elke tenantdatabase een taak, die periodiek een opgeslagen procedure aanroept die de load genereert. De laad niveaus (in Dtu's), duur en intervallen zijn in alle data bases gevarieerd, zodat er onvoorspelbare Tenant activiteiten worden gesimuleerd.

1. \\open in de **Power shell ISE**-Learning modules\\prestatie bewaking en-beheer\\*demo-PerformanceMonitoringAndManagement. ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. **$DemoScenario** = **2**instellen, _normale taak intensiteit genereren_
1. Druk op **F5** om een belasting toe te passen op al uw tenants.

Wingtip tickets SaaS multi tenant-data base is een SaaS-app en de werkelijke belasting van een SaaS-app is doorgaans sporadisch en onvoorspelbaar. Om dit te simuleren, produceert de load-generator een willekeurige workload die over alle tenants wordt verdeeld. Er zijn enkele minuten nodig om het laad patroon te laten zien, dus voer de laad Generator gedurende 3-5 minuten uit voordat u probeert de belasting in de volgende secties te bewaken.

> [!IMPORTANT]
> De load generator wordt uitgevoerd als een reeks taken in een nieuw Power shell-venster. Als u de sessie sluit, stopt de load generator. De load Generator blijft in de status van een *taak aanroepen* waarbij de belasting wordt gegenereerd voor nieuwe tenants die zijn ingericht nadat de generator is gestart. Gebruik *CTRL + C* om te stoppen met het aanroepen van nieuwe taken en het script af te sluiten. De load Generator blijft actief, maar alleen op bestaande tenants.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Resource gebruik bewaken met behulp van de Azure Portal

Als u het resource gebruik wilt bewaken dat het resultaat is van de belasting die wordt toegepast, opent u de portal naar de multi tenant-data base, **tenants1**met de tenants:

1. Open de [Azure Portal](https://portal.azure.com) en blader naar de gebruiker van de server *tenants1-mt-&lt;&gt;* .
1. Schuif omlaag en zoek naar data bases en klik op **tenants1**. Deze Shard multi tenant-Data Base bevat alle tenants die tot nu toe zijn gemaakt.

![database diagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Bekijk het **DTU** -diagram.

## <a name="set-performance-alerts-on-the-database"></a>Prestatie waarschuwingen instellen voor de data base

Stel als volgt een waarschuwing in voor de data base die wordt geactiveerd op \>75%-gebruik:

1. Open de *tenants1* -data base (op de *tenants1-MT-&lt;-gebruikers&gt;* -server) in de [Azure Portal](https://portal.azure.com).
1. Klik op **Waarschuwingsregels** en klik vervolgens op **+ Waarschuwing toevoegen**:

   ![waarschuwing toevoegen](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Geef een naam op, bijvoorbeeld **hoog DTU**.
1. Stel de volgende waarden in:
   * **Metriek = DTU-percentage**
   * **Voor waarde = groter dan**
   * **Drempel = 75**.
   * **Period = de afgelopen 30 minuten**
1. Voeg een e-mail adres toe aan het vak *extra beheerders-e-mail (s)* en klik op **OK**.

   ![waarschuwing instellen](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Een bezette data base omhoog schalen

Als het belasting niveau toeneemt aan een Data Base tot aan het punt dat de data base dergelijke en 100% DTU-gebruik bereikt, worden de database prestaties beïnvloed, waardoor de reactie tijden van query's mogelijk worden vertraagd.

**Korte termijn**kunt u overwegen om de data base te schalen om aanvullende resources te bieden of om tenants te verwijderen uit de data base met meerdere tenants (die van de multi tenant-Data Base naar een zelfstandige Data Base verplaatsen).

**Meer termijn**, overweeg het optimaliseren van query's of index gebruik om de database prestaties te verbeteren. Afhankelijk van de gevoeligheid van de toepassing om de prestaties van het best practice een Data Base omhoog te schalen voordat deze 100% DTU-gebruik heeft bereikt. Gebruik een waarschuwing zodat u tijdig wordt gewaarschuwd.

U kunt een bezette data base simuleren door de door de generator gemaakte belasting te verg Roten. Zorgt ervoor dat de tenants vaker worden gebursteerd en langer de belasting van de multi tenant-data base verhogen zonder de vereisten van de afzonderlijke tenants te wijzigen. Het omhoog schalen van de Data Base vindt eenvoudig plaats in de portal of vanuit Power shell. In deze oefening wordt de portal gebruikt.

1. Stel *$DemoScenario* = **3**in, _Genereer belasting met langere en frequentere bursts per data base_ om de intensiteit van de cumulatieve belasting voor de data base te verg Roten zonder de piek belasting voor elke Tenant te wijzigen.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.
1. Ga naar de **tenants1** -data base in de Azure Portal.

Bewaak het verhoogde data base-DTU-gebruik in het bovenste diagram. Het duurt een paar minuten voordat de nieuwe hogere belasting in de tijd begint, maar u moet de data base snel kunnen bereiken om het maximale gebruik te bereiken en wanneer de belasting in het nieuwe patroon wordt geactiveerd, wordt de data base snel overbelast.

1. Als u de Data Base wilt uitbreiden, klikt u op de Blade instellingen op **prijs categorie (dtu's schalen)** .
1. Pas de **DTU** -instelling aan op **100**. 
1. Klik op **Toep assen** om de aanvraag in te dienen om de data base te schalen.

Ga terug naar **tenants1** > **overzicht** om de bewakings grafieken weer te geven. Bewaak het effect van het leveren van de data base met meer resources (hoewel er slechts enkele tenants zijn en een wille keurige werk belasting is het niet altijd duidelijk om goed te zien tot u deze tijd hebt uitgevoerd). Terwijl u de grafieken bekijkt, ziet u dat 100% in het bovenste diagram nu 100 Dtu's vertegenwoordigt, terwijl de onderste grafiek 100% nog steeds 50 Dtu's is.

Databases blijven gedurende het proces online en volledig beschikbaar. De toepassings code moet altijd worden geschreven om verbroken verbindingen opnieuw te proberen, waardoor de data base opnieuw wordt verbonden.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Een nieuwe Tenant inrichten in een eigen data base 

Met het Shard multi tenant-model kunt u kiezen of u een nieuwe Tenant in een multi tenant-data base naast andere tenants wilt inrichten, of dat u de Tenant in een eigen data base wilt inrichten. Door een Tenant in een eigen data base in te richten, profiteert u van de isolatie inherent in de afzonderlijke Data Base, zodat u de prestaties van die Tenant onafhankelijk van anderen kunt beheren, die Tenant onafhankelijk van anderen kunt herstellen, enzovoort. U kunt bijvoorbeeld kiezen voor een gratis proef versie of vaste klanten in een multi tenant-data base, en Premium-klanten in afzonderlijke data bases.  Als er geïsoleerde single tenant-data bases worden gemaakt, kunnen ze gezamenlijk worden beheerd in een elastische pool om resource kosten te optimaliseren.

Als u al een nieuwe Tenant in een eigen data base hebt ingericht, kunt u de volgende stappen overs Laan.

1. \\open in de **Power shell ISE**-Learning modules\\ProvisionTenants\\*demo-ProvisionTenants. ps1*. 
1. Modify **$TenantName = "Salix Salsa"** en **$VenueType = "dans"**
1. **$Scenario** = **2**instellen, _een Tenant inrichten in een nieuwe Data Base met één Tenant_
1. Druk op **F5** om het script uit te voeren.

Met het script wordt deze Tenant ingericht in een afzonderlijke Data Base, de data base en de Tenant bij de catalogus geregistreerd en vervolgens de pagina gebeurtenissen van de Tenant geopend in de browser. Vernieuw de pagina evenementen hub en u ziet dat "Salix Salsa" is toegevoegd als locatie.

## <a name="manage-performance-of-an-individual-database"></a>Prestaties van een afzonderlijke Data Base beheren

Als één Tenant in een multi tenant-Data Base een aanhoudende hoge belasting ondervindt, is het mogelijk dat de database bronnen worden gehinderd en andere tenants in dezelfde data base worden beïnvloed. Als de activiteit waarschijnlijk enige tijd wordt voortgezet, kan de Tenant tijdelijk worden verplaatst uit de data base en naar een eigen data base met één Tenant. Op deze manier kan de Tenant de extra resources hebben die nodig zijn, en deze volledig isoleren van de andere tenants.

Deze oefening simuleert het effect van Salix Salsa die een hoge belasting ondervindt wanneer tickets voor een populaire gebeurtenis op de verkoop gaan.

1. Open het script...\\*demo-PerformanceMonitoringAndManagement. ps1* .
1. Stel **$DemoScenario = 5** _in, Genereer een normale belasting plus een hoge belasting voor één tenant (ongeveer 90 DTU)._
1. **$SingleTenantName instellen = Salix Salsa**
1. Voer het script uit met **F5**.

Ga naar de portal en navigeer naar **salixsalsa** > **Overview** om de bewakings grafieken weer te geven. 

## <a name="other-performance-management-patterns"></a>Andere patronen voor prestatie beheer

**Self-service voor tenants schalen**

Omdat schalen een taak is die eenvoudig kan worden aangeroepen via de beheer-API, kunt u eenvoudig de mogelijkheid om Tenant databases te schalen naar uw Tenant toepassing bouwen en deze aanbieden als een functie van uw SaaS-service. Laat tenants bijvoorbeeld zelf omhoog en omlaag schalen beheren, mogelijk gekoppeld aan hun facturering.

**Een Data Base omhoog of omlaag schalen volgens een schema dat overeenkomt met gebruiks patronen**

Wanneer het samengevoegde Tenant gebruik voorspellende gebruiks patronen volgt, kunt u Azure Automation gebruiken om een Data Base omhoog of omlaag te schalen volgens een schema. U kunt bijvoorbeeld een Data Base omlaag schalen na 18:00 uur en opnieuw instellen vóór 06.00 op werk dagen wanneer u weet dat er sprake is van een daling in de resource vereisten.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik simuleren op een Shard multi tenant-data base door een meegeleverde laad Generator uit te voeren
> * De data base bewaken terwijl deze reageert op de toename van de belasting
> * De data base omhoog schalen als reactie op de verbeterde laad capaciteit van de data base
> * Een Tenant inrichten in een Data Base met één Tenant

## <a name="additional-resources"></a>Aanvullende bronnen

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)