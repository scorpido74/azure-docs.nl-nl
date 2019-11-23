---
title: Herstel na nood geval voor SaaS-apps met geo-replicatie
description: Meer informatie over het gebruik van Azure SQL Database geo-replica's om een SaaS-app met meerdere tenants te herstellen in het geval van een storing
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0668ccf5ceb972dd120e4e3f37be6d879a12d0a7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811709"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Herstel na nood gevallen voor een SaaS-toepassing met meerdere tenants met behulp van geo-replicatie voor de data base

In deze zelf studie gaat u een volledig herstel scenario voor nood gevallen verkennen voor een SaaS-toepassing met meerdere tenants die is geïmplementeerd met behulp van het model data base-per-Tenant. Als u de app wilt beveiligen tegen een storing, gebruikt u [_geo-replicatie_](sql-database-geo-replication-overview.md) om replica's te maken voor de catalogus en Tenant databases in een alternatieve herstel regio. Als er een storing optreedt, kunt u snel een failover uitvoeren naar deze replica's om normale bedrijfs bewerkingen te hervatten. Bij een failover worden de data bases in de oorspronkelijke regio secundaire replica's van de data bases in de herstel regio. Zodra deze replica's weer online zijn, worden ze automatisch aan de status van de data bases in de herstel regio door gegeven. Nadat de onderbreking is opgelost, wordt er een failback uitgevoerd naar de data bases in de oorspronkelijke productie regio.

In deze zelf studie worden de failover-en failback-werk stromen verkend. U leert het volgende:
> [!div class="checklist"]
> 
> * Data Base-en configuratie gegevens van elastische Pools in de Tenant catalogus synchroniseren
> * Een herstel omgeving instellen in een alternatieve regio, met inbegrip van toepassingen, servers en Pools
> * _Geo-replicatie_ gebruiken om de catalogus-en Tenant databases te repliceren naar de herstel regio
> * Failover van de toepassings-en catalogus-en Tenant-Data Base naar de herstel regio 
> * Later, failover van de toepassing, catalogus en Tenant databases terug naar de oorspronkelijke regio nadat de onderbreking is opgelost
> * De catalogus bijwerken wanneer er een failover wordt uitgevoerd voor elke Tenant database om de primaire locatie van de data base van elke Tenant bij te houden
> * Zorg ervoor dat de toepassing en de primaire Tenant database altijd zich in dezelfde Azure-regio bevinden om de latentie te verminderen  
 

Voordat u met deze zelf studie begint, moet u controleren of aan de volgende vereisten is voldaan:
* De Wingtip tickets SaaS-data base per Tenant-app wordt geïmplementeerd. Zie [de SaaS-data base van Wingtip tickets implementeren en verkennen per Tenant toepassing](saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren  
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Inleiding tot het herstel patroon van geo-replicatie

![Herstel architectuur](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Herstel na nood gevallen (DR) is een belang rijke overweging voor veel toepassingen, ongeacht of er nalevings redenen of bedrijfs continuïteit zijn. Als er sprake is van een langdurige service onderbreking, kan een goed voor bereide DR-plan de bedrijfs onderbreking minimaliseren. Het gebruik van geo-replicatie biedt de laagste RPO en RTO door database replica's in een herstel regio te onderhouden waarvoor failover kan worden uitgevoerd naar een korte kennisgeving.

Een DR-plan op basis van geo-replicatie bestaat uit drie afzonderlijke onderdelen:
* Instellen en onderhoud van de herstel omgeving
* Herstel: failover van de app en data bases naar de herstel omgeving als er een storing optreedt,
* Repatriëring-failover van de app en data bases terug naar de oorspronkelijke regio nadat de toepassing is opgelost 

Alle onderdelen moeten zorgvuldig worden beschouwd, met name bij het werken op schaal. Over het algemeen moet het plan verschillende doelen uitvoeren:

* Instellen
    * Stel een omgeving voor het maken en onderhouden van een mirror-installatie kopie in de herstel regio. Door de elastische Pools te maken en data bases te repliceren in deze herstel omgeving, wordt capaciteit in de herstel regio gereserveerd. Het onderhoud van deze omgeving omvat het repliceren van nieuwe Tenant databases wanneer deze worden ingericht.  
* Herstel
    * Wanneer een geschaalde herstel omgeving wordt gebruikt om dagelijkse kosten te minimaliseren, moeten Pools en data bases worden geschaald om de volledige operationele capaciteit te verkrijgen in de herstel regio
    * Nieuwe Tenant inrichting zo snel mogelijk in de herstel regio inschakelen  
    * Geoptimaliseerd voor het herstellen van tenants in volg orde van prioriteit
    * Zorg ervoor dat tenants zo snel mogelijk online kunnen worden opgehaald door stappen parallel uit te voeren.
    * Wees flexibel voor fouten, herstartbaar en idempotent
    * Het proces kan in mid-flight worden geannuleerd als de oorspronkelijke regio weer online is.
* Repatriëring 
    * Failover van data bases van de herstel regio naar replica's in de oorspronkelijke regio met minimale gevolgen voor tenants: er zijn geen gegevens verlies en een off-line periode per Tenant.   

In deze zelf studie worden deze uitdagingen verholpen met de functies van Azure SQL Database en het Azure-platform:

* [Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), om zo snel mogelijk alle benodigde capaciteit te reserveren. Azure Resource Manager sjablonen worden gebruikt om een gespiegelde installatie kopie van de productie servers en elastische Pools in de herstel regio in te richten.
* [Geo-replicatie](sql-database-geo-replication-overview.md), voor het maken van asynchroon gerepliceerde, alleen-lezen-secundairen voor alle data bases. Tijdens een onderbreking voert u een failover uit naar de replica's in de herstel regio.  Nadat de onderbreking is opgelost, wordt er een failback uitgevoerd naar de data bases in de oorspronkelijke regio zonder gegevens verlies.
* [Asynchrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) failover-bewerkingen die zijn verzonden in de volg orde van de Tenant prioriteit om de failovertijd voor een groot aantal data bases te minimaliseren.
* [Herstel functies van Shard-beheer](sql-database-elastic-database-recovery-manager.md), om database vermeldingen in de catalogus tijdens herstel en repatriër te wijzigen. Met deze functies kan de app verbinding maken met Tenant databases, ongeacht de locatie zonder de app opnieuw te hoeven configureren.
* [DNS-aliassen van SQL Server](dns-alias-overview.md), om het naadloos inrichten van nieuwe tenants mogelijk te maken, ongeacht in welke regio de app wordt uitgevoerd. DNS-aliassen worden ook gebruikt om het synchronisatie proces van de catalogus toe te staan om verbinding te maken met de actieve catalogus, ongeacht de locatie.

## <a name="get-the-disaster-recovery-scripts"></a>De nood herstel scripts ophalen 

> [!IMPORTANT]
> Net als alle Wingtip tickets hebben de DR-scripts een voorbeeld kwaliteit en kunnen ze niet worden gebruikt in de productie omgeving. 

De herstel scripts die in deze zelf studie en Wingtip-toepassings bron code worden gebruikt, zijn beschikbaar in de [Wingtip tickets SaaS-data base per Tenant github-opslag plaats](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Bekijk de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en deblokkeren van de Wingtip tickets-beheer scripts.

## <a name="tutorial-overview"></a>Overzicht van de zelfstudie
In deze zelf studie gebruikt u geo-replicatie voor het maken van replica's van de Wingtip tickets-toepassing en de bijbehorende data bases in een andere regio. Vervolgens voert u een failover uit naar deze regio om het herstellen van een storing te simuleren. Wanneer dit is voltooid, is de toepassing volledig functioneel in de herstel regio.

Later, in een afzonderlijke repatriërings stap, voert u een failover uit voor de catalogus-en Tenant databases in de herstel regio naar de oorspronkelijke regio. De toepassing en data bases blijven beschikbaar tijdens de volledige Beschik baarheid. Wanneer dit is voltooid, is de toepassing volledig functioneel in de oorspronkelijke regio.

> [!Note]
> De toepassing wordt hersteld in het _gekoppelde gebied_ van de regio waarin de toepassing wordt geïmplementeerd. Zie [gekoppelde Azure-regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie.

## <a name="review-the-healthy-state-of-the-application"></a>Bekijk de status in orde van de toepassing

Voordat u het herstel proces start, controleert u de normale status van de toepassing.
1. Open in uw webbrowser de Wingtip tickets-gebeurtenissen hub (http://events.wingtip-dpt.&lt; gebruiker&gt;. trafficmanager.net-Vervang &lt;gebruiker&gt; door de gebruikers waarde van uw implementatie.
    * Ga naar de onderkant van de pagina en Let op de naam en locatie van de catalogus server in de voet tekst. De locatie is de regio waarin u de app hebt geïmplementeerd.
    *TIP: Beweeg de muisaanwijzer over de locatie voor het vergroten van de weergave.* 
    ![Gebeurtenissen hub in orde in oorspronkelijke regio](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klik op de contoso concert hal-Tenant en open de bijbehorende gebeurtenis pagina.
    * In de voet tekst ziet u de naam van de Tenant server. De locatie is hetzelfde als de locatie van de catalogus server.

3. Open in de [Azure Portal](https://portal.azure.com)de resource groep waarin de app is geïmplementeerd
    * Let op de regio waarin de servers zijn geïmplementeerd. 

## <a name="sync-tenant-configuration-into-catalog"></a>Tenant configuratie in catalogus synchroniseren

In deze taak start u een proces dat de configuratie van de servers, elastische Pools en data bases in de Tenant catalogus synchroniseert. Het proces houdt deze informatie in de catalogus up-to-date.  Het proces werkt met de actieve catalogus, hetzij in de oorspronkelijke regio ofwel in de herstel regio. De configuratie-informatie wordt gebruikt als onderdeel van het herstel proces om ervoor te zorgen dat de herstel omgeving consistent is met de oorspronkelijke omgeving en later tijdens de inschakeling om ervoor te zorgen dat de oorspronkelijke regio consistent wordt gemaakt met de wijzigingen die zijn aangebracht in de herstel omgeving. De catalogus wordt ook gebruikt om de herstel status van Tenant bronnen bij te houden

> [!IMPORTANT]
> Ter vereenvoudiging worden het synchronisatie proces en andere langlopende herstel-en repatriërings processen geïmplementeerd in deze zelf studies als lokale Power shell-taken of-sessies die worden uitgevoerd onder de aanmelding van de client gebruiker. De verificatie tokens die worden uitgegeven wanneer u zich aanmeldt, verlopen na enkele uren en de taken mislukken vervolgens. In een productie scenario moeten langlopende processen worden geïmplementeerd als betrouw bare Azure-Services van een van de typen die worden uitgevoerd onder een service-principal. Zie [Azure PowerShell gebruiken om een service-principal met een certificaat te maken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. Open in de _Power shell-ISE_het bestand. ..\Learning Modules\UserConfig.psm1. Vervang `<resourcegroup>` en `<user>` op regels 10 en 11 door de waarde die wordt gebruikt bij het implementeren van de app.  Sla het bestand op.

2. Open in de *Power shell-ISE*het script. ..\Learning Modules\Business continuïteit en nood Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel het volgende in:
    * **$DemoScenario = 1**, start een achtergrond taak waarmee de Tenant server wordt gesynchroniseerd en configuratie gegevens van de groep worden omgezet in de catalogus

3. Druk op **F5** om het synchronisatie script uit te voeren. Er wordt een nieuwe Power shell-sessie geopend voor het synchroniseren van de configuratie van Tenant bronnen.
![synchronisatie proces](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Laat het Power shell-venster op de achtergrond draaien en ga verder met de rest van de zelf studie. 

> [!Note]
> Het synchronisatie proces maakt verbinding met de catalogus via een DNS-alias. Deze alias wordt gewijzigd tijdens het terugzetten en de repatriëring om naar de actieve catalogus te verwijzen. Het synchronisatie proces houdt de catalogus up-to-date met eventuele wijzigingen in de data base-of groeps configuratie in de herstel regio.  Tijdens de herrepatriëring worden deze wijzigingen toegepast op de gelijkwaardige resources in de oorspronkelijke regio.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Secundaire database replica's maken in de herstel regio

In deze taak start u een proces dat een duplicaat van een app-exemplaar implementeert en de catalogus en alle Tenant databases repliceert naar een herstel regio.

> [!Note]
> In deze zelf studie wordt de beveiliging van geo-replicatie toegevoegd aan de voorbeeld toepassing Wingtip tickets. In een productie scenario voor een toepassing die geo-replicatie gebruikt, zou elke Tenant vanaf het begin worden ingericht met een geo-gerepliceerde data base. Zie [Maxi maal beschik bare Services ontwerpen met Azure SQL database](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. Open in de *Power shell-ISE*het script. ..\Learning Modules\Business continuïteit en nood Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende waarden in:
    * **$DemoScenario = 2**, de herstel omgeving voor spiegel beelden maken en catalogus-en Tenant-data bases repliceren

2. Druk op **F5** om het script uit te voeren. Er wordt een nieuwe Power shell-sessie geopend om de replica's te maken.
![synchronisatie proces](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>De normale toepassings status controleren

De toepassing wordt op dit moment normaal uitgevoerd in de oorspronkelijke regio en wordt nu beveiligd door Geo-replicatie.  Alleen-lezen secundaire replica's, bestaan in de herstel regio voor alle data bases. 

1. Bekijk in de Azure Portal uw resource groepen en houd er rekening mee dat er een resource groep is gemaakt met het achtervoegsel-Recovery in de herstel regio. 

2. Verken de resources in de resource groep voor herstel.  

3. Klik op de contoso concert hal-Data Base op de _tenants1-dpt-&lt;gebruiker&gt;-herstel_ server.  Klik op geo-replicatie aan de linkerkant. 

    ![Geo-replicatie koppeling voor contoso concert](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

In de Azure Regions-kaart noteert u de geo-replicatie koppeling tussen de primaire en de secundaire regio in de herstel regio.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Failover van de toepassing naar de herstel regio

### <a name="geo-replication-recovery-process-overview"></a>Overzicht van het herstel proces van geo-replicatie

Met het herstel script worden de volgende taken uitgevoerd:

1. Hiermee schakelt u het Traffic Manager eind punt voor de web-app in de oorspronkelijke regio uit. Als u het eind punt uitschakelt, kunnen gebruikers geen verbinding maken met de app in een ongeldige status als de oorspronkelijke regio tijdens het herstel online is.

1. Maakt gebruik van een geforceerde failover van de catalogus database in de herstel regio om deze de primaire data base te maken en werkt de _activecatalog_ -alias bij zodat deze naar de herstel catalogus server verwijst.

1. Hiermee wordt de _newtenant_ -alias bijgewerkt zodat deze verwijst naar de Tenant server in de herstel regio. Als u deze alias wijzigt, zorgt u ervoor dat de data bases voor nieuwe tenants in de herstel regio worden ingericht. 

1. Hiermee worden alle bestaande tenants in de herstel catalogus als offline gemarkeerd om toegang tot Tenant databases te voor komen voordat er een failover wordt uitgevoerd.

1. Hiermee wordt de configuratie van alle elastische Pools en gerepliceerde afzonderlijke data bases in de herstel regio bijgewerkt om de configuratie in de oorspronkelijke regio te spie gelen. (Deze taak is alleen nodig als Pools of gerepliceerde data bases in de herstel omgeving omlaag worden geschaald tijdens normale bewerkingen om de kosten te verlagen).

1. Hiermee wordt het Traffic Manager-eind punt voor de web-app in de herstel regio ingeschakeld. Als u dit eind punt inschakelt, kan de toepassing nieuwe tenants inrichten. In deze fase blijven bestaande tenants nog steeds offline.

1. Verzendt batches van aanvragen om failover van data bases in volg orde van prioriteit te forceren.
    * Batches zijn ingedeeld zodat data bases in alle groepen parallel worden uitgevoerd.
    * Failover-aanvragen worden verzonden met asynchrone bewerkingen, zodat ze snel worden verzonden en meerdere aanvragen tegelijk kunnen worden verwerkt.

   > [!Note]
   > In een storings scenario zijn de primaire data bases in de oorspronkelijke regio offline.  Bij geforceerde failover op de secundaire wordt de verbinding met de primaire verbroken zonder dat er overbodige trans acties in de wachtrij moeten worden toegepast. Als er in een DR-analyse scenario, zoals deze zelf studie, een update activiteit is op het moment van de failover, kunnen er gegevens verloren gaan. Wanneer u later tijdens de inschakeling failover van data bases in de herstel regio terugzet naar de oorspronkelijke regio, wordt een normale failover gebruikt om ervoor te zorgen dat er geen gegevens verloren gaan.

1. Bewaakt de SQL database-service om te bepalen wanneer failover van data bases is uitgevoerd. Als er een failover van een Tenant database is uitgevoerd, wordt de catalogus bijgewerkt om de herstel status van de Tenant database vast te leggen en de Tenant als online te markeren.
    * Tenant-data bases kunnen worden geopend door de toepassing zodra ze online zijn gemarkeerd in de catalogus.
    * De som van de rowversion-waarden in de Tenant database wordt opgeslagen in de catalogus. Deze waarde fungeert als een vinger afdruk waarmee het planningsproces proces kan bepalen of de data base is bijgewerkt in de herstel regio.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Voer het script uit om een failover uit te voeren naar de herstel regio

Stel nu dat er een storing optreedt in de regio waarin de toepassing wordt geïmplementeerd en voer het herstel script uit:

1. Open in de *Power shell-ISE*het script. ..\Learning Modules\Business continuïteit en nood Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende waarden in:
    * **$DemoScenario = 3**, de app herstellen in een herstel regio door failover van replica's uit te voeren

2. Druk op **F5** om het script uit te voeren.  
    * Het script wordt geopend in een nieuw Power shell-venster en vervolgens wordt een reeks Power shell-taken gestart die parallel worden uitgevoerd. Bij deze taken worden failover-data bases van de Tenant naar de herstel regio uitgevoerd.
    * De herstel regio is de gekoppelde _regio_ die is gekoppeld aan de Azure-regio waarin u de toepassing hebt geïmplementeerd. Zie [gekoppelde Azure-regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie. 

3. Bewaak de status van het herstel proces in het Power shell-venster.
    ![failover-proces](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Als u de code voor de herstel taken wilt verkennen, controleert u de Power shell-scripts in de map. ..\Learning Modules\Business continuïteit en nood Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>De toepassings status controleren tijdens het herstel

Wanneer het eind punt van de toepassing in Traffic Manager is uitgeschakeld, is de toepassing niet beschikbaar. Nadat de catalogus is overgezet naar de herstel regio en alle tenants die offline zijn gemarkeerd, wordt de toepassing weer online gezet. Hoewel de toepassing beschikbaar is, wordt elke Tenant offline weer gegeven in de Events hub tot er een failover voor de data base is uitgevoerd. Het is belang rijk dat u uw toepassing ontwerpt voor het verwerken van offline Tenant databases.

1. Zodra de catalogus database is hersteld, vernieuwt u de Wingtip tickets-hub van gebeurtenissen in uw webbrowser.
   * In de voet tekst ziet u dat de naam van de catalogus server nu een achtervoegsel voor _herstel_ heeft en zich in de herstel regio bevindt.
   * U ziet dat tenants die nog niet zijn hersteld, zijn gemarkeerd als offline en niet kunnen worden geselecteerd.  

     > [!Note]
     > Als u slechts een paar data bases wilt herstellen, kunt u de browser mogelijk niet vernieuwen voordat het herstel is voltooid, dus de tenants worden mogelijk niet weer geven wanneer ze offline zijn. 
 
     ![De gebeurtenissen-hub is offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Als u rechtstreeks op de pagina gebeurtenissen van een offline Tenant opent, wordt de melding ' Tenant offline ' weer gegeven. Als contoso concert hal bijvoorbeeld offline is, probeert u http://events.wingtip-dpt.&ltte openen; gebruiker&gt;. trafficmanager.net/contosoconcerthall ![contoso offline pagina](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Een nieuwe Tenant inrichten in de herstel regio
Zelfs voordat alle bestaande Tenant databases een failover hebben uitgevoerd, kunt u nieuwe tenants inrichten in de herstel regio.  

1. Open in de *Power shell-ISE*het script. ..\Learning Modules\Business continuïteit en nood Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende eigenschap in:
    * **$DemoScenario = 4**, een nieuwe Tenant inrichten in de herstel regio

2. Druk op **F5** om het script uit te voeren en de nieuwe Tenant in te richten. 

3. De pagina Hawthorn Hall Events wordt geopend in de browser wanneer deze is voltooid. Let op de voet tekst die de Hawthorn Hall-data base in de herstel regio heeft ingericht.
    ![pagina met Hawthorn-hal-gebeurtenissen](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. Vernieuw de pagina Wingtip tickets-evenementen hub in de browser om Hawthorn Hall inbegrepen te bekijken. 
    * Als u Hawthorn-zaal hebt ingericht zonder te wachten tot de andere tenants zijn hersteld, kunnen andere tenants nog steeds offline zijn.


## <a name="review-the-recovered-state-of-the-application"></a>De herstelde status van de toepassing controleren

Wanneer het herstel proces is voltooid, zijn de toepassing en alle tenants volledig functioneel in de herstel regio. 

1. Zodra de weer gave in het Power shell-console venster aangeeft dat alle tenants worden hersteld, moet u de Events hub vernieuwen.  De tenants worden allemaal online weer gegeven, met inbegrip van de nieuwe Tenant, Hawthorn Hall.

    ![Herstelde en nieuwe tenants in de Events hub](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Open de lijst met resource groepen in het [Azure Portal](https://portal.azure.com).  
    * U ziet de resource groep die u hebt geïmplementeerd, plus de resource groep herstellen met het achtervoegsel _-Recovery_ .  De resource groep voor herstel bevat alle resources die zijn gemaakt tijdens het herstel proces, plus nieuwe resources die zijn gemaakt tijdens de onderbreking.  

3. Open de resource groep herstellen en Let op de volgende items:
   * De herstel versies van de catalogus-en tenants1-servers, met het achtervoegsel _-Recovery_ .  De herstelde catalogus en Tenant databases op deze servers hebben allemaal de namen die in de oorspronkelijke regio worden gebruikt.

   * _Tenants2-dpt-&lt;gebruiker&gt;-Recovery_ SQL Server.  Deze server wordt gebruikt voor het inrichten van nieuwe tenants tijdens de onderbreking.
   * De App Service met de naam, _Events-Wingtip-dpt-&lt;recoveryregion&gt;-&lt;gebruiker & gt_;. Dit is het herstel exemplaar van de app gebeurtenissen. 

     ![Azure Recovery-resources](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Open _tenants2-dpt-&lt;gebruiker&gt;-Recovery_ SQL Server.  U ziet dat het de Data Base _hawthornhall_ en de elastische pool _Pool1_bevat.  De _hawthornhall_ -data base is geconfigureerd als een elastische data base in _Pool1_ elastische pool.

5. Ga terug naar de resource groep en klik op de contoso concert hal-Data Base op de _tenants1-dpt-&lt;gebruiker&gt;-herstel_ server. Klik op geo-replicatie aan de linkerkant.
    
    ![Contoso-data base na failover](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Tenant gegevens wijzigen 
In deze taak werkt u een van de Tenant databases bij. 

1. Ga in uw browser naar de lijst met gebeurtenissen voor de concert hal van Contoso en noteer de naam van de laatste gebeurtenis.
2. Stel in het *Power shell-ISE*in het script. ..\Learning Modules\Business continuïteit en nood Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 de volgende waarde in:
    * **$DemoScenario = 5** Een gebeurtenis verwijderen uit een Tenant in de herstel regio
3. Druk op **F5** om het script uit te voeren
4. Vernieuw de pagina voor het uitvoeren van een concert hal van Contoso (http://events.wingtip-dpt.&lt; gebruikers&gt;. trafficmanager.net/contosoconcerthall: substitueer &lt;gebruiker&gt; met de gebruikers waarde van uw implementatie) en u ziet dat de laatste gebeurtenis is verwijderd.

## <a name="repatriate-the-application-to-its-original-production-region"></a>De toepassing in de oorspronkelijke productie regio repatriëren

Met deze taak wordt de toepassing in de oorspronkelijke regio gerepatriërd. In een echt scenario zou u een repatriëring initiëren wanneer de storing is opgelost.

### <a name="repatriation-process-overview"></a>Overzicht van het planningsproces

![Berepatriërings architectuur](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Het planningsproces:
1. Hiermee worden alle openstaande of in de vlucht geplaatste aanvragen voor database herstel geannuleerd.
2. Hiermee wordt de _newtenant_ -alias bijgewerkt zodat deze verwijst naar de server van de Tenant in de oorsprongs regio. Door deze alias te wijzigen, zorgt u ervoor dat de data bases voor nieuwe tenants nu worden ingericht in de regio oorsprong.
3. Alle gewijzigde Tenant gegevens in de oorspronkelijke regio seeden
4. Failover van Tenant databases in volg orde van prioriteit.

Met de failover wordt de data base effectief verplaatst naar de oorspronkelijke regio. Wanneer er een failover voor de data base wordt uitgevoerd, worden openstaande verbindingen verwijderd en is de data base enkele seconden niet beschikbaar. Toepassingen moeten worden geschreven met logica voor opnieuw proberen om ervoor te zorgen dat ze weer verbinding maken.  Hoewel deze korte verbreking vaak niet wordt opgemerkt, kunt u ervoor kiezen om data bases buiten kantoor uren te repatriëren. 


### <a name="run-the-repatriation-script"></a>Het repatriërings script uitvoeren
Stel nu dat de storing is opgelost en voer het repatriërings script uit.

1. In de *Power shell-ISE*in het script. ..\Learning Modules\Business continuïteit en nood Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1.

2. Controleer of het catalogus synchronisatie proces nog steeds wordt uitgevoerd in het Power shell-exemplaar.  Als dat nodig is, start u deze opnieuw met de instelling:
    * **$DemoScenario = 1**, begin met het synchroniseren van de configuratie gegevens van de Tenant server, de groep en de data base in de catalogus
    * Druk op **F5** om het script uit te voeren.

3.  Ga vervolgens als volgt te werk om het proces te starten:
    * **$DemoScenario = 6**, de app in de oorspronkelijke regio te repatriëren
    * Druk op **F5** om het herstel script uit te voeren in een nieuw Power shell-venster.  De repatriëring duurt enkele minuten en kan worden bewaakt in het Power shell-venster.
    ![planningsproces](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Wanneer het script wordt uitgevoerd, vernieuwt u de pagina gebeurtenissen hub (http://events.wingtip-dpt.&lt; gebruiker&gt;. trafficmanager.net)
    * U ziet dat alle tenants online zijn en toegankelijk zijn tijdens dit proces.

5. Nadat de bewerking is voltooid, vernieuwt u de Events hub en opent u de pagina evenementen voor Hawthorn Hall. U ziet dat deze data base is gerepatriërd tot de oorspronkelijke regio.
    ![-gebeurtenissen hub gerepatriërd](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>De toepassing ontwerpen om ervoor te zorgen dat de app en de data base zich zelf bevinden 
De toepassing is zo ontworpen dat deze altijd verbinding maakt met een instantie in dezelfde regio als de Tenant database. Dit ontwerp vermindert de latentie tussen de toepassing en de data base. Deze optimalisatie gaat ervan uit dat de interactie van de app-naar-data base chattier is dan de interactie tussen de gebruiker en de app.  

Tenant-data bases kunnen gedurende enige tijd tijdens de repatriëring over het herstel en de oorspronkelijke regio's worden verspreid. Voor elke Data Base zoekt de app naar de regio waarin de data base zich bevindt door een DNS-zoek opdracht op de naam van de Tenant server uit te voeren. In SQL Database is de naam van de server een alias. De naam van de alias server bevat de naam van de regio. Als de toepassing zich niet in dezelfde regio bevindt als de-data base, wordt deze omgeleid naar het exemplaar in dezelfde regio als de database server.  Omleiden naar een exemplaar in dezelfde regio als de data base, minimaliseert de latentie tussen de app en de data base. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> 
> * Data Base-en configuratie gegevens van elastische Pools in de Tenant catalogus synchroniseren
> * Een herstel omgeving instellen in een alternatieve regio, met inbegrip van toepassingen, servers en Pools
> * _Geo-replicatie_ gebruiken om de catalogus-en Tenant databases te repliceren naar de herstel regio
> * Failover van de toepassings-en catalogus-en Tenant-Data Base naar de herstel regio 
> * Een failback van de toepassing, Catalog en Tenant-data bases naar de oorspronkelijke regio nadat de onderbreking is opgelost

Meer informatie over de technologieën van Azure SQL database biedt u de mogelijkheid om bedrijfs continuïteit in te scha kelen in de [overzichts documentatie van bedrijfs continuïteit](sql-database-business-continuity.md) .

## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende zelf studies die voortbouwen op de Wingtip SaaS-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
