---
title: Oplossing voor capaciteit en prestaties in Azure-monitor | Microsoft Documenten
description: Gebruik de oplossing capaciteit en prestaties in Monitor om u inzicht te geven in de capaciteit van uw Hyper-V-servers.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666940"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Plan hyper-v virtuele machinecapaciteit met de oplossing capaciteit en prestaties (afgeschaft)

![Symbool Capaciteit en prestaties](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> De oplossing capaciteit en prestaties is afgeschaft.  Klanten die de oplossing al hebben geïnstalleerd, kunnen deze blijven gebruiken, maar capaciteit en prestaties kunnen niet worden toegevoegd aan nieuwe werkruimten.

U de oplossing capaciteit en prestaties in Monitor gebruiken om u inzicht te geven in de capaciteit van uw Hyper-V-servers. De oplossing biedt inzicht in uw Hyper-V-omgeving door u het totale gebruik (CPU, geheugen en schijf) van de hosts en de VM's te laten zien die op die Hyper-V-hosts worden uitgevoerd. Statistieken worden verzameld voor CPU, geheugen en schijven voor al uw hosts en de VM's die erop worden uitgevoerd.

De oplossing:

-   Toont hosts met het hoogste en laagste CPU- en geheugengebruik
-   Toont VM's met het hoogste en laagste CPU- en geheugengebruik
-   Toont VM's met de hoogste en laagste IOPS en doorvoergebruik
-   Toont welke VM's worden uitgevoerd op welke hosts
-   Toont de bovenste schijven met een hoge doorvoer, IOPS en latentie in gedeelde clustervolumes
- Hiermee u aanpassen en filteren op basis van groepen

> [!NOTE]
> De vorige versie van de oplossing capaciteit en prestaties genaamd Capaciteitsbeheer vereiste zowel System Center Operations Manager als Virtual Machine Manager van het Systeemcentrum. Deze bijgewerkte oplossing heeft deze afhankelijkheden niet.


## <a name="connected-sources"></a>Verbonden bronnen

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteuning | Beschrijving |
|---|---|---|
| [Windows-agents](../../azure-monitor/platform/agent-windows.md) | Ja | De oplossing verzamelt informatie over capaciteit en prestatiegegevens van Windows-agents. |
| [Linux-agents](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nee    | De oplossing verzamelt geen informatie over capaciteit en prestatiegegevens van directe Linux-agents.|
| [SCOM-managementgroep](../../azure-monitor/platform/om-agents.md) | Ja |De oplossing verzamelt capaciteits- en prestatiegegevens van agents in een verbonden SCOM-beheergroep. Een directe verbinding van de SCOM-agent met Log Analytics is niet vereist.|
| [Azure-opslagaccount](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nee | Azure-opslag bevat geen capaciteits- en prestatiegegevens.|

## <a name="prerequisites"></a>Vereisten

- Windows- of Operations Manager-agents moeten zijn geïnstalleerd op Windows Server 2012 of hoger Hyper-V-hosts, geen virtuele machines.


## <a name="configuration"></a>Configuratie

Voer de volgende stap uit om de oplossing Capaciteit en prestaties toe te voegen aan uw werkruimte.

- Voeg de oplossing voor capaciteit en prestaties toe aan uw Log Analytics-werkruimte met behulp van het proces dat wordt beschreven in [Log Analytics-oplossingen toevoegen vanuit de Galerie Oplossingen.](../../azure-monitor/insights/solutions.md)

## <a name="management-packs"></a>Management packs

Als uw SCOM-beheergroep is verbonden met uw Log Analytics-werkruimte, worden de volgende beheerpakketten in SCOM geïnstalleerd wanneer u deze oplossing toevoegt. Er is geen configuratie of onderhoud van deze management packs vereist.

- Microsoft.IntelligencePacks.CapacityPerformance

De gebeurtenis 1201 lijkt op:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Wanneer de oplossing Capaciteit en prestaties wordt bijgewerkt, wordt het versienummer gewijzigd.

Zie [Operations Manager koppelen aan Log Analytics](../../azure-monitor/platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="using-the-solution"></a>De oplossing gebruiken

Wanneer u de oplossing Capaciteit en prestaties toevoegt aan uw werkruimte, wordt de capaciteit en prestaties toegevoegd aan het dashboard Overzicht. Met deze tegel wordt een telling weergegeven van het aantal actieve Hyper-V-hosts en het aantal actieve virtuele machines dat is gecontroleerd voor de geselecteerde periode.

![Tegel Capaciteit en prestaties](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Gebruik bekijken

Klik op de tegel Capaciteit en prestaties om het dashboard Capaciteit en prestaties te openen. Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat maximaal tien items die overeenkomen de criteria voor het opgegeven bereik en het tijdsbereik van die kolom. U kunt een zoekactie die alle records retourneert, uitvoeren op het logboek door onderaan in de kolom te klikken op **Alles bekijken** of door te klikken op de kolomkoptekst.

- **Hosts**
    - **Cpu-gebruik host** Toont een grafische trend van het CPU-gebruik van hostcomputers en een lijst met hosts, op basis van de geselecteerde periode. Plaats de plaats in het lijndiagram om details voor een specifiek tijdstip weer te geven. Klik op de grafiek om meer details in logboekzoekopdrachten weer te geven. Klik op een hostnaam om de gegevens van de LOG-zoekopdracht te openen en cpu-tellergegevens voor gehoste VM's weer te geven.
    - **Gebruik van hostgeheugen** Toont een grafische trend van het geheugengebruik van hostcomputers en een lijst met hosts, op basis van de geselecteerde periode. Plaats de plaats in het lijndiagram om details voor een specifiek tijdstip weer te geven. Klik op de grafiek om meer details in logboekzoekopdrachten weer te geven. Klik op een hostnaam om de gegevens van de logboekzoekopdracht en de geheugenteller gegevens voor gehoste VM's te openen.
- **Virtuele machines**
    - **VM CPU-gebruik** Toont een grafische trend van het CPU-gebruik van virtuele machines en een lijst van virtuele machines, op basis van de geselecteerde periode. Plaats de muisaanwijzer op het lijndiagram om details weer te geven voor een specifiek tijdstip voor de top 3 VM's. Klik op de grafiek om meer details in logboekzoekopdrachten weer te geven. Klik op een VM-naam om logboekzoekopdrachten te openen en geaggregeerde CPU-tellergegevens voor de VM weer te geven.
    - **VM-geheugengebruik** Toont een grafische trend van het geheugengebruik van virtuele machines en een lijst met virtuele machines, gebaseerd op de geselecteerde periode. Plaats de muisaanwijzer op het lijndiagram om details weer te geven voor een specifiek tijdstip voor de top 3 VM's. Klik op de grafiek om meer details in logboekzoekopdrachten weer te geven. Klik op een VM-naam om logboekzoekopdrachten te openen en geaggregeerde geheugentellergegevens voor de VM weer te geven.
    - **VM Totaal Schijf IOPS** Toont een grafische trend van het totale schijf-IOPS voor virtuele machines en een lijst met virtuele machines met de IOPS voor elk, op basis van de geselecteerde periode. Plaats de muisaanwijzer op het lijndiagram om details weer te geven voor een specifiek tijdstip voor de top 3 VM's. Klik op de grafiek om meer details in logboekzoekopdrachten weer te geven. Klik op een VM-naam om logboekzoekopdrachten te openen en geaggregeerde gegevens over het IOPS-station voor de VM weer te geven.
    - **VM Totale schijfdoorvoer** Toont een grafische trend van de totale schijfdoorvoer voor virtuele machines en een lijst met virtuele machines met de totale schijfdoorvoer voor elk, op basis van de geselecteerde periode. Plaats de muisaanwijzer op het lijndiagram om details weer te geven voor een specifiek tijdstip voor de top 3 VM's. Klik op de grafiek om meer details in logboekzoekopdrachten weer te geven. Klik op een VM-naam om logboekzoekopdrachten te openen en geaggregeerde totale gegevens van de total disk throughput teller voor de VM weer te geven.
- **Geclusterde gedeelde volumes**
    - **Totale doorvoer** Toont de som van zowel leest als schrijft op geclusterde gedeelde volumes.
    - **Totaal Aantal IOPS** Toont de som van input/outputbewerkingen per seconde op geclusterde gedeelde volumes.
    - **Totale latentie** Toont de totale latentie op geclusterde gedeelde volumes.
- **Hostdichtheid** De bovenste tegel toont het totale aantal hosts en virtuele machines dat beschikbaar is voor de oplossing. Klik op de bovenste tegel om aanvullende details in logboekzoekopdrachten weer te geven. Bevat ook alle hosts en het aantal virtuele machines die worden gehost. Klik op een host om in te zoomen op de VM-resultaten in een logboekzoekopdracht.


![dashboard Hosts-blad](./media/capacity-performance/dashboard-hosts.png)

![dashboard virtuele machines blad](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Prestaties evalueren

Productiecomputeromgevingen verschillen sterk van organisatie tot organisatie. Capaciteit en prestatieworkloads kunnen ook afhangen van de werking van uw VM's en wat u normaal vindt. Specifieke procedures om u te helpen de prestaties te meten, zijn waarschijnlijk niet van toepassing op uw omgeving. Dus, meer algemene prescriptieve begeleiding is beter geschikt om te helpen. Microsoft publiceert verschillende voorschrijvende richtlijnen om u te helpen de prestaties te meten.

Samenvattend verzamelt de oplossing capaciteits- en prestatiegegevens uit verschillende bronnen, waaronder prestatiemeteritems. Gebruik die capaciteits- en prestatiegegevens die op verschillende oppervlakken in de oplossing worden gepresenteerd en vergelijk uw resultaten met die van het artikel [Meten van prestaties op Hyper-V.](https://msdn.microsoft.com/library/cc768535.aspx) Hoewel het artikel enige tijd geleden is gepubliceerd, zijn de statistieken, overwegingen en richtlijnen nog steeds geldig. Het artikel bevat links naar andere nuttige bronnen.


## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

In de volgende tabel vindt u voorbeeldlogboekzoekopdrachten voor capaciteits- en prestatiegegevens die door deze oplossing zijn verzameld en berekend.


| Query’s uitvoeren | Beschrijving |
|:--- |:--- |
| Alle configuraties van het hostgeheugen | Perf &#124; waar ObjectName == "Capaciteit en prestaties" en CounterName == "Host Assigned Memory MB" &#124; mb = avg(CounterValue) per InstanceName samen te vatten |
| Alle VM-geheugenconfiguraties | Perf &#124; waar ObjectName == "Capaciteit en prestaties" en CounterName == "VM Assigned Memory MB" &#124; mb = avg(CounterValue) per InstanceName samen te vatten |
| Uitsplitsing van total disk IOPS over alle VM's | Perf &#124; waar ObjectName == "Capaciteit en Prestaties" en (CounterName == "VHD Reads/s" of CounterName == "VHD Writes/s") &#124; aggregatedvalue = avg(CounterValue) per bak samen te vatten (TimeGenerated, 1h), CounterName, InstanceName |
| Uitsplitsing van de totale schijfdoorvoer in alle VM's | Perf &#124; waar ObjectName == "Capaciteit en prestaties" en (CounterName == "VHD Read MB/s" of CounterName == "VHD Write MB/s") &#124; samenvatten Van AggregatedValue = avg(CounterValue) per bak (TimeGenerated, 1h), CounterName, InstanceName |
| Uitsplitsing van de totale IOPS over alle CZV's | Perf &#124; waar ObjectName == "Capaciteit en prestaties" en (CounterName == "CSV Reads/s" of CounterName == "CSV Writes/s") &#124; samenvatten Van AggregatedValue = avg(CounterValue) per bin (TimeGenerated, 1h), CounterName, InstanceName |
| Uitsplitsing van de totale doorvoer over alle CPV's | Perf &#124; waar ObjectName == "Capaciteit en prestaties" en (CounterName == "CSV Reads/s" of CounterName == "CSV Writes/s") &#124; samenvatten Van AggregatedValue = avg(CounterValue) per bin (TimeGenerated, 1h), CounterName, InstanceName |
| Uitsplitsing van de totale latentie over alle CPV's | Perf &#124; waar ObjectName == "Capaciteit en prestaties" en (CounterName == "CSV Leeslatentie" of CounterName == "CSV Schrijflatentie") &#124; samenvatten AggregatedValue = avg(CounterValue) by bin (TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Volgende stappen
* Gebruik [Logboekzoekopdrachten in Log Analytics](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde capaciteits- en prestatiegegevens weer te geven.
