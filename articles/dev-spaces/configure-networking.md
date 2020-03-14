---
title: Netwerken configureren voor Azure-ontwikkel ruimten in verschillende netwerk topologieën
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Hierin worden de netwerk vereisten beschreven voor het uitvoeren van Azure-ontwikkel ruimten in azure Kubernetes Services
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, CNI, kubenet, SDN, netwerk
ms.openlocfilehash: 9e32e3b65451dceefaeeaf7faed7c8337797e0b8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265348"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Netwerken configureren voor Azure-ontwikkel ruimten in verschillende netwerk topologieën

Azure dev Spaces worden uitgevoerd op Azure Kubernetes service (AKS)-clusters met de standaard-netwerk configuratie. Als u de netwerk configuratie van uw AKS-cluster wilt wijzigen, bijvoorbeeld door het cluster achter een firewall te plaatsen, netwerk beveiligings groepen te gebruiken of netwerk beleid te gebruiken, moet u aanvullende overwegingen nemen voor het uitvoeren van Azure dev Spaces.

![Configuratie van virtueel netwerk](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configuraties van virtuele netwerken of subnetten

Uw AKS-cluster kan een andere configuratie voor het virtuele netwerk of subnet hebben om binnenkomend of uitgaand verkeer voor uw AKS-cluster te beperken. Het is bijvoorbeeld mogelijk dat uw cluster zich achter een firewall bevindt, zoals Azure Firewall, of u kunt netwerk beveiligings groepen of aangepaste rollen gebruiken voor het beperken van netwerk verkeer.

Azure dev Spaces heeft bepaalde vereisten voor binnenkomend *en* uitgaand netwerk verkeer, en *alleen* binnenkomend verkeer. Als u Azure dev Spaces gebruikt in een AKS-cluster met een virtueel netwerk of een subnet-configuratie waarmee het verkeer voor uw AKS-cluster wordt beperkt, moet u de volgende ingangs-en ingangs-en uitgangs vereisten volgen om Azure dev Spaces te kunnen gebruiken. goed werkt.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Netwerk verkeer vereisten voor ingangs-en uitgangs gegevens

Azure dev Spaces heeft ingangs-en uitgangs verkeer nodig voor de volgende FQDN-tekens:

| FQDN-NAAM                       | Poort       | Gebruiken      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Docker-installatie kopieën ophalen voor Azure dev Spaces |
| gcr.io                     | HTTPS: 443 | Helm-installatie kopieën voor Azure-ontwikkel ruimten ophalen |
| storage.googleapis.com     | HTTPS: 443 | Helm-installatie kopieën voor Azure-ontwikkel ruimten ophalen |
| azds-*. azds. io             | HTTPS: 443 | Om te communiceren met Azure dev Spaces back-upservices voor de Azure dev Spaces-controller. De exacte FQDN vindt u in *dataplaneFqdn* in `USERPROFILE\.azds\settings.json` |

Werk uw firewall of beveiligings configuratie bij om netwerk verkeer toe te staan van en naar alle bovenstaande FQDN-namen. Als u bijvoorbeeld een firewall gebruikt om uw netwerk te beveiligen, moeten de bovenstaande FQDN-namen worden toegevoegd aan de toepassings regel van de firewall om verkeer toe te staan van en naar deze domeinen.

### <a name="ingress-only-network-traffic-requirements"></a>Alleen inkomend netwerk verkeer vereisten

Azure dev Spaces biedt Kubernetes-route ring op naam ruimte niveau en open bare toegang tot services met behulp van een eigen FQDN. Als u beide functies wilt gebruiken, werkt u uw firewall of netwerk configuratie bij zodat open bare binnenkomend verkeer naar het externe IP-adres van de controller van Azure dev Spaces kan worden uitgevoerd in uw cluster. U kunt ook een [interne Load Balancer][aks-internal-lb] maken en een NAT-regel in uw firewall toevoegen om het open bare IP-adres van uw firewall om te zetten in het IP-adres van uw interne Load Balancer. U kunt ook [traefik][traefik-ingress] of [NGINX][nginx-ingress] gebruiken om een aangepaste ingangs controller te maken.

## <a name="aks-cluster-network-requirements"></a>AKS-cluster netwerk vereisten

Met AKS kunt u [netwerk beleid][aks-network-policies] gebruiken voor het beheren van binnenkomend en uitgaand verkeer tussen de verschillende clusters op een cluster en het uitkomen van verkeer van een pod. Azure dev Spaces heeft bepaalde vereisten voor binnenkomend *en* uitgaand netwerk verkeer, en *alleen* binnenkomend verkeer. Als u Azure dev Spaces gebruikt in een AKS-cluster met AKS-netwerk beleid, moet u de volgende ingangs-en uitgangs-en uitvoerings vereisten voor verkeer volgen om Azure-ontwikkel ruimten goed te laten functioneren.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Netwerk verkeer vereisten voor ingangs-en uitgangs gegevens

Met Azure dev Spaces kunt u rechtstreeks communiceren met een pod in een dev-ruimte op uw cluster voor fout opsporing. Als u deze functie wilt gebruiken, voegt u een netwerk beleid toe waarmee binnenkomend en uitgaand verkeer wordt toegestaan aan de IP-adressen van de Azure dev Spaces-infra structuur, die [per regio verschillen][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Alleen inkomend netwerk verkeer vereisten

Azure dev Spaces biedt route ring tussen de verschillende naam ruimten. Bijvoorbeeld: naam ruimten waarvoor Azure dev Spaces zijn ingeschakeld, kunnen een bovenliggende/onderliggende relatie hebben, waardoor netwerk verkeer tussen de verschillende bovenliggende en onderliggende naam ruimtes kan worden gerouteerd. Als u deze functie wilt gebruiken, voegt u een netwerk beleid toe dat verkeer tussen naam ruimten toestaat waarbij netwerk verkeer wordt gerouteerd, zoals bovenliggende/onderliggende naam ruimten. Als de ingangs controller wordt geïmplementeerd in de *azds* -naam ruimte, moet de ingangs controller ook communiceren met een van de Azure-ontwikkel ruimte in een andere naam ruimte. Voor een goede werking van de ingangs controller moet het netwerk verkeer van de *azds* -naam ruimte worden toegestaan in de naam ruimte waar de gewerkte peulen worden uitgevoerd.

## <a name="using-azure-cni"></a>Azure CNI gebruiken

AKS-clusters zijn standaard geconfigureerd voor het gebruik van [kubenet][aks-kubenet] voor netwerken, die werken met Azure dev Spaces. U kunt uw AKS-cluster ook configureren voor het gebruik van [Azure container Networking interface (cni)][aks-cni]. Als u Azure dev Spaces wilt gebruiken met Azure CNI op uw AKS-cluster, moet u het virtuele netwerk en het subnet adres voorzien van Maxi maal 10 privé-IP-adressen voor de peulen die worden geïmplementeerd door Azure dev Spaces. Meer informatie over het toestaan van privé-IP-adressen is beschikbaar in de [AKS Azure cni-documentatie][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Met API server geautoriseerde IP-adresbereiken gebruiken

Met AKS-clusters kunt u extra beveiliging configureren waarmee het IP-adres kan communiceren met uw clusters, bijvoorbeeld met behulp van aangepaste virtuele netwerken of [het beveiligen van toegang tot de API-server met behulp van geautoriseerde IP-bereiken][aks-ip-auth-ranges]. Als u Azure dev Spaces wilt gebruiken wanneer u deze extra beveiliging gebruikt tijdens het [maken][aks-ip-auth-range-create] van het cluster, moet u [extra bereiken op basis van uw regio toestaan][dev-spaces-ip-auth-range-regions]. U kunt ook een bestaand cluster [bijwerken][aks-ip-auth-range-update] om deze extra bereiken toe te staan. U moet ook het IP-adres van alle ontwikkel computers toestaan die verbinding maken met uw AKS-cluster voor fout opsporing om verbinding te maken met uw API-server.

## <a name="using-aks-private-clusters"></a>AKS persoonlijke clusters gebruiken

Op dit moment wordt Azure dev Spaces niet ondersteund met [persoonlijke AKS-clusters][aks-private-clusters].

## <a name="client-requirements"></a>Client vereisten

Azure dev Spaces maakt gebruik van Program ma's aan de client zijde, zoals de Azure dev Spaces CLI-extensie, Visual Studio code extension en Visual Studio-extensie, om te communiceren met uw AKS-cluster voor fout opsporing. Als u de Azure dev Spaces-client-side Tool wilt gebruiken, moet u verkeer van de ontwikkel machines naar het *azds-\*. azds.io-* domein toestaan. Zie *dataplaneFqdn* in `USERPROFILE\.azds\settings.json` voor de exacte FQDN. Als u door [API server geautoriseerde IP-adresbereiken][auth-range-section]gebruikt, moet u ook het IP-adres van alle ontwikkel machines toestaan die verbinding maken met uw AKS-cluster voor fout opsporing om verbinding te maken met uw API-server.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md