---
title: Hoge beschikbaarheid en disaster recovery in Azure Kubernetes Service (AKS)
description: Lees de aanbevolen procedures van een clusteroperator om maximale uptime voor uw toepassingen te bereiken, met een hoge beschikbaarheid en voorbereiding op disaster recovery in Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 894ec4e543f0c68cc652141d2c1578cda61d7f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594737"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor bedrijfscontinuïteit en disaster recovery in Azure Kubernetes Service (AKS)

Als u clusters beheert in Azure Kubernetes Service (AKS), wordt uptime van toepassingen belangrijk. AKS biedt een hoge beschikbaarheid door gebruik te maken van meerdere knooppunten in een beschikbaarheidsset. Maar deze meerdere knooppunten beschermen uw systeem niet tegen een regiostoring. Om uw uptime te maximaliseren, u vooruit plannen om de bedrijfscontinuïteit te behouden en u voor te bereiden op herstel na noodgevallen.

Dit artikel richt zich op hoe te plannen voor bedrijfscontinuïteit en disaster recovery in AKS. Procedures voor:

> [!div class="checklist"]
> * Plannen voor AKS-clusters in meerdere regio's.
> * Leid verkeer over meerdere clusters met Azure Traffic Manager.
> * Gebruik georeplicatie voor de registers van uw containerafbeelding.
> * Plan de toepassingsstatus voor meerdere clusters.
> * Replicatieopslag in meerdere regio's.

## <a name="plan-for-multiregion-deployment"></a>Plannen voor implementatie met meerdere regio's

**Aanbevolen procedures:** Wanneer u meerdere AKS-clusters implementeert, kiest u regio's waar AKS beschikbaar is en gebruikt u gekoppelde regio's.

Een AKS-cluster wordt geïmplementeerd in één regio. Als u uw systeem wilt beschermen tegen een regiostoring, implementeert u uw toepassing in meerdere AKS-clusters in verschillende regio's. Wanneer u van plan bent waar u uw AKS-cluster wilt implementeren, u rekening houden met:

* [**Beschikbaarheid van AKS-regio's**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Kies regio's die dicht bij uw gebruikers staan. AKS breidt voortdurend uit naar nieuwe regio's.
* [**Gekoppelde azure-regio's:**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)kies voor uw geografische gebied twee regio's die aan elkaar zijn gekoppeld. Gekoppelde regio's coördineren platformupdates en geven waar nodig prioriteit aan herstelinspanningen.
* **Beschikbaarheid van de service**: Bepaal of uw gekoppelde regio's warm/warm, warm/warm of warm/koud moeten zijn. Wilt u beide regio's tegelijkertijd uitvoeren, waarbij één regio *klaar is* om het verkeer te gaan bedienen? Of wilt u dat één regio tijd heeft om zich klaar te maken voor het verkeer?

Beschikbaarheid van AKS-regio's en gekoppelde regio's zijn een gezamenlijke overweging. Implementeer uw AKS-clusters in gekoppelde regio's die zijn ontworpen om samen het herstel van regiorampen te beheren. AKS is bijvoorbeeld beschikbaar in Oost-VS en West-VS. Deze regio's zijn gekoppeld. Kies deze twee regio's wanneer u een AKS BC/DR-strategie maakt.

Wanneer u uw toepassing implementeert, voegt u een volgende stap toe aan uw CI/CD-pijplijn om te implementeren in deze meerdere AKS-clusters. Als u uw implementatiepijplijnen niet bijwerkt, kunnen toepassingen worden geïmplementeerd in slechts één van uw regio's en AKS-clusters. Het klantverkeer dat naar een secundaire regio is geleid, ontvangt niet de meest recente code-updates.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager gebruiken om verkeer te routeren

**Aanbevolen procedures:** Azure Traffic Manager kan klanten doorverwijzen naar hun dichtstbijzijnde AKS-cluster- en toepassingsexemplaar. Voor de beste prestaties en redundantie u al het toepassingsverkeer door Traffic Manager leiden voordat het naar uw AKS-cluster gaat.

Als u meerdere AKS-clusters in verschillende regio's hebt, gebruikt u Traffic Manager om te bepalen hoe verkeer stroomt naar de toepassingen die in elk cluster worden uitgevoerd. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) is een op DNS gebaseerde traffic load balancer die netwerkverkeer over regio's kan distribueren. Gebruik Traffic Manager om gebruikers te routeren op basis van de reactietijd van het cluster of op basis van geografie.

![AKS met Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Klanten die één AKS-cluster hebben, maken doorgaans verbinding met de IP- of DNS-naam van een bepaalde toepassing. In een implementatie met meerdere clusteren moeten klanten verbinding maken met een DNS-naam van Traffic Manager die verwijst naar de services op elk AKS-cluster. Definieer deze services met behulp van eindpunten voor traffic manager. Elk eindpunt is het *IP-adres van de serviceloadbalancer*. Gebruik deze configuratie om netwerkverkeer te leiden van het eindpunt Traffic Manager in een regio naar het eindpunt in een andere regio.

![Geografische routering via Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager voert DNS-lookups uit en retourneert het meest geschikte eindpunt van een gebruiker. Geneste profielen kunnen prioriteit geven aan een primaire locatie. Gebruikers moeten bijvoorbeeld over het algemeen verbinding maken met hun dichtstbijzijnde geografische regio. Als die regio een probleem heeft, leidt Traffic Manager de gebruikers in plaats daarvan naar een secundaire regio. Deze aanpak zorgt ervoor dat klanten verbinding kunnen maken met een toepassingsinstantie, zelfs als hun dichtstbijzijnde geografische regio niet beschikbaar is.

Zie [De routeringsmethode voor geografisch verkeer configureren met Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)voor informatie over het instellen van eindpunten en routering.

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Layer 7-toepassingsroutering met Azure Front Door Service

Traffic Manager gebruikt DNS (laag 3) om verkeer vorm te geven. [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) biedt een http/HTTPS (layer 7) routeringsoptie. Extra functies van Azure Front Door Service zijn SSL-beëindiging, aangepast domein, firewall van webtoepassingen, URL-herschrijven en sessieaffiniteit. Bekijk de behoeften van uw applicatieverkeer om te begrijpen welke oplossing het meest geschikt is.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Regio's verbinden met wereldwijd virtueel netwerkpeering

Als de clusters met elkaar moeten praten, kan het verbinden van beide virtuele netwerken met elkaar worden bereikt door [middel van virtuele netwerkpeering.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Deze technologie verbindt virtuele netwerken met elkaar en biedt een hoge bandbreedte in het backbone-netwerk van Microsoft, zelfs in verschillende geografische regio's.

Een voorwaarde om te peer en de virtuele netwerken waar AKS-clusters worden uitgevoerd, is het gebruik van de standaard Load Balancer in uw AKS-cluster, zodat Kubernetes-services bereikbaar zijn via het virtuele netwerk peering.

## <a name="enable-geo-replication-for-container-images"></a>Geo-replicatie inschakelen voor containerafbeeldingen

**Aanbevolen procedures**: Sla uw containerafbeeldingen op in Azure Container Registry en geo-repliceren van het register naar elke AKS-regio.

Als u uw toepassingen wilt implementeren en uitvoeren in AKS, hebt u een manier nodig om de containerafbeeldingen op te slaan en te trekken. Container Registry integreert met AKS, zodat het uw containerafbeeldingen of Helm-diagrammen veilig kan opslaan. Container Registry ondersteunt multimaster geo-replicatie om uw afbeeldingen automatisch te repliceren naar Azure-regio's over de hele wereld. 

Als u de prestaties en beschikbaarheid wilt verbeteren, gebruikt u georeplicatie containerregister om een register te maken in elke regio waar u een AKS-cluster hebt. Elk AKS-cluster haalt vervolgens containerafbeeldingen uit het lokale containerregister in dezelfde regio:

![Geo-replicatie containerregister voor containerafbeeldingen](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Wanneer u georeplicatie containerregister gebruikt om afbeeldingen uit hetzelfde gebied te trekken, zijn de resultaten:

* **Sneller:** u haalt afbeeldingen op van snelle netwerkverbindingen met lage latentie binnen hetzelfde Azure-gebied.
* **Betrouwbaarder**: Als een regio niet beschikbaar is, haalt uw AKS-cluster de afbeeldingen uit een beschikbaar containerregister.
* **Goedkoper**: Er is geen netwerk uitgang kosten tussen datacenters.

Geo-replicatie is een functie van *Premium* SKU-containerregisters. Zie [Geo-replicatie containerregister](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)voor informatie over het configureren van georeplicatie.

## <a name="remove-service-state-from-inside-containers"></a>Servicestatus verwijderen uit containers

**Aanbevolen procedures**: Sla waar mogelijk geen servicestatus op in de container. Gebruik in plaats daarvan een Azure-platform als een service (PaaS) die replicatie met meerdere regio's ondersteunt.

*Servicestatus* verwijst naar de gegevens in het geheugen of op de schijf die een service nodig heeft om te kunnen functioneren. Staat omvat de gegevensstructuren en lidvariabelen die de service leest en schrijft. Afhankelijk van de manier waarop de service is ontworpen, kan de status ook bestanden of andere bronnen bevatten die op de schijf zijn opgeslagen. De status kan bijvoorbeeld de bestanden bevatten die een database gebruikt om gegevens en transactielogboeken op te slaan.

Status kan worden geexternaliseerd of naast de code die de status manipuleert. Doorgaans externiseert u de status met behulp van een database of ander gegevensarchief dat op verschillende machines via het netwerk wordt uitgevoerd of die op dezelfde machine zonder proces komt te zitten.

Containers en microservices zijn het meest veerkrachtig wanneer de processen die erin worden uitgevoerd, de status niet behouden. Omdat toepassingen bijna altijd een bepaalde status bevatten, gebruikt u een PaaS-oplossing zoals Azure Database voor MySQL, Azure Database voor PostgreSQL of Azure SQL Database.

Zie de volgende richtlijnen voor het bouwen van draagbare toepassingen:

* [De 12-factor app methodologie](https://12factor.net/)
* [Een webtoepassing uitvoeren in meerdere Azure-regio's](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Een opslagmigratieplan maken

**Aanbevolen procedures:** als u Azure Storage gebruikt, moet u voorbereiden en testen hoe u uw opslag migreren van het primaire gebied naar het back-upgebied.

Uw toepassingen kunnen Azure Storage gebruiken voor hun gegevens. Omdat uw toepassingen verspreid zijn over meerdere AKS-clusters in verschillende regio's, moet u de opslag gesynchroniseerd houden. Hier volgen twee veelvoorkomende manieren om opslag te repliceren:

* Op infrastructuur gebaseerde asynchrone replicatie
* Op toepassing gebaseerde asynchrone replicatie

### <a name="infrastructure-based-asynchronous-replication"></a>Op infrastructuur gebaseerde asynchrone replicatie

Uw toepassingen vereisen mogelijk permanente opslag, zelfs nadat een pod is verwijderd. In Kubernetes u permanente volumes gebruiken om gegevensopslag te blijven bestaan. Permanente volumes worden gemonteerd op een node VM en vervolgens blootgesteld aan de pods. Permanente volumes volgen pods, zelfs als de pods worden verplaatst naar een ander knooppunt in hetzelfde cluster.

De replicatiestrategie die u gebruikt, is afhankelijk van uw opslagoplossing. Gemeenschappelijke opslagoplossingen zoals [Gluster,](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/) [Ceph,](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/) [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)en [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) bieden hun eigen richtlijnen over noodherstel en replicatie.

De typische strategie is om een gemeenschappelijk opslagpunt waar toepassingen hun gegevens kunnen schrijven. Deze gegevens worden vervolgens gerepliceerd in verschillende regio's en vervolgens lokaal geopend.

![Op infrastructuur gebaseerde asynchrone replicatie](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Als u Azure Managed Disks gebruikt, u replicatie- en DR-oplossingen zoals deze kiezen:

* [Velero op Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure-siteherstel](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Op toepassing gebaseerde asynchrone replicatie

Kubernetes biedt momenteel geen native implementatie voor op toepassingen gebaseerde asynchrone replicatie. Omdat containers en Kubernetes losjes zijn gekoppeld, zou elke traditionele toepassing of taalbenadering moeten werken. Doorgaans repliceren de toepassingen zelf de opslagaanvragen, die vervolgens worden geschreven naar de onderliggende gegevensopslag van elk cluster.

![Op toepassing gebaseerde asynchrone replicatie](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Volgende stappen

Dit artikel richt zich op business continuity en disaster recovery overwegingen voor AKS clusters. Zie de volgende artikelen over aanbevolen procedures voor meer informatie over clusterbewerkingen in AKS:

* [Multitenancy en clusterisolatie][aks-best-practices-cluster-isolation]
* [Basisfuncties kubernetes scheduler][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
