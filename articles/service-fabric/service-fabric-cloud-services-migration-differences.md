---
title: Verschillen tussen Cloud Services en Service Fabric
description: Een conceptueel overzicht voor het migreren van applicaties van Cloud Services naar Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 283ad2c63bb59771dab7881522e737f773ab1705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463380"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Meer informatie over de verschillen tussen Cloud Services en Service Fabric voordat u toepassingen migreert.
Microsoft Azure Service Fabric is het cloudapplicatieplatform van de volgende generatie voor zeer schaalbare, zeer betrouwbare gedistribueerde toepassingen. Het introduceert veel nieuwe functies voor het verpakken, implementeren, upgraden en beheren van gedistribueerde cloudtoepassingen. 

Dit is een inleidende handleiding voor het migreren van applicaties van Cloud Services naar Service Fabric. Het richt zich voornamelijk op architecturale en ontwerpverschillen tussen Cloud Services en Service Fabric.

## <a name="applications-and-infrastructure"></a>Toepassingen en infrastructuur
Een fundamenteel verschil tussen Cloud Services en Service Fabric is de relatie tussen VM's, workloads en applicaties. Een werkbelasting wordt hier gedefinieerd als de code die u schrijft om een specifieke taak uit te voeren of een service te verlenen.

* **Cloud Services gaat over het implementeren van applicaties als VM's.** De code die u schrijft, is nauw gekoppeld aan een VM-instantie, zoals een web- of werknemersrol. Als u een workload in Cloud Services wilt implementeren, moet u een of meer VM-exemplaren implementeren die de werkbelasting uitvoeren. Er is geen scheiding van toepassingen en VM's, en dus is er geen formele definitie van een toepassing. Een toepassing kan worden gezien als een set web- of werknemersrolexemplaren binnen een implementatie van Cloud Services of als een volledige implementatie van Cloud Services. In dit voorbeeld wordt een toepassing weergegeven als een set rolinstanties.

![Cloud Services-toepassingen en -topologie][1]

* **Service Fabric gaat over het implementeren van applicaties naar bestaande VM's of machines met Service Fabric op Windows of Linux.** De services die u schrijft, zijn volledig losgekoppeld van de onderliggende infrastructuur, die wordt geabstraheerd door het Service Fabric-toepassingsplatform, zodat een toepassing naar meerdere omgevingen kan worden geïmplementeerd. Een werkbelasting in Service Fabric wordt een 'service' genoemd en een of meer services worden gegroepeerd in een formeel gedefinieerde toepassing die wordt uitgevoerd op het Service Fabric-toepassingsplatform. Meerdere toepassingen kunnen worden geïmplementeerd in één Cluster van ServiceFabric.

![Service Fabric-toepassingen en topologie][2]

Service Fabric zelf is een applicatieplatformlaag die wordt uitgevoerd op Windows of Linux, terwijl Cloud Services een systeem is voor het implementeren van door Azure beheerde VM's met workloads.
Het Service Fabric applicatiemodel heeft een aantal voordelen:

* Snelle implementatietijden. Het maken van VM-exemplaren kan tijdrovend zijn. In Service Fabric worden VM's slechts één keer geïmplementeerd om een cluster te vormen dat het Service Fabric-toepassingsplatform host. Vanaf dat moment kunnen toepassingspakketten zeer snel naar het cluster worden geïmplementeerd.
* Hosting met hoge dichtheid. In Cloud Services host een WORKER Role VM één workload. In Service Fabric staan toepassingen los van de VM's die ze uitvoeren, wat betekent dat u een groot aantal toepassingen implementeren in een klein aantal VM's, wat de totale kosten voor grotere implementaties kan verlagen.
* Het Service Fabric-platform kan overal worden uitgevoerd met Windows Server- of Linux-machines, of het nu Azure of on-premises is. Het platform biedt een abstractielaag over de onderliggende infrastructuur, zodat uw toepassing op verschillende omgevingen kan worden uitgevoerd. 
* Gedistribueerd applicatiebeheer. Service Fabric is een platform dat niet alleen gedistribueerde applicaties host, maar ook helpt bij het beheren van hun levenscyclus onafhankelijk van de hosting VM of machine levenscyclus.

## <a name="application-architecture"></a>Toepassingsarchitectuur
De architectuur van een Cloud Services-toepassing bevat meestal tal van externe serviceafhankelijkheden, zoals Service Bus, Azure Table en Blob Storage, SQL, Redis en anderen om de status en gegevens van een toepassing en communicatie tussen web en communicatie te beheren Functie van werknemer in een implementatie van Cloud Services. Een voorbeeld van een volledige Cloud Services-toepassing ziet er als volgt uit:  

![Cloud Services-architectuur][9]

Service Fabric-toepassingen kunnen er ook voor kiezen om dezelfde externe services in een volledige toepassing te gebruiken. Met behulp van dit voorbeeld Cloud Services-architectuur is het eenvoudigste migratiepad van Cloud Services naar Service Fabric om alleen de cloudservices-implementatie te vervangen door een Service Fabric-toepassing, zodat de algehele architectuur gelijk blijft. De web- en werknemersrollen kunnen worden geport naar servicefabric-statusloze services met minimale codewijzigingen.

![Service Fabric-architectuur na eenvoudige migratie][10]

In dit stadium moet het systeem hetzelfde blijven werken als voorheen. Als u profiteert van de stateful functies van Service Fabric, kunnen externe statusopslag, indien van toepassing, worden geïnternaliseerd als stateful services. Dit is meer betrokken dan een eenvoudige migratie van web- en werknemersrollen naar servicefabric-statusloze services, omdat hiervoor aangepaste services moeten worden geschreven die vergelijkbare functionaliteit bieden aan uw toepassing als de externe services eerder deden. De voordelen hiervan zijn onder meer: 

* Externe afhankelijkheden verwijderen 
* Het verenigen van de implementatie-, beheer- en upgrademodellen. 

Een voorbeeld van de architectuur om deze services te internaliseren, kan er als volgt uitzien:

![Service Fabric-architectuur na volledige migratie][11]

## <a name="communication-and-workflow"></a>Communicatie en workflow
De meeste Cloud Service-toepassingen bestaan uit meer dan één laag. Op dezelfde manier bestaat een Service Fabric-toepassing uit meer dan één service (meestal veel services). Twee gemeenschappelijke communicatiemodellen zijn directe communicatie en via een externe duurzame opslag.

### <a name="direct-communication"></a>Directe communicatie
Met directe communicatie kunnen lagen rechtstreeks communiceren via eindpunt dat door elke laag wordt blootgesteld. In stateloze omgevingen zoals Cloud Services betekent dit het selecteren van een instantie van een VM-rol, willekeurig of round-robin om de belasting in evenwicht te brengen en rechtstreeks verbinding te maken met het eindpunt.

![Directe communicatie van Cloud Services][5]

 Directe communicatie is een gemeenschappelijk communicatiemodel in Service Fabric. Het belangrijkste verschil tussen Service Fabric en Cloud Services is dat u in Cloud Services verbinding maakt met een VM, terwijl u in Service Fabric verbinding maakt met een service. Dit is een belangrijk onderscheid om een paar redenen:

* Services in Service Fabric zijn niet gebonden aan de VM's die ze hosten; services kunnen zich verplaatsen in het cluster en in feite wordt verwacht dat ze zich verplaatsen om verschillende redenen: resourcebalancing, failover, applicatie- en infrastructuurupgrades en plaatsings- of belastingsbeperkingen. Dit betekent dat het adres van een service-instantie op elk gewenst moment kan worden gewijzigd. 
* Een VM in Service Fabric kan meerdere services hosten, elk met unieke eindpunten.

Service Fabric biedt een servicedetectiemechanisme, de naamgevingsservice, dat kan worden gebruikt om eindpuntadressen van services op te lossen. 

![Service Fabric directe communicatie][6]

### <a name="queues"></a>Wachtrijen
Een gemeenschappelijk communicatiemechanisme tussen lagen in stateloze omgevingen zoals Cloud Services is het gebruik van een externe opslagwachtrij om werktaken van de ene laag naar de andere duurzaam op te slaan. Een veelvoorkomend scenario is een weblaag die taken verzendt naar een Azure Queue of Service Bus waar exemplaren van werknemersrollen de wachtrij kunnen worden verwijderd en de taken kunnen verwerken.

![Communicatie in de wachtrij van Cloud Services][7]

Hetzelfde communicatiemodel kan worden gebruikt in Service Fabric. Dit kan handig zijn bij het migreren van een bestaande Cloud Services-toepassing naar Service Fabric. 

![Service Fabric directe communicatie][8]

## <a name="parity"></a>Parity
[Cloud Services is vergelijkbaar met Service Fabric in mate van controle versus gebruiksgemak, maar het is nu een verouderde service en Service Fabric wordt aanbevolen voor nieuwe ontwikkeling;](https://docs.microsoft.com/azure/app-service/overview-compare) het volgende is een API-vergelijking:


| **Cloud Service API** | **API voor servicefabric** | **Opmerkingen** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId of . NodeName | ID is een eigenschap van de NodeName |
| RoleInstance.GetFaultDomein | FabricClient.QueryManager.GetNodeList | Filteren op NodeName en FD-eigenschap gebruiken |
| RoleInstance.GetUpgradeDomein | FabricClient.QueryManager.GetNodeList | Filteren op NodeName en de eigenschap Upgrade gebruiken |
| RoleInstance.GetInstanceEindpunten | FabricRuntime.GetActivationContext of Naamgeving (ResolveService) | CodePackageActivationContext die zowel door FabricRuntime.GetActivationContext als binnen de replica's wordt geleverd via ServiceInitializationParameters.CodePackageActivationContext verstrekt tijdens . Initialiseren |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Als u hetzelfde soort filtering per type wilt doen, u de lijst met knooppunttypen uit het clustermanifest krijgen via FabricClient.ClusterManager.GetClusterManifest en de rol-/knooppunttypen van daaruit grijpen. |
| RoleEnvironment.GetIsAvailable | Connect-WindowsFabricCluster of maak een FabricRuntime die naar een bepaald knooppunt wordt gericht | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList of ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext of Naamgeving (ResolveService) | * |

## <a name="next-steps"></a>Volgende stappen
Het eenvoudigste migratiepad van Cloud Services naar Service Fabric is om alleen de Cloud Services-implementatie te vervangen door een Service Fabric-toepassing, waardoor de algehele architectuur van uw toepassing ongeveer hetzelfde blijft. In het volgende artikel vindt u een handleiding om een web- of werknemersrol om te zetten in een statusloze service service service van Service Fabric.

* [Eenvoudige migratie: een web- of werknemersrol converteren naar een statusloze service service van Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
