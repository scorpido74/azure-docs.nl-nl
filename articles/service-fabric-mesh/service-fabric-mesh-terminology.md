---
title: Terminologie voor Azure Service Fabric-mesh
description: In dit artikel wordt de terminologie beschreven die wordt gebruikt door Azure Service Fabric Mesh om u te helpen een beter inzicht te krijgen in de termen die in de documentatie worden gebruikt.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 6df7a6b708bca81f1390ac879f24ea4c22c38bee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351979"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric Mesh-terminologie

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. In dit artikel wordt de terminologie beschreven die wordt gebruikt door Azure Service Fabric Mesh om u te helpen een beter inzicht te krijgen in de termen die in de documentatie worden gebruikt.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) is een open source gedistribueerd systeemplatform dat het eenvoudig maakt om schaalbare en betrouwbare microservices te verpakken, implementeren en beheren. Service Fabric is de orchestrator die Service Fabric Mesh van kracht is. Service Fabric biedt opties voor hoe u uw microservices-toepassingen bouwen en uitvoeren. U elk framework gebruiken om uw services te schrijven en te kiezen waar u de toepassing wilt uitvoeren vanuit meerdere omgevingskeuzes.

## <a name="application-and-service-concepts"></a>Toepassings- en serviceconcepten

**Service Fabric Mesh-toepassing:** Service Fabric Mesh-toepassingen worden beschreven door het [Resourcemodel](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (YAML- en JSON-bronbestanden) en kunnen worden geïmplementeerd in elke omgeving waar Service Fabric wordt uitgevoerd.

**Service Fabric Native Application:** Service Fabric Native Applications worden beschreven door het [Native Application Model](/azure/service-fabric/service-fabric-application-model) (XML-gebaseerde applicatie- en servicemanifesten).  Native applications servicefabric kunnen niet worden uitgevoerd in Service Fabric Mesh.

**Toepassing:** Een Service Fabric Mesh-toepassing is de eenheid van implementatie, versiebeheer en levensduur van een Mesh-toepassing. De levenscyclus van elke toepassingsinstantie kan onafhankelijk worden beheerd.  Toepassingen bestaan uit een of meer servicecodepakketten en -instellingen. Een toepassing wordt gedefinieerd met het RM-schema (Azure Resource Model).  Services worden beschreven als eigenschappen van de toepassingsbron in een RM-sjabloon.  Netwerken en volumes die door de toepassing worden gebruikt, worden door de toepassing verwezen.  Bij het maken van een toepassing worden de toepassing, service(s), het netwerk en het volume(en) gemodelleerd met behulp van het Service Fabric Resource Model.

**Service:** Een service in een toepassing vertegenwoordigt een microservice en voert een volledige en zelfstandige functie uit. Elke service bestaat uit één of meer codepakketten waarin alles wordt beschreven wat nodig is om de containerafbeelding uit te voeren die aan het codepakket is gekoppeld.  Het aantal servicereplica's in een toepassing kan in- en uitschalen.

**Codepakket**: Codepakketten beschrijven alles wat nodig is om de containerafbeelding uit te voeren die aan het codepakket is gekoppeld, inclusief het volgende:

* Containernaam, versie en register
* CPU- en geheugenbronnen die nodig zijn voor elke container
* Netwerkeindpunten
* Volumes die in de container moeten worden gemonteerd, verwijzend naar een afzonderlijke volumebron.

## <a name="deployment-and-application-models"></a>Implementatie- en toepassingsmodellen 

Als u uw services wilt implementeren, moet u beschrijven hoe ze moeten worden uitgevoerd. Service Fabric ondersteunt drie verschillende implementatiemodellen:

### <a name="resource-model"></a>Resourcemodel
Service Fabric Resources zijn alles wat individueel kan worden geïmplementeerd in Service Fabric; toepassingen, services, netwerken en volumes. Resources worden gedefinieerd met behulp van een JSON-bestand, dat kan worden geïmplementeerd op een clustereindpunt.  Voor Service Fabric Mesh wordt het Azure Resource Model-schema gebruikt. Een YAML-bestandsschema kan ook worden gebruikt om eenvoudiger definitiebestanden te maken. Resources kunnen overal worden geïmplementeerd waar Service Fabric wordt uitgevoerd. Het resourcemodel is de eenvoudigste manier om uw Service Fabric-toepassingen te beschrijven. De belangrijkste focus ligt op eenvoudige implementatie en beheer van containerdiensten. Lees [Inleiding tot het Service Fabric Resource Model](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)voor meer informatie.

### <a name="native-model"></a>Native model
Het native applicatiemodel biedt uw toepassingen volledige toegang op laag niveau tot Service Fabric. Toepassingen en services worden gedefinieerd als geregistreerde typen in XML-manifestbestanden.

Het native model ondersteunt het Reliable Services-framework, dat toegang biedt tot de Runtime API's van Service Fabric en API's voor clusterbeheer in C# en Java. Het native model ondersteunt ook willekeurige containers en executables.

Het native model wordt niet ondersteund in de Service Fabric Mesh-omgeving.  Zie voor meer informatie [het overzicht van het programmeermodel](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker componeren 
[Docker Compose](https://docs.docker.com/compose/) maakt deel uit van het Docker project. Service Fabric biedt beperkte ondersteuning voor het implementeren van toepassingen met behulp van het Docker Compose-model.

## <a name="environments"></a>Omgevingen

Service Fabric is een open-source platform technologie waar verschillende diensten en producten op gebaseerd zijn. Microsoft biedt de volgende opties:

 - **Service Fabric Mesh:** een volledig beheerde service voor het uitvoeren van Service Fabric-toepassingen in Microsoft Azure.
 - **Azure Service Fabric:** het azure hosted Service Fabric-clusteraanbod. Het biedt integratie tussen Service Fabric en de Azure-infrastructuur, samen met upgrade- en configuratiebeheer van Service Fabric-clusters.
 - **Service Fabric standalone:** een set installatie- en configuratietools om [Service Fabric-clusters overal](/azure/service-fabric/service-fabric-deploy-anywhere) te implementeren (on-premises of op elke cloudprovider). Niet beheerd door Azure.
 - **Service Fabric development cluster**: Biedt een lokale ontwikkelingservaring op Windows, Linux of Mac voor de ontwikkeling van Service Fabric-toepassingen.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matrix voor ondersteuning van het omgevings-, framework- en implementatiemodel
Verschillende omgevingen hebben verschillende ondersteuningsniveaus voor frameworks en implementatiemodellen. In de volgende tabel worden de ondersteunde combinaties van framework en implementatiemodellen beschreven.

| Type toepassing | Beschreven door | Azure Service Fabric Mesh | Azure Service Fabric-clusters (elk besturingssysteem)| Lokaal cluster | Zelfstandig cluster |
|---|---|---|---|---|---|
| Mesh-toepassingen voor servicefabric | Resourcemodel (YAML & JSON) | Ondersteund |Niet ondersteund | Windows- ondersteund, Linux en Mac- niet ondersteund | Windows- niet ondersteund |
|Native toepassingen voor servicefabric | Native Application Model (XML) | Niet ondersteund| Ondersteund|Ondersteund|Windows- ondersteund|

In de volgende tabel worden de verschillende toepassingsmodellen en de tooling beschreven die voor hen bestaat ten opzichte van Service Fabric.

| Type toepassing | Beschreven door | Visual Studio | Eclipse | SFCTL (SFCTL) | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Mesh-toepassingen voor servicefabric | Resourcemodel (YAML & JSON) | VS 2017 |Niet ondersteund |Niet ondersteund | Alleen ondersteund - Mesh-omgeving | Niet ondersteund|
|Native toepassingen voor servicefabric | Native Application Model (XML) | VS 2017 en VS 2015| Ondersteund|Ondersteund|Ondersteund|Ondersteund|

## <a name="next-steps"></a>Volgende stappen

Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.

Zoek antwoorden op [veelgestelde vragen](service-fabric-mesh-faq.md).
