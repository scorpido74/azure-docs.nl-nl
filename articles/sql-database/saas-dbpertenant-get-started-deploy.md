---
title: Database-per-tenant SaaS-zelfstudie
description: Implementeer en verken de Wingtip Tickets SaaS multitenant-toepassing die het database-per-tenant-patroon en andere SaaS-patronen demonstreert met Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3182daa4ebf3becc824b600d1e487e12b875b275
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529656"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Een multitenant SaaS-app implementeren en verkennen die het database-per-tenant-patroon met SQL Database gebruikt

In deze zelfstudie implementeer en verken je de Wingtip Tickets SaaS database-per-tenant applicatie (Wingtip). De app maakt gebruik van een database-per-tenant patroon om de gegevens van meerdere tenants op te slaan. De app is ontworpen om functies van Azure SQL Database te presenteren die vereenvoudigen hoe u SaaS-scenario's inschakelen.

Vijf minuten nadat u **Deploy to Azure**hebt geselecteerd, hebt u een SaaS-toepassing met meerdere tenant's. De app bevat een SQL-database die in de cloud wordt uitgevoerd. De app wordt geïmplementeerd met drie voorbeeldtenants, elk met een eigen database. Alle databases worden geïmplementeerd in een SQL-elastische groep. De app wordt geïmplementeerd in uw Azure-abonnement. Je hebt volledige toegang tot het verkennen en werken met de afzonderlijke onderdelen van de app. De broncode van toepassing C# en de beheerscripts zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant GitHub repo.][github-wingtip-dpt]

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> - De Wingtip SaaS-toepassing implementeren?
> - Waar de broncode en beheerscripts van de toepassing te krijgen.
> - Over de servers, pools en databases die deel uitmaken van de app.
> - Hoe tenants worden toegewezen aan hun gegevens met de *catalogus*.
> - Hoe een nieuwe huurder te voorzien.
> - Hoe houd je de activiteit van huurders in de app in de gaten.

Een [reeks gerelateerde tutorials](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) biedt om verschillende SaaS ontwerp- en managementpatronen te verkennen. De tutorials bouwen verder dan deze eerste implementatie. Wanneer u de tutorials gebruikt, u de meegeleverde scripts onderzoeken om te zien hoe de verschillende SaaS-patronen worden geïmplementeerd. De scripts laten zien hoe functies van SQL Database de ontwikkeling van SaaS-toepassingen vereenvoudigen.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, controleert u of Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="deploy-the-wingtip-tickets-saas-application"></a>De Wingtip Tickets SaaS-applicatie implementeren

### <a name="plan-the-names"></a>De namen plannen

In de stappen van deze sectie geeft u een gebruikerswaarde op die wordt gebruikt om ervoor te zorgen dat resourcenamen wereldwijd uniek zijn. U geeft ook een naam op voor de resourcegroep die alle bronnen bevat die zijn gemaakt door een implementatie van de app. Voor een fictieve persoon genaamd Ann Finley, stellen wij voor:

- **Gebruiker**: *af1* bestaat uit ann Finley's initialen plus een cijfer. Als u de app een tweede keer implementeert, gebruikt u een andere waarde. Een voorbeeld is af2.
- **Resourcegroep**: *wingtip-dpt-af1* geeft aan dat dit de database-per-tenant-app is. De gebruikersnaam af1 toevoegen om de naam van de brongroep te correleren met de namen van de resources die deze bevat.

Kies nu je namen en schrijf ze op.

### <a name="steps"></a>Stappen

1. Als u de saas-database-per-tenant-implementatiesjabloon wingtip Tickets-per-tenant wilt openen in de Azure-portal, selecteert **u Implementeren naar Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Voer waarden in de sjabloon voor de vereiste parameters in.

    > [!IMPORTANT]
    > Sommige verificatie- en serverfirewalls zijn opzettelijk onbeveiligd voor demonstratiedoeleinden. We raden u aan een nieuwe brongroep te maken. Gebruik geen bestaande resourcegroepen, servers of groepen. Gebruik deze toepassing, scripts of geïmplementeerde resources niet voor productie. Verwijder deze brongroep wanneer u klaar bent met de toepassing om gerelateerde facturering te stoppen.

    - **Resourcegroep**: Selecteer **Nieuw maken**en geef de unieke naam op die u eerder hebt gekozen voor de resourcegroep.
    - **Locatie**: Selecteer een locatie in de vervolgkeuzelijst.
    - **Gebruiker:** gebruik de gebruikersnaamwaarde die u eerder hebt gekozen.

1. De toepassing implementeren.

    a. Selecteer om akkoord te gaan met de algemene voorwaarden.

    b. Selecteer **Aankoop**.

1. Als u de implementatiestatus wilt controleren, selecteert u **Meldingen** (het belpictogram rechts van het zoekvak). Het implementeren van de Wingtip Tickets SaaS-app duurt ongeveer vijf minuten.

   ![Implementatie geslaagd](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>De beheerscripts van Wingtip Tickets downloaden en deblokkeren

Download terwijl de toepassing wordt geïmplementeerd, de broncode en beheerscripts.

> [!IMPORTANT]
> Uitvoerbare inhoud (scripts en DLL's) kan door Windows worden geblokkeerd wanneer .zip-bestanden worden gedownload van een externe bron en worden geëxtraheerd. Volg de stappen om het .zip-bestand te deblokkeren voordat u de scripts uitpakt. Als u de blokkering desblokkeringen uitvoert, wordt de blokkering uitgevoerd.

1. Blader naar de [WingtipTicketsSaaS-DbPerTenant GitHub repo.][github-wingtip-dpt]
1. Selecteer **Klonen of downloaden**.
1. Selecteer **ZIP downloaden**en sla het bestand op.
1. Klik met de rechtermuisknop op het bestand **WingtipTicketsSaaS-DbPerTenant-master.zip** en selecteer **Eigenschappen**.
1. Selecteer op het tabblad **Algemeen** de optie**Toepassen** **opheffen** > .
1. Selecteer **OK**en de bestanden extraheren

Scripts bevinden zich in de ... \\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules map.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Het gebruikersconfiguratiebestand voor deze implementatie bijwerken

Voordat u scripts uitvoert, werkt u de brongroep en gebruikerswaarden in het bestand User Config bij. Stel deze variabelen in op de waarden die u tijdens de implementatie hebt gebruikt.

1. Open in de PowerShell ISE ... \\Leermodules\\**UserConfig.psm1**
1. **ResourceGroupName** en **Naam bijwerken** met de specifieke waarden voor uw implementatie (alleen op de lijnen 10 en 11).
1. Sla de wijzigingen op.

Deze waarden worden verwezen in bijna elk script.

## <a name="run-the-application"></a>De toepassing uitvoeren

De app toont locaties die evenementen organiseren. Locatietypes zijn concertzalen, jazzclubs en sportclubs. In Wingtip Tickets worden locaties geregistreerd als huurders. Als huurder geeft een locatie een gemakkelijke manier om evenementen te vermelden en om tickets te verkopen aan hun klanten. Elke locatie krijgt een gepersonaliseerde website om hun evenementen te vermelden en om tickets te verkopen.

Intern in de app krijgt elke tenant een SQL-database geïmplementeerd in een SQL-elastische groep.

Een centrale **pagina met gebeurtenissenhub** bevat een lijst met koppelingen naar de tenants in uw implementatie.

1. Gebruik de URL om de evenementenhub http://events.wingtip-dpt.&ltin&gt;uw webbrowser te openen: ;gebruiker .trafficmanager.net. Vervang &lt;&gt; de gebruiker door de gebruikerswaarde van uw implementatie.

    ![Evenementenhub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Kies **fabrikam Jazz Club** in de Evenementenhub.

    ![Gebeurtenissen](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

De Wingtip-toepassing maakt gebruik van [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) om de verdeling van binnenkomende aanvragen te beheren. De URL om toegang te krijgen tot de pagina gebeurtenissen voor een specifieke tenant gebruikt de volgende indeling:

- http://events.wingtip-dpt.&lt;gebruiker&gt;.trafficmanager.net/fabrikamjazzclub

    De delen van de vorige notatie worden uitgelegd in de volgende tabel.

    | URL-onderdeel        | Beschrijving       |
    | :-------------- | :---------------- |
    | events.wingtip-dpt | De evenementenonderdelen van de Wingtip-app.<br /><br /> *-dpt* onderscheidt de *database-per-tenant* implementatie van Wingtip Tickets van andere implementaties. Voorbeelden hiervan zijn de *implementaties van de single* app-per-tenant *(-sa)* of *multitenant database* *(-mt).* |
    | . * &lt;gebruiker&gt;* | *af1* in het voorbeeld. |
    | trafficmanager.net/ | Traffic Manager, basis-URL. |
    | fabrikamjazzclub | Identificeert de huurder genaamd Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- De tenantnaam wordt van de URL ontleed door de gebeurtenis-app.
- De tenantnaam wordt gebruikt om een sleutel te maken.
- De sleutel wordt gebruikt om toegang te krijgen tot de catalogus om de locatie van de database van de tenant te verkrijgen.
  - De catalogus wordt geïmplementeerd met behulp van *shard map management*.
- De gebeurtenishub gebruikt uitgebreide metagegevens in de catalogus om de URL's van de lijst van gebeurtenissen voor elke tenant te construeren.

In een productieomgeving maakt u doorgaans een Dns-record cname om [*een bedrijfsinternetdomein*](../traffic-manager/traffic-manager-point-internet-domain.md) naar de DNS-naam van Traffic Manager te leiden.

> [!NOTE]
> Het is misschien niet meteen duidelijk wat het gebruik van de verkeersmanager is in deze tutorial. Het doel van deze serie tutorials is om patronen te demonstreren die de schaal van een complexe productieomgeving aankunnen. In een dergelijk geval hebt u bijvoorbeeld meerdere web-apps verspreid over de hele wereld, naast databases en hebt u verkeersbeheermanager nodig om tussen deze instanties te routeren.
Een andere set van tutorials die het gebruik van traffic manager illustreert zijn echter de [geo-restore](saas-dbpertenant-dr-geo-restore.md) en de [geo-replicatie](saas-dbpertenant-dr-geo-replication.md) tutorials. In deze zelfstudies wordt de verkeersbeheerder gebruikt om over te schakelen naar een herstelinstantie van de SaaS-app in het geval van een regionale storing.

## <a name="start-generating-load-on-the-tenant-databases"></a>Workloads genereren voor de tenant-databases

Nu de app is geïmplementeerd, laten we deze aan het werk zetten.

Het *Demo-LoadGenerator PowerShell-script* start een werkbelasting die wordt uitgevoerd ten opzichte van alle tenantdatabases. De real-world belasting op veel SaaS apps is sporadisch en onvoorspelbaar. Om dit type belasting te simuleren, produceert de generator een belasting met gerandomiseerde pieken of uitbarstingen van activiteit op elke tenant. De uitbarstingen treden op gerandomiseerde intervallen op. Het duurt enkele minuten voordat het belastingspatroon ontstaat. Laat de generator minstens drie of vier minuten draaien voordat u de belasting controleert.

1. Open in de PowerShell ISE de ... \\Learning\\Modules\\Utilities*Demo-LoadGenerator.ps1* script.
2. Druk op F5 om het script uit te voeren en start de laadgenerator. Laat de standaardparameterwaarden voor nu.
3. Meld u aan bij uw Azure-account en selecteer het abonnement dat u wilt gebruiken, indien nodig.

Het script van de laadgenerator start een achtergrondtaak voor elke database in de catalogus en stopt vervolgens. Als u het script van de laadgenerator opnieuw uitvoert, worden alle achtergrondtaken gestopt die worden uitgevoerd voordat deze nieuwe worden gestart.

### <a name="monitor-the-background-jobs"></a>De achtergrondtaken controleren

Als u de achtergrondtaken wilt beheren en controleren, gebruikt u de volgende cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 acties

*Demo-LoadGenerator.ps1* bootst een actieve werkbelasting van klanttransacties na. In de volgende stappen wordt de reeks acties beschreven die *Demo-LoadGenerator.ps1* initieert:

1. *Demo-LoadGenerator.ps1* start *LoadGenerator.ps1* op de voorgrond.

    - Beide .ps1-bestanden worden opgeslagen onder\\de\\mappen Learning Modules Utilities .

2. *LoadGenerator.ps1* loopt door alle tenantdatabases in de catalogus.

3. *LoadGenerator.ps1* start een PowerShell-taak op de achtergrond voor elke tenantdatabase:

    - Standaard worden de achtergrondtaken uitgevoerd gedurende 120 minuten.
    - Elke taak veroorzaakt een CPU-gebaseerde belasting op één tenantdatabase door *sp_CpuLoadGenerator*uit te voeren. De intensiteit en duur van de belasting `$DemoScenario`varieert afhankelijk van .
    - *sp_CpuLoadGenerator* loopt rond een SQL SELECT-instructie die een hoge CPU-belasting veroorzaakt. Het tijdsinterval tussen de problemen van de SELECT varieert afhankelijk van parameterwaarden om een controleerbare CPU-belasting te maken. Belastingsniveaus en intervallen worden gerandomiseerd om meer realistische belastingen te simuleren.
    - Dit .sql-bestand wordt opgeslagen onder *WingtipTenantDB\\\\dbo StoredProcedures\\*.

4. Als `$OneTime = $false`, de load generator start de achtergrond taken en vervolgens blijft draaien. Elke 10 seconden controleert het op alle nieuwe tenants die zijn ingericht. Als u `$OneTime = $true`de achtergrondtaken instelt, start de LoadGenerator de achtergrondtaken en stopt deze met het uitvoeren op de voorgrond. Voor deze tutorial, laat `$OneTime = $false`.

   Gebruik Ctrl-C of Stop Operation Ctrl-Break als u de laadgenerator wilt stoppen of opnieuw wilt starten.

   Als u de laadgenerator op de voorgrond laat draaien, gebruikt u een andere PowerShell ISE-instantie om andere PowerShell-scripts uit te voeren.

&nbsp;

Voordat u verder gaat met de volgende sectie, laat u de laadgenerator in de taakaanroepende status draaien.

## <a name="provision-a-new-tenant"></a>Een nieuwe tenant inrichten

De eerste implementatie maakt drie voorbeeldtenants. Nu maakt u een andere tenant om de impact op de geïmplementeerde toepassing te zien. In de Wingtip-app wordt de werkstroom voor het inrichten van nieuwe tenants uitgelegd in de [zelfstudie Voorvoorziening en catalogus](saas-dbpertenant-provision-and-catalog.md). In deze fase maakt u een nieuwe tenant, die minder dan een minuut duurt.

1. Open een nieuwe PowerShell ISE.
2. Open... \\Leermodules\Provision and\\Catalog*Demo-ProvisionAndCatalog.ps1*.
3. Als u het script wilt uitvoeren, drukt u op F5. Laat de standaardwaarden voor nu.

   > [!NOTE]
   > Veel Wingtip SaaS-scripts gebruiken *$PSScriptRoot* om door mappen te bladeren om functies in andere scripts aan te roepen. Deze variabele wordt alleen geëvalueerd wanneer het volledige script wordt uitgevoerd door op F5 te drukken.Het markeren en uitvoeren van een selectie met F8 kan leiden tot fouten. Als u de scripts wilt uitvoeren, drukt u op F5.

De nieuwe tenantdatabase is:

- Gemaakt in een SQL-elastische pool.
- Geïnitialiseerd.
- Geregistreerd in de catalogus.

Na een succesvolle inrichting wordt de *site Gebeurtenissen* van de nieuwe tenant in uw browser weergegeven.

![Nieuwe tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Vernieuw de gebeurtenishub om de nieuwe tenant in de lijst te laten verschijnen.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Servers, pools en tenant-databases

Nu u een belasting hebt uitgevoerd tegen de verzameling huurders, bekijken we enkele van de resources die zijn geïmplementeerd.

1. Blader in de [Azure-portal](https://portal.azure.com)naar uw lijst met SQL-servers. Open vervolgens de **catalogus-dpt-&lt;USER-server.&gt; **
    - De catalogusserver bevat twee databases, **tenantcatalogus** en **basetenantdb** (een sjabloondatabase die is gekopieerd om nieuwe tenants te maken).

   ![Databases](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Ga terug naar uw lijst met SQL-servers.

3. Open de **tenants1-dpt- USER-server&lt;&gt; ** die de tenantdatabases bevat.

4. Zie de volgende items:

    - Elke tenantdatabase is een **Elastic Standard-database** in een standaardgroep van 50 eDTU.
    - De Red Maple Racing database is de tenant database die u eerder hebt ingericht.

   ![Server met databases](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>De pool bewaken

Nadat *LoadGenerator.ps1* enkele minuten heeft uitgevoerd, moeten er voldoende gegevens beschikbaar zijn om te beginnen met kijken naar een aantal bewakingsmogelijkheden. Deze mogelijkheden zijn ingebouwd in pools en databases.

Blader naar de **servertenants1-dpt-gebruiker&lt;&gt;** en selecteer **Pool1** om het gebruik van resources voor de groep weer te geven. In de volgende grafieken, de lading generator liep voor een uur.

   ![Monitorgroep](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- De eerste grafiek, met het label **Resourcegebruik,** toont het eDTU-gebruik van de groep.
- De tweede grafiek toont het eDTU-gebruik van de vijf meest actieve databases in de groep.

De twee grafieken illustreren dat elastische pools en SQL Database goed geschikt zijn voor onvoorspelbare SaaS-toepassingsworkloads. De grafieken tonen aan dat vier databases elk barsten tot maar liefst 40 eDTU's, en toch alle databases worden comfortabel ondersteund door een 50-eDTU pool. De 50-eDTU-pool kan nog zwaardere workloads ondersteunen. Als de databases zijn ingericht als enkele databases, moet elk een S2 (50 DTU) om de uitbarstingen te ondersteunen. De kosten van vier enkele S2-databases zijn bijna drie keer de prijs van de pool. In situaties in de echte wereld draaien SQL Database-klanten tot 500 databases in 200 eDTU-pools. Zie de [zelfstudie Prestatiecontrole](saas-dbpertenant-performance-monitoring.md)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie voor meer informatie aanvullende [zelfstudies die voortbouwen op de Wingtip Tickets SaaS-database-per-tenant-toepassing.](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Zie [Wat is een Azure SQL-elastische pool voor](sql-database-elastic-pool.md)meer informatie over elastische pools?
- Zie [Geschaalde clouddatabases beheren](elastic-jobs-overview.md)voor meer informatie over elastische taken.
- Zie [Ontwerppatronen voor Multitenant SaaS-toepassingen voor](saas-tenancy-app-design-patterns.md)meer informatie over SaaS-toepassingen met meerdere tenants.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - Hoe de Wingtip Tickets SaaS applicatie te implementeren.
> - Over de servers, pools en databases die deel uitmaken van de app.
> - Hoe tenants worden toegewezen aan hun gegevens met de *catalogus*.
> - Hoe nieuwe huurders te voorzien.
> - Het gebruik van de groep weergeven om de tenantactiviteit te controleren.
> - Voorbeeldbronnen verwijderen om gerelateerde facturering te stoppen.

Probeer vervolgens de [zelfstudie Voorvoorziening en catalogus](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
