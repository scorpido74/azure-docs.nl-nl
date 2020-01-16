---
title: Aan de slag-hand leiding voor Azure IT-Opera tors | Microsoft Docs
description: Aan de slag-hand leiding voor Azure IT-Opera tors
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: e6ef4cc825d40615fa8a6aee7dff8d4542407974
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75970847"
---
# <a name="get-started-for-azure-it-operators"></a>Aan de slag met Azure IT-Opera tors

In deze hand leiding worden kern concepten geïntroduceerd die betrekking hebben op de implementatie en het beheer van een Microsoft Azure-infra structuur. Als u geen ervaring hebt met Cloud Computing of Azure zelf, kunt u met deze hand leiding snel aan de slag met concepten, implementatie en beheer Details. In veel secties van deze hand leiding wordt een bewerking besproken, zoals het implementeren van een virtuele machine. vervolgens geeft u een koppeling op voor gedetailleerde technische details.

## <a name="cloud-computing-overview"></a>Overzicht van Cloud Computing

Cloud Computing biedt een modern alternatief voor het traditionele on-premises Data Center. Leveranciers van open bare Clouds bieden en beheren alle computer infrastructuren en de onderliggende beheer software. Deze leveranciers bieden een groot aantal Cloud Services. Een Cloud service in dit geval kan een virtuele machine, een webserver of een in de Cloud gehoste data base-engine zijn. Als klant van een Cloud provider huur u deze Cloud Services op basis van de behoeften. In dat geval kunt u de kapitaal kosten van hardwarematig onderhoud omzetten in een operationele uitgave. Een Cloud service biedt ook de volgende voor delen:

- Snelle implementatie van grote reken omgevingen

- Snelle toewijzing van systemen die niet meer nodig zijn

- Eenvoudige implementatie van traditionele complexe systemen, zoals load balancers

- Mogelijkheid om flexibele reken capaciteit te bieden of te schalen wanneer dit nodig is

- Voordeligere computer omgevingen

- Toegang vanaf elke locatie met een webgebaseerde portal of programmatische automatisering

- Cloud Services om te voldoen aan de meeste reken-en toepassings behoeften

Met on-premises infra structuur hebt u volledige controle over de hardware en software die is geïmplementeerd. In het verleden heeft dit geleid tot beslissingen over de aankoop van hardware die zich richten op het omhoog schalen. Een voor beeld is het kopen van een server met meer kernen om te voldoen aan de prestatie vereisten. Helaas kan deze infra structuur buiten een vraag venster worden ondergebruikt. Met Azure kunt u alleen de infra structuur implementeren die u nodig hebt en dit op elk gewenst moment aanpassen. Dit leidt tot een focus op uitbrei ding van de implementatie van extra reken knooppunten om te voldoen aan de prestatie behoeften. Het uitschalen van Cloud Services is rendabeler dan het schalen van de dure hardware.

Micro soft heeft veel Azure-data centers overal ter wereld geïmplementeerd, met meer gepland. Daarnaast neemt micro soft soevereine Clouds in regio's zoals China en Duitsland uit. Alleen de grootste wereld wijde ondernemingen kunnen data centers op deze manier implementeren, waardoor het voor ondernemingen van elke omvang gemakkelijk is om hun services dicht bij hun klanten te implementeren.

Voor kleine bedrijven biedt Azure een goedkope invoer punt, met de mogelijkheid om snel te schalen als de vraag naar de reken kracht toeneemt. Dit voor komt een grote investering in de voor grond in de infra structuur en biedt de flexibiliteit om systemen te bouwen en opnieuw te bouwen als dat nodig is. Het gebruik van Cloud Computing past goed met het model voor schaal snelle en failover-snelle opstart groei.

Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor meer informatie over de beschik bare Azure-regio's.

### <a name="cloud-computing-model"></a>Model voor Cloud Computing

Azure maakt gebruik van een cloud computing model op basis van de service categorieën die aan klanten worden gegeven. De drie service categorieën zijn onder andere infra structuur als een service (IaaS), platform as a Service (PaaS) en software as a Service (SaaS). Leveranciers delen enige of alle verantwoordelijkheid voor onderdelen in de computer stack in elk van deze categorieën. Laten we eens kijken naar de categorieën voor Cloud Computing.
Vergelijking van ![voor Cloud Computing-stacks](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastructure as a Service

Een IaaS-Cloud leverancier voert en beheert alle fysieke Compute-resources en de vereiste software om computer virtualisatie in te scha kelen. Een klant van deze service implementeert virtuele machines in deze gehoste data centers. Hoewel de virtuele machines zich in een extern Data Center bevinden, heeft de IaaS-Consumer controle over de configuratie en het beheer van het besturings systeem, waarbij de onderliggende infra structuur naar de Cloud leverancier wordt overgelaten.

Azure bevat verschillende IaaS-oplossingen, zoals virtuele machines, virtuele-machine schaal sets en de gerelateerde netwerk infrastructuur. Virtuele machines zijn een populaire keuze voor het migreren van services naar Azure, omdat hiermee het migratie model "lift en verschuiving" is ingeschakeld. U kunt een virtuele machine configureren, zoals de infra structuur die uw services in uw Data Center uitvoert, en vervolgens uw software naar de nieuwe virtuele machine migreren. Het kan zijn dat u configuratie-updates moet maken, zoals Url's voor andere services of opslag, maar u kunt op deze manier veel toepassingen migreren.

Virtuele-machine schaal sets zijn gebouwd op Azure Virtual Machines en bieden een eenvoudige manier om clusters van identieke Vm's te implementeren. Virtuele-machine schaal sets bieden ook ondersteuning voor automatisch schalen, zodat er zo nodig nieuwe Vm's kunnen worden geïmplementeerd. Dit zorgt ervoor dat de schaal van de virtuele machine een ideaal platform is voor het hosten van micro service-reken clusters op een hoger niveau, zoals Azure Service Fabric en Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: platform as a Service

Met PaaS implementeert u uw toepassing in een omgeving die de leverancier van de Cloud service biedt. De leverancier doet al het infrastructuur beheer, zodat u zich kunt concentreren op het ontwikkelen van toepassingen en gegevens beheer.

Azure biedt verschillende PaaS Compute-aanbiedingen, met inbegrip van de Web Apps functie van Azure App Service en Azure Cloud Services (web-en werk rollen). In beide gevallen hebben ontwikkel aars meerdere manieren om hun toepassing te implementeren zonder iets te weten over de NUTS en schichten die dit ondersteunen. Ontwikkel aars hoeven geen virtuele machines (Vm's) te maken, gebruik Remote Desktop Protocol (RDP) om u aan te melden bij elke computer of om de toepassing te installeren. Ze bevinden zich gewoon op een knop (of dicht bij elkaar) en de hulpprogram ma's die door micro soft worden geleverd, leveren de Vm's en implementeren en installeren van de toepassing.

#### <a name="saas-software-as-a-service"></a>SaaS: Software as a Service

SaaS is software die centraal wordt gehost en beheerd. Het is doorgaans gebaseerd op een multi tenant-architectuur: er wordt één versie van de toepassing gebruikt voor alle klanten. Het kan worden uitgebreid naar meerdere instanties om te zorgen voor de beste prestaties op alle locaties. SaaS-software wordt doorgaans in licentie gegeven via een maandelijks of jaarlijks abonnement. SaaS-software leveranciers zijn verantwoordelijk voor alle onderdelen van de software stack, zodat u alleen beheert dat de services worden ondersteund.

Microsoft Office 365 is een goed voor beeld van een SaaS-aanbieding. Abonnees betalen een maandelijks of jaarlijks abonnements tarief en ze ontvangen micro soft Exchange, micro soft OneDrive en de rest van de Microsoft Office Suite als een service. Abonnees krijgen altijd de meest recente versie en de Exchange-Server wordt voor u beheerd. Vergeleken met het installeren en upgraden van Office elk jaar, is dit minder duur en vereist minder inspanning.

## <a name="azure-services"></a>Azure-services

Azure biedt veel services in het cloud computing platform. Deze services omvatten het volgende:

### <a name="compute-services"></a>Compute services

Services voor het hosten en uitvoeren van de werk belasting van de toepassing:

- Azure-Virtual Machines: zowel Linux als Windows

- App Services (Web Apps, Mobile Apps, Logic Apps, API Apps en functie-apps)

- Azure Batch (voor grootschalige parallelle en batch Compute-taken)

- Azure Service Fabric

- Azure-containerservice

### <a name="data-services"></a>Gegevensservices

Services voor het opslaan en beheren van gegevens:

- Azure Storage (omvat de Azure Blob-, wachtrij-, tabel-en bestands Services)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache voor Redis

### <a name="application-services"></a>Toepassingsservices

Services voor het maken en ontwikkelen van toepassingen:

- Azure Active Directory (Azure AD)

- Azure Service Bus voor het verbinden van gedistribueerde systemen

- Azure HDInsight voor het verwerken van big data

- Azure scheduler

- Azure Media Services

### <a name="network-services"></a>Netwerk services

Services voor netwerken in Azure en tussen Azure en on-premises data centers:

- Virtual Network van Azure

- ExpressRoute van Azure

- Azure-provided DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Zie de [documentatie van Azure service](https://docs.microsoft.com/azure)voor gedetailleerde documentatie over Azure-Services.

## <a name="azure-key-concepts"></a>Azure-basis concepten

### <a name="datacenters-and-regions"></a>Data centers en regio's

Azure is een wereld wijd Cloud platform dat algemeen beschikbaar is in veel regio's over de hele wereld. Wanneer u een service, toepassing of virtuele machine inricht in azure, wordt u gevraagd een regio te selecteren. De geselecteerde regio vertegenwoordigt een specifiek Data Center waarin uw toepassing wordt uitgevoerd. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor meer informatie.

Een van de voor delen van het gebruik van Azure is dat u uw toepassingen kunt implementeren in verschillende data centers over de hele wereld. De regio die u kiest, kan invloed hebben op de prestaties van uw toepassing. Het is optimaal om een regio te kiezen die dichter bij de meeste klanten ligt om de latentie in netwerk aanvragen te verminderen. U kunt ook een regio selecteren om te voldoen aan de wettelijke vereisten voor het distribueren van uw app in bepaalde landen/regio's.

### <a name="azure-portal"></a>Azure Portal

De Azure Portal is een webtoepassing die kan worden gebruikt voor het maken, beheren en verwijderen van Azure-resources en-services. Het Azure Portal bevindt zich op [Portal.Azure.com](https://portal.azure.com). Het bevat een aanpasbaar dash board en hulp programma voor het beheer van Azure-resources. Het biedt ook informatie over facturering en abonnementen. Zie [Microsoft Azure-Portal-overzicht](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) en [Azure-resources beheren via de portal](https://docs.microsoft.com/azure/azure-portal/resource-group-portal)voor meer informatie.

### <a name="resources"></a>Resources

Azure-resources zijn afzonderlijke compute-, netwerk-, gegevens-of app-hosting services die zijn geïmplementeerd in een Azure-abonnement. Enkele algemene resources zijn virtuele machines, opslag accounts of SQL-data bases. Azure-Services bestaan vaak uit diverse gerelateerde Azure-resources. Een virtuele machine van Azure kan bijvoorbeeld een VM, opslag account, netwerk adapter en openbaar IP-adres bevatten. Deze resources kunnen afzonderlijk of als groep worden gemaakt, beheerd en verwijderd. Azure-resources worden verderop in deze hand leiding uitgebreid besproken.

### <a name="resource-groups"></a>Resourcegroepen

Een Azure-resource groep is een container met gerelateerde resources voor een Azure-oplossing. De resource groep kan alle resources voor de oplossing bevatten of alleen resources die u als groep wilt beheren. Azure-resource groepen worden verderop in deze hand leiding uitgebreid besproken.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Een Azure Resource Manager sjabloon is een JavaScript Object Notation bestand (JSON) waarmee een of meer resources worden gedefinieerd die moeten worden geïmplementeerd in een resource groep. Hierin worden ook de afhankelijkheden tussen geïmplementeerde resources gedefinieerd. Resource Manager-sjablonen worden verderop in deze hand leiding uitgebreid besproken.

### <a name="automation"></a>Automation

Naast het maken, beheren en verwijderen van resources met behulp van de Azure Portal, kunt u deze activiteiten automatiseren met Power shell of de Azure-opdracht regel interface (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell is een set modules die cmdlets biedt voor het beheren van Azure. U kunt de-cmdlets gebruiken voor het maken, beheren en verwijderen van Azure-Services. De cmdlets kunnen u helpen bij het behalen van consistente, herhaal bare en praktische implementaties. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps) voor meer informatie.

#### <a name="azure-command-line-interface"></a>Azure-opdrachtregelinterface

De Azure-opdracht regel interface is een hulp programma dat u kunt gebruiken om Azure-resources te maken, te beheren en te verwijderen vanaf de opdracht regel. De Azure CLI is beschikbaar voor Linux, Mac OS X en Windows. Zie [de Azure cli installeren](/cli/azure/install-azure-cli)voor meer informatie en technische informatie.

#### <a name="rest-apis"></a>REST API's

Azure is gebouwd op een set REST-Api's die ondersteuning bieden voor de Azure Portal-gebruikers interface. De meeste van deze REST Api's worden ook ondersteund om uw Azure-resources en-apps programmatisch in te richten en te beheren vanaf elk apparaat met een Internet verbinding. Zie de [Azure rest SDK-referentie](https://docs.microsoft.com/rest/api/index)voor meer informatie.

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Beheerders hebben toegang tot Azure PowerShell en Azure CLI via een browser-toegankelijke ervaring met de naam Azure Cloud Shell. Deze interactieve interface biedt een flexibel hulp programma voor Linux-en Windows-beheerders voor het gebruik van de opdracht regel interface van Choice, ofwel bash of Power shell. Azure Cloud Shell kunnen worden geopend via de portal, als een zelfstandige webinterface op [shell.Azure.com](https://shell.azure.com)of vanuit een aantal andere toegangs punten. Zie [overzicht van Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview)voor meer informatie.

## <a name="azure-subscriptions"></a>Azure-abonnementen

Een abonnement is een logische groepering van Azure-Services die zijn gekoppeld aan een Azure-account. Eén Azure-account kan meerdere abonnementen bevatten. Facturering voor Azure-Services wordt uitgevoerd op basis van per abonnement. Azure-abonnementen hebben een account beheerder die volledige controle heeft over het abonnement en een service beheerder die de controle heeft over alle services in het abonnement. Zie [beheerders van Azure-abonnementen toevoegen of wijzigen](../../cost-management-billing/manage/add-change-subscription-administrator.md)voor meer informatie over klassieke abonnements beheerders. Naast beheerders kunnen individuele accounts een gedetailleerde controle krijgen over Azure-resources met behulp van [op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Een Azure-abonnement selecteren en inschakelen

Voordat u met Azure-Services kunt werken, hebt u een abonnement nodig. Er zijn verschillende typen abonnementen beschikbaar.

**Gratis accounts**: de koppeling om u aan te melden voor een gratis account bevindt zich op de [Azure-website](https://azure.microsoft.com/). Dit geeft u een tegoed van 30 dagen voor het uitproberen van een combi natie van resources in Azure. Als u uw tegoed overschrijdt, wordt uw account geschorst. Aan het einde van de proef versie worden uw services buiten gebruik gesteld en werken ze niet meer. U kunt op elk moment een upgrade uitvoeren naar een abonnement met betalen per gebruik.

**MSDN-abonnementen**: als u een MSDN-abonnement hebt, ontvangt u elke maand een specifiek bedrag in azure-tegoed. Als u bijvoorbeeld een abonnement voor micro soft Visual Studio Enterprise met MSDN hebt, krijgt u \$150 per maand in azure-tegoed.

Als u het tegoed overschrijdt, wordt uw service uitgeschakeld totdat de volgende maand wordt gestart. U kunt de bestedings limiet uitschakelen en een credit card toevoegen die wordt gebruikt voor de extra kosten. Sommige van deze kosten zijn korting op MSDN-accounts. U betaalt bijvoorbeeld de Linux-prijs voor Vm's waarop Windows Server wordt uitgevoerd en er worden geen extra kosten in rekening gebracht voor micro soft-servers, zoals Microsoft SQL Server. Dit maakt MSDN-accounts ideaal voor ontwikkelings-en test scenario's.

**BizSpark-accounts**: het micro soft BizSpark-programma biedt veel voor delen voor opstarten. Een van deze voor delen is toegang tot alle micro soft-software voor ontwikkelings-en test omgevingen voor Maxi maal vijf MSDN-accounts. U krijgt $150 in azure-tegoed voor elk van deze vijf MSDN-accounts en u betaalt gereduceerde tarieven voor verschillende Azure-Services, zoals Virtual Machines.

**Betalen**naar gebruik: met dit abonnement betaalt u voor wat u gebruikt door een credit card of bankpas aan het account te koppelen. Als u een organisatie bent, kunt u ook worden goedgekeurd voor facturering.

**Enter prise agreements**: met een Enter prise Agreement kunt u een bepaald aantal services in Azure in het komende jaar gebruiken en betaalt u dat bedrag van tevoren. De toezeg ging die u maakt, wordt gedurende het hele jaar verbruikt. Als u de toezeggings hoeveelheid overschrijdt, kunt u de overschrijding in achterstanden betalen. Afhankelijk van het bedrag van de toezeg ging krijgt u een korting op de services in Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Beheerders toegang verlenen aan een Azure-abonnement

RBAC heeft verschillende ingebouwde rollen die u kunt gebruiken om machtigingen toe te wijzen. Als u een gebruiker beheerder van een Azure-abonnement wilt maken, wijst u deze toe aan de rol van [eigenaar](../../role-based-access-control/built-in-roles.md#owner) op het abonnements bereik. De rol van eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, waaronder het recht om toegang aan anderen te delegeren.

Zie [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.

### <a name="view-billing-information-in-the-azure-portal"></a>Facturerings gegevens weer geven in de Azure Portal

Een belang rijk onderdeel van het gebruik van Azure is de mogelijkheid om facturerings gegevens weer te geven. De Azure Portal biedt gedetailleerde inzicht in de facturerings gegevens van Azure.

Zie [How to down load your Azure billing invoice en dagelijks Usage Data](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)(Engelstalig) voor meer informatie.

### <a name="get-billing-information-from-billing-apis"></a>Facturerings gegevens ophalen van facturering-Api's

Naast het weer geven van de facturering in de portal, kunt u de facturerings gegevens openen met behulp van een script of programma via de REST Api's van Azure billing:

- U kunt de Azure Usage API gebruiken om uw gebruiks gegevens op te halen. U kunt de informatie over het facturerings gebruik verfijnen door verwante Azure-resources te labelen. U kunt bijvoorbeeld elk van de resources in een resource groep labelen met een afdelings naam of project naam en vervolgens de kosten voor die ene tag volgen.

- U kunt de Azure Rate Card-API gebruiken om alle beschik bare resources weer te geven, samen met de meta gegevens en prijs informatie over elk van deze resources.

Zie [Inzicht verkrijgen in het gebruik van Microsoft Azure-resources](../../cost-management-billing/manage/usage-rate-card-overview.md)voor meer informatie.

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognose kosten met de prijs calculator

De prijzen voor elke service in azure wijken af. Veel Azure-Services bieden Basic-, Standard-en Premium-lagen. Normaal gesp roken heeft elke laag verschillende prijs-en prestatie niveaus. Met de [online prijs calculator](https://azure.microsoft.com/pricing/calculator)kunt u prijs schattingen maken. De reken machine bevat flexibiliteit voor het schatten van de kosten voor een enkele resource of een groep resources.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager is een mechanisme voor implementatie, beheer en organisatie voor Azure-resources. Met Resource Manager kunt u veel afzonderlijke resources samen voegen in een resource groep.

Resource Manager bevat ook implementatie mogelijkheden die aanpas bare implementatie en configuratie van gerelateerde resources mogelijk maken. U kunt bijvoorbeeld met behulp van Resource Manager een toepassing implementeren die bestaat uit meerdere virtuele machines, een load balancer en een SQL database als één eenheid. U kunt deze implementaties ontwikkelen met behulp van een resource manager-sjabloon.

Resource Manager biedt diverse voordelen:

- U kunt alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

- U kunt uw oplossing herhaaldelijk implementeren tijdens de ontwikkelings levenscyclus en u betrouwt dat uw resources in een consistente staat worden geïmplementeerd.

- U kunt uw infrastructuur beheren via declaratieve sjablonen in plaats van scripts.

- U kunt de afhankelijkheden tussen resources zo definiëren dat deze in de juiste volgorde worden geïmplementeerd.

- U kunt toegangs beheer Toep assen op alle services in uw resource groep, omdat RBAC systeem eigen is geïntegreerd in het beheer platform.

- U kunt Tags Toep assen op resources om alle resources in uw abonnement logisch te organiseren.

- U kunt de facturering van uw organisatie verduidelijken door de kosten voor een groep resources met dezelfde tag te bekijken.

### <a name="tips-for-creating-resource-groups"></a>Tips voor het maken van resource groepen

Houd rekening met de volgende tips wanneer u beslissingen neemt over uw resource groepen:

- Alle resources in een resource groep moeten dezelfde levens cyclus hebben.

- U kunt een resource slechts aan één groep tegelijk toewijzen.

- U kunt op elk gewenst moment een resource toevoegen aan of verwijderen uit een resource groep. Elke resource moet behoren tot een resource groep. Dus als u een resource uit de ene groep verwijdert, moet u deze toevoegen aan een andere.

- U kunt de meeste typen resources op elk gewenst moment verplaatsen naar een andere resource groep.

- De resources in een resource groep kunnen zich in verschillende regio's bevindt.

- U kunt een resource groep gebruiken om de toegang voor de resources in het bestand te beheren.

### <a name="building-resource-manager-templates"></a>Resource Manager-sjablonen maken

Resource Manager-sjablonen definiëren declaratieve bronnen en resource configuraties die in één resource groep worden geïmplementeerd. U kunt Resource Manager-sjablonen gebruiken om complexe implementaties te organiseren zonder dat u hoeft te wachten op het uitvoeren van scripts of hand matige configuratie. Nadat u een sjabloon hebt ontwikkeld, kunt u deze meerdere keren implementeren: elke keer met een identieke uitkomst.

Een resource manager-sjabloon bestaat uit vier secties:

- **Para meters**: Dit zijn invoer waarden voor de implementatie. Parameter waarden kunnen worden verschaft door een menselijk of een geautomatiseerd proces. Een voor beeld-para meter is een gebruikers naam en wacht woord voor de beheerder voor een Windows-VM. De parameter waarden worden gebruikt tijdens de implementatie wanneer ze worden opgegeven.

- **Variabelen**: deze worden gebruikt om waarden op te slaan die tijdens de implementatie worden gebruikt. In tegens telling tot para meters is een variabele waarde niet opgegeven tijdens de implementatie. In plaats daarvan wordt de code vastgelegd of dynamisch gegenereerd.

- **Resources**: in deze sectie van de sjabloon worden de resources gedefinieerd die moeten worden geïmplementeerd, zoals virtuele machines, opslag accounts en virtuele netwerken.

- **Uitvoer**: nadat een implementatie is voltooid, kan Resource Manager gegevens, zoals dynamisch gegenereerde verbindings reeksen, retour neren.

De volgende mechanismen zijn beschikbaar voor implementatie automatisering:

- **Functies**: u kunt verschillende functies gebruiken in Resource Manager-sjablonen. Dit zijn onder andere bewerkingen, zoals het converteren van een teken reeks naar kleine letters, het implementeren van meerdere exemplaren van een gedefinieerde resource en het dynamisch retour neren van de doel resource groep. Met Resource Manager-functies kunt u dynamische implementaties bouwen.

- **Bron afhankelijkheden**: wanneer u meerdere resources implementeert, hebben sommige resources afhankelijk van andere bronnen. Als u de implementatie wilt vereenvoudigen, kunt u een afhankelijkheids declaratie gebruiken zodat afhankelijke resources vóór de andere worden geïmplementeerd.

- **Sjabloon koppelen**: vanuit een resource manager-sjabloon kunt u een koppeling maken naar een andere sjabloon. Op deze manier kan de implementatie worden uitgevouwen in een set doel gerichte sjablonen.

U kunt Resource Manager-sjablonen samen stellen in een tekst editor. De Azure SDK voor Visual Studio bevat echter hulp middelen die u kunnen helpen. Met Visual Studio kunt u via een wizard Resources toevoegen aan de sjabloon en vervolgens de sjabloon rechtstreeks vanuit Visual Studio implementeren en fouten opsporen. Zie [Azure Resource Manager sjablonen ontwerpen](../../resource-group-authoring-templates.md)voor meer informatie.

Ten slotte kunt u bestaande resource groepen converteren naar een herbruikbare sjabloon vanuit het Azure Portal. Dit kan handig zijn als u een Implementeer bare sjabloon van een bestaande resource groep wilt maken of als u alleen de onderliggende JSON wilt onderzoeken. Als u een resource groep wilt exporteren, selecteert u de knop **Automation-script** uit de instellingen van de resource groep.

## <a name="security-of-azure-resources-rbac"></a>Beveiliging van Azure-resources (RBAC)

U kunt operationele toegang verlenen aan gebruikers accounts bij een opgegeven bereik: abonnement, resource groep of afzonderlijke resource. Dit betekent dat u een set resources kunt implementeren in een resource groep, zoals een virtuele machine en alle gerelateerde resources, en om machtigingen te verlenen aan een specifieke gebruiker of groep. Deze benadering beperkt de toegang tot alleen de resources die bij de doel resource groep horen. U kunt ook toegang verlenen tot één resource, zoals een virtuele machine of een virtueel netwerk.

Als u toegang wilt verlenen, wijst u een rol toe aan de gebruiker of gebruikers groep. Er zijn veel vooraf gedefinieerde rollen. U kunt ook uw eigen aangepaste rollen definiëren.

Hier volgen enkele voor beelden [van ingebouwde rollen in azure](../../role-based-access-control/built-in-roles.md):

- **Eigenaar**: een gebruiker met deze rol kan alles beheren, met inbegrip van toegang.

- **Lezer**: een gebruiker met deze rol kan resources van alle typen (met uitzonde ring van geheimen) lezen, maar kan geen wijzigingen aanbrengen.

- **Inzender voor virtuele machines**: een gebruiker met deze rol kan virtuele machines beheren, maar kan niet het virtuele netwerk beheren waarmee ze zijn verbonden of het opslag account waarin het VHD-bestand zich bevindt.

- **Inzender voor SQL-data**bases: een gebruiker met deze rol kan SQL-data banken beheren, maar niet het beveiligings beleid.

- **SQL-beveiligings beheer**: een gebruiker met deze rol kan het beveiligings beleid van SQL-servers en-data bases beheren.

- **Inzender voor opslag**accounts: een gebruiker met deze rol kan opslag accounts beheren, maar kan geen toegang tot de opslag accounts beheren.

Zie [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines is een van de centrale IaaS-Services in Azure. Azure Virtual Machines ondersteunt de implementatie van virtuele Windows-of Linux-machines in een Microsoft Azure Data Center. Met Azure Virtual Machines hebt u volledige controle over de configuratie van de virtuele machine en bent u verantwoordelijk voor de installatie, configuratie en het onderhoud van de software.

Wanneer u een virtuele machine van Azure implementeert, kunt u een installatie kopie selecteren in azure Marketplace of kunt u een eigen gegeneraliseerde installatie kopie opgeven. Deze installatie kopie wordt gebruikt om het besturings systeem en de initiële configuratie toe te passen. Tijdens de implementatie worden bepaalde configuratie-instellingen door Resource Manager verwerkt, zoals het toewijzen van de computer naam, de beheerders referenties en de netwerk configuratie. U kunt Azure-extensies voor virtuele machines gebruiken om configuraties verder te automatiseren, zoals software-installatie, antivirus configuratie en bewakings oplossingen.

U kunt virtuele machines maken in veel verschillende grootten. De grootte van de virtuele machine bepaalt de toewijzing van resources, zoals de verwerking, het geheugen en de opslag capaciteit. In sommige gevallen zijn specifieke functies, zoals netwerk adapters met RDMA-functionaliteit en SSD-schijven, alleen beschikbaar voor bepaalde VM-grootten. Zie ' grootten voor virtuele machines in azure ' voor [Windows](../../virtual-machines/windows/sizes.md) en [Linux](../../virtual-machines/linux/sizes.md)voor een volledige lijst met VM-grootten en-mogelijkheden.

### <a name="use-cases"></a>Use cases

Omdat virtuele machines van Azure volledige controle over de configuratie bieden, zijn ze ideaal voor een groot aantal server werkbelastingen die niet in een PaaS-model passen. Server werkbelastingen zoals database servers (SQL Server, Oracle of MongoDB), Windows Server Active Directory, micro soft share point en veel meer kunnen worden uitgevoerd op het Microsoft Azure platform. Indien gewenst kunt u dergelijke werk belastingen van een on-premises Data Center verplaatsen naar een of meer Azure-regio's, zonder een grote hoeveelheid herconfiguratie.

### <a name="deployment-of-virtual-machines"></a>Implementatie van virtuele machines

U kunt virtuele Azure-machines implementeren met behulp van de Azure Portal, met behulp van Automation met de module Azure PowerShell of door gebruik te maken van Automation met de platformoverschrijdende CLI.

#### <a name="portal"></a>Portal

Voor het implementeren van een virtuele machine met behulp van de Azure Portal is alleen een actief Azure-abonnement vereist en toegang tot een webbrowser. U kunt veel verschillende installatie kopieën van een besturings systeem selecteren met verschillende configuraties. Alle opslag-en netwerk vereisten worden tijdens de implementatie geconfigureerd. Zie "een virtuele machine maken in de Azure Portal" voor [Windows](../../virtual-machines/windows/quick-create-portal.md) en [Linux](../../virtual-machines/linux/quick-create-portal.md)voor meer informatie.

Naast het implementeren van een virtuele machine vanuit de Azure Portal, kunt u een Azure Resource Manager-sjabloon implementeren vanuit de portal. Hiermee worden alle resources geïmplementeerd en geconfigureerd, zoals gedefinieerd in de sjabloon. Zie [resources implementeren met Resource Manager-sjablonen en Azure Portal](../../azure-resource-manager/templates/deploy-portal.md)voor meer informatie.

#### <a name="powershell"></a>PowerShell

Als u een virtuele machine van Azure implementeert met behulp van Power shell, kunt u volledige implementatie automatisering van alle gerelateerde bronnen van virtuele machines, waaronder opslag en netwerken. Zie [een Windows-VM maken met Resource Manager en Power shell](../../virtual-machines/windows/quick-create-powershell.md)voor meer informatie.

Naast het individueel implementeren van Azure Compute-resources kunt u de module Azure PowerShell gebruiken om een Azure Resource Manager sjabloon te implementeren. Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

#### <a name="command-line-interface-cli"></a>Opdrachtregelinterface (Command-line Interface, CLI)

Net als bij de Power shell-module biedt de Azure-opdracht regel interface implementatie automatisering en kan deze worden gebruikt op Windows-, OS X-of Linux-systemen. Wanneer u de Azure CLI VM-opdracht voor **snel maken** gebruikt, worden alle gerelateerde resources van de virtuele machine (inclusief opslag en netwerken) en de virtuele machine zelf geïmplementeerd. Zie [een virtuele Linux-machine maken in azure met behulp van de CLI](../../virtual-machines/linux/quick-create-cli.md)voor meer informatie.

Op dezelfde manier kunt u de Azure CLI gebruiken om een Azure Resource Manager-sjabloon te implementeren. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../../azure-resource-manager/templates/deploy-cli.md)voor meer informatie.

### <a name="access-and-security-for-virtual-machines"></a>Toegang en beveiliging voor virtuele machines

Voor toegang tot een virtuele machine via internet moet de bijbehorende netwerk interface of load balancer indien van toepassing, worden geconfigureerd met een openbaar IP-adres. Het open bare IP-adres bevat een DNS-naam die wordt omgezet naar de virtuele machine of load balancer. Zie [IP-adressen in azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)voor meer informatie.

U beheert de toegang tot de virtuele machine via het open bare IP-adres met behulp van een NSG-resource (netwerk beveiligings groep). Een NSG fungeert als een firewall en maakt of weigert verkeer via de netwerk interface of het subnet op een reeks gedefinieerde poorten. Als u bijvoorbeeld een Extern bureaublad-sessie wilt maken met een virtuele machine van Azure, moet u de NSG zo configureren dat inkomend verkeer op poort 3389 wordt toegestaan. Zie [poorten openen voor een virtuele machine in azure met behulp van de Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md)voor meer informatie.

Ten slotte moet u, net als bij het beheer van een computer systeem, beveiliging bieden voor een virtuele Azure-machine in het besturings systeem met behulp van beveiligings referenties en software firewalls.

## <a name="azure-storage"></a>Azure Storage

Azure Storage is een door micro soft beheerde service die duurzame, schaal bare en redundante opslag biedt. U kunt een Azure-opslag account als resource aan een resource groep toevoegen met behulp van een resource-implementatie methode. Azure bevat vier opslag typen: Blob Storage, File Storage, tabel opslag en wachtrij opslag. Bij het implementeren van een opslag account zijn twee account typen beschikbaar, algemeen gebruik en Blob-opslag. Een opslag account voor algemeen gebruik biedt toegang tot alle vier de opslag typen. Blob Storage-accounts zijn vergelijkbaar met accounts voor algemene doel einden, maar bevatten speciale blobs met hot-en koude toegangs lagen. Zie [Azure Blob Storage](../../storage/blobs/storage-blob-storage-tiers.md)voor meer informatie over Blob Storage.

Azure Storage-accounts kunnen worden geconfigureerd met verschillende redundantie niveaus:

- **Lokaal redundante opslag** biedt een hoge Beschik baarheid door ervoor te zorgen dat drie kopieën van alle gegevens synchroon worden gemaakt voordat een schrijf bewerking wordt uitgevoerd. Deze kopieën worden opgeslagen in één faciliteit in één regio. De replica's bevinden zich in afzonderlijke fout domeinen en upgrade domeinen. Dit betekent dat de gegevens ook beschikbaar zijn als een opslag knooppunt dat uw gegevens bevat, mislukt of offline wordt gezet om te worden bijgewerkt.

- **Geografisch redundante opslag** maakt drie synchrone kopieën van de gegevens in de primaire regio voor hoge Beschik baarheid en maakt vervolgens asynchroon drie replica's in een gekoppelde regio voor herstel na nood gevallen.

- **Geografisch redundante opslag met lees toegang** is geografisch redundante opslag en de mogelijkheid om de gegevens in de secundaire regio te lezen. Deze mogelijkheid maakt het geschikt voor gedeeltelijk nood herstel. Als er een probleem is met de primaire regio, kunt u uw toepassing wijzigen zodat deze alleen-lezen toegang heeft tot de gekoppelde regio.

### <a name="use-cases"></a>Use cases

Elk opslag type heeft een andere use-case.

#### <a name="blob-storage"></a>Blob-opslag

De Word- *BLOB* is een acroniem voor een *binair large-object*. Blobs zijn niet-gestructureerd, zoals de bestanden die u op uw computer opslaat. In Blob Storage kan elk type tekst of binaire gegevens, zoals een document, mediabestand of toepassingsinstallatieprogramma, worden opgeslagen. U kunt Blob Storage zien als een vorm van objectopslag. Azure Blob-opslag bevat ook Azure Virtual Machines-gegevens schijven.

Azure Storage ondersteunt drie soorten blobs:

- **Blok-blobs** worden gebruikt voor het opslaan van normale bestanden met een grootte van maxi maal 195 GB (4 MB × 50.000 blokken). Blok-blobs worden primair gebruikt om bestanden op te slaan die van begin tot eind worden gelezen, zoals mediabestanden of afbeeldingsbestanden voor websites. Ze zijn benoemde blok-blobs omdat bestanden groter zijn dan 64 MB moeten worden geüpload als kleine blokken. Deze blokken worden vervolgens geconsolideerd (of vastgelegd) in de uiteindelijke blob.

- **Pagina-blobs** worden gebruikt om bestanden met een wille keurige toegang tot Maxi maal 1 TB te bewaren. Pagina-blobs worden voornamelijk gebruikt als de back-upopslag voor de Vhd's die duurzame schijven bieden voor Azure Virtual Machines, de IaaS compute-service in Azure. Ze heten pagina-blobs omdat ze willekeurige lees-/schrijftoegang bieden tot pagina’s van 512 bytes.

- **Toevoeg-blobs** bestaan uit blokken zoals blok-blobs, maar ze zijn geoptimaliseerd voor toevoeg bewerkingen. Deze worden vaak gebruikt voor het vastleggen van gegevens van een of meer bronnen naar dezelfde blob. U kunt bijvoorbeeld al uw traceer logboek registratie naar dezelfde toevoeg-BLOB schrijven voor een toepassing die wordt uitgevoerd op meerdere Vm's. Eén toevoeg-blob kan maximaal 195 GB zijn.

Zie [aan de slag met Azure Blob Storage met .net](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor meer informatie.

#### <a name="file-storage"></a>Bestandsopslag

Azure File Storage is een service die bestands shares in de Cloud aanbiedt met behulp van het standaard SMB-protocol (Server Message Block). De service ondersteunt zowel SMB 2,1 als SMB 3,0. Met Azure File Storage kunt u toepassingen die op bestands shares zijn gebaseerd snel migreren naar Azure, zonder dat u de kosten hoeft te herschrijven. Toepassingen die worden uitgevoerd op virtuele machines van Azure, in Cloud Services of op on-premises clients kunnen een bestands share in de Cloud koppelen. Dit is vergelijkbaar met de manier waarop een bureaublad toepassing een typische SMB-share koppelt. Daarna kan een willekeurig aantal toepassingsonderdelen de File Storage-share tegelijkertijd koppelen en gebruiken.

Omdat een file storage-share een standaard SMB-bestands share is, hebben toepassingen die in Azure worden uitgevoerd, toegang tot gegevens in de share via I/O-Api's van het bestands systeem. Ontwikkel aars kunnen daarom hun bestaande code en vaardig heden gebruiken om bestaande toepassingen te migreren. IT-professionals kunnen Power shell-cmdlets gebruiken om shares voor bestands opslag te maken, koppelen en beheren als onderdeel van het beheer van Azure-toepassingen.

Zie aan de [slag met Azure file storage in Windows](../../storage/files/storage-how-to-use-files-windows.md) of [Azure File Storage gebruiken met Linux](../../storage/files/storage-how-to-use-files-linux.md)voor meer informatie.

#### <a name="table-storage"></a>Tabelopslag

Azure Table Storage is een service waarmee gestructureerde NoSQL-gegevens worden opgeslagen in de cloud. Table Storage is een sleutel/kenmerk Archief met een schema-minder ontwerp. Omdat tabel opslag een schema is, is het eenvoudig om uw gegevens aan te passen aan de ontwikkeling van uw toepassing. De toegang tot gegevens verloopt snel en kostenefficiënt voor alle soorten toepassingen. Table Storage is doorgaans aanzienlijk goedkoper dan traditionele SQL voor vergelijkbare gegevensvolumes.

U kunt Table Storage gebruiken voor het opslaan van flexibele gegevenssets, zoals gebruikersgegevens voor webtoepassingen, adresboeken, apparaatgegevens en alle overige typen metagegevens die uw service nodig heeft. U kunt elk wille keurig aantal entiteiten in een tabel opslaan. Een opslag account kan een wille keurig aantal tabellen bevatten, tot de capaciteits limiet van het opslag account.

Zie [aan de slag met Azure Table Storage](../../cosmos-db/table-storage-how-to-use-dotnet.md)voor meer informatie.

#### <a name="queue-storage"></a>Queue Storage

Azure Queue Storage biedt uitwisseling van berichten tussen toepassingsonderdelen in de cloud. Bij het ontwerpen van toepassingen voor schalen worden toepassings onderdelen vaak losgekoppeld zodat ze onafhankelijk kunnen worden geschaald. Queue Storage biedt asynchrone uitwisseling van berichten voor communicatie tussen toepassingsonderdelen, of deze nu worden uitgevoerd in de cloud, op een desktopcomputer, op een on-premises server of op een mobiel apparaat. Queue Storage biedt ook ondersteuning voor het beheren van asynchrone taken en het samenstellen van proceswerkstromen.

Zie [aan de slag met Azure Queue Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md)voor meer informatie.

### <a name="deploying-a-storage-account"></a>Een opslag account implementeren

Er zijn verschillende opties voor het implementeren van een opslag account.

#### <a name="portal"></a>Portal

Voor het implementeren van een opslag account met behulp van de Azure Portal hebt u alleen een actief Azure-abonnement nodig en toegang tot een webbrowser. U kunt een nieuw opslag account implementeren in een nieuwe of bestaande resource groep. Nadat u het opslag account hebt gemaakt, kunt u een BLOB of bestands share maken met behulp van de portal. U kunt de opslag entiteiten voor tabellen en wacht rijen via een programma maken. Zie [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor meer informatie.

Naast het implementeren van een opslag account vanuit de Azure Portal, kunt u een Azure Resource Manager-sjabloon implementeren vanuit de portal. Hiermee worden alle resources geïmplementeerd en geconfigureerd zoals gedefinieerd in de sjabloon, inclusief eventuele opslag accounts. Zie [resources implementeren met Resource Manager-sjablonen en Azure Portal](../../azure-resource-manager/templates/deploy-portal.md)voor meer informatie.

#### <a name="powershell"></a>PowerShell

Als u een Azure Storage-account implementeert met behulp van Power shell, kunt u een volledige implementatie automatisering van het opslag account uitvoeren. Zie [Using Azure PowerShell with Azure Storage](../../storage/common/storage-powershell-guide-full.md)(Engelstalig) voor meer informatie.

Naast het individueel implementeren van Azure-resources kunt u de module Azure PowerShell gebruiken om een Azure Resource Manager sjabloon te implementeren. Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

#### <a name="command-line-interface-cli"></a>Opdrachtregelinterface (Command-line Interface, CLI)

Net als bij de Power shell-module biedt de Azure-opdracht regel interface implementatie automatisering en kan deze worden gebruikt op Windows-, OS X-of Linux-systemen. U kunt de opdracht Azure CLI- **opslag account maken** gebruiken om een opslag account te maken. Zie [de Azure CLI gebruiken met Azure Storage](../../storage/common/storage-azure-cli.md) voor meer informatie.

Op dezelfde manier kunt u de Azure CLI gebruiken om een Azure Resource Manager-sjabloon te implementeren. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../../resource-group-template-deploy-cli.md)voor meer informatie.

### <a name="access-and-security-for-azure-storage"></a>Toegang en beveiliging voor Azure Storage

Azure Storage wordt op verschillende manieren geopend, met inbegrip van de Azure Portal, tijdens het maken en uitvoeren van de VM, en uit client bibliotheken voor opslag.

#### <a name="virtual-machine-disks"></a>Schijven van virtuele machines

Wanneer u een virtuele machine implementeert, moet u ook een opslag account maken om de schijf met het besturings systeem van de virtuele machine en eventuele extra gegevens schijven op te slaan. U kunt een bestaand opslag account selecteren of een nieuwe maken. Omdat de maximale grootte van een BLOB 1.024 GB is, heeft één VM-schijf een maximale grootte van 1.023 GB. Als u een grotere gegevens schijf wilt configureren, kunt u meerdere gegevens schijven voor de virtuele machine weer geven en deze samen voegen als één logische schijf. Zie "Azure-schijven beheren" voor [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) en [Linux](../../virtual-machines/linux/tutorial-manage-disks.md)voor meer informatie.

#### <a name="storage-tools"></a>Hulpprogram ma's voor opslag

Azure Storage-accounts kunnen worden geopend via veel verschillende opslag verkenners, zoals Visual Studio Cloud Explorer. Met deze hulpprogram ma's kunt u bladeren door opslag accounts en gegevens. Zie [Azure Storage-client hulpprogramma's](../../storage/common/storage-explorers.md)voor meer informatie en een lijst met beschik bare opslag verkenners.

#### <a name="storage-api"></a>Opslag-API

Opslag resources zijn toegankelijk voor elke taal waarin HTTP/HTTPS-aanvragen kunnen worden gemaakt. Daarnaast biedt Azure Storage programmeringsbibliotheken voor verschillende veelgebruikte talen. Deze bibliotheken vereenvoudigen het werken met Azure Storage door gegevens zoals synchrone en asynchrone aanroep, batch verwerking, uitzonderings beheer en automatische nieuwe pogingen te verwerken. Zie [Azure Storage service rest API Reference](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference)(Engelstalig) voor meer informatie.

#### <a name="storage-access-keys"></a>Toegangs sleutels voor opslag

Elk opslag account heeft twee verificatie sleutels, een primaire en een secundaire. Beide kunnen worden gebruikt voor bewerkingen voor opslag toegang. Deze opslag sleutels worden gebruikt voor het beveiligen van een opslag account en zijn vereist om programmatisch toegang te krijgen tot gegevens. Er zijn twee sleutels die incidentele overschakeling van de sleutels toestaan om de beveiliging te verbeteren. Het is essentieel om de sleutels veilig te houden, omdat hun eigendom, samen met de account naam, onbeperkte toegang tot alle gegevens in het opslag account mogelijk maakt.

#### <a name="shared-access-signatures"></a>Shared Access signatures

Als u gebruikers de mogelijkheid wilt geven om de toegang tot uw opslag resources te beheren, kunt u een gedeelde toegangs handtekening maken. Een Shared Access Signature is een token dat kan worden toegevoegd aan een URL waarmee gedelegeerde toegang tot een opslag bron mogelijk wordt. Iedereen die over het token beschikt, heeft toegang tot de bron waar deze naar verwijst met de machtigingen die ze opgeeft, voor de periode dat deze geldig is. Zie [using Shared Access signatures](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)(Engelstalig) voor meer informatie.

## <a name="azure-virtual-network"></a>Virtual Network van Azure

Virtuele netwerken zijn nodig om communicatie tussen virtuele machines te ondersteunen. U kunt subnetten, aangepaste IP-adressen, DNS-instellingen, beveiligings filters en taak verdeling definiëren. Azure biedt ondersteuning voor verschillende gebruiks voorbeelden: Cloud netwerken of hybride virtuele netwerken.

### <a name="cloud-only-virtual-networks"></a>Virtuele netwerken in de Cloud

Een virtueel Azure-netwerk is standaard alleen toegankelijk voor resources die zijn opgeslagen in Azure. Resources die zijn verbonden met hetzelfde virtuele netwerk kunnen met elkaar communiceren. U kunt netwerk interfaces van virtuele machines en load balancers koppelen aan een openbaar IP-adres om de virtuele machine toegankelijk te maken via internet. U kunt de toegang tot de openbaar gemaakte bronnen beveiligen met behulp van een netwerk beveiligings groep.

![Azure-Virtual Network voor een webtoepassing met twee lagen](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybride virtuele netwerken

U kunt een on-premises netwerk verbinden met een virtueel Azure-netwerk met behulp van ExpressRoute of een site-naar-site-VPN-verbinding. In deze configuratie is het virtuele Azure-netwerk in feite een Cloud extensie van uw on-premises netwerk.

Omdat het virtuele netwerk van Azure is verbonden met uw on-premises netwerk, moeten cross-premises virtuele netwerken een uniek deel uitmaken van de adres ruimte die uw organisatie gebruikt. Op dezelfde manier als andere bedrijfs locaties een specifiek IP-subnet toegewezen, wordt Azure een andere locatie wanneer u uw netwerk uitbreidt.
Er zijn verschillende opties voor het implementeren van een virtueel netwerk.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Opdracht regel interface (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager sjablonen

> **Wanneer gebruiken: wanneer**u werkt met Vm's in azure, werkt u met virtuele netwerken. Dit maakt het mogelijk om uw Vm's te segmenteren in open bare en particuliere subnetten, vergelijkbaar met on-premises data centers.
>
> **Aan de slag**: het implementeren van een virtueel Azure-netwerk met behulp van de Azure Portal vereist alleen een actief Azure-abonnement en toegang tot een webbrowser. U kunt een nieuw virtueel netwerk implementeren in een nieuwe of bestaande resource groep. Wanneer u een nieuwe virtuele machine maakt vanuit de portal, kunt u een bestaand virtueel netwerk selecteren of een nieuwe maken. Ga aan de slag en [Maak een virtueel netwerk met behulp van de Azure Portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Toegang en beveiliging voor virtuele netwerken

U kunt Azure Virtual Networks beveiligen met behulp van een netwerk beveiligings groep. Nsg's bevatten een lijst met ACL-regels (Access Control List) waarmee netwerk verkeer naar uw VM-exemplaren in een virtueel netwerk wordt toegestaan of geweigerd. U kunt Nsg's koppelen aan subnetten of afzonderlijke VM-instanties binnen dat subnet. Wanneer u een NSG koppelt aan een subnet, zijn de ACL-regels van toepassing op alle VM-exemplaren in dat subnet. Daarnaast kunt u het verkeer naar een afzonderlijke virtuele machine verder beperken door een NSG rechtstreeks aan die VM te koppelen. Raadpleeg [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../../virtual-network/security-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Een Windows-VM maken](../../virtual-machines/windows/quick-create-portal.md)
- [Een Linux-VM maken](../../virtual-machines/linux/quick-create-portal.md)
