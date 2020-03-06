---
title: Een Azure Image Builder-sjabloon maken (preview)
description: Meer informatie over het maken van een sjabloon voor gebruik met Azure Image Builder.
author: danis
ms.author: danis
ms.date: 01/23/2020
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 08a1ca0c85d69d1a5262f1dcac5d46fb82b1c22b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385102"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Voor beeld: een Azure Image Builder-sjabloon maken 

Azure Image Builder gebruikt een. JSON-bestand om informatie door te geven aan de Image Builder-service. In dit artikel gaan we verder met de secties van het JSON-bestand, zodat u uw eigen kunt bouwen. Zie de [Azure Image Builder github](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)voor meer voor beelden van de volledige json-bestanden.

Dit is de basis indeling van de sjabloon:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>"
            },
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Type en API-versie

Het `type` is het resource type dat moet worden `"Microsoft.VirtualMachineImages/imageTemplates"`. De `apiVersion` wordt na verloop van tijd gewijzigd wanneer de API wordt gewijzigd, maar moet `"2019-05-01-preview"` zijn voor de preview-versie.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Locatie

De locatie is de regio waar de aangepaste installatie kopie wordt gemaakt. Voor de preview-versie van Image Builder worden de volgende regio's ondersteund:

- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Standaard wordt met de opbouw functie voor installatie kopieën een VM voor het bouwen van een Standard_D1_v2 gebruikt. u kunt dit bijvoorbeeld negeren als u een installatie kopie voor een GPU-VM wilt aanpassen, moet u een GPU VM-grootte hebben. Dit is optioneel.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Standaard wordt de grootte van de installatie kopie niet door de opbouw functie voor installatie kopieën gewijzigd, wordt de grootte van de bron afbeelding gebruikt. U kunt de grootte van de besturingssysteem schijf (Win en Linux) aanpassen, Let op: niet te klein is dan de mini maal vereiste ruimte voor het besturings systeem. Dit is optioneel en de waarde 0 betekent dat de grootte van de bron afbeelding gelijk blijft. Dit is optioneel.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="tags"></a>Tags

Dit zijn sleutel-waardeparen die u kunt opgeven voor de afbeelding die wordt gegenereerd.

## <a name="depends-on-optional"></a>Is afhankelijk van (optioneel)

Deze optionele sectie kan worden gebruikt om ervoor te zorgen dat afhankelijkheden worden voltooid voordat u doorgaat. 

```json
    "dependsOn": [],
```

Zie [resource afhankelijkheden definiëren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)voor meer informatie.

## <a name="identity"></a>Identiteit
Standaard ondersteunt Image Builder het gebruik van scripts of het kopiëren van bestanden vanaf meerdere locaties, zoals GitHub en Azure Storage. Als u deze wilt gebruiken, moeten ze openbaar toegankelijk zijn.

U kunt ook een door u gedefinieerde door de gebruiker toegewezen beheerde identiteit gebruiken om de toegang tot de installatie kopie functie toe te staan Azure Storage, zolang aan de identiteit een minimum van ' Storage BLOB data Reader ' is toegekend op het Azure-opslag account. Dit betekent dat u de opslag-blobs niet extern toegankelijk moet maken of SAS-tokens kunt instellen.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Zie een door de [gebruiker toegewezen beheerde identiteit gebruiken om toegang te krijgen tot bestanden in azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)voor een volledig voor beeld.

Image Builder-ondersteuning voor een door de gebruiker toegewezen identiteit: • ondersteunt slechts één identiteit. • biedt geen ondersteuning voor aangepaste domein namen

Zie [Wat is beheerde identiteiten voor Azure-resources?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)voor meer informatie.
Voor meer informatie over het implementeren van deze functie raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met behulp van Azure cli](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Eigenschappen: Bron

De sectie `source` bevat informatie over de bron installatie kopie die wordt gebruikt door de opbouw functie voor installatie kopieën.

Voor de API is een source type vereist dat de bron voor de build van de installatie kopie definieert. momenteel zijn er drie typen:
- ISO: gebruik deze wanneer de bron een RHEL ISO is.
- PlatformImage: er is aangegeven dat de bron afbeelding een Marketplace-installatie kopie is.
- ManagedImage: gebruik dit wanneer u vanaf een normale beheerde installatie kopie begint.
- SharedImageVersion: dit wordt gebruikt wanneer u een installatie kopie versie in een galerie met gedeelde afbeeldingen als de bron gebruikt.

### <a name="iso-source"></a>ISO-bron

Azure Image Builder biedt alleen ondersteuning voor het gebruik van gepubliceerde Red Hat Enterprise Linux 7. x binaire DVD-Iso's, voor beeld. Image Builder ondersteunt:
- RHEL 7,3 
- RHEL 7,4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Als u de waarden voor `sourceURI` en `sha256Checksum` wilt ophalen, gaat u naar `https://access.redhat.com/downloads` en selecteert u vervolgens de product **Red Hat Enterprise Linux**en een ondersteunde versie. 

In de lijst met **installatie Programma's en installatie kopieën voor Red Hat Enterprise Linux server**moet u de koppeling voor Red Hat Enterprise Linux 7. x binaire DVD en de controlesom kopiëren.

> [!NOTE]
> De toegangs tokens van de koppelingen worden met regel matige tussen pozen vernieuwd, dus telkens wanneer u een sjabloon wilt verzenden, moet u controleren of het adres van de RH-koppeling is gewijzigd.
 
### <a name="platformimage-source"></a>PlatformImage-bron 
Azure Image Builder biedt ondersteuning voor Windows Server-en client-en Linux Azure Marketplace-installatie kopieën. Zie [hier](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) voor de volledige lijst. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


De eigenschappen die hier worden gebruikt voor het maken van VM'S met behulp van AZ CLI, voert u de onderstaande stappen uit om de eigenschappen op te halen: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> De versie mag niet ' meest recent ' zijn, u moet de bovenstaande opdracht gebruiken om een versie nummer op te halen. 

### <a name="managedimage-source"></a>ManagedImage-bron

Hiermee stelt u de bron installatie kopie als een bestaande beheerde installatie kopie van een gegeneraliseerde VHD of virtuele machine. De door de bron beheerde installatie kopie moet van een ondersteund besturings systeem zijn en moet zich in dezelfde regio bevinden als de Azure Image Builder-sjabloon. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

De `imageId` moet de ResourceId van de beheerde installatie kopie zijn. Gebruik `az image list` om beschik bare installatie kopieën weer te geven.


### <a name="sharedimageversion-source"></a>SharedImageVersion-bron
Hiermee stelt u de bron afbeelding een versie van een bestaande installatie kopie in een galerie met gedeelde afbeeldingen. De versie van de installatie kopie moet van een ondersteund besturings systeem zijn en de installatie kopie moet worden gerepliceerd naar dezelfde regio als uw Azure Image Builder-sjabloon. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

De `imageVersionId` moet de ResourceId van de versie van de installatie kopie zijn. Gebruik [AZ sig installatie kopie](/cli/azure/sig/image-version#az-sig-image-version-list) van de lijst met installatie kopieën om versie-versies weer te geven.

## <a name="properties-buildtimeoutinminutes"></a>Eigenschappen: buildTimeoutInMinutes

Standaard wordt de opbouw functie voor installatie kopieën gedurende 240 minuten uitgevoerd. Daarna wordt de time-out en stopt, ongeacht of de installatie kopie is gemaakt. Als de time-out wordt weer gegeven, ziet u een fout die vergelijkbaar is met de volgende:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Als u geen buildTimeoutInMinutes-waarde opgeeft of als u deze instelt op 0, wordt de standaard waarde gebruikt. U kunt de waarde verg Roten of verkleinen, tot het maximum van 960mins (16hrs). Voor Windows raden we u aan dit 60 minuten niet in te stellen. Als u merkt dat u de time-out hebt, raadpleegt u de [Logboeken](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)om te zien of de stap voor aanpassing wacht op een soort gebruikers invoer. 

Als u vindt dat er meer tijd nodig is voor het volt ooien van aanpassingen, stelt u deze in op wat u denkt dat u nodig hebt, met een beetje extra overhead. Stel deze echter niet te hoog in omdat u mogelijk moet wachten op time-out voordat u een fout ziet. 


## <a name="properties-customize"></a>Eigenschappen: aanpassen

De opbouw functie voor installatie kopieën ondersteunt meerdere ' Customizers '. Customizers zijn functies die worden gebruikt voor het aanpassen van uw installatie kopie, zoals het uitvoeren van scripts of het opnieuw opstarten van servers. 

Bij het gebruik van `customize`: 
- U kunt meerdere aanpassingen gebruiken, maar ze moeten een uniek `name`hebben.
- Aanpassingen worden uitgevoerd in de volg orde die is opgegeven in de sjabloon.
- Als een aanpassings functie mislukt, mislukt het hele aanpassings onderdeel en wordt er een fout melding weer gegeven.
- U wordt aangeraden het script grondig te testen voordat u het in een sjabloon kunt gebruiken. Fout opsporing van het script op uw eigen VM is eenvoudiger.
- Plaats geen gevoelige gegevens in de scripts. 
- De script locaties moeten openbaar toegankelijk zijn, tenzij u [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)gebruikt.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
De sectie Customize is een matrix. De opbouw functie voor installatie kopieën van Azure wordt door de aanpassings functies in sequentiële volg orde uitgevoerd. Als er een fout optreedt in een aanpassings proces, mislukt het buildproces. 
 
 
### <a name="shell-customizer"></a>Shell-aanpassing

Shell-aanpassing ondersteunt het uitvoeren van shell scripts. deze moeten openbaar toegankelijk zijn voor de IB om ze te openen.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Ondersteuning voor besturings systeem: Linux 
 
Eigenschappen aanpassen:

- **type** – shell 
- **naam** -naam voor het bijhouden van de aanpassing 
- **scriptUri** -URI naar de locatie van het bestand 
- **inline** -matrix van shell opdrachten, gescheiden door komma's.
- **sha256Checksum** -waarde van de sha256-controlesom van het bestand, u genereert dit lokaal en vervolgens wordt de opbouw functie voor installatie kopieën gecontroleerd en gevalideerd.
    * De sha256Checksum genereren met behulp van een Terminal op Mac/Linux run: `sha256sum <fileName>`


Om opdrachten uit te voeren met super gebruikers bevoegdheden, moeten ze worden voorafgegaan door `sudo`.

> [!NOTE]
> Wanneer u de shell-aanpassing uitvoert met de ISO-bron RHEL, moet u ervoor zorgen dat uw eerste aanpassings shell wordt geregistreerd met een Red Hat-rechten server voordat er aanpassingen worden uitgevoerd. Zodra de aanpassing is voltooid, moet het script de registratie bij de rechten server ongedaan maken.

### <a name="windows-restart-customizer"></a>Aanpassings venster voor Windows opnieuw starten 
Met de aanpassings functie voor opnieuw opstarten kunt u een Windows-VM opnieuw opstarten en wachten totdat deze weer online is. Hierdoor kunt u software installeren waarvoor opnieuw moet worden opgestart.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

BESTURINGSSYSTEEM ondersteuning: Windows
 
Eigenschappen aanpassen:
- **Type**: WindowsRestart
- **restartCommand** : opdracht voor het uitvoeren van de herstart (optioneel). De standaardwaarde is `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – opdracht om te controleren of opnieuw opstarten is geslaagd (optioneel). 
- **restartTimeout** : de time-out voor opnieuw opstarten is opgegeven als een teken reeks van grootte en eenheid. Bijvoorbeeld `5m` (5 minuten) of `2h` (2 uur). De standaard waarde is: ' 5 min. '

### <a name="linux-restart"></a>Linux opnieuw opstarten  
Er is geen Linux-aanpassings programma nodig. Als u echter Stuur Programma's installeert of onderdelen die opnieuw moeten worden opgestart, kunt u deze installeren en een herstart aanroepen met de shell-aanpassings programma. er is een 20min SSH-time-out voor de build-VM.

### <a name="powershell-customizer"></a>Power shell-aanpassing 
Shell Customize ondersteunt het uitvoeren van Power shell-scripts en de inline-opdracht, maar de scripts moeten openbaar toegankelijk zijn voor de IB om ze te openen.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

BESTURINGSSYSTEEM ondersteuning: Windows en Linux

Eigenschappen aanpassen:

- **type** – Power shell.
- **scriptUri** -URI naar de locatie van het Power shell-script bestand. 
- **inline** : inline-opdrachten die moeten worden uitgevoerd, gescheiden door komma's.
- **valid_exit_codes** – optioneel, geldige codes die kunnen worden geretourneerd door de script/inline opdracht, waardoor het mislukken van de script/inline-opdracht wordt voor komen.
- **runElevated** : optioneel, Booleaans, ondersteuning voor het uitvoeren van opdrachten en scripts met verhoogde machtigingen.
- **sha256Checksum** -waarde van de sha256-controlesom van het bestand, u genereert dit lokaal en vervolgens wordt de opbouw functie voor installatie kopieën gecontroleerd en gevalideerd.
    * De sha256Checksum genereren met behulp van een Power shell op Windows [Get-hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Bestands aanpassing

Met de file Customizer kunt u met Image Builder een bestand downloaden van een GitHub of Azure-opslag. Als u een pijp lijn voor het bouwen van een installatie kopie hebt die afhankelijk is van het bouwen van artefacten, kunt u de bestands aanpassings functie zo instellen dat deze wordt gedownload via de build-share en de artefacten verplaatsen naar de installatie kopie.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Ondersteuning voor besturings systeem: Linux en Windows 

Eigenschappen van bestands aanpassing:

- **sourceUri** : een toegankelijk eind punt dat kan worden github of Azure Storage. U kunt slechts één bestand downloaden, niet een volledige map. Als u een map wilt downloaden, gebruikt u een gecomprimeerd bestand en comprimeert u het met de shell-of Power shell-aanpassingen. 
- **doel** : dit is het volledige doelpad en de bestands naam. Elk pad en submappen waarnaar wordt verwezen moeten bestaan, de shell-of Power shell-aanpassingen gebruiken om deze vooraf in te stellen. U kunt de script Customizers gebruiken om het pad te maken. 

Dit wordt ondersteund door Windows-mappen en Linux-paden, maar er zijn enkele verschillen: 
- Linux-besturings systeem: de alleen de opbouw functie van het pad naar de afbeelding kan schrijven naar/tmp.
- Windows: geen pad beperken, maar het pad moet bestaan.
 
 
Als er een fout optreedt bij het downloaden van het bestand of in een opgegeven map worden geplaatst, mislukt de stap voor het aanpassen en wordt deze weer gegeven in de aanpassings. log.

> [!NOTE]
> De bestands aanpassing is alleen geschikt voor kleine bestands downloads, < 20 MB. Voor grotere down loads van bestanden gebruikt u een script of inline opdracht, de use-code voor het downloaden van bestanden, zoals Linux `wget` of `curl`, Windows `Invoke-WebRequest`.

Bestanden in file Customize kunnen worden gedownload van Azure Storage met [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generaliseren 
Azure Image Builder voert standaard ook de code ' provisioning ' uit aan het einde van elke aanpassings fase van de installatie kopie tot ' generalize ' in de installatie kopie. Generalize is een proces waarbij de installatie kopie wordt ingesteld zodat deze opnieuw kan worden gebruikt om meerdere virtuele machines te maken. Voor virtuele Windows-machines maakt Azure Image Builder gebruik van Sysprep. Voor Linux voert Azure Image Builder ' waagent-deprovision ' uit. 

De opdrachten Image Builder-gebruikers om te generaliseren zijn mogelijk niet geschikt voor elke situatie. Daarom kunt u met Azure Image Builder deze opdracht aanpassen, als dat nodig is. 

Als u bestaande aanpassingen migreert en u verschillende Sysprep/waagent-opdrachten gebruikt, kunt u de algemene opdrachten van de opbouw functie voor afbeeldingen gebruiken en als het maken van de virtuele machine mislukt, gebruikt u uw eigen Sysprep-of waagent-opdrachten.

Als de opbouw functie van Azure image een Windows-aangepaste installatie kopie maakt en u hiervan een VM maakt, kunt u er vervolgens voor zorgen dat de VM wordt gemaakt of mislukt, moet u de Windows Server Sysprep-documentatie raadplegen of een ondersteunings aanvraag met de Ondersteunings team van Windows Server Sysprep Customer Services, die het juiste Sysprep-gebruik kan oplossen en adviseren.


#### <a name="default-sysprep-command"></a>Standaard Sysprep-opdracht
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Standaard opdracht voor het ongedaan maken van Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>De opdrachten overschrijven
Als u de opdrachten wilt onderdrukken, gebruikt u de Power shell-of shell-script inrichtingen om de opdracht bestanden met de exacte bestands naam te maken en deze in de juiste directory's te plaatsen:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

Met de opbouw functie voor installatie kopieën worden deze opdrachten gelezen, die worden wegge schreven naar de AIB-logboeken Customization. log. Zie [probleem oplossing](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) voor het verzamelen van Logboeken.
 
## <a name="properties-distribute"></a>Eigenschappen: distribueren

Azure Image Builder ondersteunt drie distributie doelen: 

- **managedImage** -beheerde installatie kopie.
- Galerie met **sharedImage** -gedeelde afbeeldingen.
- **VHD** -VHD in een opslag account.

U kunt een installatie kopie distribueren naar beide doel typen in dezelfde configuratie, Zie [voor beelden](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Omdat er meer dan één doel kan zijn om naar te distribueren, houdt Image Builder een status bij voor elk distributie doel dat toegankelijk is door query's uit te stellen op de `runOutputName`.  De `runOutputName` is een object waarmee u een query kunt uitvoeren op distributie voor informatie over die distributie. U kunt bijvoorbeeld een query uitvoeren op de locatie van de VHD, of regio's waarnaar de versie van de installatie kopie is gerepliceerd, of de versie van de SIG-installatie kopie is gemaakt. Dit is een eigenschap van elke distributie doel. De `runOutputName` moet uniek zijn voor elk distributie doel. Hier volgt een voor beeld van het uitvoeren van een query op de distributie van een gedeelde installatie kopie galerie:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Uitvoer:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribueren: managedImage

De uitvoer van de installatie kopie is een beheerde afbeeldings bron.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Eigenschappen distribueren:
- **type** – managedImage 
- **imageId** – resource-id van de doel afbeelding, verwachte indeling:/Subscriptions/\<subscriptionId >/ResourceGroups/\<destinationResourceGroupName >/providers/Microsoft.Compute/images/\<image naam >
- **locatie** : locatie van de beheerde installatie kopie.  
- **runOutputName** : een unieke naam voor het identificeren van de distributie.  
- **artifactTags** -optionele door de gebruiker opgegeven sleutel waarde-paar tags.
 
 
> [!NOTE]
> De doel resource groep moet bestaan.
> Als u wilt dat de afbeelding wordt gedistribueerd naar een andere regio, neemt de implementatie tijd toe. 

### <a name="distribute-sharedimage"></a>Distribueren: sharedImage 
De galerie met gedeelde Azure-installatie kopieën is een nieuwe service voor het beheer van installatie kopieën waarmee u de installatie kopie regio kunt beheren, versie beheer en delen van aangepaste installatie kopieën. Azure Image Builder ondersteunt de distributie met deze service, zodat u installatie kopieën kunt distribueren naar regio's die worden ondersteund door de galerie met gedeelde afbeeldingen. 
 
Een galerie met gedeelde afbeeldingen bestaat uit: 
 
- Galerie-container voor meerdere gedeelde installatie kopieën. Een galerie wordt in één regio geïmplementeerd.
- Afbeeldings definities: een conceptuele groepering voor installatie kopieën. 
- Installatie kopie versies: dit is een afbeeldings type dat wordt gebruikt voor het implementeren van een virtuele machine of schaalset. Installatie kopie versies kunnen worden gerepliceerd naar andere regio's waar Vm's moeten worden geïmplementeerd.
 
Voordat u naar de galerie met installatie kopieën kunt distribueren, moet u een galerie en een definitie van een installatie kopie maken. Zie [gedeelde installatie kopieën](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Eigenschappen voor gedeelde afbeeldings galerieën distribueren:

- **type** -sharedImage  
- **galleryImageId** : id van de galerie met gedeelde afbeeldingen. De indeling is:/Subscriptions/\<subscriptionId >/resourceGroups/\<resourceGroupName >/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName >/Images/\<imageGalleryName >.
- **runOutputName** : een unieke naam voor het identificeren van de distributie.  
- **artifactTags** -optionele door de gebruiker opgegeven sleutel waarde-paar tags.
- **replicationRegions** : matrix van regio's voor replicatie. Een van de regio's moet de regio zijn waarin de galerie wordt geïmplementeerd.
 
> [!NOTE]
> U kunt Azure image builder in een andere regio gebruiken voor de galerie, maar de installatie kopie van de Azure Image Builder-service moet worden overgedragen tussen de data centers. dit duurt langer. De installatie kopie wordt door de opbouw functie voor installatie kopieën automatisch een versie op basis van een monotone integer opgegeven. u kunt deze op dit moment niet opgeven. 

### <a name="distribute-vhd"></a>Distribueren: VHD  
U kunt naar een VHD uitvoeren. U kunt de VHD vervolgens kopiëren en gebruiken om te publiceren naar Azure MarketPlace of gebruiken met Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
BESTURINGSSYSTEEM ondersteuning: Windows en Linux

VHD-para meters distribueren:

- **type** -VHD.
- **runOutputName** : een unieke naam voor het identificeren van de distributie.  
- **Tags** -optioneel door de gebruiker opgegeven sleutel waarde-paar tags.
 
De gebruiker kan met Azure Image Builder geen locatie opgeven voor het opslag account, maar u kunt wel een query uitvoeren op de status van de `runOutputs` om de locatie op te halen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Zodra de VHD is gemaakt, kopieert u deze naar een andere locatie, zo snel mogelijk. De VHD wordt opgeslagen in een opslag account in de tijdelijke resource groep die is gemaakt wanneer de installatie kopie sjabloon wordt verzonden naar de Azure Image Builder-service. Als u de afbeeldings sjabloon verwijdert, gaat de VHD verloren. 
 
## <a name="next-steps"></a>Volgende stappen

Er zijn voor beelden van json-bestanden voor verschillende scenario's in de [Azure Image Builder-github](https://github.com/danielsollondon/azvmimagebuilder).
 
