---
title: KEUR Een Azure Container Service cluster bewaken met Sysdig
description: Een Azure Container Service-cluster met Sysdig bewaken.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 611d3e6d5ecaf986cc09c550c4aee760ed0a0d98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166140"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>KEUR Een Azure Container Service cluster bewaken met Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In dit artikel implementeren we Sysdig-agents op alle agentknooppunten in uw Azure Container Service-cluster. Voor deze configuratie hebt u een Sysdig-account nodig. 

## <a name="prerequisites"></a>Vereisten
[Implementeer](container-service-deployment.md) en [verbind](../container-service-connect.md) een cluster dat door Azure Container Service is geconfigureerd. Verken de [Marathon-gebruikers interface](container-service-mesos-marathon-ui.md). Ga naar [https://app.sysdigcloud.com](https://app.sysdigcloud.com) om een Sysdig-Cloud account in te stellen. 

## <a name="sysdig"></a>Sysdig
Sysdig is een bewakingsservice waarmee u de containers in uw cluster kunt bewaken. Sysdig is handig bij het oplossen van problemen, maar biedt ook algemene bewakingswaarden voor CPU, netwerk, geheugen en I/O. Met Sysdig kunt u gemakkelijk zien welke containers het hardst werken of in wezen het geheugen en de CPU het meest gebruiken. Deze weergave vindt u in de sectie ‘Overview’ (Overzicht), die zich momenteel in de bètafase bevindt. 

![Gebruikersinterface van Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Een Sysdig-implementatie met Marathon configureren
Deze stappen geven aan hoe u met Marathon Sysdig-toepassingen in uw cluster configureert en implementeert. 

Ga in de DC/OS-gebruikers interface van de DC/OS-gebruikers interface `http://localhost:80/` naar het ' universum ', dat linksonder is en zoek naar ' Sysdig '.

![Sysdig in het DC/OS-universum](./media/container-service-monitoring-sysdig/sysdig1.png)

Om nu de configuratie te voltooien, hebt u een Sysdig-cloudaccount of gratis proefaccount nodig. Nadat u bent aangemeld bij de Sysdig-cloudwebsite, klikt u op uw gebruikersnaam. Op de pagina ziet u uw ‘toegangssleutel’. 

![Sysdig API-sleutel](./media/container-service-monitoring-sysdig/sysdig2.png) 

Voer vervolgens in het DC/OS-universum uw toegangssleutel in de Sysdig-configuratie in. 

![Sysdig-configuratie in het DC/OS-universum](./media/container-service-monitoring-sysdig/sysdig3.png)

Stel nu de exemplaren in op 10000000, zodat telkens wanneer aan het cluster een nieuw knooppunt wordt toegevoegd, Sysdig automatisch op dat nieuwe knooppunt een agent implementeert. Dit is een tijdelijke oplossing om ervoor te zorgen dat Sysdig op alle nieuwe agents binnen het cluster wordt geïmplementeerd. 

![Sysdig-configuratie in de instanties van het DC/OS-universum](./media/container-service-monitoring-sysdig/sysdig4.png)

Nadat u het pakket hebt geïnstalleerd, navigeert u terug naar de Sysdig-gebruikersinterface en kunt u de verschillende gebruikswaarden voor de containers in uw cluster verkennen. 

U kunt ook Mesos- en Marathon-specifieke dashboards installeren via de [wizard Nieuw dashboard](https://app.sysdigcloud.com/#/dashboards/new).
