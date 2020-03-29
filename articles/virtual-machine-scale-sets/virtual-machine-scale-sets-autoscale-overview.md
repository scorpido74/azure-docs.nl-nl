---
title: Overzicht van automatische schaalaanpassing met virtuele-machineschaalsets van Azure
description: Meer informatie over de verschillende manieren waarop u een Azure-schaalset voor virtuele machines automatisch schalen op basis van prestaties of een vast schema
author: cynthn
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb96be187502afcccfd3fb2c88f709facfbc3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278145"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Overzicht van automatische schaalaanpassing met virtuele-machineschaalsets van Azure
Een Azure-set voor virtuele machines kan automatisch het aantal VM-exemplaren verhogen of verkleinen waarop uw toepassing wordt uitgevoerd. Dit geautomatiseerde en elastische gedrag vermindert de beheeroverhead om de prestaties van uw toepassing te bewaken en te optimaliseren. U maakt regels die de acceptabele prestaties definiëren voor een positieve klantervaring. Wanneer aan deze gedefinieerde drempelwaarden is voldaan, worden autoscale-regels actie ondernomen om de capaciteit van uw schaalset aan te passen. U ook gebeurtenissen plannen om de capaciteit van uw schaaldie op vaste tijdstippen is ingesteld, automatisch te vergroten of te verlagen. In dit artikel vindt u een overzicht van welke prestatiestatistieken beschikbaar zijn en welke acties automatisch schalen kan uitvoeren.


## <a name="benefits-of-autoscale"></a>Voordelen van autoscale
Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen.

Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke statistieken u moet controleren, zoals CPU of geheugen, hoe lang de toepassingsbelasting aan een bepaalde drempelwaarde moet voldoen en hoeveel VM-exemplaren u aan de schaalset moet toevoegen.

In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.


## <a name="use-host-based-metrics"></a>Statistieken op basis van host gebruiken
U regels voor automatische schaal maken die ingebouwde hoststatistieken beschikbaar hebben in uw VM-exemplaren. Hoststatistieken geven u inzicht in de prestaties van de VM-exemplaren in een schaalset zonder dat u extra agents en gegevensverzamelingen hoeft te installeren of configureren. Regels voor automatisch schalen die deze statistieken gebruiken, kunnen uitschalen of in het aantal VM-exemplaren als reactie op CPU-gebruik, geheugenvraag of schijftoegang.

Regels voor automatisch schalen die gebruikmaken van hostgebaseerde metrische gegevens, kunnen worden gemaakt met een van de volgende hulpprogramma's:

- [Azure-portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure-CLI](tutorial-autoscale-cli.md)
- [Azure-sjabloon](tutorial-autoscale-template.md)

Als u automatische schaalregels wilt maken die meer gedetailleerde prestatiestatistieken gebruiken, u [de Azure-diagnostische extensie installeren en configureren](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) op VM-exemplaren of uw [toepassingsgebruik App Insights configureren.](#application-level-metrics-with-app-insights)

Regels voor automatisch schalen die hostgebaseerde statistieken, vm-statistieken in gast met de Azure-diagnostische extensie gebruiken en App Insights kunnen de volgende configuratie-instellingen gebruiken.

### <a name="metric-sources"></a>Metrische bronnen
Regels voor autoscalen kunnen statistieken uit een van de volgende bronnen gebruiken:

| Bron van metrische gegevens        | Gebruiksvoorbeeld                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Huidige schaalset    | Voor statistieken op basis van host waarvoor geen extra agents hoeven te worden geïnstalleerd of geconfigureerd.                                  |
| Storage-account      | De diagnostische extensie azure schrijft prestatiestatistieken voor Azure-opslag die vervolgens worden verbruikt om regels voor automatisch schalen te activeren. |
| Service Bus-wachtrij    | Uw toepassing of andere onderdelen kunnen berichten verzenden in een Azure Service Bus-wachtrij om regels te activeren.                   |
| Application Insights | Een instrumentatiepakket dat in uw toepassing is geïnstalleerd en dat metrische gegevens rechtstreeks vanuit de app streamt.                         |


### <a name="autoscale-rule-criteria"></a>Regelcriteria voor automatisch schalen
De volgende statistieken op basis van host zijn beschikbaar voor gebruik wanneer u regels voor autoschaal maakt. Als u de diagnostische extensie Azure of App Insights gebruikt, definieert u welke statistieken u wilt controleren en gebruiken met regels voor automatisch schalen.

| Naam van meetwaarde               |
|---------------------------|
| Percentage CPU            |
| Netwerk in                |
| Netwerk uit               |
| Bytes voor schijflezen           |
| Schrijfbytes voor schijf          |
| Bewerkingen voor schijflezen/sec  |
| Bewerkingen voor schijfschrijfbewerkingen/sec |
| Resterende CPU-credits     |
| CPU-credits verbruikt      |

Wanneer u regels voor automatische schaal maakt om een bepaalde statistiek te controleren, worden in de regels een van de volgende acties voor statistiekaggregatie weergegeven:

| Aggregatietype |
|------------------|
| Average          |
| Minimum          |
| Maximum          |
| Totaal            |
| Laatste             |
| Count            |

De regels voor automatische schaal worden vervolgens geactiveerd wanneer de statistieken worden vergeleken met uw gedefinieerde drempelwaarde met een van de volgende operatoren:

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

| Schaalbewerking     | Gebruiksvoorbeeld                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aantal verhogen met   | Een vast aantal vm-exemplaren dat moet worden gemaakt. Handig in schaalsets met een kleiner aantal VM's.                                           |
| Verhoog het percentage met | Een op percentage gebaseerde toename van VM-exemplaren. Goed voor grotere schaal sets waar een vaste toename kan niet merkbaar verbeteren van de prestaties. |
| Aantal verhogen tot   | Maak zoveel mogelijk VM-exemplaren die nodig zijn om een gewenst maximumbedrag te bereiken.                                                            |
| Aantal verlagen met   | Een vast aantal VM-exemplaren dat moet worden verwijderd. Handig in schaalsets met een kleiner aantal VM's.                                           |
| Percentage verlagen door | Een op percentage gebaseerde daling van VM-exemplaren. Goed voor grotere schaalsets waar een vaste stijging het verbruik en de kosten van hulpbronnen niet merkbaar kan verminderen. |
| Aantal verlagen tot   | Verwijder zoveel mogelijk VM-exemplaren die nodig zijn om een gewenst minimumbedrag te bereiken.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Vm-statistieken in gast met de Azure-diagnostische extensie
De Azure-diagnostische extensie is een agent die wordt uitgevoerd in een VM-exemplaar. De agent controleert en slaat prestatiestatistieken op voor Azure-opslag. Deze prestatiestatistieken bevatten meer gedetailleerde informatie over de status van de VM, zoals *AverageReadTime* voor schijven of *PercentIdleTime* voor CPU. U regels voor autoschaal maken op basis van een gedetailleerder bewustzijn van de VM-prestaties, niet alleen het percentage CPU-gebruik of geheugenverbruik.

Als u de Azure-diagnostische extensie wilt gebruiken, moet u Azure-opslagaccounts voor uw VM-exemplaren maken, de Azure-diagnoseagent installeren en vervolgens de VM's configureren om specifieke prestatiemeteritems naar het opslagaccount te streamen.

Zie voor meer informatie de artikelen voor het inschakelen van de diagnostische Azure-extensie op een [Linux-VM](../virtual-machines/extensions/diagnostics-linux.md) of [Windows-VM](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Statistieken op toepassingsniveau met App Insights
Om meer inzicht te krijgen in de prestaties van uw toepassingen, u Application Insights gebruiken. U installeert een klein instrumentatiepakket in uw toepassing dat de app bewaakt en telemetrie naar Azure verzendt. U statistieken controleren, zoals de responstijden van uw toepassing, de prestaties van het laden van pagina's en het aantal sessies. Deze toepassingsstatistieken kunnen worden gebruikt om regels voor automatisch schalen te maken op een gedetailleerd en ingesloten niveau terwijl u regels activeert op basis van bruikbare inzichten die van invloed kunnen zijn op de klantervaring.

Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md) voor meer informatie over App Insights.


## <a name="scheduled-autoscale"></a>Geplande automatische schaal
U ook regels voor autoschaal maken op basis van schema's. Met deze planningsregels u het aantal VM-exemplaren op vaste tijdstippen automatisch schalen. Met op prestaties gebaseerde regels kan dit een prestatie-impact hebben op de toepassing voordat de automatische schaalregels worden geactiveerd en de nieuwe VM-exemplaren zijn ingericht. Als u op een dergelijke vraag anticiperen, zijn de extra VM-exemplaren ingericht en klaar voor het extra gebruik van de klant en de vraag naar toepassingen.

De volgende voorbeelden zijn scenario's die het gebruik van op schema's gebaseerde regels voor autoschaal ten goede kunnen komen:

- Schaal het aantal VM-exemplaren aan het begin van de werkdag automatisch uit wanneer de vraag van de klant toeneemt. Aan het einde van de werkdag u het aantal VM-exemplaren automatisch schalen om de resourcekosten 's nachts te minimaliseren wanneer het toepassingsgebruik laag is.
- Als een afdeling een toepassing in bepaalde delen van de maand of fiscale cyclus intensief gebruikt, schaalt u het aantal VM-exemplaren automatisch om aan hun aanvullende vereisten te voldoen.
- Wanneer er een marketingevenement, promotie of vakantieverkoop is, u het aantal VM-exemplaren automatisch schalen vóór de verwachte vraag van de klant. 


## <a name="next-steps"></a>Volgende stappen
U regels voor automatische schaal maken die hoststatistieken gebruiken met een van de volgende hulpprogramma's:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure-CLI](tutorial-autoscale-cli.md)
- [Azure-sjabloon](tutorial-autoscale-template.md)

In dit overzicht wordt beschreven hoe u regels voor automatisch schalen gebruikt om horizontaal te schalen en het *aantal* VM-exemplaren in uw schaalset te verhogen of te verminderen. U ook verticaal schalen om de *grootte*van de VM-instantie te vergroten of te verkleinen. Zie [Verticale automatische schaal met virtuele machineschaalsets](virtual-machine-scale-sets-vertical-scale-reprovision.md)voor meer informatie.

Zie [Virtuele machineschaalsets beheren met Azure PowerShell](virtual-machine-scale-sets-windows-manage.md)voor informatie over het beheren van uw VM-exemplaren.

Zie Acties voor automatische schaal gebruiken om [e-mail- en webhook-waarschuwingsmeldingen te verzenden in Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md)voor meer informatie over het genereren van waarschuwingen wanneer uw regels voor automatische schaal worden geactiveerd. U ook [controlelogboeken gebruiken om e-mail- en webhook-waarschuwingsmeldingen te verzenden in Azure Monitor.](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
