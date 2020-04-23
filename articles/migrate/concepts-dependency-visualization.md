---
title: Afhankelijkheidsanalyse in Azure Migrate Server Assessment
description: Beschrijft hoe u afhankelijkheidsanalyse gebruiken voor beoordeling met Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024758"
---
# <a name="dependency-analysis"></a>Afhankelijkheidsanalyse

In dit artikel wordt afhankelijkheidsanalyse beschreven in Azure Migrate:Server Assessment.

## <a name="overview"></a>Overzicht

Afhankelijkheidsanalyse helpt u om afhankelijkheden te identificeren tussen on-premises machines die u wilt beoordelen en migreren naar Azure. 

- In Azure Migrate:Server Assessment verzamelt u machines in een groep en beoordeelt u de groep. Afhankelijkheidsanalyse helpt u om machines nauwkeuriger te groeperen, met een hoog vertrouwen voor beoordeling.
- Met afhankelijkheidsanalyse u machines identificeren die samen moeten worden gemigreerd. U bepalen of machines in gebruik zijn of dat ze kunnen worden buiten bedrijf gesteld in plaats van gemigreerd.
- Het analyseren van afhankelijkheden helpt ervoor te zorgen dat er niets achterblijft en voorkom verrassingsonderbrekingen tijdens migratie.
- Analyse is vooral handig als u niet zeker weet of machines deel uitmaken van een app-implementatie die u naar Azure wilt migreren.
- [Lees](common-questions-discovery-assessment.md#what-is-dependency-visualization) veelgestelde vragen over afhankelijkheidsanalyse.

Er zijn twee opties voor het implementeren van afhankelijkheidsanalyse

- **Op agentgebaseerd:** voor afhankelijkheidsanalyse op basis van agenten moeten agents worden geïnstalleerd op elke on-premises machine die u wilt analyseren.
- **Agentless**: Met agentless analyse hoeft u geen agents te installeren op machines die u wilt controleren. Deze optie is momenteel in preview en is alleen beschikbaar voor VMware VM's.

> [!NOTE]
> Afhankelijkheidsanalyse op basis van agenten is niet beschikbaar in Azure Government. U gebruik maken van agentless afhankelijkheidanalyse.

## <a name="agentless-analysis"></a>Agentloze analyse

Agentless dependency analysis werkt door het vastleggen van TCP-verbindingsgegevens van machines waarvoor deze is ingeschakeld. Er zijn geen agents geïnstalleerd op machines die u wilt analyseren.

### <a name="collected-data"></a>Verzamelde gegevens

Nadat de afhankelijkheidsdetectie is gestart, verzamelt het toestel elke vijf minuten gegevens van machines om gegevens te verzamelen. Deze gegevens worden verzameld van gast VM's via vCenter Server, met behulp van vSphere API's. De verzamelde gegevens worden verwerkt op het Azure Migrate-toestel om identiteitsgegevens af te leiden en worden elke zes uur naar Azure Migrate verzonden.

Polling verzamelt deze gegevens van machines: 
- Naam van processen die actieve verbindingen hebben.
- Naam van de toepassing die processen uitvoert die actieve verbindingen hebben.
- Bestemmingspoort op de actieve verbindingen.

## <a name="agent-based-analysis"></a>Agent-gebaseerde analyse

Voor analyse op basis van agenten gebruikt Server Assessment de [servicekaartoplossing](../azure-monitor/insights/service-map.md) in Azure Monitor om afhankelijkheidsvisualisatie en -analyse mogelijk te maken. De [Microsoft Monitoring Agent/Log Analytics-agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent) en de [afhankelijkheidsagent](../azure-monitor/platform/agents-overview.md#dependency-agent)moeten worden geïnstalleerd op elke machine die u wilt analyseren.

### <a name="collected-data"></a>Verzamelde gegevens

Voor analyse op basis van agenten worden de volgende gegevens verzameld:

- Naam van de sourcemachineserver, proces, naam van de toepassing.
- Naam, proces, toepassingsnaam en poort van doelmachineserver.
- Het aantal verbindingen, latentie en gegevensoverdrachtsgegevens wordt verzameld en beschikbaar voor Log Analytics-query's. 


## <a name="compare-agentless-and-agent-based"></a>Vergelijk agentless en agent-based

De verschillen tussen agentloze visualisatie en agentgebaseerde visualisatie worden samengevat in de tabel.

**Vereiste** | **Zonder agent** | **Agent-based**
--- | --- | ---
Ondersteuning | Deze optie is momenteel in preview en is alleen beschikbaar voor VMware VM's. [Bekijk](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) ondersteunde besturingssystemen. | In het algemeen beschikbaarheid (GA).
Agent | Het is niet nodig om agents te installeren op machines die u wilt controleren. | Agents die moeten worden geïnstalleerd op elke on-premises machine die u wilt analyseren: de [Microsoft Monitoring-agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)en de [afhankelijkheidsagent.](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent) 
Log Analytics | Niet vereist. | Azure Migrate gebruikt de [Service Map-oplossing](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) in [Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) voor afhankelijkheidsanalyse. 
Hoe werkt het? | Hiermee legt u TCP-verbindingsgegevens vast op machines die zijn ingeschakeld voor afhankelijkheidsvisualisatie. Na ontdekking verzamelt het gegevens met intervallen van vijf minuten. | Servicemap-agents die op een machine zijn geïnstalleerd, verzamelen gegevens over TCP-processen en inkomende/uitgaande verbindingen voor elk proces.
Gegevens | Naam van de sourcemachineserver, proces, naam van de toepassing.<br/><br/> Naam, proces, toepassingsnaam en poort van doelmachineserver. | Naam van de sourcemachineserver, proces, naam van de toepassing.<br/><br/> Naam, proces, toepassingsnaam en poort van doelmachineserver.<br/><br/> Het aantal verbindingen, latentie en gegevensoverdrachtsgegevens wordt verzameld en beschikbaar voor Log Analytics-query's. 
Visualisatie | Afhankelijkheidskaart van één server kan gedurende een uur tot 30 dagen worden bekeken. | Afhankelijkheidskaart van één server.<br/><br/> Kaart kan worden bekeken meer dan een uur alleen.<br/><br/> Afhankelijkheidskaart van een groep servers.<br/><br/> Servers in een groep toevoegen en verwijderen uit de kaartweergave.
Gegevensexport | Kan momenteel niet worden gedownload in tabelindeling. | Gegevens kunnen worden opgevraagd met Log Analytics.



## <a name="next-steps"></a>Volgende stappen
- Bekijk de vereisten voor het opzetten van op agentgebaseerde analyses voor Vm's op basis [van VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [fysieke servers](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)en [Hyper-V VM's](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Bekijk](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) de vereisten voor agentless analyse van VMware VM's.
- [Afhankelijkheidsvisualisatie](how-to-create-group-machine-dependencies.md) op basis van agent instellen
- [Probeer](how-to-create-group-machine-dependencies-agentless.md) agentloze afhankelijkheidsvisualisatie voor VMware VM's uit.
- Lees [veelgestelde vragen](common-questions-discovery-assessment.md#what-is-dependency-visualization) over afhankelijkheidsvisualisatie.


