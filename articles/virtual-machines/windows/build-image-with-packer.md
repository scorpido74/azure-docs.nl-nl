---
title: Windows VM-afbeeldingen maken met Packer in Azure
description: Meer informatie over het gebruik van Packer om afbeeldingen van virtuele Windows-machines in Azure te maken
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: b2ff9869b0de7a0285644bea462101cd1dc80b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74039225"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Packer gebruiken om Windows-afbeeldingen voor virtuele machines in Azure te maken
Elke virtuele machine (VM) in Azure wordt gemaakt op basis van een afbeelding die de Windows-distributie- en OS-versie definieert. Afbeeldingen kunnen vooraf geïnstalleerde toepassingen en configuraties bevatten. De Azure Marketplace biedt veel afbeeldingen van eerste en derde partijen voor de meest voorkomende besturingssysteem- en toepassingsomgevingen, of u uw eigen aangepaste afbeeldingen maken die zijn afgestemd op uw behoeften. In dit artikel wordt beschreven hoe u het open-sourcetool [Packer](https://www.packer.io/) gebruiken om aangepaste afbeeldingen in Azure te definiëren en te bouwen.

Dit artikel is voor het laatst getest op 2/21/2019 met de [Az PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) versie 1.3.0 en [Packer](https://www.packer.io/docs/install/index.html) versie 1.3.4.

> [!NOTE]
> Azure heeft nu een service, Azure Image Builder (preview), voor het definiëren en maken van uw eigen aangepaste afbeeldingen. Azure Image Builder is gebouwd op Packer, zodat u er zelfs uw bestaande Packer shell provisioner-scripts mee gebruiken. Zie [Een Windows-vm maken met Azure Image Builder](image-builder.md)om aan de slag te gaan met Azure Image Builder.

## <a name="create-azure-resource-group"></a>Azure-brongroep maken
Tijdens het bouwproces maakt Packer tijdelijke Azure-resources terwijl het de bron-VM bouwt. Als u die bron-VM wilt vastleggen voor gebruik als afbeelding, moet u een brongroep definiëren. De uitvoer van het Packer-buildproces wordt opgeslagen in deze resourcegroep.

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure-referenties maken
Packer verifieert met Azure met behulp van een serviceprincipal. Een Azure-serviceprincipal is een beveiligingsidentiteit die u gebruiken met apps, services en automatiseringshulpprogramma's zoals Packer. U beheert en definieert de machtigingen voor welke bewerkingen de serviceprincipal in Azure kan uitvoeren.

Maak een serviceprincipal met [Nieuw-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) en wijs machtigingen toe voor de serviceprincipal om resources te maken en te beheren met [Nieuwe AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment). De waarde `-DisplayName` voor moet uniek zijn; vervangen door uw eigen waarde als dat nodig is.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Voer vervolgens het wachtwoord en de toepassings-id uit.

```powershell
$plainPassword
$sp.ApplicationId
```


Als u wilt verifiëren voor Azure, moet u ook uw Azure-tenant en abonnements-iD's verkrijgen met [Get-AzSubscription:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Packer-sjabloon definiëren
Als u afbeeldingen wilt maken, maakt u een sjabloon als JSON-bestand. In de sjabloon definieert u bouwers en provisioneerders die het eigenlijke bouwproces uitvoeren. Packer heeft een [bouwer voor Azure](https://www.packer.io/docs/builders/azure.html) waarmee u Azure-resources definiëren, zoals de hoofdreferenties van de service die in de vorige stap zijn gemaakt.

Maak een bestand met de naam *windows.json* en plak de volgende inhoud. Voer uw eigen waarden in voor het volgende:

| Parameter                           | Waar te verkrijgen |
|-------------------------------------|----------------------------------------------------|
| *Client_id*                         | Serviceprincipal-id weergeven met`$sp.applicationId` |
| *client_secret*                     | Het automatisch gegenereerde wachtwoord weergeven met`$plainPassword` |
| *tenant_id*                         | Uitvoer `$sub.TenantId` van opdracht |
| *subscription_id*                   | Uitvoer `$sub.SubscriptionId` van opdracht |
| *managed_image_resource_group_name* | Naam van de resourcegroep die u in de eerste stap hebt gemaakt |
| *managed_image_name*                | Naam voor de beheerde schijfafbeelding die is gemaakt |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Deze sjabloon bouwt een Windows Server 2016 VM, installeert IIS en generaliseert vervolgens de VM met Sysprep. De IIS-installatie laat zien hoe u de PowerShell-provisioner gebruiken om extra opdrachten uit te voeren. De uiteindelijke Packer-afbeelding bevat vervolgens de vereiste software-installatie en -configuratie.


## <a name="build-packer-image"></a>Packer-afbeelding bouwen
Als Packer nog niet op uw lokale machine is geïnstalleerd, [volgt u de installatie-instructies van Packer.](https://www.packer.io/docs/install/index.html)

Bouw de afbeelding door een cmd-prompt te openen en het sjabloonbestand Packer als volgt op te geven:

```
./packer build windows.json
```

Een voorbeeld van de uitvoer van de voorgaande opdrachten is als volgt:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Het duurt een paar minuten voordat Packer de VM bouwt, de provisioners uitvoert en de implementatie opruimt.


## <a name="create-a-vm-from-the-packer-image"></a>Een VM maken op basis van de packerafbeelding
U nu een VM maken op basis van uw afbeelding met [Nieuw-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). De ondersteunende netwerkbronnen worden gemaakt als ze nog niet bestaan. Voer desgevraagd een administratieve gebruikersnaam en wachtwoord in die op de vm moet worden gemaakt. In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt van *myPackerImage:*

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Als u VM's wilt maken in een andere resourcegroep of -regio dan uw Packer-afbeelding, geeft u de afbeeldings-id op in plaats van de naam van de afbeelding. U de afbeeldings-ID verkrijgen met [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage).

Het duurt een paar minuten om de VM te maken op basis van uw Packer-afbeelding.


## <a name="test-vm-and-webserver"></a>VM-webserver testen en
Haal het openbare IP-adres van uw virtuele machine op met [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). In het volgende voorbeeld wordt het IP-adres opgehaald voor het eerder gemaakte *myPublicIP*:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Als u uw VM wilt bekijken, inclusief de IIS-installatie van de Packer-provisioner, voert u het openbare IP-adres in in een webbrowser in.

![Standaardsite van IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Volgende stappen
U ook bestaande Packer provisioner-scripts gebruiken met [Azure Image Builder.](image-builder.md)
