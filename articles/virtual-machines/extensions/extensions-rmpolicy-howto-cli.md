---
title: Azure-beleid gebruiken om de installatie van VM-extensies te beperken
description: Gebruik Azure Policy om implementaties van VM-extensies te beperken.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 3c660f7e05af43c2aad6f7283e32cfc1d85571ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066832"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Azure-beleid gebruiken om de installatie van extensies op Linux VM's te beperken

Als u het gebruik of de installatie van bepaalde extensies op uw Linux-VM's wilt voorkomen, u een Azure-beleid maken met behulp van de CLI om extensies voor VM's binnen een resourcegroep te beperken. 

Deze zelfstudie maakt gebruik van de CLI in de Azure Cloud Shell, die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Azure CLI lokaal wilt uitvoeren, moet u versie 2.0.26 of hoger installeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Een regelsbestand maken

Om te beperken welke extensies kunnen worden geïnstalleerd, moet u een [regel](../../governance/policy/concepts/definition-structure.md#policy-rule) hebben om de logica te bieden om de extensie te identificeren.

In dit voorbeeld ziet u hoe u het installeren van extensies die zijn gepubliceerd door 'Microsoft.OSTCExtensions' weigeren door een regelsbestand in Azure Cloud Shell te maken, maar als u lokaal in CLI werkt, u ook een lokaal bestand maken en het pad (~/clouddrive) vervangen door het pad naar het lokale bestand op uw machine.

In een [bash Cloud Shell,](https://shell.azure.com/bash)type:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Kopieer en plak de volgende .json in het bestand.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Wanneer u klaar bent, drukt u op de **Esc-toets** en typt u **:wq** om het bestand op te slaan en te sluiten.


## <a name="create-a-parameters-file"></a>Een parametersbestand maken

U hebt ook een [parametersbestand](../../governance/policy/concepts/definition-structure.md#parameters) nodig dat een structuur maakt die u gebruiken om in een lijst met extensies te worden doorstaan. 

In dit voorbeeld ziet u hoe u een parametersbestand voor Linux-VM's maakt in Cloud Shell, maar als u lokaal in CLI werkt, u ook een lokaal bestand maken en het pad (~/clouddrive) vervangen door het pad naar het lokale bestand op uw machine.

In de [bash Cloud Shell,](https://shell.azure.com/bash)type:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Kopieer en plak de volgende .json in het bestand.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Wanneer u klaar bent, drukt u op de **Esc-toets** en typt u **:wq** om het bestand op te slaan en te sluiten.

## <a name="create-the-policy"></a>Beleid maken

Een beleidsdefinitie is een object dat wordt gebruikt om de configuratie op te slaan die u wilt gebruiken. De beleidsdefinitie gebruikt de regels en parametersbestanden om het beleid te definiëren. Maak de beleidsdefinitie met behulp van [az-beleidsdefinitie maken](/cli/azure/role/assignment?view=azure-cli-latest).

In dit voorbeeld zijn de regels en parameters de bestanden die u hebt gemaakt en opgeslagen als .json-bestanden in uw cloudshell.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Wijs het beleid toe

In dit voorbeeld wordt het beleid toegewezen aan een resourcegroep met behulp van [az-beleidstoewijzing maken](/cli/azure/policy/assignment). Vm's die zijn gemaakt in de **brongroep myResourceGroup,** kunnen de Linux VM Access of de Custom Script-extensies voor Linux niet installeren. De resourcegroep moet bestaan voordat u het beleid toewijzen.

Gebruik [de lijst met az-account](/cli/azure/account?view=azure-cli-latest) om uw abonnements-ID te gebruiken in plaats van de lijst in het voorbeeld.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Het beleid testen

Test het beleid door een nieuwe virtuele machine te maken en te proberen een nieuwe gebruiker toe te voegen.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Probeer een nieuwe gebruiker met de naam **myNewUser** te maken met behulp van de VM Access-extensie.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>De toewijzing verwijderen

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Het beleid verwijderen

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Policy](../../governance/policy/overview.md) voor meer informatie.