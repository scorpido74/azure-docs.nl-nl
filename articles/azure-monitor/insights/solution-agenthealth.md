---
title: Agentstatusoplossing in Azure-monitor | Microsoft Documenten
description: Dit artikel is bedoeld om u te helpen begrijpen hoe u deze oplossing gebruiken om de status van uw agents die rechtstreeks rapporteren aan Log Analytics of System Center Operations Manager te controleren.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 7093e20473b799a3f05ddf30803721636732241e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663252"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Agentstatusoplossing in Azure-monitor
De Agent Health-oplossing in Azure helpt u inzicht te krijgen in alle agents die rechtstreeks rapporteren aan de Log Analytics-werkruimte in Azure Monitor of een beheergroep van System Center Operations Manager die is verbonden met Azure Monitor, die niet reageert en niet reageert en het indienen van operationele gegevens.  U kunt ook bijhouden hoeveel agents er zijn geïmplementeerd en waar deze zich geografisch gezien bevinden. Bovendien kunt u query's uitvoeren om op de hoogte te blijven van de verdeling van agents over Azure, andere cloudomgevingen of on-premises.    

## <a name="prerequisites"></a>Vereisten
Voordat u deze oplossing implementeert, bevestigt u dat u [windows-agents](../../log-analytics/log-analytics-windows-agent.md) momenteel hebt ondersteund die rapporteren aan de werkruimte Log Analytics of rapporteert aan een [beheergroep Operations Manager](../../azure-monitor/platform/om-agents.md) die is geïntegreerd met uw werkruimte.

## <a name="solution-components"></a>Oplossingsonderdelen
Deze oplossing bestaat uit de volgende resources die worden toegevoegd aan uw werkruimte en rechtstreeks verbonden agents of verbonden Operations Manager-beheergroepen.

### <a name="management-packs"></a>Management packs
Als uw beheergroep System Center Operations Manager is verbonden met een Log Analytics-werkruimte, worden de volgende beheerpakketten geïnstalleerd in Operations Manager.  Deze management packs worden na het toevoegen van deze oplossing ook op rechtstreeks verbonden Windows-computers geïnstalleerd. Met deze management packs hoeft u niets te configureren of te beheren.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Zie [Operations Manager koppelen aan Log Analytics](../../azure-monitor/platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="configuration"></a>Configuratie
Voeg de agentstatusoplossing toe aan uw Log Analytics-werkruimte met behulp van het proces dat wordt beschreven in [Oplossingen toevoegen.](solutions.md) Er is geen verdere configuratie nodig.


## <a name="data-collection"></a>Gegevensverzameling
### <a name="supported-agents"></a>Ondersteunde agents
De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| Windows-agents | Ja | Er worden heartbeat-gebeurtenissen verzameld van direct verbonden Windows-agents.|
| Beheergroep System Center Operations Manager | Ja | Heartbeatgebeurtenissen worden verzameld van agents die elke 60 seconden rapporteren aan de beheergroep en vervolgens worden doorgestuurd naar Azure Monitor. Een directe verbinding van Operations Manager-agents met Azure Monitor is niet vereist. Heartbeat-gebeurtenisgegevens worden doorgestuurd van de beheergroep naar de werkruimte Log Analytics.|

## <a name="using-the-solution"></a>De oplossing gebruiken
Wanneer u de oplossing toevoegt aan uw Log Analytics-werkruimte, wordt de tegel **Agentstatus** toegevoegd aan uw dashboard. Op deze tegel ziet u het totale aantal agents en het aantal agents dat de afgelopen 24 uur niet heeft gereageerd.<br><br> ![De tegel Status van agent in het dashboard](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Klik op de tegel **Status van agent** om het **gelijknamige** dashboard te openen.  Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat de tien belangrijkste gebeurtenissen naar aantal die overeenkomen met de criteria van die kolom voor de opgegeven periode. U kunt de volledige lijst met gebeurtenissen weergeven door linksonder elke kolom **Alles weergeven** te selecteren. Dit kan overigens ook door op de kolomkop te klikken.

| Kolom | Beschrijving |
|--------|-------------|
| Agent count over time | Een trend van het aantal agents gedurende een periode van zeven dagen voor Linux- en Windows-agents.|
| Count of unresponsive agents | Een lijst met agents die in de afgelopen 24 uur geen heartbeat hebben verzonden.|
| Distribution by OS Type | Een visualisatie van het aantal Windows- en Linux-agents in uw omgeving.|
| Verdeling naar agent-versie | Een visualisatie van de verschillende agentversies die zijn geïnstalleerd in uw omgeving en het aantal van elke versie.|
| Verdeling naar agent-catgeorie | Een visualisatie van de verschillende categorieën agents die heartbeat-gebeurtenissen verzenden: directe agents, OpsMgr-agents of OpsMgr Management Server.|
| Verdeling naar beheergroep | Een verdeling van de verschillende Operations Manager Management-groepen in uw omgeving.|
| Geo-location of Agents | Een verdeling van de verschillende landen/regio's waar u agenten hebt en een totaal aantal agenten dat in elk land/regio is geïnstalleerd.|
| Count of Gateways Installed | Het aantal servers waarop de Log Analytics-gateway is geïnstalleerd en een lijst met deze servers.|

![Voorbeeld van het dashboard van de oplossing Status van agent](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Logboekrecords voor Azure Monitor
De oplossing maakt één type record in de werkruimte Log Analytics.  

### <a name="heartbeat-records"></a>Heartbeat-records
Er wordt een record van het type **Heartbeat** gemaakt.  Deze records hebben de eigenschappen uit de volgende tabel.  

| Eigenschap | Beschrijving |
| --- | --- |
| `Type` | *Heartbeat*|
| `Category` | De waarde is *Direct Agent*, *SCOM Agent* of *SCOM Management Server*.|
| `Computer` | De naam van de computer.|
| `OSType` | Windows- of Linux-besturingssysteem.|
| `OSMajorVersion` | De primaire versie van het besturingssysteem.|
| `OSMinorVersion` | De secundaire versie van het besturingssysteem.|
| `Version` | Log Analytics Agent of Operations Manager Agent versie.|
| `SCAgentChannel` | De waarde is *Direct* en/of *SCManagementServer*.|
| `IsGatewayInstalled` | Als de Logboekanalysegateway is geïnstalleerd, is de waarde *waar,* anders is de waarde *onwaar.*|
| `ComputerIP` | Het openbare IP-adres van de computer. Op Azure VM's wordt het openbare IP weergegeven als er een beschikbaar is. Voor VM's die privé-IP's gebruiken, wordt hiermee het Azure SNAT-adres weergegeven (niet het privé-IP-adres). |
| `RemoteIPCountry` | De geografische locatie waar de computer is geïmplementeerd.|
| `ManagementGroupName` | De naam van de beheergroep van Operations Manager.|
| `SourceComputerId` | De unieke ID van de computer.|
| `RemoteIPLongitude` | De lengtegraad van geografische locatie van de computer.|
| `RemoteIPLatitude` | De breedtegraad van de geografische locatie van de computer.|

Elke agent die rapporteert aan een Operations Manager-beheerserver stuurt twee heartbeats en de waarde van de eigenschap SCAgentChannel bevat zowel **Direct** als **SCManagementServer,** afhankelijk van welke gegevensbronnen en bewakingsoplossingen u in uw abonnement hebt ingeschakeld. Als u zich herinnert, worden gegevens uit oplossingen rechtstreeks van een Operations Manager-beheerserver naar Azure Monitor verzonden of vanwege de hoeveelheid gegevens die op de agent worden verzameld, rechtstreeks van de agent naar Azure Monitor verzonden. Voor heartbeat-gebeurtenissen met de waarde **SCManagementServer** bestaat de waarde van ComputerIP uit het IP-adres van de beheerserver, aangezien de gegevens door deze server worden geüpload.  Voor heartbeats waarvan de eigenschap SCAgentChannel is ingesteld op **Direct**, is de waarde het openbare IP-adres van de agent.  

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken
De volgende tabel bevat voorbeelden van zoekopdrachten in logboeken voor records die zijn verzameld met deze oplossing.

| Query’s uitvoeren | Beschrijving |
|:---|:---|
| Heartbeat &#124; distinct Computer |Het totale aantal agents |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Het aantal agents dat de afgelopen 24 uur niet heeft gereageerd |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Het aantal agents dat de afgelopen 15 minuten niet heeft gereageerd |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computers die online waren (in de afgelopen 24 uur) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Het totale aantal agents dat offline was in de afgelopen 30 minuten (voor de afgelopen 24 uur) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Een trend van het aantal agents in de tijd per type besturingssysteem|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution by OS Type |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Verdeling naar agent-versie |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Verdeling naar agent-catgeorie |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Verdeling naar beheergroep |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geo-location of Agents |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Aantal geïnstalleerde Log Analytics Gateways |




## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waarschuwingen in Azure Monitor](../platform/alerts-overview.md) voor meer informatie over het genereren van waarschuwingen van logboekquery's. 
