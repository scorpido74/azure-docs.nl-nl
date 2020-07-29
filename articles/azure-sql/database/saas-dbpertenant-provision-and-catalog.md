---
title: Nieuwe tenants inrichten in een multi tenant-app
description: Meer informatie over het inrichten en catalogiseren van nieuwe tenants in een Azure SQL Database multi tenant SaaS-app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 368843cf9b6e59497fe00e69433ae0d14b3f3398
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84042811"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Meer informatie over het inrichten van nieuwe tenants en registreren in de catalogus
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelf studie leert u hoe u SaaS-patronen kunt inrichten en catalogiseren. U leert ook hoe deze worden geïmplementeerd in de toepassing Wingtip tickets SaaS data base-per-Tenant. U maakt en initialiseert nieuwe Tenant databases en registreert deze in de Tenant catalogus van de toepassing. De catalogus is een Data Base die de toewijzing onderhoudt tussen de vele tenants en hun gegevens van de SaaS-toepassing. De catalogus speelt een belang rijke rol bij het omleiden van aanvragen voor toepassingen en beheer naar de juiste data base.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Richt één nieuwe Tenant in.
> * Richt een batch met extra tenants in.


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip tickets SaaS data base-per-Tenant-app wordt geïmplementeerd. Zie [de toepassing Wingtip tickets SaaS data base-per-Tenant implementeren en verkennen](../../sql-database/saas-dbpertenant-get-started-deploy.md)om deze in minder dan vijf minuten te implementeren.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-the-saas-catalog-pattern"></a>Inleiding tot het SaaS-catalogus patroon

In een door een Data Base ondersteunde multi tenant SaaS-toepassing is het belang rijk om te weten waar informatie voor elke Tenant is opgeslagen. In het SaaS-catalogus patroon wordt een catalogus database gebruikt om de toewijzing tussen elke Tenant en de Data Base waarin de gegevens zijn opgeslagen, op te slaan. Dit patroon is van toepassing wanneer Tenant gegevens over meerdere data bases worden gedistribueerd.

Elke Tenant wordt geïdentificeerd aan de hand van een sleutel in de catalogus, die wordt toegewezen aan de locatie van de data base. In de Wingtip tickets-app wordt de sleutel gevormd door een hash van de naam van de Tenant. Met dit schema kan de app de sleutel maken op basis van de naam van de Tenant die is opgenomen in de URL van de toepassing. Andere Tenant sleutel schema's kunnen worden gebruikt.

Met de catalogus kan de naam of locatie van de Data Base worden gewijzigd met minimale gevolgen voor de toepassing. In een model voor multi tenant-data bases is deze mogelijkheid ook geschikt voor het verplaatsen van een Tenant tussen data bases. De catalogus kan ook worden gebruikt om aan te geven of een Tenant of data base offline is voor onderhoud of andere acties. Deze mogelijkheid vindt u in de [zelf studie Eén Tenant herstellen](saas-dbpertenant-restore-single-tenant.md).

De catalogus kan ook aanvullende Tenant-of data base-meta gegevens opslaan, zoals de schema versie, het service plan of de Sla's die aan tenants worden aangeboden. De catalogus kan andere informatie opslaan die toepassings beheer, klant ondersteuning of DevOps mogelijk maakt.

Buiten de SaaS-toepassing kan de catalogus database hulpprogramma's inschakelen. In de Wingtip tickets SaaS data base-per-Tenant-voor beeld wordt de catalogus gebruikt voor het inschakelen van query's tussen tenants, die in de [zelf studie voor AD-hocrapportage](saas-tenancy-cross-tenant-reporting.md)worden besproken. Taak beheer voor meerdere data bases wordt geverkennen in de zelf studies voor [schema beheer](saas-tenancy-schema-management.md) en [Tenant analyse](saas-tenancy-tenant-analytics.md) .

In de Wingtip tickets SaaS-voor beelden wordt de catalogus geïmplementeerd met behulp van de Shard-beheer functies van de [Elastic database-client bibliotheek (EDCL)](elastic-database-client-library.md). De EDCL is beschikbaar in Java en de .NET Framework. Met de EDCL kan een toepassing een Shard-toewijzing met een Data Base maken, beheren en gebruiken.

Een Shard-toewijzing bevat een lijst met Shards (data bases) en de toewijzing tussen sleutels (tenants) en Shards. EDCL-functies worden gebruikt tijdens het inrichten van de Tenant om de vermeldingen in de Shard-toewijzing te maken. Ze worden tijdens runtime gebruikt door toepassingen om verbinding te maken met de juiste data base. EDCL slaat de verbindings gegevens op om het verkeer naar de catalogus database te minimaliseren en de toepassing te versnellen.

> [!IMPORTANT]
> De toewijzings gegevens zijn toegankelijk in de catalogus database, maar *u kunt deze niet bewerken*. Toewijzings gegevens bewerken door alleen Elastic Database-client bibliotheek-Api's te gebruiken. Het rechtstreeks bewerken van de toewijzings gegevens Risico's die de catalogus beschadigen, wordt niet ondersteund.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Inleiding tot het SaaS-inrichtings patroon

Wanneer u een nieuwe Tenant toevoegt aan een SaaS-toepassing die gebruikmaakt van een model met één Tenant database, moet u een nieuwe Tenant database inrichten. De data base moet worden gemaakt op de juiste locatie en servicelaag. Het moet ook worden geïnitialiseerd met het juiste schema en de bijbehorende referentie gegevens. En moet worden geregistreerd in de catalogus onder de juiste Tenant sleutel.

Er kunnen verschillende benaderingen van het inrichten van data bases worden gebruikt. U kunt SQL-scripts uitvoeren, een Bacpac implementeren of een sjabloon database kopiëren.

De inrichting van de data base moet deel uitmaken van uw schema beheer strategie. U moet ervoor zorgen dat er nieuwe data bases zijn ingericht met het meest recente schema. Deze vereiste wordt besproken in de [zelf studie schema beheer](saas-tenancy-schema-management.md).

De Wingtip tickets data base-per-Tenant app richt zich op nieuwe tenants door een sjabloon database te kopiëren met de naam _basetenantdb_, die op de catalogus server wordt geïmplementeerd. Het inrichten kan worden geïntegreerd in de toepassing als onderdeel van een registratie-ervaring. Het kan ook offline worden ondersteund met behulp van scripts. In deze zelf studie wordt het inrichten geverkennen met behulp van Power shell.

Inrichtings scripts Kopieer de _basetenantdb_ -data base om een nieuwe Tenant database in een elastische pool te maken. De Tenant-data base wordt gemaakt in de Tenant server die is toegewezen aan de DNS-alias van _newtenant_ . Deze alias houdt een verwijzing bij naar de server die wordt gebruikt voor het inrichten van nieuwe tenants en wordt bijgewerkt naar een herstel Tenant server in de zelf studies voor herstel na nood gevallen ([Dr met geoherstel](../../sql-database/saas-dbpertenant-dr-geo-restore.md), [Dr met georeplicatie](../../sql-database/saas-dbpertenant-dr-geo-replication.md)). De scripts initialiseeren de data base vervolgens met Tenant-specifieke gegevens en registreren deze in de catalogus Shard toewijzing. Tenant databases krijgen namen die zijn gebaseerd op de naam van de Tenant. Dit naamgevings schema is geen essentieel onderdeel van het patroon. De catalogus wijst de Tenant sleutel toe aan de naam van de data base, zodat elke naam Conventie kan worden gebruikt.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De scripts van de Wingtip tickets SaaS-data base-per-Tenant toepassing ophalen

De SaaS-scripts van Wingtip tickets en de bron code van de toepassing zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github opslag plaats. Bekijk de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en blok keren van de Wingtip tickets SaaS-scripts.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Gedetailleerd overzicht van inrichten en catalogiseren

Als u wilt weten hoe de toepassing Wingtip tickets nieuwe Tenant inrichting implementeert, voegt u een onderbrekings punt toe en volgt u de werk stroom terwijl u een Tenant inricht.

1. Open in de Power shell-ISE.. \\ . Leer modules \\ ProvisionAndCatalog \\ _Demo-ProvisionAndCatalog.ps1_ en stel de volgende para meters in:

   * **$TenantName** = de naam van de nieuwe venue (bijvoorbeeld *Bushwillow Blues*).
   * **$VenueType** = een van de vooraf gedefinieerde typen locaties: _blauw, classicalmusic, dans, jazz, Judo, Motor Racing, multifunctioneel, Opera, rockmusic, voetbal_.
   * **$DemoScenario**  =  **1**, *inrichten voor één Tenant*.

2. Als u een onderbrekings punt wilt toevoegen, plaatst u de cursor ergens op de regel met de tekst *Nieuw-Tenant*. Druk vervolgens op F9.

   ![Stopt](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Druk op F5 om het script uit te voeren.

4. Wanneer de uitvoering van het script stopt bij het onderbrekings punt, drukt u op F11 om de code te Step Into.

   ![Foutopsporing](./media/saas-dbpertenant-provision-and-catalog/debug.png)



Traceer de uitvoering van het script met behulp van de opties in het menu **fout opsporing** . Druk op F10 en F11 om over te stappen of naar de aangeroepen functies te gaan. Zie [Tips voor het werken met en het opsporen van fouten in Power shell-scripts](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)voor meer informatie over fout opsporing in Power shell-scripts.


U hoeft deze werk stroom niet expliciet te volgen. Hierin wordt uitgelegd hoe u fouten oplost in het script.

* **Importeer de module CatalogAndDatabaseManagement. psm1.** Het bevat een samen vatting van de catalogus en Tenant niveau over de [Shard-beheer](elastic-scale-shard-map-management.md) functies. Deze module bevat een groot deel van het catalogus patroon en is beter te verkennen.
* **Importeer de module SubscriptionManagement. psm1.** Het bevat functies voor het aanmelden bij Azure en het selecteren van het Azure-abonnement waarmee u wilt werken.
* **Configuratie Details ophalen.** Neem deel aan de Get-configuratie met behulp van F11 en Bekijk hoe de app-configuratie is opgegeven. Resource namen en andere app-specifieke waarden worden hier gedefinieerd. Wijzig deze waarden pas wanneer u bekend bent met de scripts.
* **Het object Catalog ophalen.** Stap voor Get-Catalog, waarmee een catalogus object wordt samengesteld en geretourneerd dat wordt gebruikt in het script op een hoger niveau. Deze functie maakt gebruik van Shard-beheer functies die worden geïmporteerd uit **AzureShardManagement. psm1**. Het catalogus object bestaat uit de volgende elementen:

   * $catalogServerFullyQualifiedName gemaakt met behulp van de Standard-stam plus uw gebruikers naam: _Catalog- \<user\> . data base. Windows .net_.
   * $catalogDatabaseName wordt opgehaald uit de configuratie *tenantcatalog*.
   * Het object $shardMapManager wordt geïnitialiseerd uit de catalogusdatabase.
   * Het object $shardMap wordt geïnitialiseerd uit de shard map _tenantcatalog_ in de catalogusdatabase. Een catalogus object bestaat uit en wordt geretourneerd. Deze wordt gebruikt in het script op een hoger niveau.
* **Bereken de nieuwe Tenant sleutel.** Er wordt een hash-functie gebruikt om de tenant-sleutel te maken op basis van de naam van de tenant.
* **Controleer of de Tenant sleutel al bestaat.** De catalogus wordt gecontroleerd om te controleren of de sleutel beschikbaar is.
* **De tenant-database wordt ingericht met behulp van New-TenantDatabase.** Gebruik F11 om te Step Into hoe de data base wordt ingericht met behulp van een [Azure Resource Manager sjabloon](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    De databasenaam wordt samengesteld aan de hand van de naam van de tenant om duidelijk aan te geven welke shard bij welke tenant hoort. U kunt ook andere database naam conventies gebruiken. Een resource manager-sjabloon maakt een Tenant database door een sjabloon database (_baseTenantDB_) op de catalogus server te kopiëren. Als alternatief kunt u een Data Base maken en deze initialiseren door een Bacpac te importeren. U kunt ook een initialisatie script uitvoeren vanaf een bekende locatie.

    De Resource Manager-sjabloon bevindt zich in de map. ..\Learning Modules\Common\: *tenantdatabasecopytemplate.jsop*

* **De Tenant database is verder geïnitialiseerd.** De naam van de locatie (Tenant) en het type locatie worden toegevoegd. U kunt hier ook andere initialisaties uitvoeren.

* **De Tenant database is geregistreerd in de catalogus.** Het is geregistreerd bij *add-TenantDatabaseToCatalog* met behulp van de Tenant sleutel. Gebruik F11 om de details van deze bewerkingen te bekijken:

    * De catalogusdatabase wordt toegevoegd aan de shard map (de lijst met bekende databases).
    * De toewijzing tussen de sleutelwaarde en de shard wordt gemaakt.
    * Aanvullende meta gegevens over de Tenant (de naam van de locatie) worden toegevoegd aan de tabel tenants in de catalogus. De tabel tenants maakt geen deel uit van het Shard-beheer schema en wordt niet geïnstalleerd door de EDCL. In deze tabel ziet u hoe de catalogus database kan worden uitgebreid om aanvullende toepassingsspecifieke gegevens te ondersteunen.


Nadat het inrichten is voltooid, keert de uitvoering terug naar het oorspronkelijke *ProvisionAndCatalog-* script. De pagina **gebeurtenissen** wordt geopend voor de nieuwe Tenant in de browser.

   ![Pagina gebeurtenissen](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Een batch met tenants inrichten

In deze oefening worden een batch van 17 tenants ingericht. U wordt aangeraden deze batch tenants in te richten voordat u andere zelf studies voor Wingtip tickets SaaS-Data Base kunt starten. Er zijn meer dan slechts een paar data bases waarmee u kunt werken.

1. Open in de Power shell-ISE.. \\ . Learning modules \\ ProvisionAndCatalog \\ *Demo-ProvisionAndCatalog.ps1*. Wijzig de *$DemoScenario* para meter in 3:

   * **$DemoScenario**  =  **3**, *een batch met tenants inrichten*.
2. Druk op F5 om het script uit te voeren.

Met het script worden verschillende tenants tegelijk ingericht. Er wordt gebruikgemaakt van een [Azure Resource Manager sjabloon](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) waarmee de batch wordt beheerd en de inrichting van elke Data Base wordt overgedragen aan een gekoppelde sjabloon. Door op deze manier sjablonen in te zetten, kan Azure Resource Manager als broker optreden voor het inrichtingsproces voor het script. De sjabloon voorziet in parallelle data bases en verwerkings pogingen, indien nodig. Het script is idempotent, dus als het mislukt of stopt om een wille keurige reden, voert u het opnieuw uit.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Controleer de batch met tenants die zijn geïmplementeerd

* Blader in het [Azure Portal](https://portal.azure.com)naar de lijst met servers en open de *tenants1* -server. Selecteer **SQL-data bases**en controleer of de batch met 17 extra data bases nu in de lijst staat.

   ![Databaselijst](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andere inrichtingspatronen

Andere inrichtings patronen die niet in deze zelf studie zijn opgenomen:

**Provisioning data bases**: het vooraf inrichtings patroon exploiteert het feit dat data bases in een elastische pool geen extra kosten toevoegen. Facturering is voor de elastische pool, niet voor de data bases. Niet-actieve data bases nemen geen resources in beslag. Door data bases vooraf in te richten in een pool en ze toe te wijzen wanneer dat nodig is, kunt u de tijd beperken om tenants toe te voegen. Het aantal vooraf ingerichte data bases kan zo nodig worden aangepast om een buffer te houden die geschikt is voor de verwachte inrichtings frequentie.

**Automatische inrichting**: in het automatisch inrichtings patroon, indien nodig, worden servers, Pools en data bases automatisch ingericht met een inrichtings service. Als u wilt, kunt u previsioning-data bases in elastische Pools insluiten. Als data bases uit bedrijf worden genomen en verwijderd, kunnen hiaten in elastische Pools worden gevuld door de inrichtings service. Een dergelijke service kan eenvoudig of complex zijn, zoals het afhandelen van inrichting in meerdere geographs en het instellen van geo-replicatie voor nood herstel.

Met het patroon voor automatische inrichting, verzendt een client toepassing of script een inrichtings aanvraag naar een wachtrij die moet worden verwerkt door de inrichtings service. Vervolgens wordt de service gecontroleerd om de voltooiing te bepalen. Als vooraf inrichten wordt gebruikt, worden aanvragen snel afgehandeld. De service richt een vervangende Data Base op de achtergrond.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
>
> * Richt één nieuwe Tenant in.
> * Richt een batch met extra tenants in.
> * Stap in de details van het inrichten van tenants en het registreren daarvan in de catalogus.

Probeer de [zelf studie voor prestatie bewaking](../../sql-database/saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* Aanvullende [zelf studies die voortbouwen op de Wingtip tickets SaaS data base-per-Tenant toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Clientbibliotheek voor Elastic Database](elastic-database-client-library.md)
* [Fouten opsporen in scripts in de Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
