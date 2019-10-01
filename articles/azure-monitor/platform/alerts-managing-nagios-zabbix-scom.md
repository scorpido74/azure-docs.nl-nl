---
title: Waarschuwingen beheren vanuit System Center Operations Manager, zabbix en nagios in Azure Monitor
description: Waarschuwingen beheren vanuit System Center Operations Manager, zabbix en nagios in Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 6730402348069df7c2597aa0d73a4211669c2d66
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702835"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Waarschuwingen beheren vanuit System Center Operations Manager, zabbix en nagios in Azure Monitor

U kunt nu uw waarschuwingen weer geven via nagios, zabbix en System Center Operations Manager in [Azure monitor](https://aka.ms/azure-alerts-overview). Deze waarschuwingen zijn afkomstig van integraties met nagios/zabbix-servers of System Center Operations Manager in Log Analytics. 

## <a name="prerequisites"></a>Vereisten
Alle records in de Log Analytics opslag plaats met een type waarschuwing worden geïmporteerd in Azure Monitor. u moet dus de configuratie uitvoeren die vereist is voor het verzamelen van deze records.
1. Configureer voor **nagios** -en **zabbix** -waarschuwingen [die servers](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) om waarschuwingen naar log Analytics te [verzenden](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) .
1. Voor **System Center Operations Manager** waarschuwingen [verbindt u uw Operations Manager-beheer groep met uw log Analytics-werk ruimte](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Hiertoe implementeert u de [Waarschuwingenbeheer](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) oplossing van de Azure Solutions Marketplace. Zodra u klaar bent, worden alle waarschuwingen die zijn gemaakt in System Center Operations Manager, in Log Analytics geïmporteerd.

## <a name="view-your-alert-instances"></a>Uw waarschuwings instanties weer geven
Zodra u het importeren in Log Analytics hebt geconfigureerd, kunt u waarschuwings instanties van deze bewakings Services in [Azure monitor](https://aka.ms/azure-alerts-overview)starten. Zodra ze aanwezig zijn in Azure Monitor, kunt u [uw waarschuwings instanties beheren](https://aka.ms/managing-alert-instances), [slimme groepen beheren die zijn gemaakt voor deze waarschuwingen](https://aka.ms/managing-smart-groups) en [de status van uw waarschuwingen en slimme groepen wijzigen](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Met deze oplossing kunt u alleen System Center Operations Manager/zabbix/nagios geactiveerde waarschuwings instanties in Azure Monitor weer geven. De configuratie van de waarschuwings regel kan alleen worden weer gegeven/bewerkt in de respectieve controle hulpprogramma's. 
>  1. Alle geactiveerde waarschuwings instanties zijn beschikbaar in Azure Monitor en Azure Log Analytics. Er is momenteel geen manier om te kiezen tussen de twee of opname alleen specifieke geactiveerde waarschuwingen.
>  1. Alle waarschuwingen van System Center Operations Manager, zabbix en nagios hebben het signaal type ' onbekend ', omdat het onderliggende telemetrie-type niet beschikbaar is.
>  1. Nagios-waarschuwingen zijn niet stateful: de [bewakings voorwaarde](https://aka.ms/azure-alerts-overview) van een waarschuwing gaat bijvoorbeeld niet van ' geactiveerd ' naar ' opgelost '. In plaats daarvan worden de ' geactiveerde ' en ' opgeloste ' als afzonderlijke waarschuwings instanties weer gegeven. 

