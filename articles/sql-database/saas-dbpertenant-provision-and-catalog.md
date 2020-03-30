---
title: Nieuwe tenants inrichten in een multitenant-app
description: Informatie over het inrichten en catalogiseren van nieuwe tenants in een Azure SQL Database multitenant SaaS-app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 6ec8f8835e925663fc6ac21a6eb1df09d6927109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132103"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Meer informatie over het inrichten van nieuwe tenants en registreren in de catalogus

In deze zelfstudie leert u hoe u SaaS-patronen in- en catalogiseert. U leert ook hoe ze worden geïmplementeerd in de Wingtip Tickets SaaS database-per-tenant applicatie. U maakt en initialiseert nieuwe tenantdatabases en registreert deze in de tenantcatalogus van de toepassing. De catalogus is een database die de toewijzing tussen de vele tenants van de SaaS-toepassing en hun gegevens bijhoudt. De catalogus speelt een belangrijke rol bij het aansturen van aanvragen en beheeraanvragen naar de juiste database.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Inrichten van een nieuwe huurder.
> * Inrichten van een partij extra huurders.


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets SaaS database-per-tenant app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS-database-per-tenant-toepassing implementeren en verkennen](saas-dbpertenant-get-started-deploy.md)om het in minder dan vijf minuten te implementeren.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-the-saas-catalog-pattern"></a>Inleiding tot het SaaS-cataloguspatroon

In een multitenant SaaS-toepassing met databaseondersteuning is het belangrijk om te weten waar informatie voor elke tenant wordt opgeslagen. In het SaaS-cataloguspatroon wordt een catalogusdatabase gebruikt om de toewijzing tussen elke tenant en de database waarin hun gegevens worden opgeslagen, vast te houden. Dit patroon is van toepassing wanneer tenantgegevens worden gedistribueerd over meerdere databases.

Elke tenant wordt geïdentificeerd door een sleutel in de catalogus, die is toegewezen aan de locatie van hun database. In de Wingtip Tickets-app wordt de sleutel gevormd uit een hash met de naam van de huurder. Met dit schema kan de app de sleutel bouwen op basis van de tenantnaam die is opgenomen in de url van de toepassing. Andere tenant sleutel schema's kunnen worden gebruikt.

De catalogus maakt het mogelijk de naam of locatie van de database te wijzigen met minimale impact op de toepassing. In een multitenant databasemodel biedt deze mogelijkheid ook plaats aan het verplaatsen van een tenant tussen databases. De catalogus kan ook worden gebruikt om aan te geven of een tenant of database offline is voor onderhoud of andere acties. Deze mogelijkheid wordt onderzocht in de [zelfstudie Voor één tenant herstellen](saas-dbpertenant-restore-single-tenant.md).

De catalogus kan ook extra tenant- of databasemetagegevens opslaan, zoals de schemaversie, het serviceplan of SLA's die aan tenants worden aangeboden. De catalogus kan andere informatie opslaan waarmee toepassingsbeheer, klantenondersteuning of DevOps kunnen worden beheerd.

Naast de SaaS-toepassing kan de catalogus databasehulpprogramma's inschakelen. In het voorbeeld van de Wingtip Tickets SaaS-database per tenant wordt de catalogus gebruikt om query's met meerdere tenant's in te schakelen, die wordt onderzocht in de [ad hoc-rapportage-zelfstudie](saas-tenancy-cross-tenant-reporting.md). Cross-database jobmanagement wordt onderzocht in de [schemabeheer-](saas-tenancy-schema-management.md) en [tenantanalyse-zelfstudies.](saas-tenancy-tenant-analytics.md)

In de Wingtip Tickets SaaS-samples wordt de catalogus geïmplementeerd met behulp van de Shard Management-functies van de [Elastic Database clientlibrary (EDCL).](sql-database-elastic-database-client-library.md) De EDCL is beschikbaar in Java en het .NET Framework. Met de EDCL kan een toepassing een shardkaart met databaseback-back maken, beheren en gebruiken.

Een shardkaart bevat een lijst met scherven (databases) en het toewijzen tussen sleutels (tenants) en shards. EDCL-functies worden gebruikt tijdens het inrichten van de tenant om de vermeldingen in de shardkaart te maken. Ze worden gebruikt tijdens het uitvoeren van de tijd door toepassingen om verbinding te maken met de juiste database. EDCL caches verbindingsgegevens om het verkeer naar de catalogusdatabase te minimaliseren en de toepassing te versnellen.

> [!IMPORTANT]
> De kaartgegevens zijn toegankelijk in de catalogusdatabase, maar *bewerken deze niet.* Bewerk toewijzingsgegevens met alleen API's van de Elastic Database Client Library. Direct manipuleren van de kaartgegevens dreigt de catalogus te beschadigen en wordt niet ondersteund.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Inleiding tot het SaaS-inrichtingspatroon

Wanneer u een nieuwe tenant toevoegt aan een SaaS-toepassing die gebruikmaakt van een databasemodel met één tenant, moet u een nieuwe tenantdatabase inrichten. De database moet worden gemaakt op de juiste locatie en servicelaag. Het moet ook worden geïnitialiseerd met het juiste schema en referentiegegevens. En het moet worden geregistreerd in de catalogus onder de juiste tenant sleutel.

Er kunnen verschillende benaderingen voor databasebepaling worden gebruikt. U SQL-scripts uitvoeren, een bacpac implementeren of een sjabloondatabase kopiëren.

Databaseprovisioning moet deel uitmaken van uw schemabeheerstrategie. U moet ervoor zorgen dat nieuwe databases zijn ingericht met het nieuwste schema. Deze vereiste wordt onderzocht in de [zelfstudie Schemabeheer](saas-tenancy-schema-management.md).

De Wingtip Tickets database-per-tenant app voorziet in nieuwe tenants door het kopiëren van een sjabloon database genaamd _basetenantdb_, die wordt geïmplementeerd op de catalogus server. Provisioning kan in de applicatie worden geïntegreerd als onderdeel van een aanmeldingservaring. Het kan ook offline worden ondersteund met behulp van scripts. Deze zelfstudie onderzoekt de inrichting met PowerShell.

Het inrichten van scripts kopieert de _basetenantdb-database_ om een nieuwe tenantdatabase in een elastische groep te maken. De tenantdatabase wordt gemaakt in de tenantserver die is toegewezen aan de _nieuwetenant_ DNS-alias. Deze alias behoudt een verwijzing naar de server die wordt gebruikt om nieuwe tenants in te richten en wordt bijgewerkt om te verwijzen naar een hersteltenantserver in de zelfstudies voor noodherstel[(DR met behulp van georestore](saas-dbpertenant-dr-geo-restore.md), [DR met behulp van georeplicatie).](saas-dbpertenant-dr-geo-replication.md) De scripts initialiseren vervolgens de database met tenant-specifieke informatie en registreren deze in de catalogusshardkaart. Tenantdatabases worden namen gegeven op basis van de tenantnaam. Dit naamgevingsschema is geen essentieel onderdeel van het patroon. De catalogus brengt de tenantsleutel in kaart aan de databasenaam, zodat elke naamgevingsconventie kan worden gebruikt.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Download de Wingtip Tickets SaaS database-per-tenant applicatiescripts

De Wingtip Tickets SaaS scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Gedetailleerd overzicht van inrichten en catalogiseren

Als u wilt begrijpen hoe de wingtip-toepassing nieuwe tenantprovisioning implementeert, voegt u een breekpunt toe en volgt u de werkstroom terwijl u een tenant indient.

1. Open in de PowerShell ISE ... \\Learning\\Modules ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ en stel de volgende parameters:

   * **$TenantName** = de naam van de nieuwe venue (bijvoorbeeld *Bushwillow Blues*).
   * **$VenueType** = een van de vooraf gedefinieerde locatietypes: _blues, klassiekmuziek, dans, jazz, judo, autosport, multifunctioneel, opera, rockmuziek, voetbal._
   * **$DemoScenario** = **1**, Een enkele *huurder inrichten*.

2. Als u een breekpunt wilt toevoegen, plaatst u de cursor overal op de regel met de tekst *Nieuw-Tenant .* Druk dan op F9.

   ![Onderbrekingspunt](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Als u het script wilt uitvoeren, drukt u op F5.

4. Nadat de uitvoering van het script is gestopt bij het breekpunt, drukt u op F11 om in de code te stappen.

   ![Foutopsporing](media/saas-dbpertenant-provision-and-catalog/debug.png)



Traceer de uitvoering van het script met behulp van de menuopties **Foutopsporing.** Druk op F10 en F11 om over of in de zogenaamde functies te stappen. Zie Tips voor het werken met [PowerShell-scripts voor](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)meer informatie over het debuggen van PowerShell-scripts.


U hoeft deze werkstroom niet expliciet te volgen. Het legt uit hoe je het script debuggen.

* **Importeer de module CatalogAndDatabaseManagement.psm1.** Het biedt een catalogus en tenant-level abstractie over de [Shard Management](sql-database-elastic-scale-shard-map-management.md) functies. Deze module kapselt veel van de catalogus patroon en is het verkennen waard.
* **Importeer de module SubscriptionManagement.psm1.** Het bevat functies voor het aanmelden bij Azure en het selecteren van het Azure-abonnement waarmee u wilt werken.
* **Informatie over de configuratie.** Stap in Get-Configuration met behulp van F11 en zie hoe de app-config is opgegeven. Resourcenamen en andere app-specifieke waarden worden hier gedefinieerd. Wijzig deze waarden pas als u bekend bent met de scripts.
* **Haal het catalogusobject op.** Stap in Get-Catalog, waarin een catalogusobject wordt samengesteld en geretourneerd dat wordt gebruikt in het script op een hoger niveau. Deze functie maakt gebruik van Shard Management-functies die worden geïmporteerd uit **AzureShardManagement.psm1**. Het catalogusobject bestaat uit de volgende elementen:

   * $catalogServerFullyQualifiedName wordt opgebouwd met behulp van de standaardsteel plus uw gebruikersnaam: _catalogusgebruiker\<\>.database.windows .net_.
   * $catalogDatabaseName wordt opgehaald uit de configuratie *tenantcatalog*.
   * Het object $shardMapManager wordt geïnitialiseerd uit de catalogusdatabase.
   * Het object $shardMap wordt geïnitialiseerd uit de shard map _tenantcatalog_ in de catalogusdatabase. Een catalogusobject wordt samengesteld en geretourneerd. Het wordt gebruikt in het script op een hoger niveau.
* **Bereken de nieuwe tenantsleutel.** Er wordt een hash-functie gebruikt om de tenant-sleutel te maken op basis van de naam van de tenant.
* **Controleer of de tenantsleutel al bestaat.** De catalogus wordt gecontroleerd om te controleren of de sleutel beschikbaar is.
* **De tenant-database wordt ingericht met behulp van New-TenantDatabase.** Gebruik F11 om een stap in te zetten in de manier waarop de database is ingericht met behulp van een [Azure Resource Manager-sjabloon.](../azure-resource-manager/resource-manager-template-walkthrough.md)

    De databasenaam wordt samengesteld aan de hand van de naam van de tenant om duidelijk aan te geven welke shard bij welke tenant hoort. U ook andere conventies voor het benoemen van gegevens gebruiken. Een resourcemanagersjabloon maakt een tenantdatabase door een sjabloondatabase _(baseTenantDB)_ op de catalogusserver te kopiëren. Als alternatief u een database maken en initialiseren door een bacpac te importeren. Of u een initialisatiescript uitvoeren vanaf een bekende locatie.

    De sjabloon Resourcemanager bevindt zich in de map ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **De tenantdatabase wordt verder geïnitialiseerd.** De naam van de locatie (huurder) en het type locatie worden toegevoegd. U hier ook andere initialisatie doen.

* **De tenantdatabase is geregistreerd in de catalogus.** Het is geregistreerd bij *Add-TenantDatabaseToCatalog* met behulp van de tenantsleutel. Gebruik F11 om de details van deze bewerkingen te bekijken:

    * De catalogusdatabase wordt toegevoegd aan de shard map (de lijst met bekende databases).
    * De toewijzing tussen de sleutelwaarde en de shard wordt gemaakt.
    * Aanvullende metagegevens over de tenant (de naam van de locatie) worden toegevoegd aan de tabel Tenants in de catalogus. De tabel Tenants maakt geen deel uit van het shardbeheerschema en is niet geïnstalleerd door de EDCL. In deze tabel wordt uitgelegd hoe de catalogusdatabase kan worden uitgebreid om aanvullende toepassingsspecifieke gegevens te ondersteunen.


Na het inrichten van voltooiden, wordt de uitvoering teruggegeven aan het oorspronkelijke *demo-provisionandcatalogusscript.* De pagina **Gebeurtenissen** wordt geopend voor de nieuwe tenant in de browser.

   ![Pagina Gebeurtenissen](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Een partij huurders voorzien

Deze oefening voorziet in een partij van 17 huurders. We raden u aan deze batch van huurders in te richten voordat u begint met andere Wingtip Tickets SaaS database-per-tenant tutorials. Er zijn meer dan een paar databases om mee te werken.

1. Open in de PowerShell ISE ... \\Leermodules\\provisionandCatalog\\*Demo-provisionandCatalog.ps1*. Wijzig de *parameter $DemoScenario* in 3:

   * **$DemoScenario** = **3**, Voorziening een partij *van huurders*.
2. Als u het script wilt uitvoeren, drukt u op F5.

Met het script worden verschillende tenants tegelijk ingericht. Het maakt gebruik van een [Azure Resource Manager-sjabloon](../azure-resource-manager/resource-manager-template-walkthrough.md) die de batch- en gemachtigdenvoorziening van elke database aan een gekoppelde sjabloon regelt. Door op deze manier sjablonen in te zetten, kan Azure Resource Manager als broker optreden voor het inrichtingsproces voor het script. De sjablonen bevatten databases parallel en verwerken indien nodig nieuwe pogingen. Het script is idempotent, dus als het mislukt of stopt om welke reden dan ook, voer het opnieuw uit.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Controleer de batch van tenants die is geïmplementeerd

* Blader in de [Azure-portal](https://portal.azure.com)naar uw lijst met servers en open de *server tenants1.* Selecteer **SQL-databases**en controleer of de batch van 17 extra databases nu in de lijst staat.

   ![Databaselijst](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andere inrichtingspatronen

Andere inrichtingspatronen die niet in deze zelfstudie zijn opgenomen:

**Pre-provisioning databases**: Het pre-provisioning patroon maakt gebruik van het feit dat databases in een elastische groep geen extra kosten toevoegen. Facturering is voor de elastische pool, niet voor de databases. Niet-actieve databases verbruiken geen resources. Door databases vooraf in een groep in te richten en toe te wijzen wanneer dat nodig is, u de tijd verkorten om tenants toe te voegen. Het aantal vooraf ingerichte databanken kan zo nodig worden aangepast om een buffer geschikt te houden voor het verwachte inrichtingspercentage.

**Automatische inrichting**: In het patroon voor automatische inrichting worden, indien nodig, automatisch servers, pools en databases ingericht voor het inrichten van servicevoorzieningen. Als u wilt, u vooraf inrichten van databases opnemen in elastische pools. Als databases worden buiten bedrijf gesteld en verwijderd, kunnen hiaten in elastische pools worden opgevuld door de inrichtingsservice. Een dergelijke service kan eenvoudig of complex zijn, zoals het verwerken van voorzieningen in meerdere regio's en het instellen van georeplicatie voor herstel na noodgevallen.

Met het patroon voor automatisch inrichten dient een clienttoepassing of -script een inrichtingsaanvraag in bij een wachtrij die door de inrichtingsservice moet worden verwerkt. Het vervolgens polls de dienst om de voltooiing te bepalen. Als pre-provisioning wordt gebruikt, worden aanvragen snel afgehandeld. De service voorziet op de achtergrond van een vervangende database.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
>
> * Inrichten van een nieuwe huurder.
> * Inrichten van een partij extra huurders.
> * Stap in de details van de inrichting van huurders en registreren ze in de catalogus.

Probeer de [zelfstudie Prestatiecontrole](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* Aanvullende [tutorials die voortbouwen op de Wingtip Tickets SaaS database-per-tenant applicatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Clientbibliotheek voor Elastic Database](sql-database-elastic-database-client-library.md)
* [Foutopsporingsscripts in de Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
