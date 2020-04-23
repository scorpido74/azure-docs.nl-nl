---
title: 'Een Azure Virtual Machine maken en beheren met C #'
description: Gebruik C# en Azure Resource Manager om een virtuele machine en al zijn ondersteunende resources te implementeren.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 07c66b2955f3df1ffae1a0cb0c2b0888bdc790e9
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082880"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Windows VM's maken en beheren in Azure met C # #

Een [Azure Virtual Machine](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) heeft verschillende ondersteunende Azure-bronnen nodig. In dit artikel wordt vm-resources maken, beheren en verwijderen met C#. Procedures voor:

> [!div class="checklist"]
> * Een Visual Studio-project maken
> * Het pakket installeren
> * Referenties maken
> * Resources maken
> * Beheertaken uitvoeren
> * Resources verwijderen
> * De toepassing uitvoeren

Het duurt ongeveer 20 minuten om deze stappen te doen.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

1. Als u dit nog niet hebt gedaan, installeert u [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecteer **.NET-bureaubladontwikkeling** op de pagina Workloads en klik op **Installeren**. In het overzicht u zien dat **.NET Framework 4 - 4.6 ontwikkeltools** automatisch voor u worden geselecteerd. Als u Visual Studio al hebt geïnstalleerd, u de .NET-werkbelasting toevoegen met behulp van de Visual Studio Launcher.
2. Klik in Visual**New** > Studio op Nieuw**project** **bestand** > .
3. Selecteer **in Sjablonen** > **Visual C#** de optie Console App **(.NET Framework),** voer *myDotnetProject* in voor de naam van het project, selecteer de locatie van het project en klik op **OK**.

## <a name="install-the-package"></a>Het pakket installeren

NuGet-pakketten zijn de eenvoudigste manier om de bibliotheken te installeren die u nodig hebt om deze stappen te voltooien. Ga als volgt te werk om de bibliotheken te krijgen die u nodig hebt in Visual Studio:

1. Klik **op Extra** > **Nuget Package Manager**en klik vervolgens op Package Manager **Console**.
2. Typ deze opdracht in de console:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Referenties maken

Voordat u met deze stap begint, moet u ervoor zorgen dat u toegang hebt tot een [Active Directory-serviceprincipal.](../../active-directory/develop/howto-create-service-principal-portal.md) U moet ook de toepassings-ID, de verificatiesleutel en de tenant-id registreren die u in een latere stap nodig hebt.

### <a name="create-the-authorization-file"></a>Het autorisatiebestand maken

1. Klik in Solution Explorer met de rechtermuisknop op *myDotnetProject* > Nieuw**item****toevoegen** > en selecteer **vervolgens Tekstbestand** in Visual *C# Items*. Geef de eigenschappen van bestand *azureauth.en*klik op **Toevoegen**.
2. Voeg deze autorisatie-eigenschappen toe:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Vervang ** &lt;abonnements-id&gt; ** door uw abonnements-id, ** &lt;toepassings-id&gt; ** door de Active ** &lt;Directory-toepassingsid, verificatiesleutel&gt; ** door de toepassingssleutel en ** &lt;tenant-id&gt; ** door de tenant-id.

3. Sla het bestand azureauth.properties op. 
4. Stel een omgevingsvariabele in Windows met de naam AZURE_AUTH_LOCATION met het volledige pad naar autorisatiebestand dat u hebt gemaakt. De volgende PowerShell-opdracht kan bijvoorbeeld worden gebruikt:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>De beheerclient maken

1. Open het Program.cs bestand voor het project dat u hebt gemaakt. Voeg deze vervolgens toe met behulp van instructies aan de bestaande instructies boven aan het bestand:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Als u de beheerclient wilt maken, voegt u deze code toe aan de hoofdmethode:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Resources maken

### <a name="create-the-resource-group"></a>De resourcegroep maken

Alle resources moeten zijn opgenomen in een [resourcegroep](../../azure-resource-manager/management/overview.md).

Als u waarden voor de toepassing wilt opgeven en de resourcegroep wilt maken, voegt u deze code toe aan de hoofdmethode:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>De beschikbaarheidsset maken

[Beschikbaarheidssets](tutorial-availability-sets.md) maken het eenvoudiger voor u om de virtuele machines te onderhouden die door uw toepassing worden gebruikt.

Als u de beschikbaarheidsset wilt maken, voegt u deze code toe aan de hoofdmethode:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Het openbare IP-adres maken

Een [openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) is nodig om te communiceren met de virtuele machine.

Als u het openbare IP-adres voor de virtuele machine wilt maken, voegt u deze code toe aan de hoofdmethode:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

Een virtuele machine moet zich in een subnet van een [virtueel netwerk bevinden.](../../virtual-network/virtual-networks-overview.md)

Als u een subnet en een virtueel netwerk wilt maken, voegt u deze code toe aan de hoofdmethode:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>De netwerkinterface maken

Een virtuele machine heeft een netwerkinterface nodig om te communiceren op het virtuele netwerk.

Als u een netwerkinterface wilt maken, voegt u deze code toe aan de hoofdmethode:

```csharp
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>De virtuele machine maken

Nu u alle ondersteunende bronnen hebt gemaakt, u een virtuele machine maken.

Als u de virtuele machine wilt maken, voegt u deze code toe aan de hoofdmethode:

```csharp
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Met deze zelfstudie wordt een virtuele machine gemaakt waarop een versie van het Windows Server-besturingssysteem wordt uitgevoerd. Zie Navigeren en selecteren van [Azure-afbeeldingen voor virtuele machines met Windows PowerShell en azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie over het selecteren van andere afbeeldingen.
> 
>

Als u een bestaande schijf wilt gebruiken in plaats van een marketplace-afbeelding, gebruikt u deze code:

```csharp
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Beheertaken uitvoeren

Tijdens de levenscyclus van een virtuele machine wilt u mogelijk beheertaken uitvoeren, zoals het starten, stoppen of verwijderen van een virtuele machine. Daarnaast u code maken om repetitieve of complexe taken te automatiseren.

Wanneer u iets met de VM moet doen, moet u er een voorbeeld van krijgen:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Informatie over de VM

Als u informatie wilt krijgen over de virtuele machine, voegt u deze code toe aan de hoofdmethode:

```csharp
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>De virtuele machine stoppen

U een virtuele machine stoppen en alle instellingen behouden, maar er nog steeds kosten voor in rekening worden gebracht, of u een virtuele machine stoppen en detoewijzing ervan doen. Wanneer een virtuele machine is toegewezen, worden alle resources die eraan zijn gekoppeld ook deallocatie en eindigt de facturering ervoor.

Als u de virtuele machine wilt stoppen zonder deze te dealeren, voegt u deze code toe aan de hoofdmethode:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Als u de virtuele machine wilt detoewijzen, wijzigt u de PowerOff-aanroep in deze code:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>De virtuele machine starten

Als u de virtuele machine wilt starten, voegt u deze code toe aan de hoofdmethode:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Het formaat van de vm wijzigen

Veel aspecten van de implementatie moeten worden overwogen bij de beslissing over een grootte voor uw virtuele machine. Zie [VM-formaten voor](sizes.md)meer informatie .  

Als u de grootte van de virtuele machine wilt wijzigen, voegt u deze code toe aan de hoofdmethode:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Een gegevensschijf toevoegen aan de virtuele machine

Als u een gegevensschijf aan de virtuele machine wilt toevoegen, voegt u deze code toe aan de hoofdmethode. In dit voorbeeld wordt een gegevensschijf van 2 GB, han een LUN van 0 en een caching-type ReadWrite toegevoegd:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Resources verwijderen

Omdat er kosten in rekening worden gebracht voor resources die in Azure worden gebruikt, is het altijd een goede gewoonte om resources te verwijderen die niet langer nodig zijn. Als u de virtuele machines en alle ondersteunende resources wilt verwijderen, hoeft u alleen de brongroep te verwijderen.

Als u de brongroep wilt verwijderen, voegt u deze code toe aan de hoofdmethode:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Het duurt ongeveer vijf minuten voordat deze consoletoepassing volledig van begin tot eind wordt uitgevoerd. 

1. Als u de consoletoepassing wilt uitvoeren, klikt u op **Start**.

2. Voordat u op **Enter** drukt om resources te verwijderen, u enkele minuten nodig hebben om de creatie van de resources in de Azure-portal te verifiëren. Klik op de implementatiestatus om informatie over de implementatie te bekijken.

## <a name="next-steps"></a>Volgende stappen
* Profiteer van het gebruik van een sjabloon om een virtuele machine te maken met behulp van de informatie in [Een Azure Virtual Machine implementeren met C# en een Resource Manager-sjabloon.](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Meer informatie over het gebruik van de [Azure-bibliotheken voor .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
