---
title: KEUR Agent groepen voor DC/OS voor Azure Container Service
description: Hoe de Pools voor open bare en persoonlijke agents werken met een Azure Container Service DC/OS-cluster
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278385"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>KEUR Agent groepen voor DC/OS voor Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS-clusters in Azure Container Service bevatten agent knooppunten in twee groepen, een open bare groep en een persoonlijke groep. Een toepassing kan worden geïmplementeerd in een van beide groepen, waardoor de toegankelijkheid wordt beïnvloed door computers in uw container service. De machines kunnen worden blootgesteld aan Internet (openbaar) of intern (privé) worden bewaard. Dit artikel bevat een kort overzicht van de redenen waarom er open bare en privé groepen zijn.


* **Persoonlijke**agents: knoop punten van particuliere agents worden uitgevoerd via een niet-routeerbaar netwerk. Dit netwerk is alleen toegankelijk vanuit de beheer zone of de open bare zone-edge-router. DC/OS start standaard apps op knoop punten van particuliere agents. 

* **Open bare agenten**: open bare agent knooppunten voeren apps en services van DC/OS uit via een openbaar toegankelijk netwerk. 

Zie de [DC/OS-documentatie](https://docs.mesosphere.com/)voor meer informatie over DC/OS-netwerk beveiliging.

## <a name="deploy-agent-pools"></a>Agent groepen implementeren

De agent groepen DC/OS in Azure Container Service worden als volgt gemaakt:

* De **persoonlijke groep** bevat het aantal agent knooppunten dat u opgeeft wanneer u [het DC/OS-cluster implementeert](container-service-deployment.md). 

* De **open bare groep** bevat in eerste instantie een vooraf bepaald aantal agent knooppunten. Deze groep wordt automatisch toegevoegd wanneer het DC/OS-cluster is ingericht.

De persoonlijke groep en de open bare groep zijn virtuele-machine schaal sets van Azure. U kunt de grootte van deze Pools wijzigen na de implementatie.

## <a name="use-agent-pools"></a>Agent groepen gebruiken
**Marathon** implementeert standaard elke nieuwe toepassing naar de knoop punten van de *privé* -agent. U moet de toepassing expliciet implementeren naar de *open bare* knoop punten tijdens het maken van de toepassing. Selecteer het tabblad **optioneel** en voer **slave_public** in voor de waarde van de **geaccepteerde resource rollen** . Dit proces wordt [hier](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) beschreven en in de [DC/OS-](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) documentatie.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het beheren van uw DC/OS-containers](container-service-mesos-marathon-ui.md).

* Meer informatie over [het openen van de firewall](container-service-enable-public-access.md) van Azure om open bare toegang tot uw DC/OS-containers mogelijk te maken.

