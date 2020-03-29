---
title: Azure Image Builder gebruiken met een afbeeldingsgalerie voor Linux VM's (voorbeeld)
description: Linux VM-afbeeldingen maken met Azure Image Builder en Shared Image Gallery.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945025"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Preview: Een Linux-afbeelding maken en distribueren naar een gedeelde afbeeldingsgalerie 

In dit artikel ziet u hoe u de Azure Image Builder en de Azure CLI gebruiken om een afbeeldingsversie te maken in een [Gedeelde Galerie voor afbeeldingen](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)en de afbeelding vervolgens wereldwijd te distribueren. U dit ook doen met [Azure PowerShell](../windows/image-builder-gallery.md).


We gebruiken een voorbeeld .json-sjabloon om de afbeelding te configureren. Het .json-bestand dat we gebruiken is hier: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Als u de afbeelding wilt distribueren naar een gedeelde afbeeldingsgalerie, gebruikt de sjabloon [sharedImage](image-builder-json.md#distribute-sharedimage) als de waarde voor de `distribute` sectie van de sjabloon.

> [!IMPORTANT]
> Azure Image Builder bevindt zich momenteel in een openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

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

We zullen een aantal stukjes informatie herhaaldelijk gebruiken, dus we zullen een aantal variabelen maken om die informatie op te slaan.

Voor Preview ondersteunt imagebuilder alleen het maken van aangepaste afbeeldingen in dezelfde resourcegroep als de bronbeheerde afbeelding. Werk de naam van de brongroep in dit voorbeeld bij om dezelfde resourcegroep te zijn als de bronbeheerde afbeelding.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Maak een variabele voor uw abonnements-ID. U dit `az account show | grep id`krijgen met behulp van.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Maak de resourcegroep.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Geef Azure Image Builder toestemming om resources in die resourcegroep te maken. De `--assignee` waarde is de app-registratie-id voor de Image Builder-service. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Een afbeeldingsdefinitie en galerie maken

Als u Image Builder wilt gebruiken met een gedeelde afbeeldingsgalerie, moet u een bestaande afbeeldingsgalerie en afbeeldingsdefinitie hebben. Image Builder maakt niet de afbeeldingsgalerie en afbeeldingsdefinitie voor u.

Als u nog geen galerie- en afbeeldingsdefinitie hebt om te gebruiken, moet u deze eerst maken. Maak eerst een afbeeldingsgalerie.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Maak vervolgens een afbeeldingsdefinitie.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>De .json downloaden en configureren

Download de .json-sjabloon en configureer deze met uw variabelen.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>De afbeeldingsversie maken

In dit volgende deel wordt de afbeeldingsversie in de galerie gemaakt. 

Verzend de afbeeldingsconfiguratie naar de Azure Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Start de afbeeldingsopbouw.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Het maken van de afbeelding en het repliceren naar beide regio's kan een tijdje duren. Wacht totdat dit onderdeel is voltooid voordat u overgaat tot het maken van een vm.


## <a name="create-the-vm"></a>De virtuele machine maken

Maak een VM op basis van de afbeeldingsversie die is gemaakt door Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Maak via SSH verbinding met de virtuele machine.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

U moet zien dat de afbeelding is aangepast met een *Bericht van de Dag* zodra uw SSH-verbinding tot stand is gebracht!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u nu wilt proberen de afbeeldingsversie opnieuw aan te passen om een nieuwe versie van dezelfde afbeelding te maken, slaat u de volgende stappen over en gaat u Azure [Image Builder gebruiken om een andere afbeeldingsversie te maken.](image-builder-gallery-update-image-version.md)


Hiermee wordt de afbeelding verwijderd die is gemaakt, samen met alle andere bronbestanden. Zorg ervoor dat u klaar bent met deze implementatie voordat u de resources verwijderd.

Wanneer u bronnen voor afbeeldingengalerij verwijdert, moet u alle afbeeldingsversies verwijderen voordat u de afbeeldingsdefinitie verwijderen die wordt gebruikt om deze te maken. Als u een galerie wilt verwijderen, moet u eerst alle afbeeldingsdefinities in de galerie hebben verwijderd.

Verwijder de sjabloon voor de opbouw van afbeeldingen.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

De afbeeldingsversie laten maken door imagebuilder, dit begint altijd met, `0.`en verwijder vervolgens de afbeeldingsversie

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Verwijder de afbeeldingsdefinitie.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Verwijder de galerie.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Verwijder de brongroep.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Shared Image Galleries](shared-image-galleries.md).