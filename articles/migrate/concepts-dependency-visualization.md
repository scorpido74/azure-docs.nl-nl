---
title: Visualisatie van afhankelijkheden in Azure Migrate
description: Hierin wordt een overzicht gegeven van de evaluatie berekeningen in de server Assessment-service in Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: f24656d02e19f422ff26e6b06d1631a9128dff43
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587104"
---
# <a name="dependency-visualization"></a>Visualisatie van afhankelijkheden

In dit artikel wordt de afhankelijkheids visualisatie in Azure Migrate beschreven: Server evaluatie.

## <a name="what-is-dependency-visualization"></a>Wat is de visualisatie van afhankelijkheden?

Met de visualisatie van afhankelijkheden kunt u afhankelijkheden identificeren tussen on-premises machines die u wilt beoordelen en migreren naar Azure. 

- In Azure Migrate: Server analyse verzamelt u computers in een groep en evalueert u vervolgens de groep. Met een afhankelijkheids visualisatie kunt u machines nauw keuriger groeperen, met een hoge mate van vertrouwen voor evaluatie.
- Met de functie voor afhankelijkheids visualisatie kunt u computers identificeren die samen moeten worden gemigreerd. U kunt nagaan of machines in gebruik zijn of in plaats van gemigreerd kunnen worden.
- Met het visualiseren van afhankelijkheden kunt u ervoor zorgen dat er geen onverwachte storingen optreden tijdens de migratie.
- Visualisatie is vooral nuttig als u niet zeker weet of machines deel uitmaken van een app-implementatie die u naar Azure wilt migreren.


> [!NOTE]
> Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.

## <a name="agent-basedagentless-visualization"></a>Op agents gebaseerde en zonder agents visualisatie

Er zijn twee opties voor het implementeren van afhankelijkheids visualisatie:

- **Op agent gebaseerd**: voor visualisatie op basis van een agent moeten agents worden geïnstalleerd op elke on-premises computer die u wilt analyseren.
- Zonder **agent**: met deze optie hoeft u geen agents te installeren op computers die u wilt kruisen. Deze optie is momenteel in Preview en is alleen beschikbaar voor virtuele VMware-machines.


## <a name="agent-based-visualization"></a>Visualisatie op basis van een agent

**Vereiste** | **Details** | **Meer informatie**
--- | --- | ---
**Vóór implementatie** | Er moet een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project.<br/><br/>  U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises machines te detecteren. | [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/><br/> [Meer informatie over het](how-to-assess.md) toevoegen van een evaluatie programma aan een bestaand project.<br/><br/> Meer informatie over het instellen van het Azure Migrate-apparaat voor de evaluatie van [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)-en fysieke servers.
**Vereiste agents** | Installeer de volgende agents op elke computer die u wilt analyseren:<br/><br/> [Micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> De [afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Als on-premises computers niet zijn verbonden met internet, moet u Log Analytics gateway op deze machines downloaden en installeren. | Meer informatie over het installeren van de [dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics** | Azure Migrate gebruikt de [servicetoewijzing](../operations-management-suite/operations-management-suite-service-map.md) oplossing in [Azure monitor logboeken](../log-analytics/log-analytics-overview.md) voor de visualisatie van afhankelijkheden.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werk ruimte aan een Azure Migrate-project. De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.<br/><br/> De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.
**Uitgaven** | De Servicetoewijzing oplossing heeft geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte Log Analytics koppelt aan het Azure Migrate project)/<br/><br/> Na 180 dagen zijn de standaard Log Analytics kosten van toepassing.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden [standaard kosten](https://azure.microsoft.com/pricing/details/log-analytics/) in rekening gebracht voor log Analytics.<br/><br/> Wanneer het Azure Migrate project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Na het verwijderen van het project is Servicetoewijzing gebruik niet gratis en worden voor elk knoop punt de kosten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt vóór Azure Migrate algemene Beschik baarheid (GA 28 februari 2018), hebt u mogelijk extra Servicetoewijzing kosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen alleen betaalt, is het raadzaam om een nieuw project te maken, omdat bestaande werk ruimten voor GA nog steeds toerekenbaar zijn.
**Beheer** | Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel van het Azure Migrate project.<br/><br/> U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate.<br/><br/> Als u het gekoppelde Azure Migrate project verwijdert, wordt de werk ruimte niet automatisch verwijderd. [Verwijder deze hand matig](../azure-monitor/platform/manage-access.md).<br/><br/> Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het Azure Migrate project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.

## <a name="agentless-visualization"></a>Visualisatie zonder agent


**Vereiste** | **Details** | **Meer informatie**
--- | --- | ---
**Vóór implementatie** | Er moet een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project.<br/><br/>  U kunt afhankelijkheids visualisatie implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises VMWare-machines te detecteren. | [Meer informatie over](create-manage-projects.md) het maken van een project voor de eerste keer.<br/><br/> [Meer informatie over het](how-to-assess.md) toevoegen van een evaluatie programma aan een bestaand project.<br/><br/> Meer informatie over het instellen van het Azure Migrate-apparaat voor de evaluatie van virtuele [VMware](how-to-set-up-appliance-vmware.md) -machines.
**Vereiste agents** | Er is geen agent vereist voor computers die u wilt analyseren.
**Ondersteunde besturingssystemen** | Bekijk de [besturings systemen](migrate-support-matrix-vmware.md#agentless-dependency-visualization) die worden ondersteund voor visualisatie zonder agent.
**VM's** | **VMware-hulpprogram ma's**: VMware-hulpprogram ma's moeten zijn geïnstalleerd en worden uitgevoerd op de virtuele machines die u wilt analyseren.<br/><br/> **Account**: op het Azure migrate apparaat moet u een gebruikers account toevoegen dat kan worden gebruikt om toegang te krijgen tot vm's voor analyse.<br/><br/> **Windows-vm's**: het gebruikers account moet een lokale of een domein beheerder zijn op de computer.<br/><br/> **Linux-vm's**: de hoofd bevoegdheid is vereist voor het account. De gebruikers account vereist ook deze twee mogelijkheden op/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE. | [Meer informatie over](migrate-appliance.md) het Azure migrate apparaat.
**VMware** | **vCenter**: voor het apparaat is een vCenter Server-account met alleen-lezen toegang vereist en de bevoegdheden voor virtual machines > gast bewerkingen zijn ingeschakeld.<br/><br/> **ESXi-hosts**: op ESXi-hosts waarop vm's worden uitgevoerd die u wilt analyseren, moet het Azure migrate apparaat verbinding kunnen maken met TCP-poort 443.
**Verzamelde gegevens** |  Visualisatie van de afhankelijkheid van agents werkt door TCP-verbindings gegevens vast te leggen van computers waarvoor deze is ingeschakeld. Nadat de afhankelijkheids detectie is gestart, verzamelt het apparaat deze gegevens van machines door elke vijf minuten te pollen:<br/> -TCP-verbindingen.<br/> -Namen van processen die actieve verbindingen hebben.<br/> -Namen van geïnstalleerde toepassingen die het proces uitvoeren met actieve verbindingen.<br/> -Het aantal verbindingen dat wordt gedetecteerd bij elk polling-interval.


## <a name="next-steps"></a>Volgende stappen
- [Visualisatie van afhankelijkheid instellen](how-to-create-group-machine-dependencies.md)
- [Probeer visualisatie van de afhankelijkheid van agents uit](how-to-create-group-machine-dependencies-agentless.md) voor VMware-vm's.
- Bekijk [Veelgestelde vragen](common-questions-discovery-assessment.md#what-is-dependency-visualization) over de visualisatie van afhankelijkheden.


