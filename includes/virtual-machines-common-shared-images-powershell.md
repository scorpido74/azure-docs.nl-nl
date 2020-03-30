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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241205"
---
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U Cloud Shell ook starten op [https://shell.azure.com/powershell](https://shell.azure.com/powershell)een apart browsertabblad door naar. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.


## <a name="get-the-managed-image"></a>De beheerde afbeelding oppakken

U een lijst met afbeeldingen zien die beschikbaar zijn in een resourcegroep met [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Zodra u de naam van de afbeelding en de `Get-AzImage` brongroep weet waarin deze zich bevindt, u het afbeeldingsobject opnieuw gebruiken en opslaan in een variabele om later te gebruiken. In dit voorbeeld wordt een afbeelding met de naam *myImage* uit de resourcegroep 'myResourceGroup' opgehaald en aan de variabele *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Een afbeeldingsgalerie maken 

Een afbeeldingsgalerie is de primaire bron die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. Toegestane tekens voor galerienaam zijn hoofdletters, cijfers, stippen en perioden. De naam van de galerie mag geen streepjes bevatten. Galerijnamen moeten uniek zijn binnen uw abonnement. 

Maak een afbeeldingsgalerie met [Nieuw-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). In het volgende voorbeeld wordt een galerie met de naam *myGallery* gemaakt in de *brongroep myGalleryRG.*

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
   
## <a name="create-an-image-definition"></a>Een afbeeldingsdefinitie maken 

Afbeeldingsdefinities maken een logische groepering voor afbeeldingen. Ze worden gebruikt om informatie over de afbeeldingsversies die erin zijn gemaakt, te beheren. Afbeeldingsdefinitienamen kunnen bestaan uit hoofdletters of kleine letters, cijfers, stippen, streepjes en perioden. Zie [Afbeeldingsdefinities](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u voor een afbeeldingsdefinitie opgeven.

Maak de afbeeldingsdefinitie met [Nieuwe-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). In dit voorbeeld wordt de galerijafbeelding *myGalleryImage*genoemd.

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


## <a name="create-an-image-version"></a>Een afbeeldingsversie maken

Maak een afbeeldingsversie op basis van een beheerde afbeelding met [Nieuwe-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Toegestane tekens voor de afbeeldingsversie zijn getallen en perioden. Getallen moeten zich binnen het bereik van een 32-bits geheel getal bevinden. Formaat: *MajorVersion*. *MinorVersion*. *Patch*.

In dit voorbeeld is de afbeeldingsversie *1.0.0* en wordt deze gerepliceerd naar zowel *datacenters in West Central US* als South Central *US.* Houd er bij het kiezen van doelgebieden voor replicatie rekening mee dat u ook het *brongebied* als doel voor replicatie moet opnemen.


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

Het kan even duren voordat de afbeelding wordt gerepliceerd naar alle doelregio's, dus we hebben een taak gemaakt zodat we de voortgang kunnen volgen. Als u de voortgang van `$job.State`de taak wilt zien, typt u .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> U moet wachten tot de afbeeldingsversie volledig is voltooid en wordt gerepliceerd voordat u dezelfde beheerde afbeelding gebruiken om een andere afbeeldingsversie te maken. 
>
> U uw afbeeldingsversie ook opslaan `-StorageAccountType Standard_ZRS` in Zone Redundant [Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) door toe te voegen wanneer u de afbeeldingsversie maakt.
>


## <a name="share-the-gallery"></a>De galerie delen

We raden u aan de toegang op het niveau van de afbeeldingsgalerie te delen. Gebruik een e-mailadres en de [cmdlet Get-AzADUser](/powershell/module/az.resources/get-azaduser) om de object-ID voor de gebruiker te krijgen en gebruik vervolgens [Nieuw-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om ze toegang te geven tot de galerij. Vervang het voorbeeld alinne_montes@contoso.com e-mail, in dit voorbeeld, door uw eigen gegevens.

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