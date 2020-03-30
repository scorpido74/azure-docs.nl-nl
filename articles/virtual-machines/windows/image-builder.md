---
title: Een Windows-vm maken met Azure Image Builder (voorbeeld)
description: Maak een Windows-vm met de Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: e82d82dac833f7455e3d83d7e11c0c57c4eea816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238809"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Voorbeeld: een Windows-vm maken met Azure Image Builder

In dit artikel ziet u hoe u een aangepaste Windows-afbeelding maken met behulp van de Azure VM Image Builder. In het voorbeeld in dit artikel worden [aanpasapparaten](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) gebruikt voor het aanpassen van de afbeelding:
- PowerShell (ScriptUri) - een [PowerShell-script](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)downloaden en uitvoeren .
- Windows Opnieuw opstarten - start de VM opnieuw.
- PowerShell (inline) - voer een specifieke opdracht uit. In dit voorbeeld wordt een map `mkdir c:\\buildActions`op de virtuele machine gemaakt met behulp van .
- Bestand - kopieer een bestand van GitHub naar de VM. In dit [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) voorbeeld `c:\buildArtifacts\index.html` wordt index.md op de VM.

U ook `buildTimeoutInMinutes`een. De standaardinstelling is 240 minuten en u de buildtijd verhogen om langer lopende builds mogelijk te maken.

We gebruiken een voorbeeld .json-sjabloon om de afbeelding te configureren. Het .json-bestand dat we gebruiken is hier: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="set-variables"></a>Variabelen instellen

We zullen een aantal stukjes informatie herhaaldelijk gebruiken, dus we zullen een aantal variabelen maken om die informatie op te slaan.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Maak een variabele voor uw abonnements-ID. U dit `az account show | grep id`krijgen met behulp van.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Een resourcegroep maken
Deze brongroep wordt gebruikt om het artefact van de afbeeldingsconfiguratiesjabloon en de afbeelding op te slaan.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Machtigingen instellen voor de resourcegroep

Geef Image Builder 'bijdrager' toestemming om de afbeelding in de resourcegroep te maken. Zonder dit, zal het beeld te bouwen mislukken. 

De `--assignee` waarde is de app-registratie-id voor de Image Builder-service. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Voorbeeld van de afbeeldingsconfiguratiesjabloon downloaden

Er is een parametersjabloon voor afbeeldingsconfiguratie gemaakt die u proberen. Download het voorbeeld .json-bestand en configureer het met de variabelen die u eerder hebt ingesteld.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

U dit voorbeeld wijzigen in de `vi`terminal met behulp van een teksteditor zoals.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Voor de bronafbeelding moet u altijd een `latest`versie [opgeven](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)die u niet gebruiken.
> Als u de resourcegroep toevoegt of wijzigt waarin de afbeelding wordt gedistribueerd, moet u ervoor zorgen dat de [machtigingen zijn ingesteld](#set-permissions-on-the-resource-group) op de resourcegroep.
 
## <a name="create-the-image"></a>De installatiekopie maken

De afbeeldingsconfiguratie verzenden naar de VM Image Builder-service

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Als deze is voltooid, wordt een succesbericht teruggestuurd `Image Builder Configuration Template` naar `$imageResourceGroup`de console en wordt een in de . U deze bron zien in de brongroep in de Azure-portal als u 'Verborgen typen weergeven' inschakelt.

Op de achtergrond maakt Image Builder ook een brongroep voor tijdelijke bestanden in uw abonnement. Deze resourcegroep wordt gebruikt voor het maken van afbeeldingen. Het zal in dit formaat:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> U mag de groep voor tijdelijke bronnen niet rechtstreeks verwijderen. Verwijder eerst het artefact van de afbeeldingssjabloon, waardoor de groep met tijdelijke bron wordt verwijderd.

Als de service een fout meldt tijdens het indienen van de afbeeldingsconfiguratiesjabloon:
-  Bekijk deze [stappen voor het oplossen van problemen.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 
- U moet de sjabloon met het volgende fragment verwijderen voordat u opnieuw de indiening probeert.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>De afbeeldingsopbouw starten
Start het imagebuildingproces met [az resource invoke-action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Wacht tot de build is voltooid. Dit kan ongeveer 15 minuten duren.

Als u fouten tegenkomt, raadpleegt u deze [stappen voor het oplossen van problemen.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>De virtuele machine maken

Maak de VM met de afbeelding die u hebt gemaakt. Vervang * \<wachtwoord>* met uw `aibuser` eigen wachtwoord voor de op de VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>De aanpassing verifiëren

Maak een verbinding met Extern bureaublad met de vm met de gebruikersnaam en het wachtwoord die u hebt ingesteld toen u de virtuele machine maakte. Open in de VM een cmd-prompt en typ:

```console
dir c:\
```

U ziet deze twee mappen die zijn gemaakt tijdens het aanpassen van afbeeldingen:
- buildActies
- buildArtefacten

## <a name="clean-up"></a>Opruimen

Wanneer u klaar bent, verwijdert u de resources.

### <a name="delete-the-image-builder-template"></a>De sjabloon voor de opbouw van afbeeldingen verwijderen

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>De afbeeldingsbrongroep verwijderen

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Volgende stappen

Zie Sjabloonverwijzing voor [imagebuilder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over de onderdelen van het .json-bestand dat in dit artikel wordt gebruikt.
