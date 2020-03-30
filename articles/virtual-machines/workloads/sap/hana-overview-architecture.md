---
title: Overzicht van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Overzicht van het implementeren van SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 39fcf5d0fe2273c4debd3ae5ebe5fd1190ddc959
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616954"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Wat is SAP HANA on Azure (grote exemplaren)?

SAP HANA op Azure (Large Instances) is een unieke oplossing voor Azure. Azure biedt u niet alleen virtuele machines voor het implementeren en uitvoeren van SAP HANA, maar biedt u ook de mogelijkheid om SAP HANA uit te voeren en te implementeren op bare-metal servers die aan u zijn gewijd. De SAP HANA-oplossing voor Azure (Large Instances) bouwt voort op niet-gedeelde host/server bare-metal hardware die aan u is toegewezen. De serverhardware is ingebed in grotere stempels die compute/server, netwerk- en opslaginfrastructuur bevatten. Als combinatie is het HANA-datacenterintegratie (TDI) gecertificeerd. SAP HANA op Azure (Large Instances) biedt verschillende serverSKU's of -formaten. Eenheden kunnen 36 Intel CPU-cores en 768 GB geheugen hebben en gaan naar eenheden met maximaal 480 Intel CPU-cores en tot 24 TB geheugen.

De klantisolatie binnen de infrastructuurstempel wordt uitgevoerd bij huurders, die eruit ziet als:

- **Netwerken**: Isolatie van klanten binnen infrastructuurstack via virtuele netwerken per door de klant toegewezen tenant. Een tenant wordt toegewezen aan één klant. Een klant kan meerdere huurders hebben. De netwerkisolatie van huurders verbiedt netwerkcommunicatie tussen huurders op het niveau van de infrastructuurstempel, zelfs als de huurders tot dezelfde klant behoren.
- **Opslagcomponenten:** Isolatie via virtuele opslagmachines waaraan opslagvolumes zijn toegewezen. Opslagvolumes kunnen alleen aan één virtuele opslagmachine worden toegewezen. Een virtuele opslagmachine wordt uitsluitend toegewezen aan één tenant in de SAP HANA TDI-gecertificeerde infrastructuurstack. Als gevolg hiervan kunnen opslagvolumes die zijn toegewezen aan een virtuele opslagmachine alleen in één specifieke en gerelateerde tenant worden geopend. Ze zijn niet zichtbaar tussen de verschillende geïmplementeerde tenants.
- **Server of host:** een server of host-eenheid wordt niet gedeeld tussen klanten of tenants. Een server of host die is geïmplementeerd bij een klant, is een atomaire bare-metal compute unit die is toegewezen aan één tenant. *Er* wordt geen hardwarepartitie of zachte partitionering gebruikt die ertoe kan leiden dat u een host of een server deelt met een andere klant. Opslagvolumes die zijn toegewezen aan de virtuele opslagmachine van de specifieke tenant, worden op een dergelijke server gemonteerd. Een tenant kan een tot veel servereenheden van verschillende SKU's exclusief toegewezen.
- Binnen een SAP HANA op Azure (Large Instances) infrastructuurstempel worden veel verschillende tenants geïmplementeerd en tegen elkaar geïsoleerd via de tenantconcepten op netwerk-, opslag- en rekenniveau. 


Deze bare-metal server units worden ondersteund om SAP HANA alleen uit te voeren. De SAP-toepassingslaag of workload middle-ware-laag wordt uitgevoerd in virtuele machines. De infrastructuurstempels waarop de SAP HANA op Azure (Large Instances)-eenheden worden uitgevoerd, zijn verbonden met de backbones azure network services. Op deze manier wordt low-latency connectiviteit tussen SAP HANA op Azure (Large Instances) eenheden en virtuele machines verstrekt.

Vanaf juli 2019 maken we onderscheid tussen twee verschillende revisies van HANA Large Instance-stempels en locatie van implementaties:

- "Revisie 3" (Rev 3): Zijn de stempels die beschikbaar zijn gesteld voor de klant om te implementeren vóór juli 2019
- 'Revisie 4' (Rev 4): nieuw stempelontwerp dat wordt geïmplementeerd in de nabijheid van Azure VM-hosts en dat tot nu toe is uitgebracht in de Azure-regio's van:
    -  VS - west 2 
    -  VS - oost 
    -  Europa -west
    -  Europa - noord


Dit document is een van de vele documenten die BETREKKING hebben op SAP HANA op Azure (Large Instances). Dit document introduceert de basisarchitectuur, verantwoordelijkheden en services die door de oplossing worden geleverd. Ook de mogelijkheden op hoog niveau van de oplossing komen aan bod. Voor de meeste andere gebieden, zoals netwerken en connectiviteit, vier andere documenten betrekking hebben op details en drill-down informatie. De documentatie van SAP HANA op Azure (Large Instances) heeft geen betrekking op aspecten van de SAP NetWeaver-installatie of -implementaties van SAP NetWeaver in VM's. SAP NetWeaver op Azure is bedekt met afzonderlijke documenten die in dezelfde Azure-documentatiecontainer zijn gevonden. 


De verschillende documenten van HANA Large Instance guidance hebben betrekking op de volgende gebieden:

- [SAP HANA (Large Instances) overzicht en architectuur op Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (Large Instances) infrastructuur en connectiviteit op Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (Large Instances) installeren en configureren op Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (Large Instances) hoge beschikbaarheid en disaster recovery op Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [PROBLEEMOPLOSSING en bewaking van SAP HANA (Large Instances) op Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hoge beschikbaarheid in SUSE met behulp van de STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [OS back-up en herstel voor Type II SKU's van Revisie 3 stempels](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Volgende stappen**
- Verwijs [Ken de voorwaarden](hana-know-terms.md)