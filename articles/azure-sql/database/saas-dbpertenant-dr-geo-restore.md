---
title: 'SaaS-apps: geografisch redundante back-ups voor herstel na nood gevallen'
description: Meer informatie over het gebruik van Azure SQL Database geo-redundante back-ups om een multitenant SaaS-app te herstellen in het geval van een storing
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 44ed9c0d19b6e0034b49e36448765d098d575273
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325314"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Geo-herstel gebruiken om een multi tenant-SaaS-toepassing te herstellen vanuit back-ups van data bases
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelf studie wordt een volledig herstel scenario voor nood gevallen voor een SaaS-toepassing met meerdere tenants verkend, geïmplementeerd met de data base per Tenant model. U gebruikt [geo-Restore](recovery-using-backups.md) om de catalogus en de Tenant databases te herstellen van het automatisch onderhouden van geografisch redundante back-ups in een alternatieve herstel regio. Nadat de onderbreking is opgelost, gebruikt u [geo-replicatie](active-geo-replication-overview.md) om gewijzigde data bases te repatriëren in hun oorspronkelijke regio.

![Geo-Restore-architectuur](./media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geo-Restore is de voordeligste oplossing voor herstel na nood geval voor Azure SQL Database. Het herstellen van geo-redundante back-ups kan echter tot een uur gegevens verlies leiden. Het kan veel tijd duren, afhankelijk van de grootte van elke Data Base. 

> [!NOTE]
> Herstel toepassingen met de laagst mogelijke RPO en RTO met behulp van geo-replicatie in plaats van geo-herstel.

In deze zelf studie worden zowel herstel-als repatriër-werk stromen besproken. In deze zelfstudie leert u procedures om het volgende te doen:
> [!div class="checklist"]
> 
> * Data Base-en configuratie gegevens van elastische groepen synchroniseren met de Tenant catalogus.
> * Stel een gespiegelde installatie kopie omgeving in een herstel regio in die toepassingen, servers en Pools bevat.   
> * Herstel Catalog-en Tenant-data bases met behulp van geo-Restore.
> * Gebruik geo-replicatie om de Tenant catalogus te repatriëren en gewijzigde Tenant databases nadat de onderbreking is opgelost.
> * Werk de catalogus bij wanneer elke Data Base wordt hersteld (of gerepatriërd) om de huidige locatie van de actieve kopie van de data base van elke Tenant bij te houden.
> * Zorg ervoor dat de toepassing en de Tenant database altijd zich in dezelfde Azure-regio bevinden om de latentie te verminderen. 
 

Voordat u met deze zelf studie begint, moet u aan de volgende vereisten voldoen:
* Implementeer de SaaS-data base van de Wingtip tickets per Tenant-app. Zie [de SaaS-data base van Wingtip tickets per Tenant toepassing implementeren en verkennen](saas-dbpertenant-get-started-deploy.md)om in minder dan vijf minuten te implementeren. 
* Installeer Azure PowerShell. Zie [aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)voor meer informatie.

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Inleiding tot het herstel patroon voor geo-herstel

Herstel na nood gevallen (DR) is een belang rijke overweging voor veel toepassingen, ongeacht of er nalevings redenen of bedrijfs continuïteit zijn. Als er sprake is van een langdurige service onderbreking, kan een goed voor bereide DR-plan de bedrijfs onderbreking minimaliseren. Een DR-plan op basis van geo-Restore moet verschillende doelen uitvoeren:
 * Reserveer zo snel mogelijk alle benodigde capaciteit in de gekozen herstel regio om ervoor te zorgen dat deze beschikbaar is voor het herstellen van Tenant databases.
 * Stel een gespiegelde herstel omgeving voor installatie kopieën in die de oorspronkelijke groep en database configuratie weerspiegelt. 
 * Annulering van het herstel proces in medio vlucht toestaan als de oorspronkelijke regio weer online is.
 * Schakel het inrichten van tenants snel in zodat de nieuwe Tenant onboarding zo snel mogelijk opnieuw kan worden opgestart.
 * Worden geoptimaliseerd om tenants in volg orde van prioriteit te herstellen.
 * Zorg zo snel mogelijk om tenants online te halen door stappen parallel uit te voeren.
 * Wees flexibel voor fouten, herstartbaar en idempotent.
 * U kunt data bases in hun oorspronkelijke regio met minimale gevolgen voor tenants bekrachtigen wanneer de storing is opgelost.  

> [!NOTE]
> De toepassing wordt hersteld in het gekoppelde gebied van de regio waarin de toepassing wordt geïmplementeerd. Zie [gekoppelde Azure-regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie.   

In deze zelf studie worden functies van Azure SQL Database en het Azure-platform gebruikt om deze uitdagingen aan te pakken:

* [Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), om zo snel mogelijk alle benodigde capaciteit te reserveren. Azure Resource Manager sjablonen worden gebruikt om een gespiegelde installatie kopie van de oorspronkelijke servers en elastische Pools in de herstel regio in te richten. Er wordt ook een afzonderlijke server en groep gemaakt voor het inrichten van nieuwe tenants.
* [Elastic database-client bibliotheek](elastic-database-client-library.md) (EDCL) om een catalogus voor een Tenant database te maken en te onderhouden. De uitgebreide catalogus bevat regel matig vernieuwde groeps-en database configuratie-informatie.
* [Shard beheer herstel functies](elastic-database-recovery-manager.md) van de EDCL voor het behouden van database locatie vermeldingen in de catalogus tijdens herstel en repatriëring.  
* [Geo-herstellen](../../key-vault/general/disaster-recovery-guidance.md), om de catalogus en de Tenant databases te herstellen van automatisch onderhanden geografisch redundante back-ups. 
* [Asynchrone herstel bewerkingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), verzonden in de volg orde van de Tenant prioriteit, worden voor elke pool door het systeem in de wachtrij geplaatst en verwerkt in batches, zodat de groep niet overbelast is. Deze bewerkingen kunnen, indien nodig, vóór of tijdens de uitvoering worden geannuleerd.   
* [Geo-replicatie](active-geo-replication-overview.md), om data bases te repatriëren naar de oorspronkelijke regio na de storing. Er zijn geen gegevens verlies en minimale invloed op de Tenant wanneer u geo-replicatie gebruikt.
* [DNS-aliassen van SQL Server](../../sql-database/dns-alias-overview.md), zodat het synchronisatie proces van de catalogus verbinding kan maken met de actieve catalogus, ongeacht de locatie.  

## <a name="get-the-disaster-recovery-scripts"></a>De nood herstel scripts ophalen

De DR-scripts die in deze zelf studie worden gebruikt, zijn beschikbaar in de [opslag plaats SaaS-data base van Wingtip tickets per Tenant github](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Bekijk de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en deblokkeren van de Wingtip tickets-beheer scripts.

> [!IMPORTANT]
> Net als alle Wingtip tickets hebben de DR-scripts een voorbeeld kwaliteit en kunnen ze niet worden gebruikt in de productie omgeving.

## <a name="review-the-healthy-state-of-the-application"></a>Bekijk de status in orde van de toepassing
Voordat u het herstel proces start, controleert u de normale status van de toepassing.

1. Open in uw webbrowser de Wingtip tickets-gebeurtenissen hub ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net, vervang de &lt; gebruiker door &gt; de gebruikers waarde van uw implementatie).
    
   Ga naar de onderkant van de pagina en Let op de naam en locatie van de catalogus server in de voet tekst. De locatie is de regio waarin u de app hebt geïmplementeerd.    

   > [!TIP]
   > Beweeg de muis aanwijzer over de locatie om de weer gave te verg Roten.

   ![Gebeurtenissen hub in de oorspronkelijke regio in orde](./media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Selecteer de contoso concert hal-Tenant en open de bijbehorende gebeurtenis pagina.

   In de voet tekst ziet u de server naam van de Tenant. De locatie is hetzelfde als de locatie van de catalogus server.

   ![Oorspronkelijke regio voor concert van contoso](./media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)   

3. Bekijk en open in het [Azure Portal](https://portal.azure.com)de resource groep waarin u de app hebt geïmplementeerd.

   Let op de resources en de regio waarin de app service-onderdelen en-SQL Database worden geïmplementeerd.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>De Tenant configuratie synchroniseren met de catalogus

In deze taak start u een proces voor het synchroniseren van de configuratie van de servers, elastische Pools en data bases in de Tenant catalogus. Deze informatie wordt later gebruikt voor het configureren van een gespiegelde installatie kopie omgeving in de herstel regio.

> [!IMPORTANT]
> Ter vereenvoudiging worden het synchronisatie proces en andere langlopende herstel-en planningsproces-processen geïmplementeerd in deze voor beelden als lokale Power shell-taken of-sessies die worden uitgevoerd onder de aanmelding van de client gebruiker. De verificatie tokens die worden uitgegeven wanneer u zich na enkele uren verstrijkt, en de taken mislukken. In een productie scenario moeten langlopende processen worden geïmplementeerd als betrouw bare Azure-Services van een van de typen die worden uitgevoerd onder een service-principal. Zie [Azure PowerShell gebruiken om een service-principal met een certificaat te maken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. Open in de Power shell-ISE het bestand. ..\Learning Modules\UserConfig.psm1. Vervang `<resourcegroup>` en `<user>` op regels 10 en 11 door de waarde die wordt gebruikt bij het implementeren van de app. Sla het bestand op.

2. Open in de Power shell-ISE het script. ..\Learning Modules\Business continuïteit en nood Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.

    In deze zelf studie voert u elk van de scenario's in dit Power shell-script uit. Zorg dat u dit bestand opent.

3. Stel het volgende in:

    $DemoScenario = 1: start een achtergrond taak waarmee de gegevens van de Tenant server en de groeps configuratie worden gesynchroniseerd met de catalogus.

4. Selecteer F5 om het synchronisatie script uit te voeren. 

    Deze informatie wordt later gebruikt om ervoor te zorgen dat herstel een mirror-installatie kopie maakt van de servers, Pools en data bases in de herstel regio.  

    ![Synchronisatie proces](./media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Laat het Power shell-venster op de achtergrond draaien en ga verder met de rest van deze zelf studie.

> [!NOTE]
> Het synchronisatie proces maakt verbinding met de catalogus via een DNS-alias. De alias wordt gewijzigd tijdens het terugzetten en de repatriëring om naar de actieve catalogus te verwijzen. Het synchronisatie proces houdt de catalogus up-to-date met eventuele wijzigingen in de data base-of groeps configuratie in de herstel regio. Tijdens de herrepatriëring worden deze wijzigingen toegepast op de gelijkwaardige resources in de oorspronkelijke regio.

## <a name="geo-restore-recovery-process-overview"></a>Overzicht van het herstel proces geo-Restore

Het proces voor het terugzetten van geo-herstel implementeert de toepassing en herstelt data bases van back-ups naar de herstel regio.

Het herstel proces voert het volgende uit:

1. Hiermee schakelt u het Azure Traffic Manager-eind punt voor de web-app uit in de oorspronkelijke regio. Als u het eind punt uitschakelt, kunnen gebruikers geen verbinding maken met de app in een ongeldige status als de oorspronkelijke regio tijdens het herstel online is.

2. Voorziet in een herstel catalogus server in de herstel regio, geo-herstelt de catalogus database en werkt de activecatalog-alias bij zodat deze verwijst naar de herstelde catalogus server. Als u de catalogus alias wijzigt, zorgt u ervoor dat het synchronisatie proces van de catalogus altijd wordt gesynchroniseerd met de actieve catalogus.

3. Hiermee worden alle bestaande tenants in de herstel catalogus als offline gemarkeerd om te voor komen dat de toegang tot de Tenant databases wordt hersteld.

4. Hiermee wordt een exemplaar van de app in de herstel regio ingericht en geconfigureerd voor gebruik van de herstelde catalogus in die regio. Om de latentie tot een minimum te beperken, is de voor beeld-app ontworpen om altijd verbinding te maken met een Tenant database in dezelfde regio.

5. Richt een server en elastische pool in waarin nieuwe tenants zijn ingericht. Door deze resources te maken, zorgt u ervoor dat het inrichten van nieuwe tenants geen conflicten veroorzaakt bij het herstellen van bestaande tenants.

6. Hiermee wordt de nieuwe Tenant alias bijgewerkt zodat deze verwijst naar de server voor nieuwe Tenant databases in de herstel regio. Door deze alias te wijzigen, zorgt u ervoor dat data bases voor nieuwe tenants in de herstel regio worden ingericht.
        
7. Richt servers en elastische Pools in de herstel regio voor het herstellen van Tenant databases. Deze servers en Pools zijn een spiegel beeld van de configuratie in de oorspronkelijke regio. Bij het inrichten van Pools vooraf worden de benodigde capaciteit voor het herstellen van alle data bases gereserveerd.

    Een storing in een regio kan aanzienlijke druk op de resources die beschikbaar zijn in de gekoppelde regio, worden geplaatst. Als u gebruikmaakt van geo-Restore voor DR, is het raadzaam om snel resources te reserveren. Overweeg geo-replicatie als het essentieel is dat een toepassing wordt hersteld in een bepaalde regio. 

8. Hiermee wordt het Traffic Manager-eind punt voor de web-app in de herstel regio ingeschakeld. Als u dit eind punt inschakelt, kan de toepassing nieuwe tenants inrichten. In deze fase blijven bestaande tenants nog steeds offline.

9. Verzendt batches van aanvragen om data bases in volg orde van prioriteit te herstellen. 

    * Batches zijn ingedeeld zodat data bases parallel worden hersteld in alle groepen.  

    * Herstel aanvragen worden asynchroon verzonden, zodat ze snel worden verzonden en in de wachtrij worden geplaatst voor uitvoering in elke groep.

    * Omdat herstel aanvragen parallel worden verwerkt in alle groepen, is het beter om belang rijke tenants in veel Pools te distribueren. 

10. Controleert de service om te bepalen wanneer data bases worden hersteld. Nadat een Tenant database is hersteld, wordt deze online gemarkeerd in de catalogus en wordt een rowversion-totaal voor de Tenant database vastgelegd. 

    * Tenant-data bases kunnen worden geopend door de toepassing zodra ze online zijn gemarkeerd in de catalogus.

    * De som van de rowversion-waarden in de Tenant database wordt opgeslagen in de catalogus. Deze som fungeert als een vinger afdruk waarmee het planningsproces proces kan bepalen of de data base is bijgewerkt in de herstel regio.       

## <a name="run-the-recovery-script"></a>Het herstel script uitvoeren

> [!IMPORTANT]
> In deze zelf studie worden data bases teruggezet vanuit geografisch redundante back-ups. Hoewel deze back-ups doorgaans binnen tien minuten beschikbaar zijn, kan het tot een uur duren voordat het duurt. Het script wordt onderbroken totdat ze beschikbaar zijn.

Stel dat er een storing optreedt in de regio waarin de toepassing wordt geïmplementeerd en voer het herstel script uit:

1. Stel in het Power shell-ISE in het script. ..\Learning Modules\Business continuïteit en ramp Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende waarde in:

    $DemoScenario = 2: herstel de app in een herstel regio door geografisch redundante back-ups te herstellen.

2. Selecteer F5 om het script uit te voeren.  

    * Het script wordt geopend in een nieuw Power shell-venster en vervolgens wordt een set Power shell-taken gestart die parallel worden uitgevoerd. Met deze taken worden servers, Pools en data bases teruggezet naar de herstel regio.

    * De herstel regio is de gekoppelde regio die is gekoppeld aan de Azure-regio waarin u de toepassing hebt geïmplementeerd. Zie [gekoppelde Azure-regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie. 

3. Bewaak de status van het herstel proces in het Power shell-venster.

    ![Herstel proces](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Als u de code voor de herstel taken wilt verkennen, controleert u de Power shell-scripts in de map. ..\Learning Modules\Business continuïteit en nood Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>De toepassings status controleren tijdens het herstel
Wanneer het eind punt van de toepassing in Traffic Manager is uitgeschakeld, is de toepassing niet beschikbaar. De catalogus wordt hersteld en alle tenants zijn offline gemarkeerd. Het toepassings eindpunt in de herstel regio wordt vervolgens ingeschakeld en de toepassing is weer online. Hoewel de toepassing beschikbaar is, worden tenants offline weer gegeven in de gebeurtenissen hub totdat de data bases worden hersteld. Het is belang rijk dat u uw toepassing ontwerpt voor het verwerken van offline Tenant databases.

* Nadat de catalogus database is hersteld, maar voordat de tenants weer online zijn, vernieuwt u de Wingtip tickets-gebeurtenissen hub in uw webbrowser.

  * In de voet tekst ziet u dat de naam van de catalogus server nu een achtervoegsel voor herstel heeft en zich in de herstel regio bevindt.

  * U ziet dat tenants die nog niet zijn hersteld, zijn gemarkeerd als offline en niet kunnen worden geselecteerd.   
 
    ![Herstel proces](./media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)  

  * Als u de gebeurtenis pagina van een Tenant rechtstreeks opent terwijl de Tenant offline is, wordt op de pagina een offline melding voor tenants weer gegeven. Als contoso concert zaal bijvoorbeeld offline is, probeert u, http://events.wingtip-dpt.&lt User &gt; . trafficmanager.net/contosoconcerthall te openen.

    ![Herstel proces](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Een nieuwe Tenant inrichten in de herstel regio
Zelfs voordat Tenant databases worden hersteld, kunt u nieuwe tenants inrichten in de herstel regio. Nieuwe Tenant databases die in de herstel regio worden ingericht, worden later hersteld met de herstelde data bases.   

1. Stel in het Power shell-ISE in het script. ..\Learning Modules\Business continuïteit en ramp Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende eigenschap in:

    $DemoScenario = 3: een nieuwe Tenant inrichten in de herstel regio.

2. Selecteer F5 om het script uit te voeren.

3. De pagina Hawthorn Hall Events wordt geopend in de browser wanneer het inrichten is voltooid. 

    U ziet dat de Hawthorn Hall-data base zich in de herstel regio bevindt.

    ![Hawthorn-zaal ingericht in de herstel regio](./media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. Vernieuw de pagina Wingtip tickets-evenementen hub in de browser om Hawthorn Hall inbegrepen te bekijken. 

    Als u Hawthorn-zaal hebt ingericht zonder te wachten tot de andere tenants zijn hersteld, kunnen andere tenants nog steeds offline zijn.

## <a name="review-the-recovered-state-of-the-application"></a>De herstelde status van de toepassing controleren

Wanneer het herstel proces is voltooid, zijn de toepassing en alle tenants volledig functioneel in de herstel regio. 

1. Nadat de weer gave in het Power shell-console venster aangeeft dat alle tenants zijn hersteld, moet u de Events hub vernieuwen. 

    De tenants worden allemaal online weer gegeven, met inbegrip van de nieuwe Tenant, Hawthorn Hall.

    ![Herstelde en nieuwe tenants in de Events hub](./media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klik op contoso concert hal en open de pagina evenementen. 

    In de voet tekst ziet u dat de data base zich bevindt op de herstel server die zich in de herstel regio bevindt.

    ![Contoso in de herstel regio](./media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Open de lijst met resource groepen in het [Azure Portal](https://portal.azure.com).  

    U ziet de resource groep die u hebt geïmplementeerd, plus de resource groep herstellen met het achtervoegsel-Recovery. De resource groep voor herstel bevat alle resources die zijn gemaakt tijdens het herstel proces, plus nieuwe resources die zijn gemaakt tijdens de onderbreking. 

4. Open de resource groep herstellen en Let op de volgende items:

   * De herstel versies van de catalogus-en tenants1-servers met het achtervoegsel-Recovery. De herstelde catalogus en Tenant databases op deze servers hebben allemaal de namen die in de oorspronkelijke regio worden gebruikt.

   * De tenants2-dpt- &lt; gebruikers &gt; herstel SQL-Server. Deze server wordt gebruikt voor het inrichten van nieuwe tenants tijdens de onderbreking.

   * De app service met de naam Events-Wingtip-dpt- &lt; recoveryregion &gt; - &lt; gebruiker &gt; , die het herstel exemplaar is van de app Events.

     ![Contoso-resources in de herstel regio](./media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Open de tenants2-dpt- &lt; gebruikers &gt; herstel SQL-Server. U ziet dat het de data base hawthornhall en de elastische pool Pool1 bevat. De hawthornhall-data base is geconfigureerd als een elastische data base in de elastische Pool1-pool.

## <a name="change-the-tenant-data"></a>De Tenant gegevens wijzigen 
In deze taak werkt u een van de herstelde Tenant databases bij. In het proces voor het repatriëren worden herstelde data bases gekopieerd die in de oorspronkelijke regio zijn gewijzigd. 

1. Ga in uw browser naar de lijst met gebeurtenissen voor de concert zaal van contoso, blader door de gebeurtenissen en Let op de laatste gebeurtenis, serieus Strauss.

2. Stel in het Power shell-ISE in het script. ..\Learning Modules\Business continuïteit en ramp Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende waarde in:

    $DemoScenario = 4: een gebeurtenis verwijderen van een Tenant in de herstel regio.

3. Selecteer F5 om het script uit te voeren.

4. Vernieuw de pagina van de contoso hal voor concert ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net/contosoconcerthall) en u ziet dat de gebeurtenis ernstig Strauss ontbreekt.

Op dit punt in de zelf studie hebt u de toepassing hersteld die nu wordt uitgevoerd in de herstel regio. U hebt een nieuwe Tenant ingericht in de herstel regio en de gewijzigde gegevens van een van de herstelde tenants.  

> [!NOTE]
> Andere zelf studies in het voor beeld zijn niet ontworpen om te worden uitgevoerd met de app in de herstel status. Als u andere zelf studies wilt verkennen, moet u de toepassing eerst berepatriëren.

## <a name="repatriation-process-overview"></a>Overzicht van het planningsproces

Het planningsproces herstelt de toepassing en de bijbehorende data bases naar de oorspronkelijke regio nadat een storing is opgelost.

![Geo-herstel-repatriëring](./media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png)   

Het proces:

1. Hiermee stopt u de actieve terugzet activiteit en worden alle openstaande of in de vlucht data base terugzet aanvragen geannuleerd.

2. Hiermee wordt opnieuw geactiveerd in de oorspronkelijke regionale Tenant-data bases die niet zijn gewijzigd sinds de onderbreking. Deze data bases bevatten de bestanden die nog niet zijn hersteld en die daarna niet meer worden gewijzigd. De opnieuw geactiveerde data bases zijn precies net als laatst geopend door hun tenants.

3. Richt een spiegel beeld in van de server van de nieuwe Tenant en de elastische pool in de oorspronkelijke regio. Nadat deze actie is voltooid, wordt de nieuwe Tenant alias bijgewerkt om naar deze server te verwijzen. Als u de alias bijwerkt, wordt de nieuwe Tenant onboarding uitgevoerd in de oorspronkelijke regio in plaats van de herstel regio.

3. Maakt gebruik van geo-replicatie om de catalogus te verplaatsen naar de oorspronkelijke regio vanuit de herstel regio.

4. Hiermee wordt de groeps configuratie in de oorspronkelijke regio bijgewerkt, zodat deze consistent is met de wijzigingen die zijn aangebracht in de herstel regio tijdens de onderbreking.

5. Maakt de vereiste servers en Pools voor het hosten van nieuwe data bases die zijn gemaakt tijdens de onderbreking.

6. Maakt gebruik van geo-replicatie om teruggezette Tenant-data bases te repatriëren die na het herstellen zijn bijgewerkt en alle nieuwe Tenant databases die tijdens de storing zijn ingericht. 

7. Hiermee worden resources opgeschoond die zijn gemaakt in de herstel regio tijdens het herstel proces.

Als u het aantal Tenant databases wilt beperken dat moet worden gerepatriërd, worden de stappen 1 tot en met 3 onmiddellijk uitgevoerd.  

Stap 4 wordt alleen uitgevoerd als de catalogus in de herstel regio tijdens de onderbreking is gewijzigd. De catalogus wordt bijgewerkt als er nieuwe tenants worden gemaakt of als een Data Base of groeps configuratie is gewijzigd in de herstel regio.

Het is belang rijk dat stap 7 een minimale onderbreking van tenants veroorzaakt en dat er geen gegevens verloren gaan. Het proces maakt gebruik van geo-replicatie om dit doel te krijgen.

Voordat elke Data Base geo-repliceerbaar is, wordt de bijbehorende data base in de oorspronkelijke regio verwijderd. De data base in de herstel regio wordt vervolgens geo-gerepliceerd en er wordt een secundaire replica in de oorspronkelijke regio gemaakt. Nadat de replicatie is voltooid, wordt de Tenant offline in de catalogus gemarkeerd, waardoor de verbindingen met de data base in de herstel regio worden verbroken. Er wordt vervolgens een failover uitgevoerd voor de data base, waardoor alle in behandeling zijnde trans acties op de secundaire worden verwerkt zodat er geen gegevens verloren gaan. 

Bij een failover worden de database rollen omgekeerd. De secundaire in de oorspronkelijke regio wordt de primaire Data Base voor lezen en schrijven, en de data base in de herstel regio wordt een alleen-lezen-secundair. De Tenant vermelding in de catalogus wordt bijgewerkt om te verwijzen naar de data base in de oorspronkelijke regio en de Tenant is online gemarkeerd. Op dit moment is de repatriëring van de data base voltooid. 

Toepassingen moeten worden geschreven met logica voor opnieuw proberen om ervoor te zorgen dat ze automatisch opnieuw verbinding maken wanneer verbindingen worden verbroken. Wanneer ze de catalogus gebruiken om de nieuwe verbinding te brokeren, maken ze verbinding met de gerepatriërde data base in de oorspronkelijke regio. Hoewel de korte verbreking vaak niet wordt opgemerkt, kunt u ervoor kiezen om data bases buiten kantoor uren te repatriëren.

Nadat een Data Base is gerepatriërd, kan de secundaire data base in de herstel regio worden verwijderd. De data base in de oorspronkelijke regio vertrouwt vervolgens opnieuw op geo-herstel voor DR-beveiliging.

In stap 8 worden bronnen in de herstel regio, met inbegrip van de herstel servers en-groepen, verwijderd.

## <a name="run-the-repatriation-script"></a>Het repatriërings script uitvoeren
Stel dat de storing is opgelost en het repatriërings script uit te voeren.

Als u de zelf studie hebt gevolgd, wordt fabrikam Jazz Club en Dogwood Dojo in de oorspronkelijke regio onmiddellijk opnieuw geactiveerd door het script, omdat deze ongewijzigd zijn. Vervolgens worden de nieuwe Tenant, het Hawthorne hal en de concert zaal van Contoso hersteld, omdat deze is gewijzigd. Met het script wordt ook de catalogus gemaakt die is bijgewerkt toen Hawthorn Hall werd ingericht.
  
1. Controleer in de Power shell-ISE in het script. ..\Learning Modules\Business-continuïteit en nood Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 op dat het catalogus synchronisatie proces nog steeds wordt uitgevoerd in het Power shell-exemplaar. Als dat nodig is, start u deze opnieuw met de instelling:

    $DemoScenario = 1: begin met het synchroniseren van de configuratie gegevens van de Tenant server, de groep en de data base in de catalogus.

    Selecteer F5 om het script uit te voeren.

2.  Ga vervolgens als volgt te werk om het proces te starten:

    $DemoScenario = 5: de app in de oorspronkelijke regio berepatriëren.

    Selecteer F5 om het herstel script uit te voeren in een nieuw Power shell-venster. De repatriëring duurt enkele minuten en kan worden bewaakt in het Power shell-venster.

3. Wanneer het script wordt uitgevoerd, vernieuwt u de pagina Events hub ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net).

    U ziet dat alle tenants online zijn en toegankelijk zijn tijdens dit proces.

4. Selecteer de fabrikam Jazz-Club om deze te openen. Als u deze Tenant niet hebt gewijzigd, kunt u zien uit de voet tekst die de server al heeft teruggezet naar de oorspronkelijke server.

5. Open of vernieuw de pagina met de evenementen voor concert van contoso. U ziet dat de data base zich nog steeds op de herstel server bevindt, in de voet tekst. 

6. Vernieuw de pagina concert van Contoso hal als de bewerking is voltooid en u ziet dat de data base nu in uw oorspronkelijke regio is.

7. Vernieuw de gebeurtenissen hub opnieuw en open Hawthorn Hall. U ziet dat de data base zich ook in de oorspronkelijke regio bevindt. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Herstel regio resources opschonen na voltooiing
Nadat de repatriëring is voltooid, is het veilig om de resources in de herstel regio te verwijderen. 

> [!IMPORTANT]
> Verwijder deze resources onmiddellijk om alle facturering voor hen te stoppen.

Het herstel proces maakt alle herstel resources in een herstel resource groep. Het opschonings proces verwijdert deze resource groep en verwijdert alle verwijzingen naar de resources uit de catalogus. 

1. Stel in het Power shell-ISE in het script. ..\Learning Modules\Business continuïteit en ramp Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 het volgende in:
    
    $DemoScenario = 6: Verwijder verouderde resources uit de herstel regio.

2. Selecteer F5 om het script uit te voeren.

Nadat de scripts zijn opgeruimd, wordt de toepassing weer gestart. Op dit moment kunt u het script opnieuw uitvoeren of andere zelf studies proberen.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>De toepassing ontwerpen om ervoor te zorgen dat de app en de data base zich op dezelfde locatie bevinden 
De toepassing is ontworpen om altijd verbinding te maken met een exemplaar in dezelfde regio als de data base van de Tenant. Dit ontwerp vermindert de latentie tussen de toepassing en de data base. Deze optimalisatie gaat ervan uit dat de interactie van de app-naar-data base chattier is dan de interactie tussen de gebruiker en de app.  

Tenant-data bases kunnen gedurende enige tijd tijdens de repatriëring over het herstel en de oorspronkelijke regio's worden verspreid. Voor elke Data Base zoekt de app naar de regio waarin de data base zich bevindt door een DNS-zoek opdracht op de naam van de Tenant server uit te voeren. De server naam is een alias. De naam van de alias server bevat de naam van de regio. Als de toepassing zich niet in dezelfde regio bevindt als de-data base, wordt deze omgeleid naar het exemplaar in dezelfde regio als de-server. Omleiden naar het exemplaar in dezelfde regio als de data base, minimaliseert de latentie tussen de app en de data base.  

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:
> [!div class="checklist"]
> 
> * Gebruik de Tenant catalogus om regel matig vernieuwde configuratie gegevens op te slaan, zodat een installatie omgeving voor de gespiegelde afbeelding in een andere regio kan worden gemaakt.
> * Herstel data bases in de herstel regio met behulp van geo-Restore.
> * Werk de Tenant catalogus bij om de herstelde Tenant database locaties weer te geven. 
> * Gebruik een DNS-alias om ervoor te zorgen dat een toepassing gedurende zonder opnieuw configureren verbinding maakt met de Tenant catalogus.
> * Gebruik geo-replicatie om herstelde data bases te repatriëren in hun oorspronkelijke regio nadat een storing is opgelost.

Probeer het [nood herstel voor een multi tenant-SaaS-toepassing met behulp van de data base geo-Replication-](../../sql-database/saas-dbpertenant-dr-geo-replication.md) zelf studie voor meer informatie over het gebruik van geo-replicatie om de tijd te verkorten die nodig is om een grootschalige multi tenant-toepassing te herstellen.

## <a name="additional-resources"></a>Aanvullende bronnen

[Aanvullende zelf studies die voortbouwen op de Wingtip SaaS-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
