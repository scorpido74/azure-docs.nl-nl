---
title: Uitgaand verkeer beperken in azure Kubernetes service (AKS)
description: Meer informatie over welke poorten en adressen vereist zijn voor het beheren van uitgaand verkeer in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/29/2019
ms.author: mlearned
ms.openlocfilehash: 3010973c7d0af784938e9295bb80fc22b7f718f3
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018646"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Uitgaand verkeer beheren voor cluster knooppunten in azure Kubernetes service (AKS)

Standaard hebben AKS-clusters onbeperkte uitgaande (uitgaand) Internet toegang. Met dit niveau van netwerk toegang kunnen knoop punten en services die u uitvoert om toegang te krijgen tot externe bronnen als dat nodig is. Als u uitgaand verkeer wilt beperken, moet een beperkt aantal poorten en adressen toegankelijk zijn voor het onderhouden van goede cluster onderhouds taken. Uw cluster wordt standaard alleen geconfigureerd voor het gebruik van basis installatie kopieën van het hoofd systeem van micro soft Container Registry (MCR) of Azure Container Registry (ACR). Configureer uw voorkeurs firewall en beveiligings regels om deze vereiste poorten en adressen toe te staan.

In dit artikel wordt beschreven welke netwerk poorten en FQDN-namen (FULLy Qualified Domain names) vereist zijn en optioneel als u uitgaand verkeer in een AKS-cluster beperkt.

> [!IMPORTANT]
> In dit document wordt alleen beschreven hoe u het verkeer vergrendelt dat het AKS-subnet verlaat. AKS heeft geen ingangs vereisten.  Het blok keren van het verkeer van interne subnetten met netwerk beveiligings groepen (Nsg's) en firewalls wordt niet ondersteund. Gebruik [netwerk beleid][network-policy]om het verkeer binnen het cluster te beheren en te blok keren.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.66 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="egress-traffic-overview"></a>Overzicht van uitgaand verkeer

Voor beheer-en operationele doel einden moeten knoop punten in een AKS-cluster toegang hebben tot bepaalde poorten en FQDN-namen (Fully Qualified Domain Name). Deze acties zijn mogelijk te communiceren met de API-server of voor het downloaden en installeren van kern Kubernetes-cluster onderdelen en beveiligings updates voor knoop punten. Uitgaand verkeer van het internet is standaard niet beperkt tot een knoop punt in een AKS-cluster. Het cluster kan installatie kopieën van het basis systeem container uit externe opslag plaatsen halen.

Als u de beveiliging van uw AKS-cluster wilt verhogen, kunt u uitgaand verkeer beperken. Het cluster is geconfigureerd voor het ophalen van basis installatie kopieën van het systeem container van MCR of ACR. Als u het uitgaande verkeer op deze manier vergrendelt, definieert u specifieke poorten en FQDN-s zodat de AKS-knoop punten correct kunnen communiceren met de vereiste externe services. Zonder deze geautoriseerde poorten en FQDN-namen kunnen uw AKS-knoop punten niet communiceren met de API-server of kern onderdelen installeren.

U kunt [Azure firewall][azure-firewall] of een firewall apparaat van een derde partij gebruiken om uw uitgangs verkeer te beveiligen en deze vereiste poorten en adressen te definiëren. AKS maakt deze regels niet automatisch voor u. De volgende poorten en adressen zijn als referentie bij het maken van de juiste regels in uw netwerk firewall.

> [!IMPORTANT]
> Wanneer u Azure Firewall gebruikt om uitgaand verkeer te beperken en een door de gebruiker gedefinieerde route (UDR) te maken om al het uitgaande verkeer af te dwingen, moet u een geschikte DNAT-regel in de firewall maken om binnenkomend verkeer op de juiste wijze toe te staan. Als Azure Firewall wordt gebruikt met een UDR, wordt de ingangs instellingen voor asymmetrische route ring onderbroken. (Het probleem treedt op als het AKS-subnet een standaard route heeft die naar het privé-IP-adres van de firewall gaat, maar u een open bare load balancer-binnenkomend of Kubernetes service van het type: LoadBalancer). In dit geval wordt het binnenkomende load balancer verkeer ontvangen via het open bare IP-adres, maar het retour pad gaat via het privé-IP-adres van de firewall. Omdat de firewall stateful is, wordt het terugkerende pakket neergezet omdat de firewall niet op de hoogte is van een vastgestelde sessie. Zie [Azure firewall integreren met Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb)voor meer informatie over het integreren van Azure Firewall met uw ingangs-of service Load Balancer.
> U kunt het verkeer voor TCP-poort 9000 en TCP-poort 22 vergren delen met behulp van een netwerk regel tussen de IP ('s) van de uitvoerder knooppunt en het IP-adres voor de API-server.

In AKS zijn er twee sets poorten en adressen:

* De [vereiste poorten en het adres voor AKS-clusters](#required-ports-and-addresses-for-aks-clusters) zijn de minimale vereisten voor het toestaan van uitgaand uitgaand verkeer.
* De [optionele aanbevolen adressen en poorten voor AKS-clusters](#optional-recommended-addresses-and-ports-for-aks-clusters) zijn niet vereist voor alle scenario's, maar integratie met andere services, zoals Azure monitor, werkt niet correct. Bekijk deze lijst met optionele poorten en FQDN-namen en autoriseer de services en onderdelen die worden gebruikt in uw AKS-cluster.

> [!NOTE]
> Het beperken van uitgaand verkeer werkt alleen op nieuwe AKS-clusters. Voor bestaande clusters [voert u een cluster upgrade bewerking uit][aks-upgrade] met `az aks upgrade` behulp van de opdracht voordat u het uitgaande verkeer beperkt.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Vereiste poorten en adressen voor AKS-clusters

De volgende uitgaande poorten/netwerk regels zijn vereist voor een AKS-cluster:

* TCP-poort *443*
* TCP [IPAddrOfYourAPIServer]: 443 is vereist als u een app hebt die moet communiceren met de API-server.  Deze wijziging kan worden ingesteld nadat het cluster is gemaakt.
* TCP-poort *9000* en TCP-poort *22* voor de tunnel-front-pod om te communiceren met het tunnel einde op de API-server.
    * Zie voor meer specifieke informatie over het * *. hcp.\< Location\>. azmk8s.io* en * *. tun.\< locatie\>. azmk8s.io* -adressen in de volgende tabel.
* UDP-poort *53* voor DNS is ook vereist als u een rechtstreekse toegang hebt tot de API-server.

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
| packages.microsoft.com     | HTTPS:443 | Dit adres is de micro soft packages-opslag plaats die wordt gebruikt voor *apt-get-* bewerkingen in de cache.  Voor beelden van pakketten zijn Moby, Power shell en Azure CLI. |
| acs-mirror.azureedge.net   | HTTPS:443 | Dit adres is voor de opslag plaats die vereist is voor het installeren van vereiste binaire bestanden, zoals kubenet en Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Optionele aanbevolen adressen en poorten voor AKS-clusters

De volgende uitgaande poorten/netwerk regels zijn optioneel voor een AKS-cluster:

De volgende FQDN/toepassings regels worden aanbevolen voor een juiste werking van AKS-clusters:

| FQDN-NAAM                                    | Port      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Met dit adres kunnen Linux-cluster knooppunten de vereiste beveiligings patches en updates downloaden. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Vereiste adressen en poorten voor GPU-ingeschakelde AKS-clusters

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarop GPU is ingeschakeld:

| FQDN-NAAM                                    | Port      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Dit adres wordt gebruikt voor juiste installatie van Stuur Programma's en bewerkingen op op GPU gebaseerde knoop punten. |
| us.download.nvidia.com | HTTPS:443 | Dit adres wordt gebruikt voor juiste installatie van Stuur Programma's en bewerkingen op op GPU gebaseerde knoop punten. |
| apt.dockerproject.org | HTTPS:443 | Dit adres wordt gebruikt voor juiste installatie van Stuur Programma's en bewerkingen op op GPU gebaseerde knoop punten. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Vereiste adressen en poorten met Azure Monitor voor containers ingeschakeld

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarvoor de Azure Monitor voor containers is ingeschakeld:

| FQDN-NAAM                                    | Port      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443  | Dit is voor het corrigeren van metrische gegevens en het bewaken van telemetrie met behulp van Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS:443 | Dit wordt gebruikt door Azure Monitor voor opname van log Analytics-gegevens. |
| *.oms.opinsights.azure.com | HTTPS:443 | Dit adres wordt gebruikt door omsagent, dat wordt gebruikt om de log Analytics-service te verifiëren. |
|*.microsoftonline.com | HTTPS:443 | Dit wordt gebruikt voor het verifiëren en verzenden van metrische gegevens naar Azure Monitor. |
|*. monitoring.azure.com | HTTPS:443 | Dit wordt gebruikt om metrische gegevens naar Azure Monitor te verzenden. |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Vereiste adressen en poorten voor AKS-clusters met Azure Policy (in open bare preview) ingeschakeld

> [!CAUTION]
> Enkele van de onderstaande functies zijn beschikbaar als preview-versie.  De suggesties in dit artikel zijn onderhevig aan wijzigingen wanneer de functie wordt verplaatst naar de open bare preview-versie en toekomstige release fasen.

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarvoor de Azure Policy ingeschakeld.

| FQDN-NAAM                                    | Port      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Dit adres wordt gebruikt voor een juiste werking van Azure Policy. (momenteel als preview-versie in AKS) |
| raw.githubusercontent.com | HTTPS:443 | Dit adres wordt gebruikt om het ingebouwde beleid van GitHub te halen om te zorgen voor een juiste werking van Azure Policy. (momenteel als preview-versie in AKS) |
| *. GK. <location>. azmk8s.io | HTTPS:443 | Azure Policy add-on spreekt naar een gate keeper audit-eind punt dat wordt uitgevoerd op de hoofd server om de controle resultaten te verkrijgen. |
| dc.services.visualstudio.com | HTTPS:443 | Met de invoeg toepassing Azure Policy worden telemetriegegevens verzonden naar het apps Insights-eind punt. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Vereist voor knoop punten op basis van Windows Server (in open bare preview-versie) ingeschakeld

> [!CAUTION]
> Enkele van de onderstaande functies zijn beschikbaar als preview-versie.  De suggesties in dit artikel zijn onderhevig aan wijzigingen wanneer de functie wordt verplaatst naar de open bare preview-versie en toekomstige release fasen.

De volgende FQDN/toepassings regels zijn vereist voor op Windows Server gebaseerde AKS-clusters:

| FQDN-NAAM                                    | Port      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Windows-gerelateerde binaire bestanden installeren |
| mp.microsoft.com, www<span></span>. msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Windows-gerelateerde binaire bestanden installeren |
| kms.core.windows.net | TCP: 1688 | Windows-gerelateerde binaire bestanden installeren |


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
