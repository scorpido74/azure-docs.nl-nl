---
title: (AFGESCHAFT) DC/OS-agentpools voor Azure Container Service
description: Hoe de openbare en private agentpools werken met een Azure Container Service DC/OS-cluster
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278385"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(AFGESCHAFT) DC/OS-agentpools voor Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS-clusters in Azure Container Service bevatten agentknooppunten in twee pools, een openbare pool en een privépool. Een toepassing kan worden geïmplementeerd in een van beide groep, wat de toegankelijkheid tussen machines in uw containerservice beïnvloedt. De machines kunnen worden blootgesteld aan het internet (openbaar) of intern (privé) worden gehouden. Dit artikel geeft een kort overzicht van waarom er openbare en private zwembaden zijn.


* **Privéagents**: Privéagentknooppunten lopen door een niet-routeerbaar netwerk. Dit netwerk is alleen toegankelijk vanuit de beheerzone of via de openbare zone edge router. Dc/OS lanceert standaard apps op private agent-knooppunten. 

* **Openbare agents**: Openbare agentknooppunten draaien DC/OS-apps en -services via een openbaar toegankelijk netwerk. 

Zie de [DC/OS-documentatie](https://docs.mesosphere.com/)voor meer informatie over dc/os-netwerkbeveiliging.

## <a name="deploy-agent-pools"></a>Agentpools implementeren

De DC/OS-agentpools in Azure Container Service worden als volgt gemaakt:

* De **privégroep** bevat het aantal agentknooppunten dat u opgeeft wanneer u [het DC/OS-cluster implementeert.](container-service-deployment.md) 

* De **openbare pool** bevat in eerste instantie een vooraf bepaald aantal agentknooppunten. Deze groep wordt automatisch toegevoegd wanneer het DC/OS-cluster is ingericht.

De privépool en de openbare pool zijn Azure-sets voor virtuele machineschalen. U het formaat van deze pools na implementatie wijzigen.

## <a name="use-agent-pools"></a>Agentpools gebruiken
**Marathon** implementeert standaard elke nieuwe toepassing op de knooppunten van de *privé-agent.* U moet de toepassing expliciet implementeren op de *openbare* knooppunten tijdens het maken van de toepassing. Selecteer het tabblad **Optioneel** en voer **slave_public** in voor de waarde **Geaccepteerde resourcerollen.** Dit proces wordt [hier](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) gedocumenteerd en in de [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) documentatie.

## <a name="next-steps"></a>Volgende stappen
* Lees meer over [het beheren van uw DC/OS-containers.](container-service-mesos-marathon-ui.md)

* Meer informatie over het openen van [de firewall](container-service-enable-public-access.md) van Azure om openbare toegang tot uw DC/OS-containers mogelijk te maken.

