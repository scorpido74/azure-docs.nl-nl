---
title: Azure Cloud Shell Quickstart - Bash
description: Meer informatie over het gebruik van de opdrachtregel Bash in uw browser met Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 58d795a5aee79e4149864a79a923ce34950b31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458066"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Snel aan de slag voor Bash in Azure Cloud Shell

In dit document wordt beschreven hoe u Bash in Azure Cloud Shell gebruikt in de [Azure-portal.](https://ms.portal.azure.com/)

> [!NOTE]
> Een [PowerShell in Azure Cloud Shell](quickstart-powershell.md) Quickstart is ook beschikbaar.

## <a name="start-cloud-shell"></a>Cloud Shell starten
1. Start **Cloud Shell** vanaf de bovenste navigatie van de Azure-portal. <br>
![](media/quickstart/shell-icon.png)

2. Selecteer een abonnement om een opslagaccount te maken en Microsoft Azure Files te delen.
3. Selecteer 'Opslag maken'

> [!TIP]
> U wordt automatisch geverifieerd voor Azure CLI in elke sessie.

### <a name="select-the-bash-environment"></a>De Bash-omgeving selecteren
Controleer of de omgeving drop-down van de linkerkant van shell venster zegt `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Uw abonnement instellen
1. Lijstabonnementen waartoe u toegang hebt.
   ```azurecli-interactive
   az account list
   ```

2. Stel uw voorkeursabonnement in: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Uw abonnement wordt onthouden voor `/home/<user>/.azure/azureProfile.json`toekomstige sessies met behulp van .

### <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een nieuwe resourcegroep in WestUS met de naam "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Een Linux-VM maken
Maak een Ubuntu VM in uw nieuwe brongroep. De Azure CLI maakt SSH-sleutels en stelt de VM ermee in. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Met `--generate-ssh-keys` behulp van azure CLI wordt azure CLI instrueert om openbare en privésleutels in uw vm en map te maken en `$Home` in te stellen. Standaard worden sleutels in Cloud `/home/<user>/.ssh/id_rsa` `/home/<user>/.ssh/id_rsa.pub`Shell geplaatst op en . Uw `.ssh` map blijft bestaan in de 5 GB-afbeelding van `$Home`uw bijgevoegde bestandsshare die wordt gebruikt om te blijven bestaan.

Uw gebruikersnaam op deze VM is uw gebruikersnaamUser@Azure:die wordt gebruikt in Cloud Shell ($ ).

### <a name="ssh-into-your-linux-vm"></a>SSH in uw Linux VM
1. Zoek naar uw VM-naam in de zoekbalk van azure-portal.
2. Klik op 'Verbinding maken' om uw VM-naam en openbaar IP-adres op te halen. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH in uw `ssh` VM met de cmd.
   ```
   ssh username@ipaddress
   ```

Bij het opzetten van de SSH-verbinding, moet u de Ubuntu welkomstprompt zien. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Opschonen 
1. Sluit je ssh-sessie af.
   ```
   exit
   ```

2. Verwijder uw resourcegroep en alle bronnen daarin.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over aanhoudende bestanden voor Bash in Cloud Shell](persisting-shell-storage.md) <br>
[Meer informatie over Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Meer informatie over Azure Files-opslag](../storage/files/storage-files-introduction.md) <br>
