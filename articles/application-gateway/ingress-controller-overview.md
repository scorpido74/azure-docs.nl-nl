---
title: Wat is Azure Application Gateway Ingress Controller?
description: Dit artikel geeft een inleiding tot wat Application Gateway Ingress Controller is.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335805"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Wat is Application Gateway Ingress Controller?
De Application Gateway Ingress Controller (AGIC) is een Kubernetes-toepassing, die het mogelijk maakt voor [Azure Kubernetes Service (AKS)-klanten](https://azure.microsoft.com/services/kubernetes-service/) om azure's native [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 load-balancer te gebruiken om cloudsoftware bloot te stellen aan het internet. AGIC bewaakt het Kubernetes-cluster waarop het wordt gehost en werkt een Application Gateway voortdurend bij, zodat geselecteerde services worden blootgesteld aan het internet.

De Ingress Controller draait in zijn eigen pod op de AKS van de klant. AGIC controleert een subset van Kubernetes Resources op wijzigingen. De status van het AKS-cluster wordt vertaald naar de specifieke configuratie van de Toepassingsgateway en toegepast op de [Azure Resource Manager (ARM).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="benefits-of-application-gateway-ingress-controller"></a>Voordelen van Application Gateway Ingress Controller
Met AGIC kan uw implementatie meerdere AKS-clusters beheren met één Application Gateway Ingress-controller. AGIC helpt ook de noodzaak te elimineren om een ander load balancer/public IP voor AKS-cluster te hebben en voorkomt meerdere hopin uw gegevenspad voordat aanvragen het AKS-cluster bereiken. Application Gateway praat rechtstreeks met pods met hun privé-IP en vereist geen NodePort- of KubeProxy-services. Dit brengt ook betere prestaties aan uw implementaties.

Ingress Controller wordt uitsluitend ondersteund door Standard_v2 en WAF_v2 SKU's, wat ook voordelen biedt voor automatisch schalen. Application Gateway kan reageren als reactie op een toename of afname van de verkeersbelasting en -schaal dienovereenkomstig, zonder dat er resources uit uw AKS-cluster worden verbruikt.

Het gebruik van Application Gateway naast AGIC helpt ook uw AKS-cluster te beschermen door TLS-beleid en WAF-functionaliteit (Web Application Firewall) te bieden.

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC is geconfigureerd via de Kubernetes [Ingress-bron,](https://kubernetes.io/docs/user-guide/ingress/)samen met Service en Implementaties/Pods. Het biedt een aantal functies, gebruikmakend azure's native Application Gateway L7 load balancer. Om er een paar te noemen:
  - URL-routering
  - Affiniteit op basis van cookies
  - TLS-beëindiging
  - End-to-end TLS
  - Ondersteuning voor openbare, particuliere en hybride websites
  - Geïntegreerde firewall voor webtoepassingen

AGIC kan meerdere naamruimten verwerken en heeft ForbiddTargets, wat betekent dat AGIC de Application Gateway specifiek voor AKS-clusters kan configureren zonder dat dit gevolgen heeft voor andere bestaande backends. 

## <a name="next-steps"></a>Volgende stappen

- [**Greenfield Deployment:**](ingress-controller-install-new.md)Instructies voor het installeren van AGIC, AKS en Application Gateway op een infrastructuur met lege leien.
- [**Brownfield Deployment**](ingress-controller-install-existing.md): Installeer AGIC op een bestaande AKS en Application Gateway.

