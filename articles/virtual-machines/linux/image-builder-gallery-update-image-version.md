---
title: Een nieuwe VM-afbeeldingsversie maken op basis van een bestaande afbeeldingsversie met Azure Image Builder (voorbeeld)
description: Maak een nieuwe VM-afbeeldingsversie van een bestaande afbeeldingsversie met Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 5766e91dc6a17d50c46d396dd8a68d17081e0926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246803"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Voorbeeld: een nieuwe VERSIE van de VM-afbeelding maken op basis van een bestaande afbeeldingsversie met Azure Image Builder

In dit artikel ziet u hoe u een bestaande afbeeldingsversie in een [gedeelde afbeeldingsgalerie kunt](shared-image-galleries.md)gebruiken, deze bijwerken en publiceren als een nieuwe afbeeldingsversie naar de galerie.

We gebruiken een voorbeeld .json-sjabloon om de afbeelding te configureren. Het .json-bestand dat we gebruiken is hier: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>De functies registreren
Als u Azure Image Builder wilt gebruiken tijdens de preview, moet u de nieuwe functie registreren.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Controleer de status van de functieregistratie.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Controleer uw registratie.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Als ze niet zeggen geregistreerd, voer het volgende uit:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Variabelen en machtigingen instellen

Als je [Een afbeelding maken en distribueren naar een gedeelde afbeeldingsgalerie](image-builder-gallery.md) hebt gebruikt om je gedeelde afbeeldingsgalerie te maken, heb je al een aantal variabelen gemaakt die we nodig hebben. Zo niet, dan u instellen een aantal variabelen worden gebruikt voor dit voorbeeld.

Voor Preview ondersteunt imagebuilder alleen het maken van aangepaste afbeeldingen in dezelfde resourcegroep als de bronbeheerde afbeelding. Werk de naam van de brongroep in dit voorbeeld bij om dezelfde resourcegroep te zijn als de bronbeheerde afbeelding.


```console
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

Maak een variabele voor uw abonnements-ID. U dit `az account show | grep id`krijgen met behulp van.

```console
subscriptionID=<Subscription ID>
```

Download de afbeeldingsversie die u wilt bijwerken.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Als u al uw eigen gedeelde afbeeldingsgalerie hebt en het vorige voorbeeld niet hebt gevolgd, moet u machtigingen toewijzen voor Image Builder om toegang te krijgen tot de brongroep, zodat deze toegang heeft tot de galerie.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Voorbeeld van helloImage wijzigen
U het voorbeeld dat we gaan gebruiken bekijken door het .json-bestand hier te openen: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) samen met de [sjabloonverwijzing Image Builder.](image-builder-json.md) 


Download het voorbeeld .json en configureer het met uw variabelen. 

```console
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

Verzend de afbeeldingsconfiguratie naar de VM Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Start de afbeeldingsopbouw.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Wacht tot de afbeelding is gebouwd en replicatie voordat u doorgaat naar de volgende stap.


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

Maak een SSH-verbinding met de VM met het openbare IP-adres van de virtuele machine.

```console
ssh azureuser@<pubIp>
```

U moet zien dat de afbeelding is aangepast met een "Bericht van de Dag" zodra uw SSH-verbinding tot stand is gebracht.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` om de SSH-verbinding te sluiten.

U ook de afbeeldingsversies weergeven die nu beschikbaar zijn in uw galerie.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Volgende stappen

Zie Sjabloonverwijzing voor [imagebuilder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over de onderdelen van het .json-bestand dat in dit artikel wordt gebruikt.