---
title: 'Zelf studie: aangepaste VM-installatie kopieën maken met Azure CLI'
description: In deze zelfstudie leert u hoe u Azure CLI gebruikt om een aangepaste installatiekopie van een virtuele machine te maken in Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc
ms.reviewer: akjosh
ms.openlocfilehash: 9f3a175352aa0455cecc2e31e235a60cc27c76c5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792170"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Zelfstudie: Een aangepaste installatiekopie van een Azure-VM maken met Azure CLI

Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. In deze zelfstudie maakt u uw eigen aangepaste installatiekopie van een virtuele Azure-machine. Procedures voor:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een definitie van een installatie kopie maken
> * De versie van een installatiekopie maken
> * Een virtuele machine maken op basis van een installatie kopie 
> * Een galerie met installatie kopieën delen


In deze zelf studie wordt gebruikgemaakt van de CLI binnen de [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **deze** in het begin van een wille keurig code blok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van de Azure CLI-versie 2.4.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Overzicht

Een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de galerie gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen. Kies welke installatie kopieën u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u wilt delen. 

De functie gedeelde installatie kopie galerie heeft meerdere bron typen:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Voordat u begint

In de onderstaande stappen wordt gedetailleerd beschreven hoe u van een bestaande virtuele machine een herbruikbare aangepaste installatiekopie maakt die u kunt gebruiken om nieuwe virtuele machines te maken.

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Als dat nodig is, kunt u de [cli-Snelstartgids](quick-create-cli.md) bekijken om een virtuele machine te maken die u voor deze zelf studie kunt gebruiken. Wanneer u de zelf studie doorloopt, vervangt u de resource namen waar nodig.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook starten op een afzonderlijk browser tabblad door naar te [https://shell.azure.com/powershell](https://shell.azure.com/powershell)gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-an-image-gallery"></a>Een galerie met installatie kopieën maken 

Een galerie met installatie kopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. 

Toegestane tekens voor de naam van de galerie bestaan uit hoofd letters of kleine letters, cijfers, punten en punten. De naam van de galerie mag geen streepjes bevatten.   Galerie namen moeten uniek zijn binnen uw abonnement. 

Maak een galerie met installatie kopieën met [AZ sig Create](/cli/azure/sig#az-sig-create). In het volgende voor beeld wordt een resource groep met de naam Gallery gemaakt met de naam *myGalleryRG* in *VS-Oost*en een galerie met de naam *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-infornation-about-the-vm"></a>Infornation over de virtuele machine ophalen

U ziet een lijst met virtuele machines die beschikbaar zijn via de [AZ VM-lijst](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Wanneer u de naam van de virtuele machine kent en de resource groep waarin deze zich bevindt, haalt u de ID van de virtuele machine op met [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Kopieer de ID van de virtuele machine die u later wilt gebruiken.

## <a name="create-an-image-definition"></a>Een definitie van een installatie kopie maken

Afbeeldings definities maken een logische groepering voor installatie kopieën. Ze worden gebruikt voor het beheren van informatie over de installatie kopieën die in deze versies worden gemaakt. 

Definitie namen van afbeeldingen kunnen bestaan uit hoofd letters, kleine letters, cijfers, punten, streepjes en punten. 

Zie [afbeeldings definities](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u kunt opgeven voor de definitie van een installatie kopie.

Maak een definitie van een installatie kopie in de galerie met behulp van [AZ sig image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create). 

In dit voor beeld heeft de definitie van de installatie kopie de naam *myImageDefinition*en is voor een [gespecialiseerde](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) Linux-installatie kopie van het besturings systeem. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

Kopieer de ID van de afbeeldings definitie uit de uitvoer die u later wilt gebruiken.

## <a name="create-the-image-version"></a>De versie van de installatie kopie maken

Maak een installatie kopie versie van de virtuele machine met behulp van [AZ Image Gallery Create-Image galerie-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Toegestane tekens voor de versie van de installatie kopie zijn getallen en punten. Getallen moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*. *MinorVersion*. *Patch*.

In dit voor beeld is de versie van onze installatie kopie *1.0.0* en we gaan twee replica's maken in de regio *West-Centraal VS* , 1 replica in de regio *Zuid-Centraal VS* en 1 replica in de regio *VS-Oost 2* met zone-redundante opslag. De replicatie regio's moeten de regio bevatten waarin de bron-VM zich bevindt.

Vervang de waarde van `--managed-image` in dit voor beeld door de id van uw virtuele machine uit de vorige stap.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> U moet wachten tot de versie van de installatie kopie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatie kopie kunt gebruiken om een andere versie van de installatie kopie te maken.
>
> U kunt uw installatie kopie ook opslaan in Premiun-opslag door `--storage-account-type  premium_lrs`een toevoeg-of [zone-redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) door toe te voegen `--storage-account-type  standard_zrs` wanneer u de versie van de installatie kopie maakt.
>

 
## <a name="create-the-vm"></a>De virtuele machine maken

Maak de virtuele machine met [AZ VM Create](/cli/azure/vm#az-vm-create) met behulp van de--gespecialiseerde para meter om aan te geven dat de afbeelding een gespecialiseerde installatie kopie is. 

Gebruik de definitie-ID van `--image` de installatie kopie om de virtuele machine te maken op basis van de meest recente versie van de installatie kopie die beschikbaar is. U kunt de virtuele machine ook maken op basis van een specifieke versie door de versie-ID `--image`voor de installatie kopie op te geven voor. 

In dit voor beeld maken we een VM op basis van de nieuwste versie van de *myImageDefinition* -installatie kopie.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
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

## <a name="azure-image-builder"></a>Azure Image Builder

Azure biedt ook een service, gebouwd op verpakker, [Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview). Geef een beschrijving van uw aanpassingen in een sjabloon, waarna het maken van de installatie kopie wordt afgehandeld. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een aangepaste installatiekopie voor een virtuele machine gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een definitie van een installatie kopie maken
> * De versie van een installatiekopie maken
> * Een virtuele machine maken op basis van een installatie kopie 
> * Een galerie met installatie kopieën delen

Ga naar de volgende zelfstudie voor meer informatie over virtuele machines met hoge beschikbaarheid.

> [!div class="nextstepaction"]
> [Maxi maal beschik bare virtuele machines maken](tutorial-availability-sets.md).

