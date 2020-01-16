---
title: Ondersteuning voor fysieke server evaluatie met Azure Migrate
description: Meer informatie over ondersteuning voor fysieke server beoordeling met Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028772"
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
| **Machtigingen**           | **Windows:** Stel een lokale gebruikers account in op alle Windows-servers die u wilt toevoegen in de detectie. Het gebruikers account moet worden toegevoegd aan deze groepen-Extern bureaublad gebruikers, prestatie meter gebruikers en prestatie logboek gebruikers. <br/> **Linux:** U hebt een hoofd account nodig op de Linux-servers die u wilt detecteren. |
| **Besturingssysteem** | Alle [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -besturings systemen worden ondersteund, met uitzonde ring van het volgende:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Vereisten van Azure Migrate apparaat

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. Het apparaat voor fysieke servers kan worden uitgevoerd op een virtuele machine of op een fysieke computer. U stelt deze in met behulp van een Power shell-script dat u van de Azure Portal downloadt.

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---physical) voor fysieke servers.
- Meer informatie over de [url's](migrate-appliance.md#url-access) waartoe het apparaat toegang moet hebben.

## <a name="port-access"></a>Poort toegang

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaat** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Uitgaande verbindingen op de poorten 443, 5671 en 5672 voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Fysieke servers** | **Windows:** Binnenkomende verbindingen op poort 443, WinRM-poorten 5985 (HTTP) en 5986 (HTTPS) voor het ophalen van meta gegevens van de configuratie en prestaties van Windows-servers. <br/> **Linux:**  Binnenkomende verbindingen op poort 22 (UDP) voor het ophalen van meta gegevens van de configuratie en prestaties van Linux-servers. |


## <a name="next-steps"></a>Volgende stappen

De [evaluatie van de fysieke server wordt voor bereid](tutorial-prepare-physical.md).
