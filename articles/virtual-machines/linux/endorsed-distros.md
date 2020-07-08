---
title: Linux-distributies die zijn goedgekeurd op Azure
description: Meer informatie over Linux op door Azure goedgekeurde distributies, inclusief richt lijnen voor Ubuntu, CentOS, Oracle en SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: e51eeb32ecd306986dc22d6bfa09ee95b8b87852
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84658696"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Officiële Linux-distributies op Azure

Partners bieden Linux-installatie kopieën in de Azure Marketplace. Micro soft werkt samen met verschillende Linux-community's om nog meer versies toe te voegen aan de gekeurde distributie lijst. Voor distributies die niet beschikbaar zijn via Marketplace, kunt u altijd uw eigen Linux gebruiken door de richt lijnen te volgen bij het [maken en uploaden van een virtuele harde schijf die het Linux-besturings systeem bevat](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Ondersteunde distributies en versies

De volgende tabel geeft een lijst van de Linux-distributies en-versies die worden ondersteund in Azure. Raadpleeg de [ondersteuning voor Linux-installatie kopieën in Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) voor meer gedetailleerde informatie over ondersteuning voor Linux en open-source technologie in Azure.

De LIS-Stuur programma's (Linux Integration Services) voor Hyper-V en Azure zijn kernel-modules die micro soft rechtstreeks bijdraagt aan de upstream Linux-kernel. Sommige LIS-Stuur Programma's zijn standaard ingebouwd in de kernel van de distributie. Oudere distributies die zijn gebaseerd op Red Hat Enter prise (RHEL)/CentOS zijn verkrijgbaar als afzonderlijke down load bij [Linux Integration Services versie 4,2 voor Hyper-V en Azure](https://www.microsoft.com/download/details.aspx?id=55106). Zie [vereisten voor Linux-kernel](create-upload-generic.md#linux-kernel-requirements) voor meer informatie over de Lis-Stuur Programma's.

De Azure Linux-agent is al vooraf geïnstalleerd op de installatie kopieën van Azure Marketplace en is doorgaans beschikbaar vanuit de opslag plaats van het distributie pakket. U vindt de bron code op [github](https://github.com/azure/walinuxagent).

| Distributie | Versie | Stuurprogramma's | Agent |
| --- | --- | --- | --- |
| CentOS door Rogue Wave software |CentOS 6. x, 7. x, 8. x |CentOS 6,3: [Lis downloaden](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: in kernel |Pakket: in [opslag plaats](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) onder WALinuxAgent <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS is nu [eind tijd](https://coreos.com/os/eol/) vanaf 26 mei 2020. |Niet meer beschikbaar | | |
| Debian door credativ |8. x, 9. x |In kernel |Pakket: in opslag plaats onder waagent <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |
|Flatcar container Linux door Kinvolk| Stabiel, Edge| | |
| Oracle Linux door Oracle |6. x, 7. x, 8. x |In kernel |Pakket: in opslag plaats onder WALinuxAgent <br/>Bron code: [github](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux door Red Hat |6. x, 7. x, 8. x |In kernel |Pakket: in opslag plaats onder WALinuxAgent <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enter prise door SUSE |SLES/SLES voor SAP 11. x, 12. x, 15. x <br/> [Levenscyclus van de open bare Cloud installatie kopie van SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |In kernel |Pakket<p> voor 11 in [Cloud: tools](https://build.opensuse.org/project/show/Cloud:Tools) opslag plaats<br>voor 12 opgenomen in de module ' Public Cloud ' onder ' python-Azure-agent '<br/>Bron code: [github](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE door SUSE |openSUSE Schrikkel 15. x |In kernel |Pakket: in [Cloud: tools](https://build.opensuse.org/project/show/Cloud:Tools) opslag plaats onder ' python-Azure-agent ' <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |
| Ubuntu op canonieke |Ubuntu Server en Pro. 16. x, 18. x, 20. x<p>Informatie over uitgebreide ondersteuning voor Ubuntu 12,04 en 14,04 vindt u hier: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm). |In kernel |Pakket: in opslag plaats onder walinuxagent <br/>Bron code: [github](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>Uitgebracht installatie kopie-update

Azure vereist dat de uitgevers van de getekende Linux-distributies hun installatie kopieën regel matig bijwerken in de Azure Marketplace met de nieuwste patches en beveiligings oplossingen, op een driemaandelijkse of snellere uitgebracht. Bijgewerkte installatie kopieën in azure Marketplace zijn automatisch beschikbaar voor klanten als nieuwe versies van een image SKU. Meer informatie over het zoeken naar Linux-installatie kopieën: [Linux VM-installatie kopieën zoeken in azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

## <a name="azure-tuned-kernels"></a>Door Azure afgestemde kernels

Azure werkt nauw samen met verschillende geplaatste Linux-distributies om de installatie kopieën te optimaliseren die ze naar Azure Marketplace hebben gepubliceerd. Eén aspect van deze samen werking is de ontwikkeling van ' afgestemde ' Linux-kernels die zijn geoptimaliseerd voor het Azure-platform en geleverd als volledig ondersteunde onderdelen van de Linux-distributie. De door Azure afgestemde kernels bevatten nieuwe functies en prestatie verbeteringen en sneller (meestal driemaandelijks) uitgebracht vergeleken met de standaard-of algemene kernels die beschikbaar zijn via de distributie.

In de meeste gevallen vindt u deze kernels die vooraf zijn geïnstalleerd op de standaard installatie kopieën in de Azure Marketplace, zodat klanten direct profiteren van de voor delen van deze geoptimaliseerde kernels. Meer informatie over deze kernels die zijn afgestemd op Azure, vindt u in de volgende koppelingen:

- [CentOS Azure-afgestemde kernel-beschikbaar via de CentOS-virtualisatie-SIG](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian Cloud kernel-beschikbaar met de installatie kopie van Debian 10 en Debian 9 op Azure](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [Door Azure afgestemde kernel SLES](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Door Azure afgestemde kernel Ubuntu](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)

## <a name="partners"></a>Partners

### <a name="coreos"></a>CoreOS

CoreOS is gepland voor [einde levens duur](https://coreos.com/os/eol/) van 26 mei 2020.
Micro soft heeft twee (2) migratie kanalen voor CoreOS-gebruikers.

- Flatcar by Kinvolk (Zie de vermelding ' Flatcar container Linux by Kinvolk '.)
- [Fedora Core-besturings systeem](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (klanten moeten hun eigen installatie kopie uploaden. Hier vindt u de [migratie documentatie](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/)).

### <a name="credativ"></a>Credativ

[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ is een onafhankelijke Consulting-en service maatschappij die is gespecialiseerd in de ontwikkeling en implementatie van professionele oplossingen door gebruik te maken van gratis software. Als toonaangevende open-source specialisten heeft Credativ internationale herkenning met veel IT-afdelingen die hun ondersteuning gebruiken. In combi natie met micro soft wordt Credativ momenteel de bijbehorende Debian-installatie kopieën voor Debian 8 (Jessie) en Debian voor 7 (Wheezy) voor bereid. Beide installatie kopieën zijn speciaal ontworpen om te worden uitgevoerd op Azure en kunnen eenvoudig worden beheerd via het platform. Credativ biedt ook ondersteuning voor de lange termijn onderhoud en het bijwerken van de Debian-installatie kopieën voor Azure via de open source-ondersteunings centra.

### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

De strategie van Oracle is om een breed assortiment oplossingen te bieden voor open bare en particuliere Clouds. De strategie biedt klanten keuze en flexibiliteit bij het implementeren van Oracle-software in Oracle-Clouds en andere Clouds. Door Oracle samen te stellen met micro soft kunnen klanten Oracle-software in open bare en persoonlijke Clouds van micro soft implementeren met het vertrouwen van certificering en ondersteuning van Oracle.  De toezeg ging en investeringen van Oracle in Oracle Public-en Private Cloud-oplossingen worden niet gewijzigd.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Dankzij de wereld wijde provider van open source-oplossingen biedt Red Hat meer dan 90% van de Fortune 500-bedrijven om zakelijke uitdagingen op te lossen, hun IT-en bedrijfs strategieën uit te lijnen en de toekomst van technologie voor te bereiden. Red Hat doet dit door veilige oplossingen te bieden met behulp van een open bedrijfs model en een betaalbaar, voorspelbaar abonnement model.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server op Azure is een bewezen platform dat een superieure betrouw baarheid en beveiliging biedt voor Cloud Computing. Het veelzijdige Linux-platform van SUSE kan naadloos worden geïntegreerd met Azure Cloud Services om een gemakkelijk te beheren cloud omgeving te bieden. Met meer dan 9.200 gecertificeerde toepassingen van meer dan 1.800 onafhankelijke software leveranciers voor SUSE Linux Enterprise Server, zorgt SUSE ervoor dat werk belastingen die worden uitgevoerd in het Data Center, in vertrouwen kunnen worden geïmplementeerd in Azure.

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

De canonieke techniek en het open Community governance-station Ubuntu slagen in client-, server-en Cloud Computing, waaronder persoonlijke Cloud Services voor consumenten. De standaardisatie van een uniform, gratis platform in Ubuntu, van telefoon tot Cloud, biedt een familie van samenhangende interfaces voor de telefoon, Tablet, TV en desktop. Deze visie maakt Ubuntu de eerste keuze voor diverse instellingen van open bare cloud providers voor de makers van consumenten elektronica en een favoriet tussen afzonderlijke Technologists.

Met ontwikkel aars en technische hulp middelen over de hele wereld is canoniek gepositioneerd naar een partner met hardware makers, inhouds providers en software ontwikkelaars om Ubuntu oplossingen op de markt te brengen voor Pc's, servers en hand held apparaten.
