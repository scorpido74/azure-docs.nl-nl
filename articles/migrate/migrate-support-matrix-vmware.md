---
title: Azure Migrate-ondersteunings matrix voor VMware-evaluatie en-migratie
description: Een overzicht van de ondersteunings instellingen en beperkingen voor de evaluatie en migratie van virtuele VMware-machines naar Azure met behulp van de Azure Migrate service.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: 2a8a19dfd2cdc7a64a5ea90b96808963b19f73bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498642"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Ondersteuningsmatrix voor VMware-evaluatie en -migratie

U kunt [Azure migrate](migrate-overview.md) gebruiken om machines te beoordelen en te migreren naar de Microsoft Azure Cloud. In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het beoordelen en migreren van on-premises VMware-Vm's.


## <a name="vmware-scenarios"></a>VMware-scenario's

De tabel bevat een overzicht van de ondersteunde scenario's voor virtuele VMware-machines.

**Implementatie** | **Details**
--- | ---
**On-premises VMware-Vm's evalueren** | [Stel](tutorial-prepare-vmware.md) uw eerste beoordeling in.<br/><br/> [Voer](scale-vmware-assessment.md) een grootschalige evaluatie uit.
**Virtuele VMware-machines migreren** | U kunt migreren met behulp van agentloze migratie of een migratie op basis van een agent gebruiken. [Meer informatie](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate projecten

**Ondersteuning** | **Details**
--- | ---
**Azure-machtigingen** | U hebt machtigingen voor Inzender of eigenaar nodig in het abonnement om een Azure Migrate project te maken.
**VMware-beperkingen**  | Evalueer Maxi maal 35.000 VMware-Vm's in één project. U kunt meerdere projecten maken in een Azure-abonnement. Een project kan zowel virtuele VMware-machines als virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten.
**Geografie** | Ondersteunde geographs [bekijken](migrate-support-matrix.md#supported-geographies) .

**Geografie** | **Opslag locatie van meta gegevens**
--- | ---
Azure Government | VS (overheid) - Virginia
Azië en Stille Oceaan | Azië Azië-oost of Zuidoost
Australië | Australië-oost of Australië-zuidoost
Brazilië | Brazilië - zuid
Canada | Canada-centraal of Canada-oost
Europa | Europa - noord of Europa - west
Frankrijk | Frankrijk - centraal
India | Centraal-India of India-zuid
Japan |  Japan-Oost of Japan-West
Korea | Korea-centraal of Korea-zuid
Verenigd Koninkrijk | UK-zuid of UK-west
Verenigde Staten | VS-midden, VS-West 2


 > [!NOTE]
 > Ondersteuning voor Azure Government is momenteel alleen beschikbaar voor de [oudere versie](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) van Azure Migrate.


## <a name="application-discovery"></a>Toepassings detectie

Azure Migrate: Server beoordeling kan apps, functies en onderdelen ontdekken. Als u uw app-inventaris detecteert, kunt u een migratie traject identificeren en plannen dat is afgestemd op uw on-premises workloads. Azure Migrate: Server evaluatie biedt detectie zonder agent, met behulp van gast referenties voor computers, en heeft op afstand toegang tot computers met WMI-en SSH-aanroepen.

**Ondersteuning** | **Details**
--- | ---
Ondersteunde computers | On-premises VMware VM's
Machine besturings systeem | Alle Windows-en Linux-versies
Referenties | Biedt momenteel ondersteuning voor het gebruik van één referentie voor alle Windows-servers en één referentie voor alle Linux-servers. U maakt een gast gebruikers account voor Windows-Vm's en een standaard/normaal gebruikers account (niet-sudo toegang) voor alle virtuele Linux-machines.
Computer limieten voor app-detectie | 10000 per apparaat. 35000 per project

## <a name="assessment-vcenter-server-requirements"></a>Beoordeling-vCenter Server vereisten

Deze tabel geeft een overzicht van de evaluatie-ondersteuning en beperkingen voor VMware Virtualization-servers.

**Ondersteuning** | **Details**
--- | ---
**vCenter-server** | VMware-Vm's die u wilt beoordelen, moeten worden beheerd door een of meer vCenter-servers met 5,5, 6,0, 6,5 of 6,7.

## <a name="assessment-vcenter-server-permissions"></a>Beoordeling-vCenter Server machtigingen

Voor evaluatie hebt u een alleen-lezen-account nodig voor de vCenter Server.

## <a name="assessment-appliance-requirements"></a>Beoordeling-vereisten voor apparaten

Azure Migrate voert een licht gewicht apparaat uit om virtuele VMware-machines te detecteren en de meta gegevens en prestatie gegevens van de VM naar Azure Migrate te verzenden. Het apparaat voor VMware wordt geïmplementeerd met behulp van een eicellen-sjabloon die in vCenter Server is geïmporteerd. De volgende tabel bevat een overzicht van de vereisten voor het apparaat.

**Ondersteuning** | **Details**
--- | ---
**Implementatie van het apparaat** | U implementeert het apparaat als een virtuele VMware-machine. U hebt voldoende resources op de vCenter Server nodig om een virtuele machine toe te wijzen met 32 GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch.<br/><br/> Voor het apparaat is toegang tot internet vereist, hetzij rechtstreeks hetzij via een proxy.<br/> De toestel-VM moet worden geïmplementeerd op een ESXi-host waarop versie 5,5 of hoger wordt uitgevoerd.
**Azure Migrate project** | Een apparaat kan worden gekoppeld aan een enkel project. <br/> Een wille keurig aantal apparaten kan aan één project worden gekoppeld.<br/> U kunt Maxi maal 35.000 Vm's in een project evalueren.
**Detectie** | Een apparaat kan tot 10.000 VMware-Vm's detecteren op een vCenter Server.<br/> Een apparaat kan verbinding maken met één vCenter Server.
**Beoordelings groep** | U kunt Maxi maal 35.000 computers in één groep toevoegen.
**Evaluatie** | U kunt Maxi maal 35.000 Vm's in één evaluatie evalueren.


## <a name="assessment-url-access-requirements"></a>Beoordeling-vereisten voor URL-toegang

Het Azure Migrate-apparaat heeft verbinding met internet nodig.

- Wanneer u het apparaat implementeert, controleert Azure Migrate een connectiviteits controle op de Url's die in de onderstaande tabel worden samenvatten.
- Als u een op URL gebaseerde proxy gebruikt om verbinding te maken met internet, toegang tot deze Url's toestaan, zodat de proxy alle CNAME-records verhelpt die zijn ontvangen tijdens het opzoeken van de Url's.

**URL** | **Details**  
--- | --- |
*.portal.azure.com  | Ga naar de Azure Migrate in het Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com <br/> *. microsoftonline-p.com | Maak Active Directory apps voor het apparaat om te communiceren met de Azure Migrate-service.
management.azure.com | Maak Active Directory apps voor het apparaat om te communiceren met de Azure Migrate-service.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
*.vault.azure.net | Geheimen beheren in de Azure Key Vault.
*.servicebus.windows.net | Communicatie tussen het apparaat en de Azure Migrate service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Verbinding maken met Azure Migrate service-Url's.
*.blob.core.windows.net | Gegevens uploaden naar opslag accounts.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Wordt gebruikt voor Azure Migrate apparaat-updates.

## <a name="assessment-port-requirements"></a>Beoordeling-poort vereisten

**Apparaatconfiguratie** | **Verbinding**
--- | ---
Apparaat | Binnenkomende verbindingen op TCP-poort 3389 om extern bureau blad-verbindingen met het apparaat toe te staan.<br/><br/> Binnenkomende verbindingen op poort 44368 voor externe toegang tot de app voor het beheren van apparaten met behulp van de URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Uitgaande verbindingen op poort 443, 5671 en 5672 voor het verzenden van meta gegevens voor detectie en prestaties naar Azure Migrate.
vCenter-server | Binnenkomende verbindingen op TCP-poort 443 zodat het apparaat configuratie-en prestatie-meta gegevens voor evaluaties kan verzamelen. <br/><br/> Het apparaat maakt standaard verbinding met vCenter op poort 443. Als de vCenter-Server op een andere poort luistert, kunt u de poort wijzigen bij het instellen van detectie.

## <a name="assessment-dependency-visualization"></a>Beoordeling-afhankelijkheids visualisatie

Met de visualisatie van afhankelijkheden kunt u afhankelijkheden visualiseren tussen computers die u wilt beoordelen en migreren. U gebruikt meestal afhankelijkheids toewijzing als u computers wilt beoordelen met een hoger vertrouwens niveau. Voor virtuele VMware-machines wordt de visualisatie van afhankelijkheden als volgt ondersteund:

- **Visualisatie van afhankelijkheid zonder agent**: deze optie is momenteel beschikbaar als preview-versie. U hoeft geen agents op computers te installeren.
    - Het werkt door de TCP-verbindings gegevens vast te leggen van de computers waarvoor deze is ingeschakeld. Nadat de detectie van afhankelijkheden is gestart, verzamelt het apparaat gegevens van machines met een polling-interval van vijf minuten.
    - De volgende gegevens worden verzameld:
        - TCP-verbindingen
        - Namen van processen die actieve verbindingen hebben
        - Namen van geïnstalleerde toepassingen die de bovenstaande processen uitvoeren
        - Nee. van verbindingen die zijn gedetecteerd bij elk polling-interval
- **Visualisatie van afhankelijkheid op basis van een agent**: voor het gebruik van op agents gebaseerde afhankelijkheids visualisatie moet u de volgende agents downloaden en installeren op elke on-premises computer die u wilt analyseren.
    - Micro soft Monitoring Agent (MMA) moet op elke computer worden geïnstalleerd. Meer [informatie](how-to-create-group-machine-dependencies.md#install-the-mma) over het installeren van de MMA-agent.
    - De afhankelijkheids agent moet op elke computer worden geïnstalleerd. Meer [informatie](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) over het installeren van de afhankelijkheids agent.
    - En als u VM's zonder internetverbinding hebt, moet u op deze machines Log Analytics-gateway downloaden en installeren.

## <a name="migration---limitations"></a>Migratie-beperkingen
U kunt Maxi maal 10 Vm's tegelijk selecteren voor replicatie. Als u meer machines wilt migreren, repliceert u in groepen van 10. Voor VMware-agentloze migratie kunt u Maxi maal 100 replicaties tegelijk uitvoeren.

## <a name="agentless-migration-vmware-server-requirements"></a>Migratie zonder agent-vereisten voor VMware-Server

Deze tabel geeft een overzicht van de evaluatie-ondersteuning en beperkingen voor VMware Virtualization-servers.

**Ondersteuning** | **Details**
--- | ---
vCenter Server | Versie 5,5, 6,0, 6,5 of 6,7.
VMware vSphere | Versie 5,5, 6,0, 6,5 of 6,7,

## <a name="agentless-migration-vcenter-server-permissions"></a>Migratie zonder agent-vCenter Server machtigingen

**Machtigingen** | **Details**
--- | ---
Datastore.Browse | Bladeren door VM-logboek bestanden toestaan om problemen met het maken en verwijderen van moment opnamen op te lossen.
Datastore.LowLevelFileOperations | Bewerkingen voor lezen/schrijven/verwijderen/naam wijzigen in de Data Store-browser toestaan om problemen met het maken en verwijderen van moment opnamen op te lossen.
VirtualMachine.Configuration.DiskChangeTracking | Het bijhouden van wijzigingen van VM-schijven in-of uitschakelen toestaan om gewijzigde gegevens blokken tussen moment opnamen op te halen
VirtualMachine.Configuration.DiskLease | Sta bewerkingen voor de lease van de schijf voor een virtuele machine toe om de schijf te lezen met behulp van de VMware vSphere Virtual Disk Development Kit (VDDK).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Het openen van een schijf op een virtuele machine toestaan om de schijf te lezen met behulp van de VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Hiermee staat u lees bewerkingen toe voor bestanden die zijn gekoppeld aan een virtuele machine, om de logboeken te downloaden en problemen op te lossen.
VirtualMachine.SnapshotManagement.* | Maken en beheren van VM-moment opnamen voor replicatie toestaan.
Virtuele machine. interactie. uit | Toestaan dat de VM wordt uitgeschakeld tijdens de migratie naar Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Migratie zonder agent-vereisten voor VMware-VM'S

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde besturingssystemen** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -besturings systemen die worden ondersteund door Azure, kunnen worden gemigreerd met migratie zonder agent.
**Vereiste wijzigingen voor Azure** | Voor sommige Vm's zijn mogelijk wijzigingen vereist zodat ze kunnen worden uitgevoerd in Azure. Azure Migrate worden deze wijzigingen automatisch aangebracht voor de volgende besturings systemen:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Voor andere besturings systemen moet u hand matig aanpassingen maken voordat u de migratie uitvoert. De relevante artikelen bevatten instructies over hoe u dit doet.
**Linux-opstart** | Als/boot zich op een toegewezen partitie bevindt, moet deze zich op de besturingssysteem schijf bevinden en niet over meerdere schijven worden verspreid.<br/> Als/boot deel uitmaakt van de hoofd partitie (/), moet de partitie '/' zich op de besturingssysteem schijf bevindt en niet over andere schijven beschikken.
**UEFI-opstart** | Vm's met UEFI-opstart bewerkingen worden niet ondersteund voor migratie.
**Schijf grootte** | 2 TB besturingssysteem schijf; 4 TB voor gegevens schijven.
**Schijf limieten** |  Maxi maal 60 schijven per VM.
**Versleutelde schijven/volumes** | Vm's met versleutelde schijven/volumes worden niet ondersteund voor migratie.
**Gedeeld schijf cluster** | Wordt niet ondersteund.
**Onafhankelijke schijven** | Wordt niet ondersteund.
**RDM/passthrough-schijven** | Als Vm's een RDM-of passthrough-schijf hebben, worden deze schijven niet gerepliceerd naar Azure.
**NBS** | NFS-volumes die zijn gekoppeld als volumes op de Vm's, worden niet gerepliceerd.
**iSCSI-doelen** | Vm's met iSCSI-doelen worden niet ondersteund voor migratie zonder agent.
**Multipath IO** | Wordt niet ondersteund.
**Storage vMotion** | Wordt niet ondersteund. De replicatie wordt niet uitgevoerd als een virtuele machine gebruikmaakt van Storage vMotion.
**Team kaarten** | Wordt niet ondersteund.
**Ipconfiguration** | Wordt niet ondersteund.
**Doel schijf** | Vm's kunnen alleen worden gemigreerd naar Managed disks (standaard HDD, Premium SSD) in Azure.
**Gelijktijdige replicatie** | 100 Vm's per vCenter Server. Als u meer hebt, migreert u deze in batches van 100.


## <a name="agentless-migration-appliance-requirements"></a>Migratie zonder agent-vereisten voor apparaat


**Ondersteuning** | **Details**
--- | ---
**ESXi** | De toestel-VM moet worden geïmplementeerd op een ESXi-host waarop versie 5,5 of hoger wordt uitgevoerd.
**Azure Migrate project** | Een apparaat kan worden gekoppeld aan een enkel project.
**vCenter Server** | Een apparaat kan tot 10.000 VMware-Vm's detecteren op een vCenter Server.<br/> Een apparaat kan verbinding maken met één vCenter Server.
**VDDK** | Als u een migratie zonder agent uitvoert met Azure Migrate server migratie, moet de VMware vSphere VDDK zijn geïnstalleerd op de apparaat-VM.

## <a name="agentless-migration-url-access-requirements"></a>Migratie van zonder agents-URL-toegang

Het Azure Migrate-apparaat heeft Internet verbinding met internet nodig.

- Wanneer u het apparaat implementeert, controleert Azure Migrate een connectiviteits controle op de Url's die in de onderstaande tabel worden samenvatten.
- Als u een proxy op basis van een URL gebruikt, verleent u toegang tot deze Url's, zodat de proxy alle CNAME-records kan omzetten die zijn ontvangen tijdens het opzoeken van de Url's.

**URL** | **Details**  
--- | ---
*.portal.azure.com | Ga naar de Azure Migrate in het Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com <br/> *. microsoftonline-p.com | Maak Active Directory apps voor het apparaat om te communiceren met de Azure Migrate-service.
management.azure.com | Maak Active Directory apps voor het apparaat om te communiceren met de Azure Migrate-service.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
*.vault.azure.net | Geheimen beheren in de Azure Key Vault.
*.servicebus.windows.net | Communicatie tussen het apparaat en de Azure Migrate service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Verbinding maken met Azure Migrate service-Url's.
*.blob.core.windows.net | Gegevens uploaden naar opslag accounts.
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Wordt gebruikt voor Azure Migrate apparaat-updates.


## <a name="agentless-migration-port-requirements"></a>Migratie zonder agent-poort vereisten

**Apparaatconfiguratie** | **Verbinding**
--- | ---
Apparaat | Uitgaande verbindingen op poort 443 voor het uploaden van gerepliceerde gegevens naar Azure en om te communiceren met Azure Migrate services die replicatie en migratie coördineren.
vCenter-server | Binnenkomende verbindingen op poort 443 om het apparaat in staat te stellen de replicatie te organiseren-moment opnamen maken, gegevens kopiëren, moment opnamen van de release
vSphere/EXSI-host | Inkomend op TCP-poort 902 voor het apparaat om gegevens van moment opnamen te repliceren.


## <a name="agent-based-migration-vmware-server-requirements"></a>Migratie op basis van een agent-vereisten voor VMware-Server

Deze tabel geeft een overzicht van de evaluatie-ondersteuning en beperkingen voor VMware Virtualization-servers.

**Ondersteuning** | **Details**
--- | ---
vCenter Server | Versie 5,5, 6,0, 6,5 of 6,7.
VMware vSphere | Versie 5,5, 6,0, 6,5 of 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Migratie op basis van agents-vCenter Server machtigingen

Een alleen-lezen account voor vCenter Server.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Migratie op basis van een agent-vereisten voor replicatie-apparaat

De vereisten voor het [replicatie apparaat](migrate-replication-appliance.md) dat wordt gebruikt voor de migratie van virtuele VMware-machines en fysieke servers met Azure migrate server, worden in de tabel samenvatten.

> [!NOTE]
> Wanneer u het replicatie apparaat instelt met behulp van de eicellen-sjabloon die is opgegeven in de Azure Migrate hub, voert het apparaat Windows Server 2016 uit en voldoet aan de ondersteunings vereisten. Als u het replicatie apparaat hand matig instelt op een fysieke server, moet u ervoor zorgen dat het voldoet aan de vereisten.



**Onderdeel** | **Vereiste**
--- | ---
 | **VMware-instellingen** (VMware VM-apparaat)
PowerCLI | [PowerCLI-versie 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) moet worden geïnstalleerd als het replicatie apparaat wordt uitgevoerd op een virtuele VMWare-machine.
NIC-type | VMXNET3 (als het apparaat een VMware-VM is)
 | **Hardware-instellingen**
CPU-kernen | 8
RAM | 16 GB
Aantal schijven | Drie: de besturingssysteem schijf, de cache schijf van de proces server en het Bewaar volume.
Vrije schijf ruimte (cache) | 600 GB
Vrije schijf ruimte (Bewaar schijf) | 600 GB
**Software-instellingen** |
Besturingssysteem | Windows Server 2016 of Windows Server 2012 R2
Landinstelling van het besturingssysteem | Engels (en-us)
TLS | TLS 1,2 moet zijn ingeschakeld.
.NET Framework | .NET Framework 4,6 of hoger moet zijn geïnstalleerd op de computer (waarbij sterke crypto grafie is ingeschakeld.
MySQL | MySQL moet op het apparaat worden geïnstalleerd.<br/> MySQL moet worden geïnstalleerd. U kunt hand matig installeren of Site Recovery kunt dit installeren tijdens de implementatie van het apparaat.
Andere apps | Voer geen andere apps uit op het replicatie apparaat.
Windows Server-functies | Deze rollen niet inschakelen: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Groeps beleid | Dit groeps beleid niet inschakelen: <br> -Toegang tot de opdracht prompt voor komen. <br> -Toegang tot register bewerkings Programma's verhinderen. <br> -Logica vertrouwen voor bestands bijlagen. <br> -Schakel uitvoering van script in. <br> [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Geen vooraf bestaande standaard website <br> -Geen vooraf bestaande website/toepassing die luistert op poort 443 <br>- [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) inschakelen <br> - [Fastcgi](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -instelling inschakelen
**Netwerk instellingen** |
Type IP-adres | Statisch
Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport)
NIC-type | VMXNET3

### <a name="replication-appliance-url-access"></a>URL-toegang voor replicatie apparaat

Het replicatie apparaat moet toegang hebben tot deze Url's.

**URL** | **Details**
--- | ---
\*.backup.windowsazure.com | Gebruikt voor gerepliceerde gegevens overdracht en coördinatie
\*.store.core.windows.net | Gebruikt voor gerepliceerde gegevens overdracht en coördinatie
\*.blob.core.windows.net | Wordt gebruikt voor toegang tot het opslag account waarin gerepliceerde gegevens worden opgeslagen
\*.hypervrecoverymanager.windowsazure.com | Gebruikt voor replicatie beheer bewerkingen en coördinatie
https:\//management.azure.com | Gebruikt voor replicatie beheer bewerkingen en coördinatie
*.services.visualstudio.com | Gebruikt voor telemetrische doel einden (deze is optioneel)
time.nist.gov | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren.
time.windows.com | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\/-login.live.com <br/> https:\/-graph.windows.net <br/> https:\//login.windows.net <br/> https:\/-www.live.com <br/> https:\/-www.microsoft.com  | OVF Setup heeft toegang tot deze Url's nodig. Ze worden gebruikt voor toegangs beheer en identiteits beheer door Azure Active Directory
https:\/-dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | De MySQL-down load volt ooien


#### <a name="mysql-installation-options"></a>Installatie opties voor MySQL

MySQL kan worden geïnstalleerd op het replicatie apparaat met een van deze methoden.

**Methode** | **Details**
--- | ---
Hand matig downloaden en installeren | Down load MySQL-toepassing & plaats deze in de map C:\Temp\ASRSetup en installeer hand matig.<br/> Bij het instellen van het apparaat MySQL wordt weer gegeven dat het al is geïnstalleerd.
Zonder online downloaden | Plaats de MySQL-installatie toepassing in de map C:\Temp\ASRSetup. Wanneer u het apparaat installeert en klikt om MySQL te downloaden en te installeren, wordt het installatie programma gebruikt dat u hebt toegevoegd.
Downloaden en installeren in Azure Migrate | Wanneer u het apparaat installeert en wordt gevraagd naar MySQL, selecteert u **downloaden en installeren**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Migratie op basis van een agent-vereisten voor VMware-VM'S

**Ondersteuning** | **Details**
--- | ---
**Machine workload** | Azure Migrate ondersteunt de migratie van elke werk belasting (bijvoorbeeld Active Directory, SQL Server, enzovoort) die wordt uitgevoerd op een ondersteunde computer.
**Besturings systemen** | Raadpleeg de ondersteuning van het [besturings systeem](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) voor site Recovery voor de meest recente informatie. Azure Migrate biedt dezelfde ondersteuning voor het VM-besturings systeem.
**Linux-bestands systeem/gast opslag** | Raadpleeg de ondersteuning van het Linux- [Bestands systeem](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) voor site Recovery voor de meest recente informatie. Azure Migrate heeft dezelfde ondersteuning voor het Linux-bestands systeem.
**Netwerk/opslag** | Raadpleeg de vereisten voor het [netwerk](../site-recovery/vmware-physical-azure-support-matrix.md#network) en de [opslag](../site-recovery/vmware-physical-azure-support-matrix.md#storage) voor site Recovery voor de meest recente informatie. Azure Migrate biedt identieke netwerk-en opslag vereisten.
**Azure-vereisten** | Bekijk de vereisten voor het Azure- [netwerk](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), de [opslag](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)en de [reken kracht](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) voor site Recovery voor de meest recente informatie. Azure Migrate heeft identieke vereisten voor VMware-migratie.
**Mobility-service** | De Mobility Service-agent moet zijn geïnstalleerd op elke virtuele machine die u wilt migreren.
**UEFI-opstart** | De gemigreerde VM in azure wordt automatisch geconverteerd naar een BIOS-opstart-VM.<br/><br/> De besturingssysteem schijf moet Maxi maal vier partities hebben en volumes moeten worden geformatteerd met NTFS.
**Doel schijf** | Vm's kunnen alleen worden gemigreerd naar Managed disks (standaard HDD, Premium SSD) in Azure.
**Schijf grootte** | 2 TB besturingssysteem schijf; 8 TB voor gegevens schijven.
**Schijf limieten** |  Maxi maal 63 schijven per VM.
**Versleutelde schijven/volumes** | Vm's met versleutelde schijven/volumes worden niet ondersteund voor migratie.
**Gedeeld schijf cluster** | Wordt niet ondersteund.
**Onafhankelijke schijven** | Ondersteund.
**Passthrough-schijven** | Ondersteund.
**NBS** | NFS-volumes die zijn gekoppeld als volumes op de Vm's, worden niet gerepliceerd.
iSCSI-doelen | Vm's met iSCSI-doelen worden niet ondersteund voor migratie zonder agent.
**Multipath IO** | Wordt niet ondersteund.
**Storage vMotion** | Ondersteund
**Team kaarten** | Wordt niet ondersteund.
**Ipconfiguration** | Wordt niet ondersteund.




## <a name="agent-based-migration-url-access-requirements"></a>Migratie op basis van een agent-vereisten voor URL-toegang

De Mobility-service die wordt uitgevoerd op VMware-Vm's, heeft Internet verbinding nodig.

Wanneer u de Mobility-service implementeert, wordt er een connectiviteits controle voor de Url's in de onderstaande tabel beschreven.


**URL** | **Details**  
--- | ---
*.portal.azure.com | Ga naar de Azure Migrate in het Azure Portal.
*.windows.net | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com | Maak Active Directory apps voor het apparaat om te communiceren met de Azure Migrate-service.
management.azure.com | Maak Active Directory apps voor het apparaat om te communiceren met de Azure Migrate-service.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
*.vault.azure.net | Geheimen beheren in de Azure Key Vault.
*.servicebus.windows.net | Communicatie tussen het apparaat en de Azure Migrate service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Verbinding maken met Azure Migrate service-Url's.
*.blob.core.windows.net | Gegevens uploaden naar opslag accounts.

## <a name="agent-based-migration-port-requirements"></a>Migratie op basis van een agent-poort vereisten

**Apparaatconfiguratie** | **Verbinding**
--- | ---
VM's | De Mobility-service die wordt uitgevoerd op Vm's communiceert met het on-premises replicatie apparaat (configuratie server) op poort HTTPS 443 inkomend voor replicatie beheer.<br/><br/> Vm's verzenden replicatie gegevens naar de proces server (die wordt uitgevoerd op de computer van de configuratie server) op poort HTTPS 9443-binnenkomend. Deze poort kan worden gewijzigd.
Replicatie apparaat | Het replicatie apparaat organiseert de replicatie met Azure via poort HTTPS 443 uitgaand.
Proces server | De proces server ontvangt replicatie gegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via poort 443 uitgaand.<br/> Standaard wordt de proces server uitgevoerd op het replicatie apparaat.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure VM

Alle on-premises Vm's die naar Azure worden gerepliceerd, moeten voldoen aan de vereisten voor de Azure-VM die in deze tabel worden samenvatten. Wanneer Site Recovery een controle op vereisten uitvoert voor replicatie, mislukt de controle als aan sommige vereisten niet wordt voldaan.

**Onderdeel** | **Vereisten** | **Details**
--- | --- | ---
Gast besturingssysteem | Controleer de ondersteunde besturings systemen voor [VMware-vm's met replicatie zonder agent](#agentless-migration-vmware-vm-requirements)en voor [virtuele VMware-machines met replicatie op basis](#agent-based-migration-vmware-vm-requirements)van een agent.<br/> U kunt elke werk belasting migreren die wordt uitgevoerd op een ondersteund besturings systeem. | De controle is mislukt als dit niet wordt ondersteund.
Architectuur van gast besturingssysteem | 64-bits. | De controle is mislukt als dit niet wordt ondersteund.
Schijf grootte van het besturings systeem | Maxi maal 2.048 GB. | De controle is mislukt als dit niet wordt ondersteund.
Aantal besturingssysteem schijven | 1 | De controle is mislukt als dit niet wordt ondersteund.
Aantal gegevens schijven | 64 of minder. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de gegevens schijf | Maxi maal 4.095 GB | De controle is mislukt als dit niet wordt ondersteund.
Netwerk adapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Wordt niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
FC-schijf | Wordt niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
BitLocker | Wordt niet ondersteund. | BitLocker moet worden uitgeschakeld voordat u replicatie voor een machine inschakelt.
VM-naam | Van 1 tot 63 tekens.<br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de computer moet beginnen en eindigen met een letter of cijfer. |  Werk de waarde in de computer eigenschappen in Site Recovery bij.
Verbinding maken na migratie-Windows | Verbinding maken met virtuele Azure-machines na de migratie:<br/> -Voordat de migratie RDP op de on-premises VM maakt. Zorg dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.<br/> Schakel voor site-naar-site-VPN-toegang RDP in en sta RDP toe in **Windows Firewall** -> **toegestane apps en functies** voor **domein en particuliere** netwerken. Controleer bovendien of het SAN-beleid van het besturings systeem is ingesteld op **OnlineAll**. [Meer informatie](prepare-for-migration.md). |
Verbinding maken na migratie-Linux | Verbinding maken met virtuele Azure-machines na de migratie met SSH:<br/> Controleer voordat u de migratie op de on-premises computer controleert of de Secure shell-service is ingesteld op Start en of de firewall regels een SSH-verbinding toestaan.<br/> Nadat de failover is uitgevoerd op de Azure-VM, moet u binnenkomende verbindingen met de SSH-poort toestaan voor de regels voor de netwerk beveiligings groep op de virtuele machine die is mislukt en voor het Azure-subnet waarmee deze is verbonden. Voeg bovendien een openbaar IP-adres voor de virtuele machine toe. |  


## <a name="next-steps"></a>Volgende stappen

[Voor bereiding voor VMware](tutorial-prepare-vmware.md) -evaluatie en-migratie.
