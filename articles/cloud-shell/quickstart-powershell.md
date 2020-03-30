---
title: Azure Cloud Shell Quickstart - PowerShell
description: Meer informatie over het gebruik van de PowerShell in uw browser met Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273005"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Snel starten voor PowerShell in Azure Cloud Shell

In dit document wordt beschreven hoe u de PowerShell in Cloud Shell gebruiken in de [Azure-portal.](https://portal.azure.com/)

> [!NOTE]
> Een [Bash in Azure Cloud Shell](quickstart.md) Quickstart is ook beschikbaar.

## <a name="start-cloud-shell"></a>Cloud Shell starten

1. Klik op de knop **Cloud Shell** vanaf de bovenste navigatiebalk van de Azure-portal

   ![](media/quickstart-powershell/shell-icon.png)

2. Selecteer de PowerShell-omgeving in de vervolgkeuzelijst en u bevindt zich in Azure-station`(Azure:)`

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell-opdrachten uitvoeren

Voer reguliere PowerShell-opdrachten uit in de Cloud Shell, zoals:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Navigeren in Azure-bronnen

 1. Al uw abonnementen `Azure` vanaf station weergeven

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd`naar uw voorkeursabonnement

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Alle Azure-bronnen weergeven onder het huidige abonnement

    Typ `dir` om meerdere weergaven van uw Azure-bronnen weer te geven.

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>AllResources,-weergave AllResources

Typ `dir` `AllResources` onder map om uw Azure-bronnen weer te geven.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Resourcegroepen verkennen

 U naar `ResourceGroups` de map gaan en in een specifieke resourcegroep u virtuele machines vinden.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> U merken dat de `dir`cloudshell de items veel sneller kan weergeven wanneer u typt.
> Dit komt omdat de onderliggende items in het geheugen worden opgeslagen voor een betere gebruikerservaring.
U echter `dir -Force` altijd gebruiken om nieuwe gegevens te krijgen.

### <a name="navigate-storage-resources"></a>Navigeren in opslagbronnen

Door de `StorageAccounts` directory in te voeren, u eenvoudig door al uw opslagbronnen navigeren

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Met de verbindingstekenreeks u de volgende opdracht gebruiken om het aandeel Azure-bestanden te monteren.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Zie Een [Azure-bestandendelen insluiten en toegang krijgen tot het aandeel in Windows][azmount]voor meer informatie.

U ook als volgt door de mappen navigeren onder het delen van Azure-bestanden:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interactie met virtuele machines

U al uw virtuele machines `VirtualMachines` vinden onder het huidige abonnement via directory.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell-script aanroepen in externe VM's

 > [!WARNING]
 > Raadpleeg [probleemoplossing voor extern beheer van Azure VM's.](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)

  Ervan uitgaande dat u een VM, MyVM1, laten we gebruiken `Invoke-AzVMCommand` om een PowerShell script blok aan te roepen op de externe machine.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  U ook eerst naar de `Invoke-AzVMCommand` map VirtualMachines navigeren en als volgt worden uitgevoerd.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Log interactief in op een externe VM

U kunt `Enter-AzVM` zich interactief aanmelden bij een VM die in Azure wordt uitgevoerd.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

U ook `VirtualMachines` eerst naar `Enter-AzVM` de map navigeren en als volgt worden uitgevoerd

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Ontdek WebApps

Door de `WebApps` directory in te voeren, u eenvoudig door uw bronnen voor web-apps navigeren

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Als u zich wilt verifiëren voor servers of VM's met SSH, genereert `authorized_keys` u het publiek-private sleutelpaar in Cloud Shell en publiceert u de openbare sleutel tot op de externe machine, zoals `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> U ssh-private-public-sleutels maken met behulp van `ssh-keygen` en deze publiceren `$env:USERPROFILE\.ssh` in Cloud Shell.

### <a name="using-ssh"></a>SSH gebruiken

Volg [hier](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) instructies om een nieuwe VM-configuratie te maken met Azure PowerShell-cmdlets.
Voordat u `New-AzVM` aanroept om de implementatie af te trappen, voegt u de openbare sleutel van SSH toe aan de VM-configuratie.
De nieuw gemaakte VM bevat de `~\.ssh\authorized_keys` openbare sleutel op de locatie, waardoor de SSH-sessie zonder referenties kan worden ingediend bij de VM.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Beschikbare opdrachten weergeven

Typ `Azure` onder `Get-AzCommand` schijf om contextspecifieke Azure-opdrachten te krijgen.

U ook altijd `Get-Command *az* -Module Az.*` de beschikbare Azure-opdrachten gebruiken om de beschikbare Azure-opdrachten te achterhalen.

## <a name="install-custom-modules"></a>Aangepaste modules installeren

U kunt `Install-Module` modules installeren vanuit de [PowerShell Gallery.][gallery]

## <a name="get-help"></a>Help

Typ `Get-Help` om informatie over PowerShell te krijgen in Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

Voor een specifieke opdracht kunt `Get-Help` u nog steeds een cmdlet uitvoeren.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Azure-bestanden gebruiken om uw gegevens op te slaan

U een script `helloworld.ps1`maken, bijvoorbeeld, `clouddrive` en opslaan in uw om het te gebruiken in shell sessies.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Wanneer u PowerShell de volgende keer `helloworld.ps1` in Cloud `$HOME\clouddrive` Shell gebruikt, wordt het bestand weergegeven onder de map waarmee uw Azure-bestanden worden weergegeven.

## <a name="use-custom-profile"></a>Aangepast profiel gebruiken

U uw PowerShell-omgeving aanpassen door PowerShell-profiel(en) te maken - `profile.ps1` (of `Microsoft.PowerShell_profile.ps1`).
Sla het `$profile.CurrentUserAllHosts` op `$profile.CurrentUserAllHosts`onder (of), zodat het kan worden geladen in elke PowerShell in Cloud Shell sessie.

Raadpleeg [Profielen][profile]over .

## <a name="use-git"></a>Git gebruiken

Als u een Git-repo in de Cloud Shell wilt klonen, moet u een [persoonlijk toegangstoken][githubtoken] maken en deze als gebruikersnaam gebruiken. Zodra u uw token hebt, kloont u de repository als volgt:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>De shell afsluiten

Typ `exit` om de sessie te beëindigen.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
