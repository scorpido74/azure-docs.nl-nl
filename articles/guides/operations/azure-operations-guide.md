---
title: Handleiding voor Azure IT-operators | Microsoft Documenten
description: Handleiding voor Azure IT-operators aan de slag
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 4f9da6cbfe8d1e6b92c39148b275de193730c8f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623582"
---
# <a name="get-started-for-azure-it-operators"></a>Aan de slag voor Azure IT-operators

Deze handleiding introduceert kernconcepten met betrekking tot de implementatie en het beheer van een Microsoft Azure-infrastructuur. Als u nieuw bent in cloud computing of Azure zelf, helpt deze handleiding u snel aan de slag te gaan met concepten, implementatie en beheerdetails. Veel secties van deze handleiding bespreken een bewerking, zoals het implementeren van een virtuele machine, en bieden vervolgens een koppeling voor diepgaande technische details.

## <a name="cloud-computing-overview"></a>Overzicht van cloudcomputing

Cloud computing biedt een modern alternatief voor het traditionele on-premises datacenter. Public cloud leveranciers leveren en beheren alle computerinfrastructuur en de onderliggende beheersoftware. Deze leveranciers bieden een breed scala aan cloudservices. Een cloudservice in dit geval kan een virtuele machine, een webserver of databaseengine met een cloudworden. Als klant van een cloudprovider lease je deze clouddiensten op basis van de benodigde basis. Zo zet u de kapitaalkosten van hardwareonderhoud om in een operationele uitgave. Een cloudservice biedt ook de volgende voordelen:

- Snelle implementatie van grote compute omgevingen

- Snelle deallocatie van systemen die niet langer nodig zijn

- Eenvoudige implementatie van traditioneel complexe systemen zoals load balancers

- Mogelijkheid om flexibele rekencapaciteit of schaal te bieden wanneer dat nodig is

- Meer kosteneffectieve computeromgevingen

- Overal toegang met een webgebaseerde portal of programmatische automatisering

- Cloudgebaseerde services om te voldoen aan de meeste reken- en toepassingsbehoeften

Met on-premises infrastructuur hebt u volledige controle over de hardware en software die wordt geïmplementeerd. Historisch gezien heeft dit geleid tot hardware inkoopbeslissingen die gericht zijn op opschaling. Een voorbeeld is de aankoop van een server met meer cores om te voldoen aan de piekprestaties. Helaas kan deze infrastructuur buiten een vraagvenster onderbenut worden. Met Azure u alleen de infrastructuur implementeren die u nodig hebt en deze op elk gewenst moment naar boven of beneden aanpassen. Dit leidt tot een focus op uitschaling door de implementatie van extra compute nodes om aan een prestatiebehoefte te voldoen. Het uitschalen van cloudservices is kosteneffectiever dan het opschalen via dure hardware.

Microsoft heeft veel Azure-datacenters over de hele wereld geïmplementeerd, met meer gepland. Daarnaast neemt Microsoft soevereine clouds toe in regio's als China en Duitsland. Alleen de grootste bedrijven ter wereld kunnen datacenters op deze manier implementeren, dus het gebruik van Azure maakt het voor ondernemingen van elke omvang gemakkelijk om hun services dicht bij hun klanten te implementeren.

Voor kleine bedrijven biedt Azure een goedkoop toegangspunt, met de mogelijkheid om snel te schalen naarmate de vraag naar rekenkracht toeneemt. Dit voorkomt een grote kapitaalinvestering vooraf in infrastructuur en biedt de flexibiliteit om systemen te ontwerpen en te herontwerpen als dat nodig is. Het gebruik van cloud computing past goed bij het schaalsnelle en fail-fast model van opstartgroei.

Zie Azure-regio's voor meer informatie over de beschikbare [Azure-regio's.](https://azure.microsoft.com/regions/)

### <a name="cloud-computing-model"></a>Cloud computing-model

Azure maakt gebruik van een cloud computing-model op basis van servicecategorieën die aan klanten worden geleverd. De drie categorieën van de dienst omvatten Infrastructure as a Service (IaaS), Platform as a Service (PaaS), en Software as a Service (SaaS). Leveranciers delen een deel of alle verantwoordelijkheid voor componenten in de computerstack in elk van deze categorieën. Laten we eens kijken naar elk van de categorieën voor cloud computing.
![Vergelijking van cloudcomputingstack](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastructure as a service

Een IaaS-cloudleverancier voert en beheert alle fysieke compute resources en de benodigde software om computervirtualisatie mogelijk te maken. Een klant van deze service implementeert virtuele machines in deze gehoste datacenters. Hoewel de virtuele machines zich in een offsite datacenter bevinden, heeft de IaaS-consument controle over de configuratie en het beheer van het besturingssysteem, waardoor de onderliggende infrastructuur aan de cloudleverancier overblijft.

Azure bevat verschillende IaaS-oplossingen, waaronder virtuele machines, virtuele machineschaalsets en de bijbehorende netwerkinfrastructuur. Virtuele machines zijn een populaire keuze voor het in eerste instantie migreren van services naar Azure, omdat het een "lift and shift" migratiemodel mogelijk maakt. U een VM configureren zoals de infrastructuur die momenteel uw services in uw datacenter uitvoert en vervolgens uw software migreren naar de nieuwe vm. Mogelijk moet u configuratie-updates uitvoeren, zoals URL's naar andere services of opslag, maar u veel toepassingen op deze manier migreren.

Virtuele machineschaalsets zijn gebouwd bovenop Azure Virtual Machines en bieden een eenvoudige manier om clusters van identieke VM's te implementeren. Virtuele machineschaalsets ondersteunen ook automatisch schalen, zodat nieuwe VM's automatisch kunnen worden geïmplementeerd wanneer dat nodig is. Dit maakt virtuele machineschaalsets een ideaal platform voor het hosten van microservicecomputeclusters op een hoger niveau, zoals Azure Service Fabric en Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platform as a service

Met PaaS implementeert u uw toepassing in een omgeving die de leverancier van cloudservices biedt. De leverancier doet al het infrastructuurbeheer, zodat u zich concentreren op applicatieontwikkeling en gegevensbeheer.

Azure biedt verschillende PaaS-compute-aanbiedingen, waaronder de Web Apps-functie van Azure App Service en Azure Cloud Services (web- en werknemersrollen). In beide gevallen hebben ontwikkelaars meerdere manieren om hun toepassing te implementeren zonder iets te weten over de moeren en bouten die het ondersteunen. Ontwikkelaars hoeven geen virtuele machines (VM's) te maken, Extern bureaublad-protocol (RDP) te gebruiken om zich bij elk van deze machines aan te melden of de toepassing te installeren. Ze gewoon op een knop (of dicht bij het), en de tools die door Microsoft de bepaling van de VM's en vervolgens implementeren en installeren van de toepassing op hen.

#### <a name="saas-software-as-a-service"></a>SaaS: Software as a service

SaaS is software die centraal wordt gehost en beheerd. Het is meestal gebaseerd op een multitenant-architectuur: één versie van de toepassing wordt voor alle klanten gebruikt. Het kan worden geschaald naar meerdere instanties om de beste prestaties op alle locaties te garanderen. SaaS-software is meestal gelicentieerd via een maandelijks of jaarlijks abonnement. SaaS software leveranciers zijn verantwoordelijk voor alle onderdelen van de software stack, zodat alles wat je beheert is de geleverde diensten.

Microsoft Office 365 is een goed voorbeeld van een SaaS-aanbod. Abonnees betalen een maandelijks of jaarlijks abonnementsgeld en ze krijgen Microsoft Exchange, Microsoft OneDrive en de rest van de Microsoft Office-suite als service. Abonnees krijgen altijd de meest recente versie en de Exchange-server wordt voor u beheerd. In vergelijking met het installeren en upgraden van Office elk jaar, dit is minder duur en vereist minder inspanning.

## <a name="azure-services"></a>Azure-services

Azure biedt veel services in zijn cloud computing-platform. Deze diensten omvatten het volgende.

### <a name="compute-services"></a>Compute services

Services voor het hosten en uitvoeren van toepassingswerkbelasting:

- Azure Virtual Machines— zowel Linux als Windows

- App-services (Web Apps, Mobiele apps, Logische Apps, API-apps en functie-apps)

- Azure Batch (voor parallelle en batchcompute-taken op grote schaal)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Gegevensdiensten

Services voor het opslaan en beheren van gegevens:

- Azure Storage (omvat de Azure Blob-, Queue-, Tabel- en Bestandsservices)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache voor Redis

### <a name="application-services"></a>Toepassingsdiensten

Diensten voor het bouwen en bedienen van toepassingen:

- Azure Active Directory (Azure AD)

- Azure Service Bus voor het verbinden van gedistribueerde systemen

- Azure HDInsight voor de verwerking van big data

- Azure Logic Apps voor integratie- en orchestration-werkstromen

- Azure Media Services

### <a name="network-services"></a>Netwerkdiensten

Services voor netwerken, zowel binnen Azure als tussen Azure en on-premises datacenters:

- Azure Virtual Network

- Azure ExpressRoute

- Door Azure geleverde DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Zie [Azure-servicedocumentatie](https://docs.microsoft.com/azure)voor gedetailleerde documentatie over Azure-services.

## <a name="azure-key-concepts"></a>Azure-sleutelconcepten

### <a name="datacenters-and-regions"></a>Datacenters en regio's

Azure is een wereldwijd cloudplatform dat algemeen beschikbaar is in veel regio's over de hele wereld. Wanneer u een service, toepassing of VM inMaakt in Azure, wordt u gevraagd een regio te selecteren. De geselecteerde regio vertegenwoordigt een specifiek datacenter waar uw toepassing wordt uitgevoerd. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor meer informatie .

Een van de voordelen van het gebruik van Azure is dat u uw toepassingen implementeren in verschillende datacenters over de hele wereld. De regio die u kiest, kan van invloed zijn op de prestaties van uw toepassing. Het is optimaal om een regio te kiezen die dichter bij de meeste uw klanten staat, om de latentie in netwerkaanvragen te verminderen. U ook een regio selecteren om te voldoen aan de wettelijke vereisten voor het distribueren van uw app in bepaalde landen/regio's.

### <a name="azure-portal"></a>Azure Portal

De Azure-portal is een webgebaseerde toepassing die kan worden gebruikt voor het maken, beheren en verwijderen van Azure-bronnen en -services. De Azure-portal bevindt zich op [portal.azure.com.](https://portal.azure.com) Het bevat een aanpasbaar dashboard en tooling voor het beheer van Azure-resources. Het biedt ook facturerings- en abonnementsgegevens. Zie microsoft [Azure-portaloverzicht](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) en [Azure-bronnen beheren via portal](https://docs.microsoft.com/azure/azure-portal/resource-group-portal)voor meer informatie.

### <a name="resources"></a>Resources

Azure-bronnen zijn afzonderlijke compute-, netwerk-, gegevens- of app-hostingservices die zijn geïmplementeerd in een Azure-abonnement. Enkele veelvoorkomende bronnen zijn virtuele machines, opslagaccounts of SQL-databases. Azure-services bestaan vaak uit verschillende gerelateerde Azure-bronnen. Een virtuele Azure-machine kan bijvoorbeeld een VM, opslagaccount, netwerkadapter en openbaar IP-adres bevatten. Deze bronnen kunnen afzonderlijk of als groep worden gemaakt, beheerd en verwijderd. Azure-bronnen worden later in deze handleiding nader behandeld.

### <a name="resource-groups"></a>Resourcegroepen

Een Azure-brongroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of alleen resources die u als groep wilt beheren. Azure-brongroepen worden later in deze handleiding nader behandeld.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Een Azure Resource Manager-sjabloon is een JSON-bestand (JavaScript Object Notation) dat een of meer resources definieert die moeten worden geïmplementeerd in een resourcegroep. Het definieert ook de afhankelijkheden tussen geïmplementeerde resources. Resource Manager-sjablonen worden later in deze handleiding nader behandeld.

### <a name="automation"></a>Automation

Naast het maken, beheren en verwijderen van resources met behulp van de Azure-portal, u deze activiteiten automatiseren met PowerShell of de Azure command-line interface (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell is een reeks modules die cmdlets bieden voor het beheer van Azure. U de cmdlets gebruiken om Azure-services te maken, beheren en verwijderen. De cmdlets kunnen u helpen consistente, herhaalbare en hands-off implementaties te realiseren. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps) voor meer informatie.

#### <a name="azure-command-line-interface"></a>Azure-opdrachtregelinterface

De Azure-opdrachtregelinterface is een hulpmiddel dat u gebruiken om Azure-resources te maken, te beheren en te verwijderen van de opdrachtregel. De Azure CLI is beschikbaar voor Linux, Mac OS X en Windows. Zie [Azure CLI installeren](/cli/azure/install-azure-cli)voor meer informatie en technische details.

#### <a name="rest-apis"></a>REST-API’s

Azure is gebouwd op een set REST-API's die de gebruikersinterface van de Azure-portal ondersteunen. De meeste van deze REST-API's worden ook ondersteund om u programmatisch uw Azure-bronnen en -apps te laten inrichten en beheren vanaf elk apparaat dat in internet is ingeschakeld. Zie de Azure [REST SDK-verwijzing](https://docs.microsoft.com/rest/api/index)voor meer informatie .

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Beheerders hebben toegang tot Azure PowerShell en Azure CLI via een browsertoegankelijke ervaring genaamd Azure Cloud Shell. Deze interactieve interface biedt een flexibel hulpmiddel voor Linux- en Windows-beheerders om hun command-line interface naar keuze te gebruiken, bash of PowerShell. Azure Cloud Shell is toegankelijk via de portal (als zelfstandige webinterface aan [shell.azure.com)](https://shell.azure.com)of via een aantal andere toegangspunten. Zie [Overzicht van Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)voor meer informatie.

## <a name="azure-subscriptions"></a>Azure-abonnementen

Een abonnement is een logische groepering van Azure-services die is gekoppeld aan een Azure-account. Eén Azure-account kan meerdere abonnementen bevatten. Facturering voor Azure-services gebeurt per abonnement. Azure-abonnementen hebben een accountbeheerder, die volledige controle heeft over het abonnement, en een servicebeheerder, die controle heeft over alle services in het abonnement. Zie [Azure-abonnementsbeheerders toevoegen of wijzigen](../../cost-management-billing/manage/add-change-subscription-administrator.md)voor informatie over beheerders van klassieke abonnementen. Naast beheerders kunnen afzonderlijke accounts gedetailleerde controle krijgen over Azure-resources met behulp van [rbac (Role-based access control).](../../role-based-access-control/overview.md)

### <a name="select-and-enable-an-azure-subscription"></a>Een Azure-abonnement selecteren en inschakelen

Voordat u met Azure-services werken, hebt u een abonnement nodig. Er zijn verschillende abonnementstypen beschikbaar.

**Gratis accounts:** De link om je aan te melden voor een gratis account is op de [Azure-website.](https://azure.microsoft.com/) Dit geeft u een krediet in de loop van 30 dagen om een combinatie van resources in Azure uit te proberen. Als u uw tegoed overschrijdt, wordt uw account opgeschort. Aan het einde van de proef worden uw diensten buiten gebruik gesteld en zullen ze niet meer werken. U op elk gewenst moment upgraden naar een abonnement op basis van betalen per gebruik.

**MSDN-abonnementen**: Als u een MSDN-abonnement hebt, krijgt u elke maand een specifiek bedrag in Azure-tegoed. Als u bijvoorbeeld een Microsoft Visual Studio Enterprise met MSDN-abonnement hebt, krijgt \$u 150 per maand in Azure-tegoed.

Als u het kredietbedrag overschrijdt, wordt uw service uitgeschakeld totdat de volgende maand begint. U de bestedingslimiet uitschakelen en een creditcard toevoegen die voor de extra kosten moet worden gebruikt. Sommige van deze kosten worden verdisconteerd voor MSDN-accounts. U betaalt bijvoorbeeld de Linux-prijs voor VM's met Windows Server en er zijn geen extra kosten verbonden aan Microsoft-servers zoals Microsoft SQL Server. Dit maakt MSDN-accounts ideaal voor ontwikkeling en testscenario's.

**BizSpark-accounts:** Het Microsoft BizSpark-programma biedt veel voordelen voor startups. Een van die voordelen is toegang tot alle Microsoft-software voor ontwikkelings- en testomgevingen voor maximaal vijf MSDN-accounts. U krijgt $ 150 aan Azure-tegoed voor elk van deze vijf MSDN-accounts en u betaalt verlaagde tarieven voor verschillende Azure-services, zoals Virtuele machines.

**Betalen per gebruik**: Met dit abonnement betaalt u voor wat u gebruikt door een creditcard of betaalkaart aan de rekening te koppelen. Als u een organisatie bent, u ook worden goedgekeurd voor facturatie.

**Ondernemingsovereenkomsten:** Met een ondernemingsovereenkomst verbindt u zich ertoe om het komende jaar een bepaald aantal services in Azure te gebruiken en betaalt u dat bedrag van tevoren. De inzet die u maakt wordt het hele jaar door verbruikt. Als u het vastleggingsbedrag overschrijdt, u de achterstallige overschrijding betalen. Afhankelijk van het bedrag van de toezegging krijgt u korting op de services in Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Beheerderstoegang verlenen tot een Azure-abonnement

RBAC heeft verschillende ingebouwde rollen die u gebruiken om machtigingen toe te wijzen. Als u een gebruiker een beheerder van een Azure-abonnement wilt maken, wijst u deze de rol [Eigenaar](../../role-based-access-control/built-in-roles.md#owner) toe op het abonnementsbereik. De rol van eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, waaronder het recht om toegang aan anderen te delegeren.

Zie [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.

### <a name="view-billing-information-in-the-azure-portal"></a>Factureringsgegevens weergeven in de Azure-portal

Een belangrijk onderdeel van het gebruik van Azure is de mogelijkheid om factureringsgegevens te bekijken. De Azure-portal biedt gedetailleerd inzicht in de factureringsgegevens van Azure.

Zie [Uw Azure-factuur en daggebruiksgegevens downloaden](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)voor meer informatie.

### <a name="get-billing-information-from-billing-apis"></a>Factureringsgegevens ophalen van facturerings-API's

Naast het bekijken van de facturering in de portal, hebt u toegang tot de factureringsgegevens met behulp van een script of programma via de Azure Billing REST API's:

- U de Azure Usage API gebruiken om uw gebruiksgegevens op te halen. U de factureringsgebruiksgegevens verfijnen door gerelateerde Azure-bronnen te taggen. U bijvoorbeeld elk van de resources in een resourcegroep taggen met een afdelingsnaam of projectnaam en vervolgens de kosten specifiek voor die ene tag bijhouden.

- U de Azure Rate Card API gebruiken om alle beschikbare bronnen weer te geven, samen met de metagegevens en prijsinformatie over elk van deze bronnen.

Zie [Inzicht verkrijgen in het gebruik van Microsoft Azure-resources](../../cost-management-billing/manage/usage-rate-card-overview.md)voor meer informatie.

### <a name="forecast-cost-with-the-pricing-calculator"></a>Kosten voorspellen met de prijscalculator

De prijzen voor elke service in Azure zijn anders. Veel Azure-services bieden basis-, standaard- en premiumlagen. Meestal heeft elke laag verschillende prijs- en prestatieniveaus. Met behulp van de [online prijscalculator](https://azure.microsoft.com/pricing/calculator)u prijsschattingen maken. De rekenmachine bevat flexibiliteit om de kosten op één resource of een groep resources te schatten.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager is een implementatie-, beheer- en organisatiemechanisme voor Azure-resources. Door Resourcemanager te gebruiken, u veel afzonderlijke resources samenvoegen in een resourcegroep.

ResourceManager bevat ook implementatiemogelijkheden die het mogelijk maken om aanpasbare implementatie en configuratie van gerelateerde resources mogelijk te maken. Door bijvoorbeeld Resource Manager te gebruiken, u een toepassing implementeren die bestaat uit meerdere virtuele machines, een load balancer en een SQL-database als één eenheid. U ontwikkelt deze implementaties met behulp van een Resource Manager-sjabloon.

Resource Manager biedt diverse voordelen:

- U kunt alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

- U uw oplossing herhaaldelijk implementeren gedurende de hele ontwikkelingslevenscyclus en er vertrouwen in hebben dat uw resources consistent zijn geïmplementeerd.

- U kunt uw infrastructuur beheren via declaratieve sjablonen in plaats van scripts.

- U kunt de afhankelijkheden tussen resources zo definiëren dat deze in de juiste volgorde worden geïmplementeerd.

- U toegangsbeheer toepassen op alle services in uw resourcegroep omdat RBAC native is geïntegreerd in het beheerplatform.

- U tags op resources toepassen om alle bronnen in uw abonnement logisch te ordenen.

- U de facturering van uw organisatie verduidelijken door de kosten weer te geven voor een groep resources die dezelfde tag delen.

### <a name="tips-for-creating-resource-groups"></a>Tips voor het maken van resourcegroepen

Wanneer u beslissingen neemt over uw resourcegroepen, u rekening houden met de volgende tips:

- Alle resources in een resourcegroep moeten dezelfde levenscyclus hebben.

- U een resource slechts aan één groep tegelijk toewijzen.

- U op elk gewenst moment een resource uit een resourcegroep toevoegen of verwijderen. Elke resource moet deel uitmaken van een resourcegroep. Dus als u een resource uit de ene groep verwijdert, moet u deze aan een andere groep toevoegen.

- U de meeste typen resources op elk gewenst moment naar een andere resourcegroep verplaatsen.

- De resources in een resourcegroep kunnen zich in verschillende regio's begeven.

- U een brongroep gebruiken om de toegang te beheren voor de bronnen in deze groep.

### <a name="building-resource-manager-templates"></a>Sjablonen voor resourcebeheer bouwen

Resourcebeheersjablonen definiëren declaratief de resources en resourceconfiguraties die in één resourcegroep worden geïmplementeerd. U ResourceManager-sjablonen gebruiken om complexe implementaties te orkestreren zonder dat er overtollige scripting of handmatige configuratie nodig is. Nadat u een sjabloon hebt ontwikkeld, u deze meerdere keren implementeren, telkens met een identiek resultaat.

Een resourcemanagersjabloon bestaat uit vier secties:

- **Parameters**: Dit zijn ingangen voor de implementatie. Parameterwaarden kunnen worden geleverd door een menselijk of een geautomatiseerd proces. Een voorbeeldparameter kan een beheerdersgebruikersnaam en -wachtwoord voor een Windows-vm zijn. De parameterwaarden worden tijdens de implementatie gebruikt wanneer ze zijn opgegeven.

- **Variabelen:** deze worden gebruikt om waarden vast te houden die tijdens de implementatie worden gebruikt. In tegenstelling tot parameters wordt een variabele waarde niet opgegeven tijdens de implementatietijd. In plaats daarvan is het hard gecodeerd of dynamisch gegenereerd.

- **Resources:** in dit gedeelte van de sjabloon worden de resources gedefinieerd die moeten worden geïmplementeerd, zoals virtuele machines, opslagaccounts en virtuele netwerken.

- **Uitvoer:** Nadat een implementatie is voltooid, kan Resourcemanager gegevens retourneren, zoals dynamisch gegenereerde verbindingstekenreeksen.

De volgende mechanismen zijn beschikbaar voor implementatieautomatisering:

- **Functies**: U verschillende functies gebruiken in Resource Manager-sjablonen. Deze omvatten bewerkingen zoals het converteren van een tekenreeks naar kleine letters, het implementeren van meerdere exemplaren van een gedefinieerde resource en het dynamisch retourneren van de doelgroep. Functies voor Resource Manager helpen bij het maken van dynamische implementaties.

- **Resourceafhankelijkheden:** wanneer u meerdere resources implementeert, zijn sommige resources afhankelijk van andere resources. Om de implementatie te vergemakkelijken, u een afhankelijkheidsverklaring gebruiken, zodat afhankelijke resources vóór de andere resources worden geïmplementeerd.

- **Sjabloonkoppeling:** vanuit de ene resourcemanagersjabloon u een koppeling maken naar een andere sjabloon. Dit maakt implementatie-ontleding mogelijk in een reeks gerichte, doelspecifieke sjablonen.

U Resource Manager-sjablonen bouwen in elke teksteditor. De Azure SDK voor Visual Studio bevat echter hulpprogramma's om u te helpen. Met Visual Studio u resources toevoegen aan de sjabloon via een wizard en de sjabloon vervolgens rechtstreeks vanuit Visual Studio implementeren en debuggen. Zie [Sjablonen voor Azure Resource Manager ontwerpen](../../resource-group-authoring-templates.md)voor meer informatie .

Ten slotte u bestaande brongroepen converteren naar een herbruikbare sjabloon vanuit de Azure-portal. Dit kan handig zijn als u een inzetbare sjabloon van een bestaande resourcegroep wilt maken of als u gewoon de onderliggende JSON wilt onderzoeken. Als u een resourcegroep wilt exporteren, selecteert u de knop **Automatiseringsscript** in de instellingen van de resourcegroep.

## <a name="security-of-azure-resources-rbac"></a>Beveiliging van Azure-resources (RBAC)

U operationele toegang verlenen tot gebruikersaccounts op een bepaald bereik: abonnement, resourcegroep of afzonderlijke resource. Dit betekent dat u een set resources implementeren in een resourcegroep, zoals een virtuele machine en alle gerelateerde resources, en machtigingen verlenen aan een specifieke gebruiker of groep. Deze aanpak beperkt de toegang tot alleen de resources die tot de doelgroep behoren. U ook toegang verlenen tot één resource, zoals een virtuele machine of een virtueel netwerk.

Als u toegang wilt verlenen, wijst u een rol toe aan de gebruiker of gebruikersgroep. Er zijn veel vooraf gedefinieerde rollen. U ook uw eigen aangepaste rollen definiëren.

Hier volgen een paar voorbeelden [van ingebouwde rollen in Azure:](../../role-based-access-control/built-in-roles.md)

- **Eigenaar**: Een gebruiker met deze rol kan alles beheren, inclusief toegang.

- **Lezer**: Een gebruiker met deze rol kan bronnen van alle soorten lezen (behalve geheimen), maar kan geen wijzigingen aanbrengen.

- **Virtuele machinebijdrager:** een gebruiker met deze rol kan virtuele machines beheren, maar kan het virtuele netwerk waarmee ze zijn verbonden of het opslagaccount waar het VHD-bestand zich bevindt, niet beheren.

- **SQL DB Contributor**: Een gebruiker met deze rol kan SQL-databases beheren, maar niet hun beveiligingsbeleid.

- **SQL Security Manager**: Een gebruiker met deze rol kan het beveiligingsbeleid van SQL-servers en -databases beheren.

- **Opslagaccountbijdrager:** een gebruiker met deze rol kan opslagaccounts beheren, maar kan de toegang tot de opslagaccounts niet beheren.

Zie [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines is een van de centrale IaaS-services in Azure. Azure Virtual Machines ondersteunt de implementatie van virtuele Windows- of Linux-machines in een Microsoft Azure-datacenter. Met Azure Virtual Machines hebt u totale controle over de VM-configuratie en bent u verantwoordelijk voor alle software-installatie, -configuratie en -onderhoud.

Wanneer u een Azure VM implementeert, u een afbeelding selecteren in de Azure Marketplace of u u een eigen algemene afbeelding leveren. Deze afbeelding wordt gebruikt om het besturingssysteem en de eerste configuratie toe te passen. Tijdens de implementatie verwerkt Resource Manager enkele configuratie-instellingen, zoals het toewijzen van de computernaam, beheerdersreferenties en netwerkconfiguratie. U Azure-extensies voor virtuele machines gebruiken om configuraties zoals software-installatie, antivirusconfiguratie en bewakingsoplossingen verder te automatiseren.

U virtuele machines maken in veel verschillende maten. De grootte van de virtuele machine bepaalt de toewijzing van resources, zoals verwerking, geheugen en opslagcapaciteit. In sommige gevallen zijn specifieke functies, zoals rdma-enabled netwerkadapters en SSD-schijven, alleen beschikbaar met bepaalde VM-formaten. Zie 'Grootte voor virtuele machines in Azure' [voor](../../virtual-machines/windows/sizes.md) Een volledige lijst met VM-formaten [en](../../virtual-machines/linux/sizes.md)-mogelijkheden.

### <a name="use-cases"></a>Gebruiksvoorbeelden

Omdat virtuele Azure-machines volledige controle bieden over de configuratie, zijn ze ideaal voor een breed scala aan serverworkloads die niet in een PaaS-model passen. Serverworkloads zoals databaseservers (SQL Server, Oracle of MongoDB), Windows Server Active Directory, Microsoft SharePoint en nog veel meer worden mogelijk om op het Microsoft Azure-platform uit te voeren. Indien gewenst u dergelijke workloads verplaatsen van een on-premises datacenter naar een of meer Azure-regio's, zonder een grote hoeveelheid herconfiguratie.

### <a name="deployment-of-virtual-machines"></a>Implementatie van virtuele machines

U virtuele Azure-machines implementeren met behulp van de Azure-portal, door automatisering te gebruiken met de Azure PowerShell-module of door automatisering te gebruiken met het cross-platform CLI.

#### <a name="portal"></a>Portal

Het implementeren van een virtuele machine met behulp van de Azure-portal vereist alleen een actief Azure-abonnement en toegang tot een webbrowser. U veel verschillende besturingssysteemafbeeldingen selecteren met verschillende configuraties. Alle opslag- en netwerkvereisten worden geconfigureerd tijdens de implementatie. Zie 'Een virtuele machine maken in de Azure-portal' voor [Windows](../../virtual-machines/windows/quick-create-portal.md) en [Linux](../../virtual-machines/linux/quick-create-portal.md)voor meer informatie.

Naast het implementeren van een virtuele machine vanuit de Azure-portal, u een Azure Resource Manager-sjabloon implementeren vanuit de portal. Hiermee worden alle resources geïmplementeerd en geconfigureerd zoals gedefinieerd in de sjabloon. Zie [Resources implementeren met Resource Manager-sjablonen en Azure-portal voor](../../azure-resource-manager/templates/deploy-portal.md)meer informatie.

#### <a name="powershell"></a>PowerShell

Het implementeren van een Azure virtuele machine met PowerShell maakt volledige implementatieautomatisering van alle gerelateerde virtuele machinebronnen mogelijk, inclusief opslag en netwerken. Zie [Een Windows-vm maken met Resource Manager en PowerShell](../../virtual-machines/windows/quick-create-powershell.md)voor meer informatie.

Naast het afzonderlijk implementeren van Azure-compute resources, u de Azure PowerShell-module gebruiken om een Azure Resource Manager-sjabloon te implementeren. Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

#### <a name="command-line-interface-cli"></a>Opdrachtregelinterface (Command-line Interface, CLI)

Net als bij de PowerShell-module biedt de Azure-command-line-interface implementatieautomatisering en kan deze worden gebruikt op Windows-, OS X- of Linux-systemen. Wanneer u de opdracht Snel maken van de Azure **CLI-vm** gebruikt, worden alle gerelateerde virtuele machineresources (inclusief opslag en netwerken) en de virtuele machine zelf geïmplementeerd. Zie [Een Linux-vm maken in Azure met de CLI](../../virtual-machines/linux/quick-create-cli.md)voor meer informatie.

U de Azure CLI ook gebruiken om een Azure Resource Manager-sjabloon te implementeren. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI voor](../../azure-resource-manager/templates/deploy-cli.md)meer informatie.

### <a name="access-and-security-for-virtual-machines"></a>Toegang en beveiliging voor virtuele machines

Toegang tot een virtuele machine vanaf het internet vereist dat de bijbehorende netwerkinterface, of load balancer indien van toepassing, wordt geconfigureerd met een openbaar IP-adres. Het openbare IP-adres bevat een DNS-naam die wordt opgelost voor de virtuele machine of load balancer. Zie [IP-adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)voor meer informatie.

U beheert de toegang tot de virtuele machine via het openbare IP-adres met behulp van een NSG-bron (Network Security Group). Een NSG werkt als een firewall en staat of weigert verkeer over de netwerkinterface of subnet op een set gedefinieerde poorten. Als u bijvoorbeeld een Extern bureaublad-sessie wilt maken met een Azure VM, moet u de NSG configureren om binnenkomend verkeer op poort 3389 toe te staan. Zie [Poorten openen voor een vm in Azure met behulp van de Azure-portal](../../virtual-machines/windows/nsg-quickstart-portal.md)voor meer informatie.

Ten slotte moet u, net als bij het beheer van elk computersysteem, beveiliging bieden voor een virtuele Azure-machine in het besturingssysteem met behulp van beveiligingsreferenties en softwarefirewalls.

## <a name="azure-storage"></a>Azure Storage

Azure Storage is een door Microsoft beheerde service die duurzame, schaalbare en redundante opslag biedt. U een Azure-opslagaccount als bron toevoegen aan een resourcegroep met behulp van een resourceimplementatiemethode. Azure bevat vier opslagtypen: Blob-opslag, bestandsopslag, tabelopslag en wachtrijopslag. Bij het implementeren van een opslagaccount zijn twee accounttypen beschikbaar, opslag voor algemene doeleinden en blobs. Een opslagaccount voor algemene doeleinden geeft u toegang tot alle vier de opslagtypen. Blob-opslagaccounts zijn vergelijkbaar met accounts voor algemene doeleinden, maar bevatten gespecialiseerde blobs die warme en koude toegangslagen bevatten. Zie [Azure Blob-opslag](../../storage/blobs/storage-blob-storage-tiers.md)voor meer informatie over blob-opslag.

Azure-opslagaccounts kunnen worden geconfigureerd met verschillende redundantieniveaus:

- **Lokaal redundante opslag** biedt een hoge beschikbaarheid door ervoor te zorgen dat drie kopieën van alle gegevens synchroon worden gemaakt voordat een schrijven als succesvol wordt beschouwd. Deze kopieën worden opgeslagen in één faciliteit in één regio. De replica's bevinden zich in afzonderlijke foutdomeinen en upgradedomeinen. Dit betekent dat de gegevens beschikbaar zijn, zelfs als een opslagknooppunt dat uw gegevens vasthoudt, mislukt of offline wordt gehaald om te worden bijgewerkt.

- **Georedundante opslag** maakt drie synchrone kopieën van de gegevens in het primaire gebied voor hoge beschikbaarheid en maakt vervolgens asynchroon drie replica's in een gekoppeld gebied voor herstel na noodgevallen.

- **Georedundante opslag voor leestoegang** is georedundante opslag plus de mogelijkheid om de gegevens in de secundaire regio te lezen. Deze mogelijkheid maakt het geschikt voor gedeeltelijke disaster recovery. Als er een probleem is met de primaire regio, u uw toepassing wijzigen om alleen-lezen toegang te hebben tot het gekoppelde gebied.

### <a name="use-cases"></a>Gebruiksvoorbeelden

Elk opslagtype heeft een andere use case.

#### <a name="blob-storage"></a>Blob Storage

Het woord *blob* is een acroniem voor *binair groot object*. Blobs zijn ongestructureerde bestanden zoals die u op uw computer opslaat. In Blob Storage kan elk type tekst of binaire gegevens, zoals een document, mediabestand of toepassingsinstallatieprogramma, worden opgeslagen. U kunt Blob Storage zien als een vorm van objectopslag. Azure Blob-opslag bevat ook Azure Virtual Machines-gegevensschijven.

Azure Storage ondersteunt drie soorten blobs:

- **Blokblobs** worden gebruikt om gewone bestanden tot 195 GB groot te houden (4 MB × 50.000 blokken). Blok-blobs worden primair gebruikt om bestanden op te slaan die van begin tot eind worden gelezen, zoals mediabestanden of afbeeldingsbestanden voor websites. Ze heten blokblobs omdat bestanden groter dan 64 MB als kleine blokken moeten worden geüpload. Deze blokken worden vervolgens geconsolideerd (of vastgelegd) in de uiteindelijke blob.

- **Paginablobs** worden gebruikt om bestanden met willekeurige toegang tot 1 TB groot te houden. Paginablobs worden voornamelijk gebruikt als achtergrondopslag voor de VHD's die duurzame schijven bieden voor Azure Virtual Machines, de IaaS-compute-service in Azure. Ze heten pagina-blobs omdat ze willekeurige lees-/schrijftoegang bieden tot pagina’s van 512 bytes.

- **Blobs toevoegen** bestaan uit blokken zoals blokblobs, maar ze zijn geoptimaliseerd voor toebehorenbewerkingen. Deze worden vaak gebruikt voor het registreren van informatie uit een of meer bronnen naar dezelfde blob. U bijvoorbeeld al uw tracelogging naar dezelfde toevoegende blob schrijven voor een toepassing die op meerdere VM's wordt uitgevoerd. Eén toevoeg-blob kan maximaal 195 GB zijn.

Zie [Aan de slag met Azure Blob-opslag met .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>File Storage

Azure File storage is een service die bestandsshares in de cloud aanbiedt met behulp van het standaard SMB-protocol (Server Message Block). De service ondersteunt zowel SMB 2.1 als SMB 3.0. Met Azure File-opslag u toepassingen die afhankelijk zijn van bestandsshares snel en zonder dure herschrijft migreren naar Azure. Toepassingen die worden uitgevoerd op virtuele Azure-machines, in cloudservices of van on-premises clients, kunnen een bestandsshare in de cloud monteren. Dit is vergelijkbaar met hoe een desktoptoepassing een typisch SMB-aandeel monteert. Daarna kan een willekeurig aantal toepassingsonderdelen de File Storage-share tegelijkertijd koppelen en gebruiken.

Omdat een bestandsopslagaandeel een standaard SMB-bestandsshare is, hebben toepassingen die in Azure worden uitgevoerd, toegang tot gegevens in het aandeel via i/o-API's van bestandssysteem. Ontwikkelaars kunnen daarom hun bestaande code en vaardigheden gebruiken om bestaande toepassingen te migreren. IT-professionals kunnen PowerShell-cmdlets gebruiken om bestandsopslagshares te maken, te monteren en te beheren als onderdeel van het beheer van Azure-toepassingen.

Zie [Aan de slag met Azure File-opslag in Windows](../../storage/files/storage-how-to-use-files-windows.md) of Azure File storage gebruiken met [Linux](../../storage/files/storage-how-to-use-files-linux.md)voor meer informatie.

#### <a name="table-storage"></a>Table Storage

Azure Table Storage is een service waarmee gestructureerde NoSQL-gegevens worden opgeslagen in de cloud. Tabelopslag is een sleutel-/attribuutarchief met een schemaloos ontwerp. Omdat tabelopslag zonder schema is, is het eenvoudig om uw gegevens aan te passen naarmate de behoeften van uw toepassing evolueren. De toegang tot gegevens verloopt snel en kostenefficiënt voor alle soorten toepassingen. Table Storage is doorgaans aanzienlijk goedkoper dan traditionele SQL voor vergelijkbare gegevensvolumes.

U kunt Table Storage gebruiken voor het opslaan van flexibele gegevenssets, zoals gebruikersgegevens voor webtoepassingen, adresboeken, apparaatgegevens en alle overige typen metagegevens die uw service nodig heeft. U een willekeurig aantal entiteiten opslaan in een tabel. Een opslagaccount kan een willekeurig aantal tabellen bevatten, tot de capaciteitslimiet van het opslagaccount.

Zie [Aan de slag met Azure Table-opslag](../../cosmos-db/table-storage-how-to-use-dotnet.md)voor meer informatie.

#### <a name="queue-storage"></a>Queue Storage

Azure Queue Storage biedt uitwisseling van berichten tussen toepassingsonderdelen in de cloud. Bij het ontwerpen van toepassingen voor schaal worden toepassingscomponenten vaak ontkoppeld, zodat ze onafhankelijk van elkaar kunnen schalen. Queue Storage biedt asynchrone uitwisseling van berichten voor communicatie tussen toepassingsonderdelen, of deze nu worden uitgevoerd in de cloud, op een desktopcomputer, op een on-premises server of op een mobiel apparaat. Queue Storage biedt ook ondersteuning voor het beheren van asynchrone taken en het samenstellen van proceswerkstromen.

Zie [Aan de slag met Azure Queue-opslag](../../storage/queues/storage-dotnet-how-to-use-queues.md)voor meer informatie.

### <a name="deploying-a-storage-account"></a>Een opslagaccount implementeren

Er zijn verschillende opties voor het implementeren van een opslagaccount.

#### <a name="portal"></a>Portal

Het implementeren van een opslagaccount met behulp van de Azure-portal vereist alleen een actief Azure-abonnement en toegang tot een webbrowser. U een nieuw opslagaccount implementeren in een nieuwe of bestaande brongroep. Nadat u het opslagaccount hebt gemaakt, u een blobcontainer of bestandsshare maken met behulp van de portal. U programmatisch tabel- en wachtrijopslagentiteiten maken. Zie [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor meer informatie.

Naast het implementeren van een opslagaccount vanuit de Azure-portal, u een Azure Resource Manager-sjabloon implementeren vanuit de portal. Hiermee worden alle resources geïmplementeerd en geconfigureerd zoals gedefinieerd in de sjabloon, inclusief opslagaccounts. Zie [Resources implementeren met Resource Manager-sjablonen en Azure-portal voor](../../azure-resource-manager/templates/deploy-portal.md)meer informatie.

#### <a name="powershell"></a>PowerShell

Het implementeren van een Azure-opslagaccount met PowerShell maakt volledige implementatieautomatisering van het opslagaccount mogelijk. Zie [Azure PowerShell gebruiken met Azure Storage](../../storage/common/storage-powershell-guide-full.md)voor meer informatie.

Naast het afzonderlijk implementeren van Azure-resources, u de Azure PowerShell-module gebruiken om een Azure Resource Manager-sjabloon te implementeren. Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

#### <a name="command-line-interface-cli"></a>Opdrachtregelinterface (Command-line Interface, CLI)

Net als bij de PowerShell-module biedt de Azure-command-line Interface implementatieautomatisering en kan deze worden gebruikt op Windows-, OS X- of Linux-systemen. U de opdracht **Azure CLI-opslagaccount maken** gebruiken om een opslagaccount te maken. Zie Azure CLI gebruiken met Azure Storage voor meer [informatie.](../../storage/common/storage-azure-cli.md)

U de Azure CLI ook gebruiken om een Azure Resource Manager-sjabloon te implementeren. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI voor](../../resource-group-template-deploy-cli.md)meer informatie.

### <a name="access-and-security-for-azure-storage"></a>Toegang en beveiliging voor Azure Storage

Azure Storage is op verschillende manieren toegankelijk, waaronder de Azure-portal, tijdens het maken en uitvoeren van vm's en vanuit opslagclientbibliotheken.

#### <a name="virtual-machine-disks"></a>Virtuele machineschijven

Wanneer u een virtuele machine implementeert, moet u ook een opslagaccount maken om de schijf van het besturingssysteem van de virtuele machine en eventuele extra gegevensschijven vast te houden. U een bestaand opslagaccount selecteren of een nieuw opslagaccount maken. Omdat de maximale grootte van een blob 1.024 GB is, heeft één VM-schijf een maximale grootte van 1.023 GB. Als u een grotere gegevensschijf wilt configureren, u meerdere gegevensschijven aan de virtuele machine presenteren en deze samenvoegen als één logische schijf. Zie Azure-schijven voor [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) en [Linux](../../virtual-machines/linux/tutorial-manage-disks.md)beheren voor meer informatie.

#### <a name="storage-tools"></a>Opslaggereedschappen

Azure-opslagaccounts zijn toegankelijk via veel verschillende opslagverkenners, zoals Visual Studio Cloud Explorer. Met deze tools u door opslagaccounts en gegevens bladeren. Zie [Azure Storage Client Tools](../../storage/common/storage-explorers.md)voor meer informatie en een lijst met beschikbare opslagverkenners.

#### <a name="storage-api"></a>Opslag-API

Opslagbronnen kunnen worden geopend door elke taal die HTTP/HTTPS-verzoeken kan indienen. Daarnaast biedt Azure Storage programmeringsbibliotheken voor verschillende veelgebruikte talen. Deze bibliotheken vereenvoudigen het werken met Azure Storage door gegevens zoals synchrone en asynchrone aanroep, batching van bewerkingen, uitzonderingsbeheer en automatische nieuwe pogingen te verwerken. Zie VOOR meer informatie [de REST API-verwijzing van azure storageservice](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Opslagtoegangssleutels

Elk opslagaccount heeft twee verificatiesleutels, een primaire en een secundaire. Beide kunnen worden gebruikt voor opslagtoegangsbewerkingen. Deze opslagsleutels worden gebruikt om een opslagaccount te beveiligen en zijn vereist voor programmatisch toegang tot gegevens. Er zijn twee sleutels om af en toe rollover van de sleutels toe te staan om de veiligheid te verbeteren. Het is van cruciaal belang om de sleutels veilig te houden, omdat hun bezit, samen met de accountnaam, onbeperkte toegang tot alle gegevens in het opslagaccount mogelijk maakt.

#### <a name="shared-access-signatures"></a>Handtekeningen voor gedeelde toegang

Als u gebruikers controletoegang tot uw opslagbronnen wilt toestaan, u een handtekening voor gedeelde toegang maken. Een handtekening voor gedeelde toegang is een token dat kan worden toegevoegd aan een URL waarmee gedelegeerde toegang tot een opslagbron kan worden toegestaan. Iedereen die het token bezit, heeft toegang tot de bron waarnaar het verwijst met de machtigingen die het opgeeft, voor de periode dat het geldig is. Zie [Handtekeningen voor gedeelde toegang gebruiken](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)voor meer informatie .

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuele netwerken zijn nodig om communicatie tussen virtuele machines te ondersteunen. U subnetten, aangepast IP-adres, DNS-instellingen, beveiligingsfiltering en taakverdeling definiëren. Azure ondersteunt verschillende gebruiksaanvragen: cloudnetwerken of hybride virtuele netwerken.

### <a name="cloud-only-virtual-networks"></a>Virtuele netwerken in de cloud

Een virtueel Azure-netwerk is standaard alleen toegankelijk voor bronnen die zijn opgeslagen in Azure. Bronnen die zijn verbonden met hetzelfde virtuele netwerk kunnen met elkaar communiceren. U virtuele machinenetwerkinterfaces en load balancers koppelen aan een openbaar IP-adres om de virtuele machine toegankelijk te maken via internet. U de toegang tot de openbaar blootgestelde bronnen beveiligen met behulp van een netwerkbeveiligingsgroep.

![Azure Virtual Network voor een 2-laagswebtoepassing](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybride virtuele netwerken

U een on-premises netwerk verbinden met een virtueel Azure-netwerk via ExpressRoute of een site-to-site VPN-verbinding. In deze configuratie is het virtuele Azure-netwerk in wezen een cloudgebaseerde uitbreiding van uw on-premises netwerk.

Omdat het virtuele Azure-netwerk is verbonden met uw on-premises netwerk, moeten virtuele netwerken met meerdere locaties een uniek deel van de adresruimte gebruiken die uw organisatie gebruikt. Op dezelfde manier waarop verschillende bedrijfslocaties een specifiek IP-subnet toegewezen krijgen, wordt Azure een andere locatie wanneer u uw netwerk uitbreidt.
Er zijn verschillende opties voor het implementeren van een virtueel netwerk.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [Powershell](../../virtual-network/quick-create-powershell.md)

- [Command-Line Interface (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager-sjablonen

> **Wanneer u het gebruik wilt gebruiken:** Wanneer u met VM's in Azure werkt, werkt u met virtuele netwerken. Hierdoor kunnen uw VM's worden gesegmenterd in openbare en particuliere subnetten vergelijkbare on-premises datacenters.
>
> **Aan de slag:** het implementeren van een Virtueel Azure-netwerk met behulp van de Azure-portal vereist alleen een actief Azure-abonnement en toegang tot een webbrowser. U een nieuw virtueel netwerk implementeren in een nieuwe of bestaande brongroep. Wanneer u een nieuwe virtuele machine maakt vanuit de portal, u een bestaand virtueel netwerk selecteren of een nieuw netwerk maken. Ga aan de slag en [maak een virtueel netwerk met de Azure-portal.](../../virtual-network/quick-create-portal.md)

### <a name="access-and-security-for-virtual-networks"></a>Toegang en beveiliging voor virtuele netwerken

U azure virtuele netwerken beveiligen met behulp van een netwerkbeveiligingsgroep. NSG's bevatten een lijst met ACL-regels (Access Control List) die netwerkverkeer toestaan of weigeren naar uw VM-exemplaren in een virtueel netwerk. U NSG's koppelen aan subnetten of afzonderlijke VM-exemplaren in dat subnet. Wanneer u een NSG koppelt aan een subnet, zijn de ACL-regels van toepassing op alle VM-exemplaren in dat subnet. Bovendien u het verkeer verder beperken tot een individuele VM door een NSG rechtstreeks aan die VM te koppelen. Raadpleeg [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../../virtual-network/security-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Een Windows-VM maken](../../virtual-machines/windows/quick-create-portal.md)
- [Een Linux-VM maken](../../virtual-machines/linux/quick-create-portal.md)
