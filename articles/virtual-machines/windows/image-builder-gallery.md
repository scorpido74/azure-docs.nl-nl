---
title: Azure Image Builder gebruiken met een afbeeldingsgalerie voor Windows VM's (voorbeeld)
description: Azure Shared Gallery-afbeeldingsversies maken met Azure Image Builder en Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263350"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Voorbeeld: een Windows-afbeelding maken en distribueren naar een gedeelde afbeeldingsgalerie 

In dit artikel ziet u hoe u de Azure Image Builder en Azure PowerShell gebruiken om een afbeeldingsversie te maken in een [Galerie voor gedeelde afbeeldingen](shared-image-galleries.md)en de afbeelding vervolgens wereldwijd te distribueren. U dit ook doen met de [Azure CLI](../linux/image-builder-gallery.md).

We gebruiken een .json-sjabloon om de afbeelding te configureren. Het .json-bestand dat we gebruiken is hier: [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). We zullen een lokale versie van de sjabloon downloaden en bewerken, dus dit artikel is geschreven met behulp van lokale PowerShell-sessie.

Als u de afbeelding wilt distribueren naar een gedeelde afbeeldingsgalerie, gebruikt de sjabloon [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) als de waarde voor de `distribute` sectie van de sjabloon.

Azure Image Builder voert automatisch sysprep uit om de afbeelding te generaliseren, dit is een algemene opdracht voor sysprep, die u indien nodig [overschrijven.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) 

Houd er rekening mee hoe vaak u laagaanpassingen hebt. U de opdracht Sysprep tot 8 keer uitvoeren op één Windows-afbeelding. Nadat u Sysprep 8 keer hebt uitgevoerd, moet u uw Windows-afbeelding opnieuw maken. Zie Limieten voor meer informatie [over hoe vaak u Sysprep uitvoeren.](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) 

> [!IMPORTANT]
> Azure Image Builder bevindt zich momenteel in een openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="register-the-features"></a>De functies registreren
Als u Azure Image Builder wilt gebruiken tijdens de preview, moet u de nieuwe functie registreren.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Controleer de status van de functieregistratie.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Wacht `RegistrationState` tot `Registered` het is voordat u naar de volgende stap gaat.

Controleer de registraties van uw provider. Zorg ervoor `Registered`dat elk retourneert .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Als ze niet `Registered`terugkeren, gebruik dan het volgende om de aanbieders te registreren:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Variabelen maken

We zullen een aantal stukjes informatie herhaaldelijk gebruiken, dus we zullen een aantal variabelen maken om die informatie op te slaan. Vervang de waarden voor de `username` `vmpassword`variabelen, zoals en , met uw eigen informatie.

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

Maak een resourcegroep en geef Azure Image Builder toestemming om resources in die resourcegroep te maken.

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

Als u Image Builder wilt gebruiken met een gedeelde afbeeldingsgalerie, moet u een bestaande afbeeldingsgalerie en afbeeldingsdefinitie hebben. Image Builder maakt niet de afbeeldingsgalerie en afbeeldingsdefinitie voor u.

Als u nog geen galerie- en afbeeldingsdefinitie hebt om te gebruiken, moet u deze eerst maken. Maak eerst een afbeeldingsgalerie.

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

Download de .json-sjabloon en configureer deze met uw variabelen.

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


## <a name="create-the-image-version"></a>De afbeeldingsversie maken

Uw sjabloon moet worden ingediend bij de service, dit downloadt alle afhankelijke artefacten, zoals scripts, en slaat ze op in de brongroep met tijdelijke bestanden, vooraf zijn bevestigd met *IT_.*

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Om de afbeelding te maken moet je 'Uitvoeren' op de sjabloon aanroepen.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Het maken van de afbeelding en het repliceren naar beide regio's kan een tijdje duren. Wacht totdat dit onderdeel is voltooid voordat u overgaat tot het maken van een vm.

Zie De [Readme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) voor deze sjabloon op GitHub voor informatie over opties voor het automatiseren van de status van het maken van de afbeelding.


## <a name="create-the-vm"></a>De virtuele machine maken

Maak een VM op basis van de afbeeldingsversie die is gemaakt door Azure Image Builder.

Download de afbeeldingsversie die u hebt gemaakt.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

De VM in het tweede gebied maken waarin de afbeelding is gerepliceerd.

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

## <a name="verify-the-customization"></a>De aanpassing verifiëren
Maak een verbinding met Extern bureaublad met de vm met de gebruikersnaam en het wachtwoord die u hebt ingesteld toen u de virtuele machine maakte. Open in de VM een cmd-prompt en typ:

```console
dir c:\
```

U ziet een `buildActions` map met de naam die is gemaakt tijdens het aanpassen van afbeeldingen.


## <a name="clean-up-resources"></a>Resources opschonen
Als u nu wilt proberen de afbeeldingsversie opnieuw aan te stellen om een nieuwe versie van dezelfde afbeelding te maken, **slaat u deze stap over** en gaat u Azure Image Builder gebruiken om een andere [afbeeldingsversie te maken.](image-builder-gallery-update-image-version.md)


Hiermee wordt de afbeelding verwijderd die is gemaakt, samen met alle andere bronbestanden. Zorg ervoor dat u klaar bent met deze implementatie voordat u de resources verwijderd.

Verwijder eerst de sjabloon resourcegroep, anders wordt de groep met tijdelijke resources *(IT_)* die door AIB wordt gebruikt, niet opgeschoond.

ResourceID van de afbeeldingssjabloon downloaden. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Afbeeldingssjabloon verwijderen.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

de brongroep verwijderen.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie Azure Image Builder gebruiken om [een andere afbeeldingsversie te maken](image-builder-gallery-update-image-version.md)voor meer informatie over het bijwerken van de afbeeldingsversie die u hebt gemaakt.
