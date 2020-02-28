---
title: Waarschuwingenbeheeroplossing in Azure Log Analytics | Microsoft Docs
description: De oplossing voor beheer van waarschuwingen in Log Analytics helpt u bij het analyseren van alle van de waarschuwingen in uw omgeving.  Naast consolideren waarschuwingen gegenereerd in Log Analytics, importeert deze waarschuwingen van verbonden beheergroepen voor System Center Operations Manager in Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668380"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Waarschuwingenbeheeroplossing in Azure Log Analytics

![Pictogram voor het beheer van waarschuwing](media/alert-management-solution/icon.png)

> [!NOTE]
>  Azure Monitor ondersteunt nu verbeterde mogelijkheden voor [het beheren van uw waarschuwingen op schaal](https://aka.ms/azure-alerts-overview), met inbegrip van de functies die worden gegenereerd door [controle hulpprogramma's als System Center Operations Manager, zabbix of nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


De Waarschuwingsbeheer oplossing helpt u bij het analyseren van alle van de waarschuwingen in uw Log Analytics-opslagplaats.  Deze waarschuwingen zijn mogelijk afkomstig uit verschillende bronnen, waaronder de bronnen die zijn [gemaakt door log Analytics](../../azure-monitor/platform/alerts-overview.md) of [geïmporteerd uit nagios of zabbix](../../azure-monitor/learn/quick-collect-linux-computer.md). De oplossing importeert ook waarschuwingen van alle [verbonden System Center Operations Manager-beheer groepen](../../azure-monitor/platform/om-agents.md).

## <a name="prerequisites"></a>Vereisten
De oplossing werkt met een wille keurige record in de Log Analytics-opslag plaats met een type **waarschuwing**, dus moet u de configuratie uitvoeren die nodig is voor het verzamelen van deze records.

- Maak voor Log Analytics waarschuwingen [waarschuwings regels](../../azure-monitor/platform/alerts-overview.md) om waarschuwings records rechtstreeks in de opslag plaats te maken.
- Configureer voor nagios-en zabbix-waarschuwingen [die servers](../../azure-monitor/learn/quick-collect-linux-computer.md) om waarschuwingen naar log Analytics te verzenden.
- Voor System Center Operations Manager waarschuwingen [verbindt u uw Operations Manager-beheer groep met uw log Analytics-werk ruimte](../../azure-monitor/platform/om-agents.md).  Alle waarschuwingen die zijn gemaakt in System Center Operations Manager worden geïmporteerd in Log Analytics.  

## <a name="configuration"></a>Configuratie
Voeg de Waarschuwingenbeheer oplossing toe aan uw Log Analytics-werk ruimte met behulp van het proces dat wordt beschreven in [oplossingen toevoegen](../../azure-monitor/insights/solutions.md). Er is geen verdere configuratie nodig.

## <a name="management-packs"></a>Management packs
Als uw System Center Operations Manager-beheergroep is verbonden met uw Log Analytics-werkruimte, klikt u vervolgens de volgende management packs geïnstalleerd in System Center Operations Manager wanneer u deze oplossing toevoegt.  Er is geen configuratie of onderhoud van de management packs vereist.

* Microsoft System Center Advisor Waarschuwingsbeheer (Microsoft.IntelligencePacks.AlertManagement)

Zie [Operations Manager koppelen aan Log Analytics](../../azure-monitor/platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="data-collection"></a>Gegevensverzameling
### <a name="agents"></a>Agents
De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteuning | Beschrijving |
|:--- |:--- |:--- |
| [Windows-agents](agent-windows.md) | Nee |Direct Windows-agents geen waarschuwingen worden gegenereerd.  Log Analytics-waarschuwingen kunnen worden gemaakt op basis van gebeurtenissen en prestatiegegevens verzameld van Windows-agents. |
| [Linux-agents](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nee |Directe Linux-agents geen waarschuwingen worden gegenereerd.  Log Analytics-waarschuwingen kunnen worden gemaakt van gebeurtenissen en prestatiegegevens verzameld van Linux-agents.  Nagios en Zabbix-waarschuwingen worden verzameld van deze servers waarvoor de Linux-agent. |
| [System Center Operations Manager beheer groep](../../azure-monitor/platform/om-agents.md) |Ja |Waarschuwingen die worden gegenereerd in Operations Manager-agents zijn die worden geleverd aan de beheergroep en vervolgens doorgestuurd naar Log Analytics.<br><br>Een directe verbinding van Operations Manager-agents naar Log Analytics is niet vereist. Waarschuwingsgegevens wordt uit de beheergroep doorgestuurd naar de opslagplaats van Log Analytics. |


### <a name="collection-frequency"></a>Verzamelingsfrequentie
- Waarschuwing records zijn beschikbaar voor de oplossing zodra ze zijn opgeslagen in de opslagplaats.
- Waarschuwingsgegevens wordt verzonden vanaf de Operations Manager-beheergroep naar Log Analytics om de drie minuten.  

## <a name="using-the-solution"></a>De oplossing gebruiken
Wanneer u de Waarschuwingenbeheer oplossing toevoegt aan uw Log Analytics-werk ruimte, wordt de tegel **Waarschuwingenbeheer** toegevoegd aan uw dash board.  Deze tegel toont een telling en grafische weergave van het aantal actieve waarschuwingen die zijn gegenereerd in de afgelopen 24 uur.  U kunt dit tijdsbereik niet wijzigen.

![Waarschuwing tegel-beheer](media/alert-management-solution/tile.png)

Klik op de tegel **Waarschuwingenbeheer** om het **Waarschuwingenbeheer** dash board te openen.  Het dashboard bevat de kolommen in de volgende tabel.  Elke kolom bevat de bovenste 10 waarschuwingen per aantal die overeenkomen met criteria voor het opgegeven bereik en het tijdsbereik van die kolom.  U kunt een zoek opdracht in het logboek uitvoeren met de volledige lijst door te klikken op **alles weer geven** onder aan de kolom of door op de kolomkop te klikken.

| Kolom | Beschrijving |
|:--- |:--- |
| Kritieke waarschuwingen |Alle waarschuwingen met een ernst gegroepeerd op de naam van de waarschuwing kritiek.  Klik op de naam van een waarschuwing om uit te voeren van een logboekzoekopdracht die alle records voor deze waarschuwing. |
| Waarschuwingsmeldingen |Alle waarschuwingen met een ernst van waarschuwing gegroepeerd op de naam van de waarschuwing.  Klik op de naam van een waarschuwing om uit te voeren van een logboekzoekopdracht die alle records voor deze waarschuwing. |
| Waarschuwingen voor actieve System Center Operations Manager |Alle waarschuwingen die zijn verzameld van Operations Manager, met een andere status dan *gesloten* , gegroepeerd op bron waardoor de waarschuwing is gegenereerd. |
| Alle actieve waarschuwingen |Alle waarschuwingen met een gegroepeerd op de naam van waarschuwing ernst. Bevat alleen Operations Manager waarschuwingen met een andere status dan *gesloten*. |

Als u naar rechts schuift, worden in het dash board verschillende algemene query's vermeld waarop u kunt klikken om een [Zoek actie](../../azure-monitor/log-query/log-query-overview.md) in het logboek voor waarschuwings gegevens uit te voeren.

![Beheer van een waarschuwingendashboard](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-records
De Waarschuwingenbeheer oplossing analyseert alle records met een type **waarschuwing**.  Waarschuwingen die zijn gemaakt door Log Analytics of die worden verzameld van Nagios of Zabbix worden niet rechtstreeks verzameld door de oplossing.

De oplossing importeert waarschuwingen van System Center Operations Manager en maakt een corresponderende record voor elk met een type **waarschuwing** en een hebben van **OpsManager**.  Deze records hebben de eigenschappen in de volgende tabel:  

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |*Waarschuwing* |
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

| Query's uitvoeren | Beschrijving |
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
