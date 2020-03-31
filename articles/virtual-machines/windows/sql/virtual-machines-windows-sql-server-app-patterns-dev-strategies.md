---
title: SQL Server-toepassingspatronen op VM's | Microsoft Documenten
description: In dit artikel worden toepassingspatronen voor SQL Server op Azure VM's weergegeven. Het biedt oplossingsarchitecten en ontwikkelaars een basis voor goede applicatiearchitectuur en -ontwerp.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: cbc2bfbb68910c3eb12352bebb575c4548885a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70124034"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Toepassingspatronen en ontwikkelingsstrategieën voor SQL Server in Azure Virtual Machines
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Samenvatting:
Bepalen welk toepassingspatroon of -patronen moeten worden gebruikt voor uw SQL-Server-gebaseerde toepassingen in azure-omgeving is een belangrijke ontwerpbeslissing en vereist een goed begrip van hoe SQL Server en elke infrastructuurcomponent van Azure samenwerken. Met SQL Server in Azure Infrastructure Services u eenvoudig uw bestaande SQL Server-toepassingen die zijn ingebouwd op Windows Server migreren, onderhouden en bewaken naar virtuele machines in Azure.

Het doel van dit artikel is om oplossingsarchitecten en -ontwikkelaars een basis te bieden voor goede toepassingsarchitectuur en -ontwerp, die ze kunnen volgen bij het migreren van bestaande toepassingen naar Azure en het ontwikkelen van nieuwe toepassingen in Azure.

Voor elk toepassingspatroon vindt u een on-premises scenario, de respectievelijke cloudoplossing en de bijbehorende technische aanbevelingen. Bovendien bespreekt het artikel Azure-specifieke ontwikkelingsstrategieën, zodat u uw toepassingen correct ontwerpen. Vanwege de vele mogelijke toepassingspatronen wordt aanbevolen dat architecten en ontwikkelaars het meest geschikte patroon voor hun toepassingen en gebruikers kiezen.

**Technische bijdragers:** Luis Carlos Vargas Haring, Madhan Arumugam Ramakrishnan

**Technische beoordelaars:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Inleiding
U veel soorten n-tier toepassingen ontwikkelen door de componenten van de verschillende toepassingslagen op verschillende machines en in afzonderlijke componenten te scheiden. U bijvoorbeeld de onderdelen van de clienttoepassing en bedrijfsregels in één machine, front-end weblaag en gegevenstoegangslaagcomponenten in een andere machine plaatsen en een back-enddatabaselaag in een andere machine. Dit soort structurering helpt elke laag van elkaar te isoleren. Als u wijzigt waar gegevens vandaan komen, hoeft u de client of webtoepassing niet te wijzigen, maar alleen de gegevenstoegangslaagcomponenten.

Een typische *n-tier-toepassing* omvat de presentatielaag, de bedrijfslaag en de gegevenslaag:

| Laag | Beschrijving |
| --- | --- |
| **Stijl** |De *presentatielaag* (weblaag, front-endlaag) is de laag waarin gebruikers met een toepassing communiceren. |
| **Business** |De *bedrijfslaag* (middelste laag) is de laag die de presentatielaag en de gegevenslaag gebruiken om met elkaar te communiceren en bevat de kernfunctionaliteit van het systeem. |
| **Gegevens** |De *gegevenslaag* is in principe de server die de gegevens van een toepassing opslaat (bijvoorbeeld een server met SQL Server). |

Toepassingslagen beschrijven de logische groeperingen van de functionaliteit en componenten in een toepassing; terwijl lagen de fysieke verdeling van de functionaliteit en componenten beschrijven op afzonderlijke fysieke servers, computers, netwerken of externe locaties. De lagen van een toepassing kunnen zich op dezelfde fysieke computer bevinden (dezelfde laag) of kunnen worden verdeeld over afzonderlijke computers (n-tier) en de componenten in elke laag communiceren met componenten in andere lagen via goed gedefinieerde interfaces. U de term laag zien als verwijzennaar fysieke distributiepatronen zoals tweelagen, drielagen en n-tier. Een **toepassingspatroon met twee lagen** bevat twee toepassingslagen: toepassingsserver en databaseserver. De directe communicatie vindt plaats tussen de toepassingsserver en de databaseserver. De toepassingsserver bevat zowel web-tier als business-tier componenten. In **het toepassingspatroon met drie**lagen zijn er drie toepassingslagen: webserver, toepassingsserver, die de gegevenstoegangscomponenten voor bedrijfslogica en/of zakelijke lagen bevat en de databaseserver. De communicatie tussen de webserver en de databaseserver vindt plaats via de toepassingsserver. Zie [Microsoft Application Architecture Guide](https://msdn.microsoft.com/library/ff650706.aspx)voor gedetailleerde informatie over toepassingslagen en -lagen.

Voordat u dit artikel begint te lezen, moet u kennis hebben over de fundamentele concepten van SQL Server en Azure. Zie SQL [Server Books Online,](https://msdn.microsoft.com/library/bb545450.aspx) [SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) en [Azure.com](https://azure.microsoft.com/)voor meer informatie.

Dit artikel beschrijft verschillende toepassingspatronen die geschikt zijn voor uw eenvoudige toepassingen en de zeer complexe bedrijfstoepassingen. Voordat u elk patroon beschrijft, raden we u aan vertrouwd te raken met de beschikbare gegevensopslagservices in Azure, zoals [Azure Storage,](../../../storage/common/storage-introduction.md) [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md)en SQL Server in een Azure Virtual [Machine.](virtual-machines-windows-sql-server-iaas-overview.md) Als u de beste ontwerpbeslissingen voor uw toepassingen wilt nemen, moet u duidelijk begrijpen wanneer u welke gegevensopslagservice duidelijk moet gebruiken.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Kies SQL Server in een Azure Virtual Machine wanneer:
* U hebt controle nodig over SQL Server en Windows. Dit kan bijvoorbeeld de SQL Server-versie, speciale hotfixes, prestatieconfiguratie, enz.
* U hebt een volledige compatibiliteit met SQL Server on-premises nodig en u wilt bestaande toepassingen naar Azure verplaatsen.
* U wilt gebruikmaken van de mogelijkheden van de Azure-omgeving, maar Azure SQL Database biedt geen ondersteuning voor alle functies die uw toepassing nodig heeft. Dit kan de volgende gebieden omvatten:
  
  * **Databasegrootte**: Op het moment dat dit artikel werd bijgewerkt, ondersteunt SQL Database een database met maximaal 1 TB aan gegevens. Als uw toepassing meer dan 1 TB aan gegevens vereist en u geen aangepaste shardingoplossingen wilt implementeren, wordt aanbevolen SQL Server te gebruiken in een Azure Virtual Machine. Zie Azure SQL [Database,](https://msdn.microsoft.com/library/azure/dn495641.aspx) [DTU-gebaseerd inkoopmodel](../../../sql-database/sql-database-service-tiers-dtu.md)en [vCore-gebaseerd inkoopmodel](../../../sql-database/sql-database-service-tiers-vcore.md)(preview) voor de meest recente informatie.
  * **HIPAA-compliance**: Klanten in de gezondheidszorg en onafhankelijke softwareleveranciers (ISV's) kunnen [SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) kiezen in plaats van Azure SQL [Database,](../../../sql-database/sql-database-technical-overview.md) omdat SQL Server in een Azure Virtual Machine onder DE HIPAA Business Associate Agreement (BAA) valt. Zie Microsoft Azure [Trust Center: Compliance voor](https://azure.microsoft.com/support/trust-center/compliance/)informatie over compliance.
  * **Functies op instantieniveau:** OP dit moment biedt SQL Database geen ondersteuning voor functies die buiten de database leven (zoals Gekoppelde servers, agenttaken, FileStream, Service Broker, enz.). Zie [Richtlijnen en beperkingen van Azure SQL Database voor](https://msdn.microsoft.com/library/azure/ff394102.aspx)meer informatie.

## <a name="1-tier-simple-single-virtual-machine"></a>1-laags (eenvoudig): één virtuele machine
In dit toepassingspatroon implementeert u uw SQL Server-toepassing en -database naar een zelfstandige virtuele machine in Azure. Dezelfde virtuele machine bevat uw client/webapplicatie, bedrijfscomponenten, gegevenstoegangslaag en de databaseserver. De presentatie-, bedrijfs- en gegevenstoegangscode zijn logisch gescheiden, maar bevinden zich fysiek in een machine met één server. De meeste klanten beginnen met dit toepassingspatroon en vervolgens schalen ze uit door meer webrollen of virtuele machines aan hun systeem toe te voegen.

Dit toepassingspatroon is handig wanneer:

* U wilt een eenvoudige migratie naar Azure-platform uitvoeren om te evalueren of het platform voldoet aan de vereisten van uw toepassing of niet.
* U wilt alle toepassingslagen die in dezelfde virtuele machine worden gehost in hetzelfde Azure-datacenter behouden om de latentie tussen lagen te verminderen.
* U wilt snel ontwikkel- en testomgevingen voor korte tijd inrichten.
* U wilt stresstesten uitvoeren voor verschillende werkbelastingsniveaus, maar tegelijkertijd wilt u niet de hele tijd veel fysieke machines bezitten en onderhouden.

In het volgende diagram wordt een eenvoudig on-premises scenario getoond en hoe u de cloudoplossing in één virtuele machine in Azure implementeren.

![Toepassingspatroon met 1 laag](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Het implementeren van de bedrijfslaag (bedrijfslogica en gegevenstoegangscomponenten) op dezelfde fysieke laag als de presentatielaag kan de prestaties van toepassingen maximaliseren, tenzij u een afzonderlijke laag moet gebruiken vanwege schaalbaarheid of beveiligingsproblemen.

Aangezien dit een veel voorkomend patroon is om mee te beginnen, vindt u mogelijk het volgende artikel over migratie nuttig voor het verplaatsen van uw gegevens naar uw SQL Server VM: [Een database migreren naar SQL Server op een Azure VM.](virtual-machines-windows-migrate-sql.md)

## <a name="3-tier-simple-multiple-virtual-machines"></a>3-laags (eenvoudig): meerdere virtuele machines
In dit toepassingspatroon implementeert u een 3-laagse toepassing in Azure door elke toepassingslaag in een andere virtuele machine te plaatsen. Dit biedt een flexibele omgeving voor eenvoudige scale-up en scale-out scenario's. Wanneer een virtuele machine uw client/webtoepassing bevat, host de andere de ene bedrijfsonderdelen en de andere de databaseserver.

Dit toepassingspatroon is handig wanneer:

* U wilt een migratie uitvoeren van complexe databasetoepassingen naar Azure Virtual Machines.
* U wilt dat verschillende toepassingslagen in verschillende regio's worden gehost. U hebt bijvoorbeeld gedeelde databases die zijn geïmplementeerd in meerdere regio's voor rapportagedoeleinden.
* U wilt bedrijfstoepassingen verplaatsen van on-premises gevirtualiseerde platforms naar Azure Virtual Machines. Zie [Wat is een ondernemingstoepassing](https://msdn.microsoft.com/library/aa267045.aspx)voor een gedetailleerde discussie over bedrijfstoepassingen.
* U wilt snel ontwikkel- en testomgevingen voor korte tijd inrichten.
* U wilt stresstesten uitvoeren voor verschillende werkbelastingsniveaus, maar tegelijkertijd wilt u niet de hele tijd veel fysieke machines bezitten en onderhouden.

In het volgende diagram ziet u hoe u een eenvoudige 3-laags-toepassing in Azure plaatsen door elke toepassingslaag in een andere virtuele machine te plaatsen.

![Toepassingspatroon met 3 lagen](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

In dit toepassingspatroon is er slechts één virtuele machine (VM) in elke laag. Als u meerdere VM's in Azure hebt, raden we u aan een virtueel netwerk in te stellen. [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) creëert een vertrouwde beveiligingsgrens en stelt VM's ook in staat om onderling te communiceren via het privé-IP-adres. Zorg er bovendien altijd voor dat alle internetverbindingen alleen naar de presentatielaag gaan. Wanneer u dit toepassingspatroon volgt, beheert u de regels van de netwerkbeveiligingsgroep om de toegang te beheren. Zie [Externe toegang tot uw VM toestaan met behulp van de Azure-portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.

In het diagram kunnen internetprotocollen TCP, UDP, HTTP of HTTPS zijn.

> [!NOTE]
> Het opzetten van een virtueel netwerk in Azure is gratis. U wordt echter in rekening gebracht voor de VPN-gateway die verbinding maakt met on-premises. Deze kosten zijn gebaseerd op de hoeveelheid tijd die de verbinding is ingericht en beschikbaar.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2-tier en 3-tier met schaalvergroting van de presentatielaag
In dit toepassingspatroon implementeert u 2-laags of 3-laags databasetoepassing naar Azure Virtual Machines door elke toepassingslaag in een andere virtuele machine te plaatsen. Bovendien schaalt u de presentatielaag uit vanwege het toegenomen aantal binnenkomende clientaanvragen.

Dit toepassingspatroon is handig wanneer:

* U wilt bedrijfstoepassingen verplaatsen van on-premises gevirtualiseerde platforms naar Azure Virtual Machines.
* U wilt de presentatielaag uitschalen vanwege het toegenomen aantal binnenkomende clientaanvragen.
* U wilt snel ontwikkel- en testomgevingen voor korte tijd inrichten.
* U wilt stresstesten uitvoeren voor verschillende werkbelastingsniveaus, maar tegelijkertijd wilt u niet de hele tijd veel fysieke machines bezitten en onderhouden.
* U wilt een infrastructuuromgeving bezitten die op en neer kan schalen op aanvraag.

In het volgende diagram ziet u hoe u de toepassingslagen in meerdere virtuele machines in Azure plaatsen door de presentatielaag uit te schalen vanwege een groter aantal binnenkomende clientaanvragen. Zoals te zien in het diagram, azure load balancer is verantwoordelijk voor het distribueren van verkeer over meerdere virtuele machines en ook bepalen welke webserver verbinding te maken met. Het hebben van meerdere exemplaren van de webservers achter een load balancer zorgt voor de hoge beschikbaarheid van de presentatielaag.

![Toepassingspatroon - schaalschaal van presentatielagen](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Aanbevolen procedures voor patronen met 2 lagen, 3 of n-tier met meerdere VM's in één laag
Het wordt aanbevolen dat u de virtuele machines die tot dezelfde laag behoren, in dezelfde cloudservice plaatst en in dezelfde beschikbaarheidsset. Plaats bijvoorbeeld een set webservers in **CloudService1** en **AvailabilitySet1** en een set databaseservers in **CloudService2** en **AvailabilitySet2.** Met een beschikbaarheidsset in Azure u de knooppunten met hoge beschikbaarheid in afzonderlijke foutdomeinen plaatsen en domeinen upgraden.

Als u gebruik wilt maken van meerdere VM-exemplaren van een laag, moet u Azure Load Balancer tussen toepassingslagen configureren. Als u Load Balancer in elke laag wilt configureren, maakt u afzonderlijk een eindpunt voor de vm's van elke laag. Maak voor een specifieke laag eerst VM's in dezelfde cloudservice. Dit zorgt ervoor dat ze hetzelfde openbare virtuele IP-adres hebben. Maak vervolgens een eindpunt op een van de virtuele machines op die laag. Wijs vervolgens hetzelfde eindpunt toe aan de andere virtuele machines op die laag voor taakverdeling. Door een load-balanced set te maken, verdeelt u het verkeer over meerdere virtuele machines en stelt u de Load Balancer ook in staat om te bepalen welk knooppunt verbinding moet maken wanneer een backend VM-knooppunt uitvalt. Als u bijvoorbeeld meerdere exemplaren van de webservers achter een load balancer hebt, zorgt u voor de hoge beschikbaarheid van de presentatielaag.

Als een best practice, zorg er altijd voor dat alle internetverbindingen eerst naar de presentatielaag gaan. De presentatielaag heeft toegang tot de bedrijfslaag en vervolgens heeft de bedrijfslaag toegang tot de gegevenslaag. Zie Externe toegang tot uw VM toestaan [met behulp van de Azure-portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over het toestaan van toegang tot de presentatielaag.

Houd er rekening mee dat de load balancer in Azure vergelijkbaar is met load balancers in een on-premises omgeving. Zie [Taakverdeling voor Azure-infrastructuurservices voor](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie.

Daarnaast raden we u aan een privénetwerk voor uw virtuele machines in te stellen met Azure Virtual Network. Dit stelt hen in staat om onderling te communiceren via het privé IP-adres. Zie [Azure Virtual Network voor](../../../virtual-network/virtual-networks-overview.md)meer informatie.

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2-tier en 3-tier met business tier scale-out
In dit toepassingspatroon implementeert u 2-laags of 3-laags databasetoepassing naar Azure Virtual Machines door elke toepassingslaag in een andere virtuele machine te plaatsen. Bovendien u de onderdelen van de toepassingsserver distribueren naar meerdere virtuele machines vanwege de complexiteit van uw toepassing.

Dit toepassingspatroon is handig wanneer:

* U wilt bedrijfstoepassingen verplaatsen van on-premises gevirtualiseerde platforms naar Azure Virtual Machines.
* U wilt de onderdelen van de toepassingsserver distribueren naar meerdere virtuele machines vanwege de complexiteit van uw toepassing.
* U wilt bedrijfslogica zware on-premises LOB-toepassingen (line-of-business) verplaatsen naar Azure Virtual Machines. LOB-toepassingen zijn een reeks kritieke computertoepassingen die essentieel zijn voor het runnen van een onderneming, zoals boekhouding, personeelszaken (HR), salarisadministratie, supply chain management en resourceplanningtoepassingen.
* U wilt snel ontwikkel- en testomgevingen voor korte tijd inrichten.
* U wilt stresstesten uitvoeren voor verschillende werkbelastingsniveaus, maar tegelijkertijd wilt u niet de hele tijd veel fysieke machines bezitten en onderhouden.
* U wilt een infrastructuuromgeving bezitten die op en neer kan schalen op aanvraag.

In het volgende diagram wordt een on-premises scenario en de oplossing met de cloud ingeschakeld. In dit scenario plaatst u de toepassingslagen in meerdere virtuele machines in Azure door de bedrijfslaag, die de bedrijfslogicalaag en gegevenstoegangscomponenten bevat, uit te schalen. Zoals te zien in het diagram, azure load balancer is verantwoordelijk voor het distribueren van verkeer over meerdere virtuele machines en ook bepalen welke webserver verbinding te maken met. Het hebben van meerdere exemplaren van de toepassingsservers achter een load balancer zorgt voor de hoge beschikbaarheid van de bedrijfslaag. Zie [Aanbevolen procedures voor toepassingspatronen met twee lagen, 3 of n-tier met meerdere virtuele machines in één laag voor](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier)meer informatie.

![Toepassingspatroon met uitschaing van de bedrijfslaag](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2-tier en 3-tier met presentatie- en bedrijfsniveaus scale-out en HADR
In dit toepassingspatroon implementeert u 2-laags of 3-laags databasetoepassing naar Azure Virtual Machines door de beschikbaarheidslaag (webserver) en de onderdelen van de bedrijfslaag (toepassingsserver) te distribueren naar meerdere virtuele machines. Daarnaast implementeert u oplossingen voor hoge beschikbaarheid en noodherstel oplossingen voor uw databases in Azure virtuele machines.

Dit toepassingspatroon is handig wanneer:

* U wilt bedrijfstoepassingen verplaatsen van gevirtualiseerde platforms on-premises naar Azure door SQL Server-mogelijkheden voor hoge beschikbaarheid en noodherstel te implementeren.
* U wilt de presentatielaag en de bedrijfslaag uitschalen vanwege het toegenomen aantal binnenkomende clientaanvragen en de complexiteit van uw toepassing.
* U wilt snel ontwikkel- en testomgevingen voor korte tijd inrichten.
* U wilt stresstesten uitvoeren voor verschillende werkbelastingsniveaus, maar tegelijkertijd wilt u niet de hele tijd veel fysieke machines bezitten en onderhouden.
* U wilt een infrastructuuromgeving bezitten die op en neer kan schalen op aanvraag.

In het volgende diagram wordt een on-premises scenario en de oplossing met de cloud ingeschakeld. In dit scenario schaalt u de presentatielaag en de onderdelen van de bedrijfslaag uit in meerdere virtuele machines in Azure. Daarnaast implementeert u hadr-technieken (High Availability and Disaster Recovery) voor SQL Server-databases in Azure.

Als u meerdere kopieën van een toepassing uitvoert in verschillende VM's, moet u ervoor zorgen dat u aanvragen voor het balanceren van deze toepassingen laadt. Wanneer u meerdere virtuele machines hebt, moet u ervoor zorgen dat al uw VM's toegankelijk zijn en op één moment worden uitgevoerd. Als u load balancing configureert, houdt Azure Load Balancer de status van VM's bij en stuurt het binnenkomende oproepen naar de correct functionerende VM-knooppunten naar de juiste manier. Zie [Taakverdeling voor Azure-infrastructuurservices voor](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)informatie over het instellen van taakverdeling van de virtuele machines. Het hebben van meerdere exemplaren van web- en toepassingsservers achter een load balancer zorgt voor de hoge beschikbaarheid van de presentatie- en bedrijfslagen.

![Scale-out en hoge beschikbaarheid](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Aanbevolen procedures voor toepassingspatronen waarvoor SQL HADR vereist is
Wanneer u SQL Server oplossingen voor hoge beschikbaarheid en noodherstel in Azure Virtual Machines instelt, is het instellen van een virtueel netwerk voor uw virtuele machines met [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) verplicht.  Virtuele machines binnen een virtueel netwerk hebben een stabiel privé-IP-adres, zelfs na een onderbreking van de service, zodat u de updatetijd vermijden die nodig is voor DNS-naamomzetting. Bovendien u met het virtuele netwerk uw on-premises netwerk uitbreiden naar Azure en een vertrouwde beveiligingsgrens creëren. Als uw toepassing bijvoorbeeld bedrijfsdomeinbeperkingen heeft (zoals Windows-verificatie, Active Directory), is het instellen van [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) noodzakelijk.

De meeste klanten, die productiecode op Azure uitvoeren, bewaren zowel primaire als secundaire replica's in Azure.

Zie Hoge beschikbaarheid en disaster recovery voor SQL [Server in Azure Virtual Machines voor](virtual-machines-windows-sql-high-availability-dr.md)uitgebreide informatie en zelfstudies over technieken met hoge beschikbaarheid en noodherstel.

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2-tier s en 3-tier met Azure VM's en Cloud Services
In dit toepassingspatroon implementeert u 2-tier of 3-tier applicatie naar Azure met behulp van zowel [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (web- en werknemersrollen - Platform as a Service (PaaS)) en Azure Virtual [Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Infrastructure as a Service (IaaS)). Het gebruik van [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) voor de presentatielaag/bedrijfslaag en SQL Server in Azure Virtual [Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de gegevenslaag is gunstig voor de meeste toepassingen die op Azure worden uitgevoerd. De reden hiervoor is dat het hebben van een rekeninstantie die wordt uitgevoerd op Cloud Services een eenvoudig beheer, implementatie, monitoring en scale-out biedt.

Met Cloud Services onderhoudt Azure de infrastructuur voor u, voert het routineonderhoud uit, patcht het de besturingssystemen en probeert het te herstellen van service- en hardwarefouten. Wanneer uw toepassing scale-out, automatische en handmatige scale-out opties zijn beschikbaar voor uw cloud service project door het verhogen of verminderen van het aantal exemplaren of virtuele machines die worden gebruikt door uw toepassing. Bovendien u on-premises Visual Studio gebruiken om uw toepassing te implementeren voor een cloudserviceproject in Azure.

Als u geen uitgebreide beheertaken voor de presentatie/bedrijfslaag wilt bezitten en uw toepassing geen complexe configuratie van software of het besturingssysteem vereist, gebruikt u Azure Cloud Services. Als Azure SQL Database niet alle functies ondersteunt die u zoekt, gebruikt u SQL Server in een Azure Virtual Machine voor de gegevenslaag. Het uitvoeren van een toepassing op Azure Cloud Services en het opslaan van gegevens in Azure Virtual Machines combineert de voordelen van beide services. Zie voor een gedetailleerde vergelijking het gedeelte in dit onderwerp over [Ontwikkelingsstrategieën vergelijken in Azure](#comparing-web-development-strategies-in-azure).

In dit toepassingspatroon bevat de presentatielaag een webrol, een Cloud Services-component die wordt uitgevoerd in de Azure-uitvoeringsomgeving en deze is aangepast voor het programmeren van webtoepassingen, zoals ondersteund door IIS en ASP.NET. De zakelijke of backendlaag bevat een werkrol, een Cloud Services-component die wordt uitgevoerd in de Azure-uitvoeringsomgeving en deze is handig voor algemene ontwikkeling en kan achtergrondverwerking uitvoeren voor een webrol. De databaselaag bevindt zich in een virtuele SQL Server-machine in Azure. De communicatie tussen de presentatielaag en de databaselaag gebeurt rechtstreeks of over de bedrijfslaag : functiecomponenten voor werknemers.

Dit toepassingspatroon is handig wanneer:

* U wilt bedrijfstoepassingen verplaatsen van gevirtualiseerde platforms on-premises naar Azure door SQL Server-mogelijkheden voor hoge beschikbaarheid en noodherstel te implementeren.
* U wilt een infrastructuuromgeving bezitten die op en neer kan schalen op aanvraag.
* Azure SQL Database biedt geen ondersteuning voor alle functies die uw toepassing of database nodig heeft.
* U wilt stresstesten uitvoeren voor verschillende werkbelastingsniveaus, maar tegelijkertijd wilt u niet de hele tijd veel fysieke machines bezitten en onderhouden.

In het volgende diagram wordt een on-premises scenario en de oplossing met de cloud ingeschakeld. In dit scenario plaatst u de presentatielaag in webrollen, de bedrijfslaag in werknemersrollen, maar de gegevenslaag in virtuele machines in Azure. Het uitvoeren van meerdere kopieën van de presentatielaag in verschillende webrollen zorgt ervoor dat saldoaanvragen over deze aanvragen worden geladen. Wanneer u Azure Cloud Services combineert met Azure Virtual Machines, raden we u aan ook [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) in te stellen. Met [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)u stabiele en permanente privé-IP-adressen hebben binnen dezelfde cloudservice in de cloud. Zodra u een virtueel netwerk voor uw virtuele machines en cloudservices definieert, kunnen ze onderling communiceren via het privé-IP-adres. Bovendien biedt het hebben van virtuele machines en Azure-web-/worker-rollen in hetzelfde [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) een lage latentie en veiligere connectiviteit. Zie [Wat is een cloudservice](../../../cloud-services/cloud-services-choose-me.md)voor meer informatie.

Zoals te zien in het diagram, distribueert Azure Load Balancer verkeer over meerdere virtuele machines en bepaalt ook met welke webserver of toepassingsserver verbinding moet worden gemaakt. Het hebben van meerdere exemplaren van de web- en toepassingsservers achter een load balancer zorgt voor de hoge beschikbaarheid van de presentatielaag en de bedrijfslaag. Zie [Aanbevolen procedures voor toepassingspatronen waarvoor SQL HADR vereist is](#best-practices-for-application-patterns-requiring-sql-hadr)voor meer informatie.

![Toepassingspatronen met Cloud Services](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Een andere benadering voor het implementeren van dit toepassingspatroon is het gebruik van een geconsolideerde webrol die zowel presentatielagen als onderdelen van bedrijfslagen bevat, zoals in het volgende diagram wordt weergegeven. Dit toepassingspatroon is handig voor toepassingen waarvoor een stateful ontwerp vereist is. Aangezien Azure stateless compute nodes biedt in web- en werknemersrollen, raden we u aan een logica te implementeren om sessiestatus op te slaan met behulp van een van de volgende technologieën: [Azure Caching,](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/) [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) of Azure SQL [Database](../../../sql-database/sql-database-technical-overview.md).

![Toepassingspatronen met Cloud Services](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Patroon met Azure VM's, Azure SQL Database en Azure App Service (Web Apps)
Het primaire doel van dit toepassingspatroon is om u te laten zien hoe u Azure-infrastructuur als service (IaaS)-componenten combineren met Azure platform-as-a-service-componenten (PaaS) in uw oplossing. Dit patroon is gericht op Azure SQL Database voor relationele gegevensopslag. Het bevat GEEN SQL Server in een Virtuele Azure-machine, die deel uitmaakt van de Azure-infrastructuur als serviceaanbieding.

In dit toepassingspatroon implementeert u een databasetoepassing naar Azure door de presentatie- en bedrijfslagen in dezelfde virtuele machine te plaatsen en toegang te krijgen tot een database in SQL Database-servers (Azure SQL Database). U de presentatielaag implementeren met behulp van traditionele IIS-gebaseerde weboplossingen. U ook een gecombineerde presentatie- en bedrijfslaag implementeren met Azure [App Service.](https://azure.microsoft.com/documentation/services/app-service/web/)

Dit toepassingspatroon is handig wanneer:

* U hebt al een bestaande SQL Database-server geconfigureerd in Azure en u wilt uw toepassing snel testen.
* U wilt de mogelijkheden van de Azure-omgeving testen.
* U wilt snel ontwikkel- en testomgevingen voor korte tijd inrichten.
* Uw bedrijfslogica en gegevenstoegangscomponenten kunnen op zichzelf staan in een webtoepassing.

In het volgende diagram wordt een on-premises scenario en de oplossing met de cloud ingeschakeld. In dit scenario plaatst u de toepassingslagen in één virtuele machine in Azure en hebt u toegang tot gegevens in Azure SQL Database.

![Gemengd toepassingspatroon](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Als u ervoor kiest een gecombineerde web- en toepassingslaag te implementeren met Azure Web Apps, raden we u aan de middelste laag of toepassingslaag als dynamic-link librarie-bibliotheken (DLL's) in de context van een webtoepassing te houden.

Bekijk bovendien de aanbevelingen in de sectie [Webontwikkelingsstrategieën vergelijken in Azure](#comparing-web-development-strategies-in-azure) aan het einde van dit artikel voor meer informatie over programmeertechnieken.

## <a name="n-tier-hybrid-application-pattern"></a>Hybride toepassingspatroon op N-laags
In het hybride toepassingspatroon op n-tier implementeert u uw toepassing in meerdere lagen die zijn verdeeld tussen on-premises en Azure. Daarom maakt u een flexibel en herbruikbaar hybride systeem, dat u wijzigen of een specifieke laag toevoegen zonder de andere lagen te wijzigen. Als u uw bedrijfsnetwerk wilt uitbreiden naar de cloud, gebruikt u [azure virtual network-service.](../../../virtual-network/virtual-networks-overview.md)

Dit hybride toepassingspatroon is handig wanneer:

* U wilt applicaties bouwen die deels in de cloud en deels on-premises worden uitgevoerd.
* U wilt sommige of alle elementen van een bestaande on-premises toepassing migreren naar de cloud.
* U wilt bedrijfstoepassingen verplaatsen van on-premises gevirtualiseerde platforms naar Azure.
* U wilt een infrastructuuromgeving bezitten die op en neer kan schalen op aanvraag.
* U wilt snel ontwikkel- en testomgevingen voor korte tijd inrichten.
* U wilt een kosteneffectieve manier om back-ups te maken voor bedrijfsdatabasetoepassingen.

In het volgende diagram wordt een hybride toepassingspatroon op n-lagen getoond dat zich uitstrekt over on-premises en Azure. Zoals in het diagram wordt weergegeven, bevat de on-premises infrastructuur [de Active Directory Domain Services-domeincontroller](https://technet.microsoft.com/library/hh831484.aspx) ter ondersteuning van gebruikersverificatie en -autorisatie. Houd er rekening mee dat het diagram een scenario weergeeft, waarbij sommige delen van de gegevenslaag in een on-premises datacenter leven terwijl sommige delen van de gegevenslaag in Azure worden uitgevoerd. Afhankelijk van de behoeften van uw toepassing, u verschillende andere hybride scenario's implementeren. U bijvoorbeeld de presentatielaag en de bedrijfslaag in een on-premises omgeving behouden, maar de gegevenslaag in Azure.

![N-tier toepassingspatroon](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

In Azure u Active Directory gebruiken als een zelfstandige cloudmap voor uw organisatie of u bestaande on-premises Active Directory ook integreren met [Azure Active Directory.](https://azure.microsoft.com/documentation/services/active-directory/) Zoals te zien in het diagram, kunnen de onderdelen van de bedrijfslaag toegang krijgen tot meerdere gegevensbronnen, zoals [SQL Server in Azure](virtual-machines-windows-sql-server-iaas-overview.md) via een privé-ip-adres, tot on-premises SQL Server via Azure Virtual [Network](../../../virtual-network/virtual-networks-overview.md)of tot [SQL Database](../../../sql-database/sql-database-technical-overview.md) met behulp van de .NET Framework-gegevensprovidertechnologieën. In dit diagram is Azure SQL Database een optionele gegevensopslagservice.

In het hybride toepassingspatroon op n-lagen u de volgende werkstroom implementeren in de opgegeven volgorde:

1. Identificeer enterprise database-toepassingen die naar de cloud moeten worden verplaatst met behulp van de [Microsoft Assessment and Planning (MAP) Toolkit.](https://microsoft.com/map) De MAP Toolkit verzamelt voorraad- en prestatiegegevens van computers die u overweegt voor virtualisatie en geeft aanbevelingen over capaciteits- en beoordelingsplanning.
2. Plan de resources en configuratie die nodig zijn in het Azure-platform, zoals opslagaccounts en virtuele machines.
3. Netwerkconnectiviteit instellen tussen het bedrijfsnetwerk on-premises en [Azure Virtual Network.](../../../virtual-network/virtual-networks-overview.md) Als u de verbinding tussen het bedrijfsnetwerk on-premises en een virtuele machine in Azure wilt instellen, gebruikt u een van de volgende twee methoden:
   
   1. Een verbinding tot stand brengen tussen on-premises en Azure via openbare eindpunten op een virtuele machine in Azure. Deze methode biedt een eenvoudige installatie en stelt u in staat sql server-verificatie te gebruiken in uw virtuele machine. Stel bovendien de regels van uw netwerkbeveiligingsgroep in om het openbare verkeer naar de VM te beheren. Zie [Externe toegang tot uw VM toestaan met behulp van de Azure-portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.
   2. Een verbinding tot stand brengen tussen on-premises en Azure via de VPN-tunnel (Azure Virtual Private Network). Met deze methode u domeinbeleid uitbreiden naar een virtuele machine in Azure. Bovendien u firewallregels instellen en Windows-verificatie gebruiken in uw virtuele machine. Momenteel ondersteunt Azure veilige vpn-site en point-to-site VPN-verbindingen:
      
      * Met een beveiligde site-to-site-verbinding u netwerkconnectiviteit tot stand brengen tussen uw on-premises netwerk en uw virtuele netwerk in Azure. Het wordt aanbevolen om uw on-premises datacenteromgeving te verbinden met Azure.
      * Met een veilige point-to-site-verbinding u netwerkconnectiviteit tot stand brengen tussen uw virtuele netwerk in Azure en uw afzonderlijke computers die overal worden uitgevoerd. Het wordt meestal aanbevolen voor ontwikkelings- en testdoeleinden.
      
      Zie Verbinding maken met [een SQL Server Virtual Machine op Azure](virtual-machines-windows-sql-connect.md)voor informatie over het maken van verbinding met SQL Server in Azure.
4. Geplande taken en waarschuwingen instellen die een back-up maken van on-premises gegevens in een schijf met virtuele machines in Azure. Zie [SQL Server Backup and Restore met Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx) en Backup and Restore for SQL Server in Azure Virtual Machines [voor](virtual-machines-windows-sql-backup-recovery.md)meer informatie.
5. Afhankelijk van de behoeften van uw toepassing u een van de volgende drie veelvoorkomende scenario's implementeren:
   
   1. U uw webserver, toepassingsserver en ongevoelige gegevens in een databaseserver in Azure bewaren terwijl u de gevoelige gegevens on-premises houdt.
   2. U uw webserver en toepassingsserver on-premises houden, terwijl de databaseserver in een virtuele machine in Azure zit.
   3. U uw databaseserver, webserver en toepassingsserver on-premises houden terwijl u de databasereplica's in virtuele machines in Azure bewaart. Met deze instelling kunnen de on-premises webservers of rapportagetoepassingen toegang krijgen tot de databasereplica's in Azure. Daarom u bereiken om de werklast in een on-premises database te verlagen. We raden u aan dit scenario te implementeren voor zware leesworkloads en ontwikkelingsdoeleinden. Zie AlwaysOn-beschikbaarheidsgroepen bij hoge beschikbaarheid en [herstel na noodgevallen voor SQL Server in Azure Virtual Machines voor](virtual-machines-windows-sql-high-availability-dr.md)informatie over het maken van databasereplica's in Azure.

## <a name="comparing-web-development-strategies-in-azure"></a>Webontwikkelingsstrategieën vergelijken in Azure
Als u een sql-servergebaseerde toepassing op meerdere niveaus in Azure wilt implementeren en implementeren, u een van de volgende twee programmeermethoden gebruiken:

* Stel een traditionele webserver (IIS - Internet Information Services) in Azure in en verkrijg toegang tot databases in SQL Server in Azure Virtual Machines.
* Een cloudservice implementeren en implementeren voor Azure. Controleer vervolgens of deze cloudservice toegang heeft tot databases in SQL Server in Azure Virtual-machines. Een cloudservice kan meerdere web- en werknemersrollen bevatten.

De volgende tabel biedt een vergelijking van traditionele webontwikkeling met Azure Cloud Services en Azure Web Apps met betrekking tot SQL Server in Azure Virtual Machines. De tabel bevat Azure Web Apps omdat het mogelijk is sql server in Azure VM te gebruiken als gegevensbron voor Azure Web Apps via het openbare virtuele IP-adres of de DNS-naam.

|  | Traditionele webontwikkeling in Azure Virtual Machines | Cloudservices in Azure | Webhosting met Azure Web Apps |
| --- | --- | --- | --- |
| **Toepassingsmigratie vanuit on-premises** |Bestaande toepassingen as-is. |Toepassingen hebben web- en werknemersrollen nodig. |Bestaande toepassingen zoals-is, maar geschikt voor zelfstandige webtoepassingen en webservices die snelle schaalbaarheid vereisen. |
| **Ontwikkeling en implementatie** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS Manager, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Elke cloudservice heeft twee omgevingen waarop u uw servicepakket en -configuratie implementeren: fasering en productie. U een cloudservice implementeren in de faseringsomgeving om deze te testen voordat u deze naar productie promoot. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Beheer en installatie** |U bent verantwoordelijk voor administratieve taken op de toepassing, gegevens, firewallregels, virtueel netwerk en besturingssysteem. |U bent verantwoordelijk voor administratieve taken op de toepassing, gegevens, firewallregels en virtueel netwerk. |Je bent verantwoordelijk voor administratieve taken op de applicatie en data. |
| **Hoge beschikbaarheid en disaster recovery (HADR)** |We raden u aan virtuele machines in dezelfde beschikbaarheidsset en in dezelfde cloudservice te plaatsen. Als u uw VM's in dezelfde beschikbaarheidsset houdt, kan Azure de knooppunten met hoge beschikbaarheid in afzonderlijke foutdomeinen plaatsen en domeinen upgraden. Op dezelfde manier kunnen het behouden van uw VM's in dezelfde cloudservice load balancing mogelijk maken en kunnen VM's rechtstreeks met elkaar communiceren via het lokale netwerk binnen een Azure-datacenter.<br/><br/>U bent verantwoordelijk voor de implementatie van een oplossing voor hoge beschikbaarheid en noodherstel voor SQL Server in Azure Virtual Machines om downtime te voorkomen. Zie Hoge beschikbaarheid en [disaster recovery voor SQL Server in Azure Virtual Machines voor](virtual-machines-windows-sql-high-availability-dr.md)ondersteunde HADR-technologieën.<br/><br/>U bent verantwoordelijk voor het maken van back-ups van uw eigen gegevens en toepassing.<br/><br/>Azure kan uw virtuele machines verplaatsen als de hostmachine in het datacenter uitvalt als gevolg van hardwareproblemen. Bovendien kan er geplande downtime van uw VM zijn wanneer de hostmachine wordt bijgewerkt voor beveiligings- of software-updates. Daarom raden we u aan om ten minste twee VM's in elke toepassingslaag te onderhouden om de continue beschikbaarheid te garanderen. Azure biedt geen SLA voor één virtuele machine. |Azure beheert de fouten die voortvloeien uit de onderliggende hardware- of besturingssysteemsoftware. We raden u aan meerdere exemplaren van een web- of werknemersrol te implementeren om de hoge beschikbaarheid van uw toepassing te garanderen. Zie Cloud [Services, Virtual Machines en Virtual Network Service Level Agreement](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)voor meer informatie.<br/><br/>U bent verantwoordelijk voor het maken van back-ups van uw eigen gegevens en toepassing.<br/><br/>Voor databases die zich in een SQL Server-database in een Azure VM bevinden, bent u verantwoordelijk voor het implementeren van een oplossing voor hoge beschikbaarheid en noodherstel om downtime te voorkomen. Zie Hoge beschikbaarheid en disaster recovery voor SQL Server in Azure Virtual Machines voor ondersteunde HDAR-technologieën.<br/><br/>**SQL Server Database Mirroring**: Gebruiken met Azure Cloud Services (web-/werknemerrollen). SQL Server VM's en een cloudserviceproject kunnen zich in hetzelfde Azure Virtual Network bevinden. Als SQL Server VM zich niet in hetzelfde virtuele netwerk bevindt, moet u een SQL Server Alias maken om communicatie naar de instantie van SQL Server te leiden. Bovendien moet de aliasnaam overeenkomen met de SQL Server-naam. |Hoge beschikbaarheid wordt overgenomen van Azure-werkrollen, Azure blob-opslag en Azure SQL-database. Azure Storage onderhoudt bijvoorbeeld drie replica's van alle blob-, tabel- en wachtrijgegevens. Op een bepaald moment houdt Azure SQL Database drie replica's van gegevens aan: één primaire replica en twee secundaire replica's. Zie [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) en [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md)voor meer informatie.<br/><br/>Wanneer u SQL Server in Azure VM gebruikt als gegevensbron voor Azure Web Apps, moet u er rekening mee houden dat Azure Web Apps geen ondersteuning biedt voor Azure Virtual Network. Met andere woorden, alle verbindingen van Azure Web Apps naar SQL Server VM's in Azure moeten door openbare eindpunten van virtuele machines gaan. Dit kan leiden tot enkele beperkingen voor scenario's met hoge beschikbaarheid en noodherstel. De clienttoepassing op Azure Web Apps die verbinding maakt met SQL Server VM met databasemirroring, kan bijvoorbeeld geen verbinding maken met de nieuwe primaire server, omdat voor databasemirroring vereist dat u Azure Virtual Network instelt tussen SQL Server-hostVM's in Azure. Daarom wordt het gebruik van **SQL Server Database Mirroring** met Azure Web Apps momenteel niet ondersteund.<br/><br/>**SQL Server AlwaysOn-beschikbaarheidsgroepen**: U AlwaysOn-beschikbaarheidsgroepen instellen wanneer u Azure Web Apps gebruikt met SQL Server VM's in Azure. U moet echter AlwaysOn Availability Group Listener configureren om de communicatie naar de primaire replica te routeren via openbare eindpunten met een evenwichtige last. |
| **cross-premises connectiviteit** |U Azure Virtual Network gebruiken om verbinding te maken met on-premises. |U Azure Virtual Network gebruiken om verbinding te maken met on-premises. |Azure Virtual Network wordt ondersteund. Zie [Web Apps Virtual Network Integration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)voor meer informatie. |
| **Schaalbaarheid** |Scale-up is beschikbaar door het vergroten van de virtuele machine maten of het toevoegen van meer schijven. Zie [Virtuele machineformaten voor Azure voor](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie over virtuele machineformaten.<br/><br/>**Voor Database Server**: Scale-out is beschikbaar via databasepartitioning technieken en SQL Server AlwaysOn Availability groepen.<br/><br/>Voor zware leesworkloads u [AlwaysOn-beschikbaarheidsgroepen](https://msdn.microsoft.com/library/hh510230.aspx) gebruiken op meerdere secundaire knooppunten en SQL Server-replicatie.<br/><br/>Voor zware schrijfworkloads u horizontale partitioneringsgegevens implementeren op meerdere fysieke servers om toepassingsscale-out te bieden.<br/><br/>Bovendien u een scale-out implementeren met [SQL Server met Gegevensafhankelijke routering.](https://technet.microsoft.com/library/cc966448.aspx) Met Data Dependent Routing (DDR) moet u het partitiemechanisme implementeren in de clienttoepassing, meestal in de laag bedrijfslagen, om de databaseaanvragen naar meerdere SQL Server-knooppunten te routeren. De bedrijfslaag bevat toewijzingen voor de manier waarop de gegevens worden verdeeld en welk knooppunt de gegevens bevat.<br/><br/>U toepassingen schalen waarop virtuele machines worden uitgevoerd. Zie [Een toepassing schalen voor](../../../cloud-services/cloud-services-how-to-scale-portal.md)meer informatie.<br/><br/>**Belangrijke opmerking:** met de functie **AutoScale** in Azure u de virtuele machines die door uw toepassing worden gebruikt, automatisch verhogen of verkleinen. Deze functie garandeert dat de ervaring van de eindgebruiker niet negatief wordt beïnvloed tijdens piekperioden en vm's worden uitgeschakeld wanneer de vraag laag is. Het wordt aanbevolen dat u de optie AutoScale niet instelt voor uw cloudservice als deze SQL Server VM's bevat. De reden hiervoor is dat Azure met de functie AutoScale een virtuele machine kan inschakelen wanneer het CPU-gebruik in die VM hoger is dan een bepaalde drempelwaarde, en een virtuele machine uit te schakelen wanneer het CPU-gebruik lager wordt dan deze. De functie AutoScale is handig voor stateloze toepassingen, zoals webservers, waarbij elke virtuele machinekan de werkbelasting beheren zonder verwijzingen naar eerdere status. De functie AutoScale is echter niet handig voor stateful-toepassingen, zoals SQL Server, waarbij slechts één exemplaar het mogelijk maakt om naar de database te schrijven. |Scale-up is beschikbaar met behulp van meerdere web- en werknemersrollen. Zie [Grooten configureren voor cloudservices voor](../../../cloud-services/cloud-services-sizes-specs.md)meer informatie over virtuele machineformaten voor webrollen en werknemersrollen.<br/><br/>Wanneer u **Cloud Services gebruikt,** u meerdere rollen definiëren om verwerking te distribueren en ook flexibele schaling van uw toepassing te bereiken. Elke cloudservice bevat een of meer webrollen en/of werknemersrollen, elk met zijn eigen toepassingsbestanden en configuratie. U een cloudservice opschalen door het aantal rolexemplaren (virtuele machines) dat voor een rol is geïmplementeerd te verhogen en een cloudservice te verkleinen door het aantal rolexemplaren te verminderen. Zie [Azure Execution Models](../../../cloud-services/cloud-services-choose-me.md)voor gedetailleerde informatie.<br/><br/>Scale-out is beschikbaar via ingebouwde Azure-ondersteuning voor hoge beschikbaarheid via [Cloud Services, Virtuele machines en Overeenkomst op het niveau](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) van de virtuele netwerkservice en load balancer.<br/><br/>Voor een toepassing met meerdere lagen raden we u aan de toepassing web-/werknemerrollen via Azure Virtual Network te verbinden met vm's voor databaseserver. Bovendien biedt Azure taakverdeling voor VM's in dezelfde cloudservice, waarbij gebruikersverzoeken over deze service worden verspreid. Virtuele machines die op deze manier zijn verbonden, kunnen rechtstreeks met elkaar communiceren via het lokale netwerk binnen een Azure-datacenter.<br/><br/>U **AutoScale** instellen op de Azure-portal en de planningstijden. Zie [Automatisch schalen configureren voor een cloudservice in de portal voor](../../../cloud-services/cloud-services-how-to-scale-portal.md)meer informatie. |**Op- en afschalen**: U de grootte van de instantie (VM) die is gereserveerd voor uw website vergroten/verkleinen.<br/><br/>Uitschalen: u meer gereserveerde exemplaren (VM's) toevoegen voor uw website.<br/><br/>U **AutoScale** instellen op de portal en de planningstijden. Zie [Web-apps schalen voor](../../../app-service/manage-scale-up.md)meer informatie. |

Zie [Azure Web Apps, Cloud Services en VM's](/azure/architecture/guide/technology-choices/compute-decision-tree)voor meer informatie over het kiezen tussen deze programmeermethoden: Wanneer u welke wilt gebruiken.

## <a name="next-steps"></a>Volgende stappen
Zie SQL Server op Azure Virtual Machines Overview voor meer informatie over het uitvoeren van SQL Server in Azure [Virtual-machines.](virtual-machines-windows-sql-server-iaas-overview.md)

