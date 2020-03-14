---
title: Ondersteuning voor fysieke server evaluatie met Azure Migrate
description: Meer informatie over ondersteuning voor fysieke server beoordeling met Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: f2698d0ff046147599a8c5c791a0980a54090932
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269521"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Ondersteunings matrix voor fysieke server evaluatie 

U kunt de [Azure migrate-service](migrate-overview.md) gebruiken voor het beoordelen en migreren van machines naar de Microsoft Azure Cloud. In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het beoordelen en migreren van on-premises fysieke servers.


## <a name="overview"></a>Overzicht

Als u on-premises machines wilt evalueren voor migratie naar Azure met dit artikel, voegt u het hulp programma Azure Migrate: Server Assessment toe aan een Azure Migrate-project. U implementeert het [Azure migrate-apparaat](migrate-appliance.md). Het apparaat detecteert voortdurend on-premises machines en verzendt configuratie-en prestatie gegevens naar Azure. Na detectie van machines verzamelt u gedetecteerde computers in groepen en voert u een evaluatie uit voor een groep

## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Beoordelings limieten**| Ontdek en evalueer Maxi maal 35.000 fysieke servers in één [project](migrate-support-matrix.md#azure-migrate-projects).
**Project limieten** | U kunt meerdere projecten maken in een Azure-abonnement. Een project kan bestaan uit VMware-Vm's, virtuele Hyper-V-machines en fysieke servers, tot aan de evaluatie limieten.
**Detectie** | Het Azure Migrate-apparaat kan Maxi maal 250 fysieke servers detecteren.
**Evaluatie** | U kunt Maxi maal 35.000 computers in één groep toevoegen.<br/><br/> U kunt Maxi maal 35.000 computers in één evaluatie evalueren.

Meer [informatie](concepts-assessment-calculation.md) over evaluaties.




## <a name="physical-server-requirements"></a>Vereisten voor fysieke servers

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Fysieke server implementatie**       | De fysieke server kan zelfstandig of in een cluster worden geïmplementeerd. |
| **Machtigingen**           | **Windows:** Stel een lokaal of domein gebruikers account in op alle Windows-servers die u wilt toevoegen in de detectie. Het gebruikers account moet worden toegevoegd aan deze groepen-Extern bureaublad gebruikers, prestatie meter gebruikers en prestatie logboek gebruikers. <br/> **Linux:** U hebt een hoofd account nodig op de Linux-servers die u wilt detecteren. |
| **Besturingssysteem** | Alle [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -besturings systemen worden ondersteund, met uitzonde ring van het volgende:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. Het apparaat voor fysieke servers kan worden uitgevoerd op een virtuele machine of op een fysieke computer. U stelt deze in met behulp van een Power shell-script dat u van de Azure Portal downloadt.

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---physical) voor fysieke servers.
- Meer informatie over de [url's](migrate-appliance.md#url-access) waartoe het apparaat toegang moet hebben.

## <a name="port-access"></a>Poort toegang

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaatconfiguratie** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Uitgaande verbindingen op poort 443 (HTTPS), 5671 en 5672 (AMQP) voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Fysieke servers** | **Windows:** Binnenkomende verbindingen op WinRM-poorten 5985 (HTTP) en 5986 (HTTPS) voor het ophalen van meta gegevens van de configuratie en prestaties van Windows-servers. <br/> **Linux:**  Binnenkomende verbindingen op poort 22 (UDP) voor het ophalen van meta gegevens van de configuratie en prestaties van Linux-servers. |

## <a name="agent-based-dependency-visualization"></a>Visualisatie van afhankelijkheid op basis van een agent

Met de [visualisatie van afhankelijkheden](concepts-dependency-visualization.md) kunt u afhankelijkheden visualiseren tussen computers die u wilt beoordelen en migreren. Voor visualisaties op basis van een agent worden vereisten en beperkingen beschreven in de volgende tabel.


**Vereiste** | **Details**
--- | ---
**Implementatie** | Voordat u een afhankelijkheids visualisatie implementeert, moet er een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project. U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises machines te detecteren.<br/><br/> Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.
**Serviceoverzicht** | Visualisatie op basis van een agent maakt gebruik van de [servicetoewijzing](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) oplossing in [Azure monitor logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Als u wilt implementeren, koppelt u een nieuwe of bestaande Log Analytics-werk ruimte aan een Azure Migrate-project.
**Log Analytics-werkruimte** | De werk ruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden.<br/><br/>  De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
**Belastingen** | Voor de Servicetoewijzing oplossing worden geen kosten in rekening gebracht voor de eerste 180 dagen (vanaf de dag dat u de werk ruimte Log Analytics aan het Azure Migrate project hebt gekoppeld).<br/><br/> Na 180 dagen zijn de standaard Log Analytics kosten van toepassing.<br/><br/> Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden standaard Log Analytics kosten in rekening gebracht.<br/><br/> Als u het Azure Migrate project verwijdert, wordt de werk ruimte niet verwijderd. Nadat het project is verwijderd, is Servicetoewijzing niet gratis en worden alle knoop punten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte.
**Middelen** | Voor visualisatie op basis van een agent moet u twee agents installeren op elke computer die u wilt analyseren.<br/><br/> - [micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - - [afhankelijkheids agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Verbinding met internet** | Als computers niet zijn verbonden met internet, moet u de Log Analytics-gateway hierop installeren.

## <a name="next-steps"></a>Volgende stappen

De [evaluatie van de fysieke server wordt voor bereid](tutorial-prepare-physical.md).
