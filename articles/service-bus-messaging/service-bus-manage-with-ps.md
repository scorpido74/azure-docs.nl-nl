---
title: PowerShell gebruiken om Azure Service Bus-bronnen te beheren | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u azure PowerShell-module gebruiken om servicebusentiteiten te maken en te beheren (naamruimten, wachtrijen, onderwerpen, abonnementen).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e333dfb109840538fd5dec8110e1c32adedce989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759258"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>PowerShell gebruiken om servicebusbronnen te beheren

Microsoft Azure PowerShell is een scriptomgeving die u gebruiken om de implementatie en het beheer van Azure-services te beheren en te automatiseren. In dit artikel wordt beschreven hoe u de [PowerShell-module Service Bus](/powershell/module/az.servicebus) gebruiken voor het inrichten en beheren van servicebusentiteiten (naamruimten, wachtrijen, onderwerpen en abonnementen) met behulp van een lokale Azure PowerShell-console of -script.

U servicebus-entiteiten ook beheren met Azure Resource Manager-sjablonen. Zie het artikel [Servicebusbronnen maken met Azure Resource Manager-sjablonen](service-bus-resource-manager-overview.md)voor meer informatie.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u de volgende vereisten nodig:

* Een Azure-abonnement. Zie [aankoopopties][purchase options], [ledenaanbiedingen][member offers]of [een gratis account][free account]voor meer informatie over het verkrijgen van een abonnement .
* Een computer met Azure PowerShell. Zie Aan [de slag met Azure PowerShell-cmdlets voor](/powershell/azure/get-started-azureps)instructies .
* Een algemeen begrip van PowerShell-scripts, NuGet-pakketten en het .NET Framework.

## <a name="get-started"></a>Aan de slag

De eerste stap is om PowerShell te gebruiken om in te loggen op uw Azure-account en Azure-abonnement. Volg de instructies in [Aan de slag met Azure PowerShell-cmdlets](/powershell/azure/get-started-azureps) om in te loggen op uw Azure-account en de bronnen in uw Azure-abonnement op te halen en te openen.

## <a name="provision-a-service-bus-namespace"></a>Een naamruimte voor een servicebus inrichten

Wanneer u met naamruimten van servicebus werkt, u de cmdlets [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [Nieuw-AzServiceBusNamespace,](/powershell/module/az.servicebus/new-azservicebusnamespace) [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)en [Set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) gebruiken.

In dit voorbeeld worden enkele lokale variabelen in het script gemaakt. `$Namespace` en `$Location`.

* `$Namespace`is de naam van de Service Bus namespace waarmee we willen werken.
* `$Location`identificeert het datacenter waarin we de naamruimte inrichten.
* `$CurrentNamespace`hiermee wordt de referentienaamruimte opgeslagen die we ophalen (of maken).

In een echt `$Namespace` `$Location` script, en kan worden doorgegeven als parameters.

Dit deel van het script doet het volgende:

1. Probeert een naamruimte van een servicebus met de opgegeven naam op te halen.
2. Als de naamruimte wordt gevonden, wordt gemeld wat er is gevonden.
3. Als de naamruimte niet wordt gevonden, wordt de naamruimte gemaakt en wordt de nieuw gemaakte naamruimte opgehaald.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Een autorisatieregel voor naamruimte maken

In het volgende voorbeeld ziet u hoe u autorisatieregels voor naamruimte beheert met behulp van de cmdlets [Nieuw-AzServiceAutorisatieregel](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)en [Remove-AzServiceBusAuthorizationRule.](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule)

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Een wachtrij maken

Als u een wachtrij of onderwerp wilt maken, voert u een naamruimtecontrole uit met behulp van het script in de vorige sectie. Maak vervolgens de wachtrij:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Wachtrijeigenschappen wijzigen

Nadat u het script in de vorige sectie hebt uitgevoerd, u de cmdlet [Set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) gebruiken om de eigenschappen van een wachtrij bij te werken, zoals in het volgende voorbeeld:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Inrichten van andere servicebusentiteiten

U de [PowerShell-module ServiceBus](/powershell/module/az.servicebus) gebruiken om andere entiteiten in te richten, zoals onderwerpen en abonnementen. Deze cmdlets zijn syntactisch vergelijkbaar met de wachtrij creatie cmdlets aangetoond in de vorige sectie.

## <a name="next-steps"></a>Volgende stappen

- Bekijk hier de volledige documentatie van de PowerShell-module van Service Bus Resource [Manager.](/powershell/module/az.servicebus) Op deze pagina vindt u alle beschikbare cmdlets.
- Zie het artikel [Servicebusbronnen maken met Azure Resource Manager-sjablonen](service-bus-resource-manager-overview.md)voor informatie over het gebruik van Azure Resource Manager-sjablonen.
- Informatie over [servicebus .NET-beheerbibliotheken](service-bus-management-libraries.md).

Er zijn een aantal alternatieve manieren om servicebusentiteiten te beheren, zoals beschreven in deze blogberichten:

* [Wachtrijen, onderwerpen en abonnementen van ServiceBus maken met een PowerShell-script](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Een Service Bus Namespace en een Event Hub maken met een PowerShell-script](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Servicebus PowerShell-scripts](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
