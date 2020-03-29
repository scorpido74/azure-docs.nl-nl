---
title: Aanbevolen procedures voor Azure Service Fabric
description: Aanbevolen procedures en ontwerpoverwegingen voor het bewaken van clusters en toepassingen met Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551813"
---
# <a name="monitoring-and-diagnostics"></a>Controle en diagnose

[Monitoring en diagnostiek](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) zijn essentieel voor het ontwikkelen, testen en implementeren van workloads in elke cloudomgeving. U bijvoorbeeld bijhouden hoe uw toepassingen worden gebruikt, welke acties het Service Fabric-platform onderneemt, uw resourcegebruik met prestatiemeteritems en de algehele status van uw cluster. U deze informatie gebruiken om problemen te diagnosticeren en te corrigeren en te voorkomen dat deze zich in de toekomst voordoen.

## <a name="application-monitoring"></a>Toepassingsbewaking

Toepassingsbewaking houdt bij hoe functies en onderdelen van uw toepassing worden gebruikt. Controleer uw toepassingen om ervoor te zorgen dat problemen die van invloed zijn op uw gebruikers worden opgevangen. Applicatiebewaking is de verantwoordelijkheid van degenen die de applicatie en de services ontwikkelen, omdat deze uniek is voor de bedrijfslogica van uw toepassing. Het wordt aanbevolen om toepassingsbewaking in te stellen met [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), het hulpprogramma voor toepassingsbewaking van Azure.

## <a name="cluster-monitoring"></a>Clusterbewaking

Een van de doelstellingen van Service Fabric is om toepassingen bestand te maken tegen hardwarefouten. Dit doel wordt bereikt door de mogelijkheid van de systeemservices van het platform om infrastructuurproblemen op te sporen en workloads snel te mislukken naar andere knooppunten in het cluster. Maar wat als de systeemdiensten zelf problemen hebben? Of als bij een poging om een werkbelasting te implementeren of te verplaatsen, regels voor de plaatsing van services worden geschonden? Service Fabric biedt diagnoses voor deze en andere problemen om ervoor te zorgen dat u op de hoogte bent van de manier waarop het Service Fabric-platform samenwerkt met uw toepassingen, services, containers en knooppunten.

Voor Windows-clusters wordt aanbevolen clusterbewaking in te stellen met [diagnostics agent](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) en [Azure Monitor-logboeken.](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup)

Voor Linux-clusters is Azure Monitor-logboeken ook het aanbevolen hulpmiddel voor Azure-platform- en infrastructuurbewaking. Linux-platformdiagnostiek vereist een andere configuratie zoals opgemerkt in [De Clustergebeurtenissen van De Stof van de dienst in Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Infrastructuur bewaken

[Azure Monitor-logboeken](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) worden aanbevolen voor het bewaken van clusterniveaugebeurtenissen. Zodra u de agent Log Analytics configureert met uw werkruimte zoals beschreven in de vorige koppeling, u prestatiestatistieken verzamelen, zoals CPU-gebruik, .NET-prestatiemeteritems zoals cpu-gebruik op procesniveau, prestatiemeteritems van servicefabric, zoals aantal uitzonderingen op een betrouwbare service en containerstatistieken zoals CPU-gebruik.  U moet containerlogboeken schrijven naar stdout of stderr, zodat deze beschikbaar zijn in Azure Monitor-logboeken.

## <a name="watchdogs"></a>Waakhonden

Over het algemeen is een waakhond een afzonderlijke service die de status en belasting van services, pings-eindpunten en rapportages van onverwachte statusgebeurtenissen in het cluster in de gaten houdt. Dit kan helpen voorkomen dat fouten die niet kunnen worden gedetecteerd alleen op basis van de prestaties van een enkele service. Waakhonden zijn ook een goede plek om code te hosten die corrigerende actie uitvoert die geen interactie van de gebruiker vereist, zoals het opruimen van logbestanden in opslag op bepaalde tijdstippen. Bekijk een voorbeeld van de implementatie van de Watchdog-service in [Service Fabric Linux-clustergebeurtenissen in Syslog.](https://github.com/Azure-Samples/service-fabric-watchdog-service)

## <a name="next-steps"></a>Volgende stappen

* Aan de slag met het bewerken van uw toepassingen: [gebeurtenis op toepassingsniveau en logboekgeneratie.](service-fabric-diagnostics-event-generation-app.md)
* Ga door de stappen om Application Insights voor uw toepassing in te stellen met [Monitor en een diagnose te stellen van een ASP.NET Core-toepassing op Service Fabric.](service-fabric-tutorial-monitoring-aspnet.md)
* Meer informatie over het monitoren van het platform en de evenementen die Service Fabric voor u biedt: [Platform level event en log generatie.](service-fabric-diagnostics-event-generation-infra.md)
* Azure Monitor-logboeken-integratie configureren met ServiceFabric: [Azure Monitor-logboeken instellen voor een cluster](service-fabric-diagnostics-oms-setup.md)
* Meer informatie over het instellen van Azure Monitor-logboeken voor het bewaken van containers: [Monitoring en Diagnostics for Windows Containers in Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Zie voorbeeld diagnostische problemen en oplossingen met Service Fabric: [het diagnosticeren van veelvoorkomende scenario's](service-fabric-diagnostics-common-scenarios.md)
* Meer informatie over algemene controleaanbevelingen voor Azure-resources: [Aanbevolen procedures - Monitoring en diagnose](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).