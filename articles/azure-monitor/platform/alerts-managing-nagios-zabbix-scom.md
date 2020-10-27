---
title: Waarschuwingen beheren vanuit System Center Operations Manager, Zabbix en Nagios in Azure Monitor
description: Waarschuwingen beheren vanuit System Center Operations Manager, Zabbix en Nagios in Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 367a7020e56db7105da7624c7911ae5b59f1365a
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558868"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Waarschuwingen beheren vanuit System Center Operations Manager, Zabbix en Nagios in Azure Monitor

U kunt nu uw waarschuwingen weer geven via nagios, zabbix en System Center Operations Manager in [Azure monitor](./alerts-overview.md). Deze waarschuwingen zijn afkomstig van integraties met nagios/zabbix-servers of System Center Operations Manager in Log Analytics. 

## <a name="prerequisites"></a>Vereisten
Alle records in de Log Analytics opslag plaats met een type waarschuwing worden geïmporteerd in Azure Monitor. u moet dus de configuratie uitvoeren die vereist is voor het verzamelen van deze records.
1. Configureer voor **nagios** -en **zabbix** -waarschuwingen [die servers](../learn/quick-collect-linux-computer.md) om waarschuwingen naar log Analytics te [verzenden](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) .
1. Voor **System Center Operations Manager** waarschuwingen [verbindt u uw Operations Manager-beheer groep met uw log Analytics-werk ruimte](./om-agents.md). Hiertoe implementeert u de [Waarschuwingenbeheer](./alert-management-solution.md) oplossing van de Azure Solutions Marketplace. Zodra u klaar bent, worden alle waarschuwingen die zijn gemaakt in System Center Operations Manager, in Log Analytics geïmporteerd.

## <a name="view-your-alert-instances"></a>Uw waarschuwings instanties weer geven
Zodra u het importeren in Log Analytics hebt geconfigureerd, kunt u waarschuwings instanties van deze bewakings Services in [Azure monitor](./alerts-overview.md)starten. Zodra ze aanwezig zijn in Azure Monitor, kunt u [uw waarschuwings instanties beheren](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json), [slimme groepen beheren die zijn gemaakt voor deze waarschuwingen](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json) en [de status van uw waarschuwingen en slimme groepen wijzigen](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json).

> [!NOTE]
>  1. Met deze oplossing kunt u alleen System Center Operations Manager/zabbix/nagios geactiveerde waarschuwings instanties in Azure Monitor weer geven. De configuratie van de waarschuwings regel kan alleen worden weer gegeven/bewerkt in de respectieve controle hulpprogramma's. 
>  1. Alle geactiveerde waarschuwings instanties zijn beschikbaar in Azure Monitor en Azure Log Analytics. Er is momenteel geen manier om te kiezen tussen de twee of opname alleen specifieke geactiveerde waarschuwingen.
>  1. Alle waarschuwingen van System Center Operations Manager, zabbix en nagios hebben het signaal type ' onbekend ', omdat het onderliggende telemetrie-type niet beschikbaar is.
>  1. Nagios-waarschuwingen zijn niet stateful: de [bewakings voorwaarde](./alerts-overview.md) van een waarschuwing gaat bijvoorbeeld niet van ' geactiveerd ' naar ' opgelost '. In plaats daarvan worden de ' geactiveerde ' en ' opgeloste ' als afzonderlijke waarschuwings instanties weer gegeven.