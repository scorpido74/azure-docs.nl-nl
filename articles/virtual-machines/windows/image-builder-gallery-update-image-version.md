---
title: Een nieuwe afbeeldingsversie maken op basis van een bestaande afbeeldingsversie met Azure Image Builder (voorbeeld)
description: Maak een nieuwe VM-afbeeldingsversie van een bestaande afbeeldingsversie met Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 60a37588ddcac339d9545fb6f597ef7bdc17ccb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247364"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Voorbeeld: een nieuwe VERSIE van de VM-afbeelding maken op basis van een bestaande afbeeldingsversie met Azure Image Builder

In dit artikel ziet u hoe u een bestaande afbeeldingsversie in een [gedeelde afbeeldingsgalerie kunt](shared-image-galleries.md)gebruiken, deze bijwerken en publiceren als een nieuwe afbeeldingsversie naar de galerie.

We gebruiken een voorbeeld .json-sjabloon om de afbeelding te configureren. De .json bestand dat we gebruiken is hier: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Als ze niet zeggen geregistreerd, voer het volgende uit:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Variabelen en machtigingen instellen

Als je [Een afbeelding maken en distribueren naar een gedeelde afbeeldingsgalerie](image-builder-gallery.md) hebt gebruikt om je gedeelde afbeeldingsgalerie te maken, heb je al de variabelen gemaakt die we nodig hebben. Zo niet, dan u instellen een aantal variabelen worden gebruikt voor dit voorbeeld.

Voor Preview ondersteunt imagebuilder alleen het maken van aangepaste afbeeldingen in dezelfde resourcegroep als de bronbeheerde afbeelding. Werk de naam van de brongroep in dit voorbeeld bij om dezelfde resourcegroep te zijn als de bronbeheerde afbeelding.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Maak een variabele voor uw abonnements-ID. U dit `az account show | grep id`krijgen met behulp van.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Download de afbeeldingsversie die u wilt bijwerken.

```azurecli-interactive
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
U het voorbeeld dat we gaan gebruiken bekijken door het .json-bestand hier te openen: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) samen met de [sjabloonverwijzing Image Builder.](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 


Download het voorbeeld .json en configureer het met uw variabelen. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>De installatiekopie maken

Verzend de afbeeldingsconfiguratie naar de VM Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Start de afbeeldingsopbouw.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Wacht tot de afbeelding is gebouwd en replicatie voordat u doorgaat naar de volgende stap.


## <a name="create-the-vm"></a>De virtuele machine maken

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>De aanpassing verifiëren
Maak een verbinding met Extern bureaublad met de vm met de gebruikersnaam en het wachtwoord die u hebt ingesteld toen u de virtuele machine maakte. Open in de VM een cmd-prompt en typ:

```console
dir c:\
```

Je zou nu twee mappen moeten zien:
- `buildActions`die is gemaakt in de eerste afbeeldingsversie.
- `buildActions2`die is gemaakt als onderdeel van het bijwerken van de eerste afbeeldingsversie om de tweede afbeeldingsversie te maken.


## <a name="next-steps"></a>Volgende stappen

Zie Sjabloonverwijzing voor [imagebuilder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over de onderdelen van het .json-bestand dat in dit artikel wordt gebruikt.