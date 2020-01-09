---
title: Capaciteit en prestaties oplossing in Azure Monitor | Microsoft Docs
description: Gebruik de Capaciteit en prestaties oplossing in monitor om inzicht te krijgen in de capaciteit van uw Hyper-V-servers.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: c444991166d9c7d3f1d8ed39e19e333d3c27f982
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405748"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Virtuele Hyper-V-machine capaciteit plannen met de Capaciteit en prestaties oplossing (afgeschaft)

![Symbool van capaciteit en prestaties](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> De oplossing capaciteit en prestaties is afgeschaft.  Klanten die de oplossing al hebt geïnstalleerd kunnen blijven gebruiken, maar de capaciteit en prestaties kan niet worden toegevoegd voor nieuwe werkruimten.

U kunt de Capaciteit en prestaties-oplossing in monitor gebruiken om inzicht te krijgen in de capaciteit van uw Hyper-V-servers. De oplossing biedt inzicht in uw Hyper-V-omgeving door u het totale gebruik (CPU, geheugen en schijfruimte) van de hosts en de virtuele machines die worden uitgevoerd op de Hyper-V-hosts weer te geven. Metrische gegevens worden verzameld voor CPU, geheugen en schijven voor alle hosts en de virtuele machines die daarop worden uitgevoerd.

De oplossing:

-   Ziet u hosts met de hoogste en laagste gebruik van CPU en geheugen
-   Virtuele machines bevat met de hoogste en laagste gebruik van CPU en geheugen
-   Virtuele machines bevat met de hoogste en laagste gebruik van IOPS en doorvoer
-   Geeft aan welke VM's worden uitgevoerd op welke hosts
-   Bevat de bovenste schijven met hoge doorvoer, IOP's en latentie in gedeelde clustervolumes
- U kunt aanpassen en filteren op basis van groepen

> [!NOTE]
> De vorige versie van de oplossing capaciteit en prestaties voor capaciteitsbeheer aangeroepen vereist zowel System Center Operations Manager en System Center Virtual Machine Manager. Deze bijgewerkte oplossing beschikt niet over de afhankelijkheden.


## <a name="connected-sources"></a>Verbonden bronnen

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteuning | Beschrijving |
|---|---|---|
| [Windows-agents](../../azure-monitor/platform/agent-windows.md) | Ja | De oplossing verzamelt gegevens van de capaciteit en prestaties van Windows-agents. |
| [Linux-agents](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nee    | De oplossing verzamelt geen gegevens van de capaciteit en prestaties van rechtstreekse Linux-agents.|
| [SCOM-beheergroep](../../azure-monitor/platform/om-agents.md) | Ja |De oplossing verzamelt gegevens van capaciteit en prestaties van agents in een verbonden SCOM-beheergroep. Een directe verbinding van de SCOM-agent naar Log Analytics is niet vereist.|
| [Azure Storage-account](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nee | Azure storage bevat geen gegevens voor capaciteit en prestaties.|

## <a name="prerequisites"></a>Vereisten

- Windows- of Operations Manager-agents moeten worden geïnstalleerd op Windows Server 2012 Hyper-V-hosts of hoger, geen virtuele machines.


## <a name="configuration"></a>Configuratie

Voer de volgende stap om de oplossing capaciteit en prestaties toevoegen aan uw werkruimte.

- De oplossing capaciteit en prestaties toevoegen aan uw Log Analytics-werkruimte met behulp van de procedure beschreven in [toevoegen Log Analytics-oplossingen uit de galerie van oplossingen](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Management packs

Als uw SCOM-beheergroep is verbonden met uw Log Analytics-werkruimte, klikt u vervolgens de volgende management packs geïnstalleerd in SCOM wanneer u deze oplossing toevoegt. Er is geen configuratie of onderhoud van deze management packs vereist.

- Microsoft.IntelligencePacks.CapacityPerformance

De gebeurtenis 1201 lijkt op:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Als u de oplossing capaciteit en prestaties is bijgewerkt, wordt het versienummer wijzigen.

Zie [Operations Manager koppelen aan Log Analytics](../../azure-monitor/platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="using-the-solution"></a>De oplossing gebruiken

Wanneer u de oplossing capaciteit en prestaties voor uw werkruimte toevoegt, wordt de capaciteit en prestaties toegevoegd aan het dashboard overzicht. Deze tegel toont een telling van het aantal actieve Hyper-V-hosts en het aantal actieve virtuele machines die zijn gecontroleerd voor de periode rond geselecteerd.

![Capaciteit en prestaties van tegel](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Gebruik controleren

Klik op de tegel capaciteit en prestaties om de capaciteit en prestaties dashboard te openen. Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat maximaal tien items die overeenkomen de criteria voor het opgegeven bereik en het tijdsbereik van die kolom. U kunt een zoekactie die alle records retourneert, uitvoeren op het logboek door onderaan in de kolom te klikken op **Alles bekijken** of door te klikken op de kolomkoptekst.

- **Hosts**
    - **CPU-gebruik hosten** ziet u een grafische trend weergegeven van het CPU-gebruik van de hostcomputers en een lijst met hosts, op basis van de geselecteerde periode. Beweeg de muisaanwijzer over het lijndiagram om details voor een bepaald punt in tijd weer te geven. Klik op de grafiek om meer details in zoeken in logboeken weer te geven. Klik op een willekeurige hostnaam om te zoeken in Logboeken openen en weergeven van details van de teller CPU voor gehoste virtuele machines.
    - **Geheugengebruik hosten** ziet u een grafische trend weergegeven van het geheugengebruik van hostcomputers en een lijst met hosts, op basis van de geselecteerde periode. Beweeg de muisaanwijzer over het lijndiagram om details voor een bepaald punt in tijd weer te geven. Klik op de grafiek om meer details in zoeken in logboeken weer te geven. Klik op een willekeurige hostnaam om te zoeken in Logboeken openen en weergeven van details van de teller geheugen voor de gehoste virtuele machines.
- **Virtuele machines**
    - **VM-CPU-gebruik** ziet u een grafische trend weergegeven van het CPU-gebruik van virtuele machines en een lijst van virtuele machines, op basis van de geselecteerde periode. Beweeg de muisaanwijzer over het lijndiagram om details te bekijken voor een bepaald punt in tijd voor de top 3 virtuele machines. Klik op de grafiek om meer details in zoeken in logboeken weer te geven. Klik op de naam van een virtuele machine om te zoeken in Logboeken openen en weergeven van details van de teller samengevoegde CPU voor de virtuele machine.
    - **Geheugengebruik van de virtuele machine** ziet u een grafische trend weergegeven van het geheugengebruik van virtuele machines en een lijst van virtuele machines, op basis van de geselecteerde periode. Beweeg de muisaanwijzer over het lijndiagram om details te bekijken voor een bepaald punt in tijd voor de top 3 virtuele machines. Klik op de grafiek om meer details in zoeken in logboeken weer te geven. Klik op de naam van een virtuele machine om te zoeken in Logboeken openen en weergeven van details van de teller samengevoegde geheugen voor de virtuele machine.
    - **Totaal aantal VM-schijf-IOPS** ziet u een grafische trend weergegeven van de totale schijf-IOPS voor virtuele machines en een lijst met virtuele machines met de IOPS voor elke, op basis van de geselecteerde periode. Beweeg de muisaanwijzer over het lijndiagram om details te bekijken voor een bepaald punt in tijd voor de top 3 virtuele machines. Klik op de grafiek om meer details in zoeken in logboeken weer te geven. Klik op de naam van een virtuele machine om te zoeken in Logboeken en weergave samengevoegde schijf-IOPS teller details voor de virtuele machine openen.
    - **Totaal aantal schijfdoorvoer VM** ziet u een grafische trend weergegeven van de totale doorvoer per schijf voor virtuele machines en een lijst met virtuele machines met de totale doorvoer per schijf voor elk, op basis van de geselecteerde periode. Beweeg de muisaanwijzer over het lijndiagram om details te bekijken voor een bepaald punt in tijd voor de top 3 virtuele machines. Klik op de grafiek om meer details in zoeken in logboeken weer te geven. Klik op de naam van een virtuele machine om te zoeken in Logboeken openen en weergeven van details van teller doorvoer cumulatieve totale schijfruimte voor de virtuele machine.
- **Geclusterde gedeelde Volumes**
    - **Totale doorvoer** bevat het totaal van beide leest en schrijft op geclusterde gedeelde volumes.
    - **Totaal aantal IOPS** bevat het totaal van invoer/uitvoer-bewerkingen per seconde op geclusterde gedeelde volumes.
    - **Totale latentie** toont de totale latentie op geclusterde gedeelde volumes.
- **Host dichtheid** de bovenste tegel ziet u het totale aantal hosts en virtuele machines beschikbaar zijn voor de oplossing. Klik op de bovenste tegel om extra details in zoeken in logboeken weer te geven. Bevat ook alle hosts en het aantal virtuele machines die worden gehost. Klik op een host om te zoomen op de resultaten van de virtuele machine in een zoeken in Logboeken.


![dashboard Hosts blade](./media/capacity-performance/dashboard-hosts.png)

![de blade virtuele machines in Dashboard](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Prestaties evalueren

Productie computeromgevingen verschillen aanzienlijk van een organisatie naar een andere. Ook, capaciteit en prestaties werkbelastingen kunnen afhangen van hoe uw virtuele machines worden uitgevoerd, en wat u rekening houden met normale. Specifieke procedures om u te helpen de prestaties van de meting waarschijnlijk niet van toepassing op uw omgeving. Dus meer gegeneraliseerd prescriptieve richtlijnen is beter geschikt voor help. Microsoft publiceert een verscheidenheid aan artikelen prescriptieve richtlijnen om te meten van prestaties.

Om samen te vatten, verzamelt de oplossing capaciteit en prestaties van gegevens uit diverse bronnen, met inbegrip van prestatiemeteritems. Gebruik van capaciteit en prestaties van gegevens die die zijn gepresenteerd in verschillende oppervlakken in de oplossing en vergelijk de resultaten die op de [meten van prestaties op Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) artikel. Hoewel het artikel kan enige tijd geleden is gepubliceerd, worden de metrische gegevens, overwegingen en richtlijnen nog geldig. Dit artikel bevat koppelingen naar andere nuttige informatiebronnen.


## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

De volgende tabel bevat voorbeelden van zoekopdrachten voor capaciteit en prestaties van gegevens die worden verzameld en wordt berekend door deze oplossing.


| Query | Beschrijving |
|:--- |:--- |
| Alle hostgeheugenconfiguraties | Perf &#124; waarbij ObjectName == 'Capaciteit en prestaties' en CounterName == "Host toegewezen geheugen (MB)" &#124; samenvatten MB avg(CounterValue) door InstanceName = |
| Alle VM-geheugenconfiguraties | Perf &#124; waarbij ObjectName == 'Capaciteit en prestaties' en CounterName == "Virtuele machine toegewezen geheugen (MB)" &#124; samenvatten MB avg(CounterValue) door InstanceName = |
| Uitsplitsing van de totale schijf-IOPS voor alle virtuele machines | Perf &#124; waarbij ObjectName == 'Capaciteit en prestaties' en (CounterName == "VHD leesbewerkingen/s" of CounterName == "VHD-schrijfbewerkingen/s") &#124; summarize AggregatedValue = avg(CounterValue) door bin (TimeGenerated, 1 uur), CounterName, InstanceName |
| Uitsplitsing van de totale doorvoer voor schijf over alle VM 's | Perf &#124; waarbij ObjectName == 'Capaciteit en prestaties' en (CounterName == "VHD lezen MB/s" of CounterName == "VHD schrijven MB/s") &#124; summarize AggregatedValue = avg(CounterValue) door bin (TimeGenerated, 1 uur), CounterName, InstanceName |
| Uitsplitsing van de totale IOPS over alle CSV 's | Perf &#124; waarbij ObjectName == 'Capaciteit en prestaties' en (CounterName == "CSV leesbewerkingen/s" of CounterName == "CSV-schrijfbewerkingen/s") &#124; summarize AggregatedValue = avg(CounterValue) door bin (TimeGenerated, 1 uur), CounterName, InstanceName |
| Uitsplitsing van de totale doorvoer over alle CSV 's | Perf &#124; waarbij ObjectName == 'Capaciteit en prestaties' en (CounterName == "CSV leesbewerkingen/s" of CounterName == "CSV-schrijfbewerkingen/s") &#124; summarize AggregatedValue = avg(CounterValue) door bin (TimeGenerated, 1 uur), CounterName, InstanceName |
| Uitsplitsing van de totale latentie over alle CSV 's | Perf &#124; waarbij ObjectName == 'Capaciteit en prestaties' en (CounterName == 'CSV lezen latentie' of CounterName == 'CSV schrijven latentie') &#124; summarize AggregatedValue = avg(CounterValue) door bin (TimeGenerated, 1 uur), CounterName, InstanceName |


## <a name="next-steps"></a>Volgende stappen
* Gebruik [zoekopdrachten in Logboeken in Log Analytics](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde gegevens voor capaciteit en prestaties te bekijken.
