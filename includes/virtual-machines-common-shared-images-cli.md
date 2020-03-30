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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814849"
---
## <a name="before-you-begin"></a>Voordat u begint

Als u het voorbeeld in dit artikel wilt voltooien, moet u een bestaande beheerde afbeelding van een gegeneraliseerde vm hebben. Zie [Zelfstudie: Een aangepaste afbeelding van een Azure-vm maken met de Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)voor meer informatie. Als de beheerde afbeelding een gegevensschijf bevat, mag de grootte van de gegevensschijf niet meer dan 1 TB bedragen.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U Cloud Shell ook starten op [https://shell.azure.com/bash](https://shell.azure.com/bash)een apart browsertabblad door naar. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Zie [Azure CLI](/cli/azure/install-azure-cli)installeren als u de CLI liever lokaal installeert en gebruikt.

## <a name="create-an-image-gallery"></a>Een afbeeldingsgalerie maken 

Een afbeeldingsgalerie is de primaire bron die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. Toegestane tekens voor galerienaam zijn hoofdletters, cijfers, stippen en perioden. De naam van de galerie mag geen streepjes bevatten.   Galerijnamen moeten uniek zijn binnen uw abonnement. 

Maak een afbeeldingsgalerij met [az sig maken](/cli/azure/sig#az-sig-create). In het volgende voorbeeld wordt een galerie met de naam *myGallery* in *myGalleryRG gemaakt.*

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Een afbeeldingsdefinitie maken

Afbeeldingsdefinities maken een logische groepering voor afbeeldingen. Ze worden gebruikt om informatie over de afbeeldingsversies die erin zijn gemaakt, te beheren. Afbeeldingsdefinitienamen kunnen bestaan uit hoofdletters of kleine letters, cijfers, stippen, streepjes en perioden. Zie [Afbeeldingsdefinities](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u voor een afbeeldingsdefinitie opgeven.

Maak een eerste afbeeldingsdefinitie in de galerij met behulp van [az sig image-definition maken](/cli/azure/sig/image-definition#az-sig-image-definition-create).

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


## <a name="create-an-image-version"></a>Een afbeeldingsversie maken 

Maak destijds versies van de afbeelding met [de create-image-versie van de AZ-afbeelding](/cli/azure/sig/image-version#az-sig-image-version-create). U moet de id van de beheerde afbeelding doorgeven om te gebruiken als basislijn voor het maken van de afbeeldingsversie. U [de az-afbeeldingslijst](/cli/azure/image?view#az-image-list) gebruiken om informatie te krijgen over afbeeldingen die zich in een brongroep bevinden. 

Toegestane tekens voor de afbeeldingsversie zijn getallen en perioden. Getallen moeten zich binnen het bereik van een 32-bits geheel getal bevinden. Formaat: *MajorVersion*. *MinorVersion*. *Patch*.

In dit voorbeeld, de versie van onze afbeelding is *1.0.0* en we gaan 2 replica's te maken in de *Regio West Central US,* 1 replica in de *regio Zuid-Centraal-VS* en 1 replica in de *Regio Oost-VS 2* met behulp van zone-redundante opslag.


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
> U moet wachten tot de afbeeldingsversie volledig is voltooid en wordt gerepliceerd voordat u dezelfde beheerde afbeelding gebruiken om een andere afbeeldingsversie te maken.
>
> U ook al uw replica's van de `--storage-account-type standard_zrs` afbeeldingsversie opslaan in Zone Redundant [Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) door toe te voegen wanneer u de afbeeldingsversie maakt.
>

## <a name="share-the-gallery"></a>De galerie delen

We raden u aan om deze te delen met andere gebruikers op galerieniveau. Gebruik az sig show om de object-ID van uw galerie te [krijgen.](/cli/azure/sig#az-sig-show)

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Gebruik de object-ID als bereik, samen met een e-mailadres en [az-roltoewijzing om](/cli/azure/role/assignment#az-role-assignment-create) een gebruiker toegang te geven tot de gedeelde afbeeldingsgalerie.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


