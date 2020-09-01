---
title: Meer informatie over Kubernetes workload Management op Azure Stack edge-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe Kubernetes-workloads kunnen worden beheerd op uw Azure Stack edge-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 38c46bdcce64f726b3a7ddf74e0cfd10a14ba663
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268024"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Kubernetes op uw Azure Stack edge-apparaat

Op uw Azure Stack edge-apparaat wordt een Kubernetes-cluster gemaakt wanneer u een compute-functie configureert. Nadat het Kubernetes-cluster is gemaakt, kunnen in een van beide toepassingen met containers in de Kubernetes-cluster worden geïmplementeerd. Er zijn verschillende manieren om werk belastingen te implementeren in uw Kubernetes-cluster. 

In dit artikel worden de verschillende methoden beschreven die kunnen worden gebruikt voor het implementeren van werk belastingen op uw Azure Stack edge-apparaat.

## <a name="workload-types"></a>Typen werk belasting

De twee algemene typen werk belastingen die u kunt implementeren op uw Azure Stack edge-apparaat zijn stateless toepassingen of stateful toepassingen.

- **Stateless toepassingen** behouden hun status niet en slaan geen gegevens op in permanente opslag. Alle gebruikers-en sessie gegevens blijven bij de client. Enkele voor beelden van stateless toepassingen zijn web-frontends zoals nginx en andere webtoepassingen.

    U kunt een Kubernetes-implementatie maken om een stateless toepassing in uw cluster te implementeren. 

- **Stateful toepassingen** vereisen dat hun status wordt opgeslagen. Stateful toepassingen gebruiken permanente opslag, zoals permanente volumes, om gegevens op te slaan voor gebruik door de server of door andere gebruikers. Voor beelden van stateful toepassingen zijn data bases zoals MongoDB.

    U kunt een Kubernetes-implementatie maken om een stateful toepassing te implementeren. 

## <a name="deployment-flow"></a>Implementatie stroom

Als u toepassingen op een Azure Stack edge-apparaat wilt implementeren, volgt u deze stappen: 
 
1. **Toegang configureren**: eerst gebruikt u de Power shell-runs Pace voor het maken van een gebruiker, het maken van een naam ruimte en het verlenen van gebruikers toegang tot die naam ruimte.
2. **Opslag configureren**: vervolgens gebruikt u de resource Azure stack Edge in de Azure Portal om permanente volumes te maken met behulp van statische of dynamische inrichting voor de stateful toepassingen die u gaat implementeren.
3. **Netwerken configureren**: tot slot gebruikt u de services om toepassingen extern en binnen het Kubernetes-cluster beschikbaar te maken.
 
## <a name="deployment-types"></a>Implementatie typen

Er zijn drie manieren om uw workloads te implementeren. Met elk van deze implementatie methoden kunt u verbinding maken met een afzonderlijke naam ruimte op het apparaat en vervolgens stateless of stateful toepassingen implementeren.

![Implementatie van Kubernetes-werk belasting](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Lokale implementatie**: dit is via het opdracht regel programma voor toegang, zoals `kubectl` waarmee u Kubernetes kunt implementeren `yamls` . U maakt verbinding met het Kubernetes-cluster op uw Azure Stack-rand die u maakt met behulp van het `kubeconfig` bestand. Ga voor meer informatie naar [toegang tot een Kubernetes-cluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **IOT Edge-implementatie**: dit is via IOT Edge, waarmee verbinding wordt gemaakt met de Azure-IOT hub. U maakt verbinding met het Kubernetes-cluster op uw Azure Stack edge-apparaat via de `iotedge` naam ruimte. De IoT Edge agents die in deze naam ruimte zijn geïmplementeerd, zijn verantwoordelijk voor de connectiviteit met Azure. U past de `IoT Edge deployment.json` configuratie toe met behulp van Azure DEVOPS CI/cd. De naam ruimte en het IoT Edge beheer worden uitgevoerd via de Cloud operator.

- **Implementatie van Azure/Arc**: Azure Arc is een Hybrid management tool waarmee u toepassingen kunt implementeren in uw Kubernetes-clusters. U verbindt het Kubernetes-cluster op uw Azure Stack edge-apparaat via de `azure-arc namespace` . Agents worden geïmplementeerd in deze naam ruimte die verantwoordelijk zijn voor de connectiviteit met Azure. U past de implementatie configuratie toe met behulp van het GitOps-configuratie beheer. Met Azure Arc kunt u ook Azure Monitor voor containers gebruiken om uw clusters weer te geven en te bewaken. Ga naar [Wat is Azure-Arc enabled Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)voor meer informatie.

## <a name="choose-the-deployment-type"></a>Het implementatie type kiezen

Houd bij het implementeren van toepassingen rekening met de volgende informatie:

- **Eén of meer typen**: u kunt kiezen uit één implementatie optie of een combi natie van verschillende implementatie opties.
- **Cloud versus lokaal**: afhankelijk van uw toepassingen kunt u lokale implementatie kiezen via kubectl of Cloud implementatie via IOT Edge en Azure Arc. 
    - Lokale implementatie is geschikter voor ontwikkelings scenario's. Wanneer u een lokale implementatie kiest, bent u beperkt tot het netwerk waarin uw Azure Stack edge-apparaat wordt geïmplementeerd.
    - Als u een Cloud agent hebt die u kunt implementeren, moet u uw Cloud operator implementeren en Cloud beheer gebruiken.
- **IOT versus Azure Arc**: de keuze van de implementatie is ook afhankelijk van het doel van uw product scenario. Als u toepassingen of containers implementeert die een diep gaande integratie met IoT of IoT-ecosysteem hebben, moet u de IoT Edge manier kiezen voor het implementeren van toepassingen. Als u bestaande Kubernetes-implementaties hebt, is Azure Arc de voorkeurs keuze.


## <a name="next-steps"></a>Volgende stappen

Als u een app lokaal wilt implementeren via kubectl, raadpleegt u:

- [Implementeer een staatloze toepassing op uw Azure stack Edge via kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Ga als volgt te werk om een app te implementeren via IoT Edge:

- [Implementeer een voorbeeld module op uw Azure stack rand via IOT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Zie voor informatie over het implementeren van een app via Azure Arc:

- [Implementeer een toepassing met behulp van Azure Arc](azure-stack-edge-gpu-deploy-sample-module.md).
