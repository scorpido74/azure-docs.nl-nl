---
title: Een gesharde SaaS-app met meerdere tenant's implementeren
description: Implementeer en verken de geshard Wingtip Tickets SaaS multi-tenant databaseapplicatie, die SaaS-patronen demonstreert met Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
ms.date: 10/16/2018
ms.openlocfilehash: 3277318e01362df8fc21ff7ca769aaeb8006abc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827995"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Een geshardmulti-tenanttoepassing implementeren en verkennen

In deze zelfstudie implementeert en verkent u een voorbeeld van saas-toepassing met meerdere tenanten met de naam Wingtip Tickets. De Wingtip Tickets-app is ontworpen om functies van Azure SQL Database te presenteren die de implementatie van SaaS-scenario's vereenvoudigen.

Deze implementatie van de Wingtip Tickets app maakt gebruik van een geshard multi-tenant database patroon. De verharding is door tenant-id. Tenantgegevens worden gedistribueerd naar een bepaalde database op basis van de waarden van de tenant-id. 

Met dit databasepatroon u een of meer tenants opslaan in elke shard of database. U optimaliseren voor de laagste kosten door ervoor te zorgen dat elke database wordt gedeeld door meerdere tenants. U ook optimaliseren voor isolatie door elke database slechts één tenant op te slaan. Uw optimalisatiekeuze kan onafhankelijk van elkaar gemaakt worden voor elke specifieke huurder. Uw keuze kan worden gemaakt wanneer de huurder voor het eerst wordt opgeslagen, of u later van gedachten veranderen. De applicatie is ontworpen om goed te werken in beide richtingen.

## <a name="app-deploys-quickly"></a>App wordt snel geïmplementeerd

De app wordt uitgevoerd in de Azure-cloud en maakt gebruik van Azure SQL Database. De implementatiesectie die volgt, biedt de blauwe knop **Implementeren naar Azure.** Wanneer de knop wordt ingedrukt, wordt de app binnen vijf minuten volledig geïmplementeerd op uw Azure-abonnement. U hebt volledige toegang tot het werken met de afzonderlijke toepassingscomponenten.

De toepassing wordt geïmplementeerd met gegevens voor drie voorbeeldtenants. De tenants worden samen opgeslagen in één multi-tenant database.

Iedereen kan de C# en PowerShell broncode voor Wingtip Tickets downloaden vanuit [zijn GitHub repository.][link-github-wingtip-multitenantdb-55g]

## <a name="learn-in-this-tutorial"></a>Meer informatie in deze zelfstudie

> [!div class="checklist"]
> - Hoe de Wingtip Tickets SaaS applicatie te implementeren.
> - Waar de broncode van de toepassing en beheerscripts te krijgen.
> - Over de servers en databases die deel uitmaken van de app.
> - Hoe tenants worden toegewezen aan hun gegevens met de *catalogus*.
> - Hoe een nieuwe huurder te voorzien.
> - Hoe houd je de activiteit van huurders in de app in de gaten.

Er is een reeks gerelateerde tutorials beschikbaar die voortbouwen op deze eerste implementatie. De tutorials verkennen een scala aan SaaS-ontwerp- en beheerpatronen. Wanneer u door de tutorials werkt, wordt u aangemoedigd om door de meegeleverde scripts te stappen om te zien hoe de verschillende SaaS-patronen worden geïmplementeerd.

## <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

- De nieuwste Azure PowerShell is geïnstalleerd. Zie Aan [de slag met Azure PowerShell][link-azure-get-started-powershell-41q]voor meer informatie.

## <a name="deploy-the-wingtip-tickets-app"></a>De app Wingtip Tickets implementeren

### <a name="plan-the-names"></a>De namen plannen

In de stappen van deze sectie geeft u een *gebruikerswaarde* op die wordt gebruikt om ervoor te zorgen dat resourcenamen wereldwijd uniek zijn, en een naam voor de *resourcegroep* die alle bronnen bevat die zijn gemaakt door een implementatie van de app. Voor een persoon genaamd *Ann Finley,* stellen wij voor:
- *Gebruiker:* **af1**  *(Hun initialen, plus een cijfer. Gebruik een andere waarde (bijvoorbeeld af2) als u de app een tweede keer implementeert.)*
- *Resourcegroep:* **wingtip-mt-af1** *(wingtip-mt geeft aan dat dit de geshardmulti-tenant-app is. Door de gebruikersnaam af1 te appen correleert de naam van de resourcegroep met de namen van de resources die deze bevat.)*

Kies nu je namen en schrijf ze op. 

### <a name="steps"></a>Stappen

1. Klik op de volgende blauwe knop **Implementeren naar Azure.**
   - Het opent de Azure-portal met de SaaS-implementatiesjabloon van Wingtip Tickets.

     [![Knop voor Implementeren naar Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Voer de vereiste parameterwaarden voor de implementatie in.

    > [!IMPORTANT]
    > Gebruik voor deze demonstratie geen reeds bestaande brongroepen, servers of groepen. Kies in plaats daarvan **Een nieuwe resourcegroep maken**. Verwijder deze resourcegroep wanneer u klaar bent om gerelateerde facturering te stoppen.
    > Gebruik deze toepassing of resources die deze maakt niet voor productie. Sommige aspecten van verificatie en de instellingen van de serverfirewall zijn opzettelijk onveilig in de app om de demonstratie te vergemakkelijken.

    - Voor **resourcegroep** - Selecteer **Nieuw maken**en geef vervolgens een **naam** op voor de resourcegroep (hoofdlettergevoelig).
        - Selecteer een **locatie** in de vervolgkeuzelijst.
    - Voor **gebruiker** - We raden u aan een korte **gebruikerswaarde te** kiezen.

1. **De toepassing implementeren**.

    - Klik hier om akkoord te gaan met de algemene voorwaarden.
    - Klik op **Kopen**.

1. Controleer de implementatiestatus door op **Meldingen**te klikken, het belpictogram rechts van het zoekvak. Het implementeren van de Wingtip-app duurt ongeveer vijf minuten.

   ![implementatie gelukt](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>De beheerscripts downloaden en deblokkeren

Terwijl de toepassing wordt geïmplementeerd, downloadt u de broncode en beheerscripts van de toepassing.

> [!NOTE]
> Uitvoerbare inhoud (scripts, DLL's) kan door Windows worden geblokkeerd wanneer zip-bestanden worden gedownload van een externe bron en worden geëxtraheerd. Wanneer u de scripts uit een zip-bestand haalt, gebruikt u de volgende stappen om het .zip-bestand te deblokkeren voordat u het uitpakken. Door het .zip-bestand te deblokkeren, zorgt u ervoor dat de scripts mogen worden uitgevoerd.

1. Blader naar [de WingtipTicketsSaaS-MultiTenantDb GitHub repo.](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klik op **Klonen of download**.
3. Klik **op ZIP downloaden** en sla het bestand op.
4. Klik met de rechtermuisknop op het **bestand WingtipTicketsSaaS-MultiTenantDb-master.zip** en selecteer **Eigenschappen**.
5. Selecteer op het tabblad **Algemeen** de optie **De blokkering opheffen**en klik op **Toepassen**.
6. Klik op **OK**.
7. Pak de bestanden uit.

De scripts bevinden zich in de *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning\\ Modules map. \\*

## <a name="update-the-configuration-file-for-this-deployment"></a>Het configuratiebestand voor deze implementatie bijwerken

Voordat u scripts uitvoert, stelt u de *brongroep* en *gebruikerswaarden* in **userConfig.psm1**. Stel deze variabelen in op dezelfde waarden die u tijdens de implementatie instelt.

1. Open... \\Leermodules\\*UserConfig.psm1* in de *PowerShell ISE*.
2. *ResourceGroupName* en *Naam bijwerken* met de specifieke waarden voor uw implementatie (alleen op de lijnen 10 en 11).
3. Sla de wijzigingen op.

De waarden in dit bestand worden gebruikt door alle scripts, dus het is belangrijk dat ze nauwkeurig zijn. Als u de app opnieuw implementeert, moet u verschillende waarden kiezen voor gebruikers- en resourcegroep. Werk vervolgens het UserConfig.psm1-bestand opnieuw bij met de nieuwe waarden.

## <a name="run-the-application"></a>De toepassing uitvoeren

In de Wingtip app zijn de huurders locaties. Een locatie kan een concertzaal, een sportclub of een andere locatie zijn waar evenementen worden georganiseerd. De locaties registreren zich in Wingtip als klant en er wordt een tenant-id gegenereerd voor elke locatie. Elke locatie geeft een lijst van de komende evenementen in Wingtip, zodat het publiek kaartjes kan kopen voor de evenementen.

Elke locatie krijgt een gepersonaliseerde web-app om hun evenementen te vermelden en tickets te verkopen. Elke web-app is onafhankelijk en geïsoleerd van andere tenants. Intern in Azure SQL Database worden de gegevens voor elke tenant standaard opgeslagen in een geshard multi-tenantdatabase. Alle gegevens worden getagd met de tenant-id.

Een centrale **website van de evenementenhub** bevat een lijst met koppelingen naar de tenants in uw specifieke implementatie. Gebruik de volgende stappen om de webpagina Van de **Evenementenhub** en een afzonderlijke web-app te ervaren:

1. Open de **evenementenhub** in uw webbrowser:
   - http://events.wingtip-mt.&lt;gebruiker&gt;.trafficmanager.net &nbsp; *(Gebruiker &lt;&gt; vervangen door de gebruikerswaarde van uw implementatie.)*

     ![events hub](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klik in de **Events Hub** op **Fabrikam Jazz Club**.

   ![Gebeurtenissen](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Als u de verdeling van binnenkomende aanvragen wilt beheren, maakt de Wingtip-app gebruik van [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). De pagina gebeurtenissen voor elke tenant bevat de tenantnaam in de URL. Elke URL bevat ook uw specifieke gebruikerswaarde. Elke URL volgt de getoonde indeling met behulp van de volgende stappen:

- http://events.wingtip-mt.&lt;gebruiker&gt;.trafficmanager.net/*fabrikamjazzclub*

1. De gebeurtenis-app ontleedt de tenantnaam van de URL. De tenantnaam is *fabrikamjazzclub* in het voorgaande voorbeeld-URL.
2. De app hashes vervolgens de tenant naam om een sleutel te maken om toegang te krijgen tot een catalogus met behulp [van shard map management](sql-database-elastic-scale-shard-map-management.md).
3. De app vindt de sleutel in de catalogus en verkrijgt de bijbehorende locatie van de database van de tenant.
4. De app gebruikt de locatiegegevens om de ene database te vinden en te openen die alle gegevens voor de tenant bevat.

### <a name="events-hub"></a>Evenementenhub

1. De **evenementenhub** bevat alle huurders die zijn geregistreerd in de catalogus en hun locaties.
2. De **gebeurtenishub** gebruikt uitgebreide metagegevens in de catalogus om de naam van de tenant op te halen die aan elke toewijzing is gekoppeld om de URL's te construeren.

In een productieomgeving maakt u meestal een CNAME DNS-record om [een bedrijfsinternetdomein](../traffic-manager/traffic-manager-point-internet-domain.md) naar het profiel van de verkeersbeheerder te leiden.

## <a name="start-generating-load-on-the-tenant-databases"></a>Workloads genereren voor de tenant-databases

Nu de app is geïmplementeerd, laten we het aan het werk zetten! Met *het Demo-LoadGenerator PowerShell-script* wordt een werkbelasting gestart die voor elke tenant wordt uitgevoerd. De real-world belasting op veel SaaS apps is meestal sporadisch en onvoorspelbaar. Om dit type belasting te simuleren, produceert de generator een belasting verdeeld over alle tenants. De belasting omvat gerandomiseerde uitbarstingen op elke tenant die zich op gerandomiseerde intervallen. Het duurt enkele minuten voordat het belastingspatroon ontstaat, dus het is het beste om de generator minstens drie of vier minuten te laten draaien voordat de belasting wordt bewaakt.

1. In de *PowerShell ISE,* open de ... \\Learning\\Modules\\Utilities*Demo-LoadGenerator.ps1* script.
2. Druk op **F5** om het script uit te voeren en de load-generator te starten (gebruik voorlopig de standaardwaarden van het script).

Het *Demo-LoadGenerator.ps1-script* opent een andere PowerShell-sessie waarbij de load generator wordt uitgevoerd. De load generator wordt in deze sessie uitgevoerd als een voorgrondtaak die taken voor het genereren van achtergrondbelasting aanroept, één voor elke tenant.

Nadat de voorgrondtaak is gestart, blijft deze in een taakaanroepende status. De taak start extra achtergrondtaken voor nieuwe tenants die vervolgens worden ingericht.

Als u de PowerShell-sessie sluit, worden alle taken gestopt.

U de sessie van de laadgenerator opnieuw starten om verschillende parameterwaarden te gebruiken. Als dat het zo is, sluit u de PowerShell-generatiesessie en voert u de *Demo-LoadGenerator.ps1*opnieuw uit.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Een nieuwe tenant inde sharden database inrichten

De eerste implementatie omvat drie voorbeeldtenants in de *Tenants1-database.* Laten we een andere tenant maken en de effecten ervan op de geïmplementeerde toepassing observeren. In deze stap drukt u op één toets om een nieuwe tenant te maken:

1. Open... \\Learning\\Modules Provision\\and Catalog*Demo-ProvisionTenants.ps1* in de *PowerShell ISE*.
2. Druk op **F5** (niet **F8)** om het script uit te voeren (laat de standaardwaarden voor nu).

   > [!NOTE]
   > U moet de PowerShell-scripts alleen uitvoeren door op de **F5-toets** te drukken, niet door op **F8** te drukken om een geselecteerd deel van het script uit te voeren. Het probleem met **F8** is dat de *$PSScriptRoot* variabele niet wordt geëvalueerd. Deze variabele is nodig voor veel scripts om door mappen te navigeren, andere scripts aan te roepen of modules te importeren.

De nieuwe Red Maple Racing tenant wordt toegevoegd aan de *Tenants1* database en geregistreerd in de catalogus. De site van de nieuwe tenant voor het verkopen van **tickets wordt** geopend in uw browser:

![Nieuwe tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Vernieuw de **gebeurtenishub**en de nieuwe tenant wordt nu weergegeven in de lijst.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Een nieuwe tenant inrichten in de eigen database

Met het geshard multitenantmodel u kiezen of u een nieuwe tenant wilt inrichten in een database die andere tenants bevat, of in een eigen database. Een huurder geïsoleerd in zijn eigen database geniet de volgende voordelen:

- De prestaties van de database van de tenant kunnen worden beheerd zonder dat u compromissen hoeft te sluiten met de behoeften van andere tenants.
- Indien nodig kan de database worden hersteld naar een eerder tijdstip, omdat er geen andere tenants zouden worden beïnvloed.

U ervoor kiezen om klanten met gratis proefperiode of economy-klanten in databases met meerdere huurders te plaatsen. U elke premium tenant in zijn eigen specifieke database plaatsen. Als u veel databases maakt die slechts één tenant bevatten, u ze allemaal gezamenlijk beheren in een elastische groep om resourcekosten te optimaliseren.

Vervolgens voorzien we een andere tenant, dit keer in de eigen database:

1. Inch... \\Learning\\Modules Provision\\en Catalog*Demo-ProvisionTenants.ps1*, wijzigen *$TenantName* naar **Salix Salsa**, *$VenueType* om te **dansen** en *$Scenario* tot **2**.

2. Druk op **F5** om het script opnieuw uit te voeren.
    - Deze **F5** pers voorziet de nieuwe tenant in een aparte database. De database en de tenant zijn geregistreerd in de catalogus. Vervolgens wordt de browser geopend naar de pagina Gebeurtenissen van de tenant.

   ![Salix Salsa evenementen pagina](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Schuif naar de onderkant van de pagina. Daar in de banner zie je de databasenaam waarin de tenantgegevens zijn opgeslagen.

3. Vernieuw de **evenementenhub** en de twee nieuwe tenants worden nu weergegeven in de lijst.

## <a name="explore-the-servers-and-tenant-databases"></a>De servers en tenantdatabases verkennen

Nu kijken we naar een aantal van de middelen die werden ingezet:

1. Blader in de [Azure-portal](https://portal.azure.com)naar de lijst met brongroepen. Open de resourcegroep die u hebt gemaakt toen u de toepassing hebt geïmplementeerd.

   ![resourcegroep](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klik **op de&lt;&gt; gebruikersserver catalog-mt.** De catalogusserver bevat twee databases met de naam *tenantcatalogus* en *basetenantdb.* De *basetenantdb-database* is een lege sjabloondatabase. Het wordt gekopieerd om een nieuwe tenantdatabase te maken, of deze nu voor veel tenants of slechts één tenant wordt gebruikt.

   ![catalogusserver](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Ga terug naar de brongroep en selecteer de *tenants1-mt-server* die de tenantdatabases bevat.
    - De tenants1-database is een database met meerdere tenants waarin de oorspronkelijke drie tenants, plus de eerste tenant die u hebt toegevoegd, worden opgeslagen. Het is geconfigureerd als een 50 DTU Standard database.
    - De **salixsalsa** database bevat de Salix Salsa danslocatie als enige huurder. Het is standaard geconfigureerd als een Standard edition-database met 50 DTUs.

   ![tenantsserver](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>De prestaties van de database controleren

Als de laadgenerator enkele minuten actief is, is er voldoende telemetrie beschikbaar om te kijken naar de databasebewakingsmogelijkheden die zijn ingebouwd in de Azure-portal.

1. Blader naar de **gebruikersserver&lt;&gt; tenants1 mt** en klik op **tenants1** om het gebruik van resources weer te geven voor de database met vier tenants. Elke tenant is onderworpen aan een sporadische zware belasting van de ladinggenerator:

   ![monitor tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   De DTU-gebruiksgrafiek illustreert mooi hoe een multi-tenant database een onvoorspelbare workload voor veel tenants kan ondersteunen. In dit geval past de laadgenerator een sporadische belasting van ongeveer 30 DTU's toe op elke tenant. Deze belasting komt overeen met 60% gebruik van een 50 DTU-database. Pieken van meer dan 60% zijn het resultaat van belasting die tegelijkertijd op meer dan één tenant wordt toegepast.

2. Blader naar de **gebruikersserver&lt;&gt; van tenants1 mt** en klik op de **salixsalsa-database.** U het resourcegebruik in deze database zien die slechts één tenant bevat.

   ![salixsalsa database](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

De load generator past een vergelijkbare belasting toe op elke tenant, ongeacht in welke database elke tenant zich bevindt. Met slechts één tenant in de **salixsalsa-database** u zien dat de database een veel hogere belasting kan dragen dan de database met meerdere tenants. 

### <a name="resource-allocations-vary-by-workload"></a>Toewijzingen van resources variëren per werkbelasting

Soms vereist een multi-tenant database meer resources voor goede prestaties dan een single-tenant database, maar niet altijd. De optimale toewijzing van middelen is afhankelijk van de specifieke werklastkenmerken voor de huurders in uw systeem.

De workloads die door het script van de laadgenerator worden gegenereerd, zijn alleen bedoeld voor illustratiedoeleinden.

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenant's voor](saas-tenancy-app-design-patterns.md)meer informatie over SaaS-toepassingen met meerdere tenants.

- Zie voor meer informatie over elastische pools:

  - [Met elastische pools kunt u meerdere Azure SQL-databases beheren en schalen](sql-database-elastic-pool.md)
  - [Uitbreiden met Azure SQL Database](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - De Toepassing Van De SaaS Multi-tenant database van Wingtip Tickets implementeren.
> - Over de servers en databases die deel uitmaken van de app.
> - Tenants worden toegewezen aan hun gegevens met de *catalogus*.
> - Nieuwe tenants inrichten, in een database met meerdere tenants en een database met één tenant.
> - Het gebruik van de groep weergeven om de tenantactiviteit te controleren.
> - Voorbeeldbronnen verwijderen om gerelateerde facturering te stoppen.

Probeer nu de [zelfstudie voor voorziening en catalogus](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Knop voor implementatie naar Azure."

