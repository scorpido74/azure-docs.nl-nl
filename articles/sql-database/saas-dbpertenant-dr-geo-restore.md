---
title: 'SaaS-apps: Geo-redundante back-ups voor herstel na noodgevallen'
description: Meer informatie over het gebruik van georedundante back-ups van Azure SQL Database om een Multitenant SaaS-app te herstellen in geval van een storing
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/14/2019
ms.openlocfilehash: 270fc157fa14efa19ed30d35b614fb769804b72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826462"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Geo-herstel gebruiken om een Multitenant SaaS-toepassing te herstellen uit databaseback-ups

In deze zelfstudie wordt een volledig scenario voor noodherstel onderzocht voor een SaaS-toepassing met meerdere tenant's die zijn geïmplementeerd met de database per tenantmodel. U gebruikt [geo-restore](sql-database-recovery-using-backups.md) om de catalogus- en tenantdatabases te herstellen van automatisch onderhouden georedundante back-ups in een alternatief herstelgebied. Nadat de storing is opgelost, gebruikt u [geo-replicatie](sql-database-geo-replication-overview.md) om gewijzigde databases te repatriëren naar hun oorspronkelijke regio.

![Geo-restore-architectuur](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geo-restore is de goedkoopste oplossing voor noodherstel voor Azure SQL Database. Het herstellen van georedundante back-ups kan echter leiden tot gegevensverlies van maximaal een uur. Het kan veel tijd in beslag nemen, afhankelijk van de grootte van elke database. 

> [!NOTE]
> Herstel toepassingen met de laagst mogelijke RPO en RTO met behulp van geo-replicatie in plaats van geo-restore.

Deze zelfstudie verkent zowel herstel- als repatriëringsworkflows. Procedures voor:
> [!div class="checklist"]
> 
> * Database- en elastische groepconfiguratiegegevens synchroniseren in de tenantcatalogus.
> * Stel een spiegelbeeldomgeving in in een herstelgebied met toepassing, servers en pools.   
> * Herstel catalogus- en tenantdatabases met behulp van geo-herstel.
> * Gebruik georeplicatie om de tenantcatalogus te repatriëren en tenantdatabases te wijzigen nadat de storing is opgelost.
> * Werk de catalogus bij wanneer elke database wordt hersteld (of gerepatrieerd) om de huidige locatie van de actieve kopie van de database van elke tenant bij te houden.
> * Zorg ervoor dat de toepassings- en tenantdatabase altijd in hetzelfde Azure-gebied is gevestigd om de latentie te verminderen. 
 

Voer de volgende voorwaarden in voordat u deze zelfstudie start:
* Implementeer de Wingtip Tickets SaaS-database per tenant-app. Zie [De SaaS-database van Wingtip Tickets implementeren en verkennen per tenanttoepassing](saas-dbpertenant-get-started-deploy.md)om in minder dan vijf minuten te implementeren. 
* Installeer Azure PowerShell. Zie Aan [de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)voor meer informatie.

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Inleiding tot het geo-herstelherstelpatroon

Disaster recovery (DR) is een belangrijke overweging voor veel toepassingen, of het nu om nalevingsredenen of om bedrijfscontinuïteit is. Als er een langdurige servicestoring optreedt, kan een goed voorbereid DR-plan de bedrijfsonderbreking minimaliseren. Een DR-plan op basis van geo-restore moet verschillende doelen bereiken:
 * Reserveer alle benodigde capaciteit in het gekozen herstelgebied zo snel mogelijk om ervoor te zorgen dat deze beschikbaar is om tenantdatabases te herstellen.
 * Stel een herstelomgeving voor spiegelbeelden in die de oorspronkelijke pool- en databaseconfiguratie weergeeft. 
 * Laat annulering van het herstelproces halverwege de vlucht toe als de oorspronkelijke regio weer online komt.
 * Maak tenantprovisioning snel inschakelen, zodat nieuwe tenant onboarding zo snel mogelijk opnieuw kan worden opgestart.
 * Worden geoptimaliseerd om huurders in prioriteitsvolgorde te herstellen.
 * Worden geoptimaliseerd om huurders online te krijgen zo spoedig mogelijk door het doen van stappen in parallel waar praktisch.
 * Wees bestand tegen falen, opnieuw op te starten en idempotent.
 * Repatriëren databases naar hun oorspronkelijke regio met minimale impact op huurders wanneer de storing is opgelost.  

> [!NOTE]
> De toepassing wordt hersteld in het gekoppelde gebied van het gebied waarin de toepassing is geïmplementeerd. Zie [Azure-gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie .   

In deze zelfstudie worden functies van Azure SQL Database en het Azure-platform gebruikt om deze uitdagingen aan te pakken:

* [Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), om alle benodigde capaciteit zo snel mogelijk te reserveren. Azure Resource Manager-sjablonen worden gebruikt om een spiegelafbeelding van de oorspronkelijke servers en elastische pools in het herstelgebied in te richten. Er wordt ook een aparte server en pool gemaakt voor het inrichten van nieuwe tenants.
* [Elastic Database Client Library](sql-database-elastic-database-client-library.md) (EDCL) om een tenantdatabasecatalogus te maken en bij te houden. De uitgebreide catalogus bevat periodiek vernieuwde informatie over de pool- en databaseconfiguratie.
* [Shard management recovery features](sql-database-elastic-database-recovery-manager.md) of the EDCL, to maintain database location items in the catalog during recovery and repatrre.  
* [Geo-restore](sql-database-disaster-recovery.md), om de catalogus en tenant databases te herstellen van automatisch onderhouden geo-redundante back-ups. 
* [Asynchrone herstelbewerkingen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), verzonden in volgorde met tenantprioriteit, worden voor elke groep door het systeem in de wachtrij geplaatst en in batches verwerkt, zodat de groep niet overbelast raakt. Deze bewerkingen kunnen indien nodig voor of tijdens de uitvoering worden geannuleerd.   
* [Geo-replicatie](sql-database-geo-replication-overview.md), om databases te repatriëren naar de oorspronkelijke regio na de storing. Er is geen gegevensverlies en minimale impact op de tenant wanneer u geo-replicatie gebruikt.
* [DNS-aliassen van SQL-server](dns-alias-overview.md), zodat het synchronisatieproces van de catalogus verbinding kan maken met de actieve catalogus, ongeacht de locatie.  

## <a name="get-the-disaster-recovery-scripts"></a>Download de scripts voor noodherstel

De DR-scripts die in deze zelfstudie worden gebruikt, zijn beschikbaar in de [Wingtip Tickets SaaS-database per tenant GitHub-repository.](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets management scripts te downloaden en te deblokkeren.

> [!IMPORTANT]
> Net als alle Wingtip Tickets management scripts, de DR scripts zijn monster kwaliteit en zijn niet te gebruiken in de productie.

## <a name="review-the-healthy-state-of-the-application"></a>De gezonde status van de toepassing bekijken
Voordat u het herstelproces start, controleert u de normale gezonde status van de toepassing.

1. Open in uw webbrowser de hub voorhttp://events.wingtip-dpt.&ltgebeurtenissen&gt;van Wingtip Tickets ( ;gebruiker .trafficmanager.net, vervang &lt;de gebruiker&gt; door de gebruikerswaarde van uw implementatie).
    
   Schuif naar de onderkant van de pagina en zie de naam en locatie van de catalogusserver in de voettekst. De locatie is de regio waarin u de app hebt geïmplementeerd.    

   > [!TIP]
   > Plaats de muis boven de locatie om het scherm te vergroten.

   ![Evenementen hub gezonde staat in de oorspronkelijke regio](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Selecteer de huurder van de Contoso Concert Hall en open de evenementpagina.

   Let in de voettekst op de servernaam van de tenant. De locatie is hetzelfde als de locatie van de catalogusserver.

   ![Contoso Concert Hall originele regio](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. Bekijk en open in de [Azure-portal](https://portal.azure.com)de brongroep waarin u de app hebt geïmplementeerd.

   Let op de resources en het gebied waarin de app-servicecomponenten en SQL Database-servers worden geïmplementeerd.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>De tenantconfiguratie synchroniseren met de catalogus

In deze taak start u een proces om de configuratie van de servers, elastische pools en databases te synchroniseren met de tenantcatalogus. Deze informatie wordt later gebruikt om een spiegelbeeldomgeving in het herstelgebied te configureren.

> [!IMPORTANT]
> Voor de eenvoud worden het synchronisatieproces en andere langlopende herstel- en repatriëringsprocessen in deze voorbeelden geïmplementeerd als lokale PowerShell-taken of -sessies die worden uitgevoerd onder het inloggen van uw clientgebruiker. De verificatietokens die worden uitgegeven wanneer u zich aanmeldt, verlopen na enkele uren en de taken mislukken dan. In een productiescenario moeten langlopende processen worden geïmplementeerd als betrouwbare Azure-services, uitgevoerd onder een serviceprincipal. Zie [Azure PowerShell gebruiken om een serviceprincipal te maken met een certificaat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. Open in het PowerShell ISE het bestand ...\Learning Modules\UserConfig.psm1. Vervang `<resourcegroup>` `<user>` en op lijnen 10 en 11 met de waarde die wordt gebruikt toen u de app hebt geïmplementeerd. Sla het bestand op.

2. Open in het PowerShell ISE het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.

    In deze zelfstudie voert u elk van de scenario's in dit PowerShell-script uit, dus houd dit bestand open.

3. Stel het volgende in:

    $DemoScenario = 1: Start een achtergrondtaak die tenantserver synchroniseert en configuratiegegevens groepeert in de catalogus.

4. Als u het synchronisatiescript wilt uitvoeren, selecteert u F5. 

    Deze informatie wordt later gebruikt om ervoor te zorgen dat herstel een spiegelbeeld maakt van de servers, pools en databases in het herstelgebied.  

    ![Synchronisatieproces](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Laat het PowerShell-venster op de achtergrond draaien en ga verder met de rest van deze zelfstudie.

> [!NOTE]
> Het synchronisatieproces maakt verbinding met de catalogus via een DNS-alias. De alias wordt gewijzigd tijdens herstel en repatriëring om naar de actieve catalogus te wijzen. Het synchronisatieproces houdt de catalogus up-to-date met eventuele database- of poolconfiguratiewijzigingen die in het herstelgebied zijn aangebracht. Tijdens de repatriëring worden deze wijzigingen toegepast op de equivalente middelen in de oorspronkelijke regio.

## <a name="geo-restore-recovery-process-overview"></a>Overzicht van herstelherstelprocessen

Het herstelproces voor geoherstel implementeert de toepassing en herstelt databases van back-ups naar het herstelgebied.

Het herstelproces doet het volgende:

1. Hiermee schakelt u het eindpunt Azure Traffic Manager voor de web-app in de oorspronkelijke regio uit. Als u het eindpunt uitschakelt, kunnen gebruikers geen verbinding maken met de app in een ongeldige status als de oorspronkelijke regio online komt tijdens het herstel.

2. Voorziet in een herstelcatalogusserver in het herstelgebied, geo-herstelt de catalogusdatabase en werkt de activecatalog-alias bij om naar de herstelde catalogusserver te wijzen. Als u de catalogusalias wijzigt, wordt het synchronisatieproces van de catalogus altijd gesynchroniseerd met de actieve catalogus.

3. Hiermee markeert u alle bestaande tenants in de herstelcatalogus als offline om toegang tot tenantdatabases te voorkomen voordat ze worden hersteld.

4. Hiermee wordt een instantie van de app in het herstelgebied ingericht en geconfigureerd om de herstelde catalogus in dat gebied te gebruiken. Om de latentie tot een minimum te beperken, is de voorbeeld-app ontworpen om altijd verbinding te maken met een tenantdatabase in dezelfde regio.

5. Voorziet in een server en een elastische pool waarin nieuwe tenants zijn ingericht. Het maken van deze resources zorgt ervoor dat het inrichten van nieuwe tenants niet interfereert met het herstel van bestaande tenants.

6. Hiermee wordt de nieuwe tenantalias bijgewerkt om naar de server te wijzen voor nieuwe tenantdatabases in het herstelgebied. Als u deze alias wijzigt, zorgt u ervoor dat databases voor nieuwe tenants worden ingericht in het herstelgebied.
        
7. Invoorzieningenservers en elastische pools in het herstelgebied voor het herstellen van tenantdatabases. Deze servers en pools zijn een spiegelbeeld van de configuratie in de oorspronkelijke regio. Het vooraf inrichten van pools behoudt de capaciteit die nodig is om alle databases te herstellen.

    Een storing in een regio kan aanzienlijke druk uitoefenen op de beschikbare middelen in de gekoppelde regio. Als u afhankelijk bent van geo-herstel voor DR, wordt het raadzaam resources snel te reserveren. Overweeg georeplicatie als het van cruciaal belang is dat een toepassing in een bepaald gebied wordt hersteld. 

8. Hiermee schakelt u het eindpunt Verkeerbeheer in voor de web-app in het herstelgebied. Als u dit eindpunt inschakelt, kan de toepassing nieuwe tenants inrichten. In dit stadium zijn bestaande tenants nog offline.

9. Hiermee worden batches van aanvragen ingediend om databases in prioriteitsvolgorde te herstellen. 

    * Batches worden zo georganiseerd dat databases parallel worden hersteld in alle groepen.  

    * Herstelaanvragen worden asynchroon ingediend, zodat ze snel worden ingediend en in de wachtrij worden geplaatst voor uitvoering in elke groep.

    * Omdat herstelaanvragen parallel over alle zwembaden worden verwerkt, is het beter om belangrijke tenants over veel zwembaden te verdelen. 

10. Controleert de SQL Database-service om te bepalen wanneer databases worden hersteld. Nadat een tenantdatabase is hersteld, wordt deze online gemarkeerd in de catalogus en wordt een rijversiesom voor de tenantdatabase geregistreerd. 

    * Tenantdatabases kunnen worden geopend door de toepassing zodra ze online zijn gemarkeerd in de catalogus.

    * Een som van de rijversiewaarden in de tenantdatabase wordt opgeslagen in de catalogus. Deze som fungeert als een vingerafdruk waarmee het repatriëringsproces kan bepalen of de database is bijgewerkt in het herstelgebied.       

## <a name="run-the-recovery-script"></a>Het herstelscript uitvoeren

> [!IMPORTANT]
> Deze zelfstudie herstelt databases van georedundante back-ups. Hoewel deze back-ups doorgaans binnen 10 minuten beschikbaar zijn, kan het tot een uur duren. Het script wordt onderbroken totdat ze beschikbaar zijn.

Stelt u zich eens voor dat er een storing is in de regio waarin de toepassing is geïmplementeerd en voert u het herstelscript uit:

1. Stel in het PowerShell ISE in het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende waarde in:

    $DemoScenario = 2: Herstel de app in een herstelgebied door te herstellen van georedundante back-ups.

2. Als u het script wilt uitvoeren, selecteert u F5.  

    * Het script wordt geopend in een nieuw PowerShell-venster en start vervolgens een set PowerShell-taken die parallel worden uitgevoerd. Deze taken herstellen servers, pools en databases naar het herstelgebied.

    * Het herstelgebied is het gekoppelde gebied dat is gekoppeld aan het Azure-gebied waarin u de toepassing hebt geïmplementeerd. Zie [Azure-gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie . 

3. Controleer de status van het herstelproces in het PowerShell-venster.

    ![Herstelproces](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Als u de code voor de hersteltaken wilt verkennen, bekijkt u de PowerShell-scripts in de map ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>De status van de aanvraag controleren tijdens het herstel
Hoewel het eindpunt van de toepassing is uitgeschakeld in Traffic Manager, is de toepassing niet beschikbaar. De catalogus is hersteld en alle tenants zijn offline gemarkeerd. Het toepassingseindpunt in het herstelgebied wordt vervolgens ingeschakeld en de toepassing is weer online. Hoewel de toepassing beschikbaar is, worden tenants offline weergegeven in de gebeurtenishub totdat hun databases zijn hersteld. Het is belangrijk om uw toepassing te ontwerpen voor het verwerken van offline tenantdatabases.

* Nadat de catalogusdatabase is hersteld, maar voordat de huurders weer online zijn, vernieuwt u de evenementenhub Wingtip Tickets in uw webbrowser.

  * In de voettekst wordt opgemerkt dat de naam van de catalogusserver nu een herstelachtervoegsel heeft en zich in het herstelgebied bevindt.

  * Merk op dat tenants die nog niet zijn hersteld, zijn gemarkeerd als offline en niet selecteerbaar zijn.   
 
    ![Herstelproces](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Als u de gebeurtenissenpagina van een tenant rechtstreeks opent terwijl de tenant offline is, wordt op de pagina een offline melding van een tenant weergegeven. Als Contoso Concert Hall bijvoorbeeld offline is,&gt;probeert u de gebruiker .trafficmanager.net/contosoconcerthall te openen. http://events.wingtip-dpt.&lt

    ![Herstelproces](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Een nieuwe huurder in het herstelgebied voorzien
Nog voordat tenantdatabases zijn hersteld, u nieuwe tenants in de herstelregio inrichten. Nieuwe tenantdatabases die in het herstelgebied worden ingericht, worden later gerepatrieerd met de herstelde databases.   

1. Stel in het PowerShell ISE in het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende eigenschap in:

    $DemoScenario = 3: Een nieuwe huurder in het herstelgebied inrichten.

2. Als u het script wilt uitvoeren, selecteert u F5.

3. De pagina Gebeurtenissen in hawthorn hall wordt geopend in de browser wanneer de inrichting is voltooid. 

    Merk op dat de Hawthorn Hall-database zich in het herstelgebied bevindt.

    ![Hawthorn Hall ingericht in het herstelgebied](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. Vernieuw in de browser de hubpagina van Wingtip Tickets om Hawthorn Hall inbegrepen te zien. 

    Als u Hawthorn Hall ingericht zonder te wachten tot de andere huurders te herstellen, andere huurders kunnen nog steeds offline.

## <a name="review-the-recovered-state-of-the-application"></a>De herstelde status van de aanvraag controleren

Wanneer het herstelproces is voltooid, zijn de toepassing en alle tenants volledig functioneel in het herstelgebied. 

1. Nadat het scherm in het PowerShell-consolevenster aangeeft dat alle tenants zijn hersteld, vernieuwt u de gebeurtenishub. 

    De huurders verschijnen allemaal online, inclusief de nieuwe huurder, Hawthorn Hall.

    ![Herstelde en nieuwe huurders in de evenementenhub](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klik op Contoso Concert Hall en open de evenementenpagina. 

    In de voettekst wordt opgemerkt dat de database zich bevindt op de herstelserver in het herstelgebied.

    ![Contoso in het herstelgebied](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Open in de [Azure-portal](https://portal.azure.com)de lijst met brongroepen.  

    Let op de resourcegroep die u hebt geïmplementeerd, plus de herstelbrongroep, met het achtervoegsel -herstel. De herstelbrongroep bevat alle resources die tijdens het herstelproces zijn gemaakt, plus nieuwe bronnen die tijdens de storing zijn gemaakt. 

4. Open de herstelbrongroep en let op de volgende items:

   * De herstelversies van de catalogus en tenants1-servers, met het achtervoegsel -herstel. De herstelde catalogus en tenant databases op deze servers hebben allemaal de namen die worden gebruikt in de oorspronkelijke regio.

   * De&lt;tenants2-dpt-&gt;user-recovery SQL server. Deze server wordt gebruikt voor het inrichten van nieuwe tenants tijdens de storing.

   * De app-service noemde de&lt;gebruiker&gt;-&lt;&gt;events-wingtip-dpt-recoveryregion, het herstelexemplaar van de gebeurtenissen-app.

     ![Contoso-bronnen in het herstelgebied](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Open de SQL-server&lt;tenants2-dpt- user-recovery.&gt; Merk op dat het bevat de database hawthornhall en de elastische pool Pool1. De hawthornhall-database is geconfigureerd als een elastische database in de elastische pool Pool1.

## <a name="change-the-tenant-data"></a>De tenantgegevens wijzigen 
In deze taak werkt u een van de herstelde tenantdatabases bij. Het repatriëringsproces kopieert herstelde databases die zijn gewijzigd in de oorspronkelijke regio. 

1. Zoek in uw browser de evenementenlijst voor de Contoso Concert Hall, blader door de gebeurtenissen en let op de laatste gebeurtenis, Seriously Strauss.

2. Stel in het PowerShell ISE in het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 de volgende waarde in:

    $DemoScenario = 4: Een gebeurtenis verwijderen uit een tenant in het herstelgebied.

3. Als u het script wilt uitvoeren, selecteert u F5.

4. Vernieuw de evenementenpagina vanhttp://events.wingtip-dpt.&ltde&gt;Contoso Concert Hall ( ;gebruiker .trafficmanager.net/contosoconcerthall) en merk op dat het evenement Seriously Strauss ontbreekt.

Op dit punt in de zelfstudie hebt u de toepassing hersteld, die nu wordt uitgevoerd in het herstelgebied. U hebt een nieuwe tenant in het herstelgebied ingericht en gewijzigde gegevens van een van de herstelde tenants.  

> [!NOTE]
> Andere zelfstudies in het voorbeeld zijn niet ontworpen om met de app in de herstelstatus uit te voeren. Als u andere tutorials wilt verkennen, moet u de toepassing eerst repatriëren.

## <a name="repatriation-process-overview"></a>Overzicht van repatriëringsproces

Het repatriëringsproces keert de toepassing en de databases ervan terug naar de oorspronkelijke regio nadat een storing is opgelost.

![Geo-herstel repatriëring](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

Het proces:

1. Hiermee stopt u alle lopende herstelactiviteiten en annuleert u openstaande of in-flight database herstelverzoeken.

2. Reactiveert in de oorspronkelijke regio tenant databases die niet zijn gewijzigd sinds de storing. Deze databases bevatten die nog niet hersteld en die hersteld, maar niet daarna veranderd. De gereactiveerde databases zijn precies zo lang als de laatste toegankelijk is voor hun huurders.

3. Voorziet in een spiegelbeeld van de server van de nieuwe tenant en de elastische pool in de oorspronkelijke regio. Nadat deze actie is voltooid, wordt de nieuwe tenantalias bijgewerkt om naar deze server te wijzen. Als u de alias bijwerkt, vindt nieuwe tenantonboarding plaats in de oorspronkelijke regio in plaats van in het herstelgebied.

3. Gebruikt georeplicatie om de catalogus vanuit het herstelgebied naar het oorspronkelijke gebied te verplaatsen.

4. Hiermee wordt de poolconfiguratie in de oorspronkelijke regio bijgewerkt, zodat deze overeenkomt met wijzigingen die tijdens de storing in het herstelgebied zijn aangebracht.

5. Hiermee worden de vereiste servers en groepen gemaakt om nieuwe databases te hosten die tijdens de storing zijn gemaakt.

6. Gebruikt georeplicatie om herstelde tenantdatabases te repatriëren die zijn bijgewerkt na het herstellen en alle nieuwe tenantdatabases die tijdens de storing zijn ingericht. 

7. Hiermee worden resources die tijdens het herstelproces in het herstelgebied zijn gemaakt, opgeschoond.

Om het aantal tenantdatabases te beperken dat moet worden gerepatrieerd, worden de stappen 1 tot en met 3 snel uitgevoerd.  

Stap 4 wordt alleen gedaan als de catalogus in het herstelgebied is gewijzigd tijdens de storing. De catalogus wordt bijgewerkt als er nieuwe tenants worden gemaakt of als een database- of poolconfiguratie wordt gewijzigd in het herstelgebied.

Het is belangrijk dat stap 7 zorgt voor minimale verstoring van huurders en geen gegevens verloren gaat. Om dit doel te bereiken, maakt het proces gebruik van geo-replicatie.

Voordat elke database geo-gerepliceerd is, wordt de bijbehorende database in het oorspronkelijke gebied verwijderd. De database in het herstelgebied wordt vervolgens geo-gerepliceerd, waardoor een secundaire replica in het oorspronkelijke gebied wordt gemaakt. Nadat de replicatie is voltooid, wordt de tenant offline gemarkeerd in de catalogus, waardoor alle verbindingen met de database in het herstelgebied worden verbroken. De database is dan mislukt, waardoor transacties in behandeling worden verwerkt op de secundaire, zodat er geen gegevens verloren gaan. 

Bij failover worden de databaserollen omgedraaid. Het secundaire in het oorspronkelijke gebied wordt de primaire lees-schrijfdatabase en de database in het herstelgebied wordt een alleen-lezen secundaire. De tenantvermelding in de catalogus wordt bijgewerkt om naar de database in de oorspronkelijke regio te verwijzen en de tenant is online gemarkeerd. Op dit moment is de repatriëring van de database voltooid. 

Toepassingen moeten worden geschreven met een logica voor nieuwe apparaten om ervoor te zorgen dat ze automatisch opnieuw verbinding maken wanneer verbindingen worden verbroken. Wanneer ze de catalogus gebruiken om de heraansluiting te bemiddelen, maken ze verbinding met de gerepatrieerde database in de oorspronkelijke regio. Hoewel de korte ontkoppeling vaak niet wordt opgemerkt, u ervoor kiezen om databases buiten kantooruren te repatriëren.

Nadat een database is gerepatrieerd, kan de secundaire database in het herstelgebied worden verwijderd. De database in de oorspronkelijke regio is vervolgens opnieuw afhankelijk van geo-restore voor DR-bescherming.

In stap 8 worden resources in het herstelgebied, inclusief de herstelservers en -groepen, verwijderd.

## <a name="run-the-repatriation-script"></a>Het repatriëringsscript uitvoeren
Stel je voor dat de storing is opgelost en voer het repatriëringsscript uit.

Als je de tutorial hebt gevolgd, activeert het script meteen Fabrikam Jazz Club en Dogwood Dojo in de oorspronkelijke regio omdat ze ongewijzigd zijn. Het repatrieert dan de nieuwe huurder, Hawthorn Hall, en Contoso Concert Hall, omdat het is gewijzigd. Het script repatrieert ook de catalogus, die werd bijgewerkt toen Hawthorn Hall werd ingericht.
  
1. Controleer in het PowerShell ISE-script of het synchronisatieproces van de catalogus nog steeds wordt uitgevoerd in het PowerShell-exemplaar. Start het zo nodig opnieuw op door het instellen van:

    $DemoScenario = 1: Begin met het synchroniseren van tenantserver-, pool- en databaseconfiguratiegegevens in de catalogus.

    Als u het script wilt uitvoeren, selecteert u F5.

2.  Dan om het repatriëringsproces te starten, stel je in:

    $DemoScenario = 5: De app repatriëren in de oorspronkelijke regio.

    Als u het herstelscript wilt uitvoeren in een nieuw PowerShell-venster, selecteert u F5. Repatriëring duurt enkele minuten en kan worden gecontroleerd in het PowerShell-venster.

3. Terwijl het script wordt uitgevoerd, vernieuwt&gt;u de pagina gebeurtenissenhub (;gebruikerhttp://events.wingtip-dpt.&lt.trafficmanager.net).

    Merk op dat alle huurders online en toegankelijk zijn gedurende dit proces.

4. Selecteer de Fabrikam Jazz Club om deze te openen. Als u deze tenant niet hebt gewijzigd, ziet u vanaf de voettekst dat de server al is teruggezet naar de oorspronkelijke server.

5. Open of vernieuw de evenementenpagina van de Contoso Concert Hall. Bericht van de voettekst dat de database in eerste instantie nog steeds op de herstelserver staat. 

6. Vernieuw de evenementenpagina van de Contoso Concert Hall wanneer het repatriëringsproces is afgelopen en zorg ervoor dat de database zich nu in uw oorspronkelijke regio bevindt.

7. Vernieuw de evenementenhub opnieuw en open Hawthorn Hall. Merk op dat de database zich ook in de oorspronkelijke regio bevindt. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Opruimen herstel regio middelen na repatriëring
Nadat de repatriëring is voltooid, is het veilig om de resources in het herstelgebied te verwijderen. 

> [!IMPORTANT]
> Verwijder deze bronnen onmiddellijk om alle facturering voor hen te stoppen.

Met het herstelproces worden alle herstelbronnen in een herstelbrongroep gemaakt. Met het opschoningsproces worden deze brongroep verwijderd en worden alle verwijzingen naar de bronnen uit de catalogus verwijderd. 

1. Stel in het PowerShell ISE-script het script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1:
    
    $DemoScenario = 6: Verwijder verouderde bronnen uit het herstelgebied.

2. Als u het script wilt uitvoeren, selecteert u F5.

Na het opruimen van de scripts, de toepassing is terug waar het begon. Op dit punt u het script opnieuw uitvoeren of andere zelfstudies uitproberen.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Het ontwerpen van de toepassing om ervoor te zorgen dat de app en de database zich naast elkaar bevinden 
De toepassing is ontworpen om altijd verbinding te maken vanuit een instantie in dezelfde regio als de database van de tenant. Dit ontwerp vermindert de latentie tussen de toepassing en de database. Deze optimalisatie gaat ervan uit dat de app-naar-database-interactie chater is dan de interactie tussen gebruiker en app.  

Tenantdatabases kunnen tijdens de repatriëring enige tijd worden verspreid over herstel- en oorspronkelijke regio's. Voor elke database zoekt de app het gebied op waarin de database zich bevindt door een DNS-lookup uit te doen op de naam van de tenantserver. In SQL Database is de servernaam een alias. De naam van de aliasserver bevat de regionaam. Als de toepassing zich niet in hetzelfde gebied bevindt als de database, wordt deze doorgestuurd naar de instantie in hetzelfde gebied als de databaseserver. Doorleiden naar de instantie in hetzelfde gebied als de database minimaliseert de latentie tussen de app en de database.  

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> 
> * Gebruik de tenantcatalogus om periodiek vernieuwde configuratie-informatie vast te houden, waardoor een herstelomgeving voor spiegelbeelden kan worden gemaakt in een andere regio.
> * Herstel Azure SQL-databases in het herstelgebied met behulp van geo-herstel.
> * Werk de tenantcatalogus bij om herstelde tenantdatabaselocaties weer te geven. 
> * Gebruik een DNS-alias om een toepassing in staat te stellen verbinding te maken met de tenantcatalogus zonder opnieuw te configureren.
> * Gebruik georeplicatie om herstelde databases te repatriëren naar hun oorspronkelijke regio nadat een storing is opgelost.

Probeer het [herstel van de ramp voor een multitenant SaaS-toepassing met behulp van database geo-replicatie](saas-dbpertenant-dr-geo-replication.md) tutorial om te leren hoe geo-replicatie te gebruiken om drastisch te verminderen de tijd die nodig is om een grootschalige multitenant-toepassing te herstellen.

## <a name="additional-resources"></a>Aanvullende bronnen

[Extra tutorials die voortbouwen op de Wingtip SaaS applicatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
