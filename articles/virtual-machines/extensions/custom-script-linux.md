---
title: Aangepaste scripts uitvoeren op Linux VM's in Azure
description: Linux VM-configuratietaken automatiseren met de Custom Script Extension v2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: b75b232c048a1ea49256b12ce1b65c4bd87a1cf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535439"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>De aangepaste scriptextensie van Azure versie 2 gebruiken met virtuele Linux-machines
De Custom Script Extension Version 2 downloadt en voert scripts uit op virtuele Azure-machines. Deze extensie is handig voor configuratie na implementatie, software-installatie of een andere configuratie/beheertaak. U scripts downloaden van Azure Storage of een andere toegankelijke internetlocatie, of u ze aan de runtime van de extensie verstrekken. 

De aangepaste scriptextensie integreert met Azure Resource Manager-sjablonen. U het ook uitvoeren met Azure CLI, PowerShell of de Azure Virtual Machines REST API.

In dit artikel wordt beschreven hoe u de aangepaste scriptextensie van Azure CLI gebruikt en hoe u de extensie uitvoert met behulp van een Azure Resource Manager-sjabloon. In dit artikel worden ook stappen voor het oplossen van problemen voor Linux-systemen vermeld.


Er zijn twee Linux Custom Script-extensies:
* Versie 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Versie 2 - Microsoft.Azure.Extensions.CustomScript

Schakel in plaats daarvan over op nieuwe en bestaande implementaties om de nieuwe versie 2 te gebruiken. De nieuwe versie is bedoeld als kant-en-klare vervanging. Voor de migratie hoeft u slechts de naam en versie te wijzigen. De configuratie van de extensie hoeft niet te worden gewijzigd.


### <a name="operating-system"></a>Besturingssysteem

De Custom Script Extensie voor Linux zal draaien op de extensie ondersteunde extensie OS's, voor meer informatie, zie dit [artikel](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Scriptlocatie

U de extensie gebruiken om uw Azure Blob-opslagreferenties te gebruiken om toegang te krijgen tot Azure Blob-opslag. Als alternatief kan de scriptlocatie overal zijn, zolang de VM naar dat eindpunt kan worden doorgestuurd, zoals GitHub, interne bestandsserver enz.

### <a name="internet-connectivity"></a>Internetverbinding
Als u een script extern moet downloaden, zoals GitHub of Azure Storage, moeten er extra firewall/Network Security Group-poorten worden geopend. Als uw script zich bijvoorbeeld in Azure Storage bevindt, u toegang toestaan via Azure NSG-servicetags voor [opslag.](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)

Als uw script zich op een lokale server bevindt, moet u mogelijk nog steeds extra firewall/Network Security Group-poorten openen.

### <a name="tips-and-tricks"></a>Tips en trucs
* De meeste fouten voor deze extensie worden veroorzaakt door syntaxisfouten in het script. Test de scriptuitvoeringen zonder fouten en schakel aanvullende logboekregistratie in het script in om gemakkelijker te achterhalen waar het misging.
* Schrijf scripts die idempotent zijn, zodat het systeem niet wordt gewijzigd als de scripts per ongeluk opnieuw meer dan één keer worden uitgevoerd.
* Zorg ervoor dat de scripts geen gebruikersinvoer vereisen wanneer ze worden uitgevoerd.
* Er is 90 minuten toegestaan voor het script uit te voeren, iets langer zal resulteren in een mislukte bepaling van de uitbreiding.
* Plaats reboots niet in het script, dit zal problemen veroorzaken met andere extensies die worden geïnstalleerd en na het opnieuw opstarten gaat de extensie niet verder na de herstart. 
* Als u een script dat een reboot zal veroorzaken, installeer dan toepassingen en scripts uitvoeren, enz. U moet de reboot plannen met behulp van een Cron-taak of tools zoals DSC of Chef, Puppet-extensies gebruiken.
* De extensie wordt slechts één keer uitgevoerd, als u een script op elke boot wilt uitvoeren, u [cloud-init-afbeelding](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) gebruiken en een [module Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) gebruiken. U het script ook gebruiken om een SystemD-service-eenheid te maken.
* Als u wilt plannen wanneer een script wordt uitgevoerd, moet u de extensie gebruiken om een Cron-taak te maken. 
* Wanneer het script wordt uitgevoerd, ziet u alleen de extensiestatus 'overgang maken' van de Azure-portal of CLI. Als u vaker statusupdates van een uitgevoerd script wilt, moet u uw eigen oplossing maken.
* Aangepaste Script-extensie ondersteunt geen standaardservers, maar u een hulpmiddel voor bestandsoverdracht gebruiken dat proxyservers in uw script ondersteunt, zoals *Curl.* 
* Wees je bewust van niet-standaard directory locaties waar uw scripts of opdrachten op kunnen vertrouwen, hebben logica om dit te verwerken.
*  Bij het implementeren van aangepaste script voor de productie vmss-exemplaren wordt voorgesteld om via json-sjabloon te implementeren en uw scriptopslagaccount op te slaan waar u controle hebt over het SAS-token. 


## <a name="extension-schema"></a>Extensieschema

De configuratie van de aangepaste scriptextensie geeft zaken aan zoals de locatie van het script en de opdracht die moet worden uitgevoerd. U deze configuratie opslaan in configuratiebestanden, deze opgeven op de opdrachtregel of opgeven in een Azure Resource Manager-sjabloon. 

U gevoelige gegevens opslaan in een beveiligde configuratie, die is versleuteld en alleen wordt gedecodeerd in de virtuele machine. De beveiligde configuratie is handig wanneer de uitvoeringsopdracht geheimen zoals een wachtwoord bevat.

Deze items moeten worden behandeld als gevoelige gegevens en worden opgegeven in de configuratie van de beveiligde instelling voor extensies. Azure VM-extensiebeveiligde instellingsgegevens worden versleuteld en alleen gedecodeerd op de virtuele doelmachine.

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
    "typeHandlerVersion": "2.1",
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
       "fileUris": ["https://.."],
        "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> managedIdentity-eigenschap **mag niet** worden gebruikt in combinatie met storageAccountName- of storageAccountKey-eigenschappen

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld | Gegevenstype | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| uitgever | Microsoft.Compute.Extensions | tekenreeks |
| type | CustomScript (CustomScript) | tekenreeks |
| typeHandlerVersie | 2.1 | int |
| fileUris (bijv. | https://github.com/MyProject/Archive/MyPythonScript.py | matrix |
| commandToExecute (bijv. | python \<MyPythonScript.py mijn-param1> | tekenreeks |
| uit | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphCQGdXBncmFkZSAteQo= | tekenreeks |
| skipDos2Unix (bijv. | false | booleaans |
| tijdstempel (bijv. | 123456789 | 32-bits geheel getal |
| storageAccountName (bijv. | voorbeeldstorageacct | tekenreeks |
| storageAccountKey (bijv. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | tekenreeks |
| managedIdentity (bijv. | { } of { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } of { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json-object |

### <a name="property-value-details"></a>Details van de waarde van onroerend goed
* `apiVersion`: De meest up-to-date apiVersie is te vinden met Behulp van [Resource Explorer](https://resources.azure.com/) of vanuit Azure CLI met behulp van de volgende opdracht`az provider list -o json`
* `skipDos2Unix`: (optioneel, booleaans) sla dos2unix-conversie van script-gebaseerde bestands-URL's of -script over.
* `timestamp`(optioneel, 32-bits geheel getal) gebruikt dit veld alleen om een re-run van het script te activeren door de waarde van dit veld te wijzigen.  Elke gehele waarde is aanvaardbaar; het mag alleen anders zijn dan de vorige waarde.
* `commandToExecute`: (**vereist** als script niet ingesteld, string) het entry point script uit te voeren. Gebruik dit veld in plaats daarvan als uw opdracht geheimen bevat, zoals wachtwoorden.
* `script`: (**vereist** als commandToExecute niet is ingesteld, tekenreeks)een base64 gecodeerd (en optioneel gzip'ed) script uitgevoerd door /bin/sh.
* `fileUris`: (optioneel, tekenreeksarray) de URL's voor bestanden die moeten worden gedownload.
* `storageAccountName`: (optioneel, tekenreeks) de naam van het opslagaccount. Als u opslagreferenties opgeeft, moeten alle `fileUris` URL's voor Azure Blobs zijn.
* `storageAccountKey`: (optioneel, tekenreeks) de toegangssleutel van het opslagaccount
* `managedIdentity`: (optioneel, json-object) de [beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor het downloaden van bestand(en)
  * `clientId`: (optioneel, tekenreeks) de client-id van de beheerde identiteit
  * `objectId`: (optioneel, tekenreeks) de object-id van de beheerde identiteit


De volgende waarden kunnen worden ingesteld in openbare of beveiligde instellingen, de extensie weigert elke configuratie waarin de onderstaande waarden zijn ingesteld in zowel openbare als beveiligde instellingen.
* `commandToExecute`
* `script`
* `fileUris`

Het gebruik van openbare instellingen is misschien handig voor het debuggen, maar het wordt ten zeerste aanbevolen om beveiligde instellingen te gebruiken.

Openbare instellingen worden in duidelijke tekst naar de VM gestuurd waar het script wordt uitgevoerd.  Beveiligde instellingen worden versleuteld met een sleutel die alleen bekend is bij azure en de VM. De instellingen worden opgeslagen in de VM als ze werden verzonden, dat wil zeggen als de instellingen zijn versleuteld worden ze versleuteld opgeslagen op de VM. Het certificaat dat wordt gebruikt om de versleutelde waarden te decoderen, wordt opgeslagen op de VM en wordt gebruikt om instellingen (indien nodig) te decoderen tijdens runtime.

#### <a name="property-skipdos2unix"></a>Eigenschap: skipDos2Unix

De standaardwaarde is false, wat betekent dat dos2unix-conversie **wordt** uitgevoerd.

De vorige versie van CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, zou dos-bestanden `\r\n` automatisch `\n`converteren naar UNIX-bestanden door te vertalen naar . Deze vertaling bestaat nog steeds en is standaard ingeschakeld. Deze conversie wordt toegepast op alle bestanden die zijn gedownload van fileUris of de scriptinstelling op basis van een van de volgende criteria.

* Als de extensie `.sh`een `.txt` `.py`van `.pl` , , of het zal worden omgezet. De scriptinstelling voldoet altijd aan deze criteria omdat wordt aangenomen dat het een script is dat is uitgevoerd met /bin/sh, en wordt opgeslagen als script.sh op de VM.
* Als het bestand `#!`begint met .

De dos2unix-conversie kan worden overgeslagen door de skipDos2Unix op true in te stellen.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Eigenschap: script

CustomScript ondersteunt de uitvoering van een door de gebruiker gedefinieerd script. De scriptinstellingen om commandToExecute en fileUris te combineren in één instelling. In plaats van een bestand in te stellen om te downloaden van Azure-opslag of GitHub-gist, u het script eenvoudig coderen als instelling. Script kan worden gebruikt om commandToExecute en fileUris te vervangen.

Het script **moet** base64 gecodeerd zijn.  Het script kan **optioneel** worden gzip'ed. De scriptinstelling kan worden gebruikt in openbare of beveiligde instellingen. De maximale grootte van de gegevens van de scriptparameter is 256 KB. Als het script deze grootte overschrijdt, wordt het niet uitgevoerd.

Bijvoorbeeld, gezien het volgende script opgeslagen in het bestand / script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

De juiste CustomScript-scriptinstelling wordt geconstrueerd door de uitvoer van de volgende opdracht te nemen.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Het script kan optioneel worden gzip'ed om de grootte verder te verkleinen (in de meeste gevallen). (CustomScript detecteert automatisch het gebruik van gzip-compressie.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript gebruikt het volgende algoritme om een script uit te voeren.

 1. de lengte van de waarde van het script niet meer dan 256 KB.
 1. base64 decoderen van de waarde van het script
 1. _poging_ om gunzip de base64 gedecodeerde waarde
 1. schrijf de gedecodeerde (en eventueel gedecomprimeerde) waarde naar schijf (/var/lib/waagent/custom-script/#/script.sh)
 1. voer het script uit met behulp van _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Eigenschap: managedIdentity

CustomScript (versie 2.1) ondersteunt [beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor het downloaden van bestanden(en) van URL's die in de instelling 'fileUris' worden gegeven. Hiermee heeft CustomScript toegang tot privéblobs of containers van Azure Storage zonder dat de gebruiker geheimen zoals SAS-tokens of opslagaccountsleutels hoeft door te geven.

Als u deze functie wilt gebruiken, moet de gebruiker een door het systeem toegewezen of [door de gebruiker toegewezen](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) identiteit toevoegen aan de VM of VMSS waar CustomScript naar verwachting wordt uitgevoerd, en de [beheerde identiteit toegang verlenen tot de Azure Storage-container of blob.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access) [system-assigned](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

Als u de door het systeem toegewezen identiteit wilt gebruiken op de doel-VM/VMSS, stelt u het veld 'beheerde identiteit' in op een leeg json-object. 

> Voorbeeld:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Als u de door de gebruiker toegewezen identiteit wilt gebruiken op de doel-VM/VMSS, configureert u het veld 'beheerde identiteit' met de client-id of de object-id van de beheerde identiteit.

> Voorbeelden:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity-eigenschap **mag niet** worden gebruikt in combinatie met storageAccountName- of storageAccountKey-eigenschappen

## <a name="template-deployment"></a>Sjabloonimplementatie
Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie is beschreven, kan worden gebruikt in een Azure Resource Manager-sjabloon om de aangepaste scriptextensie uit te voeren tijdens een azure resource manager-sjabloonimplementatie. Een voorbeeldsjabloon met de aangepaste scriptextensie vindt u hier, [GitHub.](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)


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
    "typeHandlerVersion": "2.1",
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
>Deze eigenschapsnamen zijn geval-gevoelig. Gebruik de namen zoals hier weergegeven om implementatieproblemen te voorkomen.

## <a name="azure-cli"></a>Azure-CLI
Wanneer u Azure CLI gebruikt om de aangepaste scriptextensie uit te voeren, maakt u een configuratiebestand of -bestanden. Je moet minimaal 'commandToExecute' hebben.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Optioneel u de instellingen in de opdracht opgeven als een tekenreeks met JSON-indeling. Hierdoor kan de configuratie worden opgegeven tijdens de uitvoering en zonder een apart configuratiebestand.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-voorbeelden

#### <a name="public-configuration-with-script-file"></a>Openbare configuratie met scriptbestand

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI, opdracht:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Openbare configuratie zonder scriptbestand

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI, opdracht:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Openbare en beveiligde configuratiebestanden

U gebruikt een openbaar configuratiebestand om het scriptbestand URI op te geven. U gebruikt een beveiligd configuratiebestand om de opdracht op te geven die moet worden uitgevoerd.

Openbaar configuratiebestand:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Beveiligd configuratiebestand:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI, opdracht:

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
Wanneer de aangepaste scriptextensie wordt uitgevoerd, wordt het script gemaakt of gedownload naar een map die vergelijkbaar is met het volgende voorbeeld. De opdrachtuitvoer wordt ook opgeslagen `stdout` `stderr` in deze map en bestanden.

```bash
/var/lib/waagent/custom-script/download/0/
```

Als u problemen wilt oplossen, controleert u eerst het Linux Agent Log, controleert u of de extensie is uitgevoerd, controleert u:

```bash
/var/log/waagent.log 
```

Je moet kijken voor de uitbreiding uitvoering, zal het er ongeveer uitzien als:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Enkele punten om op te merken:
1. Inschakelen is wanneer de opdracht wordt uitgevoerd.
2. Download heeft betrekking op het downloaden van het CustomScript-uitbreidingspakket van Azure, niet op de scriptbestanden die zijn opgegeven in fileUris.


De Azure Script-extensie produceert een logboek, dat u hier vinden:

```bash
/var/log/azure/custom-script/handler.log
```

Je moet op zoek naar de individuele uitvoering, zal het er ongeveer als:

```output
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

Hier u zien:
* De opdracht Inschakelen is dit logboek
* De instellingen zijn doorgegeven aan de extensie
* De extensie downloaden bestand en het resultaat van dat.
* De opdracht wordt uitgevoerd en het resultaat.

U ook de uitvoeringsstatus van de aangepaste scriptextensie ophalen met Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

De uitvoer ziet eruit als de volgende tekst:

```output
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Volgende stappen
Zie [custom-script-extension-linux repo](https://github.com/Azure/custom-script-extension-linux)voor de code, huidige problemen en versies.

