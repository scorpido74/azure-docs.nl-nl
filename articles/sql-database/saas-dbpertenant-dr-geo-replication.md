---
title: Disaster Recovery voor SaaS-apps met Geo Replication
description: Meer informatie over het gebruik van georeplica's van Azure SQL Database om een SaaS-app met meerdere tenant's te herstellen in geval van een storing
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73811709"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Herstel na noodgevallen voor een SaaS-toepassing met meerdere tenants met behulp van georeplicatie van de database

In deze zelfstudie verkent u een volledig scenario voor noodherstel voor een SaaS-toepassing met meerdere tenant's die zijn geïmplementeerd met behulp van het database-per-tenant-model. Om de app te beschermen tegen een storing, gebruikt u [_geo-replicatie_](sql-database-geo-replication-overview.md) om replica's te maken voor de catalogus- en tenantdatabases in een alternatief herstelgebied. Als er een storing optreedt, u snel niet naar deze replica's gaan om de normale bedrijfsactiviteiten te hervatten. Bij failover worden de databases in de oorspronkelijke regio secundaire replica's van de databases in het herstelgebied. Zodra deze replica's weer online komen, halen ze automatisch de status van de databases in het herstelgebied in. Nadat de storing is opgelost, gaat u niet meer terug naar de databases in het oorspronkelijke productiegebied.

Deze zelfstudie verkent zowel de failover- als failback-werkstromen. U leert het volgende:
> [!div class="checklist"]
> 
> * Database- en configuratiegegevens voor elastische groep synchroniseren in de tenantcatalogus
> * Een herstelomgeving instellen in een alternatieve regio, bestaande uit toepassing, servers en groepen
> * _Geo-replicatie_ gebruiken om de catalogus- en tenantdatabases te repliceren naar het herstelgebied
> * Fail over de toepassing en catalogus en tenant databases naar het herstelgebied 
> * Later, mislukken over de toepassing, catalogus en tenant databases terug naar de oorspronkelijke regio nadat de storing is opgelost
> * De catalogus bijwerken omdat elke tenantdatabase niet is overgeslaagd om de primaire locatie van de database van elke tenant bij te houden
> * Zorg ervoor dat de toepassings- en primaire tenantdatabase altijd in dezelfde Azure-regio is ondergebracht om de latentie te verminderen  
 

Voordat u deze zelfstudie start, moet u ervoor zorgen dat de volgende vereisten zijn voltooid:
* De Wingtip Tickets SaaS-database per tenant-app wordt geïmplementeerd. Zie [De SaaS-database van Wingtip Tickets implementeren en verkennen per tenanttoepassing](saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren, zie De SaaS-database van Wingtip Tickets implementeren en verkennen  
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Inleiding tot het herstelpatroon voor georeplicatie

![Herstelarchitectuur](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Disaster recovery (DR) is een belangrijke overweging voor veel toepassingen, of het nu om nalevingsredenen of om bedrijfscontinuïteit is. Als er een langdurige servicestoring optreedt, kan een goed voorbereid DR-plan de bedrijfsonderbreking minimaliseren. Het gebruik van geo-replicatie biedt de laagste RPO en RTO door databasereplica's te onderhouden in een herstelgebied dat op korte termijn kan worden mislukt.

Een DR-plan op basis van geo-replicatie bestaat uit drie verschillende delen:
* Opstelling - creëren en onderhouden van de herstelomgeving
* Herstel - failover van de app en databases naar de herstelomgeving als er een storing optreedt,
* Repatriëring - failover van de app en databases terug naar de oorspronkelijke regio zodra de toepassing is opgelost 

Alle onderdelen moeten zorgvuldig worden overwogen, vooral als ze op schaal werken. Over het algemeen moet het plan verschillende doelen bereiken:

* Instellen
    * Een spiegelbeeldomgeving instellen en onderhouden in het herstelgebied. Het maken van de elastische pools en het repliceren van databases in deze herstelomgeving reserveert capaciteit in het herstelgebied. Het onderhouden van deze omgeving omvat het repliceren van nieuwe tenantdatabases terwijl deze zijn ingericht.  
* Herstel
    * Wanneer een verkleinde herstelomgeving wordt gebruikt om de dagelijkse kosten te minimaliseren, moeten pools en databases worden opgeschaald om volledige operationele capaciteit in het herstelgebied te verwerven
    * Nieuwe tenantprovisioning in het herstelgebied zo snel mogelijk inschakelen  
    * Worden geoptimaliseerd voor het herstellen van huurders in prioriteitsvolgorde
    * Worden geoptimaliseerd om huurders zo snel mogelijk online te krijgen door stappen parallel te zetten waar praktische
    * Wees bestand tegen falen, opnieuw bestand en idempotent
    * Het is mogelijk om het proces halverwege de vlucht te annuleren als de oorspronkelijke regio online terugkomt.
* Repatriëring 
    * Mislukt over databases uit het herstelgebied naar replica's in de oorspronkelijke regio met minimale impact op tenants: geen gegevensverlies en minimale periode off-line per tenant.   

In deze zelfstudie worden deze uitdagingen verholpen met functies van Azure SQL Database en het Azure-platform:

* [Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), om alle benodigde capaciteit zo snel mogelijk te reserveren. Azure Resource Manager-sjablonen worden gebruikt om een spiegelbeeld in te richten van de productieservers en elastische pools in het herstelgebied.
* [Geo-replicatie](sql-database-geo-replication-overview.md), om asynchrone gerepliceerde alleen-lezen secondaries voor alle databases te maken. Tijdens een storing mislukt u niet naar de replica's in het herstelgebied.  Nadat de storing is opgelost, gaat u niet meer terug naar de databases in de oorspronkelijke regio zonder gegevensverlies.
* [Asynchrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) failoverbewerkingen die in tenantprioriteitsvolgorde worden verzonden, om failtime voor grote aantallen databases te minimaliseren.
* [Shard management recovery features](sql-database-elastic-database-recovery-manager.md), om database items in de catalogus te wijzigen tijdens herstel en repatriëring. Met deze functies kan de app verbinding maken met tenantdatabases, ongeacht de locatie, zonder de app opnieuw te configureren.
* [DNS-aliassen van SQL-server](dns-alias-overview.md), om naadloze inrichting van nieuwe tenants mogelijk te maken, ongeacht in welke regio de app actief is. DNS-aliassen worden ook gebruikt om het synchronisatieproces van de catalogus verbinding te laten maken met de actieve catalogus, ongeacht de locatie.

## <a name="get-the-disaster-recovery-scripts"></a>Download de scripts voor noodherstel 

> [!IMPORTANT]
> Net als alle Wingtip Tickets management scripts, de DR scripts zijn monster kwaliteit en zijn niet te gebruiken in de productie. 

De herstelscripts die worden gebruikt in deze zelfstudie en wingtip-toepassingsbroncode zijn beschikbaar in de [Wingtip Tickets SaaS-database per tenant GitHub-repository.](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets management scripts te downloaden en te deblokkeren.

## <a name="tutorial-overview"></a>Overzicht van de zelfstudie
In deze zelfstudie gebruikt u eerst georeplicatie om replica's te maken van de toepassing Wingtip Tickets en de databases in een andere regio. Vervolgens slaagt u er niet in om het herstellen van een storing te simuleren. Wanneer de toepassing is voltooid, is deze volledig functioneel in het herstelgebied.

Later, in een afzonderlijke repatriëringsstap, mislukt u over de catalogus- en tenantdatabases in het herstelgebied naar de oorspronkelijke regio. De applicatie en databases blijven beschikbaar tijdens de repatriëring. Wanneer de toepassing is voltooid, is deze volledig functioneel in de oorspronkelijke regio.

> [!Note]
> De toepassing wordt hersteld in het _gekoppelde gebied_ van het gebied waarin de toepassing is geïmplementeerd. Zie [Azure-gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie .

## <a name="review-the-healthy-state-of-the-application"></a>De gezonde status van de toepassing bekijken

Voordat u het herstelproces start, controleert u de normale gezonde status van de toepassing.
1. Open in uw webbrowser de Wingtiphttp://events.wingtip-dpt.&ltTickets&gt;Events Hub &lt;(&gt; ;user .trafficmanager.net - vervang de gebruiker door de gebruikerswaarde van uw implementatie).
    * Schuif naar de onderkant van de pagina en zie de naam en locatie van de catalogusserver in de voettekst. De locatie is de regio waarin u de app hebt geïmplementeerd.
    *TIP: Beweeg met de muis over de locatie om het display te vergroten.* 
    Evenementen hub gezonde staat in de oorspronkelijke ![regio](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klik op de contoso concertzaal huurder en open de evenementpagina.
    * Let in de voettekst op de naam van de tenantserver. De locatie is dezelfde als de locatie van de catalogusserver.

3. Open in de [Azure-portal](https://portal.azure.com)de brongroep waarin de app is geïmplementeerd
    * Let op het gebied waarin de servers worden geïmplementeerd. 

## <a name="sync-tenant-configuration-into-catalog"></a>Tenantconfiguratie synchroniseren in catalogus

In deze taak start u een proces dat de configuratie van de servers, elastische pools en databases synchroniseert met de tenantcatalogus. Het proces houdt deze informatie up-to-date in de catalogus.  Het proces werkt met de actieve catalogus, zowel in de oorspronkelijke regio als in het herstelgebied. De configuratie-informatie wordt gebruikt als onderdeel van het herstelproces om ervoor te zorgen dat de herstelomgeving consistent is met de oorspronkelijke omgeving en vervolgens later tijdens de repatriëring om ervoor te zorgen dat de oorspronkelijke regio wordt aangebracht in overeenstemming met eventuele wijzigingen in de herstelomgeving. De catalogus wordt ook gebruikt om de herstelstatus van tenantbronnen bij te houden

> [!IMPORTANT]
> Voor de eenvoud worden het synchronisatieproces en andere langlopende herstel- en repatriëringsprocessen geïmplementeerd in deze zelfstudies als lokale PowerShell-taken of -sessies die worden uitgevoerd onder het inloggen van uw clientgebruiker. De verificatietokens die worden uitgegeven wanneer u inlogt, verlopen na enkele uren en de taken mislukken dan. In een productiescenario moeten langlopende processen worden geïmplementeerd als betrouwbare Azure-services, uitgevoerd onder een serviceprincipal. Zie [Azure PowerShell gebruiken om een serviceprincipal te maken met een certificaat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. Open in het _PowerShell ISE_het bestand ...\Learning Modules\UserConfig.psm1. Vervang `<resourcegroup>` `<user>` en op lijnen 10 en 11 met de waarde die wordt gebruikt toen u de app hebt geïmplementeerd.  Sla het bestand op!

2. Open in het *PowerShell ISE*het script en de set ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1:
    * **$DemoScenario = 1,** Een achtergrondtaak starten die tenantserver synchroniseert en configuratiegegevens in de catalogus groepeert

3. Druk op **F5** om het synchronisatiescript uit te voeren. Er wordt een nieuwe PowerShell-sessie geopend om de configuratie van tenantbronnen te synchroniseren.
![Synchronisatieproces](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Laat het PowerShell-venster op de achtergrond draaien en ga verder met de rest van de zelfstudie. 

> [!Note]
> Het synchronisatieproces maakt verbinding met de catalogus via een DNS-alias. Deze alias wordt gewijzigd tijdens herstel en repatriëring om naar de actieve catalogus te wijzen. Het synchronisatieproces houdt de catalogus up-to-date met alle database- of poolconfiguratiewijzigingen die in het herstelgebied zijn aangebracht.  Tijdens de repatriëring worden deze wijzigingen toegepast op de equivalente middelen in de oorspronkelijke regio.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Secundaire databasereplica's maken in het herstelgebied

In deze taak start u een proces dat een dubbele app-instantie implementeert en de catalogus en alle tenantdatabases repliceert naar een herstelgebied.

> [!Note]
> Deze zelfstudie voegt georeplicatiebescherming toe aan de voorbeeldtoepassing Wingtip Tickets. In een productiescenario voor een toepassing die georeplicatie gebruikt, wordt elke tenant vanaf het begin voorzien van een geo-gerepliceerde database. Zie [Het ontwerpen van zeer beschikbare services met Azure SQL Database](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. Open in het *PowerShell ISE*het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende waarden in:
    * **$DemoScenario = 2**, Spiegelbeeldherstelomgeving maken en catalogus- en tenantdatabases repliceren

2. Druk op **F5** om het script uit te voeren. Er wordt een nieuwe PowerShell-sessie geopend om de replica's te maken.
![Synchronisatieproces](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>De normale toepassingsstatus controleren

Op dit moment wordt de toepassing normaal uitgevoerd in de oorspronkelijke regio en wordt deze nu beschermd door geo-replicatie.  Alleen-lezen secundaire replica's, bestaan in het herstelgebied voor alle databases. 

1. Bekijk in de Azure-portal uw brongroepen en houd er rekening mee dat er een resourcegroep is gemaakt met -herstelachtervoegsel in het herstelgebied. 

2. Ontdek de bronnen in de groep herstelresources.  

3. Klik op de Contoso Concert Hall database op de _tenants1-dpt-&lt;user-recovery&gt;_ server.  Klik op Geo-Replicatie aan de linkerkant. 

    ![Contoso Concert geo-replicatie link](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Noteer in de map Azure-regio's de koppeling georeplicatie tussen het primaire in het oorspronkelijke gebied en het secundaire in het herstelgebied.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Fail over de toepassing in het herstelgebied

### <a name="geo-replication-recovery-process-overview"></a>Overzicht van herstelproces voor georeplicatie

Het herstelscript voert de volgende taken uit:

1. Hiermee schakelt u het eindpunt Verkeerbeheer voor de web-app in de oorspronkelijke regio uit. Als u het eindpunt uitschakelt, kunnen gebruikers geen verbinding maken met de app in een ongeldige status als de oorspronkelijke regio online komt tijdens het herstel.

1. Hiermee wordt een force-failover van de catalogusdatabase in het herstelgebied gebruikt om de primaire database te maken en wordt de _activecatalog-alias_ bijgewerkt om naar de server van de herstelcatalogus te wijzen.

1. Hiermee wordt de _nieuwetenantalias_ bijgewerkt om naar de tenantserver in het herstelgebied te wijzen. Als u deze alias wijzigt, zorgt u ervoor dat de databases voor nieuwe tenants in het herstelgebied worden ingericht. 

1. Hiermee markeert u alle bestaande tenants in de herstelcatalogus als offline om toegang tot tenantdatabases te voorkomen voordat ze zijn mislukt.

1. Hiermee werkt u de configuratie van alle elastische pools en gerepliceerde afzonderlijke databases in het herstelgebied bij om hun configuratie in de oorspronkelijke regio te spiegelen. (Deze taak is alleen nodig als groepen of gerepliceerde databases in de herstelomgeving tijdens normale bewerkingen worden verkleind om de kosten te verlagen).

1. Hiermee schakelt u het eindpunt Verkeerbeheer in voor de web-app in het herstelgebied. Als u dit eindpunt inschakelt, kan de toepassing nieuwe tenants inrichten. In dit stadium zijn bestaande tenants nog offline.

1. Verzendt batches van aanvragen om mislukte databases in prioriteitsvolgorde af te dwingen.
    * Batches zijn zo georganiseerd dat databases parallel over alle groepen worden mislukt.
    * Failoveraanvragen worden ingediend met asynchrone bewerkingen, zodat ze snel worden ingediend en meerdere aanvragen gelijktijdig kunnen worden verwerkt.

   > [!Note]
   > In een uitvalscenario zijn de primaire databases in de oorspronkelijke regio offline.  Force fail over op de secundaire breekt de verbinding met de primaire zonder te proberen om eventuele resterende wachtrij transacties toe te passen. In een DR drill scenario zoals deze tutorial, als er een update activiteit op het moment van failover kan er wat verlies van gegevens. Later, tijdens de repatriëring, wanneer u niet over databases in het herstelgebied terug naar de oorspronkelijke regio, een normale failover wordt gebruikt om ervoor te zorgen dat er geen verlies van gegevens.

1. Hiermee controleert u de SQL-databaseservice om te bepalen wanneer databases zijn mislukt. Zodra een tenantdatabase is mislukt, wordt de catalogus bijgewerkt om de herstelstatus van de tenantdatabase vast te leggen en de tenant als online te markeren.
    * Tenantdatabases kunnen worden geopend door de toepassing zodra ze online zijn gemarkeerd in de catalogus.
    * Een som van de rijversiewaarden in de tenantdatabase wordt opgeslagen in de catalogus. Deze waarde fungeert als een vingerafdruk waarmee het repatriëringsproces kan bepalen of de database is bijgewerkt in het herstelgebied.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Voer het script uit om niet over te gaan naar het herstelgebied

Stel je nu voor dat er een storing is in de regio waarin de toepassing is geïmplementeerd en voer het herstelscript uit:

1. Open in het *PowerShell ISE*het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende waarden in:
    * **$DemoScenario = 3,** De app herstellen in een herstelgebied door niet over te gaan naar replica's

2. Druk op **F5** om het script uit te voeren.  
    * Het script wordt geopend in een nieuw PowerShell-venster en start vervolgens een reeks PowerShell-taken die parallel worden uitgevoerd. Deze taken mislukken over tenantdatabases naar het herstelgebied.
    * Het herstelgebied is het _gekoppelde gebied_ dat is gekoppeld aan het Azure-gebied waarin u de toepassing hebt geïmplementeerd. Zie [Azure-gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie . 

3. Controleer de status van het herstelproces in het PowerShell-venster.
    ![failoverproces](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Als u de code voor de hersteltaken wilt verkennen, bekijkt u de PowerShell-scripts in de map ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>De status van de aanvraag controleren tijdens het herstel

Hoewel het eindpunt van de toepassing is uitgeschakeld in Traffic Manager, is de toepassing niet beschikbaar. Nadat de catalogus is mislukt naar het herstelgebied en alle tenants die offline zijn gemarkeerd, wordt de toepassing weer online gezet. Hoewel de toepassing beschikbaar is, wordt elke tenant offline weergegeven in de gebeurtenishub totdat de database is mislukt. Het is belangrijk om uw toepassing te ontwerpen voor het verwerken van offline tenantdatabases.

1. Vernieuw onmiddellijk nadat de catalogusdatabase is hersteld, de Hub voor gebeurtenissen van Wingtip Tickets in uw webbrowser.
   * In de voettekst wordt opgemerkt dat de _-recovery_ naam van de catalogusserver nu een herstelachtervoegsel heeft en zich in het herstelgebied bevindt.
   * Merk op dat tenants die nog niet zijn hersteld, zijn gemarkeerd als offline en niet kunnen worden geselecteerd.  

     > [!Note]
     > Als u slechts een paar databases wilt herstellen, u de browser mogelijk niet vernieuwen voordat het herstel is voltooid, zodat u de tenants mogelijk niet ziet terwijl ze offline zijn. 
 
     ![Evenementenhub offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Als u de pagina Gebeurtenissen van een offlinetenant rechtstreeks opent, wordt een melding 'tenant offline' weergegeven. Als Contoso Concert Hall bijvoorbeeld offline is,&gt;probeert ![u de pagina (gebruiker trafficmanager.net/contosoconcerthall Contoso Offline) te openen http://events.wingtip-dpt.&lt](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Een nieuwe huurder in het herstelgebied voorzien
Nog voordat alle bestaande tenantdatabases zijn mislukt, u nieuwe tenants inhet herstelgebied inrichten.  

1. Open in het *PowerShell ISE*het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 en stel de volgende eigenschap in:
    * **$DemoScenario = 4**, Een nieuwe huurder in het herstelgebied voorzien

2. Druk op **F5** om het script uit te voeren en de nieuwe tenant in te richten. 

3. De pagina gebeurtenissen in de Hawthorn Hall wordt geopend in de browser wanneer deze is voltooid. Noteer van de voettekst dat de Hawthorn Hall-database is ingericht in het herstelgebied.
    ![Pagina Gebeurtenissen in Hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. Vernieuw in de browser de pagina Wingtip Tickets Events Hub om Hawthorn Hall inbegrepen te zien. 
    * Als u Hawthorn Hall ingericht zonder te wachten tot de andere huurders te herstellen, andere huurders kunnen nog steeds offline.


## <a name="review-the-recovered-state-of-the-application"></a>De herstelde status van de aanvraag controleren

Wanneer het herstelproces is voltooid, zijn de toepassing en alle tenants volledig functioneel in het herstelgebied. 

1. Zodra het scherm in het PowerShell-consolevenster aangeeft dat alle tenants zijn hersteld, vernieuwt u de gebeurtenishub.  De huurders zullen allemaal online verschijnen, inclusief de nieuwe huurder, Hawthorn Hall.

    ![herstelde en nieuwe huurders in de evenementenhub](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Open in de [Azure-portal](https://portal.azure.com)de lijst met brongroepen.  
    * Let op de resourcegroep die u hebt geïmplementeerd, plus de herstelbrongroep, met het achtervoegsel _-herstel._  De herstelbrongroep bevat alle resources die tijdens het herstelproces zijn gemaakt, plus nieuwe bronnen die tijdens de storing zijn gemaakt.  

3. Open de herstelbrongroep en let op de volgende items:
   * De herstelversies van de catalogus en _-recovery_ tenants1-servers, met -herstelachtervoegsel.  De herstelde catalogus en tenant databases op deze servers hebben allemaal de namen die worden gebruikt in de oorspronkelijke regio.

   * De _&lt;tenants2-dpt-&gt;user-recovery_ SQL server.  Deze server wordt gebruikt voor het inrichten van nieuwe tenants tijdens de storing.
   * De app-service met de _naam,&lt;de&gt;-&lt;gebruiker van de gebeurtenissen-wingtip-dpt-&gt_;, het herstelexemplaar van de gebeurtenissen-app. 

     ![Azure-herstelbronnen](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Open de _SQL-server&lt;&gt;tenants2-dpt- user-recovery._  Let op het bevat de database _meidoornhal_ en de elastische pool, _Pool1_.  De _hawthornhall-database_ is geconfigureerd als een elastische database in de elastische pool _Pool1._

5. Navigeer terug naar de brongroep en klik op de Contoso Concert Hall-database op de _server voor het doorgebruikers-herstel van tenants1-dpt-&lt;user-recovery.&gt;_ Klik op Geo-Replicatie aan de linkerkant.
    
    ![Contoso-database na failover](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Tenantgegevens wijzigen 
In deze taak werkt u een van de tenantdatabases bij. 

1. Zoek in uw browser de evenementenlijst voor de Contoso Concert Hall en noteer de achternaam van het evenement.
2. Stel in het *ise-script van PowerShell*de volgende waarde in het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1:
    * **$DemoScenario = 5** Een gebeurtenis verwijderen uit een tenant in het herstelgebied
3. Druk op **F5** om het script uit te voeren
4. Vernieuw de pagina Gebeurtenissen inhttp://events.wingtip-dpt.&ltDe&gt;Concertzaal van &lt;&gt; Contoso ( ;gebruiker .trafficmanager.net/contosoconcerthall - vervang gebruiker met de gebruikerswaarde van uw implementatie) en merk op dat de laatste gebeurtenis is verwijderd.

## <a name="repatriate-the-application-to-its-original-production-region"></a>De aanvraag repatriëren naar het oorspronkelijke productiegebied

Met deze taak wordt de toepassing gerepatrieerd naar de oorspronkelijke regio. In een echt scenario zou u de repatriëring starten wanneer de storing is opgelost.

### <a name="repatriation-process-overview"></a>Overzicht van repatriëringsproces

![RepatriëringArchitectuur](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Het repatriëringsproces:
1. Hiermee annuleert u openstaande of in-flight database herstelverzoeken.
2. Hiermee wordt de _nieuwetenantalias_ bijgewerkt om naar de server van de tenants in het herkomstgebied te wijzen. Als u deze alias wijzigt, wordt de databases voor nieuwe tenants nu in het herkomstgebied ingericht.
3. Als u gewijzigde tenantgegevens naar de oorspronkelijke regio zaait
4. Mislukt over tenantdatabases in prioriteitsvolgorde.

Failover verplaatst de database effectief naar de oorspronkelijke regio. Wanneer de database mislukt, worden alle geopende verbindingen verbroken en is de database enkele seconden niet beschikbaar. Toepassingen moeten worden geschreven met een logica voor nieuwe apparaten om ervoor te zorgen dat ze opnieuw verbinding maken.  Hoewel deze korte ontkoppeling vaak niet wordt opgemerkt, u ervoor kiezen om databases buiten kantooruren te repatriëren. 


### <a name="run-the-repatriation-script"></a>Het repatriëringsscript uitvoeren
Stel je nu eens voor dat de storing is opgelost en voer het repatriëringsscript uit.

1. In de *PowerShell ISE*, in de ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 script.

2. Controleer of het synchronisatieproces van de catalogus nog steeds wordt uitgevoerd in de PowerShell-instantie.  Start het zo nodig opnieuw op door het instellen van:
    * **$DemoScenario = 1,** Tenantserver-, pool- en databaseconfiguratiegegevens in de catalogus synchroniseren
    * Druk op **F5** om het script uit te voeren.

3.  Dan om het repatriëringsproces te starten, stel je in:
    * **$DemoScenario = 6**, De app repatriëren in de oorspronkelijke regio
    * Druk op **F5** om het herstelscript in een nieuw PowerShell-venster uit te voeren.  Repatriëring duurt enkele minuten en kan worden gecontroleerd in het PowerShell-venster.
    ![Repatriëringsproces](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Terwijl het script wordt uitgevoerd, vernieuwt u de pagina Gebeurtenissenhub (http://events.wingtip-dpt.&lt;gebruiker&gt;.trafficmanager.net)
    * Merk op dat alle huurders online en toegankelijk zijn gedurende dit proces.

5. Nadat de repatriëring is voltooid, vernieuwt u de hub Evenementen en opent u de evenementenpagina voor Hawthorn Hall. Merk op dat deze database is gerepatrieerd naar de oorspronkelijke regio.
    ![Evenementen hub gerepatrieerd](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Het ontwerpen van de toepassing om ervoor te zorgen dat app en database worden gecolocatie 
De toepassing is zo ontworpen dat deze altijd verbinding maakt vanuit een instantie in dezelfde regio als de tenantdatabase. Dit ontwerp vermindert de latentie tussen de toepassing en de database. Deze optimalisatie gaat ervan uit dat de app-naar-database-interactie chater is dan de interactie tussen gebruiker en app.  

Tenantdatabases kunnen tijdens de repatriëring enige tijd worden verspreid over herstel- en oorspronkelijke regio's. Voor elke database zoekt de app het gebied op waarin de database zich bevindt door een DNS-lookup uit te doen op de naam van de tenantserver. In SQL Database is de servernaam een alias. De naam van de aliasserver bevat de regionaam. Als de toepassing zich niet in hetzelfde gebied bevindt als de database, wordt deze doorgestuurd naar de instantie in hetzelfde gebied als de databaseserver.  Doorleiden naar instantie in hetzelfde gebied als de database minimaliseert de latentie tussen app en database. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> 
> * Database- en configuratiegegevens voor elastische groep synchroniseren in de tenantcatalogus
> * Een herstelomgeving instellen in een alternatieve regio, bestaande uit toepassing, servers en groepen
> * _Geo-replicatie_ gebruiken om de catalogus- en tenantdatabases te repliceren naar het herstelgebied
> * Fail over de toepassing en catalogus en tenant databases naar het herstelgebied 
> * De toepassings-, catalogus- en tenantdatabases naar het oorspronkelijke gebied teruglaten mislukken nadat de storing is opgelost

U meer informatie krijgen over de technologieën die Azure SQL-database biedt om bedrijfscontinuïteit mogelijk te maken in de documentatie van het [Bedrijfscontinuïteitsoverzicht.](sql-database-business-continuity.md)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Extra tutorials die voortbouwen op de Wingtip SaaS applicatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
