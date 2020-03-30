---
title: Oplossing voor waarschuwingsbeheer in Azure Log Analytics | Microsoft Documenten
description: De Alert Management-oplossing in Log Analytics helpt u bij het analyseren van alle waarschuwingen in uw omgeving.  Naast het consolideren van waarschuwingen die zijn gegenereerd binnen Log Analytics, importeert het ook waarschuwingen van verbonden Beheergroepen van System Center Operations Manager in Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668380"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Oplossing voor waarschuwingsbeheer in Azure Log Analytics

![Pictogram Waarschuwingsbeheer](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Monitor ondersteunt nu verbeterde mogelijkheden voor [het beheren van uw waarschuwingen op schaal,](https://aka.ms/azure-alerts-overview)inclusief die gegenereerd door [monitoringtools zoals System Center Operations Manager, Zabbix of Nagios.](https://aka.ms/managing-alerts-other-monitoring-services)
>  


Met de alertmanagementoplossing u alle waarschuwingen in uw Log Analytics-opslagplaats analyseren.  Deze waarschuwingen kunnen afkomstig zijn uit verschillende bronnen, waaronder die bronnen [die zijn gemaakt door Log Analytics](../../azure-monitor/platform/alerts-overview.md) of geïmporteerd uit [Nagios of Zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). De oplossing importeert ook waarschuwingen van [verbonden beheergroepen van System Center Operations Manager.](../../azure-monitor/platform/om-agents.md)

## <a name="prerequisites"></a>Vereisten
De oplossing werkt met alle records in de Log Analytics-opslagplaats met een type **waarschuwing,** dus u moet alle configuratie uitvoeren die nodig is om deze records te verzamelen.

- Maak voor Logboekanalysewaarschuwingen [waarschuwingsregels](../../azure-monitor/platform/alerts-overview.md) om waarschuwingsrecords rechtstreeks in de opslagplaats te maken.
- Configureer [deze servers](../../azure-monitor/learn/quick-collect-linux-computer.md) voor Nagios- en Zabbix-waarschuwingen om waarschuwingen naar Log Analytics te verzenden.
- Voor waarschuwingen voor Operations Manager van Systeemcentrum [koppelt u uw beheergroep Operations Manager aan uw log analytics-werkruimte](../../azure-monitor/platform/om-agents.md).  Alle waarschuwingen die zijn gemaakt in System Center Operations Manager worden geïmporteerd in Log Analytics.  

## <a name="configuration"></a>Configuratie
Voeg de oplossing Voor waarschuwingsbeheer toe aan uw log analytics-werkruimte met behulp van het proces dat wordt beschreven in [Oplossingen toevoegen.](../../azure-monitor/insights/solutions.md) Er is geen verdere configuratie nodig.

## <a name="management-packs"></a>Management packs
Als uw beheergroep System Center Operations Manager is verbonden met uw Log Analytics-werkruimte, worden de volgende beheerpakketten geïnstalleerd in System Center Operations Manager wanneer u deze oplossing toevoegt.  Er is geen configuratie of onderhoud van de beheerpakketten vereist.

* Alert Management van Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Zie [Operations Manager koppelen aan Log Analytics](../../azure-monitor/platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="data-collection"></a>Gegevensverzameling
### <a name="agents"></a>Agents
De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteuning | Beschrijving |
|:--- |:--- |:--- |
| [Windows-agents](agent-windows.md) | Nee |Directe Windows-agents genereren geen waarschuwingen.  Log Analytics-waarschuwingen kunnen worden gemaakt op basis van gebeurtenissen en prestatiegegevens die zijn verzameld van Windows-agents. |
| [Linux-agents](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nee |Directe Linux-agents genereren geen waarschuwingen.  Log Analytics-waarschuwingen kunnen worden gemaakt op basis van gebeurtenissen en prestatiegegevens die zijn verzameld van Linux-agents.  Nagios en Zabbix-waarschuwingen worden verzameld van die servers waarvoor de Linux-agent nodig is. |
| [Beheergroep System Center Operations Manager](../../azure-monitor/platform/om-agents.md) |Ja |Waarschuwingen die worden gegenereerd op Operations Manager-agents worden geleverd aan de beheergroep en vervolgens doorgestuurd naar Log Analytics.<br><br>Een directe verbinding van Operations Manager-agents met Log Analytics is niet vereist. Waarschuwingsgegevens worden doorgestuurd van de beheergroep naar de Log Analytics-repository. |


### <a name="collection-frequency"></a>Verzamelingsfrequentie
- Waarschuwingsrecords zijn beschikbaar voor de oplossing zodra ze worden opgeslagen in de repository.
- Waarschuwingsgegevens worden elke drie minuten verzonden vanuit de beheergroep Operations Manager naar Log Analytics.  

## <a name="using-the-solution"></a>De oplossing gebruiken
Wanneer u de oplossing Voor waarschuwingsbeheer toevoegt aan uw log analytics-werkruimte, wordt de tegel **Waarschuwingsbeheer** toegevoegd aan uw dashboard.  Deze tegel geeft een telling en grafische weergave weer van het aantal actieve waarschuwingen dat in de afgelopen 24 uur is gegenereerd.  U dit tijdsbereik niet wijzigen.

![Tegel Waarschuwingsbeheer](media/alert-management-solution/tile.png)

Klik op de tegel **Waarschuwingsbeheer** om het dashboard **Waarschuwingsbeheer** te openen.  Het dashboard bevat de kolommen in de volgende tabel.  Elke kolom bevat de top 10 waarschuwingen op telling overeenkomen met de criteria van die kolom voor het opgegeven bereik en tijdbereik.  U een logboekzoekopdracht uitvoeren die de hele lijst biedt door onder aan de kolom te klikken op **Alles weergeven** of door op de kolomkop te klikken.

| Kolom | Beschrijving |
|:--- |:--- |
| Kritieke waarschuwingen |Alle waarschuwingen met een ernst van kritieke gegroepeerd op waarschuwingsnaam.  Klik op een waarschuwingsnaam om een logboekzoekopdracht uit te voeren waarin alle records voor die waarschuwing worden teruggegeven. |
| Waarschuwingwaarschuwingen |Alle waarschuwingen met een ernst van waarschuwing gegroepeerd op waarschuwingsnaam.  Klik op een waarschuwingsnaam om een logboekzoekopdracht uit te voeren waarin alle records voor die waarschuwing worden teruggegeven. |
| Waarschuwingen voor Operations Manager active System Center |Alle waarschuwingen die zijn verzameld van Operations Manager met een andere status dan *Gesloten,* gegroepeerd op bron die de waarschuwing heeft gegenereerd. |
| Alle actieve waarschuwingen |Alle waarschuwingen met elke ernst gegroepeerd op waarschuwingsnaam. Bevat alleen Waarschuwingen voor Operations Manager met een andere status dan *Gesloten*. |

Als u naar rechts schuift, worden in het dashboard een aantal veelvoorkomende query's weergegeven waarop u klikken om een [logboekzoekopdracht naar](../../azure-monitor/log-query/log-query-overview.md) waarschuwingsgegevens uit te voeren.

![Dashboard Waarschuwingsbeheer](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-records
De oplossing Waarschuwingsbeheer analyseert elke record met een type **waarschuwing.**  Waarschuwingen die zijn gemaakt door Log Analytics of verzameld zijn bij Nagios of Zabbix, worden niet direct door de oplossing verzameld.

De oplossing importeert waarschuwingen van System Center Operations Manager en maakt een bijbehorende record voor elk met een type **waarschuwing** en een SourceSystem van **OpsManager.**  Deze records hebben de eigenschappen in de volgende tabel:  

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |*Waarschuwing* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Details van het gegevensitem waardoor de waarschuwing in XML-indeling is gegenereerd. |
| `AlertDescription` |Gedetailleerde beschrijving van de waarschuwing. |
| `AlertId` |GUID van de waarschuwing. |
| `AlertName` |Naam van de waarschuwing. |
| `AlertPriority` |Prioriteitsniveau van de waarschuwing. |
| `AlertSeverity` |Ernstniveau van de waarschuwing. |
| `AlertState` |Laatste resolutie staat van de waarschuwing. |
| `LastModifiedBy` |Naam van de gebruiker die de waarschuwing voor het laatst heeft gewijzigd. |
| `ManagementGroupName` |Naam van de beheergroep waar de waarschuwing is gegenereerd. |
| `RepeatCount` |Het aantal keren dat dezelfde waarschuwing is gegenereerd voor hetzelfde bewaakte object sinds het is opgelost. |
| `ResolvedBy` |Naam van de gebruiker die de waarschuwing heeft opgelost. Leeg als de waarschuwing nog niet is opgelost. |
| `SourceDisplayName` |Geef de naam weer van het bewakingsobject dat de waarschuwing heeft gegenereerd. |
| `SourceFullName` |Volledige naam van het bewakingsobject dat de waarschuwing heeft gegenereerd. |
| `TicketId` |Ticket-ID voor de waarschuwing als de System Center Operations Manager-omgeving is geïntegreerd met een proces voor het toewijzen van tickets voor waarschuwingen.  Leeg van geen ticket ID is toegewezen. |
| `TimeGenerated` |Datum en tijd waarop de waarschuwing is gemaakt. |
| `TimeLastModified` |Datum en tijd waarop de waarschuwing voor het laatst is gewijzigd. |
| `TimeRaised` |Datum en tijd waarop de waarschuwing is gegenereerd. |
| `TimeResolved` |Datum en tijd waarop de waarschuwing is opgelost. Leeg als de waarschuwing nog niet is opgelost. |

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken
In de volgende tabel vindt u voorbeeldlogboekzoekopdrachten voor waarschuwingsrecords die door deze oplossing zijn verzameld: 

| Query’s uitvoeren | Beschrijving |
|:---|:---|
| Waarschuwing &#124; waar SourceSystem == "OpsManager" en AlertSeverity == "error" en TimeRaised > geleden(24u) |Kritische waarschuwingen in de afgelopen 24 uur |
| Alert &#124; waar AlertSeverity == "waarschuwing" en timeRaised > geleden(24u) |Waarschuwingswaarschuwingen in de afgelopen 24 uur |
| Alert &#124; waar SourceSystem == "OpsManager" en AlertState != "Closed" en TimeRaised > geleden (24u) &#124; tellen samen te vatten = aantal() door SourceDisplayName |Bronnen met actieve waarschuwingen die in de afgelopen 24 uur zijn opgehaald |
| Alert &#124; waar SourceSystem == "OpsManager" en AlertSeverity == "error" en TimeRaised > geleden (24u) en AlertState != "Closed" |Kritieke waarschuwingen die in de afgelopen 24 uur zijn opgehaald en die nog steeds actief zijn |
| Waarschuw &#124; waar SourceSystem == "OpsManager" en TimeRaised > geleden(24u) en AlertState == "Closed" |Waarschuwingen die in de afgelopen 24 uur zijn opgehaald en die nu zijn gesloten |
| Waarschuwing &#124; waar SourceSystem == "OpsManager" en TimeRaised > geleden(1d) &#124; tellen samen te vatten = aantal() door AlertSeverity |Waarschuwingen die tijdens de afgelopen 1 dag zijn opgehaald, gegroepeerd op hun ernst |
| Waarschuw &#124; waar SourceSystem == "OpsManager" en TimeRaised > geleden(1d) &#124; sorteren op RepeatCount desc |Waarschuwingen die tijdens de afgelopen 1 dag zijn opgehaald, gesorteerd op de waarde van het aantal herhalingen |



## <a name="next-steps"></a>Volgende stappen
* Zie [Understanding alerts in Log Analytics](../../azure-monitor/platform/alerts-overview.md) voor meer informatie over het genereren van waarschuwingen van Log Analytics.
