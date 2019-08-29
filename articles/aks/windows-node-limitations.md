---
title: Beperkingen voor Windows Server-knooppunt groepen in azure Kubernetes service (AKS)
description: Meer informatie over de bekende beperkingen bij het uitvoeren van Windows Server-knooppunt groepen en toepassings werkbelastingen in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: c2c9e3d29ced5f75873656e253ecdbab5efe7df8
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114407"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Huidige beperkingen voor Windows Server-knooppunt groepen en toepassings werkbelastingen in azure Kubernetes service (AKS)

In azure Kubernetes service (AKS) kunt u een knooppunt groep maken waarop Windows Server als het gast besturingssysteem op de knoop punten wordt uitgevoerd. Deze knoop punten kunnen systeem eigen Windows-container toepassingen uitvoeren, zoals die zijn gebaseerd op de .NET Framework. Omdat er belang rijke verschillen zijn in de manier waarop het Linux-en Windows-besturings systeem ondersteuning bieden voor containers, zijn bepaalde algemene Kubernetes-en pod-functies momenteel niet beschikbaar voor Windows-knooppunt groepen.

In dit artikel vindt u een overzicht van enkele beperkingen en besturingssysteem concepten voor Windows Server-knoop punten in AKS. Knooppunt groepen voor Windows Server zijn momenteel beschikbaar als preview-versie.

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service opt-in. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klant ondersteuning, op basis van de beste inspanningen. Daarom zijn deze functies niet bedoeld voor productie gebruik. Raadpleeg de volgende artikelen met technische ondersteuning voor meer informatie.
>
> * [AKS-ondersteunings beleid][aks-support-policies]
> * [Veelgestelde vragen over ondersteuning voor Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Beperkingen voor Windows Server in Kubernetes

Windows Server-containers moeten worden uitgevoerd op een op Windows gebaseerde container-host. Als u Windows Server-containers in AKS wilt uitvoeren, kunt u [een knooppunt groep maken die Windows Server][windows-node-cli] als het gast besturingssysteem uitvoert. Ondersteuning voor de venster Server knooppunt groep bevat enkele beperkingen die deel uitmaken van de upstream-Windows-Server in Kubernetes-project. Deze beperkingen zijn niet specifiek voor AKS. Zie [Windows Server-containers in Kubernetes-beperkingen](https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations)voor meer informatie over deze upstream-ondersteuning voor Windows Server in Kubernetes.

De volgende beperkingen voor de stroomopwaarts voor Windows Server-containers in Kubernetes zijn relevant voor AKS:

- Windows Server-containers kunnen alleen gebruikmaken van Windows Server 2019, die overeenkomt met het onderliggende besturings systeem van het Windows Server-knoop punt.
    - Container installatie kopieën die zijn gemaakt met Windows Server 2016 als basis besturingssysteem, worden niet ondersteund.
- Geprivilegieerde containers kunnen niet worden gebruikt.
- Linux-specifieke functies, zoals RunAsUser, SELinux, AppArmor of POSIX-mogelijkheden, zijn niet beschikbaar in Windows Server-containers.
    - Beperkingen van het bestands systeem die Linux-specifiek, zoals UUI/GUID, per gebruiker zijn, zijn ook niet beschikbaar in Windows Server-containers.
- Azure-schijven en-Azure Files zijn de ondersteunde volume typen, toegankelijk als NTFS-volumes in de Windows Server-container.
    - Opslag/volumes op basis van NFS worden niet ondersteund.

## <a name="aks-limitations-for-windows-server-node-pools"></a>AKS-beperkingen voor Windows Server-knooppunt groepen

De volgende extra beperkingen zijn van toepassing op ondersteuning voor Windows Server-knooppunt groepen in AKS:

- Een AKS-cluster bevat altijd een Linux-knooppunt groep als de eerste knooppunt groep. Deze eerste knooppunt groep op basis van Linux kan alleen worden verwijderd als het AKS-cluster zelf is verwijderd.
- AKS-clusters moeten het netwerk model van Azure CNI (Geavanceerd) gebruiken.
    - Kubenet (Basic)-netwerken worden niet ondersteund. U kunt geen AKS-cluster maken dat gebruikmaakt van kubenet. Zie [Network concepten for Applications in AKS][azure-network-models](Engelstalig) voor meer informatie over de verschillen in netwerk modellen.
    - Het Azure CNI-netwerk model vereist aanvullende planning en overwegingen voor het beheer van IP-adressen. Zie [Azure cni-netwerken configureren in AKS][configure-azure-cni]voor meer informatie over het plannen en implementeren van Azure cni.
- Windows Server-knoop punten in AKS moeten worden *bijgewerkt* naar de meest recente versie van windows server 2019 om de meest recente patches en updates te kunnen onderhouden. Windows-updates zijn niet ingeschakeld in de installatie kopie van het basis knooppunt in AKS. U moet een upgrade uitvoeren op de Windows Server-knooppunt groep (en) in uw AKS-cluster, volgens een regel matige planning rond de Windows Update release cyclus en uw eigen validatie proces. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over het upgraden van een Windows Server-knooppunt groep.
    - Deze Windows Server-knooppunt upgrades gebruiken tijdelijk extra IP-adressen in het subnet van het virtuele netwerk als er een nieuw knoop punt wordt geïmplementeerd, voordat het oude knoop punt wordt verwijderd.
    - vCPU quota worden ook tijdelijk gebruikt in het abonnement als er een nieuw knoop punt wordt geïmplementeerd. vervolgens wordt het oude knoop punt verwijderd.
    - Het is niet mogelijk om opnieuw op te starten en `kured` opnieuw te beheren met als met Linux-knoop punten in AKS.
- Het AKS-cluster kan Maxi maal acht knooppunt groepen bevatten.
    - U kunt Maxi maal 400 knoop punten in deze acht knooppunt groepen hebben.
- De naam van de Windows Server-knooppunt groep heeft een limiet van 6 tekens.
- Preview-functies in AKS zoals netwerk beleid en cluster automatisch schalen, worden niet goedgekeurd voor Windows Server-knoop punten.
- Ingangs controllers mogen alleen worden gepland op Linux-knoop punten met behulp van een NodeSelector.
- Azure dev Spaces is momenteel alleen beschikbaar voor knooppunt groepen op basis van Linux.
- GMSA-ondersteuning (Group managed service accounts) wanneer de Windows Server-knoop punten niet zijn gekoppeld aan een Active Directory domein, is momenteel niet beschikbaar in AKS.
    - Het open source-upstream [AKS-engine-][aks-engine] project biedt momenteel ondersteuning voor gMSA als u deze functie moet gebruiken.

## <a name="os-concepts-that-are-different"></a>BESTURINGSSYSTEEM concepten die verschillend zijn

Kubernetes is historisch gericht op Linux. Veel voor beelden die worden gebruikt in de upstream [Kubernetes.io][kubernetes] -website zijn bedoeld voor gebruik op Linux-knoop punten. Wanneer u implementaties maakt die gebruikmaken van Windows Server-containers, gelden de volgende overwegingen op het niveau van het besturings systeem:

- **Identity** -Linux maakt gebruik van userid (UID) en GROUPID (GID), die worden weer gegeven als typen met gehele getallen. Gebruikers-en groeps namen zijn niet canoniek: ze zijn slechts een alias in */etc/groups* of */etc/passwd* terug naar de UID en GID.
    - Windows Server gebruikt een grotere binaire beveiligings-id (SID) die is opgeslagen in de SAM-data base (Windows Security Access Manager). Deze data base wordt niet gedeeld tussen de host en containers of tussen containers.
- **Bestands machtigingen** : Windows Server gebruikt een toegangs beheer lijst op basis van sid's, in plaats van een bitmasker van machtigingen en UID + GID
- **Bestands paden** : de Conventie voor Windows Server is door gebruik te gebruiken in plaats van/.
    - In pod-specificaties die volumes koppelen, geeft u het pad correct voor Windows Server-containers op. Geef bijvoorbeeld in plaats van een koppel punt van */mnt/volume* in een Linux-container een stationsletter en locatie op, zoals */K/volume* , die moeten worden gekoppeld als het station *K:* .

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Windows Server-containers in AKS, [maakt u een knooppunt groep die Windows Server in AKS uitvoert][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
