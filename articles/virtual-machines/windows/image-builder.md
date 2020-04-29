---
title: Een Windows-VM met Azure Image Builder maken (preview)
description: Een Windows-VM maken met de Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869497"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Voor beeld: een Windows-VM maken met Azure Image Builder

In dit artikel wordt uitgelegd hoe u een aangepaste installatie kopie van Windows kunt maken met behulp van de opbouw functie voor installatie kopieën van Azure VM. In het voor beeld in dit artikel worden [aanpassingen](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) gebruikt voor het aanpassen van de installatie kopie:
- Power shell (ScriptUri): down load en voer een [Power shell-script](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)uit.
- Windows opnieuw starten: Hiermee wordt de virtuele machine opnieuw opgestart.
- Power shell (inline)-een specifieke opdracht uitvoeren. In dit voor beeld maakt het een map op de VM met `mkdir c:\\buildActions`behulp van.
- Bestand: Kopieer een bestand van GitHub naar de virtuele machine. In dit voor [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) beeld wordt `c:\buildArtifacts\index.html` index.MD naar op de VM gekopieerd.

U kunt ook een `buildTimeoutInMinutes`opgeven. De standaard waarde is 240 minuten en u kunt een build-tijd verg Roten zodat er meer builds meer worden uitgevoerd.

Er wordt een voor beeld van een JSON-sjabloon gebruikt voor het configureren van de installatie kopie. Het JSON-bestand dat we gebruiken, is hier: [helloImageTemplateWin. json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


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

## <a name="set-variables"></a>Variabelen instellen

We zullen enkele gegevens herhaaldelijk gebruiken, dus we maken een aantal variabelen om deze informatie op te slaan.


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

Maak een variabele voor uw abonnements-ID. U kunt dit doen met `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Een resourcegroep maken
Deze resource groep wordt gebruikt voor het opslaan van het sjabloon artefact van de installatie kopie en de installatie kopie.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Machtigingen voor de resource groep instellen

Geef de machtiging Inzender om de afbeelding in de resource groep te maken. Als dit niet het geval is, mislukt het maken van de installatie kopie. 

De `--assignee` waarde is de app-registratie-id voor de Image Builder-service. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Het voor beeld van een installatie kopie configuratie sjabloon downloaden

Er is een geparametriseerde afbeeldings configuratie sjabloon gemaakt die u kunt proberen. Down load het bestand example. json en configureer dit met de variabelen die u eerder hebt ingesteld.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

U kunt dit voor beeld wijzigen in de Terminal met een tekst editor zoals `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Voor de bron installatie kopie moet u altijd [een versie opgeven](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure). u kunt deze `latest`niet gebruiken.
> Als u de resource groep waaraan de afbeelding is gedistribueerd toevoegt of wijzigt, moet u de [machtigingen instellen](#set-permissions-on-the-resource-group) voor de resource groep.
 
## <a name="create-the-image"></a>De installatiekopie maken

De configuratie van de installatie kopie verzenden naar de VM Image Builder-service

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Als u klaar bent, wordt er een bericht weer gegeven dat de console is voltooid en `Image Builder Configuration Template` maakt u `$imageResourceGroup`een in de. Als u verborgen typen weer geven inschakelt, ziet u deze resource in de resource groep in de Azure Portal.

Op de achtergrond maakt de opbouw functie voor installatie kopieën ook een staging-resource groep in uw abonnement. Deze resource groep wordt gebruikt voor het bouwen van de installatie kopie. Deze heeft de volgende indeling:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> U moet de faserings resource groep niet rechtstreeks verwijderen. Verwijder eerst de afbeeldings sjabloon artefact, waardoor de faserings resource groep wordt verwijderd.

Als de service een fout meldt tijdens het verzenden van de installatie kopie:
-  Bekijk deze [probleemoplossings](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) stappen. 
- U moet de sjabloon verwijderen met behulp van het volgende code fragment voordat u het opnieuw probeert te verzenden.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>De installatie kopie starten
Start het proces voor het bouwen van de installatie kopie met behulp van [AZ resource invoke-Action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Wacht tot het build is voltooid. Dit kan ongeveer 15 minuten duren.

Als er fouten optreden, raadpleegt u deze [probleemoplossings](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) stappen.


## <a name="create-the-vm"></a>De virtuele machine maken

Maak de virtuele machine met behulp van de installatie kopie die u hebt gemaakt. Vervang * \<wachtwoord>* door uw eigen wacht woord voor `aibuser` de virtuele machine.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>De aanpassing controleren

Maak een Extern bureaublad verbinding met de virtuele machine met behulp van de gebruikers naam en het wacht woord die u hebt ingesteld tijdens het maken van de virtuele machine. Open een opdracht prompt in de virtuele machine en typ het volgende:

```console
dir c:\
```

Tijdens het aanpassen van de installatie kopie ziet u deze twee directory's:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Opruimen

Wanneer u klaar bent, verwijdert u de resources.

### <a name="delete-the-image-builder-template"></a>De sjabloon voor de opbouw functie voor installatie kopieën verwijderen

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>De resource groep voor de installatie kopie verwijderen

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de onderdelen van het JSON-bestand dat in dit artikel wordt gebruikt [Image Builder-sjabloon verwijzing](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
