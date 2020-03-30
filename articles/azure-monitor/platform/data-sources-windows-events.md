---
title: Logboeken van Windows-gebeurtenissen verzamelen en analyseren in Azure Monitor | Microsoft Documenten
description: Beschrijft hoe u de verzameling Windows-gebeurtenislogboeken configureren op Azure Monitor en hoe de records die ze maken, worden geconfigureerd.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa34196233ce4037ef6fa49b782b9aa958f7632d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274682"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Windows-gebeurtenislogboekgegevensbronnen in Azure Monitor
Windows Event logs zijn een van de meest voorkomende [gegevensbronnen](agent-data-sources.md) voor het verzamelen van gegevens met behulp van Windows-agents, omdat veel toepassingen schrijven naar de Windows-gebeurtenis log.  U gebeurtenissen verzamelen uit standaardlogboeken zoals Systeem en Toepassing, naast het opgeven van aangepaste logboeken die zijn gemaakt door toepassingen die u moet controleren.

![Windows-gebeurtenissen](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows-gebeurtenislogboeken configureren
Windows-gebeurtenislogboeken configureren in het [menu Gegevens in Geavanceerde instellingen](agent-data-sources.md#configuring-data-sources).

Azure Monitor verzamelt alleen gebeurtenissen uit de Windows-gebeurtenislogboeken die zijn opgegeven in de instellingen.  U een gebeurtenislogboek toevoegen door de naam **+** van het logboek in te typen en op te klikken.  Voor elk logboek worden alleen de gebeurtenissen met de geselecteerde ernst verzameld.  Controleer de ernst van het specifieke logboek dat u wilt verzamelen.  U geen aanvullende criteria geven om gebeurtenissen te filteren.

Terwijl u de naam van een gebeurtenislogboek typt, biedt Azure Monitor suggesties voor algemene namen van gebeurtenislogboeken. Als het logboek dat u wilt toevoegen niet in de lijst wordt weergegeven, u het nog steeds toevoegen door de volledige naam van het logboek in te typen. U de volledige naam van het logboek vinden met behulp van logboekviewer. Open in gebeurtenisviewer de pagina *Eigenschappen* voor het logboek en kopieert u de tekenreeks vanuit het veld *Volledige naam.*

![Windows-gebeurtenissen configureren](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Kritieke gebeurtenissen uit het Windows-gebeurtenislogboek hebben een ernst van 'Fout' in Azure Monitor-logboeken.

## <a name="data-collection"></a>Gegevensverzameling
Azure Monitor verzamelt elke gebeurtenis die overeenkomt met een geselecteerde ernst uit een gecontroleerd gebeurtenislogboek terwijl de gebeurtenis wordt gemaakt.  De agent registreert zijn plaats in elk gebeurtenislogboek waarvan het verzamelt.  Als de agent een bepaalde tijd offline gaat, verzamelt deze gebeurtenissen van waar deze voor het laatst is gebleven, zelfs als deze gebeurtenissen zijn gemaakt terwijl de agent offline was.  Het is mogelijk dat deze gebeurtenissen niet worden verzameld als het gebeurtenislogboek wordt volgestopt met niet-verzamelde gebeurtenissen die worden overschreven terwijl de agent offline is.

>[!NOTE]
>Azure Monitor verzamelt geen auditgebeurtenissen die door SQL Server zijn gemaakt van bron *MSSQLSERVER* met gebeurtenis-id 18453 die trefwoorden bevat - *Klassiek* of *Controlesucces* en trefwoord *0xa000000000000000000*.
>

## <a name="windows-event-records-properties"></a>Eigenschappen van Windows-gebeurtenisrecords
Windows-gebeurtenisrecords hebben een type **gebeurtenis** en hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |Naam van de computer waarvan de gebeurtenis is verzameld. |
| Gebeurteniscategorie |Categorie van het evenement. |
| EventData (EventData) |Alle gebeurtenisgegevens in raw-formaat. |
| EventID |Nummer van het evenement. |
| EventLevel |Ernst van de gebeurtenis in numerieke vorm. |
| EventLevelName |Ernst van de gebeurtenis in tekstvorm. |
| Eventlog |Naam van het gebeurtenislogboek waarvan de gebeurtenis is verzameld. |
| ParameterXml |Parameterwaarden voor gebeurtenissen in XML-indeling. |
| ManagementGroupName |Naam van de beheergroep voor System Center Operations Manager-agents.  Voor andere agenten is deze waarde`AOI-<workspace ID>` |
| Geredaderde beschrijving |Gebeurtenisbeschrijving met parameterwaarden |
| Bron |Bron van het evenement. |
| SourceSystem |Type agent waar de gebeurtenis vandaan is gehaald. <br> OpsManager – Windows-agent, direct connect of Operations Manager beheerd <br> Linux – Alle Linux-agents  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Datum en tijd waarop de gebeurtenis is gemaakt in Windows. |
| UserName |Gebruikersnaam van het account dat de gebeurtenis heeft geregistreerd. |

## <a name="log-queries-with-windows-events"></a>Logboekquery's met Windows-gebeurtenissen
In de volgende tabel vindt u verschillende voorbeelden van logboekquery's waarmee Windows-gebeurtenisrecords worden opgehaald.

| Query’s uitvoeren | Beschrijving |
|:---|:---|
| Gebeurtenis |Alle Windows-gebeurtenissen. |
| Gebeurtenis &#124; waarbij EventLevelName == "fout" |Alle Windows-gebeurtenissen met de ernst van de fout. |
| Gebeurtenis &#124; count() samenvatten op bron |Aantal Windows-gebeurtenissen per bron. |
| Gebeurtenis &#124; waarbij GebeurtenisLevelName == "fout" &#124; het aantal() per bron samenvatten |Aantal foutgebeurtenissen van Windows op bron. |


## <a name="next-steps"></a>Volgende stappen
* Configureer Log Analytics om andere [gegevensbronnen](agent-data-sources.md) te verzamelen voor analyse.
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen.  
* Verzameling [prestatiemeteritems van](data-sources-performance-counters.md) uw Windows-agents configureren.
