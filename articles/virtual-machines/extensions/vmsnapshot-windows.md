---
title: Windows-extensie voor VM-moment opname voor Azure Backup
description: Toepassings consistente back-up van de virtuele machine maken van Azure Backup met behulp van de extensie van de VM-moment opname
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: c4f5650e333c209d3b5f342c1f1c61ec1ca5cf47
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264285"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Windows-extensie voor VM-moment opname voor Azure Backup

Azure Backup biedt ondersteuning voor het maken van back-ups van werk belastingen van on-premises naar de Cloud en het maken van back-ups van Cloud bronnen naar Recovery Services kluis. Azure Backup maakt gebruik van VM snap shot extension om een toepassings consistente back-up te maken van de virtuele machine van Azure, zonder dat u de VM hoeft te afsluiten. De extensie van de VM-moment opname wordt gepubliceerd en ondersteund door micro soft als onderdeel van Azure Backup service. Azure Backup wordt de uitbrei ding geïnstalleerd als onderdeel van de eerste geplande back-up geactiveerd na het inschakelen van back-ups. In dit document vindt u informatie over de ondersteunde platforms, configuraties en implementatie opties voor de VM-momentopname extensie.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem
Zie [besturings systemen die worden ondersteund door Azure backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start) voor een lijst met ondersteunde besturings systemen

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de extensie van de VM-moment opname. Voor de uitbrei ding is de taak-ID vereist. Hiermee wordt de back-uptaak geïdentificeerd waarmee de moment opname op de virtuele machine wordt geactiveerd. de status BLOB-URI: de status van de momentopname bewerking wordt geschreven, de geplande begin tijd van de moment opname, logboeken van de BLOB-URI-waarbij logboeken corresponderen met de momentopname taak zijn geschreven, objstr-weer gave van VM-schijven en meta gegevens.  Omdat deze instellingen moeten worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in een configuratie met een beveiligde instelling. Azure-VM-extensie beveiligde instellingsgegevens versleuteld en alleen op de virtuele doelmachine worden ontsleuteld. Houd er rekening mee dat deze instellingen worden aanbevolen van Azure Backup Service alleen worden door gegeven als onderdeel van de back-uptaak.

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

### <a name="property-values"></a>Waarden van eigenschappen

| Name | Waarde / voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | string |
| commandStartTimeUTCTicks | 6.36458E+17 | string |
| landinstellingen | nl-nl | string |
| objectStr | Code ring van SAS URI array-"blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/vhd's\/vmwin1404ltsc201652903941. VHD? SV = 2014-02-14 & SR = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW", "https:\/\/sopattna8461.blob.core.windows.net\/vhd's\/vmwin1404ltsc-20160629-122418. VHD? SV = 2014-02-14 & SR = b & sig = 5S0A6YDWvVwqPAkzWXVy% 2BS% 2FqMwzFMbamT5upwx05v8Q% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541. VHD? SV = 2014-02-14 & SR = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna5365.blob.core.windows.net\/vhd's\/vmwin1404ltsc-20160701-163922. VHD? SV = 2014-02-14 & SR = b & sig = oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r% 2BC% 2BNIAork% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "," https:\/\/sopattna5365.blob.core.windows.net\/vhd's\/vmwin1404ltsc-20170705-124311. VHD? SV = 2014-02-14 & SR = b & sig = ZUM9d28Mvvm% 2FfrhJ71TFZh0Ni90m38bBs3zMl% 2FQ9rs0% 3D & St = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & SP = RW "] | string |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string |



## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. De aanbevolen manier om een VM-momentopname extensie toe te voegen aan een virtuele machine is echter door back-up op de virtuele machine in te scha kelen. Dit kan worden bereikt via een resource manager-sjabloon.  Een voor beeld van een resource manager-sjabloon waarmee back-ups op een virtuele machine kunnen worden gemaakt, vindt u in de [Azure Quick Start-galerie](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Azure CLI-implementatie

De Azure CLI kan worden gebruikt om back-ups op een virtuele machine in te scha kelen. Post enable backup, eerste geplande back-uptaak installeert de VM snap shot-extensie op de VM.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als wilt zien de implementatiestatus van extensies voor een bepaalde virtuele machine, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Extensie uitvoering uitvoer wordt vastgelegd in het volgende bestand:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Foutcodes en hun betekenis

Informatie over het oplossen van problemen vindt u in de [hand leiding voor het oplossen van back-ups van Azure VM](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Ondersteuning

Als u hulp nodig hebt op elk gewenst moment in dit artikel, u kunt contact opnemen met de Azure-experts op het [forums voor Azure MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U kunt ook een Azure-ondersteuning-incident indienen. Ga naar de [ondersteuning van Azure site](https://azure.microsoft.com/support/options/) en selecteer Get-ondersteuning. Voor meer informatie over het gebruik van ondersteuning voor Azure, de [Veelgestelde vragen over Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/).
