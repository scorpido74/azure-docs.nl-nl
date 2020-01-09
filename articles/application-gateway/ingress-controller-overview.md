---
title: Wat is Azure-toepassing gateway ingangs controller?
description: Dit artikel bevat een inleiding tot de Application Gateway ingangs controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 838145f8573e11deff8566c932a9c73c6f59f03b
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561657"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Wat is Application Gateway ingangs controller?
De Application Gateway ingangs controller (AGIC) is een Kubernetes-toepassing, waardoor [Azure Kubernetes service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) -klanten de systeem eigen [Application Gateway](https://azure.microsoft.com/services/application-gateway/) N7-Load Balancer van Azure kunnen gebruiken om cloud software op internet beschikbaar te stellen. AGIC bewaakt het Kubernetes-cluster dat wordt gehost op een Application Gateway, zodat de geselecteerde services worden blootgesteld aan Internet.

De ingangs controller wordt uitgevoerd in een eigen pod op de AKS van de klant. AGIC bewaakt een subset van Kubernetes-resources op wijzigingen. De status van het AKS-cluster wordt omgezet naar Application Gateway specifieke configuratie en toegepast op de [Azure Resource Manager (arm)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Voor delen van Application Gateway ingangs controller
Met AGIC kan uw implementatie meerdere AKS-clusters beheren met één Application Gateway ingangs controller. AGIC zorgt er ook voor dat een ander load balancer/openbaar IP-adres vóór AKS-cluster niet meer nodig is en dat meerdere hops in uw DataPath worden voor komen voordat aanvragen het AKS-cluster bereiken. Application Gateway gespeert naar een doel dat rechtstreeks gebruikmaakt van het privé-IP-adres en geen NodePort-of KubeProxy-Services vereist. Dit brengt ook betere prestaties voor uw implementaties.

Ingangs controller wordt alleen ondersteund door Standard_v2 en WAF_v2 Sku's, waarmee u uw voor delen automatisch kunt schalen. Application Gateway kan reageren op een toename of afname in de belasting van het verkeer en de schaal dienovereenkomstig, zonder dat er resources van uw AKS-cluster worden gebruikt.

Het gebruik van Application Gateway naast AGIC biedt ook bescherming voor uw AKS-cluster door TLS-beleid en functionaliteit voor Web Application firewall (WAF) te bieden.

![Azure-toepassing gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC wordt geconfigureerd via de [ingangs bron](https://kubernetes.io/docs/user-guide/ingress/)van Kubernetes, samen met Service en implementaties/peul. Het biedt een aantal functies, met Azure native Application Gateway N7 load balancer. Een paar van de volgende namen:
  - URL-routering
  - Affiniteit op basis van cookies
  - SSL-beëindiging
  - End-to-end SSL
  - Ondersteuning voor open bare, persoonlijke en hybride websites
  - Geïntegreerde Web Application Firewall

AGIC kan meerdere naam ruimten verwerken en heeft ProhibitedTargets, wat betekent dat AGIC de Application Gateway kan configureren voor AKS-clusters zonder dat dit van invloed is op andere bestaande back-end. 

## <a name="next-steps"></a>Volgende stappen

- [**Ontwikkel-implementatie**](ingress-controller-install-new.md): instructies voor het installeren van AGIC, AKS en Application Gateway op een lege infra structuur.
- [**Brownfield-implementatie**](ingress-controller-install-existing.md): Installeer AGIC op een bestaande AKS en Application Gateway.

