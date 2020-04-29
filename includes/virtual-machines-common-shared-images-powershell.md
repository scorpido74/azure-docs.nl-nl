---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "66241205"
---
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook starten op een afzonderlijk browser tabblad door naar te [https://shell.azure.com/powershell](https://shell.azure.com/powershell)gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.


## <a name="get-the-managed-image"></a>De beheerde installatie kopie ophalen

U kunt een lijst met installatie kopieën die beschikbaar zijn in een resource groep weer geven met behulp van [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Zodra u de naam van de installatie kopie kent en de resource groep waarin deze zich bevindt, kunt u opnieuw gebruiken `Get-AzImage` om het afbeeldings object op te halen en op te slaan in een variabele, zodat u het later kunt gebruiken. In dit voor beeld wordt een afbeelding opgehaald met de naam *myImage* uit de resource groep ' myResourceGroup ' en toegewezen aan de variabele *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Een galerie met installatie kopieën maken 

Een galerie met installatie kopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. Toegestane tekens voor de naam van de galerie bestaan uit hoofd letters of kleine letters, cijfers, punten en punten. De naam van de galerie mag geen streepjes bevatten. Galerie namen moeten uniek zijn binnen uw abonnement. 

Maak een galerie met installatie kopieën met [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). In het volgende voor beeld wordt een galerie gemaakt met de naam *myGallery* in de resource groep *myGalleryRG* .

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Een definitie van een installatie kopie maken 

Afbeeldings definities maken een logische groepering voor installatie kopieën. Ze worden gebruikt voor het beheren van informatie over de installatie kopieën die in deze versies worden gemaakt. Definitie namen van afbeeldingen kunnen bestaan uit hoofd letters, kleine letters, cijfers, punten, streepjes en punten. Zie [afbeeldings definities](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u kunt opgeven voor de definitie van een installatie kopie.

Maak de definitie van de installatie kopie met behulp van [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). In dit voor beeld heeft de galerie-afbeelding de naam *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Een versie van een installatie kopie maken

Maak een installatie kopie versie van een beheerde installatie kopie met behulp van [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Toegestane tekens voor de versie van de installatie kopie zijn getallen en punten. Getallen moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*. *MinorVersion*. *Patch*.

In dit voor beeld is de versie van de installatie kopie *1.0.0* en wordt deze gerepliceerd naar zowel *West-Centraal VS* -en *Zuid-Centraal VS* -data centers. Houd er rekening mee dat u bij het kiezen van doel regio's voor replicatie ook de *bron* regio moet toevoegen als doel voor replicatie.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Het kan even duren om de installatie kopie te repliceren naar alle doel regio's. Daarom hebben we een taak gemaakt zodat we de voortgang kunnen volgen. Als u de voortgang van de taak wilt zien `$job.State`, typt u.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> U moet wachten tot de versie van de installatie kopie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatie kopie kunt gebruiken om een andere versie van de installatie kopie te maken. 
>
> U kunt ook uw installatie kopie versie opslaan in [zone redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) door toe te voegen `-StorageAccountType Standard_ZRS` wanneer u de installatie kopie versie maakt.
>


## <a name="share-the-gallery"></a>De galerie delen

U wordt aangeraden toegang te delen op het niveau van de galerie met installatie kopieën. Gebruik een e-mail adres en de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) om de object-id voor de gebruiker op te halen en gebruik vervolgens [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om hen toegang tot de galerie te geven. Vervang de voorbeeld-e alinne_montes@contoso.com -mail in dit voor beeld met uw eigen gegevens.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```