---
title: Linux-distributies die zijn goedgekeurd op Azure
description: Meer informatie over Linux op door Azure goedgekeurde distributies, inclusief richt lijnen voor Ubuntu, CentOS, Oracle en SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: mimckitt
ms.openlocfilehash: 7318095dce77856087ad7dd949e91e0eb7936294
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251647"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Officiële Linux-distributies op Azure
Partners bieden Linux-installatie kopieën in de Azure Marketplace. We werken samen met verschillende Linux-community's om nog meer versies toe te voegen aan de goedgekeurde distributie lijst. In de tussen tijd kunt u uw eigen Linux altijd gebruiken door de richt lijnen te volgen bij het [maken en uploaden van een virtuele harde schijf met het Linux-besturings systeem](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Ondersteunde distributies en versies
De volgende tabel geeft een lijst van de Linux-distributies en-versies die worden ondersteund in Azure. Raadpleeg de [ondersteuning voor Linux-installatie kopieën in Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) voor meer gedetailleerde informatie over ondersteuning voor Linux en open-source technologie in Azure.

De LIS-Stuur programma's (Linux Integration Services) voor Hyper-V en Azure zijn kernel-modules die micro soft rechtstreeks bijdraagt aan de upstream Linux-kernel.  Sommige LIS-Stuur Programma's zijn standaard ingebouwd in de kernel van de distributie. Oudere distributies die zijn gebaseerd op Red Hat Enter prise (RHEL)/CentOS zijn verkrijgbaar als afzonderlijke down load bij [Linux Integration Services versie 4,2 voor Hyper-V en Azure](https://www.microsoft.com/download/details.aspx?id=55106). Zie [vereisten voor Linux-kernel](create-upload-generic.md#linux-kernel-requirements) voor meer informatie over de Lis-Stuur Programma's.

De Azure Linux-agent is al vooraf geïnstalleerd op de installatie kopieën van Azure Marketplace en is doorgaans beschikbaar vanuit de opslag plaats van het distributie pakket. U vindt de bron code op [github](https://github.com/azure/walinuxagent).


| Distributie | Versie | Stuurprogramma's | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 +, 7.0 +, 8.0 + |CentOS 6,3: [Lis downloaden](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: in kernel |Pakket: in [opslag plaats](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) onder WALinuxAgent <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |In kernel |Bron code: [github](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7,9 +, 8.2 +, 9, 10 |In kernel |Pakket: in opslag plaats onder waagent <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |In kernel |Pakket: in opslag plaats onder WALinuxAgent <br/>Bron code: [github](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6,7 +, 7.1 +, 8.0 + |In kernel |Pakket: in opslag plaats onder WALinuxAgent <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enter prise |SLES/SLES voor SAP<br>11 SP4<br>12 SP1+<br>15|In kernel |Pakket:<p> voor 11 in [Cloud: tools](https://build.opensuse.org/project/show/Cloud:Tools) opslag plaats<br>voor 12 opgenomen in de module ' Public Cloud ' onder ' python-Azure-agent '<br/>Bron code: [github](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Schrikkel 42.2 + |In kernel |Pakket: in [Cloud: tools](https://build.opensuse.org/project/show/Cloud:Tools) opslag plaats onder ' python-Azure-agent ' <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |In kernel |Pakket: in opslag plaats onder walinuxagent <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** informatie over uitgebreide ondersteuning voor Ubuntu 12,04 en 14,04 vindt u hier: [Ubuntu uitgebreid beveiligings onderhoud](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Uitgebracht installatie kopie-update
Azure vereist dat de uitgevers van de getekende Linux-distributies hun installatie kopieën regel matig bijwerken in de Azure Marketplace met de nieuwste patches en beveiligings oplossingen, op een driemaandelijkse of snellere uitgebracht. Bijgewerkte installatie kopieën in azure Marketplace zijn automatisch beschikbaar voor klanten als nieuwe versies van een image SKU. Meer informatie over het zoeken naar Linux-installatie kopieën: [Linux VM-installatie kopieën zoeken in azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Aanvullende koppelingen
 - [Levenscyclus van de open bare Cloud installatie kopie van SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Door Azure afgestemde kernels

Azure werkt nauw samen met verschillende geplaatste Linux-distributies om de installatie kopieën te optimaliseren die ze naar Azure Marketplace hebben gepubliceerd. Eén aspect van deze samen werking is de ontwikkeling van ' afgestemde ' Linux-kernels die zijn geoptimaliseerd voor het Azure-platform en geleverd als volledig ondersteunde onderdelen van de Linux-distributie. De door Azure afgestemde kernels bevatten nieuwe functies en prestatie verbeteringen en sneller (meestal driemaandelijks) uitgebracht vergeleken met de standaard-of algemene kernels die beschikbaar zijn via de distributie.

In de meeste gevallen vindt u deze kernels die vooraf zijn geïnstalleerd op de standaard installatie kopieën in de Azure Marketplace, waardoor Azure-klanten onmiddellijk profiteren van de voor delen van deze geoptimaliseerde kernels. Meer informatie over deze kernels die zijn afgestemd op Azure, vindt u in de volgende koppelingen:

 - CentOS Azure-afgestemde kernel-beschikbaar via de CentOS-virtualisatie SIG- [meer informatie](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud-kernel: beschikbaar in azure- [meer informatie](https://wiki.debian.org/Cloud/MicrosoftAzure) over de installatie kopie van Debian 10 en Debian 9 "backports"
 - SLES Azure-afgestemde kernel- [meer informatie](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-afgestemde kernel- [meer informatie](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partners

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Op de CoreOS-website:

*CoreOS is ontworpen voor beveiliging, consistentie en betrouw baarheid. In plaats van pakketten te installeren via yum of apt, gebruikt CoreOS Linux-containers om uw services te beheren op een hoger niveau van abstractie. De code van een enkele service en alle afhankelijkheden zijn verpakt in een container die kan worden uitgevoerd op een of meer CoreOS machines.*

### <a name="credativ"></a>credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ is een onafhankelijke Consulting-en service maatschappij die is gespecialiseerd in de ontwikkeling en implementatie van professionele oplossingen door gebruik te maken van gratis software. Als toonaangevende open-source specialisten heeft Credativ internationale herkenning met veel IT-afdelingen die hun ondersteuning gebruiken. In combi natie met micro soft wordt Credativ momenteel de bijbehorende Debian-installatie kopieën voor Debian 8 (Jessie) en Debian voor 7 (Wheezy) voor bereid. Beide installatie kopieën zijn speciaal ontworpen om te worden uitgevoerd op Azure en kunnen eenvoudig worden beheerd via het platform. Credativ biedt ook ondersteuning voor de lange termijn onderhoud en het bijwerken van de Debian-installatie kopieën voor Azure via de open source-ondersteunings centra.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

De strategie van Oracle is om een breed assortiment oplossingen te bieden voor open bare en particuliere Clouds. De strategie biedt klanten keuze en flexibiliteit bij het implementeren van Oracle-software in Oracle-Clouds en andere Clouds. Door Oracle samen te stellen met micro soft kunnen klanten Oracle-software in open bare en persoonlijke Clouds van micro soft implementeren met het vertrouwen van certificering en ondersteuning van Oracle.  De toezeg ging en investeringen van Oracle in Oracle Public-en Private Cloud-oplossingen worden niet gewijzigd.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Dankzij de wereld wijde provider van open-source-oplossingen biedt Red Hat meer dan 90% aan Fortune 500-bedrijven om zakelijke uitdagingen op te lossen, hun IT-en bedrijfs strategieën uit te lijnen en de toekomst van technologie voor te bereiden. Red Hat doet dit door veilige oplossingen te bieden met behulp van een open bedrijfs model en een betaalbaar, voorspelbaar abonnement model.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server op Azure is een bewezen platform dat een superieure betrouw baarheid en beveiliging biedt voor Cloud Computing. Het veelzijdige Linux-platform van SUSE kan naadloos worden geïntegreerd met Azure Cloud Services om een gemakkelijk te beheren cloud omgeving te bieden. Met meer dan 9.200 gecertificeerde toepassingen van meer dan 1.800 onafhankelijke software leveranciers voor SUSE Linux Enterprise Server, zorgt SUSE ervoor dat werk belastingen die worden uitgevoerd in het Data Center, in vertrouwen kunnen worden geïmplementeerd in Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

De canonieke techniek en het open Community governance-station Ubuntu slagen in client-, server-en Cloud Computing, waaronder persoonlijke Cloud Services voor consumenten. De standaardisatie van een uniform, gratis platform in Ubuntu, van telefoon tot Cloud, biedt een familie van samenhangende interfaces voor de telefoon, Tablet, TV en desktop. Deze visie maakt Ubuntu de eerste keuze voor diverse instellingen van open bare cloud providers voor de makers van consumenten elektronica en een favoriet tussen afzonderlijke Technologists.

Met ontwikkel aars en technische hulp middelen over de hele wereld is canoniek gepositioneerd naar een partner met hardware makers, inhouds providers en software ontwikkelaars om Ubuntu oplossingen op de markt te brengen voor Pc's, servers en hand held apparaten.
