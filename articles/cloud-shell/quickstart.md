---
title: Azure Cloud Shell Snelstartgids-bash
description: Meer informatie over het gebruik van de bash-opdracht regel in uw browser met Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 574841b3a89385a3b8bf048d5ed36f40fac99a83
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252127"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Quick start voor bash in Azure Cloud Shell

Dit document bevat informatie over het gebruik van bash in Azure Cloud Shell in het [Azure Portal](https://ms.portal.azure.com/).

> [!NOTE]
> Er is ook een [Power shell in azure Cloud shell](quickstart-powershell.md) Quick Start beschikbaar.

## <a name="start-cloud-shell"></a>Cloud Shell starten
1. Start **Cloud shell** vanaf de bovenste navigatie van het Azure Portal. <br>
![](media/quickstart/shell-icon.png)

2. Selecteer een abonnement om een opslag account en Microsoft Azure bestands share te maken.
3. Selecteer opslag maken

> [!TIP]
> U wordt in elke sessie automatisch geverifieerd voor Azure CLI.

### <a name="select-the-bash-environment"></a>De bash omgeving selecteren
Controleer of de vervolg keuzelijst omgeving van de linkerkant van het shell venster `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Uw abonnement instellen
1. Abonnementen weer geven waartoe u toegang hebt.
   ```azurecli-interactive
   az account list
   ```

2. Stel uw voorkeurs abonnement in: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Uw abonnement wordt onthouden voor toekomstige sessies met `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een nieuwe resource groep in Westus met de naam ' MyRG '.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Een Linux-VM maken
Maak een Ubuntu-VM in de nieuwe resource groep. Met de Azure CLI worden SSH-sleutels gemaakt en wordt de virtuele machine ingesteld. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Het gebruik van `--generate-ssh-keys` geeft Azure CLI de opdracht om open bare en persoonlijke sleutels te maken en in te stellen in uw virtuele machine en `$Home` map. Standaard sleutels worden in Cloud Shell op `/home/<user>/.ssh/id_rsa` en `/home/<user>/.ssh/id_rsa.pub`geplaatst. Uw `.ssh` map is opgeslagen in de afbeelding van de bestands share van 5 GB die wordt gebruikt om `$Home`persistent te maken.

Uw gebruikers naam op deze VM is uw gebruikers naam die wordt gebruikt in Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH in uw virtuele Linux-machine
1. Zoek de naam van uw virtuele machine in de zoek balk Azure Portal.
2. Klik op verbinding maken om de naam van uw virtuele machine en het open bare IP-adres op te halen. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH in uw virtuele machine met de `ssh` cmd.
   ```
   ssh username@ipaddress
   ```

Wanneer u de SSH-verbinding tot stand brengt, wordt de welkomst prompt van Ubuntu weer gegeven. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Opschonen 
1. Sluit uw SSH-sessie af.
   ```azurecli-interactive
   exit
   ```

2. Verwijder de resource groep en alle resources hierin.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het persistent maken van bestanden voor bash in Cloud Shell](persisting-shell-storage.md) <br>
[Meer informatie over Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Meer informatie over Azure Files opslag](../storage/files/storage-files-introduction.md) <br>
