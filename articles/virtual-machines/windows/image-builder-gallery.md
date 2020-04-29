---
title: Azure Image Builder gebruiken met een galerie met installatie kopieën voor Windows-Vm's (preview-versie)
description: Maak installatie kopieën van de gedeelde Azure-galerie met behulp van Azure Image Builder en Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 48eff11facf0f1432534d61f003f61e6755caf33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869520"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Voor beeld: een Windows-installatie kopie maken en deze distribueren naar een gedeelde installatie kopie galerie 

In dit artikel wordt uitgelegd hoe u de opbouw functie voor installatie kopieën van Azure en Azure PowerShell kunt gebruiken om een installatie kopie versie te maken in een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md)en vervolgens de installatie kopie wereld wijd te distribueren. U kunt dit ook doen met behulp van de [Azure cli](../linux/image-builder-gallery.md).

Er wordt een JSON-sjabloon gebruikt om de installatie kopie te configureren. Het JSON-bestand dat we gebruiken, is hier: [armTemplateWinSIG. json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). We gaan een lokale versie van de sjabloon downloaden en bewerken, zodat dit artikel wordt geschreven met behulp van een lokale Power shell-sessie.

De sjabloon maakt gebruik van [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) als de waarde voor de `distribute` sectie van de sjabloon om de installatie kopie naar een galerie met gedeelde afbeeldingen te distribueren.

Azure Image Builder voert automatisch Sysprep uit om de installatie kopie te generaliseren. Dit is een generieke Sysprep-opdracht, die u zo nodig kunt [overschrijven](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) . 

Houd er rekening mee dat het aantal keren dat u de laag wilt aanpassen. U kunt de Sysprep-opdracht Maxi maal 8 keer uitvoeren op één Windows-installatie kopie. Nadat u Sysprep acht keer hebt uitgevoerd, moet u de Windows-installatie kopie opnieuw maken. Zie [limieten voor het aantal keren dat u Sysprep kunt uitvoeren](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)voor meer informatie. 

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>De functies registreren
Als u Azure Image Builder wilt gebruiken tijdens de preview, moet u de nieuwe functie registreren.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Controleer de status van de functie registratie.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Wacht tot `RegistrationState` de `Registered` bewerking is gestart voordat u verdergaat met de volgende stap.

Controleer uw provider registraties. Zorg ervoor dat elke `Registered`keer wordt geretourneerd.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Als ze niet retour neren `Registered`, gebruikt u het volgende om de providers te registreren:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Variabelen maken

We zullen enkele gegevens herhaaldelijk gebruiken, dus we maken een aantal variabelen om deze informatie op te slaan. Vervang de waarden voor de variabelen, zoals `username` en `vmpassword`, door uw eigen gegevens.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>De resourcegroep maken

Maak een resource groep en geef de Azure Image Builder-machtiging voor het maken van resources in die resource groep.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>De galerie met gedeelde afbeeldingen maken

Als u de opbouw functie voor afbeeldingen wilt gebruiken met een galerie met gedeelde afbeeldingen, moet u een bestaande afbeeldings galerie en afbeeldings definitie hebben. Met de opbouw functie voor installatie kopieën wordt de installatie kopie galerie en de definitie van de installatie kopie niet voor u gemaakt.

Als u nog geen galerie en afbeeldings definitie hebt om te gebruiken, maakt u deze eerst. Maak eerst een galerie met installatie kopieën.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>De sjabloon downloaden en configureren

Down load de JSON-sjabloon en configureer deze met de variabelen.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>De versie van de installatie kopie maken

Uw sjabloon moet worden verzonden naar de service. Hierdoor worden alle afhankelijke artefacten, zoals scripts, gedownload en opgeslagen in de staging-resource groep, voorafgegaan door *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Als u de installatie kopie wilt maken, moet u uitvoeren op de sjabloon.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Het maken van de installatie kopie en het repliceren naar beide regio's kan enige tijd duren. Wacht tot dit onderdeel is voltooid voordat u doorgaat met het maken van een virtuele machine.

Zie het [Leesmij-bestand](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) voor deze sjabloon op github voor informatie over opties voor het automatiseren van de status van de installatie kopie.


## <a name="create-the-vm"></a>De virtuele machine maken

Maak een virtuele machine op basis van de installatie kopie versie die is gemaakt door de opbouw functie voor installatie kopieën van Azure.

Haal de versie van de installatie kopie op die u hebt gemaakt.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Maak de virtuele machine in de tweede regio die de afbeelding had gerepliceerd.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>De aanpassing controleren
Maak een Extern bureaublad verbinding met de virtuele machine met behulp van de gebruikers naam en het wacht woord die u hebt ingesteld tijdens het maken van de virtuele machine. Open een opdracht prompt in de virtuele machine en typ het volgende:

```console
dir c:\
```

Als het goed is, ziet `buildActions` u een map met de naam die is gemaakt tijdens het aanpassen van de installatie kopie.


## <a name="clean-up-resources"></a>Resources opschonen
Als u de installatie kopie nu opnieuw wilt aanpassen om een nieuwe versie van dezelfde installatie kopie te maken, slaat u **deze stap over** en gaat u verder met het [gebruik van Azure Image Builder om een andere versie van de installatie kopie te maken](image-builder-gallery-update-image-version.md).


Hiermee verwijdert u de installatie kopie die is gemaakt, samen met alle andere bron bestanden. Zorg ervoor dat u klaar bent met deze implementatie voordat u de resources verwijdert.

Verwijder eerst de sjabloon voor de resource groep, anders wordt de staging-resource groep (*IT_*) die door AIB wordt gebruikt, niet opgeschoond.

Haal ResourceID van de afbeeldings sjabloon op. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Verwijder de afbeeldings sjabloon.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Verwijder de resource groep.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Image Builder gebruiken om een andere versie](image-builder-gallery-update-image-version.md)van de installatie kopie te maken voor meer informatie over het bijwerken van de versie van de installatie kopie die u hebt gemaakt.
