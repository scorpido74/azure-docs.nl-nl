---
title: Azure-beleid implementeren voor gedelegeerde abonnementen op schaal
description: Lees hoe azure gedelegeerd resourcebeheer u een beleidsdefinitie en beleidstoewijzing implementeren voor meerdere tenants.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 9015351c3fc8f374c5ce85712907fa05249cde11
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984569"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Azure-beleid implementeren voor gedelegeerde abonnementen op schaal

Als serviceprovider hebt u mogelijk meerdere klanttenants aan boord genomen voor Azure-gedelegeerd bronbeheer. [Azure Lighthouse](../overview.md) stelt serviceproviders in staat om bewerkingen op schaal uit te voeren voor meerdere tenants tegelijk, waardoor beheertaken efficiënter worden.

In dit onderwerp ziet u hoe u [Azure Policy](../../governance/policy/index.yml) gebruiken om een beleidsdefinitie en beleidstoewijzing te implementeren voor meerdere tenants met PowerShell-opdrachten. In dit voorbeeld zorgt de beleidsdefinitie ervoor dat opslagaccounts worden beveiligd door alleen HTTPS-verkeer toe te staan.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Azure Resource Graph gebruiken om query's te maken tussen clienttenants

U [Azure Resource Graph](../../governance/resource-graph/index.yml) gebruiken om alle abonnementen in de klanttenants die u beheert, te bevragen. In dit voorbeeld identificeren we opslagaccounts in deze abonnementen waarvoor momenteel geen HTTPS-verkeer nodig is.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Een beleid implementeren voor meerdere klanttenants

In het onderstaande voorbeeld ziet u hoe u een [Azure Resource Manager-sjabloon](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) gebruiken om een beleidsdefinitie en beleidstoewijzing te implementeren voor gedelegeerde abonnementen in meerdere klanttenants. Deze beleidsdefinitie vereist dat alle opslagaccounts HTTPS-verkeer gebruiken, waardoor het maken van nieuwe opslagaccounts die niet voldoen en het markeren van bestaande opslagaccounts wordt voorkomen zonder dat de instelling als niet-compatibel is.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>De beleidsimplementatie valideren

Nadat u de sjabloon Azure Resource Manager hebt geïmplementeerd, u bevestigen dat de beleidsdefinitie is toegepast door te proberen een opslagaccount te maken met **EnableHttpsTrafficOnly** ingesteld op **false** in een van uw gedelegeerde abonnementen. Vanwege de beleidstoewijzing u dit opslagaccount niet maken.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, verwijdert u de beleidsdefinitie en -toewijzing die door de implementatie is gemaakt.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure-beleid](../../governance/policy/index.yml).
- Meer informatie over [cross-tenant management ervaringen](../concepts/cross-tenant-management-experience.md).
