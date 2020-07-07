---
title: KEUR Azure DC/OS-cluster controleren-Datadog
description: Een Azure Container Service cluster bewaken met Datadog. Gebruik de Web-UI van DC/OS om de Datadog-agents te implementeren in uw cluster.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166139"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>KEUR Een Azure Container Service DC/OS-cluster bewaken met Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel gaan we Datadog-agents implementeren op alle agent knooppunten in uw Azure Container Service-cluster. U hebt een account met Datadog nodig voor deze configuratie. 

## <a name="prerequisites"></a>Vereisten
[Implementeer](container-service-deployment.md) en [verbind](../container-service-connect.md) een cluster dat door Azure Container Service is geconfigureerd. Verken de [Marathon-gebruikers interface](container-service-mesos-marathon-ui.md). Ga naar [https://datadoghq.com](https://datadoghq.com) om een Datadog-account in te stellen. 

## <a name="datadog"></a>Datadog
Datadog is een bewakings service waarmee bewakings gegevens worden verzameld van uw containers in uw Azure Container Service cluster. Datadog heeft een docker-integratie dashboard waarin u specifieke metrische gegevens in uw containers kunt bekijken. De metrische gegevens die uit uw containers zijn verzameld, zijn ingedeeld op basis van CPU, geheugen, netwerk en I/O. Datadog splitst metrische gegevens in containers en afbeeldingen. Hieronder ziet u een voor beeld van de werking van de gebruikers interface voor het CPU-gebruik.

![Datadog-gebruikers interface](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Een Datadog-implementatie configureren met marathon
In deze stappen wordt uitgelegd hoe u Datadog-toepassingen configureert en implementeert in uw cluster met marathon. 

Toegang tot uw DC/OS-gebruikers interface via `http://localhost:80/` . In de DC/OS-gebruikers interface navigeert u naar het ' universum ' aan de linkerkant en zoekt u naar ' Datadog ' en klikt u op installeren.

![Datadog-pakket binnen het DC/OS-universum](./media/container-service-monitoring/datadog1.png)

Nu u de configuratie wilt volt ooien, hebt u een Datadog-account of een gratis proef account nodig. Zodra u bent aangemeld bij de Datadog-website, kijkt u naar links en gaat u naar integraties-> en [api's](https://app.datadoghq.com/account/settings#api). 

![Datadog-API-sleutel](./media/container-service-monitoring/datadog2.png)

Voer vervolgens uw API-sleutel in de Datadog-configuratie in het DC/OS-universum in. 

![Datadog-configuratie in het DC/OS-universum](./media/container-service-monitoring/datadog3.png) 

In de bovenstaande configuratie-exemplaren worden ingesteld op 10000000, dus wanneer een nieuw knoop punt aan het cluster wordt toegevoegd, implementeert Datadog automatisch een agent op dat knoop punt. Dit is een tijdelijke oplossing. Nadat u het pakket hebt geïnstalleerd, gaat u terug naar de Datadog-website en vindt u '[Dash boards](https://app.datadoghq.com/dash/list)'. Hier ziet u aangepaste Dash boards en integratie dashboards. Het [docker-dash board](https://app.datadoghq.com/screen/integration/docker) heeft alle metrische gegevens over de container die u nodig hebt voor het controleren van het cluster. 

