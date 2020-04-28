---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66814849"
---
## <a name="before-you-begin"></a>Voordat u begint

Om het voor beeld in dit artikel te volt ooien, moet u een bestaande beheerde installatie kopie van een gegeneraliseerde virtuele machine hebben. Zie [zelf studie: een aangepaste installatie kopie maken van een Azure-VM met de Azure cli](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)voor meer informatie. Als de beheerde installatie kopie een gegevens schijf bevat, kan de grootte van de gegevens schijf niet groter zijn dan 1 TB.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook starten op een afzonderlijk browser tabblad door naar te [https://shell.azure.com/bash](https://shell.azure.com/bash)gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u liever de CLI lokaal installeert en gebruikt, raadpleegt u [Azure cli installeren](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Een galerie met installatie kopieën maken 

Een galerie met installatie kopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. Toegestane tekens voor de naam van de galerie bestaan uit hoofd letters of kleine letters, cijfers, punten en punten. De naam van de galerie mag geen streepjes bevatten.   Galerie namen moeten uniek zijn binnen uw abonnement. 

Maak een galerie met installatie kopieën met [AZ sig Create](/cli/azure/sig#az-sig-create). In het volgende voor beeld wordt een galerie met de naam *myGallery* in *myGalleryRG*gemaakt.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Een definitie van een installatie kopie maken

Afbeeldings definities maken een logische groepering voor installatie kopieën. Ze worden gebruikt voor het beheren van informatie over de installatie kopieën die in deze versies worden gemaakt. Definitie namen van afbeeldingen kunnen bestaan uit hoofd letters, kleine letters, cijfers, punten, streepjes en punten. Zie [afbeeldings definities](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u kunt opgeven voor de definitie van een installatie kopie.

Maak een eerste definitie van een installatie kopie in de galerie met behulp van [AZ sig image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Een versie van een installatie kopie maken 

Maak een versie van de installatie kopie die u nodig hebt met de [afbeeldings galerie maken-installatie kopie-versie](/cli/azure/sig/image-version#az-sig-image-version-create). U moet de ID van de beheerde installatie kopie door geven om te gebruiken als basis voor het maken van de installatie kopie versie. U kunt [AZ Image List](/cli/azure/image?view#az-image-list) gebruiken om informatie over installatie kopieën in een resource groep op te halen. 

Toegestane tekens voor de versie van de installatie kopie zijn getallen en punten. Getallen moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*. *MinorVersion*. *Patch*.

In dit voor beeld is de versie van onze installatie kopie *1.0.0* en we gaan twee replica's maken in de regio *West-Centraal VS* , 1 replica in de regio *Zuid-Centraal VS* en 1 replica in de regio *VS-Oost 2* met zone-redundante opslag.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> U moet wachten tot de versie van de installatie kopie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatie kopie kunt gebruiken om een andere versie van de installatie kopie te maken.
>
> U kunt ook al uw afbeeldings versie replica's opslaan in [zone redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) door toe `--storage-account-type standard_zrs` te voegen wanneer u de versie van de installatie kopie maakt.
>

## <a name="share-the-gallery"></a>De galerie delen

We raden u aan om te delen met andere gebruikers op het niveau van de galerie. Gebruik [AZ sig show](/cli/azure/sig#az-sig-show)om de object-id van uw galerie op te halen.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Gebruik de object-ID als bereik, samen met een e-mail adres en [AZ-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create) om een gebruiker toegang te geven tot de galerie met gedeelde afbeeldingen.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


