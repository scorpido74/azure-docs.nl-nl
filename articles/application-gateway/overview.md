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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78302573"
---
# <a name="what-is-azure-application-gateway"></a>Wat is Azure Application Gateway?

Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Traditionele load balancers werken op de transportlaag (OSI-laag 4 - TCP en UDP) en routeren verkeer op basis van IP-bronadres en een bronpoort naar een IP-doeladres en doelpoort.

Application Gateway kunt routerings beslissingen nemen op basis van aanvullende kenmerken van een HTTP-aanvraag, bijvoorbeeld URI-pad of hostheaders. U kunt bijvoorbeeld verkeer op basis van de binnenkomende URL routeren. Dus als `/images` de binnenkomende URL is, kunt u verkeer routeren naar een specifieke set servers (ook wel een pool genoemd) die is geconfigureerd voor installatiekopieën. Als `/video` zich in de URL bevindt, wordt dat verkeer doorgestuurd naar een andere groep die is geoptimaliseerd voor Video's.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Dit type routering staat bekend al taakverdeling op de toepassingslaag (OSI-laag 7). Azure Application Gateway kan URL-gebaseerde routering en meer uitvoeren.

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Zie [Wat is Azure Load Balancer?](../load-balancer/load-balancer-overview.md) als u behoefte hebt aan hoge prestaties en lage latentie, laag-4 taak verdeling. Als u op zoek bent naar globale DNS-taak verdeling, raadpleegt u [Wat is Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Uw end-to-end-scenario's kunnen van pas komen bij het combi neren van deze oplossingen.
>
> Zie [overzicht van opties voor taak verdeling in azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)voor een vergelijking van Azure-opties voor taak verdeling.

## <a name="features"></a>Functies

Zie [Azure-toepassing gateway-functies](features.md)voor meer informatie over Application Gateway-functies.

## <a name="pricing-and-sla"></a>Prijzen en SLA

Zie [Application Gateway prijzen](https://azure.microsoft.com/pricing/details/application-gateway/)voor Application Gateway prijs informatie.

Zie [Application Gateway Sla](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)voor Application Gateway Sla-informatie.

## <a name="next-steps"></a>Volgende stappen

Afhankelijk van uw vereisten en omgeving kunt u een test Application Gateway maken met behulp van de Azure Portal, Azure PowerShell of Azure CLI.

- [Snelstart: Webverkeer omleiden met Azure Application Gateway - Azure Portal](quick-create-portal.md)
- [Snelstart: webverkeer omleiden met Azure Application Gateway - Azure PowerShell](quick-create-powershell.md)
- [Snelstart: webverkeer omleiden met Azure Application Gateway - Azure CLI](quick-create-cli.md)
