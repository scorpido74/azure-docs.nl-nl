---
title: Overzicht van automatische schaalaanpassing met virtuele-machineschaalsets van Azure
description: Meer informatie over de verschillende manieren waarop u een schaalset voor virtuele Azure-machines automatisch kunt schalen op basis van prestaties of volgens een vast schema
author: ju-shim
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b3af40de107fd9273ec50425c58afc9772ea426
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010338"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Overzicht van automatische schaalaanpassing met virtuele-machineschaalsets van Azure
Een schaalset voor virtuele Azure-machines kan het aantal VM-exemplaren waarop uw toepassing wordt uitgevoerd, automatisch verg Roten of verkleinen. Dit geautomatiseerde en elastische gedrag vermindert de beheer overhead om de prestaties van uw toepassing te controleren en te optimaliseren. U kunt regels maken waarmee de aanvaard bare prestaties voor een positieve klant ervaring worden gedefinieerd. Wanneer aan deze gedefinieerde drempel waarden wordt voldaan, nemen regels voor automatisch schalen actie in om de capaciteit van de schaalset aan te passen. U kunt ook gebeurtenissen plannen voor het automatisch verg Roten of verkleinen van de capaciteit van de schaalset op vaste tijden. In dit artikel vindt u een overzicht van de prestatie gegevens die beschikbaar zijn en welke acties automatisch schalen kunnen worden uitgevoerd.


## <a name="benefits-of-autoscale"></a>Voor delen van automatisch schalen
Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen.

Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke metrische gegevens moeten worden bewaakt, zoals CPU of geheugen, hoe lang het laden van de toepassing moet voldoen aan een bepaalde drempel waarde en hoeveel VM-exemplaren moeten worden toegevoegd aan de schaalset.

In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.


## <a name="use-host-based-metrics"></a>Metrische gegevens op basis van een host gebruiken
U kunt regels voor automatisch schalen maken waarmee de metrische gegevens van de host worden weer gegeven die beschikbaar zijn via uw VM-exemplaren. Met metrische gegevens van de host krijgt u inzicht in de prestaties van de VM-exemplaren in een schaalset zonder dat u extra agents en gegevens verzamelingen hoeft te installeren of configureren. Regels voor automatisch schalen die gebruikmaken van deze metrische gegevens kunnen worden uitgeschaald of in het aantal VM-exemplaren in reactie op CPU-gebruik, geheugen vraag of toegang tot de schijf.

Regels voor automatisch schalen die gebruikmaken van hostgebaseerde metrische gegevens, kunnen worden gemaakt met een van de volgende hulpprogramma's:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure-CLI](tutorial-autoscale-cli.md)
- [Azure-sjabloon](tutorial-autoscale-template.md)

Als u regels voor automatisch schalen wilt maken die meer gedetailleerde metrische gegevens over prestaties gebruiken, kunt u [de Azure Diagnostics-extensie installeren en configureren](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) voor VM-exemplaren of [uw toepassing configureren met behulp van app Insights](#application-level-metrics-with-app-insights).

Regels voor automatisch schalen die gebruikmaken van metrische gegevens op basis van een host, metrische gegevens in de gast-VM met de diagnostische extensie van Azure, en app Insights kunnen de volgende configuratie-instellingen gebruiken.

### <a name="metric-sources"></a>Metrische bronnen
Regels voor automatisch schalen kunnen metrische gegevens uit een van de volgende bronnen gebruiken:

| Bron van metrische gegevens        | Gebruiksvoorbeeld                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Huidige schaalset    | Voor metrische gegevens op basis van een host waarvoor extra agents niet hoeven te worden geïnstalleerd of geconfigureerd.                                  |
| Storage-account      | De diagnostische Azure-extensie schrijft prestatie gegevens naar Azure Storage die vervolgens worden gebruikt om regels voor automatisch schalen te activeren. |
| Service Bus-wachtrij    | Uw toepassing of andere onderdelen kunnen berichten in een Azure Service Bus wachtrij verzenden om regels te activeren.                   |
| Application Insights | Een instrumentatie pakket dat in uw toepassing is geïnstalleerd en waarmee metrische gegevens rechtstreeks vanuit de app worden gestreamd.                         |


### <a name="autoscale-rule-criteria"></a>Regel criteria voor automatisch schalen
De volgende metrische gegevens op basis van een host zijn beschikbaar voor gebruik wanneer u regels voor automatisch schalen maakt. Als u de diagnostische extensie van Azure of app Insights gebruikt, definieert u welke metrische gegevens u wilt bewaken en gebruiken met regels voor automatisch schalen.

| Naam van meetwaarde               |
|---------------------------|
| Percentage CPU            |
| Netwerk in                |
| Netwerk uit               |
| Gelezen bytes op de schijf           |
| Geschreven bytes op de schijf          |
| Lees bewerkingen op de schijf per seconde  |
| Schrijf bewerkingen op de schijf per seconde |
| Resterende CPU-tegoeden     |
| Verbruikte CPU-tegoeden      |

Wanneer u regels voor automatisch schalen maakt om een bepaalde metriek te bewaken, bekijken de regels een van de volgende aggregatie acties voor metrische gegevens:

| Aggregatietype |
|------------------|
| Average          |
| Minimum          |
| Maximum          |
| Totaal            |
| Laatste             |
| Count            |

De regels voor automatisch schalen worden vervolgens geactiveerd wanneer de metrische gegevens worden vergeleken met uw gedefinieerde drempel waarde met een van de volgende Opera tors:

| Operator                 |
|--------------------------|
| Groter dan             |
| Groter dan of gelijk aan |
| Kleiner dan                |
| Kleiner dan of gelijk aan    |
| Gelijk aan                 |
| Niet gelijk aan             |


### <a name="actions-when-rules-trigger"></a>Acties wanneer regels worden geactiveerd
Wanneer een regel voor automatisch schalen wordt geactiveerd, kan uw schaalset automatisch op een van de volgende manieren worden geschaald:

| Schaal bewerking     | Gebruiksvoorbeeld                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aantal verhogen met   | Een vast aantal VM-exemplaren dat moet worden gemaakt. Handig in schaal sets met een kleiner aantal Vm's.                                           |
| Percentage verhogen met | Een toename op basis van een percentage van VM-exemplaren. Geschikt voor grotere schaal sets waarbij een vaste verhoging mogelijk niet merkbaar de prestaties kan verbeteren. |
| Aantal verhogen tot   | Maak zoveel VM-exemplaren nodig om een gewenste maximum hoeveelheid te bereiken.                                                            |
| Aantal verlagen met   | Een vast aantal VM-exemplaren dat moet worden verwijderd. Handig in schaal sets met een kleiner aantal Vm's.                                           |
| Percentage verlagen met | Een verlaging op basis van een percentage van VM-exemplaren. Geschikt voor grotere schaal sets waarbij een vaste toename het Resource verbruik en de kosten mogelijk niet merkbaar vermindert. |
| Aantal verlagen tot   | Verwijder zoveel VM-exemplaren die nodig zijn om een gewenste minimum hoeveelheid te bereiken.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metrische gegevens over de virtuele machine in de gast met de Azure Diagnostics-extensie
De Azure Diagnostics-extensie is een agent die wordt uitgevoerd binnen een VM-exemplaar. De agent bewaakt en slaat de metrische gegevens over prestaties naar Azure Storage. Deze metrische gegevens voor prestaties bevatten meer gedetailleerde informatie over de status van de virtuele machine, zoals *AverageReadTime* voor schijven of *PercentIdleTime* voor CPU. U kunt regels voor automatisch schalen maken op basis van een gedetailleerder inzicht in de prestaties van de virtuele machine, niet alleen het percentage CPU-gebruik of geheugen gebruik.

Als u de Azure Diagnostics-extensie wilt gebruiken, moet u Azure Storage-accounts voor uw VM-exemplaren maken, de Azure Diagnostics-agent installeren en vervolgens de virtuele machines configureren voor het streamen van specifieke prestatie meter items naar het opslag account.

Zie voor meer informatie de artikelen voor het inschakelen van de diagnostische Azure-extensie op een [Linux-VM](../virtual-machines/extensions/diagnostics-linux.md) of [Windows-VM](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metrische gegevens op toepassings niveau met app Insights
Als u meer inzicht wilt krijgen in de prestaties van uw toepassingen, kunt u Application Insights gebruiken. U installeert een klein instrumentatie pakket in uw toepassing waarmee de app wordt bewaakt en telemetrie naar Azure wordt verzonden. U kunt metrische gegevens bewaken, zoals de reactie tijden van uw toepassing, de prestaties van de pagina belasting en de sessie aantallen. Deze metrische gegevens van toepassingen kunnen worden gebruikt voor het maken van regels voor automatisch schalen op een uitgebreid en Inge sloten niveau wanneer u regels regelt op basis van inzichten met actie die van invloed kunnen zijn op de ervaring van de klant.

Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md) voor meer informatie over App Insights.


## <a name="scheduled-autoscale"></a>Geplande automatisch schalen
U kunt ook regels voor automatisch schalen maken op basis van planningen. Met deze regels op basis van een schema kunt u het aantal VM-exemplaren op vaste tijden automatisch schalen. Met regels op basis van prestaties kan de toepassing invloed hebben op de prestaties voordat de regels voor automatisch schalen worden geactiveerd en de nieuwe VM-exemplaren worden ingericht. Als u een dergelijke vraag kunt verwachten, worden de extra VM-exemplaren ingericht en gereed voor het extra klant gebruik en de vraag van de toepassing.

De volgende voor beelden zijn scenario's die kunnen profiteren van het gebruik van regels voor automatisch schalen op basis van een planning:

- Schaal automatisch het aantal VM-exemplaren aan het begin van de werkdag wanneer de vraag van de klant toeneemt. Aan het einde van de werkdag wordt het aantal VM-exemplaren automatisch geschaald om de resource kosten 's nachts te minimaliseren wanneer het gebruik van de toepassing laag is.
- Als een afdeling een toepassing intensief gebruikt voor bepaalde onderdelen van de maand of de fiscale cyclus, wordt het aantal VM-exemplaren automatisch geschaald om te voldoen aan de extra vereisten.
- Wanneer er sprake is van een marketing gebeurtenis, promotie of Kerst verkoop, kunt u het aantal VM-exemplaren automatisch laten schalen vóór de verwachte vraag van de klant. 


## <a name="next-steps"></a>Volgende stappen
U kunt regels voor automatisch schalen maken die metrische gegevens op basis van een host gebruiken met een van de volgende hulpprogram ma's:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure-CLI](tutorial-autoscale-cli.md)
- [Azure-sjabloon](tutorial-autoscale-template.md)

In dit overzicht wordt uitgelegd hoe u regels voor automatisch schalen gebruikt om horizon taal te schalen en het *aantal* VM-exemplaren in uw schaalset te verg Roten of verkleinen. U kunt ook verticaal schalen om de *grootte*van het VM-exemplaar te verg Roten of verkleinen. Zie [verticaal automatisch schalen met virtuele-machine schaal sets](virtual-machine-scale-sets-vertical-scale-reprovision.md)voor meer informatie.

Zie [schaal sets voor virtuele machines beheren met Azure PowerShell](virtual-machine-scale-sets-windows-manage.md)voor meer informatie over het beheren van uw VM-exemplaren.

Zie voor meer informatie over het genereren van waarschuwingen wanneer uw regels voor automatisch schalen de trigger [acties voor automatisch schalen gebruiken voor het verzenden van e-mail en waarschuwingen over webhooks in azure monitor](../azure-monitor/platform/autoscale-webhook-email.md). U kunt ook [controle Logboeken gebruiken voor het verzenden van e-mail en webhook-waarschuwings meldingen in azure monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
