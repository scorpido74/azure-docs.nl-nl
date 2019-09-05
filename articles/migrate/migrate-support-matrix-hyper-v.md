---
title: Azure Migrate-ondersteunings matrix voor Hyper-V-evaluatie en-migratie
description: Geeft een overzicht van de instellingen en beperkingen voor Hyper-V-evaluatie en migratie met behulp van de Azure Migrate-service.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: raynew
ms.openlocfilehash: 97972be655a6a03cfe29d8589a144d1e027b86fc
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376100"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Ondersteuningsmatrix voor Hyper-V-evaluatie en -migratie

U kunt de [Azure migrate-service](migrate-overview.md) gebruiken voor het beoordelen en migreren van machines naar de Microsoft Azure Cloud. Dit artikel bevat een overzicht van de ondersteunings instellingen en beperkingen voor het beoordelen en migreren van on-premises virtuele Hyper-V-machines.



## <a name="hyper-v-scenarios"></a>Hyper-V-scenario's

De tabel bevat een overzicht van de ondersteunde scenario's voor virtuele Hyper-V-machines.

**Implementatie** | **Details***
--- | ---
**On-premises virtuele Hyper-V-machines evalueren** | [Stel](tutorial-prepare-hyper-v.md) uw eerste beoordeling in.<br/><br/> [Voer](scale-hyper-v-assessment.md) een grootschalige evaluatie uit.
**Hyper-V-VM's migreren naar Azure** | [Probeer](tutorial-migrate-hyper-v.md) de migratie naar Azure uit te proberen. 

Migratie van Hyper-V-servers die met System Center Virtual Machine Manager (VMM) worden beheerd, wordt niet ondersteund door Azure Migrate server migratie. 

## <a name="azure-migrate-projects"></a>Azure Migrate projecten

**Ondersteuning** | **Details**
--- | ---
Azure-machtigingen | U hebt machtigingen voor Inzender of eigenaar nodig in het abonnement om een Azure Migrate project te maken.
Virtuele Hyper-V-machines | Evalueer Maxi maal 35.000 Hyper-V-Vm's in één project. U kunt meerdere projecten in een Azure-abonnement hebben. Een project kan zowel virtuele VMware-machines als virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten.
Geografie | U kunt Azure Migrate projecten maken in een aantal geographs. Hoewel u projecten in specifieke geografische grafieken kunt maken, kunt u machines voor andere doel locaties evalueren of migreren. De Geografie van het project wordt alleen gebruikt om de gedetecteerde meta gegevens op te slaan.

  **Geografie** | **Opslag locatie van meta gegevens**
  --- | ---
  Azure Government | VS (overheid) - Virginia
  Azië en Stille Oceaan | Azië Azië-oost of Zuidoost
  Australië | Australië-oost of Australië-zuidoost
  Canada | Canada-centraal of Canada-oost
  Europa | Europa - noord of Europa - west
  India | Centraal-India of India-zuid
  Japan |  Japan-Oost of Japan-West
  Verenigd Koninkrijk | UK-zuid of UK-west
  Verenigde Staten | VS-midden, VS-West 2


 > [!NOTE]
 > Ondersteuning voor Azure Government is momenteel alleen beschikbaar voor de [oudere versie](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) van Azure Migrate.


## <a name="assessment-hyper-v-host-requirements"></a>Beoordeling-vereisten voor de Hyper-V-host

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie van host**       | De Hyper-V-host kan zelfstandig of in een cluster worden geïmplementeerd. |
| **Machtigingen**           | U hebt beheerders machtigingen nodig op de Hyper-V-host. <br/> Als u geen beheerders machtigingen wilt toewijzen, maakt u een lokaal of domein gebruikers account en voegt u de gebruiker toe aan deze groepen: gebruikers van extern beheer, Hyper-V-Administrators en prestatie meter gebruikers. |
| **Besturings systeem van host** | Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2.<br/> U kunt geen Vm's beoordelen die zich bevinden op Hyper-V-hosts waarop Windows Server 2012 wordt uitgevoerd. |
| **Externe communicatie met Power shell**   | Moet op elke host zijn ingeschakeld. |
| **Hyper-V replica**       | Als u Hyper-V replica gebruikt (of als u meerdere virtuele machines met dezelfde VM-id's hebt), en zowel de oorspronkelijke als de gerepliceerde Vm's detecteert met Azure Migrate, is de door Azure Migrate gegenereerde evaluatie mogelijk niet nauw keurig. |


## <a name="assessment-hyper-v-vm-requirements"></a>Beoordeling-vereisten voor virtuele Hyper-V-machines

| **Ondersteuning**                  | **Details**               
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -besturings systemen die worden ondersteund door Azure. |
| **Integratie Services**       | [Hyper-V-integratie Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) moeten worden uitgevoerd op vm's die u controleert, om informatie over het besturings systeem vast te leggen. |



## <a name="assessment-appliance-requirements"></a>Beoordeling-vereisten voor apparaten

Voor evaluatie voert Azure Migrate een licht gewicht in voor de detectie van Hyper-V-Vm's en het verzenden van meta gegevens en prestatie gegevens van de virtuele machine naar Azure Migrate. Het apparaat wordt uitgevoerd op een Hyper-V-VM en u gebruikt een gecomprimeerde Hyper-V-VHD die u downloadt van de Azure Portal. De volgende tabel bevat een overzicht van de vereisten voor het apparaat.

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie van het apparaat**   |  U implementeert het apparaat als een Hyper-V-VM.<br/> De apparaat-VM van Azure Migrate is Hyper-V VM versie 5,0.<br/> Op de Hyper-V-host moet Windows Server 2012 R2 of later worden uitgevoerd.<br/> De host moet voldoende ruimte hebben om 16 GB RAM-geheugen, 8 Vcpu's, ongeveer 80 GB aan opslag ruimte toe te wijzen en een externe switch voor de apparaat-VM.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en Internet toegang.
| **Azure Migrate project**  |  Een apparaat kan worden gekoppeld aan een enkel project.<br/> Een wille keurig aantal apparaten kan aan één project worden gekoppeld.<br/> U kunt Maxi maal 35.000 Vm's in een project evalueren.
| **Hyper-V-hosts**          | Een apparaat kan verbinding maken met Maxi maal 300 Hyper-V-hosts.
| **Detectie**              | Eén apparaat kan Maxi maal 5000 Vm's detecteren.
| **Beoordelings groep**       | U kunt Maxi maal 35.000 computers in één groep toevoegen.
| **Onderzoek**             | U kunt Maxi maal 35.000 Vm's in één evaluatie evalueren.



## <a name="assessment-appliance-url-access"></a>Beoordeling: toestel-URL-toegang

Voor het beoordelen van Vm's heeft het Azure Migrate-apparaat Internet verbinding nodig.

- Wanneer u het apparaat implementeert, controleert Azure Migrate een connectiviteits controle op de Url's die in de onderstaande tabel worden samenvatten.
- Als u een proxy op basis van een URL gebruikt, verleent u toegang tot de Url's in de tabel en zorgt u ervoor dat de proxy alle CNAME-records verhelpt die zijn ontvangen tijdens het opzoeken van de Url's.
- Als u een onderschepende proxy hebt, moet u mogelijk het server certificaat van de proxy server importeren naar het apparaat.


**URL** | **Details**  
--- | ---
*.portal.azure.com | Navigeren naar de Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Meld u aan bij uw Azure-abonnement
*.microsoftonline.com <br/> *.microsoftonline-p.com | Het maken van Azure Active Directory-toepassingen voor het apparaat aan service communicatie.
management.azure.com | Het maken van Azure Active Directory-toepassingen voor het apparaat aan service communicatie.
dc.services.visualstudio.com | Logboekregistratie en bewaking
*.vault.azure.net | Geheimen in Azure Key Vault beheren bij de communicatie tussen het apparaat en de service.
aka.ms/* | Toegang tot ook wel-koppelingen toestaan.
https://download.microsoft.com/download/* | Down loads van de micro soft-download site toestaan.



## <a name="assessment-port-requirements"></a>Beoordeling-poort vereisten

De volgende tabel bevat een overzicht van de poort vereisten voor evaluatie.

**Apparaatconfiguratie** | **verbinding**
--- | ---
**Apparaat** | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL:``` https://<appliance-ip-or-name>:44368 ```<br/> Uitgaande verbindingen op de poorten 443, 5671 en 5672 voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
**Hyper-V-host/cluster** | Binnenkomende verbindingen op WinRM-poorten 5985 (HTTP) en 5986 (HTTPS) voor het ophalen van meta gegevens over de configuratie en prestaties van de virtuele Hyper-V-machines met behulp van een Common Information Model (CIM)-sessie.

## <a name="migration-limitations"></a>Migratie-beperkingen
U kunt Maxi maal 10 Vm's tegelijk selecteren voor replicatie. Als u meer machines wilt migreren, repliceert u in groepen van 10.

## <a name="migration-hyper-v-host-requirements"></a>Migratie-vereisten voor Hyper-V-host

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie van host**       | De Hyper-V-host kan zelfstandig of in een cluster worden geïmplementeerd. |
| **Machtigingen**           | U hebt beheerders machtigingen nodig op de Hyper-V-host. |
| **Besturings systeem van host** | Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migratie-VM-vereisten voor Hyper-V

| **Ondersteuning**                  | **Details**               
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -besturings systemen die worden ondersteund door Azure. |
| **Machtigingen**           | U hebt beheerders machtigingen nodig op elke Hyper-V-VM die u wilt beoordelen. |
| **Integratie Services**       | [Hyper-V-integratie Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) moeten worden uitgevoerd op vm's die u controleert, om informatie over het besturings systeem vast te leggen. |
| **Vereiste wijzigingen voor Azure** | Voor sommige Vm's zijn mogelijk wijzigingen vereist zodat ze kunnen worden uitgevoerd in Azure. Azure Migrate worden deze wijzigingen automatisch aangebracht voor de volgende besturings systemen:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Voor andere besturings systemen moet u hand matig aanpassingen maken voordat u de migratie uitvoert. De relevante artikelen bevatten instructies over hoe u dit doet. |
| **Linux-opstart**                 | Als/boot zich op een toegewezen partitie bevindt, moet deze zich op de besturingssysteem schijf bevinden en niet over meerdere schijven worden verspreid.<br/> Als/boot deel uitmaakt van de hoofd partitie (/), moet de partitie '/' zich op de besturingssysteem schijf bevindt en niet over andere schijven beschikken. |
| **UEFI-opstart**                  | Vm's met UEFI-opstart bewerkingen worden niet ondersteund voor migratie.  |
| **Schijf grootte**                  | 2 TB voor de besturingssysteem schijf 4 TB voor gegevens schijven.
| **Schijf nummer** | Maxi maal 16 schijven per VM.
| **Versleutelde schijven/volumes**    | Niet ondersteund voor migratie. |
| **RDM/passthrough-schijven**      | Niet ondersteund voor migratie. |
| **Gedeelde schijf** | Vm's die gebruikmaken van gedeelde schijven, worden niet ondersteund voor migratie.
| **NFS**                        | NFS-volumes die zijn gekoppeld als volumes op de Vm's, worden niet gerepliceerd. |
| **SCSI**                      | Vm's met iSCSI-doelen worden niet ondersteund voor migratie.
| **Doel schijf**                | U kunt alleen migreren naar Azure-Vm's met Managed disks. |
| **Ipconfiguration** | Wordt niet ondersteund.
| **NIC-koppeling** | Wordt niet ondersteund.
| **Azure Site Recovery** | U kunt niet repliceren met Azure Migrate server migratie als de virtuele machine is ingeschakeld voor replicatie met Azure Site Recovery.





## <a name="migration-hyper-v-host-url-access"></a>Migratie: Hyper-V-host-URL-toegang

De volgende tabel bevat een overzicht van de vereisten voor URL-toegang voor Hyper-V-hosts.

**URL** | **Details**  
--- | ---
login.microsoftonline.com | Toegangs beheer en identiteits beheer met behulp van Active Directory.
*.backup.windowsazure.com | Replicatie gegevens overdragen en coördinatie.
*.hypervrecoverymanager.windowsazure.com | Verbinding maken met Azure Migrate service-Url's.
*.blob.core.windows.net | Gegevens uploaden naar opslag accounts.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
time.windows.com | Hiermee wordt de tijd synchronisatie tussen systeem en Global time gecontroleerd.

## <a name="migration-port-access"></a>Migratie-poort toegang

De volgende tabel bevat een overzicht van de poort vereisten op Hyper-V-hosts en virtuele machines voor de migratie van Vm's.

**Apparaatconfiguratie** | **verbinding**
--- | ---
Hyper-V-hosts/Vm's | Uitgaande verbindingen op HTTPS-poort 443 voor het verzenden van VM-replicatie gegevens naar Azure Migrate.




## <a name="next-steps"></a>Volgende stappen

[Bereid u voor op de Hyper-V VM-evaluatie](tutorial-prepare-hyper-v.md) voor migratie.
