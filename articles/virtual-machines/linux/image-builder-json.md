---
title: Een azure Image Builder-sjabloon maken (voorbeeld)
description: Meer informatie over het maken van een sjabloon voor gebruik met Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246786"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Voorbeeld: een sjabloon voor Azure Image Builder maken 

Azure Image Builder gebruikt een .json-bestand om informatie door te geven aan de Image Builder-service. In dit artikel zullen we gaan over de secties van de json bestand, zodat u uw eigen bouwen. Zie de [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)azure Image Builder voor het bekijken van voorbeelden van volledige .json-bestanden.

Dit is de basissjabloonindeling:

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
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Type- en API-versie

Het `type` is het resourcetype, `"Microsoft.VirtualMachineImages/imageTemplates"`dat moet zijn. De `apiVersion` zal veranderen in de tijd als `"2019-05-01-preview"` de API verandert, maar moet worden voor preview.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Locatie

De locatie is het gebied waar de aangepaste afbeelding wordt gemaakt. Voor het voorbeeld van Image Builder worden de volgende regio's ondersteund:

- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2
- Europa - noord
- Europa -west


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Standaard gebruikt Image Builder een Standard_D1_v2 build VM, u dit overschrijven, bijvoorbeeld als u een afbeelding wilt aanpassen voor een GPU-vm, hebt u een GPU VM-grootte nodig. Dit is optioneel.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Standaard wijzigt Image Builder de grootte van de afbeelding niet, maar de grootte van de bronafbeelding. U de grootte van de OS-schijf (Win en Linux) vergroten, dit is optioneel en een waarde van 0 betekent dezelfde grootte achterlaten als de bronafbeelding. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Als u geen VNET-eigenschappen opgeeft, maakt Image Builder een eigen VNET, Openbaar IP en NSG. Het openbare IP-adres wordt gebruikt voor de service om te communiceren met de build VM, maar als u niet wilt dat een openbare IP of wilt Image Builder om toegang te hebben tot uw bestaande VNET-bronnen, zoals configuratieservers (DSC, Chef, Puppet, Ansible), bestandsshares enz. , dan u een VNET opgeven. Voor meer informatie, bekijk de [netwerkdocumentatie](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder), dit is optioneel.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tags

Dit zijn sleutel-/waardeparen die u opgeven voor de afbeelding die wordt gegenereerd.

## <a name="depends-on-optional"></a>Afhankelijk van (optioneel)

Deze optionele sectie kan worden gebruikt om ervoor te zorgen dat afhankelijkheden worden voltooid voordat u verdergaat. 

```json
    "dependsOn": [],
```

Zie [Resourcesafhankelijkheden definiëren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)voor meer informatie .

## <a name="identity"></a>Identiteit
Standaard ondersteunt Image Builder het gebruik van scripts of het kopiëren van bestanden vanaf meerdere locaties, zoals GitHub en Azure-opslag. Om deze te kunnen gebruiken, moeten ze openbaar toegankelijk zijn.

U ook een door Azure-gebruikers toegewezen beheerde identiteit gebruiken, gedefinieerd door u, om Image Builder toegang te geven tot Azure Storage, zolang de identiteit een minimum van 'Storage Blob Data Reader' heeft gekregen op het Azure-opslagaccount. Dit betekent dat u de opslagblobs niet extern toegankelijk hoeft te maken of SAS-tokens hoeft in te stellen.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Zie Een beheerde identiteit [met Azure-gebruiker gebruiken om toegang te krijgen tot bestanden in Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)voor een volledig voorbeeld.

Image Builder-ondersteuning voor een door gebruikers toegewezen identiteit: • Ondersteunt slechts één identiteit • Ondersteunt geen aangepaste domeinnamen

Zie [Wat zijn beheerde identiteiten voor Azure-resources voor](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)meer informatie.
Zie [Beheerde identiteiten configureren voor Azure-resources op een Azure VM configureren met Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)voor meer informatie over het implementeren van deze functie.

## <a name="properties-source"></a>Eigenschappen: bron

De `source` sectie bevat informatie over de bronafbeelding die wordt gebruikt door Image Builder.

De API vereist een 'SourceType' die de bron voor de image build definieert, momenteel zijn er drie typen:
- PlatformImage - aangegeven dat de bronafbeelding een Marketplace-afbeelding is.
- ManagedImage - gebruik dit bij het starten van een normaal beheerde afbeelding.
- SharedImageVersion - dit wordt gebruikt wanneer u een afbeeldingsversie gebruikt in een gedeelde afbeeldingsgalerie als bron.

### <a name="iso-source"></a>ISO-bron
We zijn deprecating deze functionaliteit van image builder, want er zijn nu [RHEL Bring Your Own Subscription afbeeldingen](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos), bekijk de tijdlijnen hieronder:
    * 31 maart 2020 - Afbeeldingssjablonen met RHEL ISO-bronnen worden nu langer geaccepteerd door de resourceprovider.
    * 30 april 2020- Afbeeldingssjablonen die RHEL ISO-bronnen bevatten, worden niet meer verwerkt.

### <a name="platformimage-source"></a>PlatformImage-bron 
Azure Image Builder ondersteunt Windows Server en client en Linux Azure Marketplace-afbeeldingen, zie [hier](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) voor de volledige lijst. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


De eigenschappen hier zijn dezelfde die worden gebruikt om VM's te maken, met behulp van AZ CLI, voer de onderstaande om de eigenschappen te krijgen: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

U 'laatste' gebruiken in de versie, de versie wordt geëvalueerd wanneer de afbeeldingsbuild plaatsvindt, niet wanneer de sjabloon wordt ingediend. Als u deze functionaliteit gebruikt met de bestemming Gedeelde galerie, u voorkomen dat de sjabloon opnieuw wordt ingediend en u de afbeeldingsopbouw met tussenpozen opnieuw uitvoeren, zodat uw afbeeldingen opnieuw worden gemaakt op de meest recente afbeeldingen.

### <a name="managedimage-source"></a>ManagedImage-bron

Hiermee stelt u de bronafbeelding in als een bestaande beheerde afbeelding van een gegeneraliseerde VHD of VM. De door de bron beheerde afbeelding moet van een ondersteund besturingssysteem zijn en zich in dezelfde regio bevinden als uw Azure Image Builder-sjabloon. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Het `imageId` moet de ResourceId van de beheerde afbeelding. Gebruiken `az image list` om beschikbare afbeeldingen weer te geven.


### <a name="sharedimageversion-source"></a>SharedImageVersion-bron
Hiermee stelt u de bronafbeelding in een bestaande afbeeldingsversie in een gedeelde afbeeldingsgalerie in. De afbeeldingsversie moet van een ondersteund besturingssysteem zijn en de afbeelding moet worden gerepliceerd naar dezelfde regio als uw Azure Image Builder-sjabloon. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Het `imageVersionId` moet de ResourceId van de afbeeldingversie zijn. Gebruik [de lijst met az sig-afbeeldingen](/cli/azure/sig/image-version#az-sig-image-version-list) om afbeeldingsversies weer te geven.

## <a name="properties-buildtimeoutinminutes"></a>Eigenschappen: buildTimeoutInMinutes

Standaard wordt de Image Builder uitgevoerd gedurende 240 minuten. Daarna zal het time-out en stoppen, of de afbeelding te bouwen is voltooid. Als de time-out wordt geraakt, ziet u een fout die vergelijkbaar is met deze:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Als u geen buildTimeoutInMinutes-waarde opgeeft of op 0 instelt, wordt de standaardwaarde gebruikt. U de waarde verhogen of verlagen, tot het maximum van 960 minuten (16 uur). Voor Windows raden we u af dit onder de 60 minuten in te stellen. Als u merkt dat u de time-out raakt, bekijkt u de [logboeken](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)om te zien of de aanpassingsstap wacht op iets als gebruikersinvoer. 

Als u vindt dat u meer tijd nodig hebt voor aanpassingen om te voltooien, stelt u dit in op wat u denkt dat u nodig hebt, met een beetje overhead. Maar, stel het niet te hoog, omdat je zou kunnen hebben om te wachten tot het time-out voor het zien van een fout. 


## <a name="properties-customize"></a>Eigenschappen: aanpassen

Image Builder ondersteunt meerdere 'customizers'. Customizers zijn functies die worden gebruikt om uw afbeelding aan te passen, zoals het uitvoeren van scripts of het opnieuw opstarten van servers. 

Bij `customize`gebruik van : 
- U meerdere customizers gebruiken, maar `name`ze moeten een unieke hebben.
- Aanpasapparaten worden uitgevoerd in de volgorde die is opgegeven in de sjabloon.
- Als één customizer mislukt, mislukt de hele aanpassingscomponent en rapporteert een fout.
- Het wordt sterk aangeraden om het script grondig te testen voordat u het in een sjabloon gebruikt. Het script op uw eigen VM debuggen, is eenvoudiger.
- Plaats geen gevoelige gegevens in de scripts. 
- De scriptlocaties moeten openbaar toegankelijk zijn, tenzij u [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)gebruikt.

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

 
De sectie aanpassen is een array. Azure Image Builder loopt in opeenvolgende volgorde door de customizers. Elke fout in een customizer zal mislukken het bouwproces. 
 
 
### <a name="shell-customizer"></a>Shell-customizer

De shell customizer ondersteunt het uitvoeren van shell scripts, deze moeten openbaar toegankelijk zijn voor de IB om toegang te krijgen tot hen.

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

OS-ondersteuning: Linux 
 
Eigenschappen aanpassen:

- **type** – Shell 
- **naam** - naam voor het bijhouden van de aanpassing 
- **scriptUri** - URI naar de locatie van het bestand 
- **inline** - array van shell commando's, gescheiden door komma's.
- **sha256Checksum** - Waarde van sha256 checksum van het bestand, genereert u dit lokaal, en vervolgens Image Builder zal checksum en valideren.
    * Om de sha256Checksum te genereren, draait u met een terminal op Mac/Linux:`sha256sum <fileName>`


Voor opdrachten die moeten worden uitgevoerd met supergebruikersrechten, moeten ze vooraf zijn vastgesteld met `sudo`.

> [!NOTE]
> Wanneer u de shell-customizer met RHEL ISO-bron uitvoert, moet u ervoor zorgen dat uw eerste aanpassingsshell zich registreert bij een Red Hat-rechtenserver voordat er een aanpassing plaatsvindt. Zodra de aanpassing is voltooid, moet het script zich uitschrijven bij de rechtenserver.

### <a name="windows-restart-customizer"></a>Windows opnieuw opstarten customizer 
Met de Restart-customizer u een Windows-vm opnieuw opstarten en wachten tot deze weer online komt, zodat u software installeren waarvoor een herstart vereist is.  

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

Ondersteuning voor besturingssysteem: Windows
 
Eigenschappen aanpassen:
- **Tekst:** WindowsOpnieuw opstarten
- **opnieuw opstartenCommand** - Opdracht om de herstart uit te voeren (optioneel). De standaardwaarde is `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **opnieuw startenCommandCommand** – Opdracht om te controleren of opnieuw starten is geslaagd (optioneel). 
- **herstartTime-out** - Time-out opnieuw starten opgegeven als een tekenreeks van grootte en eenheid. Bijvoorbeeld (5 `5m` minuten) `2h` of (2 uur). De standaard is: '5m'

### <a name="linux-restart"></a>Linux opnieuw opstarten  
Er is geen Linux Herstart customizer, echter, als u het installeren van drivers, of onderdelen die een herstart vereisen, u ze installeren en een herstart met behulp van de Shell customizer beroep, is er een 20min SSH time-out naar de build VM.

### <a name="powershell-customizer"></a>PowerShell-customizer 
De shell customizer ondersteunt het uitvoeren van PowerShell-scripts en inline opdracht, de scripts moeten openbaar toegankelijk zijn voor de IB om toegang te krijgen tot deze scripts.

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
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS-ondersteuning: Windows en Linux

Eigenschappen aanpassen:

- **type** – PowerShell.
- **scriptUri** - URI naar de locatie van het PowerShell-scriptbestand. 
- **inline** – Inline-opdrachten die moeten worden uitgevoerd, gescheiden door komma's.
- **validExitCodes** - Optionele, geldige codes die kunnen worden geretourneerd uit het script / inline opdracht, dit zal voorkomen dat gemeldfalen van het script / inline opdracht.
- **runElevated** – Optioneel, booleaans, ondersteuning voor het uitvoeren van opdrachten en scripts met verhoogde machtigingen.
- **sha256Checksum** - Waarde van sha256 checksum van het bestand, genereert u dit lokaal, en vervolgens Image Builder zal checksum en valideren.
    * De sha256Checksum genereren met een PowerShell op Windows [Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Bestandsaanpassender

Met de bestandsaanpassing kan imagebuilder een bestand downloaden van een GitHub- of Azure-opslag. Als u een pijplijn voor het bouwen van afbeeldingen hebt die is gebaseerd op buildartefacten, u de bestandsaanwijsapparaat instellen om te downloaden van het buildaandeel en de artefacten naar de afbeelding verplaatsen.  

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

OS-ondersteuning: Linux en Windows 

Eigenschappen van bestandsaanpassen:

- **sourceUri** - een toegankelijk opslageindpunt, dit kan GitHub of Azure-opslag zijn. U slechts één bestand downloaden, niet een hele directory. Als u een map wilt downloaden, gebruikt u een gecomprimeerd bestand en comprimeert u het vervolgens met de Shell- of PowerShell-aan-instellingen. 
- **bestemming** – dit is het volledige bestemmingspad en de bestandsnaam. Elk verwezen pad en submappen moeten bestaan, gebruik maken van de Shell of PowerShell customizers om deze vooraf in te stellen. U de scriptaan-instellingen gebruiken om het pad te maken. 

Dit wordt ondersteund door Windows-mappen en Linux-paden, maar er zijn enkele verschillen: 
- Linux OS's - het enige pad Image builder kan schrijven is / tmp.
- Windows - Geen pad beperking, maar het pad moet bestaan.
 
 
Als er een fout optreedt om het bestand te downloaden of in een opgegeven map te plaatsen, mislukt de stap aanpassen en dit is in het bestand customization.log.

> [!NOTE]
> De bestandsaanpasis alleen geschikt voor kleine bestandsdownloads, < 20MB. Voor grotere bestandsdownloads gebruikt u een script of inline-opdracht, `wget` de `curl`code `Invoke-WebRequest`gebruiken om bestanden te downloaden, zoals Linux of , Windows, .

Bestanden in de bestandsaan-op-mijn-netwerk kunnen worden gedownload van Azure Storage met [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Windows Update-aanpassen
Deze customizer is gebouwd op de [community Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) for Packer, een open source project dat wordt onderhouden door de Packer-community. Microsoft test en valideert de provisioner met de Image Builder-service en ondersteunt het onderzoeken van problemen met de service en werkt aan het oplossen van problemen, maar het open source-project wordt niet officieel ondersteund door Microsoft. Zie de projectrepository voor gedetailleerde documentatie over en hulp bij de Windows Update Provisioner.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Ondersteuning voor besturingssysteem: Windows

Eigenschappen aanpassen:
- **type** – WindowsUpdate.
- **searchCriteria** - Optioneel bepaalt welk type updates zijn geïnstalleerd (Aanbevolen, Belangrijk enz.), BrowseOnly=0 en IsInstalled=0 (Aanbevolen) is de standaardinstelling.
- **filters** – Optioneel u een filter opgeven om updates op te nemen of uit te sluiten.
- **updateLimit** – Optioneel, definieert hoeveel updates kunnen worden geïnstalleerd, standaard 1000.
 
 

### <a name="generalize"></a>Generaliseren 
Azure Image Builder voert standaard ook 'deprovision'-code uit aan het einde van elke aanpassingsfase van de afbeelding, om de afbeelding te 'generaliseren'. Generaliseren is een proces waarbij de afbeelding is ingesteld, zodat deze opnieuw kan worden gebruikt om meerdere VM's te maken. Voor Windows VM's maakt Azure Image Builder gebruik van Sysprep. Voor Linux draait Azure Image Builder 'waagent -deprovision'. 

De opdrachten Image Builder-gebruikers om te generaliseren zijn mogelijk niet geschikt voor elke situatie, dus Azure Image Builder stelt u in staat om deze opdracht zo nodig aan te passen. 

Als u bestaande aanpassingen migreert en u verschillende Sysprep/waagent-opdrachten gebruikt, u de algemene opdrachten Image Builder gebruiken en als de VM-creatie mislukt, gebruikt u uw eigen Sysprep- of waagent-opdrachten.

Als Azure Image Builder een aangepaste Windows-afbeelding maakt en u er een VM van maakt en vervolgens vindt dat het maken van de vm mislukt of niet is voltooid, moet u de Sysprep-documentatie van Windows Server controleren of een ondersteuningsverzoek indienen bij de Windows Server Sysprep Customer Services Support-team, dat problemen kan oplossen en adviseren over het juiste Sysprep-gebruik.


#### <a name="default-sysprep-command"></a>StandaardSysprep, opdracht
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Standaard opdracht Linux-deprovision

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>De opdrachten overschrijven
Als u de opdrachten wilt overschrijven, gebruikt u de PowerShell- of Shell-scriptprovisioners om de opdrachtbestanden met de exacte bestandsnaam te maken en plaatst u ze in de juiste mappen:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder leest deze opdrachten, deze zijn uitgeschreven naar de AIB logs, 'customization.log'. Zie [probleemoplossing](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) voor het verzamelen van logboeken.
 
## <a name="properties-distribute"></a>Eigenschappen: distribueren

Azure Image Builder ondersteunt drie distributiedoelen: 

- **managedImage** - beheerde afbeelding.
- **sharedImage** - Shared Image Gallery.
- **VHD** - VHD in een opslagaccount.

U een afbeelding distribueren naar beide doeltypen in dezelfde configuratie, zie [voorbeelden](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Omdat u meer dan één doel distribueren, behoudt Image Builder een status voor elk `runOutputName`distributiedoel dat kan worden geopend door de.  Het `runOutputName` is een object dat u opvragen na distributie voor informatie over die distributie. U bijvoorbeeld de locatie van de VHD of regio's waar de afbeeldingsversie is gerepliceerd of de SIG Image-versie is gemaakt, opvragen. Dit is een eigenschap van elk distributiedoel. Het `runOutputName` moet uniek zijn voor elk distributiedoel. Hier is een voorbeeld, dit is het opvragen van een gedeelde afbeelding galerie distributie:

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

De afbeeldingsuitvoer is een beheerde afbeeldingsbron.

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
- **imageId** – Resource ID van de doelafbeelding,\<verwachte indeling: /subscriptions/subscriptionId\<>/resourceGroups/destinationResourceGroupName\<>/providers/Microsoft.Compute/images/imageName>
- **locatie** - locatie van de beheerde afbeelding.  
- **runOutputName** – unieke naam voor het identificeren van de distributie.  
- **artefactTags** - Optionele door de gebruiker opgegeven sleutelwaardepaartags.
 
 
> [!NOTE]
> De brongroep bestemming moet bestaan.
> Als u wilt dat de afbeelding naar een andere regio wordt gedistribueerd, wordt de implementatietijd verhoogd. 

### <a name="distribute-sharedimage"></a>Distribueren: sharedImage 
De Azure Shared Image Gallery is een nieuwe Image Management-service waarmee u aangepaste afbeeldingen beheren voor het implementeren, versien en delen van aangepaste afbeeldingen. Azure Image Builder ondersteunt distribueren met deze service, zodat u afbeeldingen distribueren naar regio's die worden ondersteund door gedeelde galerieën. 
 
Een gedeelde afbeeldingsgalerie bestaat uit: 
 
- Galerij - Container voor meerdere gedeelde afbeeldingen. Een galerie wordt in één regio geïmplementeerd.
- Beelddefinities - een conceptuele groepering voor afbeeldingen. 
- Afbeeldingsversies - dit is een afbeeldingstype dat wordt gebruikt voor het implementeren van een VM- of schaalset. Afbeeldingsversies kunnen worden gerepliceerd naar andere regio's waar VM's moeten worden geïmplementeerd.
 
Voordat u distribueren naar de afbeeldingsgalerie, moet u een galerie en een afbeeldingsdefinitie maken, zie [Gedeelde afbeeldingen](shared-images.md). 

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

Eigenschappen distribueren voor gedeelde afbeeldingsgalerieën:

- **type** - sharedImage  
- **galleryImageId** – ID van de gedeelde afbeeldingsgalerie. De indeling is:\</subscriptions/subscriptionId\<>/resourceGroups/resourceGroupName>/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>.
- **runOutputName** – unieke naam voor het identificeren van de distributie.  
- **artefactTags** - Optionele door de gebruiker opgegeven sleutelwaardepaartags.
- **replicatieregio's** - Reeks regio's voor replicatie. Een van de regio's moet de regio zijn waar de galerie wordt geïmplementeerd.
 
> [!NOTE]
> U Azure Image Builder in een andere regio gebruiken dan de galerie, maar de Azure Image Builder-service moet de afbeelding tussen de datacenters overbrengen en dit duurt langer. Image Builder zal de afbeelding automatisch overtypen, op basis van een monotoon geheel getal, u deze momenteel niet opgeven. 

### <a name="distribute-vhd"></a>Distribueren: VHD  
U de uitvoer naar een VHD. U de VHD vervolgens kopiëren en gebruiken om te publiceren naar Azure MarketPlace of te gebruiken met Azure Stack.  

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
 
OS-ondersteuning: Windows en Linux

VHD-parameters distribueren:

- **type** - VHD.
- **runOutputName** – unieke naam voor het identificeren van de distributie.  
- **tags** - Optionele door de gebruiker opgegeven sleutelwaardepaartags.
 
Azure Image Builder staat niet toe dat de gebruiker een opslagaccountlocatie `runOutputs` opgeeft, maar u wel de status van de locatie opvragen om de locatie te krijgen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Zodra de VHD is gemaakt, kopieer deze naar een andere locatie, zo spoedig mogelijk. De VHD wordt opgeslagen in een opslagaccount in de tijdelijke resourcegroep die is gemaakt wanneer de afbeeldingssjabloon wordt ingediend bij de Azure Image Builder-service. Als u de afbeeldingssjabloon verwijdert, verliest u de VHD. 
 
## <a name="next-steps"></a>Volgende stappen

Er zijn voorbeeld .json-bestanden voor verschillende scenario's in de [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
