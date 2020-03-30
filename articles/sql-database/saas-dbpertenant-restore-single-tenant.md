---
title: Een database herstellen in een SaaS-app met meerdere tenant
description: Meer informatie over het herstellen van de SQL-database van één tenant nadat u per ongeluk gegevens hebt verwijderd
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 12/04/2018
ms.openlocfilehash: a54e8e5629f6f8ad688b6fe11bbf50fc038095bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818531"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Eén tenant herstellen met een SaaS-toepassing database per tenant

Het database-per-tenant-model maakt het eenvoudig om een enkele tenant te herstellen naar een eerder tijdstip zonder dat dit gevolgen heeft voor andere tenants.

In deze zelfstudie leert u twee gegevensherstelpatronen:

> [!div class="checklist"]
> * Een database herstellen in een parallelle database (naast elkaar).
> * Een database herstellen en de bestaande database vervangen.

|||
|:--|:--|
| Herstellen in een parallelle database | Dit patroon kan worden gebruikt voor taken zoals controle, controle en naleving, zodat een tenant zijn gegevens vanaf een eerder punt kan inspecteren. De huidige database van de huurder blijft online en ongewijzigd. |
| Herstellen op zijn plaats | Dit patroon wordt meestal gebruikt om een tenant te herstellen naar een eerder punt, nadat een tenant per ongeluk gegevens verwijdert of corrumpeert. De oorspronkelijke database wordt off-line gehaald en vervangen door de herstelde database. |
|||

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip SaaS-app is geïmplementeerd. Zie [De SaaS-toepassing Wingtip implementeren en verkennen](saas-dbpertenant-get-started-deploy.md)als u deze in minder dan vijf minuten wilt implementeren.
* Azure PowerShell is geïnstalleerd. Zie Aan [de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)voor meer informatie.

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Inleiding tot de SaaS-tenant herstelpatronen

Er zijn twee eenvoudige patronen voor het herstellen van de gegevens van een individuele tenant. Omdat tenantdatabases van elkaar zijn geïsoleerd, heeft het herstellen van één tenant geen invloed op de gegevens van een andere tenant. De PITR-functie (Point-in-time-restore) van Azure SQL Database wordt in beide patronen gebruikt. PITR creëert altijd een nieuwe database.

* **Herstellen in parallel:** In het eerste patroon wordt een nieuwe parallelle database gemaakt naast de huidige database van de tenant. De tenant krijgt dan alleen-lezen toegang tot de herstelde database. De herstelde gegevens kunnen worden gecontroleerd en mogelijk worden gebruikt om huidige gegevenswaarden te overschrijven. Het is aan de app-ontwerper om te bepalen hoe de tenant toegang krijgt tot de herstelde database en welke opties voor herstel worden geboden. Gewoon toestaan dat de huurder om hun gegevens te controleren op een eerder punt kan alles zijn wat nodig is in sommige scenario's.

* **Herstellen op**zijn plaats : Het tweede patroon is handig als gegevens verloren of beschadigd zijn en de tenant wil terugkeren naar een eerder punt. De tenant wordt off-line gehaald terwijl de database wordt hersteld. De oorspronkelijke database wordt verwijderd en de herstelde database wordt hernoemd. De back-upketen van de oorspronkelijke database blijft toegankelijk na het verwijderen, zodat u de database zo nodig naar een eerder tijdstip herstellen.

Als de database [actieve georeplicatie](sql-database-active-geo-replication.md) gebruikt en parallel herstelt, raden we u aan alle vereiste gegevens uit de herstelde kopie naar de oorspronkelijke database te kopiëren. Als u de oorspronkelijke database vervangt door de herstelde database, moet u georeplicatie opnieuw configureren en opnieuw synchroniseren.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Download de Wingtip Tickets SaaS database-per-tenant applicatiescripts

De Wingtip Tickets SaaS Multitenant Database scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren, raadpleegt u de [algemene richtlijnen.](saas-tenancy-wingtip-app-guidance-tips.md)

## <a name="before-you-start"></a>Voordat u begint

Wanneer een database wordt gemaakt, kan het 10 tot 15 minuten duren voordat de eerste volledige back-up beschikbaar is om te herstellen. Als u de toepassing net hebt geïnstalleerd, moet u mogelijk een paar minuten wachten voordat u dit scenario probeert.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Een tenant simuleren die per ongeluk gegevens verwijderde

Als u deze herstelscenario's wilt demonstreren, verwijdert u eerst een gebeurtenis in een van de tenantdatabases. 

### <a name="open-the-events-app-to-review-the-current-events"></a>De evenementen-app openen om de huidige gebeurtenissen te bekijken

1. Open de Evenementenhubhttp://events.wtp.&lt&gt;( ;gebruiker .trafficmanager.net) en selecteer **Contoso Concert Hall**.

   ![Evenementenhub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Blader door de lijst met gebeurtenissen en noteer de laatste gebeurtenis in de lijst.

   ![Laatste gebeurtenis wordt weergegeven](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>'Per ongeluk' de laatste gebeurtenis verwijderen

1. Open in de PowerShell ISE ... \\Learning\\Modules Business Continuity\\and\\Disaster Recovery RestoreTenant*Demo-RestoreTenant.ps1*, en stel de volgende waarde in:

   * **$DemoScenario** = **1**, Laatste evenement *verwijderen (zonder kaartverkoop)*.
2. Druk op F5 om het script uit te voeren en de laatste gebeurtenis te verwijderen. Het volgende bevestigingsbericht wordt weergegeven:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. De pagina Gebeurtenissen van Contoso wordt geopend. Scroll naar beneden en controleer of de gebeurtenis is verdwenen. Als de gebeurtenis nog steeds in de lijst staat, selecteert u **Vernieuwen** en controleert u of deze is verdwenen.
   ![Laatste gebeurtenis verwijderd](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Een tenantdatabase herstellen parallel met de productiedatabase

Deze oefening herstelt de Contoso Concert Hall database tot een punt in de tijd voordat het evenement werd verwijderd. In dit scenario wordt ervan uitgegaan dat u de verwijderde gegevens in een parallelle database wilt controleren.

 Het *script Restore-TenantInParallel.ps1* maakt een parallelle tenantdatabase met de naam *ContosoConcertHall\_old*, met een parallelle catalogusvermelding. Dit patroon van herstel is het meest geschikt voor het herstellen van een klein gegevensverlies. U dit patroon ook gebruiken als u gegevens moet controleren voor nalevings- of controledoeleinden. Het is de aanbevolen aanpak wanneer u [actieve georeplicatie](sql-database-active-geo-replication.md)gebruikt.

1. Voltooi de sectie Een tenant simuleren die [per ongeluk gegevens verwijderde.](#simulate-a-tenant-accidentally-deleting-data)
2. Open in de PowerShell ISE ... \\Leermodules\\Bedrijfscontinuïteit en\\disaster\\recovery restoretenant_demo-restoretenant.ps1_.
3. Stel **$DemoScenario** = **2**, Tenant *herstellen parallel*.
4. Als u het script wilt uitvoeren, drukt u op F5.

Het script herstelt de tenantdatabase naar een tijdstip voordat u de gebeurtenis verwijderde. De database wordt hersteld naar een nieuwe database genaamd _\_ContosoConcertHall oude_. De catalogusmetagegevens die in deze herstelde database aanwezig zijn, worden verwijderd en vervolgens wordt de database aan de catalogus toegevoegd met behulp van een sleutel die is gemaakt van de oude naam *ContosoConcertHall.\_*

Het demoscript opent de evenementenpagina voor deze nieuwe tenantdatabase in uw browser. Opmerking van ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` de URL dat deze pagina gegevens uit de herstelde database weergeeft waar *_old* aan de naam wordt toegevoegd.

Blader door de gebeurtenissen in de browser om te bevestigen dat de gebeurtenis die in de vorige sectie is verwijderd, is hersteld.

Het is onwaarschijnlijk dat het blootstellen van de herstelde tenant als een extra tenant, met een eigen gebeurtenissen-app, is hoe u een tenant toegang geeft tot herstelde gegevens. Het dient om het herstelpatroon te illustreren. Doorgaans geeft u alleen-lezen toegang tot de oude gegevens en behoudt u de herstelde database voor een bepaalde periode. In het voorbeeld u de herstelde tenantvermelding verwijderen nadat u klaar bent met het scenario _van herstelde tenant verwijderen._

1. Stel **$DemoScenario** = **4**in, *Herstelde tenant verwijderen*.
2. Als u het script wilt uitvoeren, drukt u op F5.
3. De *\_ContosoConcertHall oude* vermelding is nu verwijderd uit de catalogus. Sluit de pagina gebeurtenissen voor deze tenant in uw browser.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Een tenant herstellen en de bestaande tenantdatabase vervangen

Deze oefening herstelt de Contoso Concert Hall huurder tot een punt voordat het evenement werd verwijderd. Met het script *Restore-TenantInPlace* wordt een tenantdatabase hersteld naar een nieuwe database en wordt het origineel verwijderd. Dit herstelpatroon is het meest geschikt om te herstellen van ernstige gegevensbeschadiging en de tenant moet mogelijk aanzienlijk gegevensverlies opvangen.

1. Open in de PowerShell ISE het **bestand Demo-RestoreTenant.ps1.**
2. Stel **$DemoScenario** = **5**, Herstel huurder op *zijn plaats*.
3. Als u het script wilt uitvoeren, drukt u op F5.

Het script herstelt de tenantdatabase naar een punt voordat de gebeurtenis werd verwijderd. Het neemt eerst de Contoso Concert Hall huurder off-line om verdere updates te voorkomen. Vervolgens wordt een parallelle database gemaakt door te herstellen vanaf het herstelpunt. De herstelde database wordt benoemd met een tijdstempel om ervoor te zorgen dat de databasenaam niet in strijd is met de bestaande tenantdatabasenaam. Vervolgens wordt de oude tenantdatabase verwijderd en wordt de herstelde database omgedoopt tot de oorspronkelijke databasenaam. Tot slot wordt Contoso Concert Hall online gebracht om de app toegang te geven tot de herstelde database.

U hebt de database hersteld naar een tijdstip voordat de gebeurtenis werd verwijderd. Controleer bij het openen van de pagina **Gebeurtenissen** of de laatste gebeurtenis is hersteld.

Nadat u de database hebt hersteld, duurt het nog 10 tot 15 minuten voordat de eerste volledige back-up beschikbaar is om opnieuw te herstellen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een database herstellen in een parallelle database (naast elkaar).
> * Een database herstellen.

Probeer de zelfstudie van het [tenantdatabaseschema beheren.](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende tutorials die voortbouwen op de Wingtip SaaS-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Overzicht van bedrijfscontinuïteit met Azure SQL Database](sql-database-business-continuity.md)
* [Meer informatie over SQL Database-back-ups](sql-database-automated-backups.md)
