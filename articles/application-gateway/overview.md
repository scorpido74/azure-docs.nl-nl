---
title: Wat is Azure Application Gateway?
description: Ontdek hoe u een Azure-toepassingsgateway kunt gebruiken voor het beheren van webverkeer naar uw toepassing.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78302573"
---
# <a name="what-is-azure-application-gateway"></a>Wat is Azure Application Gateway?

Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Traditionele load balancers werken op de transportlaag (OSI-laag 4 - TCP en UDP) en routeren verkeer op basis van IP-bronadres en een bronpoort naar een IP-doeladres en doelpoort.

Application Gateway kan routeringsbeslissingen nemen op basis van extra kenmerken van een HTTP-aanvraag, bijvoorbeeld URI-pad of hostheaders. U kunt bijvoorbeeld verkeer op basis van de binnenkomende URL routeren. Dus als `/images` de binnenkomende URL is, kunt u verkeer routeren naar een specifieke set servers (ook wel een pool genoemd) die is geconfigureerd voor installatiekopieën. Als `/video` dit in de URL staat, wordt dat verkeer doorgestuurd naar een andere groep die is geoptimaliseerd voor video's.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Dit type routering staat bekend al taakverdeling op de toepassingslaag (OSI-laag 7). Azure Application Gateway kan URL-gebaseerde routering en meer uitvoeren.

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Zie [Wat is Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Zie [Wat is Traffic Manager als](../traffic-manager/traffic-manager-overview.md) u op zoek bent naar globale DNS-taakverdeling? Uw end-to-end scenario's kunnen baat hebben bij het combineren van deze oplossingen.
>
> Zie [Overzicht van opties voor taakverdeling in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)voor een vergelijking met azure-taakverdelingsopties.

## <a name="features"></a>Functies

Zie [Azure Application Gateway-functies](features.md)voor meer informatie over functies van application gateway.

## <a name="pricing-and-sla"></a>Prijzen en SLA

Zie [Prijsbepaling van toepassingsgateways](https://azure.microsoft.com/pricing/details/application-gateway/)voor prijsinformatie van application gateway.

Zie Sla van application gateway SLA voor SLA-gegevens van [toepassinggateway.](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

## <a name="next-steps"></a>Volgende stappen

Afhankelijk van uw vereisten en omgeving u een testtoepassingsgateway maken met behulp van de Azure-portal, Azure PowerShell of Azure CLI.

- [Snelstart: Webverkeer omleiden met Azure Application Gateway - Azure Portal](quick-create-portal.md)
- [Snelstart: webverkeer omleiden met Azure Application Gateway - Azure PowerShell](quick-create-powershell.md)
- [Snelstart: webverkeer omleiden met Azure Application Gateway - Azure CLI](quick-create-cli.md)
