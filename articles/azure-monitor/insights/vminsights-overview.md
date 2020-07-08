---
title: " Wat is Azure Monitor voor VM's?"
description: Overzicht van Azure Monitor voor VM's waarmee de status en prestaties van de virtuele Azure-machines worden gecontroleerd naast het automatisch detecteren en toewijzen van toepassings onderdelen en hun afhankelijkheden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: f058ce1ae5b7328c6864684994a74f2fd118ca6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506987"
---
# <a name="what-is-azure-monitor-for-vms"></a> Wat is Azure Monitor voor VM's?

Azure Monitor voor VM's bewaakt uw virtuele machines, virtuele-machine schaal sets en Azure-Arc-machines op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen. Er is ondersteuning voor het controleren van prestatie- en toepassingsafhankelijkheden voor VM's die on-premises of door een andere cloudprovider worden gehost. De volgende belang rijke functies leveren uitgebreid inzicht:

- **Vooraf gedefinieerde diagrammen voor trend prestaties**: de metrische prestatie gegevens weer geven van het gast-VM-besturings systeem.

- **Afhankelijkheids kaart**: geeft de onderling verbonden onderdelen weer met de virtuele machine uit verschillende resource groepen en abonnementen.  

>[!NOTE]
>We hebben onlangs [Wijzigingen aangekondigd](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) die zijn aangebracht in de status functie op basis van de feedback die we hebben ontvangen van onze open bare preview-klanten. Gezien het aantal wijzigingen dat we maken, zullen we de status functie voor nieuwe klanten niet meer aanbieden. Bestaande klanten kunnen het status onderdeel blijven gebruiken. Raadpleeg de [Veelgestelde vragen over algemene Beschik baarheid](vminsights-ga-release-faq.md)voor meer informatie.  

Integratie met Azure Monitor-Logboeken biedt krachtige aggregatie en filters, waardoor Azure Monitor voor VM's gegevens trends in de loop van de tijd kunt analyseren. U kunt deze gegevens rechtstreeks vanuit de virtuele machine weer geven in één VM of u kunt Azure Monitor gebruiken om een geaggregeerde weer gave van uw Vm's te leveren waar de weer gave ondersteuning biedt voor Azure resource-context of werk ruimte context modi. Zie [overzicht van toegangs modi](../platform/design-logs-deployment.md#access-mode)voor meer informatie.

![Het oogpunt van Virtual Machine Insights in het Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor voor VM's kunt voorspel bare prestaties en beschik baarheid van essentiële toepassingen bieden. Hiermee worden knel punten in de prestaties en netwerk problemen geïdentificeerd en kunt u ook zien of een probleem te maken heeft met andere afhankelijkheden.  

## <a name="data-usage"></a>Gegevensgebruik

Wanneer u Azure Monitor voor VM's implementeert, worden de gegevens die worden verzameld door uw Vm's opgenomen en opgeslagen in Azure Monitor. De verzamelde prestaties en afhankelijkheids gegevens worden opgeslagen in een Log Analytics-werk ruimte. Op basis van de prijzen die worden gepubliceerd op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/), wordt Azure monitor voor VM's gefactureerd voor:

- De gegevens die worden opgenomen en opgeslagen.
- De waarschuwings regels die worden gemaakt.
- De meldingen die worden verzonden. 

De logboek grootte is afhankelijk van de teken reeks lengte van prestatie meter items en kan toenemen met het aantal logische schijven en netwerk adapters dat aan de virtuele machine is toegewezen. Als u al een werk ruimte hebt en deze items verzamelt, worden er geen dubbele kosten toegepast. Als u Servicetoewijzing al gebruikt, is de enige wijziging die u ziet de aanvullende verbindings gegevens die naar Azure Monitor worden verzonden.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg [Deploy Azure monitor voor VM's](vminsights-enable-overview.md)voor meer informatie over de vereisten en methoden die u helpen bij het bewaken van uw virtuele machines.
