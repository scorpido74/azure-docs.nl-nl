---
title: Een nieuwe VM-installatie kopie versie maken op basis van een bestaande installatie kopie versie met behulp van Azure Image Builder (preview)
description: Maak een nieuwe VM-installatie kopie versie van een bestaande installatie kopie versie met behulp van Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 4a3a9bd518b9bc695855ad2b0b659d3cf1834c05
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945033"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Voor beeld: een nieuwe VM-installatie kopie maken van een bestaande installatie kopie versie met behulp van Azure Image Builder

In dit artikel wordt beschreven hoe u een bestaande versie van een installatie kopie in een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md)kunt maken, hoe u deze kunt bijwerken en hoe u deze publiceert als een nieuwe installatie kopie versie naar de galerie.

Er wordt een voor beeld van een JSON-sjabloon gebruikt voor het configureren van de installatie kopie. Het JSON-bestand dat we gebruiken, is hier: [helloImageTemplateforSIGfromSIG. json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>De functies registreren
Als u Azure Image Builder wilt gebruiken tijdens de preview, moet u de nieuwe functie registreren.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Controleer de status van de functie registratie.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Controleer uw registratie.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Als ze niet zijn geregistreerd, voert u de volgende handelingen uit:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Variabelen en machtigingen instellen

Als u [een installatie kopie maken hebt gebruikt en naar een galerie met gedeelde installatie kopieën hebt gedistribueerd](image-builder-gallery.md) om uw galerie met gedeelde afbeeldingen te maken, hebt u al een aantal van de gewenste variabelen gemaakt. Als dat niet het geval is, moet u een aantal variabelen instellen die voor dit voor beeld moeten worden gebruikt.

Voor de preview-versie kan de opbouw functie voor afbeeldingen alleen het maken van aangepaste installatie kopieën in dezelfde resource groep als de door de bron beheerde installatie kopie worden ondersteund. Werk de naam van de resource groep in dit voor beeld bij naar dezelfde resource groep als de door de bron beheerde installatie kopie.


```azurecli-interactive
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Maak een variabele voor uw abonnements-ID. U kunt dit doen met behulp van `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Down load de versie van de installatie kopie die u wilt bijwerken.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Als u al beschikt over uw eigen gedeelde galerie met installatie kopieën en u het vorige voor beeld niet hebt gevolgd, moet u machtigingen voor de opbouw functie voor installatie kopieën toewijzen om toegang te krijgen tot de resource groep, zodat deze toegang heeft tot de galerie.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>HelloImage-voor beeld wijzigen
U kunt het voor beeld bekijken dat we graag gebruiken door het JSON-bestand hier te openen: [helloImageTemplateforSIGfromSIG. json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) samen met de [verwijzing naar de installatie kopie Builder-sjabloon](image-builder-json.md). 


Down load het. json-voor beeld en configureer dit met de variabelen. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>De installatiekopie maken

Verzend de configuratie van de installatie kopie naar de VM Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Start het maken van de installatie kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Wacht totdat de installatie kopie is gemaakt en replicatie voordat u verdergaat met de volgende stap.


## <a name="create-the-vm"></a>De virtuele machine maken

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Maak een SSH-verbinding met de virtuele machine met behulp van het open bare IP-adres van de virtuele machine.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Zodra uw SSH-verbinding tot stand is gebracht, ziet u dat de installatie kopie is aangepast met een ' bericht van de dag '.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` om de SSH-verbinding te sluiten.

U kunt ook de installatie kopie versies weer geven die nu beschikbaar zijn in uw galerie.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de onderdelen van het JSON-bestand dat in dit artikel wordt gebruikt [Image Builder-sjabloon verwijzing](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).