---
title: Afhankelijkheids analyse in Azure Migrate server-evaluatie
description: Hierin wordt beschreven hoe u afhankelijkheids analyse gebruikt voor evaluatie met behulp van Azure Migrate server-evaluatie.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b269322f5426a68b072452bc2f79531685be3742
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84447574"
---
# <a name="dependency-analysis"></a>Afhankelijkheids analyse

In dit artikel wordt de analyse van afhankelijkheden in Azure Migrate: Server evaluatie beschreven.

## <a name="overview"></a>Overzicht

Afhankelijkheids analyse helpt u bij het identificeren van afhankelijkheden tussen on-premises machines die u wilt beoordelen en migreren naar Azure. 

- In Azure Migrate: Server analyse verzamelt u computers in een groep en evalueert u vervolgens de groep. Afhankelijkheids analyse helpt u machines nauw keuriger te groeperen, met een hoge mate van vertrouwen voor evaluatie.
- Met afhankelijkheids analyse kunt u computers identificeren die samen moeten worden gemigreerd. U kunt nagaan of machines in gebruik zijn of in plaats van gemigreerd kunnen worden.
- Met het analyseren van afhankelijkheden kunt u ervoor zorgen dat er geen onverwachte storingen optreden tijdens de migratie.
- Analyse is vooral nuttig als u niet zeker weet of machines deel uitmaken van een app-implementatie die u naar Azure wilt migreren.
- [Bekijk](common-questions-discovery-assessment.md#what-is-dependency-visualization) Veelgestelde vragen over afhankelijkheids analyse.

Er zijn twee opties voor het implementeren van afhankelijkheids analyse

- **Op agent gebaseerd**: afhankelijkheids analyse op basis van een agent vereist dat agents worden geïnstalleerd op elke on-premises computer die u wilt analyseren.
- Zonder **agents**: u hoeft geen agents te installeren op computers die u wilt kruisen om te controleren. Deze optie is momenteel in Preview en is alleen beschikbaar voor virtuele VMware-machines.

> [!NOTE]
> Afhankelijkheids analyse op basis van een agent is niet beschikbaar in Azure Government. U kunt afhankelijkheidsanalyse zonder agent gebruiken.

## <a name="agentless-analysis"></a>Analyse zonder agent

Analyse van de afhankelijkheid van agents werkt door TCP-verbindings gegevens vast te leggen van computers waarvoor deze is ingeschakeld. Er zijn geen agents geïnstalleerd op computers die u wilt analyseren.

### <a name="collected-data"></a>Verzamelde gegevens

Nadat de detectie van afhankelijkheden is gestart, pollt het apparaat om de vijf minuten gegevens van machines om gegevens te verzamelen. Deze gegevens worden verzameld van gast-Vm's via vCenter Server, met behulp van vSphere-Api's. De verzamelde gegevens worden verwerkt op het Azure Migrate apparaat, om identiteits gegevens af te leiden en worden elke zes uur verzonden naar Azure Migrate.

Met polling worden deze gegevens van machines verzameld: 
- De naam van processen die actieve verbindingen hebben.
- Naam van de toepassing die processen uitvoert die actieve verbindingen hebben.
- Doel poort van de actieve verbindingen.

## <a name="agent-based-analysis"></a>Analyse op basis van een agent

Voor analyse op basis van een agent gebruikt server beoordeling de [servicetoewijzing oplossing](../azure-monitor/insights/service-map.md) in azure monitor om de visualisatie en analyse van afhankelijkheden in te scha kelen. De agent voor [micro soft Monitoring Agent/log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) en de [agent voor afhankelijkheden](../azure-monitor/platform/agents-overview.md#dependency-agent)moeten worden geïnstalleerd op elke computer die u wilt analyseren.

### <a name="collected-data"></a>Verzamelde gegevens

Voor analyse op basis van een agent worden de volgende gegevens verzameld:

- Naam van de bron computer server, proces, toepassings naam.
- Naam van de doel computer server, proces, toepassings naam en poort.
- Het aantal gegevens over verbindingen, latentie en gegevens overdracht wordt verzameld en beschikbaar gesteld voor Log Analytics query's. 


## <a name="compare-agentless-and-agent-based"></a>Vergelijkt agentloos en op basis van een agent

De verschillen tussen visualisatie zonder agents en visualisaties op basis van agents worden in de tabel samenvatten.

**Vereiste** | **Zonder agent** | **Op basis van een agent**
--- | --- | ---
Ondersteuning | Deze optie is momenteel in Preview en is alleen beschikbaar voor virtuele VMware-machines. [Bekijk](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) ondersteunde besturings systemen. | In algemene Beschik baarheid (GA).
Agent | U hoeft geen agents te installeren op computers die u wilt cross-checken. | Agents die moeten worden geïnstalleerd op elke on-premises computer die u wilt analyseren: [micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)en de [dependency agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Niet vereist. | Azure Migrate gebruikt de [servicetoewijzing](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) oplossing in [Azure monitor logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) voor afhankelijkheids analyse. 
Hoe werkt het? | Hiermee worden TCP-verbindings gegevens vastgelegd op computers die zijn ingeschakeld voor de visualisatie van afhankelijkheden. Na detectie verzamelt het gegevens met intervallen van vijf minuten. | Servicetoewijzing agents die op een computer zijn geïnstalleerd, verzamelen gegevens over TCP-processen en inkomende/uitgaande verbindingen voor elk proces.
Gegevens | Naam van de bron computer server, proces, toepassings naam.<br/><br/> Naam van de doel computer server, proces, toepassings naam en poort. | Naam van de bron computer server, proces, toepassings naam.<br/><br/> Naam van de doel computer server, proces, toepassings naam en poort.<br/><br/> Het aantal gegevens over verbindingen, latentie en gegevens overdracht wordt verzameld en beschikbaar gesteld voor Log Analytics query's. 
Visualisatie | Afhankelijkheids toewijzing van één server kan worden weer gegeven gedurende een periode van één uur tot 30 dagen. | Afhankelijkheids toewijzing van één server.<br/><br/> De kaart kan alleen over een uur worden weer gegeven.<br/><br/> Afhankelijkheids toewijzing van een groep servers.<br/><br/> Servers in een groep toevoegen aan en verwijderen uit de kaart weergave.
Gegevensexport | Gegevens van de afgelopen 30 dagen kunnen worden gedownload in een CSV-indeling. | Gegevens kunnen worden opgevraagd met Log Analytics.



## <a name="next-steps"></a>Volgende stappen
- Bekijk de vereisten voor het instellen van analyse op basis van een agent voor virtuele [VMware-machines](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [fysieke servers](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)en [Hyper-V-vm's](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Bekijk](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) de vereisten voor de analyse zonder agent van virtuele VMware-machines.
- Visualisatie van afhankelijkheid op basis van [een agent instellen](how-to-create-group-machine-dependencies.md)
- [Probeer](how-to-create-group-machine-dependencies-agentless.md) visualisatie van de afhankelijkheid van agents uit voor VMware-vm's.
- Bekijk [Veelgestelde vragen](common-questions-discovery-assessment.md#what-is-dependency-visualization) over de visualisatie van afhankelijkheden.


