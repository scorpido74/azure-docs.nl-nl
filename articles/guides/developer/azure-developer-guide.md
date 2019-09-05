---
title: Aan de slag-hand leiding voor ontwikkel aars op Azure | Microsoft Docs
description: In dit onderwerp vindt u essentiële informatie voor ontwikkel aars die aan de slag willen gaan met behulp van het Microsoft Azure platform voor hun ontwikkelings behoeften.
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
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 367027a48dd51dd88de63cb163e05e0e8503755d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308587"
---
# <a name="get-started-guide-for-azure-developers"></a>Aan de slag-handleiding voor Azure-ontwikkelaars

## <a name="what-is-azure"></a>Wat is Azure?

Azure is een volledig Cloud platform dat uw bestaande toepassingen kan hosten, de ontwikkeling van nieuwe toepassingen kan stroom lijnen en zelfs on-premises toepassingen kan verbeteren. Azure integreert de Cloud Services die u nodig hebt om uw toepassingen te ontwikkelen, testen, implementeren en beheren, terwijl u profiteert van de efficiëntie van Cloud Computing.

Door uw toepassingen in azure te hosten, kunt u kleine en eenvoudig uw toepassing schalen naarmate uw klant vraag groeit. Azure biedt ook de betrouw baarheid die nodig is voor toepassingen met een hoge Beschik baarheid, zelfs voor failover tussen verschillende regio's. Met de [Azure Portal](https://portal.azure.com) kunt u eenvoudig al uw Azure-Services beheren. U kunt uw services ook programmatisch beheren door gebruik te maken van servicespecifieke Api's en sjablonen.

**Wie moet dit lezen**: Deze hand leiding is een inleiding tot het Azure-platform voor toepassings ontwikkelaars. Het bevat richt lijnen en richting die u nodig hebt om nieuwe toepassingen te bouwen in azure of om bestaande toepassingen te migreren naar Azure.

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

Met alle services die Azure biedt, kan dit een onduidelijke taak zijn om te achterhalen welke services u nodig hebt om uw oplossings architectuur te ondersteunen. In deze sectie worden de Azure-Services uitgelegd die ontwikkel aars vaak gebruiken. Zie de [Azure-documentatie](../../index.md)voor een lijst met alle Azure-Services.

Eerst moet u bepalen hoe u uw toepassing in azure wilt hosten. Moet u uw volledige infra structuur beheren als een virtuele machine (VM). Kunt u gebruikmaken van de platform beheer faciliteiten die Azure biedt? Misschien hebt u een serverloos Framework nodig om alleen de uitvoering van de host te kunnen uitvoeren?

Voor uw toepassing is Cloud opslag vereist, waarvoor Azure verschillende opties biedt voor. U kunt profiteren van de Enter prise-verificatie van Azure. Er zijn ook hulpprogram ma's voor het ontwikkelen en bewaken van de Cloud, en de meeste hosting services bieden DevOps-integratie.

Laten we nu eens kijken naar enkele van de specifieke services die we aanraden voor het onderzoeken van uw toepassingen.

### <a name="application-hosting"></a>Hosting van toepassingen

Azure biedt verschillende Cloud-based Compute-aanbiedingen om uw toepassing uit te voeren, zodat u zich geen zorgen hoeft te maken over de details van de infra structuur. U kunt uw resources eenvoudig schalen of uitschalen naarmate het gebruik van uw toepassing toeneemt.

Azure biedt services die ondersteuning bieden voor het ontwikkelen en hosten van toepassingen. Azure biedt een IaaS (Infrastructure as a Service) waarmee u volledige controle hebt over de hosting van uw toepassing. De PaaS-aanbiedingen (platform as a Service) van Azure bieden de volledig beheerde services die nodig zijn om uw apps uit te scha kelen. Er is zelfs sprake van een serverloze hosting in azure, waar u alleen uw code hoeft te schrijven.

![Opties voor Azure-hosting van toepassingen](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Als u wilt dat het snelste pad voor het publiceren van uw webprojecten, moet u Azure App Service. App Service maakt het eenvoudig om uw web-apps uit te breiden om uw mobiele clients te ondersteunen en eenvoudig geconsumeerde REST Api's te publiceren. Dit platform biedt verificatie met behulp van sociale providers, automatisch schalen op basis van verkeer, testen in productie en doorlopende implementaties op basis van containers.

U kunt web-apps, back-ends voor mobiele apps en API-apps maken.

Omdat alle drie de typen apps de App Service runtime delen, kunt u een website hosten, mobiele clients ondersteunen en uw Api's beschikbaar stellen in azure, allemaal vanuit hetzelfde project of dezelfde oplossing. Zie [Wat is Azure web apps](../../app-service/overview.md)? voor meer informatie over app service.

App Service is in het DevOps ontworpen met het oog. Het ondersteunt diverse hulpprogram ma's voor het publiceren en doorlopende integratie-implementaties, waaronder GitHub-webhooks, Jenkins, Azure DevOps, TeamCity en anderen.

U kunt uw bestaande toepassingen migreren naar App Service met behulp van het [hulp programma online migratie](https://www.migratetoazure.net/).

> **Wanneer gebruiken**: Gebruik App Service wanneer u bestaande webtoepassingen naar Azure migreert en wanneer u een volledig beheerd hosting platform nodig hebt voor uw web-apps. U kunt App Service ook gebruiken als u mobiele clients wilt ondersteunen of REST-Api's met uw app wilt weer geven.
> 
> **Aan de slag**: App Service maakt het eenvoudig om uw eerste [Web-app](../../app-service/app-service-web-get-started-dotnet.md), [mobiele app](../../app-service-mobile/app-service-mobile-ios-get-started.md)of [API-app](../../app-service/app-service-web-tutorial-rest-api.md)te maken en te implementeren.
> 
> **Probeer het nu**: Met App Service kunt u een app met korte levens duur inrichten om het platform te proberen zonder dat u zich hoeft aan te melden voor een Azure-account. Probeer het platform en [Maak uw Azure app service-app](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

Als IaaS-provider (Infrastructure as a Service) kunt u met Azure uw toepassing implementeren naar of migreren naar virtuele Windows-of Linux-machines. In combi natie met Azure Virtual Network ondersteunt Azure Virtual Machines de implementatie van Windows-of Linux-Vm's naar Azure. Met Vm's hebt u de volledige controle over de configuratie van de computer. Wanneer u Vm's gebruikt, bent u verantwoordelijk voor alle patches voor de server software-installatie, configuratie, onderhoud en besturings systemen.

Vanwege het beheer niveau dat u met Vm's hebt, kunt u een groot aantal server werkbelastingen uitvoeren op Azure die niet in een PaaS-model passen. Tot deze werk belastingen behoren database servers, Windows Server Active Directory en micro soft share point. Zie de Virtual Machines-documentatie voor [Linux](/azure/virtual-machines/linux/) of [Windows](/azure/virtual-machines/windows/)voor meer informatie.

> **Wanneer gebruiken**: Gebruik Virtual Machines als u volledige controle over uw toepassings infrastructuur wilt of de werk belasting van on-premises toepassingen naar Azure wilt migreren zonder dat u wijzigingen hoeft aan te brengen.
> 
> **Aan de slag**: Maak een virtuele [Linux-machine](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) of een Windows- [VM](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) op basis van de Azure Portal.

#### <a name="azure-functions-serverless"></a>Azure Functions (serverloos)

In plaats van u zorgen te maken over het bouwen en beheren van een hele toepassing of de infra structuur om uw code uit te voeren. Wat gebeurt er als u gewoon uw code schrijft en deze uitvoert als reactie op gebeurtenissen of volgens een planning?  [Azure functions](../../azure-functions/functions-overview.md) is een ' serverloze ' aanbieding waarmee u alleen de code kunt schrijven die u nodig hebt. Met functions wordt uitvoering van code geactiveerd door HTTP-aanvragen, webhooks, Cloud service gebeurtenissen of volgens een planning. U kunt code in uw door u gewenste programmeer taal gebruiken, zoals\#C,\#F, node. js, python of php. Met facturering op basis van verbruik betaalt u alleen voor de tijd dat uw code wordt uitgevoerd en Azure wordt geschaald als dat nodig is.

> **Wanneer gebruiken**: Gebruik Azure Functions wanneer u code hebt die wordt geactiveerd door andere Azure-Services, op webgebaseerde gebeurtenissen of op basis van een schema. U kunt ook functies gebruiken wanneer u de overhead van een volledig gehost project niet nodig hebt of wanneer u alleen wilt betalen voor de tijd dat uw code wordt uitgevoerd. Zie [Azure functions-overzicht](../../azure-functions/functions-overview.md)voor meer informatie.
> 
> **Aan de slag**: Volg de zelf studie functions Quick Start om [uw eerste functie te maken](../../azure-functions/functions-create-first-azure-function.md) vanuit de portal.
> 
> **Probeer het nu**: Met Azure Functions kunt u uw code uitvoeren zonder dat u zich hoeft aan te melden voor een Azure-account. Probeer het nu op en [Maak uw eerste Azure-functie](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee schaal bare en betrouw bare micro services eenvoudig kunnen worden gebouwd, verpakt, geïmplementeerd en beheerd. Het biedt ook uitgebreide mogelijkheden voor toepassings beheer voor het inrichten, implementeren, bewaken, bijwerken en verwijderen van geïmplementeerde toepassingen. Apps, die worden uitgevoerd op een gedeelde groep machines, kunnen klein worden gestart en zo nodig worden geschaald naar honderden of duizenden machines.

Service Fabric ondersteunt WebAPI met open web interface voor .NET (OWIN) en ASP.NET Core. Het biedt Sdk's voor het bouwen van services in Linux in zowel .NET Core als Java. Raadpleeg de [service Fabric-documentatie](https://docs.microsoft.com/azure/service-fabric/)voor meer informatie over service Fabric.

> **Wanneer gebruiken:** Service Fabric is een goede keuze bij het maken van een toepassing of het herschrijven van een bestaande toepassing voor het gebruik van een micro service-architectuur. Gebruik Service Fabric wanneer u meer controle nodig hebt over, of rechtstreekse toegang tot, de onderliggende infra structuur.
> 
> **Aan de slag:** [Maak uw eerste Azure service Fabric-toepassing](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Verbeter uw toepassingen met Azure-Services

Naast de hosting van toepassingen biedt Azure service aanbiedingen die de functionaliteit, ontwikkeling en het onderhoud van uw toepassingen, zowel in de Cloud als on-premises, kunnen verbeteren.

#### <a name="hosted-storage-and-data-access"></a>Gehoste opslag en gegevens toegang

In de meeste toepassingen moeten gegevens worden opgeslagen, dus u kunt een of meer van de volgende opslag-en gegevens Services overwegen, ongeacht de manier waarop u uw toepassing in azure wilt hosten.

- **Azure Cosmos DB**: Een wereld wijd gedistribueerde, multi-model database service waarmee u de door Voer en opslag voor een wille keurig aantal geografische regio's in een uitgebreide SLA kunt schalen. 
  > **Wanneer gebruiken:** Wanneer uw toepassing document-, tabel-of grafiek databases, inclusief MongoDB-data bases, met meerdere duidelijk gedefinieerde consistentie modellen nodig heeft. 
  > 
  > **Aan de slag**: [Een Azure Cosmos db web-app bouwen](../../cosmos-db/create-sql-api-dotnet.md). Als u een MongoDB-ontwikkelaar bent, raadpleegt u [een MongoDb-web-app bouwen met Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Azure Storage**: Biedt duurzame, Maxi maal beschik bare opslag voor blobs, wacht rijen, bestanden en andere soorten niet-relationele gegevens. Storage biedt de opslag basis voor Vm's.

  > **Wanneer gebruiken**: Als uw app niet-relationele gegevens opslaat, zoals sleutel-waardeparen (tabellen), blobs, bestands shares of berichten (wacht rijen).
  > 
  > **Aan de slag**: Kies uit een van deze typen opslag: [blobs](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabellen](../../cosmos-db/table-storage-how-to-use-dotnet.md), [wacht rijen](../../storage/queues/storage-dotnet-how-to-use-queues.md)of [bestanden](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: Een op Azure gebaseerde versie van de Microsoft SQL Server-engine voor het opslaan van relationele tabel gegevens in de Cloud. SQL Database biedt voorspel bare prestaties, schaal baarheid zonder uitval tijd, bedrijfs continuïteit en gegevens beveiliging.

  > **Wanneer gebruiken**: Als voor uw toepassing gegevens opslag is vereist met referentiële integriteit, transactionele ondersteuning en ondersteuning voor TSQL-query's.
  > 
  > **Aan de slag**: [Maak in een paar minuten een SQL database met behulp van de Azure Portal](../../sql-database/sql-database-get-started.md).


U kunt [Azure Data Factory](../../data-factory/introduction.md) gebruiken om bestaande on-premises gegevens naar Azure te verplaatsen. Als u geen gegevens naar de cloud kunt verplaatsen, kunt u met [hybride verbindingen](../../biztalk-services/integration-hybrid-connection-overview.md) in BizTalk Services uw app service gehoste app verbinden met on-premises resources. U kunt ook verbinding maken met Azure-gegevens-en-opslag Services vanuit uw on-premises toepassingen.

#### <a name="docker-support"></a>Docker-ondersteuning

Met docker-containers, een vorm van OS-virtualisatie, kunt u toepassingen op een efficiëntere en voorspel bare manier implementeren. Een container toepassing werkt op dezelfde manier als bij uw ontwikkel-en test systemen. U kunt containers beheren met behulp van standaard docker-hulpprogram ma's. U kunt uw bestaande vaardig heden en populaire open-source-hulpprogram ma's gebruiken voor het implementeren en beheren van op containers gebaseerde toepassingen op Azure.

Azure biedt verschillende manieren om containers in uw toepassingen te gebruiken.

- **Azure docker VM-extensie**: Hiermee kunt u uw virtuele machine configureren met docker-hulpprogram ma's om als docker-host te fungeren.

  > **Wanneer gebruiken**: Wanneer u een consistente container implementatie wilt genereren voor uw toepassingen op een virtuele machine of als u [docker opstellen](https://docs.docker.com/compose/overview/)wilt gebruiken.
  > 
  > **Aan de slag**: [Maak een docker-omgeving in azure met behulp van de docker VM-extensie](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure container service**: Hiermee kunt u een cluster van virtuele machines maken, configureren en beheren die vooraf zijn geconfigureerd voor het uitvoeren van toepassingen met containers. Zie [Azure container service Introduction](../../container-service/container-service-intro.md)(Engelstalig) voor meer informatie over de container service.

  > **Wanneer gebruiken**: Wanneer u productie klare, schaal bare omgevingen wilt bouwen die extra plannings-en beheer Programma's bieden of wanneer u een docker Swarm-cluster implementeert.
  > 
  > **Aan de slag**: [Implementeer een container service-cluster](../../container-service/dcos-swarm/container-service-deployment.md).

- **Docker-machine**: Hiermee kunt u een docker-engine op virtuele hosts installeren en beheren met behulp van docker-machine opdrachten.

  >**Wanneer gebruiken**: Wanneer u een app snel wilt prototypen door een afzonderlijke docker-host te maken.

- **Aangepaste docker-installatie kopie voor app service**: Met kunt u docker-containers uit een container register of een klant container gebruiken wanneer u een web-app op Linux implementeert.

  > **Wanneer gebruiken**: Bij het implementeren van een web-app op Linux naar een docker-installatie kopie.
  > 
  > **Aan de slag**: [Gebruik een aangepaste docker-installatie kopie voor app service op Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

Het is essentieel dat u niet alleen weet wie uw toepassingen gebruikt, maar ook om onbevoegde toegang tot uw resources te voor komen. Azure biedt verschillende manieren om uw app-clients te verifiëren.

- **Azure Active Directory (Azure AD)** : De micro soft multi tenant-service voor identiteits-en toegangs beheer op basis van de Cloud. U kunt eenmalige aanmelding (SSO) toevoegen aan uw toepassingen door te integreren met Azure AD. U kunt de eigenschappen van de Directory openen door de Azure AD-Graph API rechtstreeks of via de Microsoft Graph-API te gebruiken. U kunt integreren met Azure AD-ondersteuning voor het OAuth 2.0-autorisatie raamwerk en open-ID-verbinding door gebruik te maken van systeem eigen HTTP/REST-eind punten en de Azure AD-verificatie bibliotheken met meerdere platforms.

  > **Wanneer gebruiken**: Als u een SSO-ervaring wilt bieden, kunt u werken met gegevens op basis van een grafiek of gebruikers op basis van een domein verifiëren.
  > 
  > **Aan de slag**: Raadpleeg de [Azure Active Directory hand leiding voor ontwikkel aars](../../active-directory/develop/v1-overview.md)voor meer informatie.

- **App service-verificatie**: Wanneer u App Service kiest voor het hosten van uw app, krijgt u ook ingebouwde verificatie ondersteuning voor Azure AD, samen met sociale id-providers, zoals Facebook, Google, micro soft en Twitter.

  > **Wanneer gebruiken**: Als u verificatie in een App Service-app wilt inschakelen met behulp van Azure AD, sociale id-providers of beide.
  > 
  > **Aan de slag**: Zie [verificatie en autorisatie in azure app service](../../app-service/overview-authentication-authorization.md)voor meer informatie over verificatie in app service.

Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](../../security/fundamentals/best-practices-and-patterns.md)voor meer informatie over aanbevolen beveiligings procedures in Azure.

### <a name="monitoring"></a>Bewaking

Als uw toepassing in azure actief is, moet u de prestaties kunnen bewaken, op problemen letten en zien hoe klanten uw app gebruiken. Azure biedt verschillende controle opties.

-   **Application Insights van Visual Studio**: Een door Azure gehoste uitbreid bare analyse service die met Visual Studio kan worden geïntegreerd om uw Live webtoepassingen te controleren. Het biedt u de gegevens die u nodig hebt om de prestaties en bruikbaarheid van uw apps voortdurend te verbeteren, of ze nu worden gehost op Azure of niet.

    >**Aan de slag**: Volg de [Application Insights zelf studie](../../azure-monitor/app/app-insights-overview.md).

-   **Azure monitor**: Een service waarmee u de metrische gegevens en logboeken die door uw Azure-infra structuur en-resources worden gegenereerd, kunt visualiseren, opvragen, routeren, archiveren en verwerken. Monitor biedt de gegevens weergaven die u in de Azure Portal ziet en is één bron voor het bewaken van Azure-resources.
 
    >**Aan de slag**: [Aan de slag met Azure monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integratie met DevOps

Of het nu gaat om Vm's inrichten of het publiceren van uw web-apps met continue integratie, Azure integreert met de meeste populaire DevOps-hulpprogram ma's. Met ondersteuning voor hulpprogram ma's zoals Jenkins, GitHub, puppet, chef, TeamCity, Ansible, Azure DevOps en anderen kunt u werken met de hulpprogram ma's die u al hebt en uw bestaande ervaring maximaliseren.

> **Probeer het nu:** [Probeer verschillende DevOps-integraties uit](https://azure.microsoft.com/try/devops/).
> 
> **Aan de slag**: Als u DevOps-opties voor een App Service-app wilt weer geven, raadpleegt [u continue implementatie naar Azure app service](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Azure-regio's

Azure is een wereld wijd Cloud platform dat algemeen beschikbaar is in veel regio's over de hele wereld. Wanneer u een service, toepassing of virtuele machine inricht in azure, wordt u gevraagd om een regio te selecteren. deze vertegenwoordigt een specifiek Data Center waarin uw toepassing wordt uitgevoerd of waar uw gegevens worden opgeslagen. Deze regio's komen overeen met specifieke locaties, die worden gepubliceerd op de pagina [Azure-regio's](https://azure.microsoft.com/regions/) .

### <a name="choose-the-best-region-for-your-application-and-data"></a>De beste regio voor uw toepassing en gegevens kiezen

Een van de voor delen van het gebruik van Azure is dat u uw toepassingen kunt implementeren op verschillende data centers over de hele wereld. De regio die u kiest, kan invloed hebben op de prestaties van uw toepassing. Het is bijvoorbeeld beter om een regio te kiezen die dichter bij de meeste klanten ligt om de latentie in netwerk aanvragen te verminderen. Mogelijk wilt u ook uw regio selecteren om te voldoen aan de wettelijke vereisten voor het distribueren van uw app in bepaalde landen/regio's. Het is altijd een best practice om toepassings gegevens op te slaan in hetzelfde Data Center of in een Data Center zo dicht mogelijk bij het Data Center dat als host fungeert voor uw toepassing.

### <a name="multi-region-apps"></a>Apps met meerdere regio's

Hoewel het niet onwaarschijnlijk is, is het niet onmogelijk dat een volledig Data Center offline gaat vanwege een gebeurtenis zoals een natuur ramp of Internet fout. Het is een best practice voor het hosten van belang rijke zakelijke toepassingen in meer dan één Data Center om maximale Beschik baarheid te bieden. Het gebruik van meerdere regio's kan de latentie voor globale gebruikers ook verminderen en biedt extra mogelijkheden voor flexibiliteit bij het bijwerken van toepassingen.

Sommige services, zoals virtuele machine en App Services, gebruiken [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) om ondersteuning voor meerdere regio's in te scha kelen met failover tussen regio's voor het ondersteunen van zakelijke toepassingen met een hoge Beschik baarheid. Zie [voor een voor beeld Azure-referentie architectuur: Voer een webtoepassing uit in meerdere](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)regio's.

>**Wanneer gebruiken**: Wanneer u zakelijke en toepassingen met een hoge Beschik baarheid hebt die profiteren van failover en replicatie.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hoe kan ik mijn toepassingen en projecten beheren?

Azure biedt een uitgebreide set ervaringen waarmee u uw Azure-resources,-toepassingen en-projecten kunt maken en beheren, zowel programmatisch als in de [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Opdracht regel interfaces en Power shell

Azure biedt twee manieren om uw toepassingen en services te beheren vanaf de opdracht regel met behulp van bash, Terminal, de opdracht prompt of het opdracht regel programma van Choice. Normaal gesp roken kunt u dezelfde taken uitvoeren vanaf de opdracht regel als in de Azure Portal, zoals het maken en configureren van virtuele machines, virtuele netwerken, Web-apps en andere services.

-   [Azure-opdracht regel interface (CLI)](../../xplat-cli-install.md): Met kunt u verbinding maken met een Azure-abonnement en verschillende taken Program meren op basis van Azure-resources vanaf de opdracht regel.

-   [Azure PowerShell](../../powershell-install-configure.md): Voorziet in een set modules met cmdlets waarmee u Azure-resources kunt beheren met behulp van Windows Power shell.

### <a name="azure-portal"></a>Azure Portal

De Azure Portal is een webtoepassing die u kunt gebruiken voor het maken, beheren en verwijderen van Azure-resources en-services. Het Azure Portal bevindt <https://portal.azure.com>zich op. Het bevat een aanpasbaar dash board, hulpprogram ma's voor het beheer van Azure-resources en toegang tot abonnements instellingen en facturerings gegevens. Zie [Azure Portal-overzicht](../../azure-portal-overview.md)voor meer informatie.

### <a name="rest-apis"></a>REST-API’s

Azure is gebouwd op een set REST-Api's die ondersteuning bieden voor de Azure Portal-gebruikers interface. De meeste van deze REST Api's worden ook ondersteund om uw Azure-resources en-toepassingen programmatisch in te richten en te beheren vanaf elk apparaat met een Internet verbinding. Zie de [Naslag informatie voor Azure rest SDK](https://docs.microsoft.com/rest/api/)voor de volledige set rest API documentatie.

### <a name="apis"></a>API's

Naast REST-Api's kunt u met veel Azure-Services ook programmatisch resources van uw toepassingen beheren met platformspecifieke Azure-Sdk's, waaronder Sdk's voor de volgende ontwikkel platforms:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Services als [Mobile apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) en [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) sdk's aan de client zijde om u toegang te geven tot services van web-en mobiele client-apps.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Als u uw app in azure uitvoert, werkt dit waarschijnlijk met meerdere Azure-Services, die allemaal dezelfde levens cyclus volgen en kunnen worden beschouwd als een logische eenheid. Een web-app kan bijvoorbeeld Web Apps, SQL Database, opslag, Azure cache voor redis en Azure Content Delivery Network Services gebruiken. Met [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) kunt u als groep werken met de resources in uw toepassing. U kunt alle resources implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking.

Naast het logisch groeperen en beheren van gerelateerde resources bevat Azure Resource Manager implementatie mogelijkheden waarmee u de implementatie en configuratie van gerelateerde resources kunt aanpassen. Met Resource Manager kunt u bijvoorbeeld een toepassing implementeren en configureren die bestaat uit meerdere virtuele machines, een load balancer en een Azure SQL database als één eenheid.

U kunt deze implementaties ontwikkelen met behulp van een Azure Resource Manager sjabloon. Dit is een document met JSON-indeling. Met sjablonen kunt u een implementatie definiëren en uw toepassingen beheren door gebruik te maken van declaratieve sjablonen in plaats van scripts. Uw sjablonen kunnen worden gebruikt voor verschillende omgevingen, zoals testen, faseren en productie. Met behulp van sjablonen kunt u bijvoorbeeld een knop toevoegen aan een GitHub-opslag plaats die de code in de opslag plaats implementeert met een set van Azure-Services met één klik.

> **Wanneer gebruiken**: Gebruik Resource Manager-sjablonen als u een implementatie op basis van een sjabloon wilt voor uw app die u programmatisch kunt beheren met behulp van REST Api's, de Azure CLI en Azure PowerShell.
> 
> **Aan de slag**: Zie [Azure Resource Manager sjablonen ontwerpen](../../resource-group-authoring-templates.md)om aan de slag te gaan met sjablonen.

## <a name="understanding-accounts-subscriptions-and-billing"></a>Wat zijn accounts, abonnementen en facturering??

Als ontwikkel aars willen we meteen beginnen met de code en proberen zo snel mogelijk aan de slag te gaan met het uitvoeren van onze toepassingen. We willen er zeker van zijn dat u zo snel mogelijk aan de slag kunt gaan met Azure. Om het eenvoudig te maken, biedt Azure een [gratis proef versie](https://azure.microsoft.com/free/). Sommige services hebben zelfs de functionaliteit gratis uitproberen, zoals [Azure app service](https://tryappservice.azure.com/), waarvoor u zelfs geen account hoeft te maken. Net als bij het voor komen van het coderen en implementeren van uw toepassing naar Azure, is het ook belang rijk om te begrijpen hoe Azure werkt vanuit een oogpunt van gebruikers accounts, abonnementen en facturering.

### <a name="what-is-an-azure-account"></a>Wat is een Azure-account?

U moet een Azure-account hebben om een Azure-abonnement te kunnen maken of gebruiken. Een Azure-account is simpelweg een identiteit in azure AD of in een directory, zoals een werk-of school organisatie, die wordt vertrouwd door Azure AD. Als u geen deel uitmaakt van een dergelijke organisatie, kunt u altijd een abonnement maken met behulp van uw micro soft-account, dat wordt vertrouwd door Azure AD. Zie [uw on-premises identiteiten integreren met Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md)voor meer informatie over het integreren van on-premises Windows Server-Active Directory met Azure AD.

Voor elk Azure-abonnement is er een vertrouwensrelatie met een Azure AD-exemplaar. Dit betekent dat er op die directory wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde directory vertrouwen, maar een abonnement vertrouwt slechts één directory. Zie [hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie.

Naast het definiëren van afzonderlijke id's van een Azure-account, ook wel *gebruikers*genoemd, kunt u ook *groepen* definiëren in azure AD. Het maken van gebruikers groepen is een goede manier om de toegang tot resources in een abonnement te beheren met behulp van op rollen gebaseerd toegangs beheer (RBAC). Zie [een groep maken in azure Active Directory preview](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van groepen. U kunt ook groepen maken en beheren met [behulp van Power shell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Uw abonnementen beheren

Een abonnement is een logische groepering van Azure-Services die zijn gekoppeld aan een Azure-account. Eén Azure-account kan meerdere abonnementen bevatten. Facturering voor Azure-Services wordt uitgevoerd op basis van per abonnement. Zie [Microsoft Azure Details](https://azure.microsoft.com/support/legal/offer-details/)van de aanbieding voor een lijst met de beschik bare abonnements aanbiedingen op type. Azure-abonnementen hebben een account beheerder die volledige controle heeft over het abonnement en een service beheerder die de controle heeft over alle services in het abonnement. Zie [beheerders van Azure-abonnementen toevoegen of wijzigen](../../billing/billing-add-change-azure-subscription-administrator.md)voor meer informatie over klassieke abonnements beheerders. Naast beheerders kunnen individuele accounts een gedetailleerde controle krijgen over Azure-resources met behulp van [op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Resourcegroepen

Wanneer u nieuwe Azure-Services inricht, doet u dat in een bepaald abonnement. Afzonderlijke Azure-Services, die ook wel resources worden genoemd, worden gemaakt in de context van een resource groep. Resource groepen maken het gemakkelijker om de resources van uw toepassing te implementeren en beheren. Een resource groep moet alle resources bevatten voor uw toepassing die u als een eenheid wilt gebruiken. U kunt resources verplaatsen tussen resource groepen en zelfs naar verschillende abonnementen. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../resource-group-move-resources.md)voor meer informatie over het verplaatsen van resources.

De Azure Resource Explorer is een uitstekend hulp programma voor het visualiseren van de resources die u al hebt gemaakt in uw abonnement. Zie [Azure resource Explorer gebruiken om resources te bekijken en te wijzigen](../../resource-manager-resource-explorer.md)voor meer informatie.

#### <a name="grant-access-to-resources"></a>Toegang verlenen tot resources

Wanneer u toegang verleent tot Azure-resources, is het altijd een best practice om gebruikers de minste bevoegdheid te geven die nodig is om een bepaalde taak uit te voeren.

- **Op rollen gebaseerd toegangs beheer (RBAC)** : In azure kunt u toegang verlenen aan gebruikers accounts (principals) bij een opgegeven Scope: abonnement, resource groep of afzonderlijke resources. Met RBAC kunt u een set resources implementeren in een resource groep en machtigingen verlenen aan een specifieke gebruiker of groep. Daarnaast kunt u de toegang beperken tot de resources die deel uitmaken van de doel resource groep. U kunt ook toegang verlenen tot één resource, zoals een virtuele machine of een virtueel netwerk. Als u toegang wilt verlenen, wijst u een rol toe aan de gebruiker, groep of Service-Principal. Er zijn veel vooraf gedefinieerde rollen, en u kunt ook uw eigen aangepaste rollen definiëren. Zie [Wat is op rollen gebaseerd toegangs beheer (RBAC)?](../../role-based-access-control/overview.md)voor meer informatie.

  > **Wanneer gebruiken**: Wanneer u een nauw keurig toegangs beheer voor gebruikers en groepen nodig hebt of als u een gebruiker een eigenaar van een abonnement wilt maken.
  > 
  > **Aan de slag**: Zie [toegang beheren met RBAC en de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

- **Service-Principal-objecten**: Naast het verlenen van toegang tot gebruikers-principals en groepen, kunt u dezelfde toegang tot een Service-Principal toekennen.

  > **Wanneer gebruiken**: Wanneer u programmatisch Azure-resources beheert of toegang verleent voor toepassingen. Zie [Active Directory-toepassing en service-principal maken](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

#### <a name="tags"></a>Labels

Met Azure Resource Manager kunt u aangepaste labels toewijzen aan afzonderlijke resources. Tags, die sleutel-waardeparen zijn, kunnen handig zijn wanneer u resources voor facturering of bewaking wilt indelen. Tags bieden u een manier om resources in meerdere resource groepen bij te houden. U kunt labels toewijzen in de portal, in de Azure Resource Manager sjabloon of via een programma, met behulp van de REST API, de Azure CLI of Power shell. U kunt meerdere labels toewijzen aan elke resource. Zie [Tags gebruiken om uw Azure-resources te organiseren](../../resource-group-using-tags.md)voor meer informatie.

### <a name="billing"></a>Billing

In de overstap van on-premises computing naar in de Cloud gehoste services zijn het bijhouden en ramen van service gebruik en gerelateerde kosten aanzienlijk van belang. Het is belang rijk om te kunnen schatten welke nieuwe resources maandelijks moeten worden uitgevoerd. U moet ook kunnen bepalen hoe de facturering zoekt naar een bepaalde maand op basis van de huidige uitgaven.

#### <a name="get-resource-usage-data"></a>Gegevens over resource gebruik ophalen

Azure biedt een set facturering REST-Api's waarmee u toegang krijgt tot Resource verbruik en meta gegevens gegevens voor Azure-abonnementen. Deze Billing-API's biedt u de mogelijkheid om de kosten van Azure beter te voors pellen en te beheren. U kunt uitgaven bijhouden en analyseren in stappen van elk uur, bestedings waarschuwingen maken en toekomstige facturering voors pellen op basis van huidige gebruiks trends.

>**Aan de slag**: Zie [overzicht van Azure billing and RateCard-api's](../../billing-usage-rate-card-overview.md)voor meer informatie over het gebruik van de Billing-API's.

#### <a name="predict-future-costs"></a>Toekomstige kosten voors pellen

Hoewel het lastig is om de kosten van tevoren te schatten, heeft Azure een [prijs calculator](https://azure.microsoft.com/pricing/calculator/) die u kunt gebruiken bij het schatten van de kosten van geïmplementeerde resources. U kunt ook de facturerings Blade in de portal en de REST-Api's van de facturering gebruiken om toekomstige kosten te schatten, op basis van het huidige verbruik.

>**Aan de slag**: Zie [overzicht van Azure-facturerings gebruik en RateCard-api's](../../billing-usage-rate-card-overview.md).
