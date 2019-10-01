---
title: Waarschuwingenbeheeroplossing in Azure Log Analytics | Microsoft Docs
description: De oplossing voor beheer van waarschuwingen in Log Analytics helpt u bij het analyseren van alle van de waarschuwingen in uw omgeving.  Naast consolideren waarschuwingen gegenereerd in Log Analytics, importeert deze waarschuwingen van verbonden beheergroepen voor System Center Operations Manager in Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: e2f195f648f08c31fbfe44543ee763aeed7459f0
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702973"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Waarschuwingenbeheeroplossing in Azure Log Analytics

![Pictogram voor het beheer van waarschuwing](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Monitor ondersteunt nu verbeterde mogelijkheden voor [het beheren van uw waarschuwingen op schaal](https://aka.ms/azure-alerts-overview), met inbegrip van de functies die worden gegenereerd door [controle hulpprogramma's als System Center Operations Manager, zabbix of nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


De Waarschuwingsbeheer oplossing helpt u bij het analyseren van alle van de waarschuwingen in uw Log Analytics-opslagplaats.  Deze waarschuwingen kunnen afkomstig zijn uit een groot aantal bronnen die bronnen waaronder [die zijn gemaakt door Log Analytics](../../azure-monitor/platform/alerts-overview.md) of [geïmporteerd uit Nagios of Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). De oplossing ook waarschuwingen importeert uit een [System Center Operations Manager-beheergroepen verbonden](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Vereisten
De oplossing werkt met alle records in de opslagplaats van Log Analytics met een type **waarschuwing**, dus moet u de gewenste configuratie is vereist voor het verzamelen van deze records uitvoeren.

- Voor Log Analytics-waarschuwingen, [waarschuwingsregels maken](../../azure-monitor/platform/alerts-overview.md) waarschuwing om records te maken in de opslagplaats.
- Voor Nagios en Zabbix-waarschuwingen, [configureren die servers](../../azure-monitor/learn/quick-collect-linux-computer.md) om waarschuwingen te verzenden naar Log Analytics.
- Voor System Center Operations Manager-waarschuwingen, [uw Operations Manager-beheergroep verbinden met uw Log Analytics-werkruimte](../../azure-monitor/platform/om-agents.md).  Alle waarschuwingen die zijn gemaakt in System Center Operations Manager worden geïmporteerd in Log Analytics.  

## <a name="configuration"></a>Configuratie
De oplossing voor beheer van waarschuwingen toevoegen aan uw Log Analytics-werkruimte met behulp van de procedure beschreven in [oplossingen toevoegen](../../azure-monitor/insights/solutions.md). Er is geen verdere configuratie nodig.

## <a name="management-packs"></a>Management packs
Als uw System Center Operations Manager-beheergroep is verbonden met uw Log Analytics-werkruimte, klikt u vervolgens de volgende management packs geïnstalleerd in System Center Operations Manager wanneer u deze oplossing toevoegt.  Er is geen configuratie of onderhoud van de management packs vereist.

* Microsoft System Center Advisor Waarschuwingsbeheer (Microsoft.IntelligencePacks.AlertManagement)

Zie [Operations Manager koppelen aan Log Analytics](../../azure-monitor/platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="data-collection"></a>Gegevensverzameling
### <a name="agents"></a>Agents
De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteuning | Description |
|:--- |:--- |:--- |
| [Windows-agents](agent-windows.md) | Nee |Direct Windows-agents geen waarschuwingen worden gegenereerd.  Log Analytics-waarschuwingen kunnen worden gemaakt op basis van gebeurtenissen en prestatiegegevens verzameld van Windows-agents. |
| [Linux-agents](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nee |Directe Linux-agents geen waarschuwingen worden gegenereerd.  Log Analytics-waarschuwingen kunnen worden gemaakt van gebeurtenissen en prestatiegegevens verzameld van Linux-agents.  Nagios en Zabbix-waarschuwingen worden verzameld van deze servers waarvoor de Linux-agent. |
| [System Center Operations Manager-beheergroep](../../azure-monitor/platform/om-agents.md) |Ja |Waarschuwingen die worden gegenereerd in Operations Manager-agents zijn die worden geleverd aan de beheergroep en vervolgens doorgestuurd naar Log Analytics.<br><br>Een directe verbinding van Operations Manager-agents naar Log Analytics is niet vereist. Waarschuwingsgegevens wordt uit de beheergroep doorgestuurd naar de opslagplaats van Log Analytics. |


### <a name="collection-frequency"></a>Verzamelingsfrequentie
- Waarschuwing records zijn beschikbaar voor de oplossing zodra ze zijn opgeslagen in de opslagplaats.
- Waarschuwingsgegevens wordt verzonden vanaf de Operations Manager-beheergroep naar Log Analytics om de drie minuten.  

## <a name="using-the-solution"></a>De oplossing gebruiken
Wanneer u de waarschuwing beheeroplossing aan uw Log Analytics-werkruimte toevoegt, de **Waarschuwingsbeheer** tegel aan uw dashboard is toegevoegd.  Deze tegel toont een telling en grafische weergave van het aantal actieve waarschuwingen die zijn gegenereerd in de afgelopen 24 uur.  U kunt dit tijdsbereik niet wijzigen.

![Waarschuwing tegel-beheer](media/alert-management-solution/tile.png)

Klik op de **Waarschuwingsbeheer** tegel om te openen de **Waarschuwingsbeheer** dashboard.  Het dashboard bevat de kolommen in de volgende tabel.  Elke kolom bevat de bovenste 10 waarschuwingen per aantal die overeenkomen met criteria voor het opgegeven bereik en het tijdsbereik van die kolom.  U kunt een logboekzoekopdracht waarmee de volledige lijst door te klikken op uitvoeren **alle** aan de onderkant van de kolom of door op de kolomkop te klikken.

| Kolom | Description |
|:--- |:--- |
| Kritieke waarschuwingen |Alle waarschuwingen met een ernst gegroepeerd op de naam van de waarschuwing kritiek.  Klik op de naam van een waarschuwing om uit te voeren van een logboekzoekopdracht die alle records voor deze waarschuwing. |
| Waarschuwingsmeldingen |Alle waarschuwingen met een ernst van waarschuwing gegroepeerd op de naam van de waarschuwing.  Klik op de naam van een waarschuwing om uit te voeren van een logboekzoekopdracht die alle records voor deze waarschuwing. |
| Waarschuwingen voor actieve System Center Operations Manager |Alle waarschuwingen die zijn verzameld uit Operations Manager met een staat met andere dan *gesloten* gegroepeerd op de bron die de waarschuwing heeft gegenereerd. |
| Alle actieve waarschuwingen |Alle waarschuwingen met een gegroepeerd op de naam van waarschuwing ernst. Bevat alleen de Operations Manager-waarschuwingen met een staat met andere dan *gesloten*. |

Als u naar rechts schuift, het dashboard hier worden enkele algemene query's die u klikken kunt op om uit te voeren een [zoeken in logboeken](../../azure-monitor/log-query/log-query-overview.md) voor waarschuwingsgegevens.

![Beheer van een waarschuwingendashboard](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-records
De waarschuwing oplossing analyseert elke record van het type **waarschuwing**.  Waarschuwingen die zijn gemaakt door Log Analytics of die worden verzameld van Nagios of Zabbix worden niet rechtstreeks verzameld door de oplossing.

De oplossing waarschuwingen importeren uit System Center Operations Manager en maakt u een record voor elk type **waarschuwing** en SourceSystem **OpsManager**.  Deze records hebben de eigenschappen in de volgende tabel:  

| Eigenschap | Description |
|:--- |:--- |
| `Type` |*Ontvang een waarschuwing* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |De details van het gegevensitem dat de waarschuwing worden gegenereerd in XML-indeling heeft. |
| `AlertDescription` |Gedetailleerde beschrijving van de waarschuwing. |
| `AlertId` |De GUID van de waarschuwing. |
| `AlertName` |Naam van de waarschuwing. |
| `AlertPriority` |Prioriteit van de waarschuwing. |
| `AlertSeverity` |Ernst van de waarschuwing. |
| `AlertState` |Meest recente oplossingsstatus van de waarschuwing. |
| `LastModifiedBy` |De naam van de gebruiker die de waarschuwing het laatst is gewijzigd. |
| `ManagementGroupName` |De naam van de beheergroep waar de waarschuwing is gegenereerd. |
| `RepeatCount` |Aantal keren dat de dezelfde waarschuwing is gegenereerd voor dezelfde bewaakte object sinds worden omgezet. |
| `ResolvedBy` |De naam van de gebruiker die de waarschuwing is opgelost. Leeg zijn als de waarschuwing nog niet opgelost is. |
| `SourceDisplayName` |Weergavenaam van de controle-object dat de waarschuwing heeft gegenereerd. |
| `SourceFullName` |Volledige naam van de controle-object dat de waarschuwing heeft gegenereerd. |
| `TicketId` |Ticket-ID voor de waarschuwing als de System Center Operations Manager-omgeving is geïntegreerd met een proces voor het toewijzen van tickets voor waarschuwingen.  Lege van geen ticket-ID is toegewezen. |
| `TimeGenerated` |Datum en tijd waarop de waarschuwing is gemaakt. |
| `TimeLastModified` |Datum en tijd waarop de waarschuwing voor het laatst is gewijzigd. |
| `TimeRaised` |Datum en tijd waarop de waarschuwing is gegenereerd. |
| `TimeResolved` |Datum en tijd waarop de waarschuwing is opgelost. Leeg zijn als de waarschuwing nog niet opgelost is. |

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken
De volgende tabel bevat voorbeelden van zoekopdrachten voor waarschuwing records die zijn verzameld door deze oplossing: 

| Query’s uitvoeren | Description |
|:---|:---|
| Waarschuwing &#124; waar SourceSystem == "OpsManager" en AlertSeverity == "error" en TimeRaised > ago(24h) |Kritieke waarschuwingen die in de afgelopen 24 uur zijn geactiveerd |
| Waarschuwing &#124; waar AlertSeverity 'waarschuwing' en TimeRaised == > ago(24h) |Waarschuwingsmeldingen die in de afgelopen 24 uur zijn geactiveerd |
| Waarschuwing &#124; waar SourceSystem == "OpsManager" en AlertState! = 'Gesloten' en TimeRaised > ago(24h) &#124; samenvatten Count = count() by SourceDisplayName |Bronnen met actieve waarschuwingen die in de afgelopen 24 uur zijn geactiveerd |
| Waarschuwing &#124; waar SourceSystem == "OpsManager" en AlertSeverity == "error" en TimeRaised > ago(24h) en AlertState! = 'Gesloten' |Kritieke waarschuwingen die in de afgelopen 24 uur die nog steeds actief zijn geactiveerd |
| Waarschuwing &#124; waar SourceSystem == "OpsManager" en TimeRaised > ago(24h) en AlertState == 'Gesloten' |Waarschuwingen die zijn geactiveerd in de afgelopen 24 uur die nu zijn gesloten |
| Waarschuwing &#124; waar SourceSystem == "OpsManager" en TimeRaised > ago(1d) &#124; samenvatten Count = count() by AlertSeverity |Waarschuwingen die in de afgelopen dag zijn geactiveerd, gegroepeerd op de ernst van de waarschuwing |
| Waarschuwing &#124; waar SourceSystem == "OpsManager" en TimeRaised > ago(1d) &#124; sorteren op RepeatCount desc |Waarschuwingen die in de afgelopen dag zijn geactiveerd, gegroepeerd op de waarde voor het aantal herhalingen van de waarschuwing |



## <a name="next-steps"></a>Volgende stappen
* Zie [Understanding alerts in Log Analytics](../../azure-monitor/platform/alerts-overview.md) voor meer informatie over het genereren van waarschuwingen van Log Analytics.
