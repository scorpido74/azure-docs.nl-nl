---
title: Linux-distributies goedgekeurd op Azure
description: Meer informatie over Linux op door Azure goedgekeurde distributies, inclusief richtlijnen voor Ubuntu, CentOS, Oracle en SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: cc2fedcd4816b55aaed3573ce2593919770a4152
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062646"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Goedgekeurde Linux-distributies op Azure
Partners bieden Linux-afbeeldingen in de Azure Marketplace. We werken samen met verschillende Linux-gemeenschappen om nog meer smaken toe te voegen aan de lijst met goedgekeurde distributie. In de tussentijd, voor distributies die niet beschikbaar zijn op de Marketplace, u altijd uw eigen Linux door het volgen van de richtlijnen op [Maken en uploaden van een virtuele harde schijf die het Linux-besturingssysteem bevat](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Ondersteunde distributies en versies
In de volgende tabel worden de Linux-distributies en -versies weergegeven die op Azure worden ondersteund. Raadpleeg [ondersteuning voor Linux-afbeeldingen in Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) voor meer gedetailleerde informatie over ondersteuning voor Linux en opensourcetechnologie in Azure.

De Linux Integration Services (LIS) drivers voor Hyper-V en Azure zijn kernel modules die Microsoft rechtstreeks bijdraagt aan de upstream Linux kernel.  Sommige LIS-stuurprogramma's zijn standaard ingebouwd in de kernel van de distributie. Oudere distributies die zijn gebaseerd op Red Hat Enterprise (RHEL)/CentOS zijn beschikbaar als een aparte download op [Linux Integration Services Versie 4.2 voor Hyper-V en Azure](https://www.microsoft.com/download/details.aspx?id=55106). Zie [Linux kernel vereisten](create-upload-generic.md#linux-kernel-requirements) voor meer informatie over de LIS drivers.

De Azure Linux Agent is al vooraf geïnstalleerd op de Azure Marketplace-afbeeldingen en is meestal beschikbaar in de pakketopslagplaats van de distributie. Broncode is te vinden op [GitHub](https://github.com/azure/walinuxagent).


| Distributie | Versie | Stuurprogramma's | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+, 8.0+ |CentOS 6.3: [LIS downloaden](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: In kernel |Pakket: In [repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) onder "WALinuxAgent" <br/>Broncode: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |In kernel |Broncode: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+, 9, 10 |In kernel |Pakket: In repo onder "waagent" <br/>Broncode: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6,4+, 7,0+ |In kernel |Pakket: In repo onder "WALinuxAgent" <br/>Broncode: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6,7+, 7,1+, 8,0+ |In kernel |Pakket: In repo onder "WALinuxAgent" <br/>Broncode: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES voor SAP<br>11 SP4<br>12 SP1+<br>15|In kernel |Pakket:<p> voor 11 in [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) repo<br>voor 12 opgenomen in "Public Cloud" Module onder "python-azure-agent"<br/>Broncode: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |In kernel |Pakket: In [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) repo onder 'python-azure-agent' <br/>Broncode: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ ** <sup>1</sup>** |In kernel |Pakket: In repo onder "walinuxagent" <br/>Broncode: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Informatie over uitgebreide ondersteuning voor Ubuntu 12.04 en 14.04 vindt u hier: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Cadans voor afbeeldingsupdates
Azure vereist dat de uitgevers van de goedgekeurde Linux-distributies hun afbeeldingen in de Azure Marketplace regelmatig bijwerken met de nieuwste patches en beveiligingsoplossingen, op een kwartaal- of snellere cadans. Bijgewerkte afbeeldingen in de Azure Marketplace zijn automatisch beschikbaar voor klanten als nieuwe versies van een afbeelding SKU. Meer informatie over het vinden van [Linux-afbeeldingen: Zoek Linux VM-afbeeldingen in de Azure Marketplace.](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage)

### <a name="additional-links"></a>Aanvullende koppelingen
 - [Levenscyclus van openbare cloudafbeeldingen van SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Azure-afgestemde kernels

Azure werkt nauw samen met verschillende goedgekeurde Linux-distributies om de afbeeldingen die ze hebben gepubliceerd op de Azure Marketplace te optimaliseren. Een aspect van deze samenwerking is de ontwikkeling van "afgestemde" Linux-kernels die zijn geoptimaliseerd voor het Azure-platform en worden geleverd als volledig ondersteunde componenten van de Linux-distributie. De Azure-Tuned kernels bevatten nieuwe functies en prestatieverbeteringen, en op een snellere (meestal driemaandelijkse) cadans in vergelijking met de standaard of generieke kernels die beschikbaar zijn vanaf de distributie.

In de meeste gevallen vindt u deze kernels vooraf geïnstalleerd op de standaardafbeeldingen in de Azure Marketplace, en dus krijgen Azure-klanten onmiddellijk het voordeel van deze geoptimaliseerde kernels. Meer informatie over deze Azure-Tuned kernels vindt u in de volgende koppelingen:

 - CentOS Azure-Tuned Kernel - Beschikbaar via de CentOS Virtualization SIG - [Meer info](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud Kernel - Beschikbaar met de Debian 10 en Debian 9 "backports" afbeelding op Azure - [Meer info](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure-tuned Kernel - [Meer info](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-tuned Kernel - [Meer info](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partners

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Van de CoreOS website:

*CoreOS is ontworpen voor beveiliging, consistentie en betrouwbaarheid. In plaats van pakketten te installeren via yum of apt, gebruikt CoreOS Linux-containers om uw services op een hoger abstractieniveau te beheren. De code van één service en alle afhankelijkheden worden verpakt in een container die kan worden uitgevoerd op een of meer CoreOS-machines.*

### <a name="credativ"></a>Credativ Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ is een onafhankelijk advies- en dienstenbedrijf dat gespecialiseerd is in de ontwikkeling en implementatie van professionele oplossingen door gebruik te maken van vrije software. Als toonaangevende open-source specialisten heeft Credativ internationale erkenning met veel IT-afdelingen die hun ondersteuning gebruiken. In samenwerking met Microsoft bereidt Credativ momenteel overeenkomstige Debian-afbeeldingen voor Debian 8 (Jessie) en Debian voor 7 (Wheezy). Beide afbeeldingen zijn speciaal ontworpen om op Azure te draaien en kunnen eenvoudig worden beheerd via het platform. Credativ ondersteunt ook het onderhoud en de actualisering van de Debian-afbeeldingen voor Azure op lange termijn via zijn Open Source Support Centers.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

De strategie van Oracle is om een breed scala aan oplossingen voor publieke en private clouds aan te bieden. De strategie biedt klanten keuze en flexibiliteit in de manier waarop ze Oracle-software implementeren in Oracle-clouds en andere clouds. Oracle's partnerschap met Microsoft stelt klanten in staat om Oracle-software te implementeren in openbare en private clouds van Microsoft met het vertrouwen van certificering en ondersteuning van Oracle.  De betrokkenheid en investeringen van Oracle in publieke en private cloudoplossingen van Oracle zijn ongewijzigd.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat, 's werelds toonaangevende leverancier van open source-oplossingen, helpt meer dan 90% van de Fortune 500-bedrijven zakelijke uitdagingen op te lossen, hun IT- en bedrijfsstrategieën op elkaar af te stemmen en zich voor te bereiden op de toekomst van technologie. Red Hat doet dit door veilige oplossingen te bieden via een open business model en een betaalbaar, voorspelbaar abonnementsmodel.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server op Azure is een bewezen platform dat superieure betrouwbaarheid en beveiliging biedt voor cloud computing. Het veelzijdige Linux-platform van SUSE integreert naadloos met Azure-cloudservices om een gemakkelijk beheerbare cloudomgeving te bieden. Met meer dan 9.200 gecertificeerde toepassingen van meer dan 1.800 onafhankelijke softwareleveranciers voor SUSE Linux Enterprise Server zorgt SUSE ervoor dat workloads die worden ondersteund in het datacenter met vertrouwen kunnen worden geïmplementeerd op Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical engineering en open community governance stimuleren ubuntu's succes op het gebied van client-, server- en cloudcomputing, waaronder persoonlijke cloudservices voor consumenten. Canonical's visie op een verenigd, gratis platform in Ubuntu, van telefoon tot cloud, biedt een reeks coherente interfaces voor de telefoon, tablet, tv en desktop. Deze visie maakt Ubuntu de eerste keuze voor diverse instellingen, van public cloud providers tot de makers van consumentenelektronica en een favoriet onder individuele technologen.

Met ontwikkelaars en technische centra over de hele wereld is Canonical uniek gepositioneerd om samen te werken met hardwaremakers, contentproviders en softwareontwikkelaars om Ubuntu-oplossingen op de markt te brengen voor pc's, servers en handheld-apparaten.
