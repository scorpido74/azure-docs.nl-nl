---
title: Aangepaste scripts uitvoeren op Linux-Vm's in azure | Microsoft Docs
description: Configuratie taken voor Linux-VM'S automatiseren met behulp van de aangepaste script extensie v2
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: roiyz
ms.openlocfilehash: 23354ce310ad4916c8fa2eb9cb99c5e4521b4a62
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084739"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Gebruik de aangepaste script extensie versie 2 van Azure met virtuele Linux-machines
De aangepaste script extensie versie 2 downloadt en voert scripts uit op virtuele machines van Azure. Deze uitbrei ding is handig voor configuratie na de implementatie, software-installatie of een andere configuratie/beheer taak. U kunt scripts downloaden van Azure Storage of een andere toegankelijke Internet locatie, of u kunt deze opgeven voor de runtime van de uitbrei ding. 

De aangepaste script extensie kan worden geïntegreerd met Azure Resource Manager sjablonen. U kunt dit ook uitvoeren met behulp van Azure CLI, Power shell of de Azure Virtual Machines REST API.

In dit artikel wordt beschreven hoe u de aangepaste script extensie van Azure CLI gebruikt en hoe u de extensie kunt uitvoeren met behulp van een Azure Resource Manager sjabloon. Dit artikel bevat ook stappen voor het oplossen van problemen met Linux-systemen.


Er zijn twee aangepaste Linux-script uitbreidingen:
* Versie 1: micro soft. OSTCExtensions. CustomScriptForLinux
* Versie 2: micro soft. Azure. Extensions. CustomScript

Schakel de nieuwe en bestaande implementaties uit om in plaats daarvan de nieuwe versie 2 te gebruiken. De nieuwe versie is bedoeld als vervanging van een vervolg keuzelijst. Daarom is de migratie zo eenvoudig als het wijzigen van de naam en versie, u hoeft de configuratie van de extensie niet te wijzigen.


### <a name="operating-system"></a>Besturingssysteem

De aangepaste script extensie voor Linux wordt uitgevoerd op de extensie die wordt ondersteund door de extensie van het besturings systeem. Zie dit [artikel](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems)voor meer informatie.

### <a name="script-location"></a>Locatie van script

U kunt de extensie gebruiken om uw Azure Blob Storage-referenties te gebruiken voor toegang tot Azure Blob-opslag. Het is ook mogelijk dat de locatie van het script een wille keurige waar is, zolang de virtuele machine kan worden doorgestuurd naar dat eind punt, zoals GitHub, interne bestands server, enzovoort.

### <a name="internet-connectivity"></a>Internet verbinding
Als u een script extern moet downloaden, zoals GitHub of Azure Storage, moeten er extra poorten voor de firewall/netwerk beveiligings groep worden geopend. Als uw script zich bijvoorbeeld in Azure Storage bevindt, kunt u toegang toestaan via de Azure NSG-service tags voor [opslag](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Als uw script zich op een lokale server bevindt, moet u mogelijk nog steeds extra firewall-of netwerk beveiligings groep poorten openen.

### <a name="tips-and-tricks"></a>Tips en trucs
* De meeste fouten voor deze extensie worden veroorzaakt door syntaxisfouten in het script. Test de scriptuitvoeringen zonder fouten en schakel aanvullende logboekregistratie in het script in om gemakkelijker te achterhalen waar het misging.
* Schrijf scripts die idempotent zijn, zodat het systeem niet wordt gewijzigd als de scripts per ongeluk opnieuw meer dan één keer worden uitgevoerd.
* Zorg ervoor dat de scripts geen gebruikers invoer vereisen wanneer ze worden uitgevoerd.
* Er is 90 minuten om het script uit te voeren, wat langer resulteert in een mislukte inrichting van de uitbrei ding.
* Start de computer niet opnieuw op in het script, waardoor er problemen zijn met andere uitbrei dingen die worden geïnstalleerd en na het opnieuw opstarten. de uitbrei ding wordt na het opnieuw opstarten niet voortgezet. 
* Hebt u een script dat een herstart veroorzaakt? Installeer dan toepassingen en voer scripts uit etc. U moet de herstart plannen met behulp van een cron-taak of gebruikmaken van hulpprogram ma's zoals DSC, of chef, puppet-extensies.
* De uitbrei ding voert slechts één script uit, als u een script wilt uitvoeren op elke keer dat u opstart, dan kunt u een [Cloud-init-installatie kopie](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) gebruiken en een script [per opstart](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) module gebruiken. U kunt ook het script gebruiken om een systeem-service-eenheid te maken.
* Als u wilt plannen wanneer een script wordt uitgevoerd, moet u de extensie gebruiken om een cron-taak te maken. 
* Wanneer het script wordt uitgevoerd, ziet u alleen de extensiestatus 'overgang maken' van de Azure-portal of CLI. Als u meer frequente status updates van een actief script wilt, moet u uw eigen oplossing maken.
* Aangepaste script extensie biedt geen systeem eigen ondersteuning voor proxy servers, maar u kunt wel een hulp programma voor bestands overdracht gebruiken dat proxy servers in uw script ondersteunt, zoals *krul*. 
* Houd er rekening mee dat niet-standaard adreslijst locaties waarvan uw scripts of opdrachten afhankelijk zijn, logica hebben om dit te kunnen afhandelen.



## <a name="extension-schema"></a>Extensieschema

De configuratie van de aangepaste script extensie bevat dingen als de script locatie en de opdracht die moet worden uitgevoerd. U kunt deze configuratie opslaan in configuratie bestanden, opgeven op de opdracht regel of in een Azure Resource Manager sjabloon opgeven. 

U kunt gevoelige gegevens in een beveiligde configuratie opslaan, die zijn versleuteld en alleen worden ontsleuteld in de virtuele machine. De beveiligde configuratie is handig wanneer de uitvoering-opdracht geheimen bevat zoals een wacht woord.

Deze items moeten worden behandeld als gevoelige gegevens en worden opgegeven in de configuratie van de instellingen voor beveiligde extensies. Azure-VM-extensie beveiligde instellingsgegevens versleuteld en alleen op de virtuele doelmachine worden ontsleuteld.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Waarden van eigenschappen

| Name | Waarde / voorbeeld | Gegevenstype | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft.Compute.Extensions | string |
| Type | CustomScript | string |
| typeHandlerVersion | 2.0 | int |
| fileUris (bijvoorbeeld) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (bijvoorbeeld) | python MyPythonScript.py \<mijn-param1 > | string |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (bijvoorbeeld) | false | boolean |
| timestamp (bijvoorbeeld) | 123456789 | 32-bits geheel getal |
| storageAccountName (bijvoorbeeld) | examplestorageacct | string |
| storageAccountKey (bijvoorbeeld) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Details van eigenschaps waarde
* `skipDos2Unix`: (optioneel, Booleaans) overs Laan dos2unix conversie van bestands-Url's of script bestanden op basis van een script.
* `timestamp`(optioneel, 32-bits geheel getal) gebruik dit veld alleen om een nieuwe uitvoering van het script te activeren door de waarde van dit veld te wijzigen.  Een gehele waarde is acceptabel; de waarde mag alleen gelijk zijn aan die van de vorige.
  * `commandToExecute`: (**vereist** als script niet is ingesteld, teken reeks) het ingangs punt script dat moet worden uitgevoerd. Gebruik dit veld in plaats daarvan als uw opdracht geheimen bevat zoals wacht woorden.
* `script`: (**vereist** als commandToExecute niet is ingesteld, String) een met base64 gecodeerd (en optioneel gzip'ed) script dat wordt uitgevoerd door/bin/sh.
* `fileUris`: (optioneel, teken reeks matrix) de Url's voor bestanden die moeten worden gedownload.
* `storageAccountName`: (optioneel, String) de naam van het opslag account. Als u opslag referenties opgeeft, moeten `fileUris` alle url's voor Azure-blobs zijn.
* `storageAccountKey`: (optioneel, String) de toegangs sleutel van het opslag account


De volgende waarden kunnen worden ingesteld in de open bare of beveiligde instellingen. de uitbrei ding weigert de configuratie, waarbij de waarden hieronder worden ingesteld in de open bare en beveiligde instellingen.
* `commandToExecute`
* `script`
* `fileUris`

Het gebruik van open bare instellingen kan handig zijn voor het opsporen van fouten, maar het wordt ten zeerste aanbevolen om beveiligde instellingen te gebruiken.

Open bare instellingen worden in ongecodeerde tekst verzonden naar de virtuele machine waarop het script wordt uitgevoerd.  Beveiligde instellingen worden versleuteld met een sleutel die alleen bekend is bij Azure en de virtuele machine. De instellingen worden opgeslagen op de VM als ze zijn verzonden, d.w.z. als de instellingen zijn versleuteld, worden deze versleuteld opgeslagen op de VM. Het certificaat dat wordt gebruikt voor het ontsleutelen van de versleutelde waarden wordt opgeslagen op de virtuele machine en wordt gebruikt voor het ontsleutelen van instellingen (indien nodig) tijdens runtime.

#### <a name="property-skipdos2unix"></a>Eigenschap: skipDos2Unix

De standaard waarde is False, wat betekent dat de dos2unix-conversie **wordt** uitgevoerd.

Met de vorige versie van CustomScript, micro soft. OSTCExtensions. CustomScriptForLinux, worden DOS-bestanden automatisch geconverteerd naar UNIX- `\r\n` bestanden `\n`door te vertalen naar. Deze vertaling bestaat nog en is standaard ingeschakeld. Deze conversie wordt toegepast op alle bestanden die zijn gedownload van fileUris of de script instelling op basis van een van de volgende criteria.

* Als de uitbrei ding een `.sh`van `.txt` `.py`is,, `.pl` of wordt geconverteerd. De script instelling komt altijd overeen met deze criteria, omdat ervan wordt uitgegaan dat een script wordt uitgevoerd met/bin/sh, en dat wordt opgeslagen als script.sh op de virtuele machine.
* Als het bestand begint met `#!`.

De dos2unix-conversie kan worden overgeslagen door de skipDos2Unix in te stellen op True.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Eigenschap: script

CustomScript ondersteunt de uitvoering van een door de gebruiker gedefinieerd script. De script instellingen om commandToExecute en fileUris te combi neren in één instelling. In plaats van dat u een bestand hoeft in te stellen om te downloaden vanuit Azure Storage of GitHub, kunt u het script gewoon als een instelling coderen. Script kan worden gebruikt om commandToExecute en fileUris te vervangen.

Het script **moet** base64-gecodeerd zijn.  Het script kan **eventueel** worden gzip'ed. De script instelling kan worden gebruikt in open bare of beveiligde instellingen. De maximale grootte van de gegevens van de script parameter is 256 KB. Als het script deze grootte overschrijdt, wordt het niet uitgevoerd.

Als u bijvoorbeeld het volgende script hebt opgeslagen in het bestand-script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

De juiste CustomScript-script instelling wordt samengesteld door de uitvoer van de volgende opdracht te nemen.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Het script kan eventueel worden gzip'ed om de grootte verder te verkleinen (in de meeste gevallen). (CustomScript detecteert automatisch het gebruik van gzip-compressie.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript maakt gebruik van de volgende algoritme om een script uit te voeren.

 1. bevestiging dat de lengte van de script waarde niet groter is dan 256 KB.
 1. Base64 de waarde van het script decoderen
 1. _poging_ om de door base64 gedecodeerde waarde te gunzip
 1. de gedecodeerde (en eventueel gedecomprimeerde) waarde naar de schijf schrijven (/var/lib/waagent/custom-script/#/script.sh)
 1. Voer het script uit met behulp van _/bin/sh-c/var/lib/waagent/custom-script/#/script.sh.


## <a name="template-deployment"></a>Sjabloonimplementatie
Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie wordt beschreven, kan worden gebruikt in een Azure Resource Manager sjabloon voor het uitvoeren van de aangepaste script extensie tijdens het implementeren van een Azure Resource Manager sjabloon. Een voorbeeld sjabloon met de aangepaste script extensie vindt u hier, [github](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Deze eigenschaps namen zijn hoofdletter gevoelig. Als u implementatie problemen wilt voor komen, gebruikt u de namen zoals hier wordt weer gegeven.

## <a name="azure-cli"></a>Azure-CLI
Wanneer u Azure CLI gebruikt om de aangepaste script extensie uit te voeren, moet u een configuratie bestand of-bestanden maken. U moet mini maal ' commandToExecute ' hebben.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Desgewenst kunt u de instellingen in de opdracht opgeven als een JSON-indelings teken reeks. Hierdoor kan de configuratie worden opgegeven tijdens de uitvoering en zonder een afzonderlijk configuratie bestand.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-voorbeelden

#### <a name="public-configuration-with-script-file"></a>Open bare configuratie met script bestand

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI-opdracht:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Open bare configuratie zonder script bestand

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI-opdracht:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Open bare en beveiligde configuratie bestanden

U gebruikt een openbaar configuratie bestand om de URI van het script bestand op te geven. U gebruikt een beveiligd configuratie bestand om de opdracht op te geven die moet worden uitgevoerd.

Openbaar configuratie bestand:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Beveiligd configuratie bestand:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI-opdracht:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Problemen oplossen
Wanneer de aangepaste script extensie wordt uitgevoerd, wordt het script gemaakt of gedownload in een directory die er ongeveer als volgt uitziet. De uitvoer van de opdracht wordt ook opgeslagen in deze `stdout` map `stderr` en in bestanden.

```bash
/var/lib/waagent/custom-script/download/0/
```

Controleer eerst het logboek van de Linux-agent om problemen op te lossen, Controleer of de uitbrei ding is uitgevoerd.

```bash
/var/log/waagent.log 
```

U moet de uitvoering van de extensie controleren. deze ziet er ongeveer als volgt uit:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Enkele punten om te noteren:
1. Wanneer de opdracht wordt uitgevoerd, wordt ingeschakeld.
2. Down load is gekoppeld aan het downloaden van het CustomScript-extensie pakket van Azure, niet de script bestanden die zijn opgegeven in fileUris.


De Azure script-extensie produceert een logboek, dat u hier kunt vinden:

```bash
/var/log/azure/custom-script/handler.log
```

U moet de afzonderlijke uitvoering controleren. deze ziet er ongeveer als volgt uit:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```
Hier ziet u het volgende:
* De opdracht inschakelen starten is dit logboek
* De instellingen die zijn door gegeven aan de extensie
* De extensie die het bestand downloadt en het resultaat hiervan.
* De opdracht die wordt uitgevoerd en het resultaat.

U kunt ook de uitvoerings status van de aangepaste script extensie ophalen met behulp van Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

De uitvoer ziet er als volgt uit:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Volgende stappen
Zie [Custom-Script-extension-Linux opslag plaats](https://github.com/Azure/custom-script-extension-linux)voor een overzicht van de code, de huidige problemen en versies.

