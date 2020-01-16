---
title: Ondersteuning voor Hyper-V-evaluatie in Azure Migrate
description: Meer informatie over ondersteuning voor Hyper-V-evaluatie met Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 20bdbb16d2f0610f6519424141b09190eae3cc42
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028784"
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


## <a name="azure-migrate-appliance-requirements"></a>Vereisten van Azure Migrate apparaat

Azure Migrate gebruikt het [Azure migrate-apparaat](migrate-appliance.md) voor detectie en evaluatie. Het apparaat voor Hyper-V wordt uitgevoerd op een Hyper-V-VM en wordt geïmplementeerd met behulp van een gecomprimeerde Hyper-V VHD die u van de Azure Portal downloadt. 

- Meer informatie over de [vereisten voor apparaten](migrate-appliance.md#appliance---hyper-v) voor Hyper-V.
- Meer informatie over de [url's](migrate-appliance.md#url-access) waartoe het apparaat toegang moet hebben.

## <a name="port-access"></a>Poort toegang

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaat** | **Verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Uitgaande verbindingen op de poorten 443, 5671 en 5672 voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Hyper-V-host/cluster** | Binnenkomende verbindingen op WinRM-poorten 5985 (HTTP) en 5986 (HTTPS) voor het ophalen van meta gegevens over de configuratie en prestaties van de virtuele Hyper-V-machines met behulp van een Common Information Model (CIM)-sessie.



## <a name="next-steps"></a>Volgende stappen

[De evaluatie van de Hyper-V-VM voorbereiden](tutorial-prepare-hyper-v.md)
