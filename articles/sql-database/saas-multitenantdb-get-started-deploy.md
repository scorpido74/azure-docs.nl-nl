---
title: 'Implementeer een Shard multi tenant-data base SaaS-app die gebruikmaakt van Azure SQL Database '
description: Implementeer en verken de Shard Wingtip tickets SaaS multi tenant-database toepassing, die SaaS-patronen demonstreert met behulp van Azure SQL Database.
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
ms.openlocfilehash: aa61c9af2e8fbfbe1caeaffb6231afe5b8be6f3c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692044"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Een Shard multi tenant-toepassing implementeren en verkennen

In deze zelf studie implementeert en bekijkt u een voor beeld van een multi tenant SaaS-toepassing met de naam Wingtip tickets. De Wingtip tickets-app is ontworpen om functies van Azure SQL Database te demonstreren waarmee de implementatie van SaaS-scenario's wordt vereenvoudigd.

Deze implementatie van de Wingtip tickets-app maakt gebruik van een Shard-database patroon met meerdere tenants. De sharding is op basis van de Tenant-id. Tenant gegevens worden naar een bepaalde data base gedistribueerd op basis van de Tenant-id-waarden. 

Met dit database patroon kunt u een of meer tenants in elke Shard of data base opslaan. U kunt optimaliseren voor de laagste kosten door elke Data Base te delen met meerdere tenants. U kunt ook optimaliseren voor isolatie door elke Data Base slechts één Tenant op te slaan. Uw optimalisatie keuze kan onafhankelijk worden gemaakt voor elke specifieke Tenant. Uw keuze kan worden gemaakt wanneer de Tenant voor het eerst wordt opgeslagen of u kunt later van gedachten veranderen. De toepassing is zo ontworpen dat ze goed werkt.

## <a name="app-deploys-quickly"></a>App kan snel worden geïmplementeerd

De app wordt uitgevoerd in de Azure-Cloud en maakt gebruik van Azure SQL Database. De sectie implementatie bevat de knop Blue **implementeren naar Azure** . Wanneer op de knop wordt geklikt, wordt de app binnen vijf minuten volledig geïmplementeerd in uw Azure-abonnement. U hebt volledige toegang tot het gebruik van de afzonderlijke toepassings onderdelen.

De toepassing wordt geïmplementeerd met gegevens voor drie voorbeeld tenants. De tenants worden samen in één data base met meerdere tenants opgeslagen.

Iedereen kan de C# Power shell-bron code voor Wingtip-tickets downloaden uit [de GitHub-opslag plaats][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Meer informatie over deze zelf studie

> [!div class="checklist"]
> - De SaaS-toepassing Wingtip tickets implementeren.
> - Waar de bron code van de toepassing en beheer scripts worden opgehaald.
> - Over de servers en data bases die de app vormen.
> - Hoe tenants worden toegewezen aan hun gegevens met de *catalogus*.
> - Hoe u een nieuwe Tenant inricht.
> - De Tenant activiteiten in de app bewaken.

Er is een reeks gerelateerde zelf studies beschikbaar die op deze eerste implementatie is gebaseerd. De zelf studies verkennen een reeks SaaS-ontwerp-en-beheer patronen. Wanneer u de zelf studies doorloopt, wordt u aangeraden de meegeleverde scripts stapsgewijs te door lopen om te zien hoe de verschillende SaaS-patronen worden geïmplementeerd.

## <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

- De nieuwste Azure PowerShell is geïnstalleerd. Zie [aan de slag met Azure PowerShell][link-azure-get-started-powershell-41q]voor meer informatie.

## <a name="deploy-the-wingtip-tickets-app"></a>De Wingtip tickets-app implementeren

### <a name="plan-the-names"></a>De namen plannen

In de stappen van deze sectie geeft u een *gebruikers* waarde op die wordt gebruikt om ervoor te zorgen dat resource namen wereld wijd uniek zijn, en een naam voor de *resource groep* die alle resources bevat die zijn gemaakt door een implementatie van de app. Voor een persoon met de naam *Anne Finley*raden we het volgende aan:
- *Gebruiker:* **AF1**  *(hun initialen, plus een cijfer. Gebruik een andere waarde (bijvoorbeeld AF2) als u de app een tweede keer implementeert.)*
- *Resource groep:* **Wingtip-MT-AF1** *(Wingtip-MT geeft aan dat dit de Shard-app voor meerdere tenants is. het toevoegen van de gebruikers naam AF1 correleert de naam van de resource groep met de namen van de resources die deze bevat.)*

Kies uw namen nu en noteer deze. 

### <a name="steps"></a>Stappen

1. Klik op de volgende knop Blue **implementeren naar Azure** .
   - De Azure Portal met de SaaS-implementatie sjabloon Wingtip tickets wordt geopend.

     [![Om te implementeren naar Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Voer de vereiste parameter waarden in voor de implementatie.

    > [!IMPORTANT]
    > Gebruik voor deze demonstratie geen vooraf bestaande resource groepen,-servers of-groepen. Kies in plaats daarvan **een nieuwe resource groep maken**. Verwijder deze resourcegroep wanneer u klaar bent om gerelateerde facturering te stoppen.
    > Gebruik deze toepassing of resources die worden gemaakt, niet voor productie. Sommige aspecten van verificatie en de firewall-instellingen van de server zijn opzettelijk onveilig in de app om de demonstratie te vergemakkelijken.

    - Voor **resource groep** : Selecteer **nieuwe maken**en geef een **naam** op voor de resource groep (hoofdletter gevoelig).
        - Selecteer een **locatie** in de vervolg keuzelijst.
    - Voor de **gebruiker** is het raadzaam om een korte **gebruikers** waarde te kiezen.

1. **Implementeer de toepassing**.

    - Klik om akkoord te gaan met de voor waarden.
    - Klik op **Kopen**.

1. Bewaak de implementatie status door te klikken op **meldingen**, het klok pictogram rechts van het zoekvak. De implementatie van de Wingtip-app duurt ongeveer vijf minuten.

   ![implementatie gelukt](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>De beheer scripts downloaden en deblokkeren

Wanneer de toepassing wordt geïmplementeerd, downloadt u de bron code-en beheer scripts van de toepassing.

> [!NOTE]
> Uitvoer bare inhoud (scripts, Dll's) wordt mogelijk door Windows geblokkeerd wanneer zip-bestanden worden gedownload vanaf een externe bron en geëxtraheerd. Wanneer u de scripts uit een zip-bestand uitpakt, gebruikt u de volgende stappen om het zip-bestand te deblokkeren voordat het wordt geëxtraheerd. Door de blok kering van het zip-bestand ontoegankelijk te maken, zorgt u ervoor dat de scripts mogen worden uitgevoerd.

1. Blader naar [de WingtipTicketsSaaS-MultiTenantDb github opslag plaats](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klik op **klonen of downloaden**.
3. Klik op **zip downloaden** en sla het bestand op.
4. Klik met de rechter muisknop op het bestand **WingtipTicketsSaaS-MultiTenantDb-Master. zip** en selecteer **Eigenschappen**.
5. Selecteer op het tabblad **Algemeen** de optie **blok kering opheffen**en klik op **Toep assen**.
6. Klik op **OK**.
7. Pak de bestanden uit.

De scripts bevinden zich in de map *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\* .

## <a name="update-the-configuration-file-for-this-deployment"></a>Het configuratie bestand voor deze implementatie bijwerken

Voordat u scripts uitvoert, stelt u de *resource groep* en *gebruikers* waarden in **userconfig. psm1**in. Stel deze variabelen in op dezelfde waarden die u tijdens de implementatie hebt ingesteld.

1. Open...\\learning modules\\*userconfig. psm1* in de *Power shell ISE*.
2. *ResourceGroupName* en *naam* bijwerken met de specifieke waarden voor uw implementatie (alleen op regels 10 en 11).
3. Sla de wijzigingen op.

De waarden die in dit bestand zijn ingesteld, worden gebruikt door alle scripts. het is dus belang rijk dat ze nauw keurig zijn. Als u de app opnieuw implementeert, moet u verschillende waarden kiezen voor de gebruiker en de resource groep. Werk vervolgens het bestand UserConfig. psm1 opnieuw bij met de nieuwe waarden.

## <a name="run-the-application"></a>De toepassing uitvoeren

In de Wingtip-app zijn de tenants locaties. Een locatie kan een concert zaal, een sport club of andere locaties zijn die gebeurtenissen hosten. De locaties registreren zich in Wingtip als klanten en er wordt een Tenant-id gegenereerd voor elke locatie. Elke locatie bevat een lijst met de aanstaande gebeurtenissen in Wingtip, zodat het publiek tickets kan kopen voor de gebeurtenissen.

Elke locatie haalt een gepersonaliseerde web-app op om hun evenementen weer te geven en kaarten te verkopen. Elke web-app is onafhankelijk en geïsoleerd van andere tenants. In Azure SQL Database worden de gegevens voor elke Tenant intern opgeslagen in een Shard multi tenant-data base. Alle gegevens worden gelabeld met de Tenant-id.

Op de webpagina centrale **gebeurtenissen hub** vindt u een lijst met koppelingen naar de tenants in uw specifieke implementatie. Gebruik de volgende stappen om de webpagina van de **evenementen hub** en een afzonderlijke web-app te ervaren:

1. Open de **evenementen hub** in uw webbrowser:
   - http://events.wingtip-mt.&lt; gebruiker&gt;. trafficmanager.net &nbsp; *(vervang &lt;gebruikers&gt; door de gebruikers waarde van uw implementatie.)*

     ![events hub](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klik in de **Events Hub** op **Fabrikam Jazz Club**.

   ![Gebeurtenissen](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

De Wingtip-app maakt gebruik van [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)om de distributie van binnenkomende aanvragen te beheren. De pagina gebeurtenissen voor elke Tenant bevat de Tenant naam in de URL. Elke URL bevat ook uw specifieke gebruikers waarde. Elke URL voldoet aan de weer gegeven indeling met behulp van de volgende stappen:

- http://events.wingtip-mt.&lt; gebruiker&gt;. trafficmanager.net/*fabrikamjazzclub*

1. De app Events parseert de Tenant naam van de URL. De naam van de Tenant is *fabrikamjazzclub* in de voor gaande voorbeeld-URL.
2. De app hasht vervolgens de Tenant naam om een sleutel te maken voor toegang tot een catalogus met behulp van [Shard-toewijzings beheer](sql-database-elastic-scale-shard-map-management.md).
3. De app zoekt de sleutel in de catalogus en haalt de bijbehorende locatie op van de data base van de Tenant.
4. De app gebruikt de locatie-informatie voor het zoeken en openen van de ene data base die alle gegevens voor de Tenant bevat.

### <a name="events-hub"></a>Events hub

1. De **Events hub** vermeldt alle tenants die in de catalogus zijn geregistreerd en hun locaties.
2. De **Events hub** gebruikt uitgebreide meta gegevens in de catalogus om de naam van de Tenant die is gekoppeld aan elke toewijzing, op te halen om de url's te maken.

In een productie omgeving maakt u doorgaans een CNAME DNS-record om het [Internet domein van een bedrijf](../traffic-manager/traffic-manager-point-internet-domain.md) te laten verwijzen naar het Traffic Manager-profiel.

## <a name="start-generating-load-on-the-tenant-databases"></a>Workloads genereren voor de tenant-databases

Nu de app is geïmplementeerd, kunt u het beste aan de slag. Het Power shell *-script demo-LoadGenerator* start een werk belasting die wordt uitgevoerd voor elke Tenant. De werkelijke belasting van veel SaaS-apps is doorgaans sporadisch en onvoorspelbaar. Voor het simuleren van dit type belasting, produceert de generator een taak die wordt gedistribueerd over alle tenants. De belasting bevat wille keurige bursts op elke Tenant die wordt uitgevoerd met wille keurige intervallen. Het duurt enkele minuten voordat het laad patroon wordt weer geven. u kunt de generator het beste gedurende ten minste drie of vier minuten uitvoeren voordat u de belasting bewaken.

1. Open in de *Power shell-ISE*de modules...\\learning modules\\Utilities\\script van *demo-LoadGenerator. ps1* .
2. Druk op **F5** om het script uit te voeren en de load-generator te starten (gebruik voorlopig de standaardwaarden van het script).

Het script *demo-LoadGenerator. ps1* opent een Power shell-sessie waarin de load generator wordt uitgevoerd. De load generator wordt in deze sessie uitgevoerd als een voorgrond taak waarmee taken voor het genereren van de achtergrond worden gestart, één voor elke Tenant.

Nadat de voorgrond taak is gestart, blijft deze in een status voor het aanroepen van taken. Met de taak worden extra achtergrond taken gestart voor nieuwe tenants die daarna worden ingericht.

Het sluiten van de Power shell-sessie stopt alle taken.

Mogelijk wilt u de load Generator-sessie opnieuw starten om andere parameter waarden te gebruiken. Als dit het geval is, sluit u de sessie van de Power shell-generatie en voert u de *demo-LoadGenerator. ps1*opnieuw uit.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Een nieuwe Tenant inrichten in de Shard-data base

De eerste implementatie omvat drie voor beelden van tenants in de *Tenants1* -data base. We gaan een andere Tenant maken en de gevolgen ervan voor de geïmplementeerde toepassing naleven. In deze stap drukt u op één toets om een nieuwe Tenant te maken:

1. Open...\\learning modules\\inrichten en Catalog\\*demo-ProvisionTenants. ps1* in de *Power shell ISE*.
2. Druk op **F5** (niet **F8**) om het script uit te voeren (behoud de standaard waarden voor nu).

   > [!NOTE]
   > U moet de Power shell-scripts alleen uitvoeren door op de toets **F5** te drukken, niet door op **F8** te drukken om een geselecteerd deel van het script uit te voeren. Het probleem met **F8** is dat de *$PSScriptRoot* variabele niet wordt geëvalueerd. Deze variabele is nodig voor veel scripts om mappen te navigeren, andere scripts aan te roepen of modules te importeren.

De nieuwe rode esdoorn race-Tenant wordt toegevoegd aan de *Tenants1* -data base en geregistreerd in de catalogus. De site met het ticket-verkoop **evenementen** van de nieuwe Tenant wordt geopend in uw browser:

![Nieuwe tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Vernieuw de **Events hub**en de nieuwe Tenant wordt nu weer gegeven in de lijst.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Een nieuwe Tenant inrichten in een eigen data base

Met het Shard multi tenant-model kunt u kiezen of u een nieuwe Tenant wilt inrichten in een Data Base met andere tenants of in een eigen data base. Een Tenant die is geïsoleerd in een eigen data base, geniet van de volgende voor delen:

- De prestaties van de data base van de Tenant kunnen worden beheerd zonder dat er moet worden geknoeid met de behoeften van andere tenants.
- De data base kan, indien nodig, worden hersteld naar een eerder tijdstip, omdat er geen andere tenants worden beïnvloed.

U kunt ervoor kiezen om klanten met een gratis proef versie of economie te plaatsen in data bases met meerdere tenants. U kunt elke Premium-Tenant in een eigen speciale data base plaatsen. Als u veel data bases maakt die slechts één Tenant bevatten, kunt u deze allemaal gezamenlijk beheren in een elastische pool om de resource kosten te optimaliseren.

Daarna inrichten we een andere Tenant, deze tijd in een eigen Data Base:

1. In...\\learning modules\\inrichten en Catalog\\*demo-ProvisionTenants. ps1*, Wijzig *$TenantName* in **Salix Salsa**, *$VenueType* naar **dans** en *$scenario* naar **2**.

2. Druk op **F5** om het script opnieuw uit te voeren.
    - Op **F5** drukken wordt de nieuwe Tenant in een afzonderlijke data base ingericht. De data base en de Tenant zijn geregistreerd in de catalogus. Vervolgens wordt de browser geopend op de pagina gebeurtenissen van de Tenant.

   ![Pagina Salix Salsa-gebeurtenissen](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Schuif naar de onderkant van de pagina. In de banner ziet u de naam van de Data Base waarin de Tenant gegevens zijn opgeslagen.

3. Vernieuw de **Events hub** en de twee nieuwe tenants worden nu weer gegeven in de lijst.

## <a name="explore-the-servers-and-tenant-databases"></a>De servers en Tenant databases verkennen

We kijken nu naar een aantal van de resources die zijn geïmplementeerd:

1. Blader in het [Azure Portal](https://portal.azure.com)naar de lijst met resource groepen. Open de resource groep die u hebt gemaakt tijdens het implementeren van de toepassing.

   ![resourcegroep](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klik op **Catalog-mt&lt;gebruiker&gt;** server. De catalogus server bevat twee data bases met de naam *tenantcatalog* en *basetenantdb*. De *basetenantdb* -data base is een lege sjabloon database. Het wordt gekopieerd om een nieuwe Tenant database te maken, ongeacht of deze wordt gebruikt voor veel tenants of slechts één Tenant.

   ![catalogusserver](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Ga terug naar de resource groep en selecteer de *tenants1-MT-* server die de Tenant databases bevat.
    - De tenants1-data base is een multi tenant-Data Base waarin de oorspronkelijke drie tenants, plus de eerste Tenant die u hebt toegevoegd, worden opgeslagen. Het is geconfigureerd als een 50 DTU-standaard database.
    - De **salixsalsa** -Data Base bevat de Salix Salsa dans-platform als enige Tenant. Het is standaard geconfigureerd als een Standard Edition-data base met 50 Dtu's.

   ![Tenant servers](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>De prestaties van de data base bewaken

Als de load-Generator enkele minuten actief is, is er voldoende telemetrie beschikbaar om te kijken naar de database bewakings functies die zijn ingebouwd in de Azure Portal.

1. Blader naar de **tenants1-mt&lt;gebruiker&gt;-** server en klik op **tenants1** om het resource gebruik weer te geven voor de data base die vier tenants bevat. Elke Tenant is onderhevig aan een zware belasting van de load Generator:

   ![tenants1 bewaken](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Het DTU-gebruiks diagram illustreert hoe een Data Base met meerdere tenants een onvoorspelbare werk belasting kan ondersteunen in veel tenants. In dit geval wordt de load-generator een sporadische belasting van ongeveer 30 Dtu's toegepast op elke Tenant. Deze belasting is gelijk aan 60% gebruik van een 50 DTU-data base. Pieken die 60% overschrijden, zijn het resultaat van de belasting die op hetzelfde moment wordt toegepast op meer dan één Tenant.

2. Blader naar de **tenants1-mt&lt;gebruiker&gt;-** server en klik op de **salixsalsa** -data base. U kunt het resource gebruik in deze data base zien die slechts één Tenant bevat.

   ![salixsalsa-data base](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

De load generator past een soort gelijke belasting toe op elke Tenant, ongeacht in welke data base elke Tenant zich bevindt. Met slechts één Tenant in de **salixsalsa** -Data Base kunt u zien dat de Data Base een veel hogere belasting zou kunnen ondervangen dan de data base met meerdere tenants. 

### <a name="resource-allocations-vary-by-workload"></a>Resource toewijzingen variëren per werk belasting

Soms vereist een multi tenant-data base meer bronnen voor goede prestaties dan een Data Base met één Tenant, maar niet altijd. De optimale toewijzing van resources is afhankelijk van de specifieke eigenschappen van de werk belasting voor de tenants in uw systeem.

De werk belastingen die door het load Generator-script worden gegenereerd, zijn alleen bedoeld ter illustratie.

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie [ontwerp patronen voor SaaS-toepassingen met meerdere](saas-tenancy-app-design-patterns.md)tenants voor meer informatie over SaaS-toepassingen met meerdere tenants.

- Zie voor meer informatie over elastische Pools:

  - [Elastische Pools helpen u bij het beheren en schalen van meerdere Azure SQL-data bases](sql-database-elastic-pool.md)
  - [Uitbreiden met Azure SQL Database](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> - De Wingtip tickets SaaS multi-tenant database toepassing implementeren.
> - Over de servers en data bases die de app vormen.
> - Tenants worden toegewezen aan hun gegevens met de *catalogus*.
> - Nieuwe tenants inrichten in een multi tenant-data base en een Data Base met één Tenant.
> - Pool gebruik weer geven voor het bewaken van Tenant activiteit.
> - Voorbeeld resources verwijderen om gerelateerde facturering te stoppen.

Probeer nu de [zelf studie voor het inrichten en de catalogus](sql-database-saas-tutorial-provision-and-catalog.md).


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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Om te implementeren in Azure."

