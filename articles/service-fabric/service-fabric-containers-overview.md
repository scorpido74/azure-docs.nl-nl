---
title: Overzicht van Service Fabric en containers
description: Een overzicht van Service Fabric en het gebruik van containers om microservice applicaties te implementeren. In dit artikel vindt u een overzicht van hoe containers kunnen worden gebruikt en de beschikbare mogelijkheden in Service Fabric.
ms.topic: conceptual
ms.date: 8/8/2018
ms.openlocfilehash: 884cefa3d6a60f55269afac73c40b9f6b21518f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458218"
---
# <a name="service-fabric-and-containers"></a>Service Stof en containers

## <a name="introduction"></a>Inleiding

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u gemakkelijk schaalbare en betrouwbare microservices en containers verpakt, implementeert en beheert.

Service Fabric is microsoft's [container orchestrator](service-fabric-cluster-resource-manager-introduction.md) voor het implementeren van microservices in een cluster van machines. Service Fabric profiteert van de lessen die zijn geleerd tijdens de jaren dat ze op grote schaal diensten bij Microsoft uitvoeren.

Microservices kunnen op tal van manieren worden ontwikkeld met behulp van de [Service Fabric-programmeermodellen](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), voor het implementeren van [code naar keuze](service-fabric-guest-executables-introduction.md). Of, als u gewoon containers wilt [implementeren en beheren,](service-fabric-containers-overview.md)is Service Fabric ook een goede keuze.

Service Fabric implementeert en activeert deze services standaard als processen. Processen bieden de snelste activering en het hoogste dichtheidsgebruik van de resources in een cluster. Service Fabric kan ook services implementeren in containerafbeeldingen. U services ook mengen in processen en services in containers, in dezelfde toepassing.

Als u er meteen in wilt springen en containers wilt uitproberen op Service Fabric, probeert u een snelle start, zelfstudie of voorbeeld:  

[Snelstart: Een Linux-containertoepassing implementeren in Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Snelstart: een Windows-containertoepassing implementeren in ServiceFabric](service-fabric-quickstart-containers.md)  
[Een bestaande .NET-app in een container plaatsen](service-fabric-host-app-in-a-container.md)  
[Voorbeelden van Service Fabric-containers](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Wat zijn containers

Containers lossen het probleem van het draaien van toepassingen betrouwbaar op in verschillende computeromgevingen door een onveranderlijke omgeving te bieden waar de toepassing in kan worden uitgevoerd. Containers wikkelen een toepassing en al zijn afhankelijkheden, zoals bibliotheken en configuratiebestanden, in een eigen geïsoleerde 'box' die alles bevat wat nodig is om de software in de container uit te voeren. Waar de container ook draait, de toepassing erin heeft altijd alles wat het nodig heeft om te draaien, zoals de juiste versies van de afhankelijke bibliotheken, alle configuratiebestanden en alles wat het moet uitvoeren.

Containers worden direct boven op de kernel uitgevoerd en hebben een geïsoleerd beeld van het bestandssysteem en andere bronnen. Een toepassing in een container heeft geen kennis van andere toepassingen of processen buiten de container. Elke toepassing en de looptijd, afhankelijkheden en systeembibliotheken worden uitgevoerd in een container met volledige, privétoegang tot de eigen geïsoleerde weergave van het besturingssysteem. Naast het eenvoudig maken van alle afhankelijkheden van uw toepassing die het nodig heeft om te draaien in verschillende computeromgevingen, zijn beveiliging en resourceisolatie belangrijke voordelen van het gebruik van containers met Service Fabric - die anders services in een Proces.

In vergelijking met virtuele machines hebben containers de volgende voordelen:

* **Klein:** Containers gebruiken één opslagruimte en laagversies en -updates om de efficiëntie te verhogen.
* **Snel**: Containers hoeven niet een volledig besturingssysteem op te starten, zodat ze veel sneller kunnen starten - meestal in enkele seconden.
* **Draagbaarheid:** een containerized toepassingsafbeelding kan worden geport om te worden uitgevoerd in de cloud, op locatie, in virtuele machines of rechtstreeks op fysieke machines.
* **Resourcebeheer:** een container kan de fysieke resources beperken die het op de host kan verbruiken.

### <a name="container-types-and-supported-environments"></a>Containertypen en ondersteunde omgevingen

Service Fabric ondersteunt containers op zowel Linux als Windows en ondersteunt hyper-v-isolatiemodus op Windows.

#### <a name="docker-containers-on-linux"></a>Docker containers op Linux

Docker biedt API's voor het maken en beheren van containers bovenop Linux kernel containers. Docker Hub biedt een centrale opslagplaats voor het opslaan en ophalen van containerafbeeldingen.
Zie Uw eerste Service [Fabric-containertoepassing maken op Linux voor](service-fabric-get-started-containers-linux.md)een op Linux gebaseerde zelfstudie.

#### <a name="windows-server-containers"></a>Windows Server-containers

Windows Server 2016 biedt twee verschillende typen containers die verschillen per isolatieniveau. Windows Server-containers en Docker-containers zijn vergelijkbaar omdat beide naamruimte en bestandssysteemisolatie hebben, terwijl de kernel wordt gedeeld met de host waarop ze worden uitgevoerd. Op Linux wordt deze isolatie traditioneel geleverd door cgroups en namespaces, en Windows Server-containers gedragen zich op dezelfde manier.

Windows-containers met Hyper-V-ondersteuning bieden meer isolatie en beveiliging omdat geen enkele container de kernel van het besturingssysteem deelt met een andere container of met de host. Met dit hogere niveau van beveiliging isolatie, Hyper-V enabled containers zijn gericht op potentieel vijandige, multi-tenant scenario's.
Zie [Uw eerste servicefabric-containertoepassing maken op Windows](service-fabric-get-started-containers.md)voor een op Windows gebaseerde zelfstudie.

De volgende afbeelding toont de verschillende soorten virtualisatie- en isolatieniveaus die beschikbaar zijn.
![Service Fabric-platform][Image1]

## <a name="scenarios-for-using-containers"></a>Scenario's voor het gebruik van containers

Hier volgen typische voorbeelden waarbij een container een goede keuze is:

* **IIS-lift en -shift**: U een bestaande [ASP.NET MVC-app](https://www.asp.net/mvc) in een container plaatsen in plaats van deze te migreren naar ASP.NET Core. Deze ASP.NET MVC-apps zijn afhankelijk van Internet Information Services (IIS). U deze toepassingen verpakken in containerafbeeldingen van de vooraf gemaakte IIS-afbeelding en deze implementeren met Service Fabric. Zie [Containerafbeeldingen op Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) voor informatie over Windows-containers.

* **Mix containers en Service Fabric microservices**: Gebruik een bestaande containerafbeelding voor een deel van uw toepassing. U bijvoorbeeld de [NGINX-container](https://hub.docker.com/_/nginx/) gebruiken voor de webfront-end van uw toepassing en stateful services voor de intensievere back-endberekening.

* **De impact van 'luidruchtige buren'-services verminderen:** u de resourcegovernancecapaciteit van containers gebruiken om de resources die een service op een host gebruikt, te beperken. Als services veel resources verbruiken en de prestaties van anderen beïnvloeden (zoals een langlopende, queryachtige bewerking), u overwegen deze services in containers met resourcegovernance te plaatsen.

## <a name="service-fabric-support-for-containers"></a>Service Fabric-ondersteuning voor containers

Service Fabric ondersteunt de implementatie van Docker-containers op Linux- en Windows Server-containers op Windows Server 2016, samen met ondersteuning voor de Hyper-V-isolatiemodus. 

Service Fabric biedt een [toepassingsmodel](service-fabric-application-model.md) waarin een container een toepassingshost vertegenwoordigt waarin meerdere servicereplica's worden geplaatst. Service Fabric ondersteunt ook een [uitvoerbaar scenario](service-fabric-guest-executables-introduction.md) voor gasten waarin u de ingebouwde Service Fabric-programmeermodellen niet gebruikt, maar in plaats daarvan een bestaande toepassing, geschreven met elke taal of framework, in een container verpakt. Dit scenario is de gangbare use-case voor containers.

U ook [Service Fabric-services uitvoeren in een container.](service-fabric-services-inside-containers.md) Ondersteuning voor het uitvoeren van Service Fabric-services in containers is momenteel beperkt.

Service Fabric biedt verschillende containermogelijkheden waarmee u toepassingen bouwen die zijn samengesteld uit gecontaineriseerde microservices, zoals:

* Implementatie en activering van containerafbeeldingen.
* Resourcebeheer, inclusief standaard instelling van resourcewaarden in Azure-clusters.
* Verificatie van opslagplaatsen.
* Containerpoort om poorttoewijzing te hosten.
* Container-to-container detectie en communicatie.
* Mogelijkheid om omgevingsvariabelen te configureren en in te stellen.
* Mogelijkheid om beveiligingsreferenties in te stellen op de container.
* Een keuze uit verschillende netwerkmodi voor containers.

Zie [Azure voor](https://docs.microsoft.com/azure/containers/)containers voor een uitgebreid overzicht van containerondersteuning op Azure, zoals het maken van een Kubernetes-cluster met Azure Kubernetes Service, het maken van een privéDocker-register in Azure Container Registry en meer.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de ondersteuning die Service Fabric biedt voor het uitvoeren van containers. Vervolgens zullen we voorbeelden van elk van de functies doornemen om u te laten zien hoe u ze gebruiken.

[Uw eerste Service Fabric-containertoepassing maken in Linux](service-fabric-get-started-containers-linux.md)  
[Uw eerste Service Fabric-containertoepassing maken in Windows](service-fabric-get-started-containers.md)  
[Meer informatie over Windows-containers](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png