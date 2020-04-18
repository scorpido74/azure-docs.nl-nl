---
title: Ondersteuningsmatrix voor VMware/fysiek herstel van rampen in Azure Site Recovery
description: Vat ondersteuning voor noodherstel van VMware VM's en fysieke server samen naar Azure met Azure Site Recovery.
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: 02448754abd92eab9e095a5eaff10861f8b5e5e4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606037"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Ondersteuningsmatrix voor noodherstel van Vm's en fysieke servers naar Azure

In dit artikel worden ondersteunde onderdelen en instellingen voor noodherstel van Vm's en fysieke servers naar Azure samengevat met [Azure Site Recovery](site-recovery-overview.md).

- [Meer informatie](vmware-azure-architecture.md) over vmware VM/physical server disaster recovery architecture.
- Volg onze [tutorials](tutorial-prepare-azure.md) om te proberen ramp herstel.

## <a name="deployment-scenarios"></a>Implementatiescenario's

**Scenario** | **Details**
--- | ---
Noodherstel vmware vm's | Replicatie van on-premises Vm's vMware naar Azure. U dit scenario implementeren in de Azure-portal of met [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Noodherstel van fysieke servers | Replicatie van on-premises Fysieke Windows/Linux-servers naar Azure. U dit scenario implementeren in de Azure-portal.

## <a name="on-premises-virtualization-servers"></a>On-premises virtualisatieservers

**Server** | **Vereisten** | **Details**
--- | --- | ---
vCenter Server | Versie 6.7, 6.5, 6.0 of 5.5 | We raden u aan een vCenter-server te gebruiken bij uw implementatie bij noodherstel.
vSphere-hosts | Versie 6.7, 6.5, 6.0 of 5.5 | We raden u aan vSphere-hosts en vCenter-servers zich in hetzelfde netwerk te bevinden als de processerver. Standaard wordt de processerver uitgevoerd op de configuratieserver. [Meer informatie](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Configuratieserver siteherstel

De configuratieserver is een on-premises machine die siteherstelcomponenten uitvoert, waaronder de configuratieserver, processerver en hoofddoelserver.

- Voor VMware VM's stelt u de configuratieserver in door een OVF-sjabloon te downloaden om een Vm vmware te maken.
- Voor fysieke servers stelt u de configuratieservermachine handmatig in.

**Component** | **Vereisten**
--- |---
CPU-kernen | 8
RAM | 16 GB
Aantal schijven | 3 schijven<br/><br/> Schijven omvatten de OS-schijf, de servercacheschijf en het bewaarstation voor failback.
Schijfvrije ruimte | 600 GB ruimte voor de processervercache.
Schijfvrije ruimte | 600 GB ruimte voor de bewaarschijf.
Besturingssysteem  | Windows Server 2012 R2 of Windows Server 2016 met bureaubladervaring <br/><br> Als u van plan bent het ingebouwde mastertarget van dit toestel te gebruiken voor failback, moet u ervoor zorgen dat de os-versie hetzelfde of hoger is dan de gerepliceerde items.|
Landinstelling van het besturingssysteem | Engels (en-us)
[PowerCLI (PowerCLI)](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Niet nodig voor configuratieserverversie [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) of hoger.
Windows Server-functies | Schakel Active Directory Domain Services niet in. Internet Information Services (IIS) of Hyper-V.
Groepsbeleid| - Voorkom toegang tot de opdrachtprompt. <br/> - Voorkom toegang tot registerbewerkingstools. <br/> - Logica vertrouwen voor bestandsbijlagen. <br/> - Scriptuitvoering inschakelen. <br/> - [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Zorg ervoor dat u:<br/><br/> - Heb je geen bestaande standaardwebsite <br/> - [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) inschakelen <br/> - [FastCGI-instelling](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inschakelen  <br/> - Heb je geen reeds bestaande website/app luisteren op poort 443<br/>
NIC-type | VMXNET3 (wanneer ingezet als VMware VM)
Type IP-adres | Statisch
Poorten | 443 gebruikt voor de orkestratie van het controlekanaal<br/>9443 voor datatransport

## <a name="replicated-machines"></a>Gerepliceerde machines

Site recovery ondersteunt replicatie van alle workloads die op een ondersteunde machine worden uitgevoerd.

> [!Note]
> In de volgende tabel vindt u de ondersteuning voor machines met BIOS-boot. Raadpleeg de [sectie Opslag](#storage) voor ondersteuning op UEFI-gebaseerde machines.

**Component** | **Details**
--- | ---
Machine-instellingen | Machines die repliceren naar Azure moeten voldoen aan [azure-vereisten.](#azure-vm-requirements)
Werkbelasting voor machines | Site recovery ondersteunt replicatie van alle workloads die op een ondersteunde machine worden uitgevoerd. [Meer informatie](https://aka.ms/asr_workload).
Machinenaam | Ervoor zorgen dat de weergavenaam van de machine niet in [azure-gereserveerde bronnamen](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-reserved-resource-name) valt<br/><br/> Logische volumenamen zijn niet hoofdlettergevoelig. Zorg ervoor dat geen twee volumes op een apparaat dezelfde naam hebben. Bijvoorbeeld: Volumes met de namen 'voLUME1', 'volume1' kunnen niet worden beveiligd via Azure Site Recovery.
Windows Server 2019 | Ondersteund vanaf [Update rollup 34](https://support.microsoft.com/help/4490016) (versie 9.22 van de Mobility service) en verder.
Windows Server 2016 64-bits | Ondersteuning voor Server Core, Server met desktopervaring.
Windows Server 2012 R2 / Windows Server 2012 | Ondersteund.
Windows Server 2008 R2 met SP1 verder. | Ondersteund.<br/><br/> Vanaf versie [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility-serviceagent hebt u [servicestackupdate (SSU)](https://support.microsoft.com/help/4490628) en [SHA-2-update](https://support.microsoft.com/help/4474419) nodig die zijn geïnstalleerd op machines met Windows 2008 R2 met SP1 of hoger. SHA-1 wordt niet meer ondersteund vanaf september 2019 en als sha-2-codeondertekening niet is ingeschakeld, wordt de agentextensie niet geïnstalleerd/geüupgradet zoals verwacht. Meer informatie over [sha-2-upgrade en vereisten.](https://aka.ms/SHA-2KB)
Windows Server 2008 met SP2 of hoger (64-bits/32-bits) |  Alleen ondersteund voor migratie. [Meer informatie](migrate-tutorial-windows-server-2008.md).<br/><br/> Vanaf versie [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility-serviceagent hebt u [servicestack-update (SSU)](https://support.microsoft.com/help/4493730) en [SHA-2-updates](https://support.microsoft.com/help/4474419) nodig die zijn geïnstalleerd op Windows 2008 SP2-machines. ISHA-1 wordt niet meer ondersteund vanaf september 2019 en als sha-2-codeondertekening niet is ingeschakeld, wordt de agentextensie niet geïnstalleerd/geüupgradet zoals verwacht. Meer informatie over [sha-2-upgrade en vereisten.](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
Windows 10, Windows 8.1, Windows 8 | Ondersteund.
Windows 7 met SP1 64-bits | Ondersteund vanaf [Update rollup 36](https://support.microsoft.com/help/4503156) (versie 9.22 van de Mobility service) en verder. </br></br> Vanaf [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility service agent, moet u [onderhoud stack update (SSU)](https://support.microsoft.com/help/4490628) en [SHA-2 update](https://support.microsoft.com/help/4474419) geïnstalleerd op Windows 7 SP1 machines.  SHA-1 wordt niet meer ondersteund vanaf september 2019 en als sha-2-codeondertekening niet is ingeschakeld, wordt de agentextensie niet geïnstalleerd/geüupgradet zoals verwacht. Meer informatie over [sha-2-upgrade en vereisten.](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
Linux | Alleen 64-bits systeem wordt ondersteund. Het 32-bits systeem wordt niet ondersteund.<br/><br/>Elke Linux-server moet [Linux Integration Services (LIS) componenten](https://www.microsoft.com/download/details.aspx?id=55106) geïnstalleerd. Het is vereist om de server op te starten in Azure na test failover / failover. Als LIS-componenten ontbreken, moet u ervoor zorgen dat de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) worden geïnstalleerd voordat replicatie wordt ingemaakt zodat de machines in Azure kunnen worden opgestart. <br/><br/> Site Recovery orkestreert failover om Linux-servers in Azure uit te voeren. Linux-leveranciers kunnen de ondersteuning echter beperken tot alleen distributieversies die het einde van de levensduur nog niet hebben bereikt.<br/><br/> Op Linux-distributies worden alleen de stockkernels ondersteund die deel uitmaken van de release/update van de distributiekleine versie.<br/><br/> Het upgraden van beveiligde machines in grote Linux-distributieversies wordt niet ondersteund. Als u wilt upgraden, schakelt u replicatie uit, upgradet u het besturingssysteem en schakelt u replicatie opnieuw in.<br/><br/> [Meer informatie](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) over ondersteuning voor Linux en open-sourcetechnologie in Azure.
Linux Red Hat Enterprise | 5.2 tot 5.11</b><br/> 6.1 tot 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1 <br/> Servers met Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 hebben geen [Linux Integration Services (LIS) componenten](https://www.microsoft.com/download/details.aspx?id=55106) vooraf geïnstalleerd. Zorg ervoor dat u de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) installeert voordat replicatie wordt uitgevoerd voor de machines die in Azure kunnen worden opgestart.
Linux: CentOS | 5.2 tot 5.11</b><br/> 6.1 tot 6.10</b><br/> 7,0 tot 7,6<br/> <br/> 8,0 tot 8,1<br/><br/> Servers met CentOS 5.2-5.11 & 6.1-6.10 hebben geen [Linux Integration Services (LIS)-componenten](https://www.microsoft.com/download/details.aspx?id=55106) vooraf geïnstalleerd. Zorg ervoor dat u de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) installeert voordat replicatie wordt uitgevoerd voor de machines die in Azure kunnen worden opgestart.
Ubuntu | Ubuntu 14.04 LTS server [(review ondersteunde kernel versies)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server [(review ondersteunde kernel versies)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS server [(review ondersteunde kernel versies)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(review ondersteunde kernel versies)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(review ondersteunde kernelversies)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(review ondersteunde kernelversies)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Het upgraden van gerepliceerde machines van SUSE Linux Enterprise Server 11 SP3 naar SP4 wordt niet ondersteund. Als u wilt upgraden, schakelt u replicatie uit en schakelt u het opnieuw in na de upgrade.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Uitvoeren van de Red Hat compatibele kernel of Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)

> [!Note]
> Voor elk van de Windows-versies ondersteunt Azure Site Recovery alleen [LTSC-builds (Long-Term Servicing Channel).](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)  [Semi-Annual Channel](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) releases worden momenteel niet ondersteund op dit moment.

### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel versies

**Ondersteunde release** | **Mobility service versie** | **Kernelversie** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-generiek tot 3.13.0-170-generiek,<br/>3.16.0-25-generiek tot 3.16.0-77-generiek,<br/>3.19.0-18-generiek tot 3.19.0-80-generiek,<br/>4.2.0-18-generiek tot 4.2.0-42-generiek,<br/>4.4.0-21-generiek tot 4.4.0-148-generiek,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-generiek tot 3.13.0-170-generiek,<br/>3.16.0-25-generiek tot 3.16.0-77-generiek,<br/>3.19.0-18-generiek tot 3.19.0-80-generiek,<br/>4.2.0-18-generiek tot 4.2.0-42-generiek,<br/>4.4.0-21-generiek tot 4.4.0-148-generiek,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-generiek tot 3.13.0-170-generiek,<br/>3.16.0-25-generiek tot 3.16.0-77-generiek,<br/>3.19.0-18-generiek tot 3.19.0-80-generiek,<br/>4.2.0-18-generiek tot 4.2.0-42-generiek,<br/>4.4.0-21-generiek tot 4.4.0-148-generiek,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-generiek tot 3.13.0-170-generiek,<br/>3.16.0-25-generiek tot 3.16.0-77-generiek,<br/>3.19.0-18-generiek tot 3.19.0-80-generiek,<br/>4.2.0-18-generiek tot 4.2.0-42-generiek,<br/>4.4.0-21-generiek tot 4.4.0-148-generiek,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-generiek tot 4.4.0-171-generiek,<br/>4.8.0-34-generiek tot 4.8.0-58-generiek,<br/>4.10.0-14-generiek tot 4.10.0-42-generiek,<br/>4.11.0-13-generiek tot 4.11.0-14-generiek,<br/>4.13.0-16-generiek tot 4.13.0-45-generiek,<br/>4.15.0-13-generiek tot 4.15.0-74-generiek<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-generiek tot 4.4.0-170-generiek,<br/>4.8.0-34-generiek tot 4.8.0-58-generiek,<br/>4.10.0-14-generiek tot 4.10.0-42-generiek,<br/>4.11.0-13-generiek tot 4.11.0-14-generiek,<br/>4.13.0-16-generiek tot 4.13.0-45-generiek,<br/>4.15.0-13-generiek tot 4.15.0-72-generiek<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-generiek tot 4.4.0-166-generiek,<br/>4.8.0-34-generiek tot 4.8.0-58-generiek,<br/>4.10.0-14-generiek tot 4.10.0-42-generiek,<br/>4.11.0-13-generiek tot 4.11.0-14-generiek,<br/>4.13.0-16-generiek tot 4.13.0-45-generiek,<br/>4.15.0-13-generiek tot 4.15.0-66-generiek<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-generiek tot 4.4.0-164-generiek,<br/>4.8.0-34-generiek tot 4.8.0-58-generiek,<br/>4.10.0-14-generiek tot 4.10.0-42-generiek,<br/>4.11.0-13-generiek tot 4.11.0-14-generiek,<br/>4.13.0-16-generiek tot 4.13.0-45-generiek,<br/>4.15.0-13-generiek tot 4.15.0-64-generiek<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-generiek tot 4.15.0-74-generiek </br> 4.18.0-13-generiek tot 4.18.0-25-generiek </br> 5.0.0-15-generiek tot 5.0.0-37-generiek </br> 5.3.0-19-generiek tot 5.3.0-24-generiek </br> 4.15.0-1009-azure naar 4.15.0-1037-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1028-azure </br> 5.3.0-1007-azure naar 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-generiek tot 4.15.0-72-generiek </br> 4.18.0-13-generiek tot 4.18.0-25-generiek </br> 5.0.0-15-generiek tot 5.0.0-37-generiek </br> 5.3.0-19-generiek tot 5.3.0-24-generiek </br> 4.15.0-1009-azure naar 4.15.0-1037-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-generiek tot 4.15.0-66-generiek </br> 4.18.0-13-generiek tot 4.18.0-25-generiek </br> 5.0.0-15-generiek tot 5.0.0-32-generiek </br> 4.15.0-1009-azure naar 4.15.0-1037-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-generiek tot 4.15.0-62-generiek </br> 4.18.0-13-generiek tot 4.18.0-25-generiek </br> 5.0.0-15-generiek tot 5.0.0-27-generiek </br> 4.15.0-1009-azure naar 4.15.0-1037-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Debian kernel versies


**Ondersteunde release** | **Mobility service versie** | **Kernelversie** |
--- | --- | --- |
Debian 7 | [9.29][9.29 UR], [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR]| 3.2.0-4-amd64 naar 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR] | 3.16.0-4-amd64 naar 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 naar 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 naar 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 naar 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 ondersteunde kernelversies

**Release** | **Mobility service versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.28][9.28 UR] | SP1 3.12.49-11-default naar 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69-default naar 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-standaard naar 4.4.121-92.117-standaard</br></br>SP3 4.4.73-5-standaard naar 4.4.180-94.100-standaard</br></br>SP3 4.4.138-4.7-azure naar 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-standaard naar 4.12.14-95.29-default</br>SP4 4.12.14-6.3-azure naar 4.12.14-6.23-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default naar 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-default naar 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-standaard naar 4.4.121-92.114-standaard</br></br>SP3 4.4.73-5-standaard naar 4.4.180-94.97-standaard</br></br>SP3 4.4.138-4.7-azure naar 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-standaard naar 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure naar 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default naar 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default naar 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-standaard naar 4.4.121-92.109-default</br></br>SP3 4.4.73-5-standaard naar 4.4.178-94.91-standaard</br></br>SP3 4.4.138-4.7-azure naar 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-standaard naar 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure naar 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default naar 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default naar 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-standaard naar 4.4.121-92.104-default</br></br>SP3 4.4.73-5-standaard naar 4.4.176-94.88-standaard</br></br>SP3 4.4.138-4.7-azure naar 4.4.176-4,25-azure</br></br>SP4 4.12.14-94.41-standaard naar 4.12.14-95.13-standaard</br>SP4 4.12.14-6.3-azure naar 4.12.14-6.9-azure |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 ondersteunde kernelversies

**Release** | **Mobility service versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 en 15 SP1 | [9.32](https://support.microsoft.com/help/4550047/) | Alle stock SUSE 15 en 15 kernels worden ondersteund. </br></br> 4.12.14-5,5-azure naar 4.12.14-8,22-azure

## <a name="linux-file-systemsguest-storage"></a>Linux-bestandssystemen/gastopslag

**Component** | **Ondersteund**
--- | ---
Bestandssystemen | ext3, ext4, XFS, BTRFS (voorwaarden die van toepassing zijn volgens deze tabel)
Volumemanager | - LVM wordt ondersteund.<br/> - /boot on LVM wordt vanaf [Update Rollup 31](https://support.microsoft.com/help/4478871/) (versie 9.20 van de Mobility service) ondersteund. Het wordt niet ondersteund in eerdere Mobility-serviceversies.<br/> - Meerdere OS-schijven worden niet ondersteund.
Gevirtualiseerde opslagapparaten | Apparaten die zijn geëxporteerd door geparavirtualiseerde stuurprogramma's worden niet ondersteund.
Io-apparaten met meerdere wachtrijen blokkeren | Wordt niet ondersteund.
Fysieke servers met de HP CCISS-opslagcontroller | Wordt niet ondersteund.
Conventie voor het benoemen van apparaat/bevestigingspunt | De naam van het apparaat of de naam van het bevestigingspunt moet uniek zijn.<br/> Zorg ervoor dat er geen twee apparaten/bevestigingspunten hoofdlettergevoelige namen hebben. Het benoemen van apparaten voor dezelfde VM als *device1* en *Device1* wordt bijvoorbeeld niet ondersteund.
Mappen | Als u een versie van de Mobiliteitsservice eerder uitvoert dan versie 9.20 (uitgebracht in [Update Rollup 31),](https://support.microsoft.com/help/4478871/)zijn deze beperkingen van toepassing:<br/><br/> - Deze mappen (indien ingesteld als afzonderlijke partities / bestandssystemen) moeten zich op dezelfde OS-schijf op de bronserver bestaan: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - De /boot directory moet zich op een schijfpartitie bevinden en mag geen LVM-volume zijn.<br/><br/> Vanaf versie 9.20 zijn deze beperkingen niet van toepassing. 
Opstartmap | - Opstartschijven mogen niet in de GPT-partitieindeling staan. Dit is een Azure-architectuurbeperking. GPT-schijven worden ondersteund als gegevensschijven.<br/><br/> Meerdere opstartschijven op een virtuele machine worden niet ondersteund<br/><br/> - /boot op een LVM-volume op meer dan één schijf wordt niet ondersteund.<br/> - Een machine zonder opstartschijf kan niet worden gerepliceerd.
Vereisten voor vrije ruimte| 2 GB op de /root partitie <br/><br/> 250 MB op de installatiemap
XFSv5 (XFSv5) | XFSv5-functies op XFS-bestandssystemen, zoals metadata checksum, worden ondersteund (Mobility service versie 9.10 vanaf).<br/> Gebruik het xfs_info hulpprogramma om het XFS-superblok voor de partitie te controleren. Als `ftype` dit is ingesteld op 1, worden xfsv5-functies gebruikt.
Btrfs | BTRFS wordt vanaf [Update Rollup 34](https://support.microsoft.com/help/4490016) (versie 9.22 van de Mobility-service) ondersteund. BTRFS wordt niet ondersteund als:<br/><br/> - Het subvolume van het BTRFS-bestandssysteem wordt gewijzigd nadat bescherming is ingesteld.</br> - Het BTRFS-bestandssysteem is verspreid over meerdere schijven.</br> - Het BTRFS-bestandssysteem ondersteunt RAID.

## <a name="vmdisk-management"></a>VM/Disk-beheer

**Actie** | **Details**
--- | ---
Formaat schijf wijzigen op gerepliceerde VM | Ondersteund op de bron-VM vóór failover, rechtstreeks in de VM-eigenschappen. U hoeft replicatie niet uit te schakelen/opnieuw in te schakelen.<br/><br/> Als u de bron-VM wijzigt na een failover, worden de wijzigingen niet vastgelegd.<br/><br/> Als u de schijfgrootte op de Azure VM na een failover wijzigt, maakt Site Recovery een nieuwe vm met de updates wanneer u niet terugvalt.
Schijf toevoegen op gerepliceerde VM | Wordt niet ondersteund.<br/> Schakel replicatie voor de virtuele machine uit, voeg de schijf toe en schakel replicatie opnieuw in.

## <a name="network"></a>Netwerk

**Component** | **Ondersteund**
--- | ---
Host netwerk NIC Teaming | Ondersteund voor VMware VM's. <br/><br/>Wordt niet ondersteund voor fysieke machinereplicatie.
Hostnetwerk VLAN | Ja.
Hostnetwerk IPv4 | Ja.
Hostnetwerk IPv6 | Nee.
Gast/servernetwerk NIC Teaming | Nee.
Gast-/servernetwerk IPv4 | Ja.
Gast-/servernetwerk IPv6 | Nee.
Statisch IP-adres van gast-/servernetwerk (Windows) | Ja.
Statisch IP-adres van gast/servernetwerk (Linux) | Ja. <br/><br/>VM's zijn geconfigureerd om DHCP te gebruiken bij failback.
Gast-/servernetwerk meerdere NIC's | Ja.


## <a name="azure-vm-network-after-failover"></a>Azure VM-netwerk (na failover)

**Component** | **Ondersteund**
--- | ---
Azure ExpressRoute | Ja
ILB (ILB) | Ja
Elb | Ja
Azure Traffic Manager | Ja
Multi-NIC | Ja
Gereserveerd IP-adres | Ja
IPv4 | Ja
Ip-adres van bron behouden | Ja
Service-eindpunten voor een virtueel Azure-netwerk<br/> | Ja
Versneld netwerken | Nee

## <a name="storage"></a>Storage
**Component** | **Ondersteund**
--- | ---
Dynamische schijf | OS-schijf moet een basisschijf zijn. <br/><br/>Gegevensschijven kunnen dynamische schijven zijn
Docker-schijfconfiguratie | Nee
Host NFS | Ja voor VMware<br/><br/> Nee voor fysieke servers
Host SAN (iSCSI/FC) | Ja
Host vSAN | Ja voor VMware<br/><br/> N/A voor fysieke servers
Host multipath (MPIO) | Ja, getest met Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM voor CLARiiON
Virtuele volumes hosten (VVols) | Ja voor VMware<br/><br/> N/A voor fysieke servers
Gast/server VMDK | Ja
Gedeelde clusterschijf gast/server | Nee
Versleutelde schijf voor gasten/server | Nee
NFS gast/server | Nee
Gast/server iSCSI | Voor migratie - Ja<br/>Voor herstel na noodgevallen - Nee, iSCSI mislukt als een gekoppelde schijf aan de VM
Gast/server SMB 3.0 | Nee
Gast/server RDM | Ja<br/><br/> N/A voor fysieke servers
Gast-serverschijf > 1 TB | Ja, schijf moet groter zijn dan 1024 MB<br/><br/>Maximaal 8.192 GB bij het repliceren naar beheerde schijven (versie 9,26 en verder)<br></br> Tot 4.095 GB bij het repliceren naar opslagaccounts
Gast/serverschijf met 4K-logische en 4k-fysieke sectorgrootte | Nee
Gast-/serverschijf met 4K-logische en fysieke sectorgrootte van 512 bytes | Nee
Gast/servervolume met gestreepte schijf >4 TB <br/><br/>Logisch volumebeheer (LVM)| Ja
Gast/server - Opslagruimten | Nee
Gast/server hot add/remove disk | Nee
Gast/server - schijf uitsluiten | Ja
Gast/server multipath (MPIO) | Nee
Gpt-partities voor gasten/server | Vanaf [Update Rollup 37](https://support.microsoft.com/help/4508614/) (versie 9.25 van de Mobility-service) worden vijf partities ondersteund. Voorheen werden er vier ondersteund.
ReFS | Resilient File System wordt ondersteund met Mobility-serviceversie 9.23 of hoger
Gast/server EFI/UEFI opstarten | - Ondersteund voor Windows Server 2012 of hoger, SLES 12 SP4 en RHEL 8.0 met mobiliteitsagent versie 9.30 en vanaf 06.30<br/> - Secure UEFI boot type wordt niet ondersteund.

## <a name="replication-channels"></a>Replicatiekanalen

|**Type replicatie**   |**Ondersteund**  |
|---------|---------|
|Ongeladen gegevensoverdrachten (ODX)    |       Nee  |
|Offline zaaien        |   Nee      |
| Azure Data Box | Nee

## <a name="azure-storage"></a>Azure Storage

**Component** | **Ondersteund**
--- | ---
Lokaal redundante opslag | Ja
Geografisch redundante opslag | Ja
Geografisch redundante opslag met leestoegang | Ja
Koele opslag | Nee
Hete opslag| Nee
Blok-blobs | Nee
Encryptie-at-rest (SSE)| Ja
Encryptie-at-rest (CMK)| Ja (via PowerShell Az 3.3.0 module)
Premium Storage | Ja
Import/exportservice | Nee
Azure Storage-firewalls voor VNets | Ja.<br/> Geconfigureerd op doelopslag-/cacheopslagaccount (gebruikt om replicatiegegevens op te slaan).
V2-opslagaccounts voor algemene doeleinden (hot en cool-lagen) | Ja (Transactiekosten zijn aanzienlijk hoger voor V2 in vergelijking met V1)

## <a name="azure-compute"></a>Azure-gegevensberekenen

**Functie** | **Ondersteund**
--- | ---
Beschikbaarheidssets | Ja
Beschikbaarheidszones | Nee
Hub | Ja
Managed Disks | Ja

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

On-premises VM's die zijn gerepliceerd naar Azure moeten voldoen aan de Azure VM-vereisten die in deze tabel zijn samengevat. Wanneer siteherstel een vereiste controle uitvoert voor replicatie, mislukt de controle als niet aan een aantal vereisten wordt voldaan.

**Component** | **Vereisten** | **Details**
--- | --- | ---
Gastbesturingssysteem | Controleer [ondersteunde besturingssystemen](#replicated-machines) voor gerepliceerde machines. | Controleren mislukt als niet-ondersteund.
Gastbesturingssysteemarchitectuur | 64-bits. | Controleren mislukt als niet-ondersteund.
Grootte van de besturingssysteemschijf | Max. | Controleren mislukt als niet-ondersteund.
Aantal besturingssysteemschijven | 1 | Controleren mislukt als niet-ondersteund.
Aantal gegevensschijven | 64 of minder. | Controleren mislukt als niet-ondersteund.
Gegevensschijfgrootte | Tot 8.192 GB bij het repliceren naar beheerde schijf (versie 9,26 verder)<br></br>Maximaal 4.095 GB bij het repliceren naar een opslagaccount| Controleren mislukt als niet-ondersteund.
Netwerkadapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Wordt niet ondersteund. | Controleren mislukt als niet-ondersteund.
FC-schijf | Wordt niet ondersteund. | Controleren mislukt als niet-ondersteund.
BitLocker | Wordt niet ondersteund. | BitLocker moet zijn uitgeschakeld voordat u replicatie voor een machine inschakelt. |
VM-naam | Van 1 tot 63 tekens.<br/><br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de machine moet beginnen en eindigen met een letter of nummer. |  Werk de waarde in de machine-eigenschappen bij in Siteherstel.

## <a name="resource-group-limits"></a>Limieten voor resourcegroepen

Als u inzicht wilt krijgen in het aantal virtuele machines dat onder één resourcegroep kan worden beschermd, raadpleegt u het artikel over [abonnementslimieten en quota](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits).

## <a name="churn-limits"></a>Churn-limieten

De volgende tabel bevat de Azure Site Recovery-limieten.
- Deze limieten zijn gebaseerd op onze tests, maar dekken niet alle mogelijke app I/O combinaties.
- De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie.
- Voor de beste resultaten raden we u ten zeerste aan de [tool Deployment Planner](site-recovery-deployment-planner.md)uit te voeren en uitgebreide toepassingstests uit te voeren met behulp van testfailovers om de werkelijke prestatiefoto voor uw app te krijgen.

**Replicatiedoel** | **Gemiddelde I/O-grootte van bronschijf** |**Gemiddelde bronschijfgegevensverloop** | **Totaal bronschijfgegevensverloop per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf


**brongegevensverloop** | **Maximumaantal**
---|---
Piekgegevensverloop over alle schijven op een VM | 54 MB/s
Maximumgegevensverloop per dag dat wordt ondersteund door een processerver | 2 TB

- Dit zijn gemiddelden uitgaande van een I/O-overlapping van 30%.
- Site Recovery kan een hogere doorvoer verwerken op basis van overlappingsverhouding, grotere schrijfgrootten en daadwerkelijk workload-I/O-gedrag.
- Deze getallen gaan uit van een typische achterstand van ongeveer vijf minuten. Dat wil zeggen dat de gegevens na het uploaden binnen vijf minuten worden verwerkt en er een herstelpunt is gemaakt.

## <a name="vault-tasks"></a>Vault-taken

**Actie** | **Ondersteund**
--- | ---
Kluis verplaatsen tussen resourcegroepen | Nee
Kluis verplaatsen binnen en tussen abonnementen | Nee
Opslag, netwerk, Azure VM's verplaatsen tussen resourcegroepen | Nee
Verplaats opslag, netwerk, Azure VM's binnen en tussen abonnementen. | Nee


## <a name="obtain-latest-components"></a>De nieuwste onderdelen verkrijgen

**Naam** | **Beschrijving** | **Details**
--- | --- | ---
Configuratieserver | Geïnstalleerd op locatie.<br/> Coördineert de communicatie tussen on-premises VMware-servers of fysieke machines en Azure. | - [Meer informatie over](vmware-physical-azure-config-process-server-overview.md) de configuratieserver.<br/> - [Meer informatie over](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) upgraden naar de nieuwste versie.<br/> - [Meer informatie over](vmware-azure-deploy-configuration-server.md) het instellen van de configuratieserver.
Processerver | standaard geïnstalleerd op de configuratieserver.<br/> Ontvangt replicatiegegevens, optimaliseert deze met caching, compressie en versleuteling en stuurt deze naar Azure.<br/> Naarmate uw implementatie toeneemt, u extra processervers toevoegen om grotere hoeveelheden replicatieverkeer te verwerken. | - [Meer informatie over](vmware-physical-azure-config-process-server-overview.md) de processerver.<br/> - [Meer informatie over](vmware-azure-manage-process-server.md#upgrade-a-process-server) upgraden naar de nieuwste versie.<br/> - [Meer informatie over](vmware-physical-large-deployment.md#set-up-a-process-server) het instellen van scale-out processervers.
Mobility-service | Geïnstalleerd op VMware VM of fysieke servers die u wilt repliceren.<br/> Coördineert replicatie tussen on-premises VMware-servers/fysieke servers en Azure.| - [Meer informatie over](vmware-physical-mobility-service-overview.md) de Mobiliteitsservice.<br/> - [Meer informatie over](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) upgraden naar de nieuwste versie.<br/>



## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het](tutorial-prepare-azure.md) voorbereiden van Azure op noodherstel van VMware VM's.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
