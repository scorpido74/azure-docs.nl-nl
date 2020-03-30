---
title: VM Snapshot Windows-extensie voor Azure Backup
description: Neem een consistente back-up van de virtuele machine van Azure Backup met behulp van VM-momentopnameextensie
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 5ee3b97eb835322a0ad6c8a69c20776a243713fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415110"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>VM Snapshot Windows-extensie voor Azure Backup

Azure Backup biedt ondersteuning voor het maken van back-ups van on-premises tot cloud en het maken van back-ups van cloudresources naar vault Recovery Services. Azure Backup maakt gebruik van VM-momentopnameextensie om een consistente back-up van de azure-virtuele machine te maken zonder dat de VM hoeft te worden afgesloten. VM Snapshot-extensie wordt gepubliceerd en ondersteund door Microsoft als onderdeel van azure backup-service. Azure Backup installeert de extensie als onderdeel van de eerste geplande back-up geactiveerde post inschakelen back-up. In dit document worden de ondersteunde platforms, configuraties en implementatieopties voor de VM Snapshot-extensie beschreven.

De VMSnapshot-extensie wordt alleen in de Azure-portal weergegeven voor niet-beheerde VM's.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem
Raadpleeg voor een lijst met ondersteunde besturingssystemen [naar besturingssystemen die worden ondersteund door Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de VM-momentopnameextensie weergegeven. De extensie vereist de taak-ID - dit identificeert de back-uptaak die momentopname op de VM heeft geactiveerd, statusblob uri - waarbij de status van de momentopnamebewerking is geschreven, geplande begintijd van de momentopname, logboeken blob uri - waar logboeken overeenkomen met momentopnametaak zijn geschreven, objstr- vertegenwoordiging van VM-schijven en metagegevens.  Omdat deze instellingen moeten worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in een configuratie met beveiligde instellingen. Azure VM-extensiebeveiligde instellingsgegevens worden versleuteld en alleen gedecodeerd op de virtuele doelmachine. Houd er rekening mee dat deze instellingen worden aanbevolen om alleen vanuit de Azure Backup-service te worden doorgegeven als onderdeel van back-uptaak.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| Taskid | e07354cf-041e-4370-929f-25a319ce8933_1 | tekenreeks |
| opdrachtStartTimeUTCTicks | 6.36458E+17 | tekenreeks |
| landinstellingen | nl-nl | tekenreeks |
| objectStr | Codering van sas uri array- "blobSASUri":\/\/\/["https: sopattna5365.blob.core.windows.net vhds\/vmwin1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTgbE6JpSWRLZxAdtA%3D&st=2017 11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw",\/\/"https: sopattna8461.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3eee\/vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBXMXMGIUrcycvhQSfjYvqK LeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", " https:\/\/\/\/sopattna5365.blob.core.windows.net vhds vmwin1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=o XvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T1 4%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&\/\/sp=sopattna5365.blob.core.windows.net\/\/2014-02-14&sr=b&=ZUM9d28m 2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw=rw"] | tekenreeks |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | tekenreeks |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | tekenreeks |



## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. De aanbevolen manier om een VM-momentopnameextensie toe te voegen aan een virtuele machine is echter door back-ups op de virtuele machine in te schakelen. Dit kan worden bereikt door middel van een Resource Manager-sjabloon.  Een voorbeeld sjabloon resourcebeheer waarmee back-up op een virtuele machine wordt ingeschakeld, is te vinden in de [Azure Quick Start Gallery.](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)


## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt om back-ups op een virtuele machine in te schakelen. Post enable back-up, eerste geplande back-up taak installeert de Vm snapshot extensie op de VM.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

De uitvoer van extensieuitvoering wordt vastgelegd in het volgende bestand:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Foutcodes en hun betekenissen

Informatie over probleemoplossing voor problemen oplossen kan worden gevonden in de [azure VM-hulpprogramma voor het oplossen van problemen met de back-up.](../../backup/backup-azure-vms-troubleshoot.md)

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
