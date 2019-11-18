---
title: Wat is Azure Monitor voor virtuele machines (preview)? | Microsoft Docs
description: Azure Monitor voor virtuele machines is een functie van Azure Monitor die de status en prestaties bewaken van het besturingssysteem van de virtuele machine van Azure, evenals automatisch detecteren van onderdelen van de toepassing en afhankelijkheden met andere resources worden gecombineerd en wijst de communicatie tussen beide. In dit artikel bevat een overzicht.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 84ebf92f795dfe1a21570d782bf24fad27f50eda
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109054"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Wat is Azure Monitor voor virtuele machines (preview)?

Azure Monitor voor virtuele machines bewaakt uw Azure virtual machines (VM) en virtuele-machineschaalsets op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen. 

Het bevat ondersteuning voor het bewaken van de prestaties en toepassings afhankelijkheden voor Vm's die lokaal worden gehost of in een andere Cloud provider. De volgende belang rijke functies leveren uitgebreid inzicht:

- **Vooraf gedefinieerde diagrammen voor trend prestaties**: de metrische prestatie gegevens weer geven van het gast-VM-besturings systeem.

- **Kaart van afhankelijkheden**: bevat de onderdelen met elkaar verbonden met de virtuele machine vanuit de verschillende resourcegroepen en abonnementen.  

>[!NOTE]
>We hebben onlangs [Wijzigingen aangekondigd](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) die zijn aangebracht in de status functie op basis van de feedback die we hebben ontvangen van onze open bare preview-klanten. Gezien het aantal wijzigingen dat we maken, zullen we de status functie voor nieuwe klanten niet meer aanbieden. Bestaande klanten kunnen het status onderdeel blijven gebruiken. Raadpleeg de [Veelgestelde vragen over algemene Beschik baarheid](vminsights-ga-release-faq.md)voor meer informatie.  

Integratie met Azure Monitor-Logboeken biedt krachtige aggregatie en filtering en kan gegevens trends na verloop van tijd analyseren. Een dergelijke uitgebreide bewaking van werk belastingen kan niet worden bereikt met alleen Azure Monitor of Servicetoewijzing.  

U kunt deze gegevens rechtstreeks vanuit de virtuele machine weer geven in één VM of u kunt Azure Monitor gebruiken om een geaggregeerde weer gave van uw Vm's te leveren waar de weer gave ondersteuning biedt voor Azure resource-context of werk ruimte context modi. Zie [overzicht van toegangs modi](../platform/design-logs-deployment.md#access-mode)voor meer informatie.

![Perspectief van de virtuele machine inzicht in de Azure-portal](./media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor voor VM's kunt voorspel bare prestaties en beschik baarheid van essentiële toepassingen bieden. Hiermee worden knel punten in de prestaties en netwerk problemen geïdentificeerd. Azure Monitor voor VM's kunt u ook helpen begrijpen of een probleem is gerelateerd aan andere afhankelijkheden.  

## <a name="data-usage"></a>Gegevensgebruik

Wanneer u Azure Monitor voor virtuele machines implementeert, wordt de gegevens die worden verzameld door uw virtuele machines die zijn opgenomen en opgeslagen in Azure Monitor. De verzamelde prestaties en afhankelijkheids gegevens worden opgeslagen in een Log Analytics-werk ruimte. Op de prijzen die gepubliceerd op basis van de [Azure Monitor-pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/), Azure-Monitor voor virtuele machines wordt in rekening gebracht voor:

- De gegevens die is opgenomen en opgeslagen.
- De regels voor waarschuwingen die zijn gemaakt.
- De meldingen die worden verzonden. 

De logboek grootte is afhankelijk van de teken reeks lengte van prestatie meter items en kan toenemen met het aantal logische schijven en netwerk adapters dat aan de virtuele machine is toegewezen. Als u al een werkruimte hebt en deze prestatiemeteritems zijn verzameld, worden geen kosten verbonden aan dubbele toegepast. Als u al Serviceoverzicht, is de enige wijziging u ziet de extra verbinding-gegevens die worden verzonden naar Azure Monitor.

## <a name="next-steps"></a>Volgende stappen

Bekijk voor meer informatie over de vereisten en de methoden die u helpen bij uw virtuele machines controleren, [implementeert Azure Monitor voor virtuele machines](vminsights-enable-overview.md).
