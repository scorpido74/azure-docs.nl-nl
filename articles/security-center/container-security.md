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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 31e45906dfeafcd2af1651347dde9dc6d3c6fb7c
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769214"
---
# <a name="container-security-in-security-center"></a>Beveiliging van de container in Security Center

Azure Security Center is de Azure-systeem eigen oplossing voor container beveiliging. Security Center is ook de optimale glas ervaring voor de beveiliging van uw Cloud werkbelastingen, Vm's, servers en containers.

In dit artikel wordt beschreven hoe u de beveiliging van uw containers en hun apps kunt verbeteren, bewaken en onderhouden. U leert hoe Security Center helpt bij het werken met deze kern aspecten van container beveiliging:

* Beveiligings beheer
* Versterking van de omgeving van de container
* Runtime-beveiliging

[tabblad Beveiliging van de container ![Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>Beveiligings beheer-container installatie kopieën scannen (preview)
Als u uw Azure Container Registry wilt bewaken, moet u ervoor zorgen dat u de Standard-laag van Security Center hebt (Zie [prijzen](https://docs.microsoft.com/azure/security-center/security-center-pricing.md)). Schakel vervolgens de optionele container registers bundel in. Wanneer een nieuwe installatie kopie wordt gepusht, wordt de installatie kopie door Security Center gescand met behulp van een scanner uit de toonaangevende beveiligings problemen voor het scannen van leveranciers, Qualys.

Als er problemen worden gevonden, wordt er een melding weer gegeven in het Security Center-dash board van Qualys of Security Center. Security Center voorziet in elk beveiligings probleem met actie-aanbevelingen, samen met een Ernst classificatie en richt lijnen voor het oplossen van het probleem. Zie de lijst met aanbevelingen voor meer informatie over [de aanbevelingen van Security Center.](recommendations-compute-and-apps.md)

## <a name="environment-hardening"></a>Omgevings beveiliging

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Doorlopende bewaking van uw docker-configuratie
Azure Security Center identificeert niet-beheerde containers die worden gehost op IaaS Linux-Vm's of andere Linux-machines waarop docker-containers worden uitgevoerd. Security Center doorlopend de configuraties van deze containers evalueren. Vervolgens worden ze vergeleken met de [CIS-Referentie (Center for Internet Security](https://www.cisecurity.org/benchmark/docker/)). 

Security Center bevat de volledige regelset van de CIS docker-Bench Mark en waarschuwt u als uw containers niet voldoen aan een van de besturings elementen. Als er onjuiste configuraties worden gevonden, worden in Security Center beveiligings aanbevelingen gegenereerd. Op de **pagina aanbevelingen** kunt u aanbevelingen bekijken en problemen oplossen. U ziet ook de aanbevelingen op het tabblad **containers** waarin alle virtuele machines worden weer gegeven die zijn geïmplementeerd met docker. Wanneer u de beveiligings problemen op een virtuele machine wilt verkennen, bevat Security Center extra informatie over de containers op de computer. Deze informatie omvat de docker-versie en het aantal installatie kopieën die op de host worden uitgevoerd. Zie [hier](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection)voor meer informatie over de aanbevelingen. 

>[!NOTE]
> Deze CIS-benchmark controles worden niet uitgevoerd op door AKS beheerde instanties of door Databricks beheerde Vm's.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Continue bewaking van uw Kubernetes-clusters (preview-versie)
Security Center werkt samen met Azure Kubernetes service (AKS), de beheerde container service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers.

AKS biedt beveiligings controles en zicht baarheid in de beveiligings-postuur van uw clusters. Security Center gebruikt deze functies voor het volgende:
* De configuratie van uw AKS-clusters continu controleren
* Beveiligings aanbevelingen die zijn afgestemd op de industrie normen genereren

Zie [beveiliging van virtuele machines](security-center-virtual-machine-protection.md)voor meer informatie over de aanbevelingen van Security Center.

## <a name="run-time-protection---real-time-threat-detection"></a>Run-time beveiliging-realtime detectie van bedreigingen

Security Center voorziet in realtime detectie van bedreigingen voor uw container omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

We detecteren bedreigingen op het niveau van de host en het AKS-cluster. Zie [Azure container service](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)voor meer informatie.


## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>De beveiligings postuur van uw container bronnen weer geven
1.  Open de pagina **compute &-apps** van Security Center.
2.  Klik op het tabblad **containers** . De postuur van uw AKS-clusters, ACR-registers en Vm's met docker worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiliging van de container in Azure Security Center:
* Details van de [integratie met de Azure Kubernetes-service](azure-kubernetes-service-integration.md)

* Details van de [integratie met Azure container Registry](azure-container-registry-integration.md)