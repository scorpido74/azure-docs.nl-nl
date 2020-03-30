---
title: Een virtual machine-afbeelding maken en een door de gebruiker toegewezen beheerde identiteit gebruiken om toegang te krijgen tot bestanden in Azure Storage (voorbeeld)
description: Maak virtuele machineafbeelding met Azure Image Builder, waarmee toegang kan worden verkregen tot bestanden die zijn opgeslagen in Azure Storage met behulp van door de gebruiker toegewezen beheerde identiteit.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060751"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Een afbeelding maken en een door de gebruiker toegewezen beheerde identiteit gebruiken om toegang te krijgen tot bestanden in Azure Storage 

Azure Image Builder ondersteunt het gebruik van scripts of het kopiëren van bestanden vanaf meerdere locaties, zoals GitHub en Azure-opslag, enz. Om deze te gebruiken, moeten ze extern toegankelijk zijn geweest voor Azure Image Builder, maar u Azure Storage-blobs beveiligen met SAS-tokens.

In dit artikel ziet u hoe u een aangepaste afbeelding maakt met behulp van de Azure VM Image Builder, waarbij de service een door de gebruiker toegewezen beheerde identiteit gebruikt om toegang te krijgen tot bestanden in [Azure-opslag](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor het aanpassen van de afbeelding, zonder dat u de bestanden openbaar toegankelijk hoeft te maken of SAS-tokens hoeft in te stellen.

In het onderstaande voorbeeld maakt u twee brongroepen, één wordt gebruikt voor de aangepaste afbeelding en de andere host een Azure Storage-account, dat een scriptbestand bevat. Dit simuleert een scenario in het echte leven, waarbij u mogelijk artefacten of afbeeldingsbestanden in verschillende opslagaccounts hebt, buiten Image Builder. U maakt een door de gebruiker toegewezen identiteit en verleent vervolgens die leesmachtigingen voor het scriptbestand, maar u stelt geen openbare toegang tot dat bestand in. U gebruikt vervolgens de Shell-customizer om dat script te downloaden en uit te voeren vanaf het opslagaccount.


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


## <a name="create-a-resource-group"></a>Een resourcegroep maken

We zullen een aantal stukjes informatie herhaaldelijk gebruiken, dus we zullen een aantal variabelen maken om die informatie op te slaan.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Maak een variabele voor uw abonnements-ID. U dit `az account show | grep id`krijgen met behulp van.

```console
subscriptionID=<Your subscription ID>
```

Maak de resourcegroepen voor zowel de afbeelding als de scriptopslag.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Maak de opslag en kopieer het voorbeeldscript erin vanuit GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Geef Image Builder toestemming om resources te maken in de groep afbeeldingsbronnen. De `--assignee` waarde is de app-registratie-id voor de Image Builder-service. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Beheerde identiteit met gebruiker maken

Maak de identiteit en wijs machtigingen toe voor het scriptopslagaccount. Zie [Door gebruikers toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)voor meer informatie .

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Het voorbeeld wijzigen

Download het voorbeeld .json-bestand en configureer het met de variabelen die u hebt gemaakt.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>De installatiekopie maken

Verzend de afbeeldingsconfiguratie naar de Azure Image Builder-service.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Start de afbeeldingsopbouw.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Wacht tot de build is voltooid. Dit kan ongeveer 15 minuten duren.

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een VM op basis van de afbeelding. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Nadat de VM is gemaakt, start u een SSH-sessie met de VM.

```console
ssh aibuser@<publicIp>
```

U moet zien dat de afbeelding is aangepast met een Bericht van de Dag zodra uw SSH-verbinding tot stand is gebracht!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Opruimen

Wanneer u klaar bent, u de bronnen verwijderen als ze niet meer nodig zijn.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Zie [Probleemoplossing](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)voor problemen met Azure Image Builder als u problemen ondervindt bij het werken met Azure Image Builder.