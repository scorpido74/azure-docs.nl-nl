---
title: Handleiding voor ontwikkelaars op Azure | Microsoft Documenten
description: Dit artikel bevat essentiële informatie voor ontwikkelaars die aan de slag willen met het Microsoft Azure-platform voor hun ontwikkelingsbehoeften.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: d74fd2e3f6b5cc090c6313aba67a1e139c713b85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245003"
---
# <a name="get-started-guide-for-azure-developers"></a>Handleiding voor Azure-ontwikkelaars aan de slag

## <a name="what-is-azure"></a>Wat is Azure?

Azure is een compleet cloudplatform dat uw bestaande toepassingen kan hosten en de ontwikkeling van nieuwe toepassingen kan stroomlijnen. Azure kan zelfs on-premises toepassingen verbeteren. Azure integreert de cloudservices die u nodig hebt om uw toepassingen te ontwikkelen, te testen, implementeren en beheren, terwijl u profiteert van de efficiëntie van cloud computing.

Door uw toepassingen in Azure te hosten, u uw toepassing klein en eenvoudig schalen naarmate de vraag van uw klant toeneemt. Azure biedt ook de betrouwbaarheid die nodig is voor toepassingen met hoge beschikbaarheid, zelfs met inbegrip van failover tussen verschillende regio's. Met de [Azure-portal](https://portal.azure.com) u eenvoudig al uw Azure-services beheren. U uw services ook programmatisch beheren met behulp van servicespecifieke API's en sjablonen.

Deze handleiding is een inleiding tot het Azure-platform voor toepassingsontwikkelaars. Het biedt richtlijnen en aanwijzingen die u nodig hebt om te beginnen met het bouwen van nieuwe toepassingen in Azure of het migreren van bestaande toepassingen naar Azure.

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

Met alle services die Azure biedt, kan het een intimiderende taak zijn om erachter te komen welke services u nodig hebt om uw oplossingsarchitectuur te ondersteunen. In dit gedeelte worden de Azure-services belicht die ontwikkelaars vaak gebruiken. Zie de [Azure-documentatie](../../index.yml)voor een lijst met alle Azure-services.

Eerst moet u beslissen hoe u uw toepassing in Azure host. Moet u uw hele infrastructuur beheren als een virtuele machine (VM). u gebruik maken van de platformbeheerfaciliteiten die Azure biedt? Misschien heb je een serverless framework nodig om alleen code-uitvoering te hosten?

Uw toepassing heeft cloudopslag nodig, waarvoor Azure verschillende opties biedt. U profiteren van de bedrijfsverificatie van Azure. Er zijn ook tools voor cloudgebaseerde ontwikkeling en monitoring, en de meeste hostingservices bieden DevOps-integratie.

Laten we nu eens kijken naar enkele van de specifieke services die we aanbevelen te onderzoeken voor uw toepassingen.

### <a name="application-hosting"></a>Toepassingshosting

Azure biedt verschillende cloudgebaseerde compute-aanbiedingen om uw toepassing uit te voeren, zodat u zich geen zorgen hoeft te maken over de infrastructuurdetails. U uw resources eenvoudig opschalen of opschalen naarmate uw toepassingsgebruik toeneemt.

Azure biedt services die uw toepassingsontwikkeling en hostingbehoeften ondersteunen. Azure biedt Infrastructure as a Service (IaaS) om u volledige controle te geven over uw toepassingshosting. Het Platform as a Service -aanbod (Platform as a Service) van Azure biedt de volledig beheerde services die nodig zijn om uw apps van stroom te voorzien. Er is zelfs echte serverloze hosting in Azure waar u alleen uw code hoeft te schrijven.

![Azure-toepassingshostingopties](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service

Als u het snelste pad wilt publiceren om uw webprojecten te publiceren, u Azure App Service overwegen. App Service maakt het eenvoudig om uw web-apps uit te breiden om uw mobiele klanten te ondersteunen en gemakkelijk gebruikte REST API's te publiceren. Dit platform biedt verificatie met behulp van sociale providers, op verkeer gebaseerde autoscaling, testen in productie en continue en containergebaseerde implementaties.

U web-apps, back-ends van mobiele apps en API-apps maken.

Omdat alle drie de app-typen de runtime van de App-service delen, u een website hosten, mobiele clients ondersteunen en uw API's in Azure blootstellen, allemaal vanuit hetzelfde project of dezelfde oplossing. Zie Wat is Azure Web Apps voor meer informatie over [App-service.](../../app-service/overview.md)

App Service is ontworpen met DevOps in het achterhoofd. Het ondersteunt verschillende tools voor publicatie en implementaties van continue integratie. Deze tools omvatten GitHub webhooks, Jenkins, Azure DevOps, TeamCity en anderen.

U uw bestaande toepassingen migreren naar App Service met behulp van de [online migratietool.](https://appmigration.microsoft.com/)

> **Wanneer te gebruiken**: Gebruik App Service wanneer u bestaande webtoepassingen migreert naar Azure en wanneer u een volledig beheerd hostingplatform voor uw webapps nodig hebt. U appservice ook gebruiken wanneer u mobiele clients moet ondersteunen of REST API's met uw app wilt blootstellen.
>
> **Aan de slag:** App Service maakt het eenvoudig om uw eerste [web-app,](../../app-service/app-service-web-get-started-dotnet.md) [mobiele app](../../app-service-mobile/app-service-mobile-ios-get-started.md)of [API-app](../../app-service/app-service-web-tutorial-rest-api.md)te maken en te implementeren.
>
> **Probeer het nu:** met App Service u een app van korte duur inrichten om het platform uit te proberen zonder u aan te melden voor een Azure-account. Probeer het platform en [maak uw Azure App Service-app](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Als IaaS-provider (Infrastructure as a Service) u uw toepassing implementeren of migreren naar Windows- of Linux-vm's. Samen met Azure Virtual Network ondersteunt Azure Virtual Machines de implementatie van Windows- of Linux VM's in Azure. Met VM's hebt u totale controle over de configuratie van de machine. Bij het gebruik van VM's bent u verantwoordelijk voor alle patchs voor de installatie, configuratie, onderhoud en het besturingssysteem van de serversoftware.

Vanwege het controleniveau dat u met VM's hebt, u een breed scala aan serverworkloads op Azure uitvoeren die niet in een PaaS-model passen. Deze workloads omvatten databaseservers, Windows Server Active Directory en Microsoft SharePoint. Zie voor meer informatie de documentatie van virtuele machines voor [Linux](/azure/virtual-machines/linux/) of [Windows.](/azure/virtual-machines/windows/)

> **Wanneer te gebruiken**: Gebruik virtuele machines wanneer u volledige controle wilt over uw toepassingsinfrastructuur of on-premises toepassingsworkloads wilt migreren naar Azure zonder wijzigingen aan te brengen.
>
> **Aan de slag:** maak een [Linux VM](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) of [Windows VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) vanuit de Azure-portal.

#### <a name="azure-functions-serverless"></a>Azure-functies (serverloos)

In plaats van zorgen te maken over het bouwen en beheren van een hele applicatie of de infrastructuur om uw code uit te voeren, wat als je gewoon je code schrijven en deze laten uitvoeren in reactie op gebeurtenissen of op een schema?  [Azure Functions](../../azure-functions/functions-overview.md) is een serverless-stijl aanbod waarmee u alleen de code schrijven die u nodig hebt. Met Functions u code-uitvoering activeren met HTTP-aanvragen, webhooks, cloudservicegebeurtenissen of een planning. U coderen in uw ontwikkelingstaal\#naar\#keuze, zoals C, F, Node.js, Python of PHP. Met facturering op basis van verbruik betaalt u alleen voor de tijd die uw code uitvoert en Azure schaalt indien nodig.

> **Wanneer te gebruiken**: Gebruik Azure-functies wanneer u code hebt die wordt geactiveerd door andere Azure-services, op webgebaseerde gebeurtenissen of volgens een planning. U functies ook gebruiken wanneer u de overhead van een volledig gehost project niet nodig hebt of wanneer u alleen wilt betalen voor de tijd dat uw code wordt uitgevoerd. Zie Overzicht [van Azure-functies](../../azure-functions/functions-overview.md)voor meer informatie.
>
> **Aan de slag:** Volg de snelaanstudie Functies om je eerste functie vanuit de portal te [maken.](../../azure-functions/functions-create-first-azure-function.md)
>
> **Probeer het nu:** met Azure Functions u uw code uitvoeren zonder dat u zich hoeft aan te melden voor een Azure-account. Probeer het nu op en [maak uw eerste Azure-functie.](https://tryappservice.azure.com/)

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric is een gedistribueerd systeemplatform. Dit platform maakt het eenvoudig om schaalbare en betrouwbare microservices te bouwen, te verpakken, implementeren en beheren. Het biedt ook uitgebreide mogelijkheden voor applicatiebeheer, zoals:

* Inrichten
* Implementeren
* Bewaking
* Upgraden/patchen
* Verwijderen

Apps, die op een gedeelde pool van machines worden uitgevoerd, kunnen klein starten en naar behoefte schalen naar honderden of duizenden machines.

Service Fabric ondersteunt WebAPI met Open Web Interface voor .NET (OWIN) en ASP.NET Core. Het biedt SDKs voor het bouwen van diensten op Linux in zowel .NET Core en Java. Zie de [documentatie servicestof](https://docs.microsoft.com/azure/service-fabric/)voor meer informatie over Service Fabric.

> **Wanneer te gebruiken:** Service Fabric is een goede keuze wanneer u een toepassing maakt of een bestaande toepassing herschrijft om een microservicearchitectuur te gebruiken. Gebruik Service Fabric wanneer u meer controle nodig hebt over of directe toegang tot de onderliggende infrastructuur.
>
> **Aan de slag:** [maak uw eerste Azure Service Fabric-toepassing.](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md)

### <a name="enhance-your-applications-with-azure-services"></a>Uw toepassingen verbeteren met Azure-services

Samen met toepassingshosting biedt Azure serviceaanbiedingen die de functionaliteit kunnen verbeteren. Azure kan ook de ontwikkeling en het onderhoud van uw toepassingen verbeteren, zowel in de cloud als on-premises.

#### <a name="hosted-storage-and-data-access"></a>Gehoste opslag en toegang tot gegevens

De meeste toepassingen moeten gegevens opslaan, dus hoe u ook besluit uw toepassing in Azure te hosten, overweeg een of meer van de volgende opslag- en gegevensservices.

- **Azure Cosmos DB**: een wereldwijd gedistribueerde databaseservice met meerdere modellen. Met deze database u de doorvoer en opslag elastisch schalen in een willekeurig aantal geografische regio's met een uitgebreide SLA.

  > **Wanneer te gebruiken:** Wanneer uw toepassing document-, tabel- of grafiekdatabases, waaronder MongoDB-databases, met meerdere goed gedefinieerde consistentiemodellen nodig heeft.
  >
  > **Aan de slag:** [maak een Azure Cosmos DB-web-app](../../cosmos-db/create-sql-api-dotnet.md). Zie [Een MongoDB-webapp bouwen met Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md)als u een MongoDB-ontwikkelaar bent.

- **Azure Storage:** biedt duurzame, zeer beschikbare opslag voor blobs, wachtrijen, bestanden en andere soorten niet-relationele gegevens. Opslag vormt de opslagbasis voor VM's.

  > **Wanneer moet worden gebruikt:** wanneer uw app niet-relationele gegevens opslaat, zoals sleutelwaardeparen (tabellen), blobs, bestandenshares of berichten (wachtrijen).
  >
  > **Aan de slag:** kies uit een van deze typen opslag: [blobs,](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) [tabellen,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [wachtrijen](../../storage/queues/storage-dotnet-how-to-use-queues.md)of [bestanden](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: een op Azure gebaseerde versie van de Microsoft SQL Server-engine voor het opslaan van relationele tabelgegevens in de cloud. SQL Database biedt voorspelbare prestaties, schaalbaarheid zonder downtime, bedrijfscontinuïteit en gegevensbescherming.

  > **Wanneer te gebruiken**: Wanneer uw toepassing gegevensopslag vereist met referentiële integriteit, transactionele ondersteuning en ondersteuning voor TSQL-query's.
  >
  > **Aan de slag:** [maak binnen enkele minuten een SQL-database met behulp van de Azure-portal.](../../sql-database/sql-database-get-started.md)


U [Azure Data Factory](../../data-factory/introduction.md) gebruiken om bestaande on-premises gegevens naar Azure te verplaatsen. Als u nog niet klaar bent om gegevens naar de cloud te verplaatsen, u met [Hybride verbindingen](../../app-service/app-service-hybrid-connections.md) in Azure App Service uw door App Service gehoste app verbinden met on-premises resources. U ook verbinding maken met Azure-gegevens- en opslagservices vanuit uw on-premises toepassingen.

#### <a name="docker-support"></a>Ondersteuning voor Docker

Docker-containers, een vorm van OS-virtualisatie, laten u toepassingen efficiënter en voorspelbaarder implementeren. Een gecontaineriseerde applicatie werkt in productie op dezelfde manier als op uw ontwikkelings- en testsystemen. U containers beheren met behulp van standaard Docker-tools. U uw bestaande vaardigheden en populaire open-sourcetools gebruiken om containergebaseerde toepassingen op Azure te implementeren en te beheren.

Azure biedt verschillende manieren om containers in uw toepassingen te gebruiken.

- **Azure Docker VM-extensie:** hiermee u uw VM configureren met Docker-hulpprogramma's om als Docker-host te fungeren.

  > **Wanneer u het gebruik**wilt gebruiken: wanneer u consistente containerimplementaties voor uw toepassingen op een vm wilt genereren of wanneer u [Docker Compose](https://docs.docker.com/compose/overview/)wilt gebruiken.
  >
  > **Aan de slag:** [maak een Docker-omgeving in Azure met behulp van de Docker VM-extensie](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure Kubernetes-service:** hiermee u een cluster van virtuele machines maken, configureren en beheren die vooraf zijn geconfigureerd om gecontaineriseerde toepassingen uit te voeren. Zie [Azure Kubernetes Service-inleiding](../../aks/intro-kubernetes.md)voor meer informatie over Azure Kubernetes Service.

  > **Wanneer u het gebruik wilt gebruiken:** wanneer u productieklare, schaalbare omgevingen moet bouwen die extra plannings- en beheertools bieden, of wanneer u een Docker Swarm-cluster implementeert.
  >
  > **Aan de slag:** [een Kubernetes Service-cluster implementeren](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Docker Machine:** Hiermee u een Docker Engine installeren en beheren op virtuele hosts met behulp van docker-machine commando's.

  >**Wanneer te gebruiken:** Wanneer u snel een prototype van een app moet maken door één Docker-host te maken.

- **Aangepaste Docker-afbeelding voor App-service:** hiermee u Docker-containers gebruiken uit een containerregister of een klantcontainer wanneer u een web-app op Linux implementeert.

  > **Wanneer te gebruiken**: Bij het implementeren van een web-app op Linux naar een Docker-afbeelding.
  >
  > **Aan de slag:** [gebruik een aangepaste Docker-afbeelding voor App-service op Linux.](../../app-service/containers/quickstart-docker-go.md)

### <a name="authentication"></a>Authentication

Het is cruciaal om niet alleen te weten wie uw applicaties gebruikt, maar ook om ongeautoriseerde toegang tot uw bronnen te voorkomen. Azure biedt verschillende manieren om uw app-clients te verifiëren.

- **Azure Active Directory (Azure AD):** de microsoft multitenant, cloudgebaseerde identiteits- en toegangsbeheerservice. U single-sign on (SSO) toevoegen aan uw toepassingen door te integreren met Azure AD. U hebt toegang tot directory-eigenschappen door de Azure AD Graph-API rechtstreeks of de Microsoft Graph-API te gebruiken. U integreren met Azure AD-ondersteuning voor het OAuth2.0-autorisatiekader en Open ID Connect met behulp van native HTTP/REST-eindpunten en de multiplatform Azure AD-verificatiebibliotheken.

  > **Wanneer u het gebruik wilt gebruiken:** Wanneer u een SSO-ervaring wilt bieden, werkt u met op grafieken gebaseerde gegevens of verifieert u gebruikers op basis van een domein.
  >
  > **Aan de slag:** zie de handleiding van Azure [Active Directory-ontwikkelaars](../../active-directory/develop/v2-overview.md)voor meer informatie.

- **App Service-verificatie:** wanneer u App Service kiest om uw app te hosten, krijgt u ook ingebouwde verificatieondersteuning voor Azure AD, samen met aanbieders van sociale identiteit, waaronder Facebook, Google, Microsoft en Twitter.

  > **Wanneer u:** wanneer u verificatie in een App Service-app wilt inschakelen met Azure AD, sociale identiteitsproviders of beide.
  >
  > **Aan de slag:** zie Verificatie en [autorisatie in Azure App Service](../../app-service/overview-authentication-authorization.md)voor meer informatie over verificatie in App-service.

Zie aanbevolen procedures en patronen voor [Azure-beveiliging](../../security/fundamentals/best-practices-and-patterns.md)voor meer informatie over aanbevolen procedures voor beveiliging in Azure.

### <a name="monitoring"></a>Bewaking

Als uw toepassing in Azure wordt uitgevoerd, moet u de prestaties bewaken, problemen bekijken en zien hoe klanten uw app gebruiken. Azure biedt verschillende bewakingsopties.

-   **Application Insights:** een door Azure gehoste extensible analytics-service die integreert met Visual Studio om uw live webtoepassingen te monitoren. Het geeft u de gegevens die u nodig hebt om de prestaties en bruikbaarheid van uw apps continu te verbeteren. Deze verbetering vindt plaats, ongeacht of u uw toepassingen host op Azure of niet.

    >**Aan de slag:** Volg de [zelfstudie Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor:** een service waarmee u de statistieken en logboeken die u genereert met uw Azure-infrastructuur en -bronnen visualiseren, opvragen, routeren, archiveren en handelen. Monitor is één bron voor het bewaken van Azure-resources en biedt de gegevensweergaven die u in de Azure-portal ziet.

    >**Aan de slag:** [Aan de slag met Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps integratie

Of het nu gaat om het inrichten van VM's of het publiceren van uw web-apps met continue integratie, Azure integreert met de meeste populaire DevOps-hulpprogramma's. U werken met de tools die u al hebt en uw bestaande ervaring maximaliseren met ondersteuning voor tools zoals:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity (TeamCity)
* Ansible
* Azure DevOps

> **Aan de slag:** zie Continue implementatie naar Azure App Service als u devOps-opties voor een App Service-app [wilt bekijken.](../../app-service/deploy-continuous-deployment.md)
>
> **Probeer het nu:** [Probeer een aantal van de DevOps integraties](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Azure-regio's

Azure is een wereldwijd cloudplatform dat algemeen beschikbaar is in veel regio's over de hele wereld. Wanneer u een service, toepassing of VM indient in Azure, wordt u gevraagd een regio te selecteren. Deze regio vertegenwoordigt een specifiek datacenter waar uw toepassing wordt uitgevoerd of waar uw gegevens worden opgeslagen. Deze regio's komen overeen met specifieke locaties die worden gepubliceerd op de pagina [Azure-regio's.](https://azure.microsoft.com/regions/)

### <a name="choose-the-best-region-for-your-application-and-data"></a>Kies de beste regio voor uw toepassing en gegevens

Een van de voordelen van het gebruik van Azure is dat u uw toepassingen implementeren in verschillende datacenters over de hele wereld. De regio die u kiest, kan van invloed zijn op de prestaties van uw toepassing. Het is bijvoorbeeld beter om een regio te kiezen die dichter bij de meeste van uw klanten staat om de latentie in netwerkaanvragen te verminderen. U ook uw regio selecteren om te voldoen aan de wettelijke vereisten voor het distribueren van uw app in bepaalde landen/regio's. Het is altijd een goede gewoonte om toepassingsgegevens op te slaan in hetzelfde datacenter of in een datacenter zo dicht mogelijk bij het datacenter dat uw toepassing host.

### <a name="multi-region-apps"></a>Apps met meerdere regio's

Hoewel onwaarschijnlijk, is het niet onmogelijk voor een heel datacenter om offline te gaan vanwege een gebeurtenis zoals een natuurramp of internetstoring. Het is een beste gewoonte om vitale bedrijfstoepassingen in meer dan één datacenter te hosten om maximale beschikbaarheid te bieden. Het gebruik van meerdere regio's kan ook de latentie voor wereldwijde gebruikers verminderen en extra mogelijkheden bieden voor flexibiliteit bij het bijwerken van toepassingen.

Sommige services, zoals Virtual Machine en App Services, gebruiken [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) om ondersteuning met meerdere regio's mogelijk te maken met failover tussen regio's om bedrijfstoepassingen met hoge beschikbaarheid te ondersteunen. Zie bijvoorbeeld [Azure-referentiearchitectuur: voer een webtoepassing uit in meerdere regio's](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Wanneer te gebruiken:** Wanneer u bedrijfs- en toepassingen met hoge beschikbaarheid hebt die profiteren van failover en replicatie.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hoe kan ik mijn toepassingen en projecten beheren?

Azure biedt u een uitgebreide reeks ervaringen om uw Azure-resources, -toepassingen en -projecten te maken en te beheren, zowel programmatisch als in de [Azure-portal.](https://portal.azure.com/)

### <a name="command-line-interfaces-and-powershell"></a>Command-line interfaces en PowerShell

Azure biedt twee manieren om uw toepassingen en services vanaf de opdrachtregel te beheren. U tools gebruiken zoals Bash, Terminal, de opdrachtprompt of uw opdrachtregelgereedschap naar keuze. Meestal u dezelfde taken uitvoeren vanaf de opdrachtregel als in de Azure-portal, zoals het maken en configureren van virtuele machines, virtuele netwerken, web-apps en andere services.

-   [Azure Command-Line Interface (CLI)](../../xplat-cli-install.md): hiermee u verbinding maken met een Azure-abonnement en verschillende taken programmeren ten opzichte van Azure-resources van de opdrachtregel.

-   [Azure PowerShell:](../../powershell-install-configure.md)biedt een reeks modules met cmdlets waarmee u Azure-resources beheren met Windows PowerShell.

### <a name="azure-portal"></a>Azure Portal

De [Azure-portal](https://portal.azure.com) is een webgebaseerde toepassing. U de Azure-portal gebruiken om Azure-bronnen en -services te maken, te beheren en te verwijderen. Het omvat:

* Een configureerbaar dashboard
* Azure-hulpprogramma's voor resourcebeheer
* Toegang tot abonnementsinstellingen en factureringsgegevens. Zie het overzicht van de [Azure-portal](../../azure-portal-overview.md)voor meer informatie.

### <a name="rest-apis"></a>REST-API’s

Azure is gebouwd op een set REST-API's die de gebruikersinterface van de Azure-portal ondersteunen. De meeste van deze REST-API's worden ook ondersteund om u programmatisch uw Azure-bronnen en -toepassingen te laten inrichten en beheren vanaf elk apparaat dat in internet is ingeschakeld. Zie de [Azure REST SDK-verwijzing](https://docs.microsoft.com/rest/api/)voor de volledige set REST API-documentatie.

### <a name="apis"></a>API's

Samen met REST API's u met veel Azure-services ook programmatisch resources uit uw toepassingen beheren met behulp van platformspecifieke Azure SDK's, waaronder SDK's voor de volgende ontwikkelplatforms:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [Php](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [OK](https://docs.microsoft.com/azure/go)

Services zoals [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) en Azure Media [Services](../../media-services/previous/media-services-dotnet-how-to-use.md) bieden client-side SDKs waarmee u toegang hebt tot services van web- en mobiele client-apps.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Het uitvoeren van uw app op Azure betekent waarschijnlijk het werken met meerdere Azure-services. Deze diensten volgen dezelfde levenscyclus en kunnen worden gezien als een logische eenheid. Een web-app kan bijvoorbeeld Web Apps, SQL Database, Storage, Azure Cache for Redis en Azure Content Delivery Network-services gebruiken. [Met Azure Resource](../../azure-resource-manager/management/overview.md) Manager u als groep werken met de resources in uw toepassing. U alle bronnen in één gecoördineerde bewerking implementeren, bijwerken of verwijderen.

Samen met het logisch groeperen en beheren van gerelateerde resources, bevat Azure Resource Manager implementatiemogelijkheden waarmee u de implementatie en configuratie van gerelateerde resources aanpassen. U bijvoorbeeld Resource Manager implementeren gebruiken en een toepassing configureren. Deze toepassing kan bestaan uit meerdere virtuele machines, een load balancer en een Azure SQL-database als één eenheid.

U ontwikkelt deze implementaties met behulp van een Azure Resource Manager-sjabloon, een document met JSON-indeling. Met sjablonen u een implementatie definiëren en uw toepassingen beheren met behulp van declaratieve sjablonen, in plaats van scripts. Uw sjablonen kunnen werken voor verschillende omgevingen, zoals testen, fasering en productie. U bijvoorbeeld sjablonen gebruiken om een knop toe te voegen aan een GitHub-repo die de code in de repo met één klik implementeert naar een set Azure-services.

> **Wanneer u het gebruik wilt gebruiken:** Gebruik Resource Manager-sjablonen wanneer u een op een sjabloon gebaseerde implementatie voor uw app wilt die u programmatisch beheren met behulp van REST-API's, Azure CLI en Azure PowerShell.
>
> **Aan de slag:** Zie [Azure Resource Manager-sjablonen ontwerpen](../../resource-group-authoring-templates.md)om aan de slag te gaan met sjablonen.

## <a name="understanding-accounts-subscriptions-and-billing"></a>Inzicht in accounts, abonnementen en facturering

Als ontwikkelaars duiken we graag direct in de code en proberen we zo snel mogelijk aan de slag te gaan met het draaien van onze applicaties. We willen u zeker aanmoedigen om zo gemakkelijk mogelijk in Azure te gaan werken. Om het u gemakkelijk te maken, biedt Azure een [gratis proefversie.](https://azure.microsoft.com/free/) Sommige services hebben zelfs een 'Probeer het gratis'-functionaliteit, zoals [Azure App Service,](https://tryappservice.azure.com/)waarvoor u niet eens een account hoeft aan te maken. Hoe leuk het ook is om te duiken in codering en het implementeren van uw toepassing op Azure, het is ook belangrijk om enige tijd te nemen om te begrijpen hoe Azure werkt. U moet in het bijzonder begrijpen hoe het werkt vanuit het standpunt van gebruikersaccounts, abonnementen en facturering.

### <a name="what-is-an-azure-account"></a>Wat is een Azure-account?

Als u een Azure-abonnement wilt maken of gebruiken, moet u een Azure-account hebben. Een Azure-account is gewoon een identiteit in Azure AD of in een map, zoals een werk- of schoolorganisatie, die door Azure AD wordt vertrouwd. Als u niet tot een dergelijke organisatie behoort, u altijd een abonnement maken met uw Microsoft-account, dat wordt vertrouwd door Azure AD. Zie [Uw on-premises identiteiten integreren met Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md)voor meer informatie over het integreren van on-premises Windows Server Active Directory met Azure AD.

Voor elk Azure-abonnement is er een vertrouwensrelatie met een Azure AD-exemplaar. Dit betekent dat er op die directory wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde directory vertrouwen, maar een abonnement vertrouwt slechts één directory. Zie hoe [Azure-abonnementen zijn gekoppeld aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie.

Naast het definiëren van afzonderlijke Azure-accountidentiteiten, ook wel *gebruikers*genoemd, u *groepen* definiëren in Azure AD. Het maken van gebruikersgroepen is een goede manier om de toegang tot bronnen in een abonnement te beheren met behulp van rbac (role-based access control). Zie Een groep maken [in Azure Active Directory preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van groepen. U ook groepen maken en beheren [met PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Uw abonnementen beheren

Een abonnement is een logische groepering van Azure-services die is gekoppeld aan een Azure-account. Eén Azure-account kan meerdere abonnementen bevatten. Facturering voor Azure-services gebeurt per abonnement. Zie [Microsoft Azure-aanbiedingsgegevens](https://azure.microsoft.com/support/legal/offer-details/)voor een lijst met beschikbare abonnementsaanbiedingen per type. Azure-abonnementen hebben een accountbeheerder die volledige controle heeft over het abonnement. Ze hebben ook een servicebeheerder die controle heeft over alle services in het abonnement. Zie [Azure-abonnementsbeheerders toevoegen of wijzigen](../../cost-management-billing/manage/add-change-subscription-administrator.md)voor informatie over beheerders van klassieke abonnementen. Afzonderlijke accounts kunnen gedetailleerde controle over Azure-resources krijgen met behulp van [rbac (Role-based access control).](../../role-based-access-control/overview.md)

#### <a name="resource-groups"></a>Resourcegroepen

Wanneer u nieuwe Azure-services indient, doet u dit in een bepaald abonnement. Afzonderlijke Azure-services, die ook resources worden genoemd, worden gemaakt in de context van een resourcegroep. Resourcegroepen maken het eenvoudiger om de resources van uw toepassing te implementeren en te beheren. Een resourcegroep moet alle bronnen voor uw toepassing bevatten waarmee u als eenheid wilt werken. U resources verplaatsen tussen resourcegroepen en zelfs naar verschillende abonnementen. Zie [Resources verplaatsen naar een nieuwe resourcegroep of -abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie over het verplaatsen van resources.

De Azure Resource Explorer is een geweldig hulpmiddel voor het visualiseren van de bronnen die u al hebt gemaakt in uw abonnement. Zie [Azure Resource Explorer gebruiken om bronnen weer te geven en te wijzigen](../../resource-manager-resource-explorer.md)voor meer informatie.

#### <a name="grant-access-to-resources"></a>Toegang tot resources verlenen

Wanneer u toegang tot Azure-bronnen toestaat, is het altijd een aanbevolen manier om gebruikers de minste bevoegdheden te bieden die nodig zijn om een bepaalde taak uit te voeren.

- **RBAC (Role-based access control):** in Azure u toegang verlenen tot gebruikersaccounts (principals) op een bepaald bereik: abonnement, resourcegroep of afzonderlijke resources. Met RBAC u resources implementeren in een resourcegroep en machtigingen verlenen aan een specifieke gebruiker of groep. Hiermee u ook de toegang beperken tot alleen de resources die behoren tot de doelgroep. U ook toegang verlenen tot één resource, zoals een virtuele machine of virtueel netwerk. Als u toegang wilt verlenen, wijst u een rol toe aan de gebruiker, groep of serviceprincipal. Er zijn veel vooraf gedefinieerde rollen en u ook uw eigen aangepaste rollen definiëren. Zie [Wat is op rollen gebaseerd toegangscontrole (RBAC)?](../../role-based-access-control/overview.md).

  > **Wanneer te gebruiken**: Wanneer u fijnkorrelig toegangsbeheer voor gebruikers en groepen nodig hebt of wanneer u een gebruiker eigenaar van een abonnement moet maken.
  >
  > **Aan de slag:** zie [Toegang beheren met RBAC en de Azure-portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

- **Servicehoofdobjecten:** U dezelfde toegang verlenen tot een serviceprincipal, samen met het bieden van toegang tot gebruikersprincipals en -groepen.

  > **Wanneer te gebruiken:** Wanneer u Azure-resources programmatisch beheert of toegang verleent voor toepassingen. Zie [Active Directory-toepassing en serviceprincipal maken](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

#### <a name="tags"></a>Tags

Met Azure Resource Manager u aangepaste tags toewijzen aan afzonderlijke bronnen. Tags, die sleutelwaardeparen zijn, kunnen handig zijn wanneer u resources moet ordenen voor facturering of bewaking. Tags bieden u een manier om resources in meerdere resourcegroepen bij te houden. U tags op de volgende manieren toewijzen:

* In het portaal
* In de sjabloon Azure Resource Manager
* Met behulp van de REST API
* Azure CLI gebruiken
* PowerShell gebruiken

U meerdere tags toewijzen aan elke resource. Zie [Tags gebruiken om uw Azure-bronnen te ordenen](../../resource-group-using-tags.md)voor meer informatie.

### <a name="billing"></a>Billing

Bij de overgang van on-premises computing naar cloudgehoste services zijn het bijhouden en schatten van servicegebruik en gerelateerde kosten belangrijke zorgen. Het is belangrijk om in te schatten welke nieuwe resources maandelijks moeten worden uitgevoerd. U ook projecteren hoe de facturering er uitziet voor een bepaalde maand op basis van de huidige uitgaven.

#### <a name="get-resource-usage-data"></a>Gegevens over resourcegebruik verzamelen

Azure biedt een set FactureringsREST-API's die toegang geven tot bronverbruik en metagegevens voor Azure-abonnementen. Deze Facturerings-API's bieden u de mogelijkheid om azure-kosten beter te voorspellen en te beheren. U uitgaven per uur bijhouden en analyseren en uitgavenwaarschuwingen maken. U ook toekomstige facturering voorspellen op basis van de huidige gebruikstrends.

>**Aan de slag:** zie overzicht van [Azure Billing Usage en RateCard API's](../../cost-management-billing/manage/usage-rate-card-overview.md)voor meer informatie over het gebruik van de facturerings-API's .

#### <a name="predict-future-costs"></a>Toekomstige kosten voorspellen

Hoewel het een uitdaging is om kosten van tevoren in te schatten, heeft Azure tools die u kunnen helpen. Het heeft een [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van geïmplementeerde resources te helpen schatten. U ook de factureringsresources in de portal en de API's voor factureringrest gebruiken om toekomstige kosten te schatten op basis van het huidige verbruik.

>**Aan de slag:** zie [overzicht van Azure Billing Usage en RateCard API's](../../cost-management-billing/manage/usage-rate-card-overview.md).
