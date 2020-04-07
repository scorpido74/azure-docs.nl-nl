---
title: Ondersteuningsmatrix voor Azure VM-noodherstel met Azure Site Recovery
description: Hiermee wordt de ondersteuning voor Azure VM's disaster recovery samengevat naar een secundaire regio met Azure Site Recovery.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: f61f32ddc0a1cc6575907bc72522228b77552947
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673807"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Ondersteuningsmatrix voor Azure VM-noodherstel tussen Azure-regio's

In dit artikel worden ondersteuning en vereisten voor noodherstel van Azure VM's van de ene Azure-regio naar de andere samengevat, met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md)


## <a name="deployment-method-support"></a>Ondersteuning voor implementatiemethoden

**Implementatie** |  **Ondersteuning**
--- | ---
**Azure Portal** | Ondersteund.
**PowerShell** | Ondersteund. [Meer informatie](azure-to-azure-powershell.md)
**REST-API** | Ondersteund.
**CLI** | Momenteel niet ondersteund


## <a name="resource-support"></a>Resource-ondersteuning

**Resourceactie** | **Details**
--- | ---
**Kluizen verplaatsen tussen resourcegroepen** | Niet ondersteund
**Compute/storage/netwerkbronnen verplaatsen naar resourcegroepen** | Wordt niet ondersteund.<br/><br/> Als u een vm of bijbehorende onderdelen zoals opslag/netwerk verplaatst nadat de VM is gerepliceerd, moet u replicatie voor de virtuele machine uitschakelen en vervolgens opnieuw inschakelen.
**Azure VM's repliceren van het ene abonnement naar het andere voor herstel na noodgevallen** | Ondersteund binnen dezelfde Azure Active Directory-tenant.
**VM's migreren over regio's binnen ondersteunde geografische clusters (binnen en tussen abonnementen)** | Ondersteund binnen dezelfde Azure Active Directory-tenant.
**VM's migreren binnen dezelfde regio** | Wordt niet ondersteund.

## <a name="region-support"></a>Ondersteuning voor regio

U VM's repliceren en herstellen tussen twee regio's binnen hetzelfde geografische cluster. Geografische clusters worden gedefinieerd met het oog op gegevenslatentie en soevereiniteit.


**Geografisch cluster** | **Azure-regio's**
-- | --
Amerika | Canada East, Canada Central, South Central US, West Central US, East US, East US 2, West US, West US 2, Central US, North Central US
Europa | Uk West, UK South, Noord-Europa, West-Europa, Zuid-Afrika West, Zuid-Afrika Noord, Noorwegen Oost, Noorwegen West
Azië | Zuid-India, Centraal-India, West-India, Zuidoost-Azië, Oost-Azië, Japan Oost,Japan West, Korea Centraal, Korea Zuid
Australië    | Australië Oost, Australië Zuidoost, Australië Centraal, Australië Centraal 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Duitsland    | Duitsland Centraal, Duitsland Noordoost
China | China East, China North, China North2, China East2
Beperkte regio's gereserveerd voor herstel na rampen in het land |Duitsland Noord gereserveerd voor Duitsland West Central, Zwitserland West gereserveerd voor Zwitserland Noord, Frankrijk Zuid gereserveerd voor Frankrijk Centraal, VAE Centraal beperkt voor VAE Noord klanten

>[!NOTE]
>
> - Voor **Brazilië Zuid**, u repliceren en mislukken naar deze regio's: South Central US, West Central US, East US, East US 2, West US, West US 2, en North Central US.
> - Brazilië Zuid kan alleen worden gebruikt als een brongebied van waaruit VM's kunnen repliceren met behulp van Site Recovery. Het kan niet fungeren als een doelregio. Dit komt door latentieproblemen als gevolg van geografische afstanden. Houd er rekening mee dat als u niet overvan Brazilië Zuid als een bron regio naar een doel, failback naar Brazilië Zuid van de doelregio wordt ondersteund.
> - U werken binnen regio's waar u de juiste toegang toe hebt.
> - Als het gebied waarin u een kluis wilt maken, niet wordt weergegeven, controleert u of uw abonnement toegang heeft tot het maken van bronnen in die regio.
> - Als u een gebied in een geografisch cluster niet zien wanneer u replicatie inschakelt, controleert u of uw abonnement machtigingen heeft om VM's in dat gebied te maken.



## <a name="cache-storage"></a>Cacheopslag

In deze tabel wordt een overzicht van de ondersteuning voor het cacheopslagaccount dat door Site Recovery wordt gebruikt tijdens de replicatie.

**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
V2-opslagaccounts voor algemeen gebruik (hot- en cool-laag) | Ondersteund | Het gebruik van GPv2 wordt afgeraden omdat de transactiekosten voor V2 aanzienlijk hoger zijn dan V1-opslagaccounts.
Premium Storage | Niet ondersteund | Standaardopslagaccounts worden gebruikt voor cacheopslag om de kosten te optimaliseren.
Azure Storage-firewalls voor virtuele netwerken  | Ondersteund | Als u een cacheopslagaccount of doelopslagaccount met firewallgebruikt, controleert u of u ['Vertrouwde Microsoft-services toestaan'.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)<br></br>Zorg er ook voor dat u toegang geeft tot ten minste één subnet van bron Vnet.


## <a name="replicated-machine-operating-systems"></a>Gerepliceerde machinebesturingssystemen

Siteherstel ondersteunt replicatie van Azure VM's waarop de besturingssystemen in deze sectie worden uitgevoerd. Houd er rekening mee dat als een reeds replicerende machine vervolgens wordt geüpgraded (of gedegradeerd) naar een andere hoofdkernel, u replicatie moet uitschakelen en replicatie opnieuw moet inschakelen na de upgrade.

### <a name="windows"></a>Windows


**Besturingssysteem** | **Details**
--- | ---
Windows Server 2019 | Ondersteuning voor Server Core, Server met desktopervaring.
Windows Server 2016  | Ondersteunde servercore, server met bureaubladervaring.
Windows Server 2012 R2 | Ondersteund.
Windows Server 2012 | Ondersteund.
Windows Server 2008 R2 met SP1/SP2 | Ondersteund.<br/><br/> Vanaf versie [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility-serviceextensie voor Azure VM's moet u een Windows [Servicing Stack-update (SSU)](https://support.microsoft.com/help/4490628) en [SHA-2-update](https://support.microsoft.com/help/4474419) installeren op machines met Windows Server 2008 R2 SP1/SP2.  SHA-1 wordt niet meer ondersteund vanaf september 2019 en als sha-2-codeondertekening niet is ingeschakeld, wordt de agentextensie niet geïnstalleerd/geüupgradet zoals verwacht. Meer informatie over [sha-2-upgrade en vereisten.](https://aka.ms/SHA-2KB)
Windows 10 (x64) | Ondersteund.
Windows 8.1 (x64) | Ondersteund.
Windows 8 (x64) | Ondersteund.
Windows 7 (x64) met SP1 verder | Vanaf versie [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility-serviceextensie voor Azure VM's moet u een [Windows-update voor servicestack (SSU)](https://support.microsoft.com/help/4490628) en [SHA-2-update](https://support.microsoft.com/help/4474419) installeren op machines met Windows 7 met SP1.  SHA-1 wordt niet meer ondersteund vanaf september 2019 en als sha-2-codeondertekening niet is ingeschakeld, wordt de agentextensie niet geïnstalleerd/geüupgradet zoals verwacht.. Meer informatie over [sha-2-upgrade en vereisten.](https://aka.ms/SHA-2KB)



#### <a name="linux"></a>Linux

**Besturingssysteem** | **Details**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS-server | [Ondersteunde kernelversies](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS-server | [Ondersteunde kernelversie](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servers die verificatie op basis van wachtwoorden gebruiken en zich aanmelden, en het cloud-init-pakket om cloud-VM's te configureren, hebben mogelijk op wachtwoorden gebaseerde aanmelding uitgeschakeld bij failover (afhankelijk van de cloudinit-configuratie). Aanmelden op basis van een wachtwoord kan opnieuw worden ingeschakeld op de virtuele machine door het wachtwoord opnieuw in te stellen vanuit het menu Ondersteuning > probleemoplossing > Instellingen (van de mislukte vm in de Azure-portal).
Ubuntu 18.04 LTS-server | [Ondersteunde kernelversie](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Ondersteunde kernelversies](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Ondersteunde kernelversies](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Ondersteunde kernelversies)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 en 15 SP1. [(Ondersteunde kernelversies)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Upgrade van replicerende machines van SP3 naar SP4 wordt niet ondersteund. Als een gerepliceerde machine is bijgewerkt, moet u replicatie uitschakelen en replicatie na de upgrade opnieuw inschakelen.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Uitvoeren van de Red Hat compatibele kernel of Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde Ubuntu-kernelversies voor virtuele Azure-machines

**Release** | **Mobility service versie** | **Kernelversie** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-generiek tot 3.13.0-170-generiek,<br/>3.16.0-25-generiek tot 3.16.0-77-generiek,<br/>3.19.0-18-generiek tot 3.19.0-80-generiek,<br/>4.2.0-18-generiek tot 4.2.0-42-generiek,<br/>4.4.0-21-generiek tot 4.4.0-148-generiek,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-generiek tot 3.13.0-170-generiek,<br/>3.16.0-25-generiek tot 3.16.0-77-generiek,<br/>3.19.0-18-generiek tot 3.19.0-80-generiek,<br/>4.2.0-18-generiek tot 4.2.0-42-generiek,<br/>4.4.0-21-generiek tot 4.4.0-148-generiek,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-generiek tot 3.13.0-170-generiek,<br/>3.16.0-25-generiek tot 3.16.0-77-generiek,<br/>3.19.0-18-generiek tot 3.19.0-80-generiek,<br/>4.2.0-18-generiek tot 4.2.0-42-generiek,<br/>4.4.0-21-generiek tot 4.4.0-148-generiek,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-generiek tot 3.13.0-170-generiek,<br/>3.16.0-25-generiek tot 3.16.0-77-generiek,<br/>3.19.0-18-generiek tot 3.19.0-80-generiek,<br/>4.2.0-18-generiek tot 4.2.0-42-generiek,<br/>4.4.0-21-generiek tot 4.4.0-148-generiek,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21-generiek tot 4.4.0-171-generiek,<br/>4.8.0-34-generiek tot 4.8.0-58-generiek,<br/>4.10.0-14-generiek tot 4.10.0-42-generiek,<br/>4.11.0-13-generiek tot 4.11.0-14-generiek,<br/>4.13.0-16-generiek tot 4.13.0-45-generiek,<br/>4.15.0-13-generiek tot 4.15.0-74-generiek<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21-generiek tot 4.4.0-170-generiek,<br/>4.8.0-34-generiek tot 4.8.0-58-generiek,<br/>4.10.0-14-generiek tot 4.10.0-42-generiek,<br/>4.11.0-13-generiek tot 4.11.0-14-generiek,<br/>4.13.0-16-generiek tot 4.13.0-45-generiek,<br/>4.15.0-13-generiek tot 4.15.0-72-generiek<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-generiek tot 4.4.0-166-generiek,<br/>4.8.0-34-generiek tot 4.8.0-58-generiek,<br/>4.10.0-14-generiek tot 4.10.0-42-generiek,<br/>4.11.0-13-generiek tot 4.11.0-14-generiek,<br/>4.13.0-16-generiek tot 4.13.0-45-generiek,<br/>4.15.0-13-generiek tot 4.15.0-66-generiek<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21-generiek tot 4.4.0-164-generiek,<br/>4.8.0-34-generiek tot 4.8.0-58-generiek,<br/>4.10.0-14-generiek tot 4.10.0-42-generiek,<br/>4.11.0-13-generiek tot 4.11.0-14-generiek,<br/>4.13.0-16-generiek tot 4.13.0-45-generiek,<br/>4.15.0-13-generiek tot 4.15.0-64-generiek<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20-generiek tot 4.15.0-74-generiek </br> 4.18.0-13-generiek tot 4.18.0-25-generiek </br> 5.0.0-15-generiek tot 5.0.0-37-generiek </br> 5.3.0-19-generiek tot 5.3.0-24-generiek </br> 4.15.0-1009-azure naar 4.15.0-1037-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1028-azure </br> 5.3.0-1007-azure naar 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20-generiek tot 4.15.0-72-generiek </br> 4.18.0-13-generiek tot 4.18.0-25-generiek </br> 5.0.0-15-generiek tot 5.0.0-37-generiek </br> 5.3.0-19-generiek tot 5.3.0-24-generiek </br> 4.15.0-1009-azure naar 4.15.0-1037-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-generiek tot 4.15.0-66-generiek </br> 4.18.0-13-generiek tot 4.18.0-25-generiek </br> 5.0.0-15-generiek tot 5.0.0-32-generiek </br> 4.15.0-1009-azure naar 4.15.0-1037-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-generiek tot 4.15.0-64-generiek </br> 4.18.0-13-generiek tot 4.18.0-25-generiek </br> 5.0.0-15-generiek tot 5.0.0-29-generiek </br> 4.15.0-1009-azure naar 4.15.0-1037-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde Debian-kernelversies voor virtuele Azure-machines

**Release** | **Mobility service versie** | **Kernelversie** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 naar 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 naar 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 naar 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 naar 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 naar 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde SUSE Linux Enterprise Server 12-kernelversies voor virtuele Azure-machines

**Release** | **Mobility service versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2, SP3, SP4) | 9.32 | Alle [voorraad SUSE 12 SP1,SP2,SP3,SP4 kernels](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) worden ondersteund.</br></br> 4.4.138-4.7-azure naar 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2, SP3, SP4) | 9.31 | Alle [voorraad SUSE 12 SP1,SP2,SP3,SP4 kernels](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) worden ondersteund.</br></br> 4.4.138-4.7-azure naar 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2, SP3, SP4) | 9.30 | Alle [voorraad SUSE 12 SP1,SP2,SP3,SP4 kernels](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) worden ondersteund.</br></br> 4.4.138-4.7-azure naar 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2, SP3, SP4) | 9.29 | Alle [voorraad SUSE 12 SP1,SP2,SP3,SP4 kernels](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) worden ondersteund.</br></br> 4.4.138-4.7-azure naar 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde SUSE Linux Enterprise Server 15-kernelversies voor virtuele Azure-machines

**Release** | **Mobility service versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 en 15 SP1 | 9.32 | Alle [stock SUSE 15 en 15 kernels](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) worden ondersteund.</br></br> 4.12.14-5,5-azure naar 4.12.14-8,22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Gerepliceerde machines - Linux-bestandssysteem/gastopslag

* Bestandssystemen: ext3, ext4, XFS, BTRFS
* Volumemanager: LVM2
* Multipath-software: Device Mapper


## <a name="replicated-machines---compute-settings"></a>Gerepliceerde machines - rekeninstellingen

**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
Grootte | Elke Azure VM-grootte met ten minste 2 CPU-cores en 1 GB RAM | Controleer [de grootte van de virtuele machine van Azure](../virtual-machines/windows/sizes.md).
Beschikbaarheidssets | Ondersteund | Als u replicatie inschakelt voor een Azure VM met de standaardopties, wordt er automatisch een beschikbaarheidsset gemaakt op basis van de instellingen van het brongebied. U deze instellingen wijzigen.
Beschikbaarheidszones | Ondersteund |
Voordeel voor hybride gebruik (HUB) | Ondersteund | Als de bron-VM een HUB-licentie heeft ingeschakeld, maakt een testfailover of mislukte over VM ook gebruik van de HUB-licentie.
Virtuele-machineschaalsets | Niet ondersteund |
Azure-galerijafbeeldingen - Microsoft gepubliceerd | Ondersteund | Ondersteund als de VM op een ondersteund besturingssysteem wordt uitgevoerd.
Azure Gallery-afbeeldingen - Derde partij gepubliceerd | Ondersteund | Ondersteund als de VM op een ondersteund besturingssysteem wordt uitgevoerd.
Aangepaste afbeeldingen - Derde partij gepubliceerd | Ondersteund | Ondersteund als de VM op een ondersteund besturingssysteem wordt uitgevoerd.
VM's gemigreerd met siteherstel | Ondersteund | Als een Vm of fysieke machine van VMware is gemigreerd naar Azure met behulp van Site recovery, moet u de oudere versie van de Mobiliteitsservice verwijderen die op de machine wordt uitgevoerd en de machine opnieuw starten voordat deze wordt gerepliceerd naar een andere Azure-regio.
RBAC-beleid | Niet ondersteund | RBAC-beleid (Role based Access Control) op VM's wordt niet gerepliceerd naar de failover-VM in doelregio.
Extensies | Niet ondersteund | Extensies worden niet gerepliceerd naar de failover-VM in doelregio. Het moet handmatig worden geïnstalleerd na failover.

## <a name="replicated-machines---disk-actions"></a>Gerepliceerde machines - schijfacties

**Actie** | **Details**
-- | ---
Formaat schijf wijzigen op gerepliceerde VM | Ondersteund op de bron-VM voor failover. U hoeft replicatie niet uit te schakelen/opnieuw in te schakelen.<br/><br/> Als u de bron-vm wijzigt na een failover, worden de wijzigingen niet vastgelegd.<br/><br/> Als u de schijfgrootte op de Azure VM wijzigt na een failover, worden wijzigingen niet vastgelegd door Site Recovery en wordt failback weergegeven in de oorspronkelijke VM-grootte.
Een schijf toevoegen aan een gerepliceerde virtuele machine | Ondersteund

## <a name="replicated-machines---storage"></a>Gerepliceerde machines - opslag

In deze tabel wordt ondersteuning samengevat voor de Azure VM OS-schijf, gegevensschijf en tijdelijke schijf.

- Het is belangrijk om de VM-schijflimieten en -doelen voor [Linux-](../virtual-machines/linux/disk-scalability-targets.md) en Windows-VM's in acht te nemen om prestatieproblemen te voorkomen. [Windows](../virtual-machines/windows/disk-scalability-targets.md)
- Als u implementeert met de standaardinstellingen, maakt Site Recovery automatisch schijven en opslagaccounts op basis van de broninstellingen.
- Als u zich aanpast, moet u ervoor zorgen dat u de richtlijnen volgt.

**Component** | **Ondersteuning** | **Details**
--- | --- | ---
Maximale grootte van de OS-schijf | 2048 GB | [Meer informatie](../virtual-machines/windows/managed-disks-overview.md) over VM-schijven.
Tijdelijke schijf
 | Niet ondersteund | De tijdelijke schijf is altijd uitgesloten van replicatie.<br/><br/> Sla geen permanente gegevens op de tijdelijke schijf op. [Meer informatie](../virtual-machines/windows/managed-disks-overview.md).
Maximale grootte van gegevensschijf | 8192 GB voor beheerde schijven<br></br>4095 GB voor niet-beheerde schijven|
Minimumgrootte gegevensschijf | Geen beperking voor onbeheerde schijven. 2 GB voor beheerde schijven |
Maximumaantal gegevensschijven | Maximaal 64, in overeenstemming met ondersteuning voor een specifieke Azure VM-grootte | [Meer informatie](../virtual-machines/windows/sizes.md) over VM-formaten.
Gegevensschijfwijzigingssnelheid | Maximaal 10 MBps per schijf voor premium opslag. Maximaal 2 MBps per schijf voor standaardopslag. | Als de gemiddelde gegevenswijzigingssnelheid op de schijf continu hoger is dan het maximum, wordt de replicatie niet ingehaald.<br/><br/>  Als het maximum echter sporadisch wordt overschreden, kan replicatie een inhaalslag maken, maar ziet u mogelijk enigszins vertraagde herstelpunten.
Gegevensschijf - standaardopslagaccount | Ondersteund |
Gegevensschijf - premium opslagaccount | Ondersteund | Als een VM schijven heeft die zijn verspreid over premium- en standaardopslagaccounts, u voor elke schijf een ander doelopslagaccount selecteren om ervoor te zorgen dat u dezelfde opslagconfiguratie in het doelgebied hebt.
Beheerde schijf - standaard | Ondersteund in Azure-regio's waarin Azure Site Recovery wordt ondersteund. |
Beheerde schijf - premium | Ondersteund in Azure-regio's waarin Azure Site Recovery wordt ondersteund. |
Standard - SSD | Ondersteund |
Redundantie | LRS en GRS worden ondersteund.<br/><br/> ZRS wordt niet ondersteund.
Koele en hete opslag | Niet ondersteund | VM-schijven worden niet ondersteund op koele en hete opslag
Opslagruimten | Ondersteund |
Encryptie in rust (SSE) | Ondersteund | SSE is de standaardinstelling voor opslagaccounts.
Versleuteling in rust (CMK) | Ondersteund | Zowel Software- als HSM-toetsen worden ondersteund voor beheerde schijven
Azure Disk Encryption (ADE) voor Windows OS | Ondersteund voor VM's met beheerde schijven. | VM's die onbeheerde schijven gebruiken, worden niet ondersteund. <br/><br/> HSM-beveiligde toetsen worden niet ondersteund. |
Azure Disk Encryption (ADE) voor Linux OS | Ondersteund voor VM's met beheerde schijven. | VM's die onbeheerde schijven gebruiken, worden niet ondersteund. <br/><br/> HSM-beveiligde toetsen worden niet ondersteund. |
Hot toevoegen    | Ondersteund | Replicatie inschakelen voor een gegevensschijf die u toevoegt aan een gerepliceerde Azure VM wordt ondersteund voor VM's die beheerde schijven gebruiken.
Hot verwijderen schijf    | Niet ondersteund | Als u gegevensschijf op de virtuele machine verwijdert, moet u replicatie uitschakelen en replicatie opnieuw inschakelen voor de vm.
Schijf uitsluiten | Ondersteuning. U moet [PowerShell](azure-to-azure-exclude-disks.md) gebruiken om te configureren. |    Tijdelijke schijven zijn standaard uitgesloten.
Opslagruimten direct  | Ondersteund voor crash consistente herstelpunten. Toepassingsconsistente herstelpunten worden niet ondersteund. |
Bestandsserver uitschalen  | Ondersteund voor crash consistente herstelpunten. Toepassingsconsistente herstelpunten worden niet ondersteund. |
Drbd | Schijven die deel uitmaken van een DRBD-installatie worden niet ondersteund. |
LRS | Ondersteund |
GRS | Ondersteund |
RA-GRS | Ondersteund |
ZRS | Niet ondersteund |
Koele en hete opslag | Niet ondersteund | Virtuele machineschijven worden niet ondersteund op koele en hete opslag
Azure Storage-firewalls voor virtuele netwerken  | Ondersteund | Als u de toegang tot opslagaccounts voor virtuele netwerken beperkt, schakelt [u Vertrouwde Microsoft-services toestaan](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)in.
V2-opslagaccounts voor algemeen gebruik (zowel hot- als cool-laag) | Ondersteund | Transactiekosten stijgen aanzienlijk in vergelijking met V1-opslagrekeningen voor algemeen gebruik
Generatie 2 (UEFI boot) | Ondersteund

>[!IMPORTANT]
> Om prestatieproblemen te voorkomen, moet u de schaalbaarheid en prestatiedoelen van vm-schijven voor [Linux-](../virtual-machines/linux/disk-scalability-targets.md) of Windows-VM's volgen. [Windows](../virtual-machines/windows/disk-scalability-targets.md) Als u standaardinstellingen gebruikt, maakt Site Recovery de vereiste schijven en opslagaccounts op basis van de bronconfiguratie. Als u uw eigen instellingen aanpast en selecteert, volgt u de schaalbaarheids- en prestatiedoelen van uw bronv.M.If you customize and select your own settings, follow the disk scalability and performance targets for your source VM's.

## <a name="limits-and-data-change-rates"></a>Limieten en gegevenswijzigingspercentages

In de volgende tabel worden de limieten voor siteherstel samengevat.

- Deze limieten zijn gebaseerd op onze tests, maar dekken uiteraard niet alle mogelijke i/o-combinaties van toepassing.
- De werkelijke resultaten kunnen variëren op basis van je app I/O-mix.
- Er zijn twee limieten te overwegen, per schijf data churn en per virtuele machine data churn.

**Opslagdoel** | **Gemiddelde bronschijf I/O** |**Gemiddelde bronschijfgegevensverloop** | **Totaal bronschijfgegevensverloop per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf

## <a name="replicated-machines---networking"></a>Gerepliceerde machines - netwerken
**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
NIC | Maximaal aantal dat wordt ondersteund voor een specifieke Azure VM-grootte | NIC's worden gemaakt wanneer de VM wordt gemaakt tijdens failover.<br/><br/> Het aantal NIC's op de failover-VM is afhankelijk van het aantal NIC's op de bron-VM toen replicatie is ingeschakeld. Als u een NIC toevoegt of verwijdert nadat u replicatie hebt ingeschakeld, heeft dit geen invloed op het aantal NIC's op de gerepliceerde vm na failover. Houd er ook rekening mee dat de volgorde van NIC's na failover niet gegarandeerd hetzelfde is als de oorspronkelijke bestelling.
Internet Load Balancer | Ondersteund | Koppel de vooraf geconfigureerde load balancer aan een Azure Automation-script in een herstelplan.
Interne load balancer | Ondersteund | Koppel de vooraf geconfigureerde load balancer aan een Azure Automation-script in een herstelplan.
Openbaar IP-adres | Ondersteund | Een bestaand openbaar IP-adres koppelen aan de NIC. Of maak een openbaar IP-adres en koppel het aan de NIC met behulp van een Azure Automation-script in een herstelplan.
NSG op NIC | Ondersteund | Koppel de NSG aan de NIC met behulp van een Azure Automation-script in een herstelplan.
NSG op subnet | Ondersteund | Koppel de NSG aan het subnet met behulp van een Azure Automation-script in een herstelplan.
Gereserveerd (statisch) IP-adres | Ondersteund | Als de NIC op de bron-VM een statisch IP-adres heeft en het doelsubnet hetzelfde IP-adres beschikbaar heeft, wordt het toegewezen aan de mislukte over-VM.<br/><br/> Als het doelsubnet niet hetzelfde IP-adres beschikbaar heeft, is een van de beschikbare IP-adressen in het subnet gereserveerd voor de VM.<br/><br/> U ook een vast IP-adres en subnet opgeven in de**interfaces Voor** > de berekening van de instellingen voor **gerepliceerde items** > Compute en**Netwerknetwerk.****Compute and Network** > 
Dynamisch IP-adres | Ondersteund | Als de NIC op de bron dynamische IP-adressering heeft, is de NIC op de mislukte VM standaard ook dynamisch.<br/><br/> U dit indien nodig wijzigen naar een vast IP-adres.
Meerdere IP-adressen | Niet ondersteund | Wanneer u niet over een vm slaagt met een NIC met meerdere IP-adressen, blijft alleen het primaire IP-adres van de NIC in het brongebied behouden. Als u meerdere IP-adressen wilt toewijzen, u VM's toevoegen aan een [herstelplan](recovery-plan-overview.md) en een script toevoegen om extra IP-adressen aan het abonnement toe te wijzen, of u de wijziging handmatig of met een script na failover aanbrengen.
Traffic Manager     | Ondersteund | U Traffic Manager vooraf configureren zodat verkeer regelmatig wordt doorgestuurd naar het eindpunt in het brongebied en naar het eindpunt in het doelgebied in geval van failover.
Azure DNS | Ondersteund |
Aangepaste DNS    | Ondersteund |
Niet-geverifieerde proxy | Ondersteund | [Meer informatie](site-recovery-azure-to-azure-networking-guidance.md)
Geverifieerde proxy | Niet ondersteund | Als de VM een geverifieerde proxy gebruikt voor uitgaande connectiviteit, kan deze niet worden gerepliceerd met Azure Site Recovery.
VPN-site-to-site-verbinding met on-premises<br/><br/>(met of zonder ExpressRoute)| Ondersteund | Zorg ervoor dat de UDR's en NSG's zodanig zijn geconfigureerd dat het siteherstelverkeer niet ter plaatse wordt doorgestuurd. [Meer informatie](site-recovery-azure-to-azure-networking-guidance.md)
VNET-verbinding met VNET    | Ondersteund | [Meer informatie](site-recovery-azure-to-azure-networking-guidance.md)
Service-eindpunten voor virtueel netwerk | Ondersteund | Als u de toegang van het virtuele netwerk tot opslagaccounts beperkt, moet u ervoor zorgen dat de vertrouwde Microsoft-services toegang krijgen tot het opslagaccount.
Versneld netwerken | Ondersteund | Versnelde netwerken moeten zijn ingeschakeld op bron-VM. [Meer informatie](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Volgende stappen
- Lees [netwerkrichtlijnen](site-recovery-azure-to-azure-networking-guidance.md) voor het repliceren van Azure VM's.
- Herstel na noodgevallen implementeren door [Azure VM's te repliceren.](site-recovery-azure-to-azure.md)
