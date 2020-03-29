---
title: (AFGESCHAFT) Azure DC/OS-cluster bewaken - Datadog
description: Monitor een Azure Container Service-cluster met Datadog. Gebruik de webgebruikers interface van DC/OS om de Datadog-agents in uw cluster te implementeren.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 530092dfabacb0b07f4002a82078dd3535cd7e8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275257"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(AFGESCHAFT) Een Azure Container Service DC/OS-cluster bewaken met Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel implementeren we Datadog-agents op alle agentknooppunten in uw Azure Container Service-cluster. Voor deze configuratie heb je een account bij Datadog nodig. 

## <a name="prerequisites"></a>Vereisten
[Implementeer](container-service-deployment.md) en [verbind](../container-service-connect.md) een cluster dat door Azure Container Service is geconfigureerd. Ontdek de [Marathon UI.](container-service-mesos-marathon-ui.md) Ga [https://datadoghq.com](https://datadoghq.com) naar om een Datadog-account in te stellen. 

## <a name="datadog"></a>Datadog (Datadog)
Datadog is een bewakingsservice die bewakingsgegevens uit uw containers verzamelt binnen uw Azure Container Service-cluster. Datadog heeft een Docker Integration Dashboard waar u specifieke statistieken in uw containers zien. Statistieken verzameld uit uw containers worden georganiseerd door CPU, Geheugen, Netwerk en I/O. Datadog splitst statistieken op in containers en afbeeldingen. Een voorbeeld van hoe de gebruikersinterface eruit ziet voor CPU-gebruik is hieronder.

![Gegevenshond-gebruikersinterface](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Een Datadog-implementatie configureren met Marathon
Deze stappen laten u zien hoe u Datadog-toepassingen configureren en implementeren in uw cluster met Marathon. 

Krijg toegang tot uw [http://localhost:80/](http://localhost:80/)DC/OS-gebruikersinterface via . Eenmaal in de DC / OS UI navigeren naar de "Universe" die aan de linkerkant en vervolgens zoeken naar "Datadog" en klik op "Installeren."

![Datadog pakket binnen het DC/OS Universe](./media/container-service-monitoring/datadog1.png)

Nu om de configuratie te voltooien heb je een Datadog-account of een gratis proefaccount nodig. Zodra u bent ingelogd op de Datadog website kijken naar links en ga naar Integraties -> dan [API's](https://app.datadoghq.com/account/settings#api). 

![Api-sleutel van Datadog](./media/container-service-monitoring/datadog2.png)

Voer vervolgens uw API-sleutel in in de Datadog-configuratie binnen het DC/OS-universum. 

![Datadog-configuratie in het DC/OS-universum](./media/container-service-monitoring/datadog3.png) 

In de bovenstaande configuratie worden exemplaren ingesteld op 100000000, dus wanneer een nieuw knooppunt wordt toegevoegd aan het cluster, implementeert Datadog automatisch een agent naar dat knooppunt. Dit is een tussentijdse oplossing. Zodra u het pakket hebt geïnstalleerd, moet u terug navigeren naar de Datadog-website en "[Dashboards](https://app.datadoghq.com/dash/list)" vinden. Van daaruit ziet u Aangepaste en integratie dashboards. Het [Docker-dashboard](https://app.datadoghq.com/screen/integration/docker) heeft alle containerstatistieken die u nodig hebt om uw cluster te bewaken. 

