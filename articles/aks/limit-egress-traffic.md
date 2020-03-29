---
title: Verkeer in Azure Kubernetes-service (AKS) beperken
description: Meer informatie over welke poorten en adressen nodig zijn om het uitgaande verkeer in Azure Kubernetes Service (AKS) te beheren
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080176"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Verkeer voor uitgangsverkeer voor clusterknooppunten beheren in Azure Kubernetes Service (AKS)

Standaard hebben AKS-clusters onbeperkte uitgaande (uitgaande) internettoegang. Met dit niveau van netwerktoegang kunnen knooppunten en services die u uitvoert, toegang krijgen tot externe bronnen als dat nodig is. Als u het verkeer wilt beperken, moet een beperkt aantal poorten en adressen toegankelijk zijn voor gezonde clusteronderhoudstaken. Uw cluster is standaard geconfigureerd om alleen basissysteemcontainerafbeeldingen te gebruiken uit Microsoft Container Registry (MCR) of Azure Container Registry (ACR). Configureer de firewall- en beveiligingsregels van uw voorkeur om deze vereiste poorten en adressen toe te staan.

In dit artikel wordt beschreven welke netwerkpoorten en volledig gekwalificeerde domeinnamen (FQDN's) vereist en optioneel zijn als u het uitgangsverkeer in een AKS-cluster beperkt.

> [!IMPORTANT]
> Dit document heeft alleen betrekking op het vergrendelen van het verkeer dat het AKS-subnet verlaat. AKS heeft geen invallende eisen.  Het blokkeren van intern subnetverkeer met behulp van netwerkbeveiligingsgroepen (NSG's) en firewalls wordt niet ondersteund. Als u het verkeer binnen het cluster wilt beheren en blokkeren, gebruikt u [Netwerkbeleid][network-policy].

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.66 of hoger installeren en configureren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][install-azure-cli].

## <a name="egress-traffic-overview"></a>Overzicht van het verkeer van Uitgang

Voor beheer- en operationele doeleinden moeten knooppunten in een AKS-cluster toegang hebben tot bepaalde poorten en volledig gekwalificeerde domeinnamen (FQDN's). Deze acties kunnen zijn om te communiceren met de API-server, of om kern-Kubernetes-clustercomponenten en knooppuntbeveiligingsupdates te downloaden en vervolgens te installeren. Standaard is het uitgaande internetverkeer niet beperkt voor knooppunten in een AKS-cluster. Het cluster kan containerafbeeldingen van het basissysteem uit externe opslagplaatsen halen.

Als u de beveiliging van uw AKS-cluster wilt verhogen, u het verkeer beperken. Het cluster is geconfigureerd om containerafbeeldingen van basissysteemuit MCR of ACR te halen. Als u het uitgangsverkeer op deze manier vergrendelt, definieert u specifieke poorten en FQDN's zodat de AKS-knooppunten correct kunnen communiceren met de vereiste externe services. Zonder deze geautoriseerde poorten en FQDN's kunnen uw AKS-knooppunten niet communiceren met de API-server of kerncomponenten installeren.

U [Azure Firewall][azure-firewall] of een firewalltoestel van derden gebruiken om uw uitgangsverkeer te beveiligen en deze vereiste poorten en adressen te definiëren. AKS maakt deze regels niet automatisch voor u. De volgende poorten en adressen zijn ter referentie als u de juiste regels in uw netwerkfirewall maakt.

> [!IMPORTANT]
> Wanneer u Azure Firewall gebruikt om het uitgangsverkeer te beperken en een door de gebruiker gedefinieerde route (UDR) te maken om al het uitgangsverkeer te forceren, moet u een geschikte DNAT-regel in Firewall maken om binnendringend verkeer correct toe te staan. Het gebruik van Azure Firewall met een UDR breekt de ingress-installatie als gevolg van asymmetrische routering. (Het probleem treedt op als het AKS-subnet een standaardroute heeft die naar het privé-IP-adres van de firewall gaat, maar u een public load balancer gebruikt - ingress of Kubernetes-service van type: LoadBalancer). In dit geval wordt het inkomende load balancer-verkeer ontvangen via het openbare IP-adres, maar het retourpad gaat via het privé IP-adres van de firewall. Omdat de firewall stateful is, laat het het terugkerende pakket vallen omdat de firewall niet op de hoogte is van een gevestigde sessie. Zie [Azure Firewall integreren met Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb)voor meer informatie over het integreren van Azure Firewall met uw insgress of serviceload balancer.
> U het verkeer voor TCP-poort 9000, TCP-poort 22 en UDP-poort 1194 vergrendelen met behulp van een netwerkregel tussen het IP-adres van de uitgangsmedewerker en het IP-adres voor de API-server.

In AKS zijn er twee sets poorten en adressen:

* De [vereiste poorten en het adres voor AKS-clusters](#required-ports-and-addresses-for-aks-clusters) geven de minimale vereisten voor geautoriseerd uitgangsverkeer weer.
* De [optionele aanbevolen adressen en poorten voor AKS-clusters](#optional-recommended-addresses-and-ports-for-aks-clusters) zijn niet voor alle scenario's vereist, maar integratie met andere services zoals Azure Monitor werkt niet correct. Bekijk deze lijst met optionele poorten en FQDN's en geef een van de services en onderdelen die in uw AKS-cluster worden gebruikt, een goed overzicht.

> [!NOTE]
> Het beperken van het verkeer werkt alleen op nieuwe AKS-clusters. Voer voor bestaande clusters [een clusterupgradebewerking uit][aks-upgrade] met de `az aks upgrade` opdracht voordat u het uitgangsverkeer beperkt.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Vereiste poorten en adressen voor AKS-clusters

Voor een AKS-cluster zijn de volgende uitgaande poorten/netwerkregels vereist:

* TCP-poort *443*
* TCP [IPAddrOfYourAPIServer]:443 is vereist als u een app hebt die met de API-server moet praten.  Deze wijziging kan worden ingesteld nadat het cluster is gemaakt.
* TCP-poort *9000*, TCP-poort *22* en UDP-poort *1194* voor de tunnelfrontpod om te communiceren met het tunneluiteinde op de API-server.
    * Om specifieker te worden, zie de **.hcp.\< locatie\>.azmk8s.io* en **.tun.\< locatie\>.azmk8s.io* adressen in de volgende tabel.
* UDP-poort *123* voor NTP-tijdsynchronisatie (Network Time Protocol) (Linux-knooppunten).
* UDP-poort *53* voor DNS is ook vereist als u rechtstreeks toegang hebt tot de API-server.

De volgende FQDN/ toepassingsregels zijn vereist:

> [!IMPORTANT]
> ***.blob.core.windows.net en aksrepos.azurecr.io** zijn niet langer vereist FQDN regels voor het opheffen van lockdown.  Voer voor bestaande clusters [een clusterupgradebewerking uit][aks-upgrade] met de `az aks upgrade` opdracht om deze regels te verwijderen.

> [!IMPORTANT]
> *.cdn.mscr.io is vervangen door *.data.mcr.microsoft.com voor de Azure public cloud-regio's. Upgrade uw bestaande firewallregels om de wijzigingen van kracht te laten worden.

- Azure Global

| FQDN                       | Poort      | Gebruiken      |
|----------------------------|-----------|----------|
| *.hcp. \<locatie\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Dit adres is vereist voor < > API-servercommunicatie van Node. Vervang * \<\> de locatie* door het gebied waar uw AKS-cluster is geïmplementeerd. |
| *.tun. \<locatie\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Dit adres is vereist voor < > API-servercommunicatie van Node. Vervang * \<\> de locatie* door het gebied waar uw AKS-cluster is geïmplementeerd. |
| *.cdn.mscr.io       | HTTPS:443 | Dit adres is vereist voor MCR-opslag die wordt ondersteund door het Azure Content Delivery Network (CDN). |
| mcr.microsoft.com          | HTTPS:443 | Dit adres is vereist om toegang te krijgen tot afbeeldingen in Microsoft Container Registry (MCR). Dit register bevat afbeeldingen/grafieken van de eerste partij (bijvoorbeeld moby, enz.) die nodig zijn voor de werking van het cluster tijdens de upgrade en schaal van het cluster |
| *.data.mcr.microsoft.com             | HTTPS:443 | Dit adres is vereist voor MCR-opslag die wordt ondersteund door het Azure-content delivery network (CDN). |
| management.azure.com       | HTTPS:443 | Dit adres is vereist voor Kubernetes GET/PUT-bewerkingen. |
| login.microsoftonline.com  | HTTPS:443 | Dit adres is vereist voor Azure Active Directory-verificatie. |
| ntp.ubuntu.com             | UDP:123   | Dit adres is vereist voor NTP-tijdsynchronisatie op Linux-knooppunten. |
| packages.microsoft.com     | HTTPS:443 | Dit adres is de Microsoft-pakketten repository gebruikt voor in de cache *apt-get* operaties.  Voorbeeldpakketten zijn Moby, PowerShell en Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Dit adres is bedoeld voor de opslagplaats die nodig is om vereiste binaire bestanden te installeren, zoals kubenet en Azure CNI. |

- Azure China 21Vianet

| FQDN                       | Poort      | Gebruiken      |
|----------------------------|-----------|----------|
| *.hcp. \<locatie\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Dit adres is vereist voor < > API-servercommunicatie van Node. Vervang * \<\> de locatie* door het gebied waar uw AKS-cluster is geïmplementeerd. |
| *.tun. \<locatie\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Dit adres is vereist voor < > API-servercommunicatie van Node. Vervang * \<\> de locatie* door het gebied waar uw AKS-cluster is geïmplementeerd. |
| *.azk8s.cn        | HTTPS:443 | Dit adres is vereist om vereiste binaire bestanden en afbeeldingen te downloaden|
| mcr.microsoft.com          | HTTPS:443 | Dit adres is vereist om toegang te krijgen tot afbeeldingen in Microsoft Container Registry (MCR). Dit register bevat afbeeldingen/grafieken van de eerste partij (bijvoorbeeld moby, enz.) die nodig zijn voor de werking van het cluster tijdens de upgrade en schaal van het cluster |
| *.cdn.mscr.io       | HTTPS:443 | Dit adres is vereist voor MCR-opslag die wordt ondersteund door het Azure Content Delivery Network (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Dit adres is vereist voor MCR-opslag die wordt ondersteund door het Azure-content delivery network (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Dit adres is vereist voor Kubernetes GET/PUT-bewerkingen. |
| login.chinacloudapi.cn  | HTTPS:443 | Dit adres is vereist voor Azure Active Directory-verificatie. |
| ntp.ubuntu.com             | UDP:123   | Dit adres is vereist voor NTP-tijdsynchronisatie op Linux-knooppunten. |
| packages.microsoft.com     | HTTPS:443 | Dit adres is de Microsoft-pakketten repository gebruikt voor in de cache *apt-get* operaties.  Voorbeeldpakketten zijn Moby, PowerShell en Azure CLI. |

- Azure Government

| FQDN                       | Poort      | Gebruiken      |
|----------------------------|-----------|----------|
| *.hcp. \<locatie\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Dit adres is vereist voor < > API-servercommunicatie van Node. Vervang * \<\> de locatie* door het gebied waar uw AKS-cluster is geïmplementeerd. |
| *.tun. \<locatie\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Dit adres is vereist voor < > API-servercommunicatie van Node. Vervang * \<\> de locatie* door het gebied waar uw AKS-cluster is geïmplementeerd. |
| mcr.microsoft.com          | HTTPS:443 | Dit adres is vereist om toegang te krijgen tot afbeeldingen in Microsoft Container Registry (MCR). Dit register bevat afbeeldingen/grafieken van de eerste partij (bijvoorbeeld moby, enz.) die nodig zijn voor de werking van het cluster tijdens de upgrade en schaal van het cluster |
|*.cdn.mscr.io              | HTTPS:443 | Dit adres is vereist voor MCR-opslag die wordt ondersteund door het Azure Content Delivery Network (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Dit adres is vereist voor MCR-opslag die wordt ondersteund door het Azure-content delivery network (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Dit adres is vereist voor Kubernetes GET/PUT-bewerkingen. |
| login.microsoftonline.us  | HTTPS:443 | Dit adres is vereist voor Azure Active Directory-verificatie. |
| ntp.ubuntu.com             | UDP:123   | Dit adres is vereist voor NTP-tijdsynchronisatie op Linux-knooppunten. |
| packages.microsoft.com     | HTTPS:443 | Dit adres is de Microsoft-pakketten repository gebruikt voor in de cache *apt-get* operaties.  Voorbeeldpakketten zijn Moby, PowerShell en Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Dit adres is bedoeld voor de opslagplaats die nodig is om vereiste binaire bestanden te installeren, zoals kubenet en Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Optionele aanbevolen adressen en poorten voor AKS-clusters

De volgende uitgaande poorten/netwerkregels zijn optioneel voor een AKS-cluster:

De volgende FQDN / toepassingsregels worden aanbevolen om AKS-clusters correct te laten functioneren:

| FQDN                                    | Poort      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Met dit adres kunnen de Linux-clusterknooppunten de vereiste beveiligingspatches en -updates downloaden. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Vereiste adressen en poorten voor AKS-clusters met GPU-ingeschakeld

De volgende FQDN/ toepassingsregels zijn vereist voor AKS-clusters waarop GPU is ingeschakeld:

| FQDN                                    | Poort      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Dit adres wordt gebruikt voor de juiste installatie en werking van stuurprogramma's op GPU-gebaseerde knooppunten. |
| us.download.nvidia.com | HTTPS:443 | Dit adres wordt gebruikt voor de juiste installatie en werking van stuurprogramma's op GPU-gebaseerde knooppunten. |
| apt.dockerproject.org | HTTPS:443 | Dit adres wordt gebruikt voor de juiste installatie en werking van stuurprogramma's op GPU-gebaseerde knooppunten. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Vereiste adressen en poorten met Azure Monitor voor containers ingeschakeld

De volgende FQDN/ toepassingsregels zijn vereist voor AKS-clusters waarvoor de Azure Monitor voor containers is ingeschakeld:

| FQDN                                    | Poort      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Dit is voor de juiste statistieken en het bewaken van telemetrie met behulp van Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Dit wordt gebruikt door Azure Monitor voor het innemen van loganalytics-gegevens. |
| *.oms.opinsights.azure.com | HTTPS:443 | Dit adres wordt gebruikt door omsagent, die wordt gebruikt om de log-analyseservice te verifiëren. |
|*.microsoftonline.com | HTTPS:443 | Dit wordt gebruikt voor het verifiëren en verzenden van statistieken naar Azure Monitor. |
|*.monitoring.azure.com | HTTPS:443 | Dit wordt gebruikt om metrische gegevens naar Azure Monitor te verzenden. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Vereiste adressen en poorten met Azure Dev Spaces ingeschakeld

De volgende FQDN/ toepassingsregels zijn vereist voor AKS-clusters waarvoor de Azure Dev Spaces zijn ingeschakeld:

| FQDN                                    | Poort      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Dit adres wordt gebruikt om linux alpine en andere Azure Dev Spaces-afbeeldingen te trekken |
| gcr.io | HTTP:443 | Dit adres wordt gebruikt om helm/ helm/helmstokbeelden te trekken |
| storage.googleapis.com | HTTP:443 | Dit adres wordt gebruikt om helm/ helm/helmstokbeelden te trekken |
| azds-\<guid\>. \<locatie\>.azds.io | HTTPS:443 | Om te communiceren met Azure Dev Spaces backend services voor uw controller. De exacte FQDN is te vinden in het "dataplaneFqdn" in %USERPROFILE%\.azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Vereiste adressen en poorten voor AKS-clusters met Azure Policy (in openbare preview) ingeschakeld

> [!CAUTION]
> Enkele van de onderstaande functies zijn in preview.  De suggesties in dit artikel kunnen worden gewijzigd naarmate de functie wordt verplaatst naar openbare preview- en toekomstige releasefasen.

De volgende FQDN/ toepassingsregels zijn vereist voor AKS-clusters waarvoor het Azure-beleid is ingeschakeld.

| FQDN                                    | Poort      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Dit adres wordt gebruikt voor de juiste werking van Azure Policy. (momenteel in preview in AKS) |
| raw.githubusercontent.com | HTTPS:443 | Dit adres wordt gebruikt om het ingebouwde beleid van GitHub te halen om de juiste werking van Azure Policy te garanderen. (momenteel in preview in AKS) |
| *.gk. \<locatie\>.azmk8s.io | HTTPS:443    | Azure-beleidsadd-on waarmee wordt gesproken met het eindpunt van gatekeeper-audit die wordt uitgevoerd in hoofdserver om de controleresultaten op te halen. |
| dc.services.visualstudio.com | HTTPS:443 | Azure-beleidsadd-on waarmee telemetriegegevens worden verstuurt naar eindpunt van toepassingeninzichten. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Vereist door op Windows Server gebaseerde knooppunten (in openbare preview) ingeschakeld

> [!CAUTION]
> Enkele van de onderstaande functies zijn in preview.  De suggesties in dit artikel kunnen worden gewijzigd naarmate de functie wordt verplaatst naar openbare preview- en toekomstige releasefasen.

De volgende FQDN/ toepassingsregels zijn vereist voor AKS-clusters op basis van Windows Server:

| FQDN                                    | Poort      | Gebruiken      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Windows-gerelateerde binaire bestanden installeren |
| mp.microsoft.com,<span></span>www .msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Windows-gerelateerde binaire bestanden installeren |
| kms.core.windows.net | TCP:1688 | Windows-gerelateerde binaire bestanden installeren |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd welke poorten en adressen u wilt toestaan als u het verkeer voor het cluster beperkt. U ook definiëren hoe de pods zelf kunnen communiceren en welke beperkingen ze binnen het cluster hebben. Zie [Verkeer tussen pods beveiligen met netwerkbeleid in AKS][network-policy]voor meer informatie.

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
