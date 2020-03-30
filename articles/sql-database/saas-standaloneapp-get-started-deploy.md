---
title: SaaS-zelfstudie met één huurder
description: Een zelfstandige SaaS-toepassing met één tenant implementeren en verkennen, die Azure SQL Database gebruikt.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 11/07/2018
ms.openlocfilehash: e3afc8aa58551b995070ffaca978c8e7c8454da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822122"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Een zelfstandige single-tenanttoepassing implementeren en verkennen die Azure SQL-database gebruikt

In deze zelfstudie implementeer t-werk en verken je de Wingtip Tickets SaaS-voorbeeldtoepassing die is ontwikkeld met behulp van het standalone-toepassings- of app-per-tenant-patroon.  De toepassing is ontworpen om functies van Azure SQL Database te presenteren die het inschakelen van SaaS-scenario's met meerdere tenant's vereenvoudigen.

Het zelfstandige toepassings- of app-per-tenantpatroon implementeert een toepassingsexemplaar voor elke tenant.  Elke toepassing is geconfigureerd voor een specifieke tenant en geïmplementeerd in een afzonderlijke Azure-brongroep. Meerdere exemplaren van de toepassing zijn ingericht om een multi-tenant oplossing te bieden. Dit patroon is het meest geschikt voor kleinere aantallen, van huurders waar huurder isolatie is een topprioriteit. Azure heeft partnerprogramma's waarmee resources kunnen worden geïmplementeerd in het abonnement van een tenant en worden beheerd door een serviceprovider namens de tenant. 

In deze zelfstudie implementeert u drie zelfstandige toepassingen voor drie tenants in uw Azure-abonnement.  Je hebt volledige toegang tot het verkennen en werken met de afzonderlijke toepassingscomponenten.

De broncode en beheerscripts van de toepassing zijn beschikbaar in de [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub repo. De applicatie is gemaakt met Visual Studio 2015 en wordt niet met succes geopend en gecompileerd in Visual Studio 2019 zonder te updaten.


In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Hoe de Wingtip Tickets SaaS Standalone Applicatie te implementeren.
> * Waar de broncode van de toepassing en beheerscripts te krijgen.
> * Over de servers en databases die deel uitmaken van de app.

Extra tutorials zullen worden vrijgegeven. Hiermee u een reeks beheerscenario's verkennen op basis van dit toepassingspatroon.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Implementeer de Wingtip Tickets SaaS Standalone Applicatie

Implementeer de app voor de drie opgegeven tenants:

1. Klik op elke blauwe knop **Implementeren naar Azure** om de implementatiesjabloon in de [Azure-portal](https://portal.azure.com)te openen. Voor elke sjabloon zijn twee parameterwaarden vereist; een naam voor een nieuwe resourcegroep en een gebruikersnaam die deze implementatie onderscheidt van andere implementaties van de app. In de volgende stap vindt u details voor het instellen van deze waarden.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Contoso Concertzaal**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Kornoelje Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>&nbsp; **Fabrikam Jazz Club**

2. Voer vereiste parameterwaarden in voor elke implementatie.

    > [!IMPORTANT]
    > Sommige verificatie- en serverfirewalls zijn opzettelijk onbeveiligd voor demonstratiedoeleinden. **Maak een nieuwe resourcegroep** voor elke implementatie van toepassingen.  Gebruik geen bestaande resourcegroep. Gebruik deze toepassing of resources die deze maakt niet voor productie. Verwijder alle brongroepen wanneer u klaar bent met de toepassingen om gerelateerde facturering te stoppen.

    Het is het beste om alleen kleine letters, cijfers en koppeltekens te gebruiken in uw resourcenamen.
    * Selecteer **Voor resourcegroep**Nieuw maken en geef vervolgens een kleine naam voor de resourcegroep op. **wingtip-sa-\<\>-\<venueName\> gebruiker** is het aanbevolen patroon.  Voor \<venueName\>, vervang de naam van de locatie door geen spaties. Voor \<\>de gebruiker, vervang de gebruikerswaarde van onderen.  Met dit patroon, resource groep namen kunnen worden *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Selecteer een **locatie** in de vervolgkeuzelijst.

    * Voor **gebruiker** - We raden een korte gebruikerswaarde aan, zoals uw initialen plus een cijfer: bijvoorbeeld *af1*.


3. **De toepassing implementeren**.

    * Klik hier om akkoord te gaan met de algemene voorwaarden.
    * Klik op **Kopen**.

4. Controleer de status van alle drie de implementaties door op **Meldingen** te klikken (het belpictogram rechts van het zoekvak). Het implementeren van de apps duurt ongeveer vijf minuten.


## <a name="run-the-applications"></a>De toepassingen uitvoeren

De app toont locaties die evenementen organiseren.  De locaties zijn de huurders van de applicatie. Elke locatie krijgt een gepersonaliseerde website om hun evenementen te vermelden en tickets te verkopen. Locatietypes zijn concertzalen, jazzclubs en sportclubs. In het voorbeeld bepaalt het type locatie de achtergrondfoto die op de website van de locatie wordt weergegeven.   In het standalone app-model heeft elke locatie een aparte toepassingsinstantie met een eigen standalone SQL-database.

1. Open de pagina gebeurtenissen voor elk van de drie tenants in afzonderlijke browsertabbladen:

   - http://events.contosoconcerthall.&lt;gebruiker&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;gebruiker&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;gebruiker&gt;.trafficmanager.net

     (Vervang in elke &lt;&gt; URL de gebruiker door de gebruikerswaarde van uw implementatie.)

   ![Gebeurtenissen](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Als u de verdeling van binnenkomende aanvragen wilt beheren, maakt de app gebruik van [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Elke tenantspecifieke app-instantie bevat de tenantnaam als onderdeel van de domeinnaam in de URL. Alle tenant-URL's bevatten uw specifieke **gebruikerswaarde.** De URL's volgen de volgende indeling:
- http://events.&lt;venuename&gt;. &lt;gebruiker&gt;.trafficmanager.net

De database **locatie** van elke tenant is opgenomen in de app-instellingen van de bijbehorende geïmplementeerde app.

In een productieomgeving maakt u doorgaans een Dns-record cname om [*een bedrijfsinternetdomein*](../traffic-manager/traffic-manager-point-internet-domain.md) naar de URL van het profiel van de verkeersbeheerder te leiden.


## <a name="explore-the-servers-and-tenant-databases"></a>De servers en tenantdatabases verkennen

Laten we eens kijken naar enkele van de resources die zijn geïmplementeerd:

1. Blader in de [Azure-portal](https://portal.azure.com)naar de lijst met brongroepen.
2. U ziet de drie tenantbrongroepen.
3. Open de **wingtip-sa-fabrikam-&lt;user&gt; ** resource group, die de middelen bevat voor de Fabrikam Jazz Club-implementatie.  De **fabrikamjazzclub-user&lt;&gt; ** server bevat de **fabrikamjazzclub** database.

Elke tenantdatabase is een standalone database van 50 DTU.Each tenant database is a 50 DTU *standalone* database.

## <a name="additional-resources"></a>Aanvullende bronnen

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenant's voor](saas-tenancy-app-design-patterns.md)meer informatie over SaaS-toepassingen met meerdere tenants.

 
## <a name="delete-resource-groups-to-stop-billing"></a>Resourcegroepen verwijderen om facturering te stoppen ##

Wanneer u klaar bent met het gebruik van het voorbeeld, verwijdert u alle resourcegroepen die u hebt gemaakt om de bijbehorende facturering te stoppen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Hoe de Wingtip Tickets SaaS Standalone Applicatie te implementeren.
> * Over de servers en databases die deel uitmaken van de app.
> * Voorbeeldbronnen verwijderen om gerelateerde facturering te stoppen.

Probeer vervolgens de [zelfstudie Voorziening en catalogus](saas-standaloneapp-provision-and-catalog.md) waarin u het gebruik van een catalogus met tenants verkent waarmee een reeks scenario's voor meerdere tenants mogelijk is, zoals schemabeheer en tenantanalyses.
 

