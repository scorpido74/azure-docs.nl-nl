---
title: Ondersteuning voor Hyper-V-evaluatie in Azure Migrate
description: Meer informatie over ondersteuning voor Hyper-V-evaluatie met Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 9c1228992d71e56b9118e88967478e619c14959a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834464"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Ondersteunings matrix voor Hyper-V-evaluatie

In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het beoordelen van virtuele Hyper-V-machines met [Azure migrate: Server evaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Als u op zoek bent naar informatie over het migreren van virtuele Hyper-V-machines naar Azure, raadpleegt u de [ondersteunings matrix voor migratie](migrate-support-matrix-hyper-v-migration.md).

## <a name="overview"></a>Overzicht

Als u on-premises machines wilt evalueren voor migratie naar Azure met dit artikel, voegt u het hulp programma Azure Migrate: Server Assessment toe aan een Azure Migrate-project. U implementeert het [Azure migrate-apparaat](migrate-appliance.md). Het apparaat detecteert voortdurend on-premises machines en verzendt configuratie-en prestatie gegevens naar Azure. Na detectie van machines verzamelt u gedetecteerde computers in groepen en voert u een evaluatie uit voor een groep.


## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Beoordelings limieten**| Ontdek en evalueer Maxi maal 35.000 virtuele Hyper-V-machines in een enkel [project](migrate-support-matrix.md#azure-migrate-projects).
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement. Een project kan bestaan uit VMware-Vm's, virtuele Hyper-V-machines en fysieke servers, tot aan de evaluatie limieten.
**Detectie** | Het Azure Migrate-apparaat kan Maxi maal 5000 Hyper-V-Vm's detecteren.<br/><br/> Het apparaat kan verbinding maken met Maxi maal 300 Hyper-V-hosts.
**Evaluatie** | U kunt Maxi maal 35.000 computers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 Vm's in één evaluatie evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.



## <a name="hyper-v-host-requirements"></a>Vereisten voor de Hyper-V-host

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie van host**       | De Hyper-V-host kan zelfstandig of in een cluster worden geïmplementeerd. |
| **Machtigingen**           | U hebt beheerders machtigingen nodig op de Hyper-V-host. <br/> Als u geen beheerders machtigingen wilt toewijzen, maakt u een lokaal of domein gebruikers account en voegt u de gebruiker toe aan deze groepen: gebruikers van extern beheer, Hyper-V-Administrators en prestatie meter gebruikers. |
| **Besturings systeem van host** | Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2.<br/> U kunt geen VM's beoordelen die zich bevinden op Hyper-V-hosts waarop Windows Server 2012 wordt uitgevoerd. |
| **Externe communicatie met Power shell**   | Moet op elke host zijn ingeschakeld. |
| **Hyper-V replica**       | Als u Hyper-V replica gebruikt (of als u meerdere virtuele machines met dezelfde VM-id's hebt), en zowel de oorspronkelijke als de gerepliceerde Vm's detecteert met Azure Migrate, is de door Azure Migrate gegenereerde evaluatie mogelijk niet nauw keurig. |


## <a name="hyper-v-vm-requirements"></a>Vereisten voor Hyper-V-virtuele machines

| **Ondersteuning**                  | **Details**               
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -besturings systemen die worden ondersteund door Azure. |
| **Integratie Services**       | [Hyper-V-integratie Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) moeten worden uitgevoerd op vm's die u controleert, om informatie over het besturings systeem vast te leggen. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. Het apparaat voor Hyper-V wordt uitgevoerd op een Hyper-V-VM en wordt geïmplementeerd met behulp van een gecomprimeerde Hyper-V VHD die u van de Azure Portal downloadt. 

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---hyper-v) voor Hyper-V.
- Meer informatie over de [url's](migrate-appliance.md#url-access) waartoe het apparaat toegang moet hebben.

## <a name="port-access"></a>Poort toegang

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaatconfiguratie** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Uitgaande verbindingen op poort 443 (HTTPS), 5671 en 5672 (AMQP) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Hyper-V-host/cluster** | Binnenkomende verbindingen op WinRM-poorten 5985 (HTTP) en 5986 (HTTPS) voor het ophalen van meta gegevens over de configuratie en prestaties van de virtuele Hyper-V-machines met behulp van een Common Information Model (CIM)-sessie.

## <a name="agent-based-dependency-visualization"></a>Visualisatie van afhankelijkheid op basis van een agent

Met de [visualisatie van afhankelijkheden](concepts-dependency-visualization.md) kunt u afhankelijkheden visualiseren tussen computers die u wilt beoordelen en migreren. Voor visualisaties op basis van een agent worden vereisten en beperkingen in de volgende tabel samenvatten:


**Vereiste** | **Details**
--- | ---
**Implementatie** | Voordat u een afhankelijkheids visualisatie implementeert, moet er een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project. U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises machines te detecteren.<br/><br/> Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
**Serviceoverzicht** | Visualisatie op basis van een agent maakt gebruik van de [servicetoewijzing](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) oplossing in [Azure monitor logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Als u wilt implementeren, koppelt u een nieuwe of bestaande Log Analytics-werk ruimte aan een Azure Migrate-project.
**Log Analytics-werkruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
**Belastingen** | Voor de Servicetoewijzing oplossing worden geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte Log Analytics aan het Azure Migrate project hebt gekoppeld).<br/><br/> Na 180 dagen zijn de standaard Log Analytics kosten van toepassing.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden standaard Log Analytics kosten in rekening gebracht.<br/><br/> Als u het Azure Migrate project verwijdert, wordt de werk ruimte niet verwijderd. Nadat het project is verwijderd, is Servicetoewijzing niet gratis en worden alle knoop punten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte.
**Middelen** | Voor visualisatie op basis van een agent moet u twee agents installeren op elke computer die u wilt analyseren.<br/><br/> - [micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - - [afhankelijkheids agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Verbinding met internet** | Als computers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.


## <a name="next-steps"></a>Volgende stappen

[De evaluatie van de Hyper-V-VM voorbereiden](tutorial-prepare-hyper-v.md)
