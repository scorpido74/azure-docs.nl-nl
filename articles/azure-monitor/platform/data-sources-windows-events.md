---
title: Verzamelen en analyseren van Windows-gebeurtenis Logboeken in Azure Monitor | Microsoft Docs
description: Hierin wordt beschreven hoe u de verzameling van Windows-gebeurtenis logboeken configureert door Azure Monitor en Details van de records die ze maken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274682"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Gegevens bronnen in het Windows-gebeurtenis logboek in Azure Monitor
Windows-gebeurtenis logboeken zijn een van de meest voorkomende [gegevens bronnen](agent-data-sources.md) voor het verzamelen van gegevens met behulp van Windows-agents, omdat veel toepassingen naar het Windows-gebeurtenis logboek schrijven.  U kunt gebeurtenissen uit standaard logboeken, zoals systeem en toepassing, verzamelen naast het opgeven van aangepaste logboeken die zijn gemaakt door toepassingen die u wilt bewaken.

![Windows-gebeurtenissen](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows-gebeurtenis logboeken configureren
Configureer Windows-gebeurtenis logboeken [in het menu Data van de geavanceerde instellingen](agent-data-sources.md#configuring-data-sources).

Azure Monitor verzamelt alleen gebeurtenissen uit de Windows-gebeurtenis logboeken die zijn opgegeven in de instellingen.  U kunt een gebeurtenis logboek toevoegen door de naam van het logboek te typen en op **+** te klikken.  Voor elk logboek worden alleen de gebeurtenissen met de geselecteerde Ernst verzameld.  Controleer de ernst van het specifieke logboek dat u wilt verzamelen.  U kunt geen aanvullende criteria opgeven om gebeurtenissen te filteren.

Wanneer u de naam van een gebeurtenis logboek typt, geeft Azure Monitor suggesties voor veelvoorkomende namen van gebeurtenis Logboeken. Als het logboek dat u wilt toevoegen niet in de lijst wordt weer gegeven, kunt u het nog steeds toevoegen door de volledige naam van het logboek te typen. U kunt de volledige naam van het logboek vinden met behulp van Logboeken. Open de pagina *Eigenschappen* voor het logboek in Logboeken en kopieer de teken reeks uit het veld *volledige naam* .

![Windows-gebeurtenissen configureren](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Kritieke gebeurtenissen van het Windows-gebeurtenis logboek hebben de ernst fout in Azure Monitor Logboeken.

## <a name="data-collection"></a>Gegevensverzameling
Azure Monitor verzamelt elke gebeurtenis die overeenkomt met een bepaalde ernst van een bewaakt gebeurtenis logboek wanneer de gebeurtenis wordt gemaakt.  De agent legt de locatie vast in elk gebeurtenis logboek dat wordt verzameld van.  Als de agent gedurende een bepaalde tijd offline gaat, worden er gebeurtenissen verzameld van waar deze voor het laatst is uitgeschakeld, zelfs als deze gebeurtenissen zijn gemaakt terwijl de agent offline was.  Deze gebeurtenissen kunnen niet worden verzameld als het gebeurtenis logboek vastloopt met niet-verzamelde gebeurtenissen die worden overschreven terwijl de agent offline is.

>[!NOTE]
>Azure Monitor verzamelt geen controle gebeurtenissen die zijn gemaakt door SQL Server van de bron *MSSQLSERVER* met gebeurtenis-id 18453 die tref woorden bevat: *klassiek* of *geslaagde controle* en trefwoord *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Eigenschappen van Windows-gebeurtenis records
Windows-gebeurtenis records hebben een type **gebeurtenis** en hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |De naam van de computer waarop de gebeurtenis is verzameld. |
| EventCategory |De categorie van de gebeurtenis. |
| Event Data |Alle gebeurtenis gegevens in RAW-indeling. |
| Gebeurtenis |Nummer van de gebeurtenis. |
| EventLevel |De ernst van de gebeurtenis in de vorm van een getal. |
| EventLevelName |De ernst van de gebeurtenis in de vorm van tekst. |
| Geschreven |De naam van het gebeurtenis logboek waaruit de gebeurtenis is verzameld. |
| ParameterXml |Gebeurtenis parameter waarden in XML-indeling. |
| ManagementGroupName |De naam van de beheer groep voor System Center Operations Manager agents.  Voor andere agents is deze waarde `AOI-<workspace ID>` |
| RenderedDescription |Gebeurtenis beschrijving met parameter waarden |
| Bron |De bron van de gebeurtenis. |
| SourceSystem |Type agent waaruit de gebeurtenis is verzameld. <br> OpsManager: Windows-agent, Direct Connect of Operations Manager beheerd <br> Linux: alle Linux-agents  <br> Opslag – Azure Diagnostics |
| TimeGenerated |De datum en tijd waarop de gebeurtenis is gemaakt in Windows. |
| UserName |De gebruikers naam van het account waarmee de gebeurtenis is geregistreerd. |

## <a name="log-queries-with-windows-events"></a>Query's vastleggen in Logboeken met Windows-gebeurtenissen
De volgende tabel bevat verschillende voor beelden van logboek query's waarmee Windows-gebeurtenis records worden opgehaald.

| Query's uitvoeren | Beschrijving |
|:---|:---|
| Gebeurtenis |Alle Windows-gebeurtenissen. |
| Gebeurtenis &#124; waarbij EventLevelName = = "Error" |Alle Windows-gebeurtenissen met de ernst van de fout. |
| Aantal &#124; gebeurtenissen samenvatten () per bron |Aantal Windows-gebeurtenissen per bron. |
| Gebeurtenis &#124; waarbij EventLevelName = = "Error" &#124; aantal overzichten () per bron |Aantal Windows-fout gebeurtenissen per bron. |


## <a name="next-steps"></a>Volgende stappen
* Configureer Log Analytics voor het verzamelen van andere [gegevens bronnen](agent-data-sources.md) voor analyse.
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.  
* Configureer het [verzamelen van prestatie meter items](data-sources-performance-counters.md) van uw Windows-agents.
