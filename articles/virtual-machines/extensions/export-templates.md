---
title: Azure-resource groepen exporteren die VM-extensies bevatten
description: Exporteer Resource Manager-sjablonen die de extensies van de virtuele machine bevatten.
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
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84703992"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Resource groepen exporteren die VM-extensies bevatten

Azure-resource groepen kunnen worden geëxporteerd naar een nieuwe resource manager-sjabloon die vervolgens opnieuw kan worden geïmplementeerd. Het export proces interpreteert bestaande resources en maakt een resource manager-sjabloon die bij het implementeren van resultaten in een soort gelijke resource groep. Wanneer u de optie voor het exporteren van de resource groep gebruikt voor een resource groep met extensies voor virtuele machines, moeten er verschillende items worden overwogen, zoals uitbreidings compatibiliteit en beveiligde instellingen.

In dit document wordt beschreven hoe het export proces van de resource groep werkt met extensies voor virtuele machines, met inbegrip van een lijst met ondersteunde uitbrei dingen en Details over het afhandelen van beveiligde gegevens.

## <a name="supported-virtual-machine-extensions"></a>Ondersteunde extensies voor virtuele machines

Er zijn veel uitbrei dingen voor virtuele machines beschikbaar. Niet alle uitbrei dingen kunnen worden geëxporteerd naar een resource manager-sjabloon met behulp van de functie voor automatiserings scripts. Als de extensie van een virtuele machine niet wordt ondersteund, moet deze hand matig worden teruggezet naar de geëxporteerde sjabloon.

De volgende uitbrei dingen kunnen worden geëxporteerd met de functie Automation script.

| Toestelnummer ||||
|---|---|---|---|
| Acronis-back-up | Datadog Windows-agent | BESTURINGSSYSTEEM patches voor Linux | VM-moment opname Linux
| Acronis backup Linux | Docker-extensie | Puppet-agent |
| BG-gegevens | DSC-extensie | Site 24x7 apm Insight |
| BMC CMT-agent Linux | Dynatrace Linux | Site 24x7 Linux-server |
| BMC CMT-agent Windows | Dynatrace Windows | Site 24x7 Windows Server |
| Chef-client | HPE-beveiligings toepassing Defender | Trend Micro DSA |
| Aangepast script | Antimalware IaaS | Trend Micro DSA Linux |
| Aangepaste scriptextensie | IaaS diagnostische gegevens | VM-toegang voor Linux |
| Aangepast script voor Linux | Linux chef-client | VM-toegang voor Linux |
| Datadog Linux-agent | Linux-diagnose | VM-momentopname |

## <a name="export-the-resource-group"></a>De resource groep exporteren

Als u een resource groep wilt exporteren naar een herbruikbare sjabloon, voert u de volgende stappen uit:

1. Aanmelden bij Azure Portal
2. Klik in het menu hub op resource groepen
3. Selecteer de doel resource groep in de lijst
4. Klik in de Blade van de resource groep op Automation script

![Sjabloon exporteren](./media/export-templates/template-export.png)

Het Azure Resource Manager automatiserings script produceert een resource manager-sjabloon, een parameter bestand en verschillende voor beelden van implementatie scripts, zoals Power shell en Azure CLI. Op dit moment kunt u de geëxporteerde sjabloon downloaden met behulp van de knop downloaden, toegevoegd als een nieuwe sjabloon aan de sjabloon bibliotheek of opnieuw geïmplementeerd met de knop implementeren.

## <a name="configure-protected-settings"></a>Beveiligde instellingen configureren

Veel virtuele-machine uitbreidingen van Azure bevatten een configuratie voor beveiligde instellingen waarmee gevoelige gegevens, zoals referenties en configuratie teken reeksen, worden versleuteld. Beveiligde instellingen worden niet geëxporteerd met het Automation-script. Als dat nodig is, moeten beveiligde instellingen opnieuw worden ingevoegd in de geëxporteerde sjabloon.

### <a name="step-1---remove-template-parameter"></a>Stap 1-sjabloon parameter verwijderen

Wanneer de resource groep wordt geëxporteerd, wordt er één sjabloon parameter gemaakt om een waarde voor de geëxporteerde beveiligde instellingen op te geven. Deze para meter kan worden verwijderd. Als u de para meter wilt verwijderen, bekijkt u de parameter lijst en verwijdert u de para meter die lijkt op dit JSON-voor beeld.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Stap 2: eigenschappen van beveiligde instellingen ophalen

Omdat voor elke beveiligde instelling een set vereiste eigenschappen is opgegeven, moet er een lijst met deze eigenschappen worden verzameld. Elke para meter van de configuratie van de beveiligde instellingen vindt u in het [Azure Resource Manager schema op github](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Dit schema bevat alleen de parameter sets voor de uitbrei dingen die worden vermeld in de sectie Overzicht van dit document. 

Zoek in het schema opslagplaats de gewenste extensie, voor dit voor beeld `IaaSDiagnostics` . Zodra het uitbrei ding `protectedSettings` object is gevonden, noteert u de para meters. In het voor beeld van de `IaasDiagnostic` uitbrei ding zijn de para meters vereist `storageAccountName` , `storageAccountKey` en `storageAccountEndPoint` .

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

### <a name="step-3---re-create-the-protected-configuration"></a>Stap 3: de beveiligde configuratie opnieuw maken

Zoek in het geëxporteerde sjabloon naar `protectedSettings` het geëxporteerde object met beveiligde instellingen en vervang dit door een nieuwe. met daarin de vereiste extensie parameters en een waarde voor elk item.

In het voor beeld van de `IaasDiagnostic` uitbrei ding ziet de configuratie van de nieuwe beveiligde instelling eruit als in het volgende voor beeld:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

De uiteindelijke extensie resource ziet er ongeveer uit als in het volgende voor beeld van JSON:

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

Als u sjabloon parameters gebruikt om eigenschaps waarden op te geven, moeten deze worden gemaakt. Bij het maken van sjabloon parameters voor beveiligde instellings waarden, moet u ervoor zorgen `SecureString` dat gevoelige waarden worden beveiligd met behulp van het parameter type. Zie [Azure Resource Manager sjablonen ontwerpen](../../resource-group-authoring-templates.md)voor meer informatie over het gebruik van para meters.

In het voor beeld van de `IaasDiagnostic` uitbrei ding worden de volgende para meters gemaakt in de sectie para meters van de Resource Manager-sjabloon.

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

Op dit moment kan de sjabloon met elke sjabloon implementatie methode worden geïmplementeerd.
