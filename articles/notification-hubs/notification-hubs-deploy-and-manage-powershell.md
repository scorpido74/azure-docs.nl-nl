---
title: Berichtenhubs implementeren en beheren met PowerShell
description: Meldingshubs maken en beheren met PowerShell voor automatisering
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 863fdb445cce41f0fe4cbee63a3d6198c0a79339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264641"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Meldingenhubs implementeren en beheren met PowerShell

## <a name="overview"></a>Overzicht

In dit artikel ziet u hoe u Azure Notification Hubs maken en beheren met PowerShell. In dit artikel worden de volgende veelvoorkomende automatiseringstaken weergegeven.

- Een Notification Hub maken
- Referenties instellen

Zie [Servicebus beheren met PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)als u ook een nieuwe naamruimte voor servicebussen voor uw meldingshubs moet maken.

Het beheren van meldingenhubs wordt niet rechtstreeks ondersteund door de cmdlets die bij Azure PowerShell zijn inbegrepen. De beste benadering van PowerShell is om te verwijzen naar de Microsoft.Azure.NotificationHubs.dll-assemblage. De assemblage wordt gedistribueerd met het [NuGet-pakket van Microsoft Azure Notification Hubs.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Azure is een op abonnementen gebaseerd platform. Zie [Aankoopopties], [Aanbiedingen voor leden]of Gratis [proefabonnement]voor meer informatie over het verkrijgen van een abonnement.
- Een computer met Azure PowerShell. Zie Azure [PowerShell installeren en configureren]voor instructies.
- Een algemeen begrip van PowerShell-scripts, NuGet-pakketten en het .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inclusief een verwijzing naar de .NET-assemblage voor servicebus

Het beheren van Azure Notification Hubs is nog niet inbegrepen bij de PowerShell-cmdlets in Azure PowerShell. Als u meldingshubs wilt inrichten, u de .NET-client gebruiken die wordt geleverd in het [NuGet-pakket microsoft Azure Notification Hubs.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

Controleer eerst of uw script de **Microsoft.Azure.NotificationHubs.dll-assemblage** kan vinden, die is geïnstalleerd als een NuGet-pakket in een Visual Studio-project. Om flexibel te zijn, voert het script de volgende stappen uit:

1. Hiermee bepaalt u het pad waarop het is aangeroepen.
2. Hiermee wordt het pad doorkruisen `packages`totdat er een map met de naam wordt gevonden. Deze map wordt gemaakt wanneer u NuGet-pakketten installeert voor Visual Studio-projecten.
3. Recursief zoekt in `packages` de map `Microsoft.Azure.NotificationHubs.dll`naar een verzameling met de naam .
4. Verwijst naar de assemblage, zodat de typen beschikbaar zijn voor later gebruik.

U volgt de manier waarop deze stappen worden geïmplementeerd in een PowerShell-script:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>De `NamespaceManager` klasse maken

Als u meldingshubs wilt inrichten, maakt u een instantie van de klasse [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) van de SDK.

U de cmdlet [Get-AzureSBAuthorizationRule] die bij Azure PowerShell is meegeleverd, gebruiken om een autorisatieregel op te halen die wordt gebruikt om een verbindingstekenreeks op te geven. Een verwijzing `NamespaceManager` naar de instantie `$NamespaceManager` wordt opgeslagen in de variabele. `$NamespaceManager`wordt gebruikt om een meldingshub in te richten.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Een nieuwe meldingshub inrichten

Als u een nieuwe meldingshub wilt inrichten, gebruikt u de [.NET API voor meldingshubs].

In dit deel van het script stelt u vier lokale variabelen in.

1. `$Namespace`: Stel dit in op de naam van de naamruimte waar u een meldingshub wilt maken.
2. `$Path`: Stel dit pad in op de naam van de nieuwe meldingshub.  Bijvoorbeeld "MyHub".
3. `$WnsPackageSid`: Stel dit in op het pakket SID voor uw Windows-app vanuit het [Windows Dev Center.](https://developer.microsoft.com/en-us/windows)
4. `$WnsSecretkey`: Stel dit in op de geheime sleutel voor uw Windows-app vanuit het [Windows Dev Center.](https://developer.microsoft.com/en-us/windows)

Deze variabelen worden gebruikt om verbinding te maken met uw naamruimte en een nieuwe meldingshub te maken die is geconfigureerd voor het verwerken van WNS-meldingen (Windows Notification Services) met WNS-referenties voor een Windows-app. Voor informatie over het verkrijgen van het pakket SID en geheime sleutel zie, de [aan de slag met Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tutorial.

- Het scriptfragment `NamespaceManager` gebruikt het object om te controleren `$Path` of de meldingshub die is geïdentificeerd, bestaat.
- Als het niet bestaat, `NotificationHubDescription` maakt het script met WNS-referenties en geeft het door aan de `NamespaceManager` klassemethode. `CreateNotificationHub`

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Aanvullende resources

- [Service Bus met PowerShell beheren](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Wachtrijen, onderwerpen en abonnementen van ServiceBus maken met een PowerShell-script](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Een Service Bus Namespace en een Event Hub maken met een PowerShell-script](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Sommige kant-en-klare scripts zijn ook beschikbaar om te downloaden:

- [Servicebus PowerShell-scripts](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Aankoopopties]: https://azure.microsoft.com/pricing/purchase-options/
[Aanbiedingen voor leden]: https://azure.microsoft.com/pricing/member-offers/
[Gratis proefperiode]: https://azure.microsoft.com/pricing/free-trial/
[Azure PowerShell installeren en configureren]: /powershell/azureps-cmdlets-docs
[.NET API voor meldingshubs]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
