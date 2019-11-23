---
title: Zelf studie voor de SaaS-data base per Tenant
description: Implementeer en verken de SaaS multi tenant-toepassing Wingtip tickets die het data base-per-Tenant patroon en andere SaaS-patronen demonstreert met behulp van Azure SQL Database.
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
ms.openlocfilehash: 31f712f80ee2492e4bbaec99bd093d46f9d04e6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824001"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Implementeer en verken een multi tenant SaaS-app die gebruikmaakt van het data base-per-Tenant patroon met SQL Database

In deze zelf studie implementeert en bekijkt u de Wingtip tickets SaaS data base-per-Tenant toepassing (Wingtip). De app maakt gebruik van een Data Base-per-Tenant patroon om de gegevens van meerdere tenants op te slaan. De app is ontworpen om functies van Azure SQL Database te demonstreren die het inschakelen van SaaS-scenario's vereenvoudigen.

Vijf minuten nadat u **implementeren naar Azure**hebt geselecteerd, hebt u een multi tenant-SaaS-toepassing. De app bevat een SQL database die wordt uitgevoerd in de Cloud. De app wordt geïmplementeerd met drie voor beelden van tenants, elk met een eigen data base. Alle data bases worden geïmplementeerd in een elastische SQL-pool. De app is geïmplementeerd in uw Azure-abonnement. U hebt volledige toegang om de afzonderlijke onderdelen van de app te verkennen en ermee te werken. De bron C# code van de toepassing en de beheer scripts zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant github opslag plaats][github-wingtip-dpt].

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> - Hoe u de SaaS-toepassing Wingtip implementeert.
> - Waar de bron code en beheer scripts van de toepassing worden opgehaald.
> - Over de servers, Pools en data bases die de app vormen.
> - Hoe tenants worden toegewezen aan hun gegevens met de *catalogus*.
> - Hoe u een nieuwe Tenant inricht.
> - De Tenant activiteiten in de app bewaken.

Een [reeks gerelateerde zelf studies](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) biedt verschillende SaaS-ontwerp-en beheer patronen. De zelf studies maken gebruik van de eerste implementatie. Wanneer u de zelf studies gebruikt, kunt u de meegeleverde scripts bekijken om te zien hoe de verschillende SaaS-patronen worden geïmplementeerd. De scripts laten zien hoe functies van SQL Database de ontwikkeling van SaaS-toepassingen te vereenvoudigen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat Azure PowerShell is geïnstalleerd om deze zelf studie te volt ooien. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="deploy-the-wingtip-tickets-saas-application"></a>De SaaS-toepassing Wingtip tickets implementeren

### <a name="plan-the-names"></a>De namen plannen

In de stappen van deze sectie geeft u een gebruikers waarde op die wordt gebruikt om te controleren of de resource namen wereld wijd uniek zijn. U geeft ook een naam op voor de resource groep met alle resources die zijn gemaakt door een implementatie van de app. Voor een fictieve persoon met de naam Anne Finley wordt voorgesteld:

- **Gebruiker**: *AF1* bestaat uit de initialen van Anne Finley plus een cijfer. Als u de app een tweede keer implementeert, moet u een andere waarde gebruiken. Een voor beeld is AF2.
- **Resource groep**: *Wingtip-dpt-AF1* geeft aan dat dit de data base-per-Tenant-app is. Voeg de gebruikers naam AF1 toe om de naam van de resource groep te correleren met de namen van de resources die deze bevat.

Kies uw namen nu en noteer deze.

### <a name="steps"></a>Stappen

1. Als u de sjabloon voor de implementatie van de Wingtip tickets SaaS-data base per Tenant in de Azure Portal wilt openen, selecteert **u implementeren naar Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Voer waarden in de sjabloon in voor de vereiste para meters.

    > [!IMPORTANT]
    > Sommige verificatie-en server firewalls zijn opzettelijk onveilig voor demonstratie doeleinden. We raden u aan een nieuwe resource groep te maken. Gebruik geen bestaande resource groepen,-servers of-groepen. Gebruik deze toepassing, scripts of enige geïmplementeerde resources niet voor productie. Verwijder deze resource groep wanneer u klaar bent met de toepassing om gerelateerde facturering te stoppen.

    - **Resource groep**: Selecteer **nieuwe maken**en geef de unieke naam op die u eerder hebt gekozen voor de resource groep.
    - **Locatie**: Selecteer een locatie in de vervolg keuzelijst.
    - **Gebruiker**: gebruik de waarde voor de gebruikers naam die u eerder hebt gekozen.

1. De toepassing implementeren.

    a. Selecteer deze optie om de voor waarden te accepteren.

    b. Selecteer **Aankoop**.

1. Als u de implementatie status wilt controleren, selecteert u **meldingen** (het klok pictogram rechts van het zoekvak). De implementatie van de SaaS-app van Wingtip tickets neemt ongeveer vijf minuten in beslag.

   ![Implementatie is voltooid](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>De beheer scripts voor Wingtip tickets downloaden en deblokkeren

Wanneer de toepassing wordt geïmplementeerd, downloadt u de bron code-en beheer scripts.

> [!IMPORTANT]
> Uitvoer bare inhoud (scripts en Dll's) wordt mogelijk geblokkeerd door Windows wanneer zip-bestanden worden gedownload vanaf een externe bron en geëxtraheerd. Volg de stappen voor het deblokkeren van het zip-bestand voordat u de scripts uitpakt. Als u de blok kering opheffen, zorgt u ervoor dat de scripts mogen worden uitgevoerd.

1. Blader naar de [WingtipTicketsSaaS-DbPerTenant github opslag plaats][github-wingtip-dpt].
1. Selecteer **Klonen of downloaden**.
1. Selecteer **zip downloaden**en sla het bestand op.
1. Klik met de rechter muisknop op het bestand **WingtipTicketsSaaS-DbPerTenant-Master. zip** en selecteer **Eigenschappen**.
1. Selecteer op het tabblad **Algemeen** de optie **blok kering opheffen** > **Toep assen**.
1. Selecteer **OK**en pak de bestanden uit

De scripts bevinden zich in de map...\\WingtipTicketsSaaS-DbPerTenant-Master\\learning modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Het gebruikers configuratie bestand voor deze implementatie bijwerken

Voordat u scripts uitvoert, moet u de waarden van de resource groep en de gebruiker in het gebruikers configuratie bestand bijwerken. Stel deze variabelen in op de waarden die u tijdens de implementatie hebt gebruikt.

1. Open in de Power shell ISE-learning modules\\**userconfig. psm1** ...\\
1. **ResourceGroupName** en **naam** bijwerken met de specifieke waarden voor uw implementatie (alleen op regels 10 en 11).
1. Sla de wijzigingen op.

Er wordt in bijna elk script naar deze waarden verwezen.

## <a name="run-the-application"></a>De toepassing uitvoeren

De app geeft een overzicht van locaties die gebeurtenissen hosten. Locatie typen zijn onder andere concert huizen, Jazz klaveren en sport clubs. In Wingtip-tickets worden locaties geregistreerd als tenants. Een Tenant geeft u een eenvoudige manier om gebeurtenissen weer te geven en om tickets aan hun klanten te verkopen. Elke locatie krijgt een gepersonaliseerde website om hun evenementen weer te geven en om tickets te verkopen.

In de app wordt intern een SQL database geïmplementeerd in een elastische SQL-pool.

Een centrale **gebeurtenis hub** pagina bevat een lijst met koppelingen naar de tenants in uw implementatie.

1. Gebruik de URL om de evenementen hub in uw webbrowser te openen: http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net. Vervang &lt;gebruiker&gt; door de gebruikers waarde van uw implementatie.

    ![Events hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Selecteer **fabrikam Jazz Club** in de evenementen hub.

    ![Gebeurtenissen](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

De Wingtip-toepassing maakt gebruik van [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) om de distributie van binnenkomende aanvragen te beheren. De URL voor toegang tot de gebeurtenissen pagina voor een specifieke Tenant gebruikt de volgende indeling:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    De delen van de voor gaande notatie worden beschreven in de volgende tabel.

    | URL-onderdeel        | Beschrijving       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | De gebeurtenissen delen van de Wingtip-app.<br /><br /> *-dpt* onderscheidt de implementatie van de *Data Base-per-Tenant* van Wingtip-tickets van andere implementaties. Voor beelden zijn de implementaties van *één* app-per-Tenant ( *-sa*) of *multi tenant-data base* ( *-MT*). |
    | . *gebruikers&gt;&lt;* | *AF1* in het voor beeld. |
    | .trafficmanager.net/ | Traffic Manager, basis-URL. |
    | fabrikamjazzclub | Identificeert de Tenant met de naam fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- De naam van de Tenant wordt geparseerd uit de URL door de gebeurtenissen-app.
- De naam van de Tenant wordt gebruikt om een sleutel te maken.
- De sleutel wordt gebruikt om toegang te krijgen tot de catalogus om de locatie van de data base van de Tenant op te halen.
  - De catalogus wordt geïmplementeerd met behulp van *Shard-toewijzings beheer*.
- De Events hub gebruikt uitgebreide meta gegevens in de catalogus om de lijst met Url's voor elke Tenant op te bouwen.

In een productie omgeving maakt u doorgaans een CNAME DNS-record om het [*Internet domein van een bedrijf*](../traffic-manager/traffic-manager-point-internet-domain.md) te laten verwijzen naar de Traffic Manager DNS-naam.

> [!NOTE]
> Het is mogelijk niet onmiddellijk duidelijk wat het gebruik van Traffic Manager is in deze zelf studie. Het doel van deze reeks zelf studies is het demonstreren van patronen die de schaal van een complexe productie omgeving kunnen afhandelen. In een dergelijk geval hebt u bijvoorbeeld meerdere web-apps verdeeld over de hele wereld, naast elkaar met data bases en hebt u Traffic manager nodig om tussen deze instanties te routeren.
Een andere set zelf studies die het gebruik van Traffic Manager illustreert hoewel de [geo-Restore](saas-dbpertenant-dr-geo-restore.md) en de [geo-replicatie](saas-dbpertenant-dr-geo-replication.md) zelf studies zijn. In deze zelf studies wordt Traffic Manager gebruikt om over te scha kelen naar een herstel exemplaar van de SaaS-app in het geval van een regionale storing.

## <a name="start-generating-load-on-the-tenant-databases"></a>Workloads genereren voor de tenant-databases

Nu de app is geïmplementeerd, kunt u deze gebruiken.

Het Power shell *-script demo-LoadGenerator* start een werk belasting die wordt uitgevoerd op alle Tenant databases. De werkelijke belasting van veel SaaS-apps is sporadisch en onvoorspelbaar. Voor het simuleren van dit type belasting, produceert de generator een belasting met wille keurige pieken of bursts van de activiteit op elke Tenant. De bursts treden op in wille keurige intervallen. Het duurt enkele minuten voordat het laad patroon zich voordoet. Laat de generator ten minste drie of vier minuten uitvoeren voordat u de belasting bewaken.

1. Open in de Power shell-ISE de modules...\\learning modules\\Utilities\\script van *demo-LoadGenerator. ps1* .
2. Druk op F5 om het script uit te voeren en de load generator te starten. Wijzig de standaard parameter waarden nu.
3. Meld u aan bij uw Azure-account en selecteer het abonnement dat u wilt gebruiken, indien nodig.

Met het script laad generator wordt een achtergrond taak voor elke data base in de catalogus gestart en vervolgens gestopt. Als u het script voor de laad Generator opnieuw uitvoert, worden alle achtergrond taken gestopt die worden uitgevoerd voordat nieuwe worden gestart.

### <a name="monitor-the-background-jobs"></a>De achtergrond taken bewaken

Als u de achtergrond taken wilt beheren en controleren, gebruikt u de volgende cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator. ps1-acties

*Demo-LoadGenerator. ps1* imiteert een actieve werk belasting van klant transacties. De volgende stappen beschrijven de volg orde van de acties die door *demo-LoadGenerator. ps1* worden gestart:

1. *Demo-LoadGenerator. ps1* start *LoadGenerator. ps1* op de voor grond.

    - Beide. ps1-bestanden worden opgeslagen in de mappen learning modules\\-Hulpprogram Ma's\\.

2. *LoadGenerator. ps1* loopt door alle Tenant databases in de catalogus.

3. *LoadGenerator. ps1* start een Power shell-taak op de achtergrond voor elke Tenant database:

    - De achtergrond taken worden standaard gedurende 120 minuten uitgevoerd.
    - Elke taak veroorzaakt een op CPU gebaseerde belasting voor één Tenant database door *sp_CpuLoadGenerator*uit te voeren. De intensiteit en duur van de belasting variëren afhankelijk van `$DemoScenario`.
    - *sp_CpuLoadGenerator* lussen rond een SQL SELECT-instructie die een hoge CPU-belasting veroorzaakt. Het tijds interval tussen de problemen van de SELECT is afhankelijk van de parameter waarden voor het maken van een instelbaar CPU-belasting. Laad niveaus en intervallen worden wille keurig voor het simuleren van meer realistische belasting.
    - Dit. SQL-bestand wordt opgeslagen onder *WingtipTenantDB\\dbo\\StoredProcedures\\* .

4. Als `$OneTime = $false`, start de load Generator de achtergrond taken en gaat vervolgens verder met uitvoeren. Elke 10 seconden wordt gecontroleerd op nieuwe tenants die zijn ingericht. Als u `$OneTime = $true`instelt, start de LoadGenerator de achtergrond taken en stopt de uitvoering op de voor grond. Voor deze zelf studie moet u `$OneTime = $false`laten staan.

   Gebruik CTRL + C of stop de bewerking CTRL-outlock als u de load generator wilt stoppen of opnieuw wilt starten.

   Als u de load-generator op de voor grond uitvoert, gebruikt u een ander Power shell ISE-exemplaar om andere Power shell-scripts uit te voeren.

&nbsp;

Voordat u verdergaat met de volgende sectie, moet u de belasting Generator laten uitvoeren in de status van de taak die wordt aangeroepen.

## <a name="provision-a-new-tenant"></a>Een nieuwe tenant inrichten

Bij de eerste implementatie worden drie voor beelden van tenants gemaakt. U maakt nu een andere Tenant om de impact op de geïmplementeerde toepassing te bekijken. In de Wingtip-app wordt de werk stroom voor het inrichten van nieuwe tenants uitgelegd in de [zelf studie voor het inrichten en de catalogus](saas-dbpertenant-provision-and-catalog.md). In deze fase maakt u een nieuwe Tenant die minder dan een minuut in beslag neemt.

1. Open een nieuwe Power shell-ISE.
2. Open...\\Learning Modules\Provision en Catalog\\*demo-ProvisionAndCatalog. ps1*.
3. Druk op F5 om het script uit te voeren. Behoud de standaard waarden voor nu.

   > [!NOTE]
   > Veel Wingtip SaaS-scripts gebruiken *$PSScriptRoot* om door mappen te bladeren om functies in andere scripts aan te roepen. Deze variabele wordt alleen geëvalueerd wanneer het volledige script wordt uitgevoerd door op F5 te drukken. Het markeren en uitvoeren van een selectie met F8 kan fouten veroorzaken. Als u de scripts wilt uitvoeren, drukt u op F5.

De nieuwe Tenant database is:

- Gemaakt in een elastische SQL-pool.
- Geïnitialiseerd.
- Geregistreerd in de catalogus.

Nadat het inrichten is voltooid, wordt de site met *gebeurtenissen* van de nieuwe Tenant weer gegeven in uw browser.

![Nieuwe tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Vernieuw de Events hub om te zorgen dat de nieuwe Tenant wordt weer gegeven in de lijst.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Servers, pools en tenant-databases

Nu u bent begonnen met het uitvoeren van een belasting voor de verzameling tenants, kijken we naar enkele van de resources die zijn geïmplementeerd.

1. Blader in het [Azure Portal](https://portal.azure.com)naar de lijst met SQL-servers. Open vervolgens de **&gt;** -server voor de&lt;van de catalogus.
    - De catalogus server bevat twee data bases: **tenantcatalog** en **basetenantdb** (een sjabloon database die is gekopieerd om nieuwe tenants te maken).

   ![Databases](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Ga terug naar de lijst met SQL-servers.

3. Open de **tenants1-dpt-&lt;gebruiker&gt;-** server die de Tenant-data bases bevat.

4. Bekijk de volgende items:

    - Elke Tenant database is een **elastische Standard** -data base in een 50-eDTU Standard-pool.
    - De Red esdoorn race-data base is de Tenant database die u eerder hebt ingericht.

   ![Server met data bases](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>De pool bewaken

Nadat *LoadGenerator. ps1* enkele minuten is uitgevoerd, moeten er voldoende gegevens beschikbaar zijn om te kijken naar sommige bewakings mogelijkheden. Deze mogelijkheden zijn ingebouwd in Pools en data bases.

Blader naar de server **tenants1-dpt-&lt;gebruikers&gt;** en selecteer **Pool1** om het resource gebruik voor de groep weer te geven. In de volgende grafieken is de load Generator één uur uitgevoerd.

   ![Groep bewaken](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Het eerste diagram, het **resource gebruik**met een label, toont het gebruik van de pool-eDTU.
- Het tweede diagram toont het eDTU-gebruik van de vijf meest actieve data bases in de pool.

De twee grafieken illustreren dat elastische Pools en SQL Database goed zijn afgestemd op onvoorspelbare SaaS-toepassings werkbelastingen. In de grafieken ziet u dat vier data bases elke bursting tot 40 Edtu's hebben en dat alle data bases kunnen worden ondersteund door een 50-eDTU-groep. De 50-eDTU-groep kan zelfs zware werk belastingen ondersteunen. Als de data bases zijn ingericht als afzonderlijke data bases, moet elk een S2 (50 DTU) zijn voor de ondersteuning van de bursts. De kosten van vier enkele S2-data bases zijn bijna drie maal de prijs van de pool. In praktijk situaties SQL Database klanten Maxi maal 500 data bases uitvoeren in 200 eDTU-groepen. Zie voor meer informatie de [zelf studie voor prestatie bewaking](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie voor meer informatie aanvullende [zelf studies over het bouwen van de Wingtip tickets SaaS data base-per-Tenant toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Zie [Wat is een elastische Azure SQL-pool?](sql-database-elastic-pool.md)voor meer informatie over elastische Pools.
- Zie [uitgeschaalde Cloud databases beheren](elastic-jobs-overview.md)voor meer informatie over elastische taken.
- Zie [ontwerp patronen voor SaaS-toepassingen met meerdere tenants voor](saas-tenancy-app-design-patterns.md)meer informatie over SaaS-toepassingen met meerdere tenants.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - De SaaS-toepassing Wingtip tickets implementeren.
> - Over de servers, Pools en data bases die de app vormen.
> - Hoe tenants worden toegewezen aan hun gegevens met de *catalogus*.
> - Het inrichten van nieuwe tenants.
> - Pool gebruik weer geven voor het bewaken van Tenant activiteit.
> - Voorbeeld resources verwijderen om gerelateerde facturering te stoppen.

Probeer vervolgens de [zelf studie voor het inrichten en de catalogus](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
