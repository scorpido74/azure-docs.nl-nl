---
title: Veelgestelde vragen over Azure dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Antwoorden vinden op enkele veelgestelde vragen over Azure dev Spaces
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: 2baab0812061bec7dcf08d35056804313d873889
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482296"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Veelgestelde vragen over Azure dev Spaces

Dit is een oplossing voor veelgestelde vragen over Azure dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Welke Azure-regio's bevatten momenteel Azure dev Spaces?

Zie [ondersteunde regio's en configuraties][supported-regions]voor een volledige lijst met beschik bare regio's.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Kan ik Azure dev Spaces gebruiken zonder een openbaar IP-adres?

Nee, u kunt geen Azure dev-ruimten inrichten op een AKS-cluster zonder een openbaar IP-adres. Er is een openbaar IP-adres [nodig door Azure dev Spaces voor route ring][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Kan ik mijn eigen ingangen gebruiken met Azure dev Spaces?

Ja, u kunt uw eigen inkomend verkeer configureren aan de zijde die door Azure dev Spaces wordt gemaakt. U kunt bijvoorbeeld [traefik][ingress-traefik]gebruiken.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Kan ik HTTPS gebruiken met Azure dev Spaces?

Ja, u kunt uw eigen inkomend verkeer configureren met HTTPS met behulp van [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kan ik Azure dev Spaces gebruiken in een cluster dat gebruikmaakt van CNI in plaats van kubenet? 

Ja, u kunt Azure dev Spaces gebruiken in een AKS-cluster dat gebruikmaakt van CNI voor netwerken. U kunt bijvoorbeeld Azure dev Spaces gebruiken in een AKS-cluster met [bestaande Windows-containers][windows-containers], die gebruikmaken van cni voor netwerken.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Kan ik Azure dev Spaces gebruiken met Windows-containers?

Momenteel is Azure dev Spaces alleen bedoeld om te worden uitgevoerd op Linux en alleen knoop punten, maar u kunt Azure dev-ruimten uitvoeren op een AKS-cluster met [bestaande Windows-containers][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Kan ik Azure dev Spaces gebruiken op AKS-clusters waarop de door API server geautoriseerde IP-adresbereiken zijn ingeschakeld?

Ja, u kunt Azure dev Spaces gebruiken in AKS-clusters met [API-server geautoriseerde IP-][aks-auth-range] adresbereiken ingeschakeld. Wanneer u uw cluster [maakt][aks-auth-range-create] , moet u [extra bereiken op basis van uw regio toestaan][aks-auth-range-ranges]. U kunt ook een bestaand cluster [bijwerken][aks-auth-range-update] om deze extra bereiken toe te staan.

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Kan ik Azure dev Spaces gebruiken in AKS-clusters met beperkt uitgaand verkeer voor cluster knooppunten?

Ja, u kunt Azure dev Spaces gebruiken in AKS-clusters met beperkt uitgaand [verkeer voor cluster knooppunten][aks-restrict-egress-traffic] die zijn ingeschakeld wanneer de volgende FQDN-namen zijn toegestaan:

| FQDN-NAAM                                    | Poort      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Voor het ophalen van images voor Linux alpine en andere Azure dev Spaces |
| gcr.io | HTTP: 443 | Helm/Tiller-installatie kopieën ophalen |
| storage.googleapis.com | HTTP: 443 | Helm/Tiller-installatie kopieën ophalen |
| azds-<guid>.<location>. azds.io | HTTPS:443 | Om te communiceren met Azure dev Spaces back-upservices voor uw controller. De exacte FQDN kan worden gevonden in de ' dataplaneFqdn ' in% USERPROFILE%\.azds\settings.json |


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md