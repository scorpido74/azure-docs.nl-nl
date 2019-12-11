---
title: Azure Image Builder gebruiken met een galerie met installatie kopieën voor Windows-Vm's (preview-versie)
description: Windows VM-installatie kopieën maken met Azure Image Builder en de galerie met gedeelde installatie kopieën.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 1d9763ccc5f5967b9fc9932a11fff655e6120fd0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976074"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Voor beeld: een Windows-installatie kopie maken en deze distribueren naar een gedeelde installatie kopie galerie 

In dit artikel wordt uitgelegd hoe u de Azure Image Builder kunt gebruiken om een installatie kopie versie te maken in een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md)en vervolgens de installatie kopie wereld wijd te distribueren.

Er wordt een JSON-sjabloon gebruikt om de installatie kopie te configureren. Het JSON-bestand dat we gebruiken, is hier: [helloImageTemplateforWinSIG. json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

De sjabloon maakt gebruik van [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) als de waarde voor de sectie `distribute` van de sjabloon om de installatie kopie naar een galerie met gedeelde afbeeldingen te distribueren.

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Als ze niet zijn geregistreerd, voert u de volgende handelingen uit:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Variabelen en machtigingen instellen 

We zullen enkele gegevens herhaaldelijk gebruiken, dus we maken een aantal variabelen om deze informatie op te slaan. Vervang de waarden voor de variabelen, zoals `username` en `vmpassword`, door uw eigen gegevens.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Maak een variabele voor uw abonnements-ID. U kunt dit doen met behulp van `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Maak de resourcegroep.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Stel de Azure Image Builder-machtiging in voor het maken van resources in die resource groep. De `--assignee` waarde is de ID van de app-registratie voor de Image Builder-service. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Een definitie en galerie voor een installatie kopie maken

Als u de opbouw functie voor afbeeldingen wilt gebruiken met een galerie met gedeelde afbeeldingen, moet u een bestaande afbeeldings galerie en afbeeldings definitie hebben. Met de opbouw functie voor installatie kopieën wordt de installatie kopie galerie en de definitie van de installatie kopie niet voor u gemaakt.

Als u nog geen galerie en afbeeldings definitie hebt om te gebruiken, maakt u deze eerst. Maak eerst een galerie met installatie kopieën.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Maak vervolgens een definitie voor de installatie kopie.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>De json downloaden en configureren

Down load de JSON-sjabloon en configureer deze met de variabelen.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>De versie van de installatie kopie maken

Met dit volgende deel wordt de installatie kopie versie gemaakt in de galerie. 

Verzend de configuratie van de installatie kopie naar de Azure Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Start het maken van de installatie kopie.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Het maken van de installatie kopie en het repliceren naar beide regio's kan enige tijd duren. Wacht tot dit onderdeel is voltooid voordat u doorgaat met het maken van een virtuele machine.


## <a name="create-the-vm"></a>De virtuele machine maken

Maak een virtuele machine op basis van de installatie kopie versie die is gemaakt door de opbouw functie voor installatie kopieën van Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>De aanpassing controleren
Maak een Extern bureaublad verbinding met de virtuele machine met behulp van de gebruikers naam en het wacht woord die u hebt ingesteld tijdens het maken van de virtuele machine. Open een opdracht prompt in de virtuele machine en typ het volgende:

```console
dir c:\
```

Als het goed is, ziet u een map met de naam `buildActions` die is gemaakt tijdens het aanpassen van de installatie kopie.


## <a name="clean-up-resources"></a>Resources opschonen
Als u de installatie kopie nu opnieuw wilt aanpassen om een nieuwe versie van dezelfde installatie kopie te maken, slaat u **deze stap over** en gaat u verder met het [gebruik van Azure Image Builder om een andere versie van de installatie kopie te maken](image-builder-gallery-update-image-version.md).


Hiermee verwijdert u de installatie kopie die is gemaakt, samen met alle andere bron bestanden. Zorg ervoor dat u klaar bent met deze implementatie voordat u de resources verwijdert.

Bij het verwijderen van de afbeeldingen galerie-resources, moet u alle installatie kopieën verwijderen voordat u de definitie van de installatie kopie kunt verwijderen die wordt gebruikt om ze te maken. Als u een galerie wilt verwijderen, moet u eerst alle definities van de installatie kopieën in de galerie hebben verwijderd.

Verwijder de sjabloon voor de opbouw functie voor installatie kopieën.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Haal de installatie kopie versie op die is gemaakt door de opbouw functie voor afbeeldingen, dit begint altijd met `0.`en verwijder vervolgens de versie van de installatie kopie

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


De definitie van de installatie kopie verwijderen.

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

Verwijder de resource groep.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Image Builder gebruiken om een andere versie](image-builder-gallery-update-image-version.md)van de installatie kopie te maken voor meer informatie over het bijwerken van de versie van de installatie kopie die u hebt gemaakt.