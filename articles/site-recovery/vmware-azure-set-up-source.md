---
title: Broninstellingen voor VMware-noodherstel instellen voor Azure met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u uw on-premises omgeving instelt om Vm's van VMware te repliceren naar Azure met Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ff01aed92669acb193ff149ea9298550134f42a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257054"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>De bronomgeving voor VMware instellen voor Azure-replicatie

In dit artikel wordt beschreven hoe u uw on-premises bronomgeving instelt om Vm's van VMware te repliceren met Azure. Het artikel bevat stappen voor het selecteren van uw replicatiescenario, het instellen van een on-premises machine als de siteherstelconfiguratieserver en het automatisch detecteren van on-premises VM's.

## <a name="prerequisites"></a>Vereisten

Het artikel gaat ervan uit dat je al hebt:

- Plan uw implementatie met behulp van [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Dit helpt u om voldoende bandbreedte toe te wijzen, op basis van uw dagelijkse data-change rate, om te voldoen aan uw gewenste recovery point objective (RPO).
- [Resources instellen](tutorial-prepare-azure.md) in de [Azure-portal](https://portal.azure.com).
- [Stel on-premises VMware in,](vmware-azure-tutorial-prepare-on-premises.md)inclusief een speciaal account voor automatische detectie.

## <a name="choose-your-protection-goals"></a>Kies uw beschermingsdoelen

1. Selecteer de naam van de kluis in **Recovery Services-kluizen**. We gebruiken **ContosoVMVault** voor dit scenario.
2. Selecteer in **Aan de slag** Site Recovery. Selecteer vervolgens **Infrastructuur voorbereiden**.
3. Selecteer **On-premises**in **Beschermingsdoel** > **Waar uw machines zich bevinden.**
4. In **Waarnaartoe wilt u de machines repliceren** selecteert u **Naar Azure**.
5. In **Zijn de machines gevirtualiseerd** selecteert **Ja, met VMware vSphere Hypervisor**. Selecteer vervolgens **OK**.

## <a name="set-up-the-configuration-server"></a>De configuratieserver instellen

U de configuratieserver instellen als een on-premises VMware VM via een OVA-sjabloon (Open Virtualization Application). [Meer informatie](concepts-vmware-to-azure-architecture.md) over de onderdelen die op de VMware VM worden geïnstalleerd.

1. Meer informatie over de [vereisten](vmware-azure-deploy-configuration-server.md#prerequisites) voor het implementeren van configuratieservers.
2. [Controleer capaciteitsnummers](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) voor implementatie.
3. [Download](vmware-azure-deploy-configuration-server.md#download-the-template) en [importeer](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) de OVA-sjabloon om een on-premises VMware VM in te stellen waarop de configuratieserver wordt uitgevoerd. De licentie bij de sjabloon is een evaluatielicentie en is 180 dagen geldig. Na deze periode moet de klant de vensters activeren met een aangeschafte licentie.
4. Schakel de VMware VM in en [registreer deze](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) in de kluis Van Recovery Services.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Uitsluitingen van Azure Site Recovery-mappen van antivirusprogramma

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Als antivirussoftware actief is op de bronmachine

Als de bronmachine een antivirussoftware actief heeft, moet de installatiemap worden uitgesloten. Sluit map *C:\ProgramData\ASR\agent* dus uit voor vloeiende replicatie.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Als antivirussoftware actief is op de configuratieserver

Volgende mappen uitsluiten van antivirussoftware voor vloeiende replicatie en om verbindingsproblemen te voorkomen

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\thirdparty
  - C:\temp
  - C:\aardbei
  - C:\ProgramData\MySQL
  - C:\Program Files (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Site Recovery-serverinstallatiemap. Bijvoorbeeld: E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Als antivirussoftware actief is bij scale-out Process server/Master Target

Volgende mappen uitsluiten van antivirussoftware

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery load balanced process server installation directory, Example: C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Volgende stappen
[Uw doelomgeving instellen](./vmware-azure-set-up-target.md) 
