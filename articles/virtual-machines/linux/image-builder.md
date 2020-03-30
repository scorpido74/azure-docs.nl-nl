---
title: Een Linux-vm maken met Azure Image Builder (voorbeeld)
description: Maak een Linux-vm met de Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066671"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Preview: Een Linux-vm maken met Azure Image Builder

In dit artikel ziet u hoe u een aangepaste Linux-afbeelding maken met behulp van de Azure Image Builder en de Azure CLI. In het voorbeeld in dit artikel worden drie verschillende [aanstelrs](image-builder-json.md#properties-customize) gebruikt voor het aanpassen van de afbeelding:

- Shell (ScriptUri) - downloadt en voert een [shell script](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - voert specifieke opdrachten uit. In dit voorbeeld omvatten de inline-opdrachten het maken van een map en het bijwerken van het besturingssysteem.
- Bestand - kopieert een [bestand van GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) naar een map op de VM.

U ook `buildTimeoutInMinutes`een. De standaardinstelling is 240 minuten en u de buildtijd verhogen om langer lopende builds mogelijk te maken.

We gebruiken een voorbeeld .json-sjabloon om de afbeelding te configureren. Het .json-bestand dat we gebruiken is hier: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Voorbeeldvariabelen instellen

We zullen een aantal stukjes informatie herhaaldelijk gebruiken, dus we zullen een aantal variabelen maken om die informatie op te slaan.


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

Maak een variabele voor uw abonnements-ID. U dit `az account show | grep id`krijgen met behulp van.

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Maak de resourcegroep.
Dit wordt gebruikt om het artefact van de afbeeldingsconfiguratiesjabloon en de afbeelding op te slaan.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Machtigingen instellen voor de resourcegroep
Geef Image Builder 'bijdrager' toestemming om de afbeelding in de resourcegroep te maken. Zonder de juiste machtigingen zal de image build mislukken. 

De `--assignee` waarde is de app-registratie-id voor de Image Builder-service. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Het voorbeeld van de sjabloon downloaden

Er is een parametersjabloon voor voorbeeldafbeeldinggemaakt die u gebruiken. Download het voorbeeld .json-bestand en configureer het met de variabelen die u eerder hebt ingesteld.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

U dit voorbeeld .json indien nodig wijzigen. U bijvoorbeeld de waarde `buildTimeoutInMinutes` verhogen om langer lopende builds mogelijk te maken. U het bestand bewerken in Cloud `vi`Shell met behulp van een teksteditor zoals.

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Voor bronafbeelding moet u altijd een versie `latest` [opgeven](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)die u niet gebruiken.
>
> Als u de resourcegroep toevoegt of wijzigt waarin de afbeelding wordt gedistribueerd, moet u ervoor zorgen dat de [machtigingen zijn ingesteld voor de resourcegroep](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>De afbeeldingsconfiguratie verzenden
De afbeeldingsconfiguratie verzenden naar de VM Image Builder-service

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Als het voltooid is, wordt een succesbericht weergegeven en wordt een sjabloonartefact voor de configuratievan de afbeeldingsbouwer in de $imageResourceGroup. U de brongroep in de portal zien als u 'Verborgen typen weergeven' inschakelt.

Op de achtergrond maakt Image Builder ook een brongroep voor tijdelijke bestanden in uw abonnement. Image Builder gebruikt de brongroep met fasering voor de afbeeldingsopbouw. De naam van de resourcegroep vindt `IT_<DestinationResourceGroup>_<TemplateName>`plaats in deze indeling: .

> [!IMPORTANT]
> Verwijder de groep met tijdelijke bestanden niet rechtstreeks. Als u het artefact van de afbeeldingssjabloon verwijdert, wordt de groep met tijdelijke bestanden automatisch verwijderd. Zie voor meer informatie de sectie [Opruimen](#clean-up) aan het einde van dit artikel.

Als de service een fout meldt tijdens het indienen van de afbeeldingsconfiguratiesjabloon, raadpleegt u de [stappen voor het oplossen van problemen.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) U moet de sjabloon ook verwijderen voordat u opnieuw probeert de build in te dienen. Ga als u de sjabloon verwijdert:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>De afbeeldingsopbouw starten

Start de afbeeldingsopbouw.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Wacht tot de build is voltooid, voor dit voorbeeld kan het 10-15 minuten duren.

Als u fouten tegenkomt, raadpleegt u deze [stappen voor het oplossen van problemen.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>De virtuele machine maken

Maak de VM met de afbeelding die u hebt gemaakt.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Haal het IP-adres uit de uitvoer van het maken van de VM en gebruik het naar SSH naar de VM.

```bash
ssh azureuser@<pubIp>
```

U moet zien dat de afbeelding is aangepast met een Bericht van de Dag zodra uw SSH-verbinding tot stand is gebracht!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` wanneer u klaar bent om de SSH-verbinding te sluiten.

## <a name="check-the-source"></a>Controleer de bron

In de sjabloon Image Builder ziet u in de 'Eigenschappen' de bronafbeelding, het aanpassingsscript dat wordt uitgevoerd en waar deze wordt gedistribueerd.

```bash
cat helloImageTemplateLinux.json
```

Zie [Sjabloonverwijzing voor imagebuilder-sjabloon](image-builder-json.md) voor meer gedetailleerde informatie over dit .json-bestand

## <a name="clean-up"></a>Opruimen

Wanneer u klaar bent, u de bronnen verwijderen.

Verwijder de sjabloon voor de opbouw van afbeeldingen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Verwijder de groep afbeeldingsbronnen.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Volgende stappen

Zie [Sjabloonverwijzing](image-builder-json.md)image builder voor meer informatie over de onderdelen van het JSon-bestand dat in dit artikel wordt gebruikt.
