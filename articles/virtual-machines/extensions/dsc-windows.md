---
title: Azure Desired State Configuration Extension Handler
description: Een PowerShell DSC-configuratie uploaden en toepassen op een Azure VM met DSC-extensie
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 592c731d1851ac36cf9b57864750df0603b6c3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253960"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC-extensie

## <a name="overview"></a>Overzicht

De PowerShell DSC-extensie voor Windows wordt gepubliceerd en ondersteund door Microsoft. De extensie uploadt en past een PowerShell DSC-configuratie toe op een Azure VM. De DSC Extension roept PowerShell DSC op om de ontvangen DSC-configuratie op de VM uit te voeren. In dit document worden de ondersteunde platforms, configuraties en implementatieopties voor de DSC-extensie voor virtuele machines voor Windows beschreven.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De DSC Extension ondersteunt de volgende OS

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Internetconnectiviteit

De DSC-extensie voor Windows vereist dat de beoogde virtuele machine in staat is om te communiceren met Azure en de locatie van het configuratiepakket (.zip-bestand) als het is opgeslagen op een locatie buiten Azure. 

## <a name="extension-schema"></a>Extensieschema

In het volgende JSON wordt het schema voor het instellingengedeelte van de DSC-extensie weergegeven in een Azure Resource Manager-sjabloon. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde / Voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| uitgever | Microsoft.Powershell.DSC | tekenreeks |
| type | DSC | tekenreeks |
| typeHandlerVersie | 2.77 | int |

### <a name="settings-property-values"></a>Eigenschappenwaarden instellingen

| Name | Gegevenstype | Beschrijving
| ---- | ---- | ---- |
| settings.wmfVersion | tekenreeks | Hiermee geeft u de versie op van het Windows Management Framework die op uw vm moet worden geïnstalleerd. Als u deze eigenschap op 'laatste' instelt, wordt de meest recente versie van WMF geïnstalleerd. De enige huidige mogelijke waarden voor deze eigenschap zijn '4.0', '5.0' en 'latest'. Deze mogelijke waarden zijn onderhevig aan updates. De standaardwaarde is 'laatste'. |
| settings.configuration.url | tekenreeks | Hiermee geeft u de URL-locatie op van waaruit u uw DSC-configuratiezip-bestand downloaden. Als de opgegeven URL een SAS-token voor toegang vereist, moet u de eigenschap protectedSettings.configurationUrlSasToken instellen op de waarde van uw SAS-token. Deze eigenschap is vereist als instellingen.configuration.script en/of settings.configuration.function zijn gedefinieerd.
| settings.configuration.script | tekenreeks | Hiermee geeft u de bestandsnaam op van het script dat de definitie van uw DSC-configuratie bevat. Dit script moet zich in de hoofdmap van het zip-bestand bevinden dat is gedownload van de URL die is opgegeven door de eigenschap configuration.url. Deze eigenschap is vereist als settings.configuration.url en/of settings.configuration.script zijn gedefinieerd.
| settings.configuration.function | tekenreeks | Hiermee geeft u de naam van uw DSC-configuratie op. De benoemde configuratie moet worden opgenomen in het script dat is gedefinieerd door configuration.script. Deze eigenschap is vereist als instellingen.configuration.url en/of settings.configuration.function zijn gedefinieerd.
| instellingen.configurationArguments | Verzameling | Hiermee definieert u alle parameters die u wilt doorgeven aan uw DSC-configuratie. Deze eigenschap wordt niet versleuteld.
| settings.configurationData.url | tekenreeks | Hiermee geeft u de URL op van waaruit u uw configuratiegegevensbestand (.pds1) wilt downloaden om te gebruiken als invoer voor uw DSC-configuratie. Als de opgegeven URL een SAS-token voor toegang vereist, moet u de eigenschap protectedSettings.configurationDataUrlSasToken instellen op de waarde van uw SAS-token.
| settings.privacy.dataIngeschakeld | tekenreeks | Hiermee schakelt u telemetrieverzameling in of uit. De enige mogelijke waarden voor deze eigenschap zijn 'Inschakelen', 'Uitschakelen', ", of $null. Als u deze eigenschap leeg of null laat staan, schakelt u telemetrie in
| settings.advancedOptions.forcePullAndApply | Booleaanse waarde | Deze instelling is ontworpen om de ervaring van het werken met de extensie om knooppunten te registreren bij Azure Automation DSC te verbeteren.  Als de `$true`waarde is, wacht de extensie op de eerste uitvoering van de configuratie die uit de service is getrokken voordat het succes/de fout wordt teruggezet.  Als de waarde is ingesteld op $false, wordt de status die door de extensie wordt geretourneerd, alleen bedoeld als of het knooppunt is geregistreerd bij azure-automatiseringsstatusconfiguratie en de knooppuntconfiguratie niet wordt uitgevoerd tijdens de registratie.
| settings.advancedOptions.downloadMappings | Verzameling | Definieert alternatieve locaties om afhankelijkheden zoals WMF en .NET te downloaden

### <a name="protected-settings-property-values"></a>Eigenschappenwaarden voor beveiligde instellingen

| Name | Gegevenstype | Beschrijving
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | tekenreeks | Hiermee definieert u alle parameters die u wilt doorgeven aan uw DSC-configuratie. Deze eigenschap wordt versleuteld. |
| protectedSettings.configurationUrlSasToken | tekenreeks | Hiermee geeft u het SAS-token op om toegang te krijgen tot de URL die is gedefinieerd door configuration.url. Deze eigenschap wordt versleuteld. |
| protectedSettings.configurationDataUrlSasToken | tekenreeks | Hiermee geeft u het SAS-token op om toegang te krijgen tot de URL die is gedefinieerd door configurationData.url. Deze eigenschap wordt versleuteld. |


## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager-sjablonen.
Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor een configuratie na implementatie vereist is.
Een voorbeeld sjabloon resourcebeheer met de DSC-extensie voor Windows is te vinden in de [Azure Quick Start Gallery.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91)

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure-portal en met behulp van de Azure CLI. Als u de implementatiestatus van extensies voor een bepaalde VM wilt bekijken, voert u de volgende opdracht uit met de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Extensiepakket wordt gedownload en geïmplementeerd op deze locatie op de Azure VM
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Extensie status bestand bevat de sub status en status succes / fout codes samen met de gedetailleerde fout en beschrijving voor elke extensie run.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Extensieuitvoerlogboeken worden aangemeld bij de volgende map:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Foutcodes en hun betekenissen

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 1000 | Algemene fout | Het bericht voor deze fout wordt weergegeven door de specifieke uitzondering in extensielogboeken |
| 52 | Fout bij installatie van extensie | Het bericht voor deze fout wordt geleverd door de specifieke uitzondering |
| 1002 | Wmf-installatiefout | Fout bij het installeren van WMF. |
| 1004 | Ongeldig zip-pakket | Ongeldige rits ; Fout bij het uitpakken van de rits |
| 1100 | Argumentfout | Geeft een probleem aan in de invoer die door de gebruiker wordt geleverd. Het bericht voor de fout wordt geleverd door de specifieke uitzondering|


### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op de [FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen over [Microsoft Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor informatie over het gebruik van Azure Support.
