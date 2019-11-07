---
title: Inrichten in SaaS multi-tenant Azure
description: Meer informatie over het inrichten en catalogiseren van nieuwe tenants in een Azure SQL Database multi tenant SaaS-app
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
ms.openlocfilehash: f829c0d734838de42a82343876cefa007dcca04d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692010"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Nieuwe tenants inrichten en catalogiseren in een SaaS-toepassing met behulp van een Shard multi tenant Azure SQL database

In dit artikel vindt u informatie over het inrichten en catalogiseren van nieuwe tenants in een *Shard-database model of-patroon met meerdere tenants* .

Dit artikel heeft twee belang rijke onderdelen:

- [Conceptuele bespreking](#goto_2_conceptual) van het inrichten en catalogiseren van nieuwe tenants.

- [Zelf studie](#goto_1_tutorial) voor het markeren van de Power shell-script code die het inrichten en catalogiseren uitvoert.
  - In de zelf studie wordt gebruikgemaakt van de SaaS-toepassing Wingtip tickets, aangepast aan het database patroon multi-tenant Shard.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Database patroon

In deze sectie vindt u meer informatie over de concepten van het Shard-database patroon met meerdere tenants.

In dit multi tenant-Shard model bevatten de tabel schema's in elke Data Base een Tenant sleutel in de primaire sleutel van tabellen waarin Tenant gegevens worden opgeslagen. De Tenant sleutel maakt het mogelijk dat elke afzonderlijke data base 0, 1 of veel tenants opslaat. Het gebruik van Shard-data bases maakt het eenvoudig voor het toepassings systeem om een zeer groot aantal tenants te ondersteunen. Alle gegevens voor een Tenant worden opgeslagen in één data base. Het grote aantal tenants wordt gedistribueerd over de vele Shard-data bases. Een catalogus database slaat de toewijzing van elke Tenant op in de data base.

#### <a name="isolation-versus-lower-cost"></a>Isolatie tegenover lagere kosten

Een Tenant met een Data Base die zich allemaal op zichzelf heeft voor de voor delen van isolatie. De Tenant kan de data base herstellen naar een eerdere datum zonder dat dit wordt beperkt door de invloed op andere tenants. De prestaties van de data base kunnen worden afgestemd op de optimalisatie van de ene Tenant en opnieuw zonder dat u zich hoeft te houden met andere tenants. Het probleem is dat de isolatie kosten meer zijn dan de IT-kosten om een Data Base te delen met andere tenants.

Wanneer een nieuwe Tenant is ingericht, kan een Data Base met andere tenants worden gedeeld of in een eigen nieuwe Data Base worden geplaatst. Later kunt u van gedachten veranderen en de data base verplaatsen naar de andere situatie.

Data bases met meerdere tenants en afzonderlijke tenants worden in dezelfde SaaS-toepassing gemengd om de kosten of isolatie van elke Tenant te optimaliseren.

   ![Shard multi tenant-data base-app met Tenant catalogus](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Tenant catalogus patroon

Wanneer u twee of meer data bases hebt die elk ten minste één Tenant bevatten, moet de toepassing een manier hebben om te ontdekken welke data base de Tenant van de huidige interesse opslaat. Deze toewijzing wordt opgeslagen in een catalogus database.

#### <a name="tenant-key"></a>Tenant sleutel

Voor elke Tenant kan de toepassing Wingtip een unieke sleutel afleiden, de Tenant sleutel. De app haalt de Tenant naam op uit de URL van de webpagina. De app hasht de naam om de sleutel te verkrijgen. De app gebruikt de sleutel voor toegang tot de catalogus. De catalogus bevat kruis verwijzingen naar informatie over de Data Base waarin de Tenant is opgeslagen. De app maakt gebruik van de database gegevens om verbinding te maken. Andere Tenant-sleutel schema's kunnen ook worden gebruikt.

Als u een catalogus gebruikt, kan de naam of locatie van een Tenant database na het inrichten worden gewijzigd zonder de toepassing te onderbreken. In een database model met meerdere tenants is de catalogus geschikt voor het verplaatsen van een Tenant tussen data bases.

#### <a name="tenant-metadata-beyond-location"></a>Tenant meta gegevens buiten locatie

De catalogus kan ook aangeven of een Tenant offline is voor onderhoud of andere acties. En de catalogus kan worden uitgebreid om aanvullende Tenant-of meta gegevens van de Data Base op te slaan, zoals de volgende items:
- De servicelaag of editie van een Data Base.
- De versie van het database schema.
- De naam van de Tenant en de SLA (service level agreement).
- Informatie over het inschakelen van toepassings beheer, klant ondersteuning of devops-processen.  

De catalogus kan ook worden gebruikt om multi tenant rapporten, schema beheer en gegevens extractie voor analyse doeleinden in te scha kelen. 

### <a name="elastic-database-client-library"></a>Clientbibliotheek voor Elastic Database 

In Wingtip wordt de catalogus geïmplementeerd in de *tenantcatalog* -data base. De *tenantcatalog* wordt gemaakt met behulp van de Shard-beheer functies van de [Elastic database-client bibliotheek (EDCL)](sql-database-elastic-database-client-library.md). Met de bibliotheek kan een toepassing een *Shard-toewijzing* maken, beheren en gebruiken die is opgeslagen in een Data Base. Een Shard-toewijzing kruis verwijst naar de Tenant sleutel met de Shard, wat betekent dat de Shard-data base.

Tijdens het inrichten van de Tenant kunnen EDCL-functies worden gebruikt vanuit toepassingen of Power shell-scripts om de vermeldingen in de Shard-toewijzing te maken. Later kunt u de EDCL-functies gebruiken om verbinding te maken met de juiste data base. De EDCL slaat de verbindings gegevens op in de cache om het verkeer in de catalogus database zo klein mogelijk te maken en het verbindings proces te versnellen.

> [!IMPORTANT]
> Bewerk de gegevens in de catalogus database *niet* via directe toegang! Directe updates worden niet ondersteund vanwege het hoge risico op beschadiging van gegevens. Bewerk in plaats daarvan de toewijzings gegevens met behulp van EDCL-Api's.

## <a name="tenant-provisioning-pattern"></a>Inrichtings patroon Tenant

#### <a name="checklist"></a>Controlelijst

Wanneer u een nieuwe Tenant in een bestaande gedeelde data base wilt inrichten, moet u de volgende vragen stellen:
- Is er voldoende ruimte voor de nieuwe Tenant?
- Bevat de tabel tabellen met de vereiste referentie gegevens voor de nieuwe Tenant of kunnen de gegevens worden toegevoegd?
- Heeft het de juiste variatie van het basis schema voor de nieuwe Tenant?
- Bevindt het zich op de juiste geografische locatie dicht bij de nieuwe Tenant?
- Bevindt het zich op de juiste servicelaag voor de nieuwe Tenant?

Als u wilt dat de nieuwe Tenant wordt geïsoleerd in een eigen data base, kunt u deze maken om te voldoen aan de specificaties voor de Tenant.

Nadat het inrichten is voltooid, moet u de Tenant registreren in de catalogus. Ten slotte kan de Tenant toewijzing worden toegevoegd om te verwijzen naar de juiste Shard.

#### <a name="template-database"></a>Sjabloon database

Richt de data base in door SQL-scripts uit te voeren, een Bacpac te implementeren of een sjabloon database te kopiëren. De Wingtip-apps kopiëren een sjabloon database om nieuwe Tenant databases te maken.

Net als bij elke toepassing zal Wingtip zich na verloop van tijd ontwikkelen. Op het moment van Wingtip moeten wijzigingen in de Data Base worden aangebracht. Wijzigingen kunnen de volgende items bevatten:
- Nieuw of gewijzigd schema.
- Nieuwe of gewijzigde referentie gegevens.
- Routine onderhouds taken voor de data base om optimale prestaties van apps te garanderen.

Bij een SaaS-toepassing moeten deze wijzigingen op een gecoördineerde wijze worden geïmplementeerd in een mogelijk enorme reeks tenantdatabases. Als u deze wijzigingen wilt aanbrengen in toekomstige Tenant databases, moeten ze worden opgenomen in het inrichtings proces. Deze uitdaging vindt u verderop in de [zelf studie schema beheer](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

De scripts voor het inrichten van tenants in deze zelf studie ondersteunen de volgende scenario's:
- Het inrichten van een Tenant in een bestaande data base die wordt gedeeld met andere tenants.
- Een Tenant inrichten in een eigen data base.

Tenant gegevens worden vervolgens geïnitialiseerd en geregistreerd in de catalogus Shard-toewijzing. In de voor beeld-app worden data bases die meerdere tenants bevatten, een algemene naam gegeven, zoals *tenants1* of *tenants2*. Data bases die één Tenant bevatten, krijgen de naam van de Tenant. De specifieke naamgevings conventies die in het voor beeld worden gebruikt, zijn geen essentieel onderdeel van het patroon. Als u een catalogus gebruikt, kan elke naam worden toegewezen aan de data base.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Zelf studie begint

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Tenant inrichten in een multi tenant-data base
> * Een Tenant inrichten in een Data Base met één Tenant
> * Een batch met tenants inrichten in meerdere tenants en single tenant-data bases
> * Een Data Base en Tenant toewijzing registreren in een catalogus

#### <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

- Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.

- De Wingtip tickets SaaS multi-tenant-data base-app wordt geïmplementeerd. Zie [de toepassing Wingtip tickets SaaS multi-tenant data base implementeren en verkennen](saas-multitenantdb-get-started-deploy.md) voor meer informatie over de implementatie in minder dan vijf minuten

- De Wingtip-scripts en-bron code ophalen:
    - De Wingtip tickets SaaS multi-tenant database scripts en toepassings bron code zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) github opslag plaats.
    - Raadpleeg de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen om de Wingtip-scripts te downloaden en de blok kering op te heffen. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Een Tenant inrichten in een Data Base die wordt *gedeeld* met andere tenants

In deze sectie ziet u een lijst met de belangrijkste acties voor het inrichten die worden uitgevoerd door de Power shell-scripts. Vervolgens gebruikt u de Power shell ISE Debugger om de scripts te door lopen om de acties in de code te bekijken.

#### <a name="major-actions-of-provisioning"></a>Belangrijkste acties voor het inrichten

Hieronder ziet u de belangrijkste elementen van de werk stroom voor het inrichten:

- **De nieuwe Tenant sleutel berekenen**: een hash-functie wordt gebruikt om de Tenant sleutel te maken op basis van de naam van de Tenant.
- **Controleer of de Tenant sleutel al bestaat**: de catalogus wordt gecontroleerd om te controleren of de sleutel nog niet is geregistreerd.
- **Tenant initialiseren in de standaard-Tenant database**: de Tenant database is bijgewerkt om de nieuwe Tenant gegevens toe te voegen.  
- **Tenant in de catalogus registreren**: de toewijzing tussen de nieuwe Tenant sleutel en de bestaande tenants1-data base wordt toegevoegd aan de catalogus. 
- **Voeg de naam van de Tenant toe aan een catalogus extensie tabel**: de naam van de locatie wordt toegevoegd aan de tabel tenants in de catalogus.  Deze toevoeging laat zien hoe de catalogus database kan worden uitgebreid om aanvullende toepassingsspecifieke gegevens te ondersteunen.
- **Pagina met open gebeurtenissen voor de nieuwe Tenant**: de pagina *Bushwillow blauw* gebeurtenissen wordt geopend in de browser.

   ![events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Probleemoplossings stappen

Als u wilt weten hoe de Wingtip-app nieuwe Tenant inrichting implementeert in een gedeelde data base, voegt u een onderbrekings punt toe en voert u de volgende stappen uit:

1. Open in de *Power shell-ISE*\\Learning modules\\ProvisionTenants\\*demo-ProvisionTenants. ps1* en stel de volgende para meters in:
   - **$TenantName** = **Bushwillow blauw**, de naam van een nieuwe locatie.
   - **$VenueType** = **blauw**, een van de vooraf gedefinieerde typen locaties: blauw, classicalmusic, dans, jazz, Judo, motorracing, Multipurpose, Opera, rockmusic, voetbal (kleine letters, geen spaties).
   - **$DemoScenario** = **1**, om een Tenant in een gedeelde data base in te richten met andere tenants.

2. Voeg een onderbrekings punt toe door de cursor ergens op regel 38, de regel met de tekst: *New-Tenant*, te plaatsen en op **F9**te drukken.

   ![onderbrekingspunt](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Voer het script uit door op **F5**te drukken.

4. Wanneer het uitvoeren van het script stopt bij het onderbrekings punt, drukt u op **F11** om de code te Step into.

   ![Fout opsporing](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. De uitvoering van het script traceren met behulp van de opties voor het menu **fout opsporing** , **F10** en **F11**, om over te stappen of functies te noemen.

Zie [Tips voor het werken met en het opsporen van fouten in Power shell-scripts](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)voor meer informatie over fout opsporing in Power shell-scripts.

## <a name="provision-a-tenant-in-its-own-database"></a>Een Tenant inrichten in een *eigen* data base

#### <a name="major-actions-of-provisioning"></a>Belangrijkste acties voor het inrichten

Hieronder vindt u belang rijke elementen van de werk stroom tijdens het traceren van het script:

- **De nieuwe Tenant sleutel berekenen**: een hash-functie wordt gebruikt om de Tenant sleutel te maken op basis van de naam van de Tenant.
- **Controleer of de Tenant sleutel al bestaat**: de catalogus wordt gecontroleerd om te controleren of de sleutel nog niet is geregistreerd.
- **Een nieuwe Tenant database maken**: de data base wordt gemaakt door de *basetenantdb* -data base te kopiëren met behulp van een resource manager-sjabloon.  De naam van de nieuwe Data Base is gebaseerd op de naam van de Tenant.
- **Data Base toevoegen aan catalogus**: de nieuwe Tenant database is geregistreerd als een Shard in de catalogus.
- **Tenant initialiseren in de standaard-Tenant database**: de Tenant database is bijgewerkt om de nieuwe Tenant gegevens toe te voegen.  
- **Tenant registreren in de catalogus**: de toewijzing tussen de nieuwe Tenant sleutel en de *sequoiasoccer* -data base wordt toegevoegd aan de catalogus.
- **Tenant naam wordt toegevoegd aan de catalogus**: de naam van de locatie wordt toegevoegd aan de extensie tabel voor tenants in de catalogus.
- **Pagina met open gebeurtenissen voor de nieuwe Tenant**: de pagina *Sequoia voetbal* Events wordt geopend in de browser.

   ![events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Probleemoplossings stappen

Bekijk nu het script proces bij het maken van een Tenant in een eigen Data Base:

1. Nog steeds in...\\learning modules\\ProvisionTenants\\*demo-ProvisionTenants. ps1* Stel de volgende para meters in:
   - **$TenantName** = **Sequoia voetbal**, de naam van een nieuwe locatie.
   - **$VenueType** = **voetbal**, een van de vooraf gedefinieerde typen locaties: blauw, classicalmusic, dans, jazz, Judo, motorracing, Multipurpose, Opera, rockmusic, voetbal (kleine letters, geen spaties).
   - **$DemoScenario** = **2**, om een Tenant in te richten in een eigen data base.

2. Voeg een nieuw onderbrekings punt toe door de cursor ergens op regel 57 te plaatsen, de regel met de volgende tekst: *&&nbsp;$PSScriptRoot \New-tenantanddatabase '* en druk op **F9**.

   ![onderbrekingspunt](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Voer het script uit door op **F5**te drukken.

4. Wanneer de uitvoering van het script stopt bij het onderbrekings punt, drukt u op **F11** om de code te Step into.  Gebruik **F10** en **F11** om over en Step into functies te gebruiken om de uitvoering te traceren.

## <a name="provision-a-batch-of-tenants"></a>Een batch met tenants inrichten

In deze oefening worden een batch van 17 tenants ingericht. Het is raadzaam deze batch met tenants in te richten voordat u andere zelf studies voor Wingtip tickets start, zodat er meer data bases zijn om mee te werken.

1. Open in de *Power shell-ISE*\\Learning modules\\ProvisionTenants\\*demo-ProvisionTenants. ps1* en wijzig de para meter *$DemoScenario* in 4:
   - **$DemoScenario** = **4**, voor het inrichten van een batch tenants in een gedeelde data base.

2. Druk op **F5** om het script uit te voeren.

### <a name="verify-the-deployed-set-of-tenants"></a>De geïmplementeerde set tenants controleren 

In deze fase hebt u een combi natie van tenants die zijn geïmplementeerd in een gedeelde data base en tenants die zijn geïmplementeerd in hun eigen data bases. De Azure Portal kan worden gebruikt om de gemaakte data bases te controleren. Open in de [Azure Portal](https://portal.azure.com)de **tenants1-MT-\<gebruiker\>-** server door te bladeren naar de lijst met SQL-servers.  De lijst met **SQL-data bases** moet de gedeelde **tenants1** -data base en de data bases voor de tenants in hun eigen data base bevatten:

   ![databaselijst](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Terwijl de Azure Portal de Tenant databases toont, kunt u de tenants *in* de gedeelde data base niet zien. De volledige lijst met tenants kan worden weer gegeven op de webpagina **evenementen hub** van Wingtip en door te bladeren in de catalogus.

#### <a name="using-wingtip-tickets-events-hub-page"></a>De pagina Wingtip tickets-gebeurtenissen hub gebruiken

Open de pagina evenementen hub in de browser (http: Events. Wingtip-mt.\<USER\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Catalog-Data Base gebruiken

De volledige lijst met tenants en de bijbehorende data base voor elk is beschikbaar in de catalogus. Er wordt een SQL-weer gave gegeven die de naam van de Tenant koppelt aan de naam van de data base. De weer gave mooi toont de waarde voor het uitbreiden van de meta gegevens die zijn opgeslagen in de catalogus.
- De SQL-weer gave is beschikbaar in de tenantcatalog-data base.
- De naam van de Tenant wordt opgeslagen in de tabel tenants.
- De database naam wordt opgeslagen in de Shard-beheer tabellen.

1. In SQL Server Management Studio (SSMS) maakt u verbinding met de Tenant server op **catalogus-mt.\<gebruiker\>. database.Windows.net**, met login = **Developer**en password = **P\@ssword1**

    ![Dialoog venster voor SSMS-verbinding](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. In het Objectverkenner SSMS, bladert u naar de weer gaven in de *tenantcatalog* -data base.

3. Klik met de rechter muisknop op de weer gave *TenantsExtended* en kies **Top 1000-rijen selecteren**. Let op de toewijzing tussen de Tenant naam en de Data Base voor de verschillende tenants.

    ![Weer gave ExtendedTenants in SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Andere inrichtingspatronen

In deze sectie worden andere interessante inrichtings patronen besproken.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Data bases vooraf inrichten in elastische Pools

Het vooraf inrichtings patroon exploiteert het feit dat bij het gebruik van elastische Pools de facturering voor de groep niet de data bases is. Data bases kunnen daarom worden toegevoegd aan een elastische pool voordat ze nodig zijn zonder extra kosten te betalen. Deze previsie vermindert de tijd die nodig is om een Tenant in te richten in een Data Base. Het aantal vooraf ingerichte data bases kan zo nodig worden aangepast om een buffer te houden die geschikt is voor de verwachte inrichtings frequentie.

#### <a name="auto-provisioning"></a>Automatische inrichting

In het patroon voor automatische inrichting wordt een speciale inrichtings service gebruikt om zo nodig automatisch servers, groepen en data bases in te richten. Deze automatisering omvat het vooraf inrichten van data bases in elastische Pools. En als de data bases uit bedrijf worden genomen en verwijderd, kunnen de hiaten die in elastische Pools worden gemaakt, worden gevuld door de inrichtings service naar wens.

Dit type geautomatiseerde service kan eenvoudig of complex zijn. De automatisering kan bijvoorbeeld het inrichten voor meerdere geografische grafieken afhandelen en geo-replicatie voor nood herstel instellen. Met het automatisch inrichtings patroon zou een client toepassing of script een inrichtings aanvraag indienen bij een wachtrij die door een inrichtings service kan worden verwerkt. Het script wordt vervolgens gecontroleerd om de voltooiing te detecteren. Als vooraf inrichten wordt gebruikt, worden aanvragen snel verwerkt, terwijl een achtergrond service het inrichten van een vervangende data base beheert.

## <a name="additional-resources"></a>Aanvullende bronnen

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Clientbibliotheek voor Elastic Database](sql-database-elastic-database-client-library.md)
- [How to Debug Scripts in Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise) (Fouten opsporen in scripts met Windows PowerShell ISE)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Eén nieuwe Tenant inrichten in een gedeelde multi tenant-data base en een eigen data base
> * Verschillende tenants tegelijk inrichten
> * De details van het inrichten van tenants stapsgewijs door lopen en registreren in de catalogus

Probeer de [zelf studie voor prestatie bewaking](saas-multitenantdb-performance-monitoring.md).

