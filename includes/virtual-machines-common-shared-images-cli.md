---
title: Include-bestand
description: Include-bestand
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: de1a22ed6e9707767c0d097a9250f0bdd31414d5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788957"
---
## <a name="create-an-image-gallery"></a>Een galerie met installatie kopieën maken 

Een galerie met installatie kopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. 

Toegestane tekens voor de naam van de galerie bestaan uit hoofd letters of kleine letters, cijfers, punten en punten. De naam van de galerie mag geen streepjes bevatten.   Galerie namen moeten uniek zijn binnen uw abonnement. 

Maak een galerie met installatie kopieën met [AZ sig Create](/cli/azure/sig#az-sig-create). In het volgende voor beeld wordt een resource groep met de naam Gallery gemaakt met de naam *myGalleryRG* in *VS-Oost*en een galerie met de naam *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>De galerie delen

U kunt afbeeldingen in abonnementen delen met behulp van op rollen gebaseerde Access Control (RBAC). U kunt afbeeldingen delen in de galerie, afbeeldings definitie of installatie kopie versie leve. Elke gebruiker met lees machtigingen voor een installatie kopie versie, zelfs via abonnementen, kan een virtuele machine implementeren met de versie van de installatie kopie.

We raden u aan om te delen met andere gebruikers op het niveau van de galerie. Gebruik [AZ sig show](/cli/azure/sig#az-sig-show)om de object-id van uw galerie op te halen.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Gebruik de object-ID als bereik, samen met een e-mail adres en [AZ-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create) om een gebruiker toegang te geven tot de galerie met gedeelde afbeeldingen. Vervang `<email-address>` en `<gallery iD>` door uw eigen gegevens.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Zie [toegang beheren met RBAC en Azure cli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)voor meer informatie over het delen van resources met RBAC.
