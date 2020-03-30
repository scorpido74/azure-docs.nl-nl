---
title: Wat is Azure Monitor voor VM's?
description: Overzicht van Azure Monitor voor VM's die de status en prestaties van de Azure VM's bewaakt, naast het automatisch ontdekken en toewijzen van toepassingsonderdelen en hun afhankelijkheden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480484"
---
# <a name="what-is-azure-monitor-for-vms"></a>Wat is Azure Monitor voor VM's?

Azure Monitor voor VM's bewaakt uw virtuele Azure-machines (VM) en de schaalsets voor virtuele machines op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen. Het bevat ondersteuning voor het bewaken van prestaties en toepassingsafhankelijkheden voor VM's die on-premises of in een andere cloudprovider worden gehost. De volgende belangrijke functies bieden diepgaand inzicht:

- **Vooraf gedefinieerde trending performance charts:** Geef core performance metrics van het gast VM-besturingssysteem weer.

- **Afhankelijkheidskaart**: hiermee worden de onderling verbonden componenten met de VM weergegeven uit verschillende resourcegroepen en -abonnementen.  

>[!NOTE]
>We [hebben](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) onlangs wijzigingen aangekondigd die we aanbrengen in de gezondheidsfunctie op basis van de feedback die we hebben ontvangen van onze openbare preview-klanten. Gezien het aantal veranderingen dat we zullen maken, gaan we stoppen met het aanbieden van de Health-functie voor nieuwe klanten. Bestaande klanten kunnen de statusfunctie blijven gebruiken. Voor meer informatie verwijzen wij u naar onze [Algemene Beschikbaarheid FAQ](vminsights-ga-release-faq.md).  

Integratie met Azure Monitor Logs levert krachtige aggregatie en filtering, waardoor Azure Monitor voor VM's in de loop van de tijd gegevenstrends kan analyseren. U deze gegevens rechtstreeks vanuit de virtuele machine in één vm bekijken of u Azure Monitor gebruiken om een geaggregeerde weergave van uw VM's te leveren waarin de weergave azure-resource-context- of werkruimtecontextmodi ondersteunt. Zie [overzicht van de toegangsmodi](../platform/design-logs-deployment.md#access-mode)voor meer informatie.

![Perspectief voor virtuele machineinzichten in de Azure-portal](media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor voor VM's kan voorspelbare prestaties en beschikbaarheid van vitale toepassingen leveren. Het identificeert prestatieknelpunten en netwerkproblemen en kan u ook helpen te begrijpen of een probleem verband houdt met andere afhankelijkheden.  

## <a name="data-usage"></a>Gegevensgebruik

Wanneer u Azure Monitor voor VM's implementeert, worden de gegevens die door uw VM's worden verzameld, ingenomen en opgeslagen in Azure Monitor. Prestatie- en afhankelijkheidsgegevens die worden verzameld, worden opgeslagen in een werkruimte log Analytics. Op basis van de prijzen die op de [prijspagina van Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)worden gepubliceerd, wordt Azure Monitor voor VM's gefactureerd voor:

- De gegevens die worden ingenomen en opgeslagen.
- De waarschuwingsregels die zijn gemaakt.
- De meldingen die worden verzonden. 

De logboekgrootte varieert per tekenreekslengte van prestatiemeteritems en kan toenemen met het aantal logische schijven en netwerkadapters dat aan de VM is toegewezen. Als u al een werkruimte hebt en deze tellers verzamelt, worden er geen dubbele kosten in rekening gebracht. Als u servicetoewijzing al gebruikt, ziet u alleen de extra verbindingsgegevens die naar Azure Monitor worden verzonden.

## <a name="next-steps"></a>Volgende stappen

Als u inzicht wilt krijgen in de vereisten en methoden waarmee u uw virtuele machines controleren, controleert u [Azure-monitor voor VM's implementeren.](vminsights-enable-overview.md)
