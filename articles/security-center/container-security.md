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
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800597"
---
# <a name="container-security-in-security-center"></a>Beveiliging van de container in Security Center

Azure Security Center is de Azure-systeem eigen oplossing voor het beveiligen van uw containers. Security Center kunt de volgende container resource typen beveiligen:



|Resource |Name  |Details  |
|:---------:|---------|---------|
|![Container host](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Container-hosts (virtuele machines met docker)|Security Center scant uw Docker-configuraties en geeft u inzicht in onjuiste configuraties door een lijst op te stellen met alle mislukte regels die zijn beoordeeld. Security Center biedt richt lijnen die u helpen bij het snel oplossen van deze problemen en het besparen van tijd. Docker-configuraties worden doorlopend beoordeeld door Security Center en u ziet steeds wat de actuele status is.|
|![Kubernetes-service](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Clusters van Azure Kubernetes service (AKS)|Krijg meer inzicht in uw AKS-knoop punten, Cloud verkeer en beveiligings controles met [de optionele AKS-bundel van Security Center voor gebruikers van de](azure-kubernetes-service-integration.md) Standard-laag.|
|![Containerregister](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Azure Container Registry (ACR) registers|Krijg meer inzicht in de kwets baarheid van de installatie kopieën in uw ACR-registers op basis van ARM met de [optionele ACR-bundel van Security Center](azure-kubernetes-service-integration.md) voor gebruikers van de Standard-laag.|
||||


In dit artikel wordt beschreven hoe u deze bundels kunt gebruiken om de beveiliging van uw containers en hun apps te verbeteren, bewaken en onderhouden. U leert hoe Security Center helpt bij het werken met deze kern aspecten van container beveiliging:

- [Beveiligings beheer-container installatie kopieën scannen](#vulnerability-management---scanning-container-images)
- [Omgevings beveiliging: doorlopende bewaking van uw docker-configuratie en Kubernetes-clusters](#environment-hardening)
- [Run-time beveiliging-realtime detectie van bedreigingen](#run-time-protection---real-time-threat-detection)

[![Tabblad Beveiliging van Azure Security Center container](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Zie [de beveiliging van uw containers bewaken](monitor-container-security.md)voor instructies over het gebruik van deze functies.

## <a name="vulnerability-management---scanning-container-images"></a>Beveiligings beheer-container installatie kopieën scannen
Als u uw op ARM gebaseerde Azure Container Registry wilt bewaken, controleert u of u de Standard-laag van Security Center hebt (Zie de [prijzen](/azure/security-center/security-center-pricing)). Schakel vervolgens de optionele container registers bundel in. Wanneer een nieuwe installatie kopie wordt gepusht, wordt de installatie kopie door Security Center gescand met behulp van een scanner uit de toonaangevende beveiligings problemen voor het scannen van leveranciers, Qualys.

Als er problemen worden gevonden, wordt er een melding weer gegeven in het Security Center-dash board van Qualys of Security Center. Security Center voorziet in elk beveiligings probleem met actie-aanbevelingen, samen met een Ernst classificatie en richt lijnen voor het oplossen van het probleem. Zie de [naslag lijst met aanbevelingen](recommendations-reference.md#recs-containers)voor meer informatie over de aanbevelingen van Security Center voor containers.

Security Center filters en classificeert de resultaten van de scanner. Wanneer een afbeelding in orde is, wordt deze als zodanig gemarkeerd door Security Center. Security Center genereert alleen beveiligings aanbevelingen voor installatie kopieën waarvoor problemen moeten worden opgelost. Als er alleen een melding wordt weer geven als er problemen zijn, vermindert Security Center het potentieel voor ongewenste informatieve waarschuwingen.

## <a name="environment-hardening"></a>Omgevings beveiliging

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Doorlopende bewaking van uw docker-configuratie
Azure Security Center identificeert niet-beheerde containers die worden gehost op IaaS Linux-Vm's of andere Linux-machines waarop docker-containers worden uitgevoerd. Security Center doorlopend de configuraties van deze containers evalueren. Vervolgens worden ze vergeleken met de [CIS-Referentie (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

Security Center bevat de volledige regelset van de CIS docker-Bench Mark en waarschuwt u als uw containers niet voldoen aan een van de besturings elementen. Als er onjuiste configuraties worden gevonden, worden in Security Center beveiligings aanbevelingen gegenereerd. Op de **pagina aanbevelingen** kunt u aanbevelingen bekijken en problemen oplossen. U ziet ook de aanbevelingen op het tabblad **containers** waarin alle virtuele machines worden weer gegeven die zijn geïmplementeerd met docker. 

Zie de [sectie container](recommendations-reference.md#recs-containers) in de naslag tabel met aanbevelingen voor meer informatie over de relevante Security Center aanbevelingen die voor deze functie kunnen worden weer gegeven.

Wanneer u de beveiligings problemen van een virtuele machine wilt verkennen, bevat Security Center extra informatie over de containers op de computer. Deze informatie omvat de docker-versie en het aantal installatie kopieën die op de host worden uitgevoerd. 

>[!NOTE]
> Deze CIS-benchmark controles worden niet uitgevoerd op door AKS beheerde instanties of door Databricks beheerde Vm's.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Continue bewaking van uw Kubernetes-clusters
Security Center werkt samen met Azure Kubernetes service (AKS), de beheerde container service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers.

AKS biedt beveiligings controles en zicht baarheid in de beveiligings-postuur van uw clusters. Security Center gebruikt deze functies voor het volgende:
* De configuratie van uw AKS-clusters continu controleren
* Beveiligings aanbevelingen die zijn afgestemd op de industrie normen genereren

Zie de [sectie container](recommendations-reference.md#recs-containers) in de naslag tabel met aanbevelingen voor meer informatie over de relevante Security Center aanbevelingen die voor deze functie kunnen worden weer gegeven.

## <a name="run-time-protection---real-time-threat-detection"></a>Run-time beveiliging-realtime detectie van bedreigingen

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>Volgende stappen

In dit overzicht hebt u geleerd over de kern elementen van de container beveiliging in Azure Security Center. Ga door met het [bewaken van de beveiliging van uw containers](monitor-container-security.md).
> [!div class="nextstepaction"]
> [De beveiliging van uw containers bewaken](monitor-container-security.md)