---
title: Wat is Azure Monitor voor VM's (preview)? | Microsoft Docs
description: Azure Monitor voor VM's is een functie van Azure Monitor die de status en prestaties bewaken van het Azure VM-besturings systeem, en het automatisch detecteren van toepassings onderdelen en afhankelijkheden met andere resources en het toewijzen van de communicatie ertussen. Dit artikel bevat een overzicht.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 2364218a9ccbde1cbfc276e3354cb93e42e5ce0b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553751"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Wat is Azure Monitor voor VM's (preview)?

Azure Monitor voor VM's bewaakt uw Azure virtual machines (VM) en virtuele-machine schaal sets op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen. 

Het bevat ondersteuning voor het bewaken van de prestaties en toepassings afhankelijkheden voor Vm's die lokaal worden gehost of in een andere Cloud provider. Drie belang rijke functies leveren uitgebreid inzicht:

- **Logische onderdelen van Azure vm's en virtuele-machine schaal sets met Windows en Linux**: worden gemeten aan de hand van vooraf geconfigureerde status criteria, en u krijgt een waarschuwing wanneer aan de geëvalueerde voor waarde wordt voldaan.  

- **Vooraf gedefinieerde diagrammen voor trend prestaties**: de metrische prestatie gegevens weer geven van het gast-VM-besturings systeem.

- **Afhankelijkheids kaart**: geeft de onderling verbonden onderdelen weer met de virtuele machine uit verschillende resource groepen en abonnementen.  

De functies zijn onderverdeeld in drie perspectieven:

- Gezondheid
- Prestaties
- Kaart

>[!NOTE]
>We hebben onlangs [Wijzigingen aangekondigd](https://azure.microsoft.com/updates/upcoming-changes-for-azure-monitor-for-vms-as-we-prepare-for-ga) die zijn aangebracht in de status functie op basis van de feedback die we hebben ontvangen van onze open bare preview-klanten. Gezien het aantal wijzigingen dat we maken, zullen we de status functie voor nieuwe klanten niet meer aanbieden. Bestaande klanten kunnen het status onderdeel blijven gebruiken. Raadpleeg de [Veelgestelde vragen over algemene Beschik baarheid](vminsights-ga-release-faq.md)voor meer informatie.  

Integratie met Azure Monitor-Logboeken biedt krachtige aggregatie en filtering en kan gegevens trends na verloop van tijd analyseren. Een dergelijke uitgebreide bewaking van werk belastingen kan niet worden bereikt met alleen Azure Monitor of Servicetoewijzing.  

U kunt deze gegevens rechtstreeks vanuit de virtuele machine weer geven in één VM of u kunt Azure Monitor gebruiken om een geaggregeerde weer gave van uw virtuele machines te leveren. Deze weer gave is gebaseerd op het perspectief van elke functie:

- **Status**: de vm's zijn gerelateerd aan een resource groep.
- **Kaart** en **prestaties**: de vm's zijn geconfigureerd om te rapporteren aan een specifieke log Analytics-werk ruimte.

![Het oogpunt van Virtual Machine Insights in het Azure Portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor voor VM's kunt voorspel bare prestaties en beschik baarheid van essentiële toepassingen bieden. Hiermee worden kritieke gebeurtenissen van het besturings systeem, knel punten in prestaties en netwerk problemen geïdentificeerd. Azure Monitor voor VM's kunt u ook helpen begrijpen of een probleem is gerelateerd aan andere afhankelijkheden.  

## <a name="data-usage"></a>Gegevens gebruik

Wanneer u Azure Monitor voor VM's implementeert, worden de gegevens die worden verzameld door uw Vm's opgenomen en opgeslagen in Azure Monitor. Metrische criteria voor de status worden opgeslagen in Azure Monitor in een Data Base met een tijd reeks, worden de verzamelde prestatie-en afhankelijkheids gegevens opgeslagen in een Log Analytics-werk ruimte. Op basis van de prijzen die worden gepubliceerd op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/), wordt Azure monitor voor VM's gefactureerd voor:

- De gegevens die worden opgenomen en opgeslagen.
- Het aantal metrische tijd criteria dat wordt bewaakt
- De waarschuwings regels die worden gemaakt.
- De meldingen die worden verzonden. 

De logboek grootte is afhankelijk van de teken reeks lengte van prestatie meter items en kan toenemen met het aantal logische schijven en netwerk adapters dat aan de virtuele machine is toegewezen. Als u al een werk ruimte hebt en deze items verzamelt, worden er geen dubbele kosten toegepast. Als u Servicetoewijzing al gebruikt, is de enige wijziging die u ziet de aanvullende verbindings gegevens die naar Azure Monitor worden verzonden.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg [Deploy Azure monitor voor VM's](vminsights-enable-overview.md)voor meer informatie over de vereisten en methoden die u helpen bij het bewaken van uw virtuele machines.
