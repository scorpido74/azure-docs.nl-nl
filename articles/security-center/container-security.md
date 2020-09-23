---
title: Beveiliging van de container in Azure Security Center | Microsoft Docs
description: Meer informatie over de beveiligings functies van de container van Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: bd4487d3d0664699a32eec2ab47297839eaf8a8a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894881"
---
# <a name="container-security-in-security-center"></a>Beveiliging van de container in Security Center

Azure Security Center is de Azure-systeem eigen oplossing voor het beveiligen van uw containers.

Security Center kunt de volgende container resource typen beveiligen:

| Resourcetype | Beveiligingen die worden aangeboden door Security Center |
|:--------------------:|-----------|
| ![Kubernetes-service](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Clusters van Azure Kubernetes service (AKS)** | -Doorlopende evaluatie van de configuraties van uw AKS-clusters om inzicht te krijgen in onjuiste configuraties en richt lijnen om eventuele gedetecteerde problemen op te lossen.<br>Meer [informatie over omgevings beveiliging via beveiligings aanbevelingen](#environment-hardening).<br><br>-Bedreigings beveiliging voor AKS-clusters en Linux-knoop punten. Waarschuwingen voor verdachte activiteiten worden gegeven door de optionele  [Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md).<br>Meer [informatie over runtime-beveiliging voor AKS-knoop punten en-clusters](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Container host](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Container-hosts**<br>(Vm's met docker) | -Doorlopende evaluatie van uw docker-configuraties om inzicht te krijgen in onjuiste configuraties en richt lijnen om te helpen bij het oplossen van gedetecteerde problemen met de optionele  [Azure Defender voor servers](defender-for-servers-introduction.md).<br>Meer [informatie over omgevings beveiliging via beveiligings aanbevelingen](#environment-hardening).|
| ![Container register](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure Container Registry (ACR) registers** | -Evaluatie-en beheer hulpprogramma's voor de installatie kopieën in uw op Azure Resource Manager gebaseerde ACR-registers met de optionele [Azure Defender voor container registers](defender-for-container-registries-introduction.md).<br>Meer [informatie over het scannen van uw container installatie kopieën voor beveiligings problemen](#vulnerability-management---scanning-container-images). |
|||

In dit artikel wordt beschreven hoe u Security Center kunt gebruiken, samen met de optionele Azure Defender-plannen voor container registers, servers en Kubernetes, om de beveiliging van uw containers en hun apps te verbeteren, bewaken en onderhouden.

U leert hoe Security Center helpt bij het werken met deze kern aspecten van container beveiliging:

- [Beveiligings beheer-container installatie kopieën scannen](#vulnerability-management---scanning-container-images)
- [Omgevings beveiliging](#environment-hardening)
- [Runtime-beveiliging voor AKS-knoop punten en-clusters](#run-time-protection-for-aks-nodes-and-clusters)

In de volgende scherm afbeelding ziet u de pagina inventarisatie van activa en de verschillende container bron typen die worden beveiligd door Security Center.

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Container-gerelateerde resources op de pagina Asset Inventory van Security Center" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Beveiligings beheer-container installatie kopieën scannen

Als u de installatie kopieën in uw op Azure Resource Manager gebaseerde Azure-container registers wilt bewaken, schakelt u [Azure Defender in voor container registers](defender-for-container-registries-introduction.md). Security Center scant alle installatie kopieën die in de afgelopen 30 dagen zijn getrokken, naar uw REGI ster gepusht of geïmporteerd. De geïntegreerde scanner wordt verschaft door de toonaangevende beveiligings problemen voor het scannen van leveranciers, Qualys.

Als er problemen worden gevonden, wordt er een melding weer gegeven in het [Azure Defender-dash board](azure-defender-dashboard.md)van Qualys of Security Center. Security Center voorziet in elk beveiligings probleem met actie-aanbevelingen, samen met een Ernst classificatie en richt lijnen voor het oplossen van het probleem. Zie de [naslag lijst met aanbevelingen](recommendations-reference.md#recs-containers)voor meer informatie over de aanbevelingen van Security Center voor containers.

Security Center filters en classificeert de resultaten van de scanner. Wanneer een afbeelding in orde is, wordt deze als zodanig gemarkeerd door Security Center. Security Center genereert alleen beveiligings aanbevelingen voor installatie kopieën waarvoor problemen moeten worden opgelost. Als er alleen een melding wordt weer geven als er problemen zijn, vermindert Security Center het potentieel voor ongewenste informatieve waarschuwingen.

## <a name="environment-hardening"></a>Omgevings beveiliging

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Doorlopende bewaking van uw docker-configuratie

Azure Security Center identificeert niet-beheerde containers die worden gehost op IaaS Linux-Vm's of andere Linux-machines waarop docker-containers worden uitgevoerd. Security Center doorlopend de configuraties van deze containers evalueren. Vervolgens worden ze vergeleken met de [CIS-Referentie (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

Security Center bevat de volledige regelset van de CIS docker-Bench Mark en waarschuwt u als uw containers niet voldoen aan een van de besturings elementen. Als er onjuiste configuraties worden gevonden, worden in Security Center beveiligings aanbevelingen gegenereerd. Op de **pagina aanbevelingen** kunt u aanbevelingen bekijken en problemen oplossen. U ziet ook de aanbevelingen op het tabblad **containers** waarin alle virtuele machines worden weer gegeven die zijn geïmplementeerd met docker. De CIS-benchmark controles worden niet uitgevoerd op door AKS beheerde instanties of door Databricks beheerde Vm's.

Zie de [sectie container](recommendations-reference.md#recs-containers) in de naslag tabel met aanbevelingen voor meer informatie over de relevante Security Center aanbevelingen die voor deze functie kunnen worden weer gegeven.

Wanneer u de beveiligings problemen van een virtuele machine wilt verkennen, bevat Security Center extra informatie over de containers op de computer. Deze informatie omvat de docker-versie en het aantal installatie kopieën die op de host worden uitgevoerd. 

Als u niet-beheerde containers wilt bewaken die worden gehost op IaaS Linux Vm's, schakelt u de optionele [Azure Defender voor servers](defender-for-servers-introduction.md)in.


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Continue bewaking van uw Kubernetes-clusters
Security Center werkt samen met Azure Kubernetes service (AKS), de beheerde container service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers.

AKS biedt beveiligings controles en zicht baarheid in de beveiligings-postuur van uw clusters. Security Center gebruikt deze functies voor het volgende:
* De configuratie van uw AKS-clusters continu controleren
* Beveiligings aanbevelingen die zijn afgestemd op de industrie normen genereren

Zie de [sectie container](recommendations-reference.md#recs-containers) in de naslag tabel met aanbevelingen voor meer informatie over de relevante Security Center aanbevelingen die voor deze functie kunnen worden weer gegeven.

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Aanbevolen procedures voor werk belasting beveiliging met behulp van Kubernetes Admission Control

Installeer de  **Azure Policy-invoeg toepassing voor Kubernetes** om een bundel te krijgen van de aanbevelingen voor het beveiligen van de workloads van uw Kubernetes-containers.

Zoals beschreven in [dit Azure Policy voor de pagina Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), breidt de invoeg toepassing de open-source [gate keeper v3](https://github.com/open-policy-agent/gatekeeper)   Admission controller-Webhook uit voor [Open Policy Agent](https://www.openpolicyagent.org/). Kubernetes Admission controllers zijn invoeg toepassingen die afdwingen hoe uw clusters worden gebruikt. De invoeg toepassing registreert zich als een webhook voor Kubernetes Admission Control en maakt het mogelijk om op schaal afdwingingen en beveiligingen op uw clusters op een gecentraliseerde, consistente manier toe te passen. 

Wanneer u de invoeg toepassing op uw AKS-cluster hebt geïnstalleerd, wordt elke aanvraag van de Kubernetes-API-server gecontroleerd op basis van de vooraf gedefinieerde set aanbevolen procedures voordat deze in het cluster wordt bewaard. U kunt vervolgens configureren om de best practices af te **dwingen** en ze te verplichten voor toekomstige workloads. 

U kunt er bijvoorbeeld voor zorgen dat geprivilegieerde containers niet moeten worden gemaakt, en toekomstige aanvragen worden geblokkeerd.

Meer informatie over [het beveiligen van uw Kubernetes-workloads](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Runtime-beveiliging voor AKS-knoop punten en-clusters

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Volgende stappen

In dit overzicht hebt u geleerd over de kern elementen van de container beveiliging in Azure Security Center. Zie voor verwante materiaal:

- [Inleiding tot Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)
- [Inleiding tot Azure Defender voor container registers](defender-for-container-registries-introduction.md)