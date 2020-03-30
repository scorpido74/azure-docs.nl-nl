---
title: Aangepaste scripts uitvoeren op Linux VM's in Azure
description: Linux VM-configuratietaken automatiseren met de Custom Script Extension v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: a3eae08510e57227b91deeeb7a7a608a6652cb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535405"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>De Azure Custom Script Extension Version 1 gebruiken met Virtuele Linux-machines

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

De Custom Script Extension Version 1 downloadt en voert scripts uit op virtuele Azure-machines. Deze extensie is handig voor configuratie na implementatie, software-installatie of een andere configuratie/beheertaak. U scripts downloaden van Azure Storage of een andere toegankelijke internetlocatie, of u ze aan de runtime van de extensie verstrekken.

De aangepaste scriptextensie integreert met Azure Resource Manager-sjablonen. U het ook uitvoeren met Azure CLI, PowerShell, de Azure-portal of de Azure Virtual Machines REST API.

In dit artikel wordt beschreven hoe u de aangepaste scriptextensie van Azure CLI gebruikt en hoe u de extensie uitvoert met behulp van een Azure Resource Manager-sjabloon. In dit artikel worden ook stappen voor het oplossen van problemen voor Linux-systemen vermeld.

Er zijn twee Linux Custom Script-extensies:

* Versie 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* Versie 2 - Microsoft.Azure.Extensions.CustomScript

Schakel in plaats daarvan over op nieuwe en bestaande implementaties om de nieuwe versie[(Microsoft.Azure.Extensions.CustomScript)](custom-script-linux.md)te gebruiken. De nieuwe versie is bedoeld als kant-en-klare vervanging. Voor de migratie hoeft u slechts de naam en versie te wijzigen. De configuratie van de extensie hoeft niet te worden gewijzigd.

### <a name="operating-system"></a>Besturingssysteem

Ondersteunde Linux-distributies:

* CentOS 6.5 en hoger
* Debian 8 en hoger
  * Debian 8.7 wordt niet verzonden met Python2 in de nieuwste afbeeldingen, die CustomScriptForLinux breekt.
* FreeBSD
* OpenSUSE 13.1 en hoger
* Oracle Linux 6.4 en hoger
* SUSE Linux Enterprise Server 11 SP3 en hoger
* Ubuntu 12.04 en hoger

### <a name="script-location"></a>Scriptlocatie

U de extensie gebruiken om uw Azure Blob-opslagreferenties te gebruiken om toegang te krijgen tot Azure Blob-opslag. Als alternatief kan de scriptlocatie overal zijn, zolang de VM naar dat eindpunt kan worden doorgestuurd, zoals GitHub, interne bestandsserver enz.

### <a name="internet-connectivity"></a>Internetverbinding

Als u een script extern moet downloaden, zoals GitHub of Azure Storage, moeten er extra firewall/Network Security Group-poorten worden geopend. Als uw script zich bijvoorbeeld in Azure Storage bevindt, u toegang toestaan via Azure NSG-servicetags voor [opslag.](../../virtual-network/security-overview.md#service-tags)

Als uw script zich op een lokale server bevindt, moet u mogelijk nog steeds extra firewall/Network Security Group-poorten openen.

### <a name="tips-and-tricks"></a>Tips en trucs

* De meeste fouten voor deze extensie worden veroorzaakt door syntaxisfouten in het script. Test de scriptuitvoeringen zonder fouten en schakel aanvullende logboekregistratie in het script in om gemakkelijker te achterhalen waar het misging.
* Schrijf scripts die idempotent zijn, zodat het systeem niet wordt gewijzigd als de scripts per ongeluk opnieuw meer dan één keer worden uitgevoerd.
* Zorg ervoor dat de scripts geen gebruikersinvoer vereisen wanneer ze worden uitgevoerd.
* Er is 90 minuten toegestaan voor het script uit te voeren, iets langer zal resulteren in een mislukte bepaling van de uitbreiding.
* Plaats reboots niet in het script, dit zal problemen veroorzaken met andere extensies die worden geïnstalleerd en na het opnieuw opstarten gaat de extensie niet verder na de herstart. 
* Als u een script dat een reboot zal veroorzaken, installeer dan toepassingen en scripts uitvoeren, enz. U moet de reboot plannen met behulp van een Cron-taak of tools zoals DSC of Chef, Puppet-extensies gebruiken.
* De extensie wordt slechts één keer uitgevoerd, als u een script op elke boot wilt uitvoeren, u [cloud-init-afbeelding](../linux/using-cloud-init.md) gebruiken en een [module Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) gebruiken. U het script ook gebruiken om een Systemd-service-eenheid te maken.
* Als u wilt plannen wanneer een script wordt uitgevoerd, moet u de extensie gebruiken om een Cron-taak te maken.
* Wanneer het script wordt uitgevoerd, ziet u alleen de extensiestatus 'overgang maken' van de Azure-portal of CLI. Als u vaker statusupdates van een uitgevoerd script wilt, moet u uw eigen oplossing maken.
* Aangepaste Script-extensie ondersteunt geen standaardservers, maar u een hulpmiddel voor bestandsoverdracht gebruiken dat proxyservers in uw script ondersteunt, zoals *Curl.*
* Wees je bewust van niet-standaard directory locaties waar uw scripts of opdrachten op kunnen vertrouwen, hebben logica om dit te verwerken.

## <a name="extension-schema"></a>Extensieschema

De configuratie van de aangepaste scriptextensie geeft zaken aan zoals de locatie van het script en de opdracht die moet worden uitgevoerd. U deze configuratie opslaan in configuratiebestanden, deze opgeven op de opdrachtregel of opgeven in een Azure Resource Manager-sjabloon. 

U gevoelige gegevens opslaan in een beveiligde configuratie, die is versleuteld en alleen wordt gedecodeerd in de virtuele machine. De beveiligde configuratie is handig wanneer de uitvoeringsopdracht geheimen zoals een wachtwoord bevat.

Deze items moeten worden behandeld als gevoelige gegevens en worden opgegeven in de configuratie van de beveiligde instelling voor extensies. Azure VM-extensiebeveiligde instellingsgegevens worden versleuteld en alleen gedecodeerd op de virtuele doelmachine.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| uitgever | Microsoft.OSTCExtensions | tekenreeks |
| type | CustomScriptForLinux (CustomScriptForLinux) | tekenreeks |
| typeHandlerVersie | 1.5 | int |
| fileUris (bijv. | https://github.com/MyProject/Archive/MyPythonScript.py | matrix |
| commandToExecute (bijv. | python \<MyPythonScript.py mijn-param1\> | tekenreeks |
| inschakelenInternalDNSCheck | waar | booleaans |
| storageAccountName (bijv. | voorbeeldstorageacct | tekenreeks |
| storageAccountKey (bijv. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | tekenreeks |

### <a name="property-value-details"></a>Details van de waarde van onroerend goed

* `fileUris`: (optioneel, tekenreeksarray) de urilijst van de scripts
* `enableInternalDNSCheck`: (optioneel, bool) standaard is True, ingesteld op False om DNS-controle uit te schakelen.
* `commandToExecute`: (optioneel, tekenreeks) het entrypoint-script dat moet worden uitgevoerd
* `storageAccountName`: (optioneel, tekenreeks) de naam van het opslagaccount
* `storageAccountKey`: (optioneel, tekenreeks) de toegangssleutel van het opslagaccount

De volgende waarden kunnen worden ingesteld in openbare of beveiligde instellingen, u mag deze waarden niet hieronder hebben ingesteld in zowel openbare als beveiligde instellingen.

* `commandToExecute`

Het gebruik van openbare instellingen is misschien handig voor het debuggen, maar het wordt ten zeerste aanbevolen om beveiligde instellingen te gebruiken.

Openbare instellingen worden in duidelijke tekst naar de VM gestuurd waar het script wordt uitgevoerd.  Beveiligde instellingen worden versleuteld met een sleutel die alleen bekend is bij azure en de VM. De instellingen worden opgeslagen in de VM als ze werden verzonden, dat wil zeggen als de instellingen zijn versleuteld worden ze versleuteld opgeslagen op de VM. Het certificaat dat wordt gebruikt om de versleutelde waarden te decoderen, wordt opgeslagen op de VM en wordt gebruikt om instellingen (indien nodig) te decoderen tijdens runtime.

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen. Het JSON-schema dat in de vorige sectie is beschreven, kan worden gebruikt in een Azure Resource Manager-sjabloon om de aangepaste scriptextensie uit te voeren tijdens een azure resource manager-sjabloonimplementatie.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Deze eigenschapsnamen zijn geval-gevoelig. Gebruik de namen zoals hier weergegeven om implementatieproblemen te voorkomen.

## <a name="azure-cli"></a>Azure-CLI

Wanneer u Azure CLI gebruikt om de aangepaste scriptextensie uit te voeren, maakt u een configuratiebestand of -bestanden. Je moet minimaal 'commandToExecute' hebben.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Optioneel u de instellingen in de opdracht opgeven als een tekenreeks met JSON-indeling. Hierdoor kan de configuratie worden opgegeven tijdens de uitvoering en zonder een apart configuratiebestand.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-voorbeelden

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
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
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
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer de aangepaste scriptextensie wordt uitgevoerd, wordt het script gemaakt of gedownload naar een map die vergelijkbaar is met het volgende voorbeeld. De opdrachtuitvoer wordt ook opgeslagen `stdout` `stderr` in deze map en bestanden.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Als u problemen wilt oplossen, controleert u eerst het Linux Agent Log, controleert u of de extensie is uitgevoerd, controleert u:

```bash
/var/log/waagent.log
```

Je moet kijken voor de uitbreiding uitvoering, zal het er ongeveer uitzien als:

```output
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Enkele punten om op te merken:

1. Inschakelen is wanneer de opdracht wordt uitgevoerd.
1. Download heeft betrekking op het downloaden van het CustomScript-uitbreidingspakket van Azure, niet op de scriptbestanden die zijn opgegeven in fileUris.
1. U ook zien welk logbestand het uitschrijft`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Volgende stap is om te gaan een check het logbestand, dit is de indeling:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Je moet op zoek naar de individuele uitvoering, zal het er ongeveer als:

```output
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
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
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Volgende stappen

Zie [Repo customscript-extensie repo](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)voor de code, huidige problemen en versies.
