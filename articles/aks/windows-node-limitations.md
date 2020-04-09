---
title: Beperkingen voor windows Server-knooppuntgroepen
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de bekende beperkingen wanneer u Windows Server-knooppuntgroepen en toepassingsworkloads uitvoert in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: 934acf06a779c1c3b0b13e74b196b174dd944e66
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886667"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Huidige beperkingen voor windows server-knooppuntgroepen en toepassingsworkloads in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) u een knooppuntgroep maken waarop Windows Server wordt uitgevoerd als gastbesturingssysteem op de knooppunten. Deze knooppunten kunnen native Windows-containertoepassingen uitvoeren, zoals die zijn gebouwd op het .NET Framework. Omdat er grote verschillen zijn in de manier waarop het Linux- en Windows-besturingssysteem containerondersteuning biedt, zijn sommige veelvoorkomende Kubernetes- en pod-gerelateerde functies momenteel niet beschikbaar voor Windows-knooppuntgroepen.

In dit artikel worden enkele beperkingen en OS-concepten voor Windows Server-knooppunten in AKS beschreven. Knooppuntgroepen voor Windows Server zijn momenteel in preview.

> [!IMPORTANT]
> AKS preview-functies zijn self-service opt-in. Previews worden geleverd "as-is" en "as available" en zijn uitgesloten van de service level agreements en beperkte garantie. AKS Previews worden gedeeltelijk gedekt door de klantenservice op basis van de beste inspanning. Als zodanig zijn deze functies niet bedoeld voor productiegebruik. Zie voor meer informatie de volgende ondersteuningsartikelen:
>
> * [AKS-ondersteuningsbeleid][aks-support-policies]
> * [Veelgestelde vragen over Azure Support][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Welke Windows-besturingssystemen worden ondersteund?

AKS gebruikt Windows Server 2019 als host-versie en ondersteunt alleen procesisolatie. Containerafbeeldingen die zijn gemaakt met andere Windows Server-versies worden niet ondersteund. [Compatibiliteit met Windows-containerversies][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Is Kubernetes anders op Windows en Linux?

Ondersteuning voor window server-knooppuntgroep bevat een aantal beperkingen die deel uitmaken van het upstream Windows Server in Kubernetes-project. Deze beperkingen zijn niet specifiek voor AKS. Zie het gedeelte [Ondersteunde functionaliteit en beperkingen][upstream-limitations] van de sectie Intro naar [Windows-ondersteuning in kubernetes-document,][intro-windows] vanuit het Kubernetes-project, voor meer informatie over deze upstream-ondersteuning voor Windows Server in Kubernetes.

Kubernetes is historisch Linux-gericht. Veel voorbeelden die worden gebruikt in de upstream [Kubernetes.io][kubernetes] website zijn bedoeld voor gebruik op Linux-knooppunten. Wanneer u implementaties maakt die Windows Server-containers gebruiken, zijn de volgende overwegingen op OS-niveau van toepassing:

- **Identiteit** - Linux identificeert een gebruiker met een integer user identifier (UID). Een gebruiker heeft ook een alfanumerieke gebruikersnaam voor het inloggen, die Linux vertaalt naar de Gebruiker UID. Op dezelfde manier identificeert Linux een gebruikersgroep met een integer groep identifier (GID) en vertaalt een groepsnaam naar de bijbehorende GID.
    - Windows Server maakt gebruik van een grotere binaire beveiligingsid (SID) die is opgeslagen in de SAM-database (Windows Security Access Manager). Deze database wordt niet gedeeld tussen de host en containers, of tussen containers.
- **Bestandsmachtigingen** - Windows Server gebruikt een lijst met toegangscontrole op basis van ID's, in plaats van een bitmask van machtigingen en UID+GID
- **Bestandspaden** - conventie op Windows Server is te gebruiken \ in plaats van /.
    - Geef in podspecificaties die volumes monteren het pad correct op voor Windows Server-containers. Geef bijvoorbeeld in plaats van een bevestigingspunt van */mnt/volume* in een Linux-container een stationsletter en -locatie op, zoals */K/Volume* om te monteren als de *K:* station.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Wat voor soort schijven worden ondersteund voor Windows?

Azure Disks en Azure Files zijn de ondersteunde volumetypen, toegankelijk als NTFS-volumes in de Windows Server-container.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Kan ik windows-clusters alleen uitvoeren in AKS?

De hoofdknooppunten (het besturingsvlak) in een AKS-cluster worden gehost door AKS de service, u wordt niet blootgesteld aan het besturingssysteem van de knooppunten die de hoofdcomponenten hosten. Alle AKS-cluster wordt gemaakt met een standaard eerste knooppuntgroep, die op Linux is gebaseerd. Deze knooppuntgroep bevat systeemservices, die nodig zijn om het cluster te laten functioneren. Het wordt aanbevolen om ten minste twee knooppunten uit te voeren in de eerste knooppuntgroep om de betrouwbaarheid van uw cluster te garanderen en de mogelijkheid om clusterbewerkingen uit te voeren. De eerste op Linux gebaseerde knooppuntgroep kan niet worden verwijderd, tenzij het AKS-cluster zelf wordt verwijderd.

## <a name="what-network-plug-ins-are-supported"></a>Welke netwerkplug-ins worden ondersteund?

AKS-clusters met Windows-knooppuntgroepen moeten het Azure CNI-netwerkmodel (advanced) gebruiken. Kubenet (basic) networking wordt niet ondersteund. Zie [Netwerkconcepten voor toepassingen in AKS voor][azure-network-models]meer informatie over de verschillen in netwerkmodellen. - Het Azure CNI-netwerkmodel vereist extra planning en overwegingen voor IP-adresbeheer. Zie [Azure CNI-netwerken configureren in AKS][configure-azure-cni]voor meer informatie over het plannen en implementeren van Azure CNI.

## <a name="can-i-change-the-max--of-pods-per-node"></a>Kan ik de max veranderen. # van peulen per knooppunt?

Ja. Zie [Maximum aantal pods voor][maximum-number-of-pods]de beschikbare implicaties en opties .

## <a name="how-do-patch-my-windows-nodes"></a>Hoe patch je mijn Windows-knooppunten?

Windows Server-knooppunten in AKS moeten worden *geüpgraded* om de nieuwste patchfixes en -updates te ontvangen. Windows-updates zijn niet ingeschakeld op knooppunten in AKS. AKS releases nieuwe node pool beelden zodra patches beschikbaar zijn, is het de verantwoordelijkheid van de klanten om knooppunt pools te upgraden om actueel te blijven op patches en hotfix. Dit geldt ook voor de Kubernetes-versie die wordt gebruikt. AKS release notes geven aan wanneer er nieuwe versies beschikbaar zijn. Zie [Een knooppuntgroep][nodepool-upgrade]in AKS upgraden voor meer informatie over het upgraden van een windows server-knooppuntgroep.

> [!NOTE]
> De bijgewerkte Windows Server-afbeelding wordt alleen gebruikt als een clusterupgrade (upgrade van het beheervlak) is uitgevoerd voordat de knooppuntgroep wordt geüpgraded
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Hoe draai ik de serviceprincipal voor mijn Windows-knooppuntgroep?

Tijdens preview ondersteunen Windows-knooppuntgroepen geen hoofdrotatie van de serviceals voorbeeldbeperking. Als u de serviceprincipal wilt bijwerken, maakt u een nieuwe Windows-knooppuntgroep en migreert u uw pods van de oudere groep naar de nieuwe groep. Zodra dit is voltooid, verwijdert u de oudere knooppuntgroep.

## <a name="how-many-node-pools-can-i-create"></a>Hoeveel knooppuntpools kan ik maken?

Het AKS-cluster kan maximaal 10 knooppuntpools hebben. U maximaal 1000 knooppunten hebben in die knooppuntgroepen. [Beperkingen van het knooppuntzwembad][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Wat kan ik mijn Windows-knooppuntpools noemen?

Je moet de naam houden tot maximaal 6 (zes) tekens. Dit is een huidige beperking van AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Worden alle functies ondersteund met Windows-knooppunten?

Netwerkbeleid en kubenet worden momenteel niet ondersteund met Windows-knooppunten. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Kan ik invallencontrollers uitvoeren op Windows-knooppunten?

Ja, een in-gress-controller die Windows Server-containers ondersteunt, kan worden uitgevoerd op Windows-knooppunten in AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Kan ik Azure Dev Spaces gebruiken met Windows-knooppunten?

Azure Dev Spaces is momenteel alleen beschikbaar voor op Linux gebaseerde knooppuntgroepen.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Kunnen mijn Windows Server-containers gMSA gebruiken?

Ondersteuning voor beheerde serviceaccounts (group managed service accounts) is momenteel niet beschikbaar in AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Kan ik Azure Monitor gebruiken voor containers met Windows-knooppunten en containers?

Ja, u wel dat Azure Monitor geen logboeken (stdout) verzamelt uit Windows-containers. U nog steeds koppelen aan de live stream van stdout-logboeken vanuit een Windows-container.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Wat moet ik doen als ik een functie nodig heb die niet wordt ondersteund?

We werken er hard aan om alle functies die u nodig hebt naar Windows in AKS te brengen, maar als u hiaten tegenkomt, biedt het open-source, upstream [aks-engine-project][aks-engine] een eenvoudige en volledig aanpasbare manier om Kubernetes in Azure te draaien, inclusief Windows-ondersteuning. Zorg ervoor dat u onze routekaart van functies komende [AKS roadmap][aks-roadmap].

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan met Windows Server-containers in AKS, [maakt u een knooppuntgroep waarop Windows Server in AKS wordt uitgevoerd.][windows-node-cli]

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
