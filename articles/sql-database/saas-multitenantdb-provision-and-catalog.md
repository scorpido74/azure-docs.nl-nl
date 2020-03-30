---
title: Voorziening in SaaS multi-tenant
description: Informatie over het inrichten en catalogiseren van nieuwe tenants in een Azure SQL Database multi-tenant SaaS-app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
ms.date: 09/24/2018
ms.openlocfilehash: 4ea18ee23d845b2d16209b23de14dc3cd70aaa59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133148"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Nieuwe tenants inrichten en catalogiseren in een SaaS-toepassing met behulp van een geshard Azure SQL-database met meerdere tenants

Dit artikel behandelt het inrichten en catalogiseren van nieuwe tenants, in een *multi-tenant geshard databasemodel* of -patroon.

Dit artikel bestaat uit twee belangrijke delen:

- [Conceptuele bespreking](#goto_2_conceptual) van de inrichting en catalogisering van nieuwe huurders.

- [Zelfstudie](#goto_1_tutorial) die de PowerShell-scriptcode markeert die de inrichting en catalogisering bereikt.
  - De tutorial maakt gebruik van de Wingtip Tickets SaaS applicatie, aangepast aan de multi-tenant sharded database patroon.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Databasepatroon

Deze sectie, plus een paar meer die volgen, bespreken de concepten van de multi-tenant sharded database patroon.

In dit geshard model met meerdere tenant's bevatten de tabelschema's in elke database een tenantsleutel in de primaire sleutel van tabellen die tenantgegevens opslaan. Met de tenantsleutel kan elke afzonderlijke database 0, 1 of veel tenants opslaan. Het gebruik van geshard databases maakt het eenvoudig voor het applicatiesysteem om een zeer groot aantal tenants te ondersteunen. Alle gegevens voor één tenant worden opgeslagen in één database. Het grote aantal tenants wordt verdeeld over de vele geshard databases. Een catalogusdatabase slaat de toewijzing van elke tenant op in de database.

#### <a name="isolation-versus-lower-cost"></a>Isolatie versus lagere kosten

Een huurder die een database heeft, geniet van de voordelen van isolatie. De tenant kan de database laten herstellen naar een eerdere datum zonder te worden beperkt door de impact op andere tenants. Databaseprestaties kunnen worden afgestemd om te optimaliseren voor de ene tenant, opnieuw zonder compromissen te hoeven sluiten met andere tenants. Het probleem is dat isolatie meer kost dan het kost om een database te delen met andere tenants.

Wanneer een nieuwe tenant is ingericht, kan deze een database delen met andere tenants of deze in een eigen nieuwe database worden geplaatst. Later u van gedachten veranderen en de database verplaatsen naar de andere situatie.

Databases met meerdere tenants en enkele tenants worden gemengd in dezelfde SaaS-toepassing, om de kosten of isolatie voor elke tenant te optimaliseren.

   ![Gesharde database-app met meerdere tenant met tenantcatalogus](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Patroon tenantcatalogus

Wanneer u twee of meer databases hebt die elk ten minste één tenant bevatten, moet de toepassing een manier hebben om te ontdekken welke database de tenant van huidige interesse opslaat. Een catalogusdatabase slaat deze toewijzing op.

#### <a name="tenant-key"></a>Tenantsleutel

Voor elke tenant kan de Wingtip-toepassing een unieke sleutel afleiden, namelijk de tenantsleutel. De app haalt de tenantnaam uit de URL van de webpagina. De app hashes de naam om de sleutel te verkrijgen. De app gebruikt de sleutel om toegang te krijgen tot de catalogus. De catalogus verwijst door gegevens over de database waarin de tenant is opgeslagen. De app gebruikt de databasegegevens om verbinding te maken. Andere tenant sleutel schema's kunnen ook worden gebruikt.

Met behulp van een catalogus kan de naam of locatie van een tenantdatabase worden gewijzigd na inprovisioning zonder de toepassing te verstoren. In een databasemodel met meerdere tenant's biedt de catalogus plaats aan het verplaatsen van een tenant tussen databases.

#### <a name="tenant-metadata-beyond-location"></a>Tenantmetagegevens buiten locatie

De catalogus kan ook aangeven of een tenant offline is voor onderhoud of andere acties. En de catalogus kan worden uitgebreid om extra tenant- of databasemetagegevens op te slaan, zoals de volgende items:
- De servicelaag of -editie van een database.
- De versie van het databaseschema.
- De tenantnaam en de SLA (servicelevelovereenkomst).
- Informatie om applicatiebeheer, klantenondersteuning of devops-processen mogelijk te maken.

De catalogus kan ook worden gebruikt om rapportage met meerdere tenants, schemabeheer en gegevensextract in te schakelen voor analysedoeleinden.

### <a name="elastic-database-client-library"></a>Elastische databaseclientbibliotheek

In Wingtip wordt de catalogus geïmplementeerd in de *tenantcatalogusdatabase.* De *tenantcatalogus* wordt gemaakt met behulp van de shardbeheerfuncties van de [Elastic Database Client Library (EDCL).](sql-database-elastic-database-client-library.md) De bibliotheek stelt een toepassing in staat om een *shardkaart* te maken, te beheren en te gebruiken die in een database is opgeslagen. Een shardkaart verwijst door naar de tenantsleutel met zijn shard, wat de sharded database betekent.

Tijdens het inrichten van de tenant kunnen EDCL-functies worden gebruikt vanuit toepassingen of PowerShell-scripts om de vermeldingen in de shardkaart te maken. Later kunnen de EDCL-functies worden gebruikt om verbinding te maken met de juiste database. De EDCL caches verbindingsgegevens om het verkeer op de catalogusdatabase te minimaliseren en het proces van verbinding te versnellen.

> [!IMPORTANT]
> Bewerk de gegevens in de catalogusdatabase *niet* via directe toegang! Directe updates worden niet ondersteund vanwege het hoge risico op gegevensbeschadiging. Bewerk in plaats daarvan de kaartgegevens met alleen EDCL API's.

## <a name="tenant-provisioning-pattern"></a>Inrichtingspatroon voor huurder

#### <a name="checklist"></a>Controlelijst

Wanneer u een nieuwe tenant wilt inrichten in een bestaande gedeelde database, moet u van de gedeelde database de volgende vragen stellen:
- Heeft het genoeg ruimte over voor de nieuwe huurder?
- Heeft het tabellen met de nodige referentiegegevens voor de nieuwe tenant, of kunnen de gegevens worden toegevoegd?
- Heeft het de juiste variatie van het basisschema voor de nieuwe tenant?
- Is het op de juiste geografische locatie dicht bij de nieuwe huurder?
- Is het op de juiste service laag voor de nieuwe huurder?

Wanneer u wilt dat de nieuwe tenant wordt geïsoleerd in de eigen database, u deze maken om te voldoen aan de specificaties voor de tenant.

Nadat de inrichting is voltooid, moet u de tenant registreren in de catalogus. Ten slotte kan de tenanttoewijzing worden toegevoegd om naar de juiste shard te verwijzen.

#### <a name="template-database"></a>Sjabloondatabase

Inrichten van de database door SQL-scripts uit te voeren, een bacpac te implementeren of een sjabloondatabase te kopiëren. De Wingtip-apps kopiëren een sjabloondatabase om nieuwe tenantdatabases te maken.

Zoals elke toepassing zal Wingtip in de loop van de tijd evolueren. Soms vereist Wingtip wijzigingen in de database. Wijzigingen kunnen de volgende items omvatten:
- Nieuw of gewijzigd schema.
- Nieuwe of gewijzigde referentiegegevens.
- Routinedatabaseonderhoudstaken om optimale app-prestaties te garanderen.

Bij een SaaS-toepassing moeten deze wijzigingen op een gecoördineerde wijze worden geïmplementeerd in een mogelijk enorme reeks tenantdatabases. Om deze wijzigingen in toekomstige tenantdatabases te kunnen vinden, moeten ze worden opgenomen in het inrichtingsproces. Deze uitdaging wordt verder onderzocht in de [schema management tutorial](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

De tenant die scripts in deze zelfstudie indient, ondersteunt beide volgende scenario's:
- Een tenant inrichten in een bestaande database die wordt gedeeld met andere tenants.
- Het inrichten van een tenant in de eigen database.

Tenantgegevens worden vervolgens geïnitialiseerd en geregistreerd in de catalogusshardkaart. In de voorbeeld-app krijgen databases die meerdere tenants bevatten een algemene naam, zoals *tenants1* of *tenants2.* Databases met één tenant krijgen de naam van de tenant. De specifieke naamgevingsconventies die in het voorbeeld worden gebruikt, zijn geen essentieel onderdeel van het patroon, omdat het gebruik van een catalogus het mogelijk maakt om elke naam aan de database toe te wijzen.

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Zelfstudie begint

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een tenant ineenzetten in een database met meerdere tenants
> * Een tenant ineenzetten in een database met één tenant
> * Een partij tenants inrichten in zowel databases met meerdere tenants als met één tenant
> * Een database- en tenanttoewijzing registreren in een catalogus

#### <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

- Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

- De Wingtip Tickets SaaS Multi-tenant Database app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Multi-tenant Database-toepassing implementeren en verkennen](saas-multitenantdb-get-started-deploy.md) als u deze in minder dan vijf minuten wilt implementeren.

- Download de Wingtip-scripts en broncode:
    - De Wingtip Tickets SaaS Multi-tenant Database scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub repo.
    - Zie de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip-scripts te downloaden en te deblokkeren.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Een tenant ineenzetten in een database *die wordt gedeeld* met andere tenants

In deze sectie ziet u een lijst met de belangrijkste acties voor het inrichten die worden uitgevoerd door de PowerShell-scripts. Vervolgens gebruikt u de PowerShell ISE-foutopsporing om door de scripts te stappen om de acties in code te zien.

#### <a name="major-actions-of-provisioning"></a>Belangrijke acties voor de voorziening

De volgende zijn de belangrijkste elementen van de inrichtingsworkflow die u doorloopt:

- **De nieuwe tenantsleutel berekenen**: Er wordt een hash-functie gebruikt om de tenantsleutel te maken op basis van de tenantnaam.
- **Controleer of de tenantsleutel al bestaat:** de catalogus wordt gecontroleerd om te controleren of de sleutel nog niet is geregistreerd.
- **Tenant initialiseren in de standaardtenantdatabase:** de tenantdatabase wordt bijgewerkt om de nieuwe tenantgegevens toe te voegen.
- **Tenant registreren in de catalogus**: De toewijzing tussen de nieuwe tenantsleutel en de bestaande tenants1-database wordt toegevoegd aan de catalogus.
- **De naam van de tenant toevoegen aan een tabel met catalogusextensie:** de locatienaam wordt toegevoegd aan de tabel Tenants in de catalogus.  Deze toevoeging laat zien hoe de catalogusdatabase kan worden uitgebreid om aanvullende toepassingsspecifieke gegevens te ondersteunen.
- **Open evenementen pagina voor de nieuwe huurder**: De *Bushwillow Blues* evenementen pagina wordt geopend in de browser.

   ![events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Foutopsporingstappen

Als u wilt begrijpen hoe de Wingtip-app nieuwe tenantprovisioning implementeert in een gedeelde database, voegt u een breekpunt toe en stapt u door de werkstroom:

1. In de *PowerShell ISE*, open ... \\Learning\\Modules\\ProvisionTenants*Demo-ProvisionTenants.ps1* en stel de volgende parameters:
   - **$TenantName** = **Bushwillow Blues**, de naam van een nieuwe locatie.
   - **$VenueType** = **blues**, een van de vooraf gedefinieerde locatie types: blues, klassiekemuziek, dans, jazz, judo, motorracing, multipurpose, opera, rockmuziek, voetbal (kleine letters, geen ruimtes).
   - **$DemoScenario** = **1**, om een tenant in een gedeelde database te voorzien van andere tenants.

2. Voeg een breekpunt toe door de cursor overal op regel 38 te zetten, de regel met de tekst: *Nieuw-Tenant*en druk vervolgens op **F9**.

   ![onderbrekingspunt](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Voer het script uit door op **F5**te drukken.

4. Nadat de uitvoering van het script is gestopt bij het breekpunt, drukt u op **F11** om in de code te stappen.

   ![foutopsporing](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Traceer de uitvoering van het script met de menuopties **Foutopsporing,** **F10** en **F11,** om over of in aangeroepen functies te stappen.

Zie Tips voor het werken met [PowerShell-scripts voor](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)meer informatie over het debuggen van PowerShell-scripts.

## <a name="provision-a-tenant-in-its-own-database"></a>Een tenant inrichten in de *eigen* database

#### <a name="major-actions-of-provisioning"></a>Belangrijke acties voor de voorziening

De volgende zijn de belangrijkste elementen van de werkstroom die u doorloopt tijdens het traceren van het script:

- **De nieuwe tenantsleutel berekenen**: Er wordt een hash-functie gebruikt om de tenantsleutel te maken op basis van de tenantnaam.
- **Controleer of de tenantsleutel al bestaat:** de catalogus wordt gecontroleerd om te controleren of de sleutel nog niet is geregistreerd.
- **Een nieuwe tenantdatabase maken:** de database wordt gemaakt door de *basetenantdb-database* te kopiëren met behulp van een Resource Manager-sjabloon.  De nieuwe databasenaam is gebaseerd op de naam van de tenant.
- **Database toevoegen aan catalogus**: De nieuwe tenantdatabase wordt geregistreerd als een scherf in de catalogus.
- **Tenant initialiseren in de standaardtenantdatabase:** de tenantdatabase wordt bijgewerkt om de nieuwe tenantgegevens toe te voegen.
- **Register tenant in de catalogus**: De toewijzing tussen de nieuwe tenantsleutel en de *sequoiasoccer-database* wordt toegevoegd aan de catalogus.
- **Tenantnaam wordt toegevoegd aan de catalogus:** de locatienaam wordt toegevoegd aan de extensietabel Tenants in de catalogus.
- **Open de pagina Gebeurtenissen voor de nieuwe tenant**: De pagina Sequoia *Voetbalgebeurtenissen* wordt geopend in de browser.

   ![events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Foutopsporingstappen

Loop nu door het scriptproces bij het maken van een tenant in de eigen database:

1. Nog steeds in ... \\Learning\\Modules\\ProvisionTenants*Demo-ProvisionTenants.ps1* stel de volgende parameters in:
   - **$TenantName** = **Sequoia Soccer**, de naam van een nieuwe locatie.
   - **$VenueType** = **voetbal**, een van de vooraf gedefinieerde locatie types: blues, klassiekemuziek, dans, jazz, judo, motorracing, multifunctionele, opera, rockmuziek, voetbal (kleine letters, geen ruimtes).
   - **$DemoScenario** = **2**, om een tenant in zijn eigen database te plaatsen.

2. Voeg een nieuw breekpunt toe door de cursor overal op regel 57 te zetten, de regel met de tekst: * & &nbsp;$PSScriptRoot\Nieuw-TenantAndDatabase '* en druk op **F9**.

   ![onderbrekingspunt](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Voer het script uit door op **F5**te drukken.

4. Nadat de uitvoering van het script is gestopt bij het breekpunt, drukt u op **F11** om in de code te stappen.  Gebruik **F10** en **F11** om over te stappen en stap in functies om de uitvoering te traceren.

## <a name="provision-a-batch-of-tenants"></a>Een partij huurders voorzien

Deze oefening voorziet in een partij van 17 huurders. Het is aanbevolen u deze batch van huurders in te richten voordat u begint met andere Wingtip Tickets tutorials, zodat er meer databases om mee te werken.

1. In de *PowerShell ISE*, open ... \\Learning\\Modules\\ProvisionTenants*Demo-ProvisionTenants.ps1* en verander de *parameter $DemoScenario* in 4:
   - **$DemoScenario** = **4**, om een partij van huurders in een gedeelde database.

2. Druk op **F5** om het script uit te voeren.

### <a name="verify-the-deployed-set-of-tenants"></a>De geïmplementeerde set tenants verifiëren

In dit stadium hebt u een mix van tenants geïmplementeerd in een gedeelde database en tenants geïmplementeerd in hun eigen databases. De Azure-portal kan worden gebruikt om de gemaakte databases te inspecteren. Open in de [Azure-portal](https://portal.azure.com)de **\<gebruikersserver\> van tenants1 mt- door** te bladeren naar de lijst met SQL-servers.  De **sql-databaseslijst** moet de gedeelde **tenants1-database** en de databases voor de tenants die zich in hun eigen database bevinden, bevatten:

   ![databaselijst](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Terwijl de Azure-portal de tenantdatabases weergeeft, u de tenants *in* de gedeelde database niet zien. De volledige lijst met huurders is te zien op de website van de **Evenementenhub** van Wingtip en door in de catalogus te bladeren.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Hubpagina van De gebeurtenissen van Wingtip Tickets gebruiken

Open de pagina Gebeurtenissenhub in de browser (http:events.wingtip-mt.\<USER\>.trafficmanager.net)

#### <a name="using-catalog-database"></a>Catalogusdatabase gebruiken

De volledige lijst met tenants en de bijbehorende database voor elk is beschikbaar in de catalogus. Er wordt een SQL-weergave weergegeven die de tenantnaam aan de databasenaam verbindt. De weergave toont mooi de waarde van het uitbreiden van de metadata die is opgeslagen in de catalogus.
- De SQL-weergave is beschikbaar in de tenantcatalogusdatabase.
- De tenantnaam wordt opgeslagen in de tabel Tenants.
- De databasenaam wordt opgeslagen in de tabellen Shardbeheer.

1. Maak in SQL Server Management Studio (SSMS) verbinding met de tenantsserver op **catalogus-mt.\<GEBRUIKER\>.database.windows.net**, met Login = **ontwikkelaar**en Wachtwoord = P **\@zwaard1**

    ![Dialoogvenster SSMS-verbinding](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. Blader in de SSMS-objectverkenner naar de weergaven in de *tenantcatalogusdatabase.*

3. Klik met de rechtermuisknop op de weergave *TenantsExtended* en kies **Top 1000 Rijen selecteren**. Let op de toewijzing tussen tenantnaam en database voor de verschillende tenants.

    ![Uitgebreide huurdersweergave in SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Andere inrichtingspatronen

Deze sectie bespreekt andere interessante inrichtingspatronen.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Databases vooraf inrichten in elastische pools

Het pre-provisioning patroon maakt gebruik van het feit dat bij het gebruik van elastische pools, facturering is voor de pool niet de databases. Zo kunnen databases worden toegevoegd aan een elastische pool voordat ze nodig zijn zonder extra kosten. Deze pre-visioning vermindert aanzienlijk de tijd die nodig is om een tenant in een database te voorzien. Het aantal vooraf ingerichte databanken kan zo nodig worden aangepast om een buffer geschikt te houden voor het verwachte inrichtingspercentage.

#### <a name="auto-provisioning"></a>Automatische inrichting

In het patroon voor automatische inrichting wordt een speciale inrichtingsservice gebruikt om servers, pools en databases automatisch in te richten als dat nodig is. Deze automatisering omvat het vooraf inrichten van databases in elastische pools. En als databases worden ontmanteld en verwijderd, kunnen de hiaten die dit creëert in elastische pools naar wens worden opgevuld door de inrichtingsservice.

Dit type geautomatiseerde service kan eenvoudig of complex zijn. De automatisering kan bijvoorbeeld de inrichting in meerdere regio's verwerken en kan geo-replicatie instellen voor herstel na noodgevallen. Met het patroon voor automatisch inrichten dient een clienttoepassing of -script een inrichtingsaanvraag in bij een wachtrij die door een inrichtingsservice moet worden verwerkt. Het script zou dan poll om voltooiing te detecteren. Als pre-provisioning wordt gebruikt, zouden aanvragen snel worden afgehandeld, terwijl een achtergrondservice de inrichting van een vervangende database beheert.

## <a name="additional-resources"></a>Aanvullende bronnen

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Clientbibliotheek voor Elastic Database](sql-database-elastic-database-client-library.md)
- [How to Debug Scripts in Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise) (Fouten opsporen in scripts met Windows PowerShell ISE)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een nieuwe tenant inrichten in een gedeelde multi-tenantdatabase en een eigen database
> * Verschillende tenants tegelijk inrichten
> * Stap door de details van de inrichting van huurders, en registreren ze in de catalogus

Probeer de [zelfstudie Prestatiecontrole](saas-multitenantdb-performance-monitoring.md).

