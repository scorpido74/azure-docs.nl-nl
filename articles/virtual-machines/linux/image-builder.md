---
title: Een virtuele Linux-machine met Azure Image Builder maken (preview)
description: Een virtuele Linux-machine maken met de Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: b50b2a9bfca99e1868c083759cce26bb176789f4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792408"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Voor beeld: een virtuele Linux-machine maken met Azure Image Builder

Dit artikel laat u zien hoe u een aangepaste Linux-installatie kopie kunt maken met behulp van de Azure Image Builder en de Azure CLI. In het voor beeld in dit artikel worden drie verschillende [aanpassingen](image-builder-json.md#properties-customize) gebruikt voor het aanpassen van de installatie kopie:

- Shell (ScriptUri): Hiermee wordt een [shell script](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)gedownload en uitgevoerd.
- Shell (inline)-voert specifieke opdrachten uit. In dit voor beeld omvatten de inline-opdrachten het maken van een map en het bijwerken van het besturings systeem.
- Bestand: kopieert een [bestand van github](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) naar een map op de virtuele machine.

U kunt ook een `buildTimeoutInMinutes`opgeven. De standaard waarde is 240 minuten en u kunt een build-tijd verg Roten zodat er meer builds meer worden uitgevoerd.

Er wordt een voor beeld van een JSON-sjabloon gebruikt voor het configureren van de installatie kopie. Het JSON-bestand dat we gebruiken, is hier: [helloImageTemplateLinux. json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Voor beeld van installatie variabelen

We zullen enkele gegevens herhaaldelijk gebruiken, dus we maken een aantal variabelen om deze informatie op te slaan.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Maak een variabele voor uw abonnements-ID. U kunt dit doen met `az account show | grep id`.

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Maak de resourcegroep.
Dit wordt gebruikt voor het opslaan van het sjabloon artefact van de installatie kopie en de installatie kopie.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Machtigingen voor de resource groep instellen
Geef de machtiging Inzender om de afbeelding in de resource groep te maken. Zonder de juiste machtigingen kan de installatie kopie niet worden gemaakt. 

De `--assignee` waarde is de app-registratie-id voor de Image Builder-service. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Het voor beeld van de sjabloon downloaden

Er is een para meter configuratie sjabloon voor voorbeeld afbeelding gemaakt die u kunt gebruiken. Down load het voor beeld. JSON-bestand en configureer dit met de variabelen die u eerder hebt ingesteld.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

U kunt dit voor beeld. json aanpassen als dat nodig is. U kunt bijvoorbeeld de waarde verg Roten `buildTimeoutInMinutes` om meer builds te kunnen uitvoeren. U kunt het bestand bewerken in Cloud Shell met behulp van een `vi`tekst editor, zoals.

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Voor de bron installatie kopie moet u altijd [een versie opgeven](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure). u kunt `latest`deze niet gebruiken.
>
> Als u de resource groep toevoegt of wijzigt waar de installatie kopie wordt gedistribueerd, moet u controleren of de [machtigingen zijn ingesteld voor de resource groep](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>De configuratie van de installatie kopie verzenden
De configuratie van de installatie kopie verzenden naar de VM Image Builder-service

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Als de bewerking is voltooid, wordt er een bericht weer gegeven en wordt er een Image Builder-configuratie sjabloon artefact gemaakt in de $imageResourceGroup. U kunt de resource groep in de portal zien als u verborgen typen weer geven inschakelt.

Bovendien maakt de opbouw functie voor afbeeldingen op de achtergrond een staging-resource groep in uw abonnement. In de opbouw functie voor installatie kopieën wordt de resource groep staging gebruikt voor het bouwen van de installatie kopie. De naam van de resource groep heeft de volgende indeling: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Verwijder de faserings resource groep niet rechtstreeks. Als u het afbeeldings sjabloon artefact verwijdert, wordt de resource groep staging automatisch verwijderd. Zie de sectie [opschonen](#clean-up) aan het einde van dit artikel voor meer informatie.

Zie de stappen voor [probleem oplossing](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) als de service een fout meldt tijdens het verzenden van de afbeeldings configuratie sjabloon. U moet de sjabloon ook verwijderen voordat u opnieuw probeert de build te verzenden. De sjabloon verwijderen:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>De installatie kopie starten

Start het maken van de installatie kopie.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Wacht totdat de build is voltooid. Dit kan 10-15 minuten duren.

Als er fouten optreden, raadpleegt u deze [probleemoplossings](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) stappen.


## <a name="create-the-vm"></a>De virtuele machine maken

Maak de virtuele machine met behulp van de installatie kopie die u hebt gemaakt.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Haal het IP-adres op uit de uitvoer van het maken van de virtuele machine en gebruik deze om te SSHen naar de virtuele machine.

```bash
ssh azureuser@<pubIp>
```

U ziet dat de installatie kopie is aangepast met een bericht van de dag zodra uw SSH-verbinding tot stand is gebracht.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` wanneer u klaar bent om de SSH-verbinding te sluiten.

## <a name="check-the-source"></a>Controleer de bron

In de afbeelding Builder-sjabloon, in de ' Eigenschappen ', ziet u de bron afbeelding, het aanpassings script dat wordt uitgevoerd en de locatie waar deze wordt gedistribueerd.

```bash
cat helloImageTemplateLinux.json
```

Zie voor meer informatie over dit JSON-bestand [Image Builder-sjabloon verwijzing](image-builder-json.md)

## <a name="clean-up"></a>Opruimen

Wanneer u klaar bent, kunt u de resources verwijderen.

Verwijder de sjabloon voor de opbouw functie voor installatie kopieën.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Verwijder de resource groep voor de installatie kopie.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de onderdelen van het JSON-bestand dat in dit artikel wordt gebruikt [Image Builder-sjabloon verwijzing](image-builder-json.md).
