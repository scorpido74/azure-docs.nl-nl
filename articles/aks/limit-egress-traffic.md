---
title: Uitgaand verkeer beperken in azure Kubernetes service (AKS)
description: Meer informatie over welke poorten en adressen vereist zijn voor het beheren van uitgaand verkeer in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 9476290669606f6eb6c56b51497f3026b9613698
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034952"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Preview-uitgaand verkeer voor cluster knooppunten beperken en toegang beheren tot vereiste poorten en services in azure Kubernetes service (AKS)

Standaard hebben AKS-clusters onbeperkte uitgaande (uitgaand) Internet toegang. Met dit niveau van netwerk toegang kunnen knoop punten en services die u uitvoert om toegang te krijgen tot externe bronnen als dat nodig is. Als u uitgaand verkeer wilt beperken, moet een beperkt aantal poorten en adressen toegankelijk zijn voor het onderhouden van goede cluster onderhouds taken. Uw cluster wordt vervolgens zodanig geconfigureerd dat alleen basis installatie kopieën van het base systeem worden gebruikt van micro soft Container Registry (MCR) of Azure Container Registry (ACR), niet voor externe open bare opslag plaatsen. U moet uw voorkeurs firewall en beveiligings regels configureren om deze vereiste poorten en adressen toe te staan.

In dit artikel wordt beschreven welke netwerk poorten en FQDN-namen (FULLy Qualified Domain names) vereist zijn en optioneel als u uitgaand verkeer in een AKS-cluster beperkt.  Deze functie is momenteel beschikbaar als preview-product.

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service opt-in. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klant ondersteuning, op basis van de beste inspanningen. Daarom zijn deze functies niet bedoeld voor productie gebruik. Raadpleeg de volgende artikelen met technische ondersteuning voor meer informatie.
>
> * [AKS-ondersteunings beleid][aks-support-policies]
> * [Veelgestelde vragen over ondersteuning voor Azure][aks-faq]

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.66 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Als u een AKS-cluster wilt maken dat uitgaand verkeer kan beperken, schakelt u eerst een functie vlag in voor uw abonnement. Met deze functie registratie kunt u AKS-clusters configureren die u maakt om basis systeem container installatie kopieën van MCR of ACR te gebruiken. Als u de functie vlag *AKSLockingDownEgressPreview* wilt registreren, gebruikt u de opdracht [AZ feature REGI ster][az-feature-register] , zoals weer gegeven in het volgende voor beeld:

> [!CAUTION]
> Wanneer u een functie op een abonnement registreert, kunt u de registratie van die functie op dit moment niet ongedaan maken. Nadat u enkele preview-functies hebt ingeschakeld, kunnen standaard waarden worden gebruikt voor alle AKS-clusters die vervolgens in het abonnement zijn gemaakt. Schakel geen preview-functies in voor productie abonnementen. Gebruik een afzonderlijk abonnement om Preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Het duurt enkele minuten voordat de status is *geregistreerd*. U kunt de registratie status controleren met de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Overzicht van uitgaand verkeer

Voor beheer-en operationele doel einden moeten knoop punten in een AKS-cluster toegang hebben tot bepaalde poorten en FQDN-namen (Fully Qualified Domain Name). Deze acties zijn mogelijk te communiceren met de API-server of voor het downloaden en installeren van kern Kubernetes-cluster onderdelen en beveiligings updates voor knoop punten. Uitgaand verkeer van het internet is standaard niet beperkt tot een knoop punt in een AKS-cluster. Het cluster kan installatie kopieën van het basis systeem container uit externe opslag plaatsen halen.

Als u de beveiliging van uw AKS-cluster wilt verhogen, kunt u uitgaand verkeer beperken. Het cluster is geconfigureerd voor het ophalen van basis installatie kopieën van het systeem container van MCR of ACR. Als u het uitgaande verkeer op deze manier vergrendelt, moet u specifieke poorten en FQDN-s opgeven zodat de AKS-knoop punten correct kunnen communiceren met de vereiste externe services. Zonder deze geautoriseerde poorten en FQDN-namen kunnen uw AKS-knoop punten niet communiceren met de API-server of kern onderdelen installeren.

U kunt [Azure firewall][azure-firewall] of een firewall apparaat van een derde partij gebruiken om uw uitgangs verkeer te beveiligen en deze vereiste poorten en adressen te definiëren. AKS maakt deze regels niet automatisch voor u. De volgende poorten en adressen zijn als referentie bij het maken van de juiste regels in uw netwerk firewall.

> [!IMPORTANT]
> Wanneer u Azure Firewall gebruikt om uitgaand verkeer te beperken en een door de gebruiker gedefinieerde route (UDR) te maken om al het uitgaande verkeer af te dwingen, moet u een geschikte DNAT-regel in de firewall maken om binnenkomend verkeer op de juiste wijze toe te staan. Als Azure Firewall wordt gebruikt met een UDR, wordt de ingangs instellingen voor asymmetrische route ring onderbroken. (Het probleem treedt op omdat het AKS-subnet een standaard route heeft die naar het privé-IP-adres van de firewall gaat, maar u een open bare load balancer-binnenkomend of Kubernetes service van het type: LoadBalancer). In dit geval wordt het binnenkomende load balancer verkeer ontvangen via het open bare IP-adres, maar het retour pad gaat via het privé-IP-adres van de firewall. Omdat de firewall stateful is, wordt het terugkerende pakket neergezet omdat de firewall niet op de hoogte is van een vastgestelde sessie. Zie [Azure firewall integreren met Azure Standard Load Balancer](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb)voor meer informatie over het integreren van Azure Firewall met uw ingangs-of service Load Balancer.
>

In AKS zijn er twee sets poorten en adressen:

* De [vereiste poorten en het adres voor AKS-clusters](#required-ports-and-addresses-for-aks-clusters) zijn de minimale vereisten voor het toestaan van uitgaand uitgaand verkeer.
* De [optionele aanbevolen adressen en poorten voor AKS-clusters](#optional-recommended-addresses-and-ports-for-aks-clusters) zijn niet vereist voor alle scenario's, maar integratie met andere services, zoals Azure monitor, werkt niet correct. Bekijk deze lijst met optionele poorten en FQDN-namen en autoriseer de services en onderdelen die worden gebruikt in uw AKS-cluster.

> [!NOTE]
> Het beperken van uitgaand verkeer werkt alleen op nieuwe AKS-clusters die zijn gemaakt nadat u de registratie van de functie vlag hebt ingeschakeld. Voor bestaande clusters [voert u een cluster upgrade bewerking uit][aks-upgrade] met `az aks upgrade` behulp van de opdracht voordat u het uitgaande verkeer beperkt.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Vereiste poorten en adressen voor AKS-clusters

De volgende uitgaande poorten/netwerk regels zijn vereist voor een AKS-cluster:

* TCP-poort *443*
* TCP-poort *9000* en TCP-poort *22* voor de tunnel-front-pod om te communiceren met het tunnel einde op de API-server.
    * Zie voor meer specifieke informatie over het * *. hcp.\< Location\>. azmk8s.io* en * *. tun.\< locatie\>. azmk8s.io* -adressen in de volgende tabel.

De volgende FQDN/toepassings regels zijn vereist:

| FQDN-NAAM                       | Port      | Gebruiken      |
|----------------------------|-----------|----------|
| *. hcp. \<Location\>. azmk8s.io | HTTPS: 443, TCP: 22, TCP: 9000 | Dit adres is het API-server eindpunt. *Vervang\<locatie\>* door de regio waar uw AKS-cluster is geïmplementeerd. |
| *.tun. \<Location\>. azmk8s.io | HTTPS: 443, TCP: 22, TCP: 9000 | Dit adres is het API-server eindpunt. *Vervang\<locatie\>* door de regio waar uw AKS-cluster is geïmplementeerd. |
| aksrepos.azurecr.io        | HTTPS:443 | Dit adres is vereist voor toegang tot afbeeldingen in Azure Container Registry (ACR). Dit REGI ster bevat installatie kopieën/grafieken van derden (bijvoorbeeld Metrics server, Core DNS, enzovoort) die vereist zijn voor de werking van het cluster tijdens de upgrade en schaal van het cluster|
| *.blob.core.windows.net    | HTTPS:443 | Dit adres is de back-end-Store voor installatie kopieën die zijn opgeslagen in ACR. |
| mcr.microsoft.com          | HTTPS:443 | Dit adres is vereist voor toegang tot afbeeldingen in micro soft Container Registry (MCR). Dit REGI ster bevat installatie kopieën/grafieken van de eerste partij (bijvoorbeeld Moby, enzovoort) die vereist zijn voor het functioneren van het cluster tijdens de upgrade en schaal van het cluster |
| *.cdn.mscr.io              | HTTPS:443 | Dit adres is vereist voor de MCR-opslag die wordt ondersteund door het Azure Content Delivery Network (CDN). |
| management.azure.com       | HTTPS:443 | Dit adres is vereist voor Kubernetes GET/PUT-bewerkingen. |
| login.microsoftonline.com  | HTTPS:443 | Dit adres is vereist voor Azure Active Directory-verificatie. |
| ntp.ubuntu.com             | UDP:123   | Dit adres is vereist voor NTP-tijd synchronisatie op Linux-knoop punten. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Optionele aanbevolen adressen en poorten voor AKS-clusters

* UDP-poort *53* voor DNS

De volgende FQDN/toepassings regels worden aanbevolen voor een juiste werking van AKS-clusters:

| FQDN-NAAM                                    | Port      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com                           | HTTP:80   | Met dit adres kunnen Linux-cluster knooppunten de vereiste beveiligings patches en updates downloaden. |
| packages.microsoft.com                  | HTTPS:443 | Dit adres is de micro soft packages-opslag plaats die wordt gebruikt voor *apt-get-* bewerkingen in de cache. |
| dc.services.visualstudio.com            | HTTPS:443 | Aanbevolen voor juiste metrische gegevens en bewaking met behulp van Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Aanbevolen voor juiste metrische gegevens en bewaking met behulp van Azure Monitor. |
| *. monitoring.azure.com                  | HTTPS:443 | Aanbevolen voor juiste metrische gegevens en bewaking met behulp van Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Dit adres wordt gebruikt voor een juiste werking van Azure Policy (momenteel als preview-versie in AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Dit adres wordt gebruikt voor juiste installatie van Stuur Programma's en bewerkingen op op GPU gebaseerde knoop punten. |
| nvidia.github.io                        | HTTPS:443 | Dit adres wordt gebruikt voor juiste installatie van Stuur Programma's en bewerkingen op op GPU gebaseerde knoop punten. |

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd welke poorten en adressen zijn toegestaan als u uitgaand verkeer voor het cluster beperkt. U kunt ook definiëren hoe de peul zelf kan communiceren en welke beperkingen ze hebben in het cluster. Zie voor meer informatie [beveiligd verkeer tussen peulen met netwerk beleid in AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
