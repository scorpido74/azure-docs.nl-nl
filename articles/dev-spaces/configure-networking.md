---
title: Netwerken configureren voor Azure Dev Spaces in verschillende netwerktopologieën
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Beschrijft de netwerkvereisten voor het uitvoeren van Azure Dev Spaces in Azure Kubernetes Services
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, CNI, kubenet, SDN, netwerk
ms.openlocfilehash: 82d046aa36fe9caf6337aa7f58ca0db525062283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240576"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Netwerken configureren voor Azure Dev Spaces in verschillende netwerktopologieën

Azure Dev Spaces draait op AKS-clusters (Azure Kubernetes Service) met de standaardnetwerkconfiguratie. Als u de netwerkconfiguratie van uw AKS-cluster wilt wijzigen, zoals het plaatsen van het cluster achter een firewall, het gebruik van netwerkbeveiligingsgroepen of het gebruik van netwerkbeleid, moet u aanvullende overwegingen opnemen voor het uitvoeren van Azure Dev Spaces.

![Virtuele netwerkconfiguratie](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Virtuele netwerk- of subnetconfiguraties

Uw AKS-cluster heeft mogelijk een andere virtuele netwerk- of subnetconfiguratie om het binnendringende of uitgaande verkeer voor uw AKS-cluster te beperken. Het cluster bevindt zich bijvoorbeeld achter een firewall, zoals Azure Firewall, of u netwerkbeveiligingsgroepen of aangepaste rollen gebruiken om het netwerkverkeer te beperken.

Azure Dev Spaces heeft bepaalde vereisten voor *Ingress- en Egress-netwerkverkeer* en alleen verkeer *in Ingress.* Als u Azure Dev Spaces gebruikt op een AKS-cluster met een virtuele netwerk- of subnetconfiguratie die het verkeer voor uw AKS-cluster beperkt, moet u alleen de volgende insgress volgen en verkeersvereisten binnendringen en binnendringen om Azure Dev Spaces Functioneren.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Vereisten voor invallenen en uitwijken van netwerkverkeer

Azure Dev Spaces heeft in- en uitgangsverkeer nodig voor het volgen van FQDN's:

| FQDN                       | Poort       | Gebruiken      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Docker-afbeeldingen voor Azure Dev Spaces trekken |
| gcr.io                     | HTTPS: 443 | Helmafbeeldingen voor Azure Dev Spaces trekken |
| storage.googleapis.com     | HTTPS: 443 | Helmafbeeldingen voor Azure Dev Spaces trekken |
| azds-*.azds.io             | HTTPS: 443 | Communiceren met Azure Dev Spaces backendservices voor de Azure Dev Spaces-controller. De exacte FQDN is te vinden in *dataplaneFqdn* in`USERPROFILE\.azds\settings.json` |

Werk uw firewall of beveiligingsconfiguratie bij om netwerkverkeer van en naar alle bovenstaande FQDN's toe te staan. Als u bijvoorbeeld een firewall gebruikt om uw netwerk te beveiligen, moeten de bovenstaande FQDN's worden toegevoegd aan de toepassingsregel van de firewall om verkeer van en naar deze domeinen toe te staan.

### <a name="ingress-only-network-traffic-requirements"></a>Alleen netwerkverkeersvereisten binnendringen

Azure Dev Spaces biedt Kubernetes namespace-level routing en openbare toegang tot services met behulp van zijn eigen FQDN. Werk uw firewall- of netwerkconfiguratie bij om ervoor te zorgen dat het externe IP-adres van de Azure Dev Spaces-binnenvallende controller op uw cluster wordt ingeschakeld. U ook een [interne load balancer][aks-internal-lb] maken en een NAT-regel toevoegen in uw firewall om het openbare IP van uw firewall te vertalen naar het IP-adres van uw interne load balancer. U ook [traefik][traefik-ingress] of [NGINX][nginx-ingress] gebruiken om een aangepaste ingress-controller te maken.

## <a name="aks-cluster-network-requirements"></a>AKS-clusternetwerkvereisten

Met AKS u [netwerkbeleid][aks-network-policies] gebruiken om het binnendringen de en het uitreizigersverkeer tussen pods op een cluster te regelen en verkeer uit een pod te leiden. Azure Dev Spaces heeft bepaalde vereisten voor *Ingress- en Egress-netwerkverkeer* en alleen verkeer *in Ingress.* Als u Azure Dev Spaces gebruikt op een AKS-cluster met AKS-netwerkbeleid, moet u alleen de volgende invallen volgen en verkeersvereisten binnendringen en binnendringen om Azure Dev Spaces goed te laten functioneren.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Vereisten voor invallenen en uitwijken van netwerkverkeer

Met Azure Dev Spaces u rechtstreeks communiceren met een pod in een dev-ruimte op uw cluster voor foutopsporing. Als u deze functie wilt gebruiken, voegt u een netwerkbeleid toe waarmee u communiceren naar de IP-adressen van de Azure Dev Spaces-infrastructuur, die [per regio verschillen.][dev-spaces-ip-auth-range-regions]

### <a name="ingress-only-network-traffic-requirements"></a>Alleen netwerkverkeersvereisten binnendringen

Azure Dev Spaces biedt routering tussen pods in naamruimten. Naamruimten met Azure Dev Spaces ingeschakeld, kunnen bijvoorbeeld een bovenliggende/onderliggende relatie hebben, waardoor netwerkverkeer kan worden gerouteerd tussen pods over de naamruimten van de bovenliggende en onderliggende gebieden. Azure Dev Spaces legt ook serviceeindpunten bloot met behulp van een eigen FQDN. Als u verschillende manieren wilt configureren om services bloot te leggen en hoe dit van invloed is op de routering op naamruimteniveau, [raadpleegt u verschillende eindpuntopties][endpoint-options].

## <a name="using-azure-cni"></a>Azure CNI gebruiken

Standaard zijn AKS-clusters geconfigureerd om [kubenet][aks-kubenet] te gebruiken voor netwerken, dat werkt met Azure Dev Spaces. U uw AKS-cluster ook configureren om [De Azure Container Networking Interface (CNI)][aks-cni]te gebruiken. Als u Azure Dev Spaces met Azure CNI wilt gebruiken op uw AKS-cluster, u uw virtuele netwerk- en subnetadresruimten tot 10 privé-IP-adressen toestaan voor pods die zijn geïmplementeerd door Azure Dev Spaces. Meer informatie over het toestaan van privé-IP-adressen is beschikbaar in de [AKS Azure CNI-documentatie.][aks-cni-ip-planning]

## <a name="using-api-server-authorized-ip-ranges"></a>Api-servergeautoriseerde IP-bereiken gebruiken

Met AKS-clusters u extra beveiliging configureren die beperkt welke IP-adres met uw clusters kan communiceren, bijvoorbeeld met behulp van aangepaste virtuele netwerken of [het beveiligen van toegang tot de API-server met geautoriseerde IP-bereiken.][aks-ip-auth-ranges] Als u Azure Dev Spaces wilt gebruiken wanneer u deze extra beveiliging gebruikt tijdens [het maken van][aks-ip-auth-range-create] uw cluster, moet u extra bereiken toestaan op basis van uw [regio.][dev-spaces-ip-auth-range-regions] U ook een bestaand cluster [bijwerken][aks-ip-auth-range-update] om deze extra bereiken toe te staan. U moet ook toestaan dat het IP-adres van alle ontwikkelmachines die verbinding maken met uw AKS-cluster voor foutopsporing verbinding maken met uw API-server.

## <a name="using-aks-private-clusters"></a>Aks-privéclusters gebruiken

Op dit moment wordt Azure Dev Spaces niet ondersteund met [AKS-privéclusters.][aks-private-clusters]

## <a name="using-different-endpoint-options"></a>Verschillende eindpuntopties gebruiken

Azure Dev Spaces heeft de mogelijkheid om eindpunten bloot te leggen voor uw services die op AKS worden uitgevoerd. Wanneer u Azure Dev Spaces inschakelt in uw cluster, hebt u de volgende opties voor het configureren van het eindpunttype voor uw cluster:

* Een *openbaar* eindpunt, dat de standaardinstelling is, implementeert een invallende controller met een openbaar IP-adres. Het openbare IP-adres wordt geregistreerd op de DNS van het cluster, waardoor het publiek toegang heeft tot uw services via een URL. U deze `azds list-uris`URL bekijken via.
* Een *privéeindpunt* implementeert een invallende controller met een privé-IP-adres. Met een privé-IP-adres is de load balancer voor uw cluster alleen toegankelijk vanuit het virtuele netwerk van het cluster. Het privé-IP-adres van de load balancer wordt geregistreerd op de DNS van het cluster, zodat services binnen het virtuele netwerk van het cluster toegankelijk zijn via een URL. U deze `azds list-uris`URL bekijken via.
* Als *u geen instelling voor* de eindpuntoptie instelt, wordt er geen invallende controller geïmplementeerd. Als er geen ingress-controller is geïmplementeerd, werken de [routeringsmogelijkheden azure Dev Spaces][dev-spaces-routing] niet. Optioneel u uw eigen ingress controller-oplossing implementeren met behulp van [traefik][traefik-ingress] of [NGINX,][nginx-ingress]waardoor de routeringsmogelijkheden weer kunnen werken.

Als u de optie Eindpunt wilt configureren, gebruikt u *-e* of *-eindpunt* wanneer u Azure Dev Spaces inschakelt in uw cluster. Bijvoorbeeld:

> [!NOTE]
> De optie Eindpunt vereist dat u Azure CLI-versie 2.2.0 of hoger uitvoert. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Clientvereisten

Azure Dev Spaces maakt gebruik van client-side tooling, zoals de Azure Dev Spaces CLI-extensie, visual studiocode-extensie en Visual Studio-extensie, om te communiceren met uw AKS-cluster voor foutopsporing. Als u de hulpprogramma's aan de clientzijde van Azure Dev Spaces wilt gebruiken, u verkeer van de ontwikkelmachines naar het *\*azds-azds.io-domein* toestaan. Zie *dataplaneFqdn* in `USERPROFILE\.azds\settings.json` voor de exacte FQDN. Als u [api-servergeautoriseerde IP-bereiken gebruikt,][auth-range-section]moet u ook het IP-adres van alle ontwikkelmachines die verbinding maken met uw AKS-cluster toestaan voor foutopsporing om verbinding te maken met uw API-server.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Teamontwikkeling in Azure Dev Spaces][team-quickstart]

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
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md