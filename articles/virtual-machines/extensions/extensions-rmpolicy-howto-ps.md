---
title: Azure-beleid gebruiken om de installatie van VM-extensies te beperken
description: Gebruik Azure Policy om extensie-implementaties te beperken.
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
ms.openlocfilehash: 428db340ce43463939ce71ffadd4188060f3e732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073114"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Azure-beleid gebruiken om de installatie van extensies op Windows VM's te beperken

Als u het gebruik of de installatie van bepaalde extensies op uw Windows VM's wilt voorkomen, u een Azure-beleid maken met PowerShell om extensies voor VM's binnen een resourcegroep te beperken. 

Deze zelfstudie maakt gebruik van Azure PowerShell in de Cloud Shell, die voortdurend wordt bijgewerkt naar de nieuwste versie. 

 

## <a name="create-a-rules-file"></a>Een regelsbestand maken

Om te beperken welke extensies kunnen worden geïnstalleerd, moet u een [regel](../../governance/policy/concepts/definition-structure.md#policy-rule) hebben om de logica te bieden om de extensie te identificeren.

In dit voorbeeld ziet u hoe u extensies weigeren die door 'Microsoft.Compute' zijn gepubliceerd door een regelsbestand in Azure Cloud Shell te maken, maar als u lokaal in PowerShell werkt, u ook een lokaal bestand maken en het pad ($home/clouddrive) vervangen door het pad naar het lokale bestand op uw machine.

Typ in een [Cloud Shell:](https://shell.azure.com/powershell)

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Kopieer en plak de volgende .json in het bestand.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Als u klaar bent, drukt u op **Ctrl + O** en vervolgens op **Enter** om het bestand op te slaan. Druk **op Ctrl + X** om het bestand en de uitgang te sluiten.

## <a name="create-a-parameters-file"></a>Een parametersbestand maken

U hebt ook een [parametersbestand](../../governance/policy/concepts/definition-structure.md#parameters) nodig dat een structuur maakt die u gebruiken om in een lijst met extensies te worden doorstaan. 

In dit voorbeeld ziet u hoe u een parametersbestand voor VM's maakt in Cloud Shell, maar als u lokaal in PowerShell werkt, u ook een lokaal bestand maken en het pad ($home/clouddrive) vervangen door het pad naar het lokale bestand op uw machine.

Typ in [Cloud Shell:](https://shell.azure.com/powershell)

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Kopieer en plak de volgende .json in het bestand.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Als u klaar bent, drukt u op **Ctrl + O** en vervolgens op **Enter** om het bestand op te slaan. Druk **op Ctrl + X** om het bestand en de uitgang te sluiten.

## <a name="create-the-policy"></a>Beleid maken

Een beleidsdefinitie is een object dat wordt gebruikt om de configuratie op te slaan die u wilt gebruiken. De beleidsdefinitie gebruikt de regels en parametersbestanden om het beleid te definiëren. Maak een beleidsdefinitie met de cmdlet [Nieuw-AzPolicyDefinition.](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition)

 De beleidsregels en parameters zijn de bestanden die u hebt gemaakt en opgeslagen als .json-bestanden in uw cloudshell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Wijs het beleid toe

In dit voorbeeld wordt het beleid toegewezen aan een resourcegroep met [nieuw-azpolicyassignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Vm's die zijn gemaakt in de **brongroep myResourceGroup,** kunnen de VM Access Agent- of Custom Script-extensies niet installeren. 

Gebruik het [Get-AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) cmdlet om uw abonnements-ID te gebruiken in plaats van de ene in het voorbeeld.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Het beleid testen

Als u het beleid wilt testen, probeert u de VM Access-extensie te gebruiken. Het volgende moet mislukken met het bericht "Set-AzVMAccessExtension: Resource 'myVMAccess' is niet toegestaan door het beleid."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

In de portal moet de wachtwoordwijziging mislukken met de implementatie van de sjabloon mislukt vanwege beleidsschending. .

## <a name="remove-the-assignment"></a>De toewijzing verwijderen

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Het beleid verwijderen

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Volgende stappen
Zie [Azure Policy](../../governance/policy/overview.md) voor meer informatie.
