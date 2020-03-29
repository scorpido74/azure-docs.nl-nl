---
title: Virtuele machines maken en beheren in DevTest Labs met Azure CLI
description: Meer informatie over het gebruik van Azure DevTest Labs om virtuele machines te maken en te beheren met Azure CLI
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167056"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Virtuele machines maken en beheren met DevTest Labs met behulp van de Azure CLI
Deze quickstart begeleidt u bij het maken, starten, verbinden, bijwerken en opruimen van een ontwikkelmachine in uw lab. 

Voordat u begint:

* Als er geen lab is gemaakt, zijn [hier](devtest-lab-create-lab.md)instructies te vinden.

* [Installeer de Azure CLI](/cli/azure/install-azure-cli). Voer az-login om te beginnen uit om een verbinding met Azure te maken. 

## <a name="create-and-verify-the-virtual-machine"></a>De virtuele machine maken en verifiëren 
Voordat u de gerelateerde opdrachten van DevTest Labs uitvoert, stelt u de juiste Azure-context in met de `az account set` opdracht:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

De opdracht om een virtuele `az lab vm create`machine te maken is: . De resourcegroep voor het lab, de naam van het lab en de naam van de virtuele machine zijn allemaal vereist. De rest van de argumenten veranderen afhankelijk van het type virtuele machine.

Met de volgende opdracht wordt een Windows-afbeelding gemaakt van Azure Market Place. De naam van de afbeelding is hetzelfde als wanneer u een virtuele machine maakt met behulp van de Azure-portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Met de volgende opdracht wordt een virtuele machine gemaakt op basis van een aangepaste afbeelding die beschikbaar is in het lab:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Het argument **afbeeldingstype** is gewijzigd van **galerie** naar **aangepast**. De naam van de afbeelding komt overeen met wat u ziet als u de virtuele machine in de Azure-portal wilt maken.

Met de volgende opdracht wordt een VM gemaakt op basis van een marketplace-afbeelding met ssh-verificatie:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

U ook virtuele machines maken op basis van formules door de parameter **image-type** in te stellen op **formule.** Als u een specifiek virtueel netwerk voor uw virtuele machine moet kiezen, gebruikt u de **vnet-naam** en **subnetparameters.** Zie az [lab vm create voor](/cli/azure/lab/vm#az-lab-vm-create)meer informatie.

## <a name="verify-that-the-vm-is-available"></a>Controleer of de vm beschikbaar is.
Gebruik `az lab vm show` de opdracht om te controleren of de virtuele machine beschikbaar is voordat u deze start en verbinding maakt. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Start en maak verbinding met de virtuele machine
In de volgende opdracht wordt een vm gestart:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Verbinding maken met een vm: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) of [Extern bureaublad](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>De virtuele machine bijwerken
Met de volgende voorbeeldopdracht worden artefacten op een vm toepast:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Lijst artefacten beschikbaar in het lab

Als u artefacten wilt weergeven die beschikbaar zijn in een vm in een lab, voert u de volgende opdrachten uit.

**Cloud Shell - PowerShell**: let op\`het gebruik van de backtick ( ) vóór de $ in $expand (d.w.z. '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: let\\op het gebruik van de slash ( ) karakter in de voorkant van $ in het commando. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Voorbeelduitvoer: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>De virtuele machine stoppen en verwijderen    
Met de volgende voorbeeldopdracht wordt een vm gestopt.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Een vm verwijderen.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende inhoud: [Azure CLI-documentatie voor Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
