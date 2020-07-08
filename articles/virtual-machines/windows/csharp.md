---
title: 'Een virtuele Azure-machine maken en beheren met C #'
description: Gebruik C# en Azure Resource Manager voor het implementeren van een virtuele machine en alle ondersteunende resources.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 4fd7ccc7b6df85397fd547f8e1e48b776f12c0df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84234518"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Virtuele Windows-machines maken en beheren in azure met behulp van C # #

Een [virtuele machine](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) van Azure heeft verschillende ondersteunende Azure-resources nodig. In dit artikel wordt beschreven hoe u VM-resources kunt maken, beheren en verwijderen met C#. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Visual Studio-project maken
> * Het pakket installeren
> * Referenties maken
> * Resources maken
> * Beheer taken uitvoeren
> * Resources verwijderen
> * De toepassing uitvoeren

Het duurt ongeveer 20 minuten om deze stappen uit te voeren.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

1. Als u dat nog niet hebt gedaan, installeert u [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecteer **.net desktop Development** op de pagina workloads en klik vervolgens op **installeren**. In de samen vatting ziet u dat **.NET Framework 4-4,6-ontwikkel Programma's** automatisch voor u worden geselecteerd. Als u Visual Studio al hebt geïnstalleerd, kunt u de .NET-workload toevoegen met behulp van de Visual Studio Launcher.
2. Klik in Visual Studio op **File** > **New** > **Project**.
3. In **sjablonen**  >  **Visual C#** selecteert u **console-app (.NET Framework)**, voert u *myDotnetProject* in voor de naam van het project, selecteert u de locatie van het project en klikt u vervolgens op **OK**.

## <a name="install-the-package"></a>Het pakket installeren

NuGet-pakketten zijn de eenvoudigste manier om de bibliotheken te installeren die u nodig hebt om deze stappen te volt ooien. Ga als volgt te werk om de bibliotheken te verkrijgen die u nodig hebt in Visual Studio:

1. Klik op **extra**  >  **Nuget package manager**en klik vervolgens op **Package Manager-console**.
2. Typ deze opdracht in de-console:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Referenties maken

Voordat u met deze stap begint, moet u ervoor zorgen dat u toegang hebt tot een [Active Directory Service-Principal](../../active-directory/develop/howto-create-service-principal-portal.md). Noteer ook de toepassings-ID, de verificatie sleutel en de Tenant-ID die u in een latere stap nodig hebt.

### <a name="create-the-authorization-file"></a>Het autorisatie bestand maken

1. Klik in Solution Explorer met de rechter muisknop op *myDotnetProject*  >  **Add**  >  **Nieuw item**toevoegen en selecteer vervolgens **tekst bestand** in *Visual C#-items*. Noem het bestand *azureauth. Properties*en klik vervolgens op **toevoegen**.
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

    Vervang ** &lt; abonnement-id &gt; ** door uw abonnements-id, ** &lt; toepassings- &gt; id** met de Active Directory toepassings-id, ** &lt; verificatie &gt; sleutel** met de toepassings sleutel en ** &lt; Tenant-id &gt; ** met de Tenant-id.

3. Sla het bestand azureauth. Properties op. 
4. Stel een omgevings variabele in Windows met de naam AZURE_AUTH_LOCATION met het volledige pad naar het autorisatie bestand dat u hebt gemaakt. U kunt bijvoorbeeld de volgende Power shell-opdracht gebruiken:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>De Management-client maken

1. Open het Program.cs-bestand voor het project dat u hebt gemaakt. Voeg deze instructies vervolgens toe aan de bestaande instructies boven aan het bestand:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Voeg deze code toe aan de methode Main om de Management-client te maken:

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

Alle resources moeten deel uitmaken van een [resource groep](../../azure-resource-manager/management/overview.md).

Als u waarden voor de toepassing wilt opgeven en de resource groep wilt maken, voegt u deze code toe aan de methode Main:

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

Met [beschikbaarheids sets](tutorial-availability-sets.md) kunt u gemakkelijker de virtuele machines onderhouden die door uw toepassing worden gebruikt.

Als u de beschikbaarheidsset wilt maken, voegt u deze code toe aan de methode Main:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Het open bare IP-adres maken

Een [openbaar IP-adres](../../virtual-network/public-ip-addresses.md) is nodig om te communiceren met de virtuele machine.

Als u het open bare IP-adres voor de virtuele machine wilt maken, voegt u deze code toe aan de methode Main:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

Een virtuele machine moet zich in een subnet van een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md)bevinden.

Als u een subnet en een virtueel netwerk wilt maken, voegt u deze code toe aan de methode Main:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>De netwerk interface maken

Een virtuele machine heeft een netwerk interface nodig om te communiceren met het virtuele netwerk.

Als u een netwerk interface wilt maken, voegt u deze code toe aan de methode Main:

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

Nu u alle ondersteunende resources hebt gemaakt, kunt u een virtuele machine maken.

Als u de virtuele machine wilt maken, voegt u deze code toe aan de methode Main:

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
> In deze zelf studie maakt u een virtuele machine waarop een versie van het Windows Server-besturings systeem wordt uitgevoerd. Zie voor meer informatie over het selecteren van andere installatie kopieën [navigeren en installatie kopieën van virtuele Azure-machines selecteren met Windows Power shell en de Azure cli](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Als u een bestaande schijf wilt gebruiken in plaats van een Marketplace-installatie kopie, gebruikt u deze code:

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

## <a name="perform-management-tasks"></a>Beheer taken uitvoeren

Tijdens de levenscyclus van een virtuele machine wilt u mogelijk beheertaken uitvoeren, zoals het starten, stoppen of verwijderen van een virtuele machine. U kunt ook code maken om herhaalde of complexe taken te automatiseren.

Wanneer u iets moet doen met de virtuele machine, moet u een exemplaar hiervan ophalen:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Informatie over de VM ophalen

Als u informatie over de virtuele machine wilt ophalen, voegt u deze code toe aan de methode Main:

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

U kunt een virtuele machine stoppen en alle instellingen behouden, maar er nog steeds kosten in rekening worden gebracht, of u kunt een virtuele machine stoppen en de toewijzing ervan ongedaan maken. Wanneer de toewijzing van een virtuele machine ongedaan wordt gemaakt, worden alle resources die eraan zijn gekoppeld, ook ongedaan gemaakt en wordt de facturering voor het apparaat beëindigd.

Als u de virtuele machine wilt stoppen zonder de toewijzing ervan op te heffen, voegt u deze code toe aan de methode Main:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Als u de toewijzing van de virtuele machine ongedaan wilt maken, wijzigt u de uitgeschakeld-aanroep naar deze code:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>De virtuele machine starten

Als u de virtuele machine wilt starten, voegt u deze code toe aan de methode Main:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Grootte van de virtuele machine wijzigen

Veel aspecten van de implementatie moeten worden overwogen bij het bepalen van de grootte van de virtuele machine. Zie [VM-grootten](sizes.md)voor meer informatie.  

Als u de grootte van de virtuele machine wilt wijzigen, voegt u deze code toe aan de methode Main:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Een gegevensschijf toevoegen aan de virtuele machine

Als u een gegevens schijf aan de virtuele machine wilt toevoegen, voegt u deze code toe aan de methode Main. In dit voor beeld wordt een gegevens schijf met een grootte van 2 GB toegevoegd, Han een LUN van 0 en het cache type ReadWrite:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Resources verwijderen

Omdat er in rekening worden gebracht voor resources die worden gebruikt in azure, is het altijd verstandig om resources te verwijderen die niet meer nodig zijn. Als u de virtuele machines en alle ondersteunende bronnen wilt verwijderen, moet u de resource groep verwijderen.

Als u de resource groep wilt verwijderen, voegt u deze code toe aan de methode Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Het duurt ongeveer vijf minuten voordat deze console toepassing volledig van begin tot eind kan worden uitgevoerd. 

1. Klik op **Start**om de console toepassing uit te voeren.

2. Voordat u op **Enter** drukt om resources te verwijderen, kan het enkele minuten duren voordat het maken van de resources in de Azure Portal is gecontroleerd. Klik op de implementatie status om informatie over de implementatie weer te geven.

## <a name="next-steps"></a>Volgende stappen
* Profiteer van het gebruik van een sjabloon voor het maken van een virtuele machine met behulp van de informatie in [een virtuele Azure-machine implementeren met C# en een resource manager-sjabloon](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Meer informatie over het gebruik [van de Azure-bibliotheken voor .net](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
