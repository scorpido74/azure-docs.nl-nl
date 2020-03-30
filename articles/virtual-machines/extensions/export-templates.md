---
title: Azure Resourcegroepen exporteren die VM-extensies bevatten
description: Resourcebeheersjablonen exporteren met virtuele machine-extensies.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253908"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Resourcegroepen exporteren die VM-extensies bevatten

Azure ResourceGroepen kunnen worden geëxporteerd naar een nieuwe resourcemanagersjabloon die vervolgens opnieuw kan worden geïmplementeerd. Het exportproces interpreteert bestaande resources en maakt een resourcebeheersjabloon die wanneer geïmplementeerd resulteert in een vergelijkbare resourcegroep. Wanneer u de exportoptie Resourcegroep gebruikt voor een resourcegroep die virtual machine-extensies bevat, moeten verschillende items worden overwogen, zoals compatibiliteit met extensies en beveiligde instellingen.

In dit document wordt beschreven hoe het exportproces van de Resourcegroep werkt met betrekking tot virtuele machine-extensies, waaronder een lijst met ondersteunde extensies en details over de verwerking van beveiligde gegevens.

## <a name="supported-virtual-machine-extensions"></a>Ondersteunde virtuele machine-extensies

Veel Virtual Machine-extensies zijn beschikbaar. Niet alle extensies kunnen worden geëxporteerd naar een Resource Manager-sjabloon met de functie 'Automatiseringsscript'. Als een extensie van een virtuele machine niet wordt ondersteund, moet deze handmatig worden teruggezet in de geëxporteerde sjabloon.

De volgende extensies kunnen worden geëxporteerd met de functie automatiseringsscript.

| Toestelnummer ||||
|---|---|---|---|
| Acronis Back-up | Datadog Windows Agent | OS patchen voor Linux | VM Snapshot Linux
| Acronis Back-up Linux | Docker-extensie | Poppenagent |
| Bg-informatie | DSC-extensie | Site 24x7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Site 24x7 Linux Server |
| BMC CTM-agent Windows | Dynatrace Windows | Site 24x7 Windows Server |
| Chef-kok Klant | HPE Security Application Defender | Trend Micro DSA |
| Aangepast script | IaaS Antimalware | Trend Micro DSA Linux |
| Aangepaste scriptextensie | IaaS Diagnostiek | VM-toegang voor Linux |
| Aangepast script voor Linux | Linux Chef-klant | VM-toegang voor Linux |
| Datadog Linux Agent | Linux-diagnose | VM-momentopname |

## <a name="export-the-resource-group"></a>De resourcegroep exporteren

Voer de volgende stappen uit om een resourcegroep naar een herbruikbare sjabloon te exporteren:

1. Aanmelden bij Azure Portal
2. Klik in het menu Hub op Brongroepen
3. De doelgroepgroep selecteren in de lijst
4. Klik in het blade resourcegroep op Automatiseringsscript

![Sjabloon exporteren](./media/export-templates/template-export.png)

Het automatiseringsscript azure resource manager produceert een Resource Manager-sjabloon, een parametersbestand en verschillende voorbeeldimplementatiescripts zoals PowerShell en Azure CLI. Op dit moment kan de geëxporteerde sjabloon worden gedownload met de downloadknop, als nieuwe sjabloon aan de sjabloonbibliotheek worden toegevoegd of opnieuw worden geïmplementeerd met de deploy-knop.

## <a name="configure-protected-settings"></a>Beveiligde instellingen configureren

Veel Azure-extensies voor virtuele machines bevatten een configuratie met beveiligde instellingen, die gevoelige gegevens versleutelt, zoals referenties en configuratietekenreeksen. Beveiligde instellingen worden niet geëxporteerd met het automatiseringsscript. Indien nodig moeten beveiligde instellingen opnieuw worden ingevoegd in de geëxporteerde sjabloon.

### <a name="step-1---remove-template-parameter"></a>Stap 1 - Sjabloonparameter verwijderen

Wanneer de resourcegroep wordt geëxporteerd, wordt één sjabloonparameter gemaakt om een waarde te geven aan de geëxporteerde beveiligde instellingen. Deze parameter kan worden verwijderd. Als u de parameter wilt verwijderen, kijkt u door de parameterlijst en verwijdert u de parameter die lijkt op dit JSON-voorbeeld.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Stap 2 - Eigenschappen van beveiligde instellingen

Omdat elke beveiligde instelling een set vereiste eigenschappen heeft, moet een lijst met deze eigenschappen worden verzameld. Elke parameter van de configuratie van beveiligde instellingen is te vinden in het [Azure Resource Manager-schema op GitHub.](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json) Dit schema bevat alleen de parametersets voor de extensies die worden vermeld in het overzichtsgedeelte van dit document. 

Zoek vanuit de schemarepository naar de gewenste `IaaSDiagnostics`extensie, bijvoorbeeld . Zodra het `protectedSettings` object extensies is gelokaliseerd, neemt u nota van elke parameter. In het voorbeeld `IaasDiagnostic` van de extensie `storageAccountName` `storageAccountKey`zijn `storageAccountEndPoint`de eisenparameters , en .

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Stap 3 - De beveiligde configuratie opnieuw maken

Zoek `protectedSettings` en vervang het geëxporteerde beveiligde instellingsobject op de geëxporteerde sjabloon door een nieuw object met de vereiste extensieparameters en een waarde voor elk object.

In het voorbeeld `IaasDiagnostic` van de extensie ziet de nieuwe beveiligde instellingsconfiguratie er als volgt uit:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

De uiteindelijke extensiebron lijkt op het volgende JSON-voorbeeld:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Als u sjabloonparameters gebruikt om eigenschapswaarden op te geven, moeten deze worden gemaakt. Wanneer u sjabloonparameters maakt voor beveiligde instellingswaarden, moet u het `SecureString` parametertype gebruiken zodat gevoelige waarden worden beveiligd. Zie Sjablonen voor Azure Resource Manager ontwerpen voor meer informatie over het gebruik [van parameters.](../../resource-group-authoring-templates.md)

In het voorbeeld `IaasDiagnostic` van de extensie worden de volgende parameters gemaakt in de sectie Parameters van de sjabloon Resourcemanager.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

Op dit moment kan de sjabloon worden geïmplementeerd met behulp van elke sjabloonimplementatiemethode.
