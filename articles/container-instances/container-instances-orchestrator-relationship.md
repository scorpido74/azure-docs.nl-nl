---
title: Containerexemplaren en containerorchestration
description: Begrijpen hoe Azure-containerexemplaren communiceren met containerorchestrators.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: f3f8693d1a9a12e7c35d126ab3e3ca53448e5e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533665"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances en containerorchestrators

Door hun kleine formaat en toepassingsoriëntatie zijn containers zeer geschikt voor flexibele leveringsomgevingen en microservicegebaseerde architecturen. De taak van het automatiseren en beheren van een groot aantal containers en hoe ze interageren staat bekend als *orchestration.* Populaire containerorchestrators zijn Kubernetes, DC/OS en Docker Swarm.

Azure Container Instances biedt een aantal van de basisplanningsmogelijkheden van orchestration-platforms. En hoewel het niet de diensten met een hogere waarde dekt die deze platforms bieden, kunnen Azure Container Instances er complementair aan zijn. In dit artikel wordt beschreven wat Azure Container Instances hanteert en hoe volledige containerorchestrators ermee kunnen communiceren.

## <a name="traditional-orchestration"></a>Traditionele orkestratie

De standaarddefinitie van orkestratie omvat de volgende taken:

- **Planning**: Zoek een geschikte machine waarop de container kan worden uitgevoerd, een containerafbeelding en een resourceaanvraag.
- **Affiniteit/Anti-affiniteit**: Geef op dat een set containers in de buurt van elkaar moet draaien (voor prestaties) of voldoende ver uit elkaar moet staan (voor beschikbaarheid).
- **Statusbewaking:** Let op containerfouten en plan deze automatisch opnieuw.
- **Failover:** houd bij wat er op elke machine wordt uitgevoerd en replan containers van mislukte machines naar gezonde knooppunten.
- **Schalen:** Containerexemplaren toevoegen of verwijderen om aan de vraag te voldoen, handmatig of automatisch.
- **Netwerken:** zorg voor een overlaynetwerk voor het coördineren van containers om te communiceren over meerdere hostmachines.
- **Servicedetectie:** Schakel containers in om elkaar automatisch te lokaliseren, zelfs als ze tussen hostmachines bewegen en IP-adressen wijzigen.
- **Gecoördineerde toepassingsupgrades:** Beheer containerupgrades om uitvaltijd van toepassingen te voorkomen en schakel terugdraaiing in als er iets misgaat.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orkestratie met Azure Container Instances: een gelaagde aanpak

Azure Container Instances maakt een gelaagde benadering van orkestratie mogelijk, met alle plannings- en beheermogelijkheden die nodig zijn om één container uit te voeren, terwijl orchestrator-platforms meerdere containertaken kunnen beheren.

Omdat de onderliggende infrastructuur voor containerexemplaren wordt beheerd door Azure, hoeft een orchestrator-platform zich niet bezig te houden met het vinden van een geschikte hostmachine waarop één container kan worden uitgevoerd. De elasticiteit van de cloud zorgt ervoor dat er altijd een beschikbaar is. In plaats daarvan kan de orchestrator zich richten op de taken die de ontwikkeling van multi-container architecturen vereenvoudigen, inclusief schalen en gecoördineerde upgrades.

## <a name="scenarios"></a>Scenario's

Hoewel de orchestrator-integratie met Azure Container Instances nog steeds ontluikend is, verwachten we dat er een paar verschillende omgevingen zullen ontstaan:

### <a name="orchestration-of-container-instances-exclusively"></a>Orkestratie van containerexemplaren uitsluitend

Omdat ze snel beginnen en per seconde worden gefactureerd, biedt een omgeving die uitsluitend is gebaseerd op Azure Container Instances de snelste manier om aan de slag te gaan en zeer variabele workloads aan te pakken.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinatie van containerexemplaren en containers in virtuele machines

Voor langlopende, stabiele workloads is het orkestreren van containers in een cluster van speciale virtuele machines doorgaans goedkoper dan het uitvoeren van dezelfde containers met Azure Container Instances. Container-exemplaren bieden echter een geweldige oplossing voor het snel uitbreiden en contracteren van uw totale capaciteit om onverwachte of kortstondige pieken in gebruik aan te pakken.

In plaats van het aantal virtuele machines in uw cluster uit te schalen en vervolgens extra containers op die machines te implementeren, kan de orchestrator de extra containers in Azure Container Instances eenvoudig plannen en verwijderen wanneer ze niet langer Nodig.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Voorbeeldimplementatie: virtuele knooppunten voor Azure Kubernetes Service (AKS)

Als u toepassingsworkloads snel wilt schalen in een AKS-cluster [(Azure Kubernetes Service),](../aks/intro-kubernetes.md) u *virtuele knooppunten* gebruiken die dynamisch zijn gemaakt in Azure Container Instances. Virtuele knooppunten maken netwerkcommunicatie mogelijk tussen pods die worden uitgevoerd in ACI en het AKS-cluster. 

Virtuele knooppunten ondersteunen momenteel Linux-containerexemplaren. Ga aan de slag met virtuele knooppunten via de Azure CLI- of [Azure-portal.](https://go.microsoft.com/fwlink/?linkid=2047545) [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538)

Virtuele knooppunten gebruiken de open source [Virtual Kubelet][aci-connector-k8s] om de Kubernetes [kubelet][kubelet-doc] na te bootsen door zich te registreren als een knooppunt met onbeperkte capaciteit. De Virtuele Kubelet verzendt het maken van [pods][pod-doc] als containergroepen in Azure Container Instances.

Zie het [Virtual Kubelet-project](https://github.com/virtual-kubelet/virtual-kubelet) voor aanvullende voorbeelden van het uitbreiden van de Kubernetes API naar serverless containerplatforms.

## <a name="next-steps"></a>Volgende stappen

Maak uw eerste container met Azure Container Instances met behulp van de [quickstart-handleiding](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
