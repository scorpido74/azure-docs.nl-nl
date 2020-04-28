---
title: Een Data Base herstellen in een multi tenant SaaS-app
description: Meer informatie over het herstellen van de SQL database van één Tenant na het per ongeluk verwijderen van gegevens
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73818531"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Eén Tenant herstellen met een SaaS-toepassing met een Data Base per Tenant

Met het model data base-per-Tenant kunt u eenvoudig één Tenant herstellen naar een eerder tijdstip zonder dat dit van invloed is op andere tenants.

In deze zelf studie leert u twee gegevens herstel patronen:

> [!div class="checklist"]
> * Een Data Base herstellen naar een parallelle data base (naast elkaar).
> * Een Data Base op locatie herstellen, waarbij de bestaande data base wordt vervangen.

|||
|:--|:--|
| Herstellen naar een parallelle data base | Dit patroon kan worden gebruikt voor taken zoals beoordeling, controle en naleving zodat een Tenant hun gegevens vanaf een eerder punt kan inspecteren. De huidige data base van de Tenant blijft online en ongewijzigd. |
| Herstellen op locatie | Dit patroon wordt doorgaans gebruikt om een Tenant te herstellen naar een eerder tijdstip, nadat een Tenant gegevens per ongeluk heeft verwijderd of beschadigd. De oorspronkelijke Data Base wordt offline gezet en vervangen door de herstelde data base. |
|||

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip SaaS-app wordt geïmplementeerd. Zie [de Wingtip SaaS-toepassing implementeren en verkennen](saas-dbpertenant-get-started-deploy.md)om in minder dan vijf minuten te implementeren.
* Azure PowerShell is geïnstalleerd. Zie [aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)voor meer informatie.

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Inleiding tot de herstel patronen van de SaaS-Tenant

Er zijn twee eenvoudige patronen voor het herstellen van de gegevens van een individuele Tenant. Omdat de Tenant databases van elkaar zijn geïsoleerd, heeft het herstellen van één Tenant geen invloed op de gegevens van andere tenants. De Azure SQL Database PITR-functie (Point-in-time-Restore) wordt in beide patronen gebruikt. PITR maakt altijd een nieuwe data base.

* **Herstellen parallel**: in het eerste patroon wordt een nieuwe parallelle data base gemaakt naast de huidige data base van de Tenant. De Tenant krijgt vervolgens alleen-lezen toegang tot de herstelde data base. De herstelde gegevens kunnen worden gecontroleerd en mogelijk gebruikt voor het overschrijven van huidige gegevens waarden. Het is aan de ontwerp functie voor apps om te bepalen hoe de Tenant toegang heeft tot de herstelde data base en welke opties voor herstel worden weer gegeven. Als u de Tenant toestaat hun gegevens op een eerder punt te controleren, kan dit allemaal in sommige scenario's zijn.

* **Herstel op locatie**: het tweede patroon is nuttig als gegevens verloren zijn gegaan of beschadigd zijn en de Tenant wil terugkeren naar een eerder punt. De Tenant wordt uitgeschakeld tijdens het herstellen van de data base. De oorspronkelijke Data Base wordt verwijderd en de naam van de herstelde data base wordt gewijzigd. De back-upketen van de oorspronkelijke Data Base blijft toegankelijk na het verwijderen, zodat u de data base zo nodig kunt herstellen naar een eerder tijdstip.

Als de data base gebruikmaakt van [actieve geo-replicatie](sql-database-active-geo-replication.md) en parallel herstellen, raden we u aan om de vereiste gegevens van de herstelde kopie te kopiëren naar de oorspronkelijke data base. Als u de oorspronkelijke data base vervangt door de herstelde data base, moet u geo-replicatie opnieuw configureren en synchroniseren.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De scripts van de Wingtip tickets SaaS-data base-per-Tenant toepassing ophalen

De Wingtip tickets SaaS multi tenant-database scripts en toepassings bron code zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github opslag plaats. Raadpleeg de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md)voor de stappen voor het downloaden en blok keren van de Wingtip tickets SaaS-scripts.

## <a name="before-you-start"></a>Voordat u begint

Wanneer een Data Base wordt gemaakt, kan het 10 tot 15 minuten duren voordat de eerste volledige back-up beschikbaar is voor herstel van. Als u de toepassing zojuist hebt geïnstalleerd, moet u mogelijk enkele minuten wachten voordat u dit scenario probeert.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Een Tenant simuleren die per ongeluk gegevens verwijdert

Als u deze herstel scenario's wilt demonstreren, moet u eerst per ongeluk een gebeurtenis in een van de Tenant-data bases verwijderen. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Open de app gebeurtenissen om de huidige gebeurtenissen te controleren

1. Open de evenementen hub (http://events.wtp.&lt; user&gt;. Trafficmanager.net) en selecteer **Contoso concert hal**.

   ![Events hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Scroll de lijst met gebeurtenissen en noteer de laatste gebeurtenis in de lijst.

   ![Laatste gebeurtenis wordt weer gegeven](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>De laatste gebeurtenis onbedoeld verwijderen

1. Open in de Power shell-ISE... \\Leer modules\\bedrijfs continuïteit en herstel\\na nood\\gevallen RestoreTenant*demo-RestoreTenant. ps1*en stel de volgende waarde in:

   * **$DemoScenario** = **1**, *laatste gebeurtenis verwijderen (zonder ticket verkoop)*.
2. Druk op F5 om het script uit te voeren en de laatste gebeurtenis te verwijderen. Het volgende bevestigings bericht wordt weer gegeven:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. De pagina contoso-gebeurtenissen wordt geopend. Schuif omlaag en controleer of de gebeurtenis is verdwenen. Als de gebeurtenis zich nog in de lijst bevindt, selecteert u **vernieuwen** en controleert u of deze is verdwenen.
   ![Laatste gebeurtenis verwijderd](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Een Tenant database parallel herstellen met de productie database

Met deze oefening wordt de contoso hal-Data Base op een bepaald moment teruggezet voordat de gebeurtenis werd verwijderd. In dit scenario wordt ervan uitgegaan dat u de verwijderde gegevens in een parallelle Data Base wilt controleren.

 Met het script *Restore-TenantInParallel. ps1* maakt u een parallelle Tenant database met de naam *\_ContosoConcertHall Old*, met een vermelding in een parallelle catalogus. Dit patroon van herstellen is het meest geschikt voor het herstellen van een klein gegevens verlies. U kunt dit patroon ook gebruiken als u gegevens moet controleren op nalevings-of controle doeleinden. Het is de aanbevolen benadering voor het gebruik van [actieve geo-replicatie](sql-database-active-geo-replication.md).

1. Voltooi de sectie [een Tenant verwijderen die per ongeluk gegevens verwijdert](#simulate-a-tenant-accidentally-deleting-data) .
2. Open in de Power shell-ISE... \\Leer modules\\bedrijfs continuïteit en herstel\\na nood\\gevallen RestoreTenant_demo-RestoreTenant. ps1_.
3. Stel **$DemoScenario** = **2** *in en herstel de Tenant parallel*.
4. Druk op F5 om het script uit te voeren.

Het script herstelt de Tenant database naar een tijdstip voordat u de gebeurtenis hebt verwijderd. De data base is hersteld naar een nieuwe Data Base met de naam _ContosoConcertHall\_Old_. De meta gegevens van de catalogus die in deze herstelde data base bestaan, worden verwijderd en vervolgens wordt de data base aan de catalogus toegevoegd met behulp van een sleutel die is gemaakt van de *oude ContosoConcertHall\_* -naam.

Met het demo script wordt de pagina gebeurtenissen voor deze nieuwe Tenant database in uw browser geopend. Opmerking van de URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` die op deze pagina gegevens uit de herstelde data base worden weer gegeven, waar *_OLD* wordt toegevoegd aan de naam.

Schuif de gebeurtenissen in de browser om te bevestigen dat de gebeurtenis die in de vorige sectie is verwijderd, is hersteld.

Het weer geven van de herstelde Tenant als een extra Tenant met een eigen Events-app is waarschijnlijk niet de manier waarop u een Tenant toegang hebt tot herstelde gegevens. Het is bedoeld om het herstel patroon te illustreren. Normaal gesp roken geeft u alleen-lezen toegang tot de oude gegevens en behoudt u de herstelde data base gedurende een opgegeven periode. In het voor beeld kunt u de herstelde Tenant vermelding verwijderen nadat u bent voltooid door het scenario voor het verwijderen van een _herstelde Tenant_ uit te voeren.

1. Stel **$DemoScenario** = **4**in, *Verwijder de herstelde Tenant*.
2. Druk op F5 om het script uit te voeren.
3. De *ContosoConcertHall\_oude* vermelding wordt nu verwijderd uit de catalogus. Sluit de pagina gebeurtenissen voor deze Tenant in uw browser.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Een Tenant op locatie herstellen en de bestaande Tenant database vervangen

Deze oefening zet de contoso-Tenant voor concert hal om naar een punt voordat de gebeurtenis werd verwijderd. Met het script *Restore-TenantInPlace* wordt een Tenant database teruggezet naar een nieuwe data base en wordt het oorspronkelijke verwijderd. Dit herstel patroon is het meest geschikt voor het herstellen van ernstige gegevens beschadiging en de Tenant moet mogelijk een aanzienlijk gegevens verlies bevatten.

1. Open in de Power shell-ISE het bestand **demo-RestoreTenant. ps1** .
2. Stel **$DemoScenario** = **5** *in en herstel de Tenant*.
3. Druk op F5 om het script uit te voeren.

Het script herstelt de Tenant database naar een punt voordat de gebeurtenis werd verwijderd. Het gaat eerst om de contoso concert hal-Tenant uit om verdere updates te voor komen. Vervolgens wordt een parallelle data base gemaakt door het herstellen vanaf het herstel punt. De herstelde data base heet een tijds tempel om ervoor te zorgen dat de naam van de data base geen conflict veroorzaakt met de naam van de bestaande Tenant database. Vervolgens wordt de oude Tenant database verwijderd en wordt de naam van de herstelde data base gewijzigd in de oorspronkelijke database naam. Ten slotte wordt de concert hal van Contoso online gebracht zodat de app toegang kan krijgen tot de herstelde data base.

U hebt de data base hersteld naar een tijdstip voordat de gebeurtenis is verwijderd. Wanneer de pagina **gebeurtenissen** wordt geopend, controleert u of de laatste gebeurtenis is hersteld.

Nadat u de Data Base hebt hersteld, duurt het nog een 10 tot 15 minuten voordat de eerste volledige back-up opnieuw kan worden hersteld.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een Data Base herstellen naar een parallelle data base (naast elkaar).
> * Een Data Base op locatie herstellen.

Probeer de zelf studie [Tenant-database schema beheren](saas-tenancy-schema-management.md) .

## <a name="additional-resources"></a>Extra resources

* [Aanvullende zelf studies die voortbouwen op de Wingtip SaaS-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Overzicht van bedrijfscontinuïteit met Azure SQL Database](sql-database-business-continuity.md)
* [Meer informatie over SQL Database back-ups](sql-database-automated-backups.md)
