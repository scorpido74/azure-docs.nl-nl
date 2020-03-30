---
title: Waarschuwingen beheren van System Center Operations Manager, Zabbix en Nagios in Azure Monitor
description: Waarschuwingen beheren van System Center Operations Manager, Zabbix en Nagios in Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667445"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Waarschuwingen beheren van System Center Operations Manager, Zabbix en Nagios in Azure Monitor

U uw waarschuwingen nu bekijken van Nagios, Zabbix en System Center Operations Manager in [Azure Monitor.](https://aka.ms/azure-alerts-overview) Deze waarschuwingen zijn afkomstig van integraties met Nagios/Zabbix-servers of System Center Operations Manager in Log Analytics. 

## <a name="prerequisites"></a>Vereisten
Alle records in de Log Analytics-opslagplaats met een type waarschuwing worden geïmporteerd in Azure Monitor, dus u moet de configuratie uitvoeren die nodig is om deze records te verzamelen.
1. Configureer [deze servers](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) voor **Nagios-** en **Zabbix-waarschuwingen** om waarschuwingen naar Log Analytics te [verzenden.](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json)
1. Voor **waarschuwingen voor Operations Manager** van Systeemcentrum koppelt u uw [beheergroep Operations Manager aan uw log analytics-werkruimte](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Implementeer hierna de [oplossing voor waarschuwingsbeheer](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) vanuit de Azure-oplossingsmarkt. Na deze melding worden alle waarschuwingen die zijn gemaakt in System Center Operations Manager geïmporteerd in Log Analytics.

## <a name="view-your-alert-instances"></a>Uw waarschuwingsinstanties weergeven
Nadat u de import in Logboekanalyse hebt geconfigureerd, u waarschuwingsinstanties van deze bewakingsservices in [Azure Monitor](https://aka.ms/azure-alerts-overview)weergeven. Zodra ze aanwezig zijn in Azure Monitor, u [uw waarschuwingsinstanties beheren,](https://aka.ms/managing-alert-instances) [slimme groepen beheren die zijn gemaakt met deze waarschuwingen](https://aka.ms/managing-smart-groups) en de status van uw [waarschuwingen en slimme groepen wijzigen.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  1. Met deze oplossing u alleen System Center Operations Manager/Zabbix/Nagios-waarschuwingsexemplaren bekijken in Azure Monitor. De configuratie van waarschuwingsregels kan alleen worden bekeken/bewerkt in de desbetreffende bewakingshulpprogramma's. 
>  1. Alle geactiveerde waarschuwingsexemplaren zijn beschikbaar in Azure Monitor en Azure Log Analytics. Momenteel is er geen manier om te kiezen tussen de twee of inname alleen specifieke ontslagen waarschuwingen.
>  1. Alle waarschuwingen van System Center Operations Manager, Zabbix en Nagios hebben het signaaltype "Onbekend", omdat het onderliggende telemetrietype niet beschikbaar is.
>  1. Nagios-waarschuwingen zijn niet stateful - bijvoorbeeld, de [monitor voorwaarde](https://aka.ms/azure-alerts-overview) van een waarschuwing zal niet gaan van "Ontslagen" naar "Opgelost". In plaats daarvan worden zowel de "Ontslagen" als "Opgelost" weergegeven als afzonderlijke waarschuwingsinstanties. 

