---
title: Virtuele machines in een Azure Resource Manager-sjabloon | Microsoft Azure
description: Meer informatie over hoe de virtuele machinebron wordt gedefinieerd in een Azure Resource Manager-sjabloon.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: c9bf1cf0564655c932e066e5b74225382375e9c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235425"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuele machines in een Azure Resource Manager-sjabloon

In dit artikel worden aspecten beschreven van een Azure Resource Manager-sjabloon die van toepassing zijn op virtuele machines. In dit artikel wordt geen volledige sjabloon beschreven voor het maken van een virtuele machine. daarvoor hebt u brondefinities nodig voor opslagaccounts, netwerkinterfaces, openbare IP-adressen en virtuele netwerken. Zie de [sjabloon walkthrough resourcebeheer voor](../../azure-resource-manager/resource-manager-template-walkthrough.md)meer informatie over hoe deze resources samen kunnen worden gedefinieerd.

Er zijn veel [sjablonen in de galerie](https://azure.microsoft.com/documentation/templates/?term=VM) die de VM-bron bevatten. Niet alle elementen die in een sjabloon kunnen worden opgenomen, worden hier beschreven.

 

In dit voorbeeld wordt een typische resourcesectie van een sjabloon weergegeven voor het maken van een opgegeven aantal VM's:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Dit voorbeeld is gebaseerd op een opslagaccount dat eerder is gemaakt. U het opslagaccount maken door het te implementeren vanuit de sjabloon. Het voorbeeld is ook gebaseerd op een netwerkinterface en de afhankelijke bronnen die in de sjabloon worden gedefinieerd. Deze bronnen worden niet weergegeven in het voorbeeld.
>
>

## <a name="api-version"></a>API-versie

Wanneer u resources implementeert met behulp van een sjabloon, moet u een versie van de API opgeven die u wilt gebruiken. In het voorbeeld wordt de bron van de virtuele machine weergegeven met behulp van dit apiVersion-element:

```json
"apiVersion": "2016-04-30-preview",
```

De versie van de API die u in uw sjabloon opgeeft, is van invloed op welke eigenschappen u in de sjabloon definiëren. In het algemeen moet u de meest recente API-versie selecteren bij het maken van sjablonen. Voor bestaande sjablonen u bepalen of u een eerdere API-versie wilt blijven gebruiken of uw sjabloon wilt bijwerken voor de nieuwste versie om te profiteren van nieuwe functies.

Gebruik deze mogelijkheden voor het verkrijgen van de nieuwste API-versies:

- REST API - [Alle resourceproviders weergeven](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell - [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI - [az provider show](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parameters en variabelen

[Met parameters](../../resource-group-authoring-templates.md) u eenvoudig waarden opgeven voor de sjabloon wanneer u deze uitvoert. Deze sectie parameters wordt gebruikt in het voorbeeld:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Wanneer u de voorbeeldsjabloon implementeert, voert u waarden in voor de naam en het wachtwoord van het beheerdersaccount op elke vm en het aantal VM's dat u wilt maken. U hebt de mogelijkheid om parameterwaarden op te geven in een afzonderlijk bestand dat met de sjabloon wordt beheerd of waarden op te geven wanneer daarom wordt gevraagd.

[Variabelen](../../resource-group-authoring-templates.md) maken het gemakkelijk voor u om waarden in de sjabloon in te stellen die herhaaldelijk in de sjabloon worden gebruikt of die in de loop van de tijd kunnen veranderen. Deze sectie variabelen wordt gebruikt in het voorbeeld:

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Wanneer u de voorbeeldsjabloon implementeert, worden variabele waarden gebruikt voor de naam en id van het eerder gemaakte opslagaccount. Variabelen worden ook gebruikt om de instellingen voor de diagnostische extensie te bieden. Gebruik de [aanbevolen procedures voor het maken van Azure Resource Manager-sjablonen](../../resource-manager-template-best-practices.md) om u te helpen bepalen hoe u de parameters en variabelen in uw sjabloon wilt structureren.

## <a name="resource-loops"></a>Resourcelussen

Wanneer u meer dan één virtuele machine voor uw toepassing nodig hebt, u een kopieerelement in een sjabloon gebruiken. Met dit optionele element wordt het aantal VM's gemaakt dat u als parameter hebt opgegeven:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Let ook op in het voorbeeld dat de lusindex wordt gebruikt bij het opgeven van een aantal waarden voor de resource. Als u bijvoorbeeld een aantal voorbeelden van drie hebt ingevoerd, zijn de namen van de schijven van het besturingssysteem myOSDisk1, myOSDisk2 en myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>In dit voorbeeld worden beheerde schijven voor de virtuele machines gebruikt.
>
>

Houd er rekening mee dat het maken van een lus voor één resource in de sjabloon mogelijk vereist dat u de lus gebruikt bij het maken of openen van andere bronnen. Meerdere VM's kunnen bijvoorbeeld niet dezelfde netwerkinterface gebruiken, dus als uw sjabloon door loopt met het maken van drie VM's, moet deze ook worden herhaald door het maken van drie netwerkinterfaces. Wanneer u een netwerkinterface aan een VM toewijst, wordt de lusindex gebruikt om deze te identificeren:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Afhankelijkheden

De meeste resources zijn afhankelijk van andere resources om correct te werken. Virtuele machines moeten worden gekoppeld aan een virtueel netwerk en om dat te doen, heeft het een netwerkinterface nodig. Het element [dependsOn](../../resource-group-define-dependencies.md) wordt gebruikt om ervoor te zorgen dat de netwerkinterface klaar is om te worden gebruikt voordat de VM's worden gemaakt:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resourcemanager implementeert tegelijkertijd alle resources die niet afhankelijk zijn van een andere resource die wordt geïmplementeerd. Wees voorzichtig bij het instellen van afhankelijkheden, omdat u per ongeluk uw implementatie vertragen door onnodige afhankelijkheden op te geven. Afhankelijkheden kunnen door meerdere resources worden geketend. De netwerkinterface is bijvoorbeeld afhankelijk van het openbare IP-adres en virtuele netwerkbronnen.

Hoe weet u of een afhankelijkheid vereist is? Bekijk de waarden die u in de sjabloon hebt ingesteld. Als een element in de definitie van virtuele machinebronnen wijst op een andere resource die in dezelfde sjabloon wordt geïmplementeerd, hebt u een afhankelijkheid nodig. Uw voorbeeld virtuele machine definieert bijvoorbeeld een netwerkprofiel:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Om deze eigenschap in te stellen, moet de netwerkinterface bestaan. Daarom hebt u een afhankelijkheid nodig. U moet ook een afhankelijkheid instellen wanneer een resource (een onderliggende bron) is gedefinieerd binnen een andere resource (een bovenliggende bron). De diagnostische instellingen en aangepaste scriptextensies worden bijvoorbeeld zowel gedefinieerd als onderliggende resources van de virtuele machine. Ze kunnen pas worden gemaakt als de virtuele machine bestaat. Daarom zijn beide resources gemarkeerd als afhankelijk van de virtuele machine.

## <a name="profiles"></a>Profielen

Verschillende profielelementen worden gebruikt bij het definiëren van een virtuele machinebron. Sommige zijn vereist en sommige zijn optioneel. De hardwareProfiel-, osProfile-, storageProfile- en networkProfile-elementen zijn bijvoorbeeld vereist, maar de diagnoseProfiel is optioneel. Deze profielen definiëren instellingen zoals:
   
- [Grootte](sizes.md)
- [naam](/azure/architecture/best-practices/resource-naming) en referenties
- schijf- en [besturingssysteeminstellingen](cli-ps-findimage.md)
- [netwerkinterface](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- opstartdiagnostiek

## <a name="disks-and-images"></a>Schijven en afbeeldingen
   
In Azure kunnen vhd-bestanden [schijven of afbeeldingen](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)weergeven. Wanneer het besturingssysteem in een vhd-bestand is gespecialiseerd om een specifieke VM te zijn, wordt het een schijf genoemd. Wanneer het besturingssysteem in een vhd-bestand wordt gegeneraliseerd om te worden gebruikt om veel VM's te maken, wordt het een afbeelding genoemd.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Nieuwe virtuele machines en nieuwe schijven maken op basis van een platformafbeelding

Wanneer u een vm maakt, moet u bepalen welk besturingssysteem u wilt gebruiken. Het element imageReference wordt gebruikt om het besturingssysteem van een nieuwe virtuele machine te definiëren. In het voorbeeld wordt een definitie voor een Windows Server-besturingssysteem weergegeven:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Als u een Linux-besturingssysteem wilt maken, u deze definitie gebruiken:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Configuratie-instellingen voor de schijf van het besturingssysteem worden toegewezen met het element osDisk. In het voorbeeld wordt een nieuwe beheerde schijf gedefinieerd met de cachemodus die is ingesteld op **ReadWrite** en dat de schijf wordt gemaakt op basis van een [platformafbeelding:](cli-ps-findimage.md)

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Nieuwe virtuele machines maken op bestaande beheerde schijven

Als u virtuele machines wilt maken van bestaande schijven, verwijdert u de imageReference en de elementen osProfile en definieert u deze schijfinstellingen:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Nieuwe virtuele machines maken op basis van een beheerde afbeelding

Als u een virtuele machine wilt maken op basis van een beheerde afbeelding, wijzigt u het element imageReference en definieert u de volgende schijfinstellingen:

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Gegevensschijven koppelen

U optioneel gegevensschijven toevoegen aan de VM's. Het [aantal schijven](sizes.md) is afhankelijk van de grootte van de schijf van het besturingssysteem die u gebruikt. Met de grootte van de VM's ingesteld op Standard_DS1_v2, het maximum aantal gegevensschijven die kunnen worden toegevoegd aan de hen is twee. In het voorbeeld wordt aan elke vm één beheerde gegevensschijf toegevoegd:

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Extensies

Hoewel [extensies](extensions-features.md) een afzonderlijke bron zijn, zijn ze nauw verbonden met VM's. Extensies kunnen worden toegevoegd als onderliggende bron van de VM of als een afzonderlijke bron. In het voorbeeld wordt de [diagnostische extensie](extensions-diagnostics-template.md) weergegeven die aan de VM's wordt toegevoegd:

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Deze extensiebron gebruikt de variabele storageName en de diagnostische variabelen om waarden op te geven. Als u de gegevens die door deze extensie worden verzameld, wilt wijzigen, u meer prestatiemeteritems toevoegen aan de variabele wadperfcounters. U er ook voor kiezen om de diagnostische gegevens in een ander opslagaccount te plaatsen dan waar de VM-schijven worden opgeslagen.

Er zijn veel extensies die u installeren op een VM, maar het meest nuttig is waarschijnlijk de [Custom Script Extensie](extensions-customscript.md). In het voorbeeld wordt op elke VM een PowerShell-script met de naam start.ps1 uitgevoerd:

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Het script start.ps1 kan veel configuratietaken uitvoeren. De gegevensschijven die in het voorbeeld aan de VM's worden toegevoegd, worden bijvoorbeeld niet geïnitialiseerd. u een aangepast script gebruiken om ze te initialiseren. Als u meerdere opstarttaken moet uitvoeren, u het start.ps1-bestand gebruiken om andere PowerShell-scripts in Azure-opslag aan te roepen. Het voorbeeld maakt gebruik van PowerShell, maar u elke scriptmethode gebruiken die beschikbaar is op het besturingssysteem dat u gebruikt.

U de status van de geïnstalleerde extensies zien via de instellingen voor extensies in de portal:

![Extensiestatus krijgen](./media/template-description/virtual-machines-show-extensions.png)

U ook extensiegegevens opvragen met de opdracht **Get-AzVMExtension** PowerShell, de **vm-extensie krijgt** Azure CLI of de REST API **voor extensiegegevens voor informatie opvragen.**

## <a name="deployments"></a>Implementaties

Wanneer u een sjabloon implementeert, houdt Azure de resources bij die u als groep hebt geïmplementeerd en wijst u automatisch een naam toe aan deze geïmplementeerde groep. De naam van de implementatie is dezelfde als de naam van de sjabloon.

Als u nieuwsgierig bent naar de status van resources in de implementatie, bekijkt u de brongroep in de Azure-portal:

![Implementatiegegevens verzamelen](./media/template-description/virtual-machines-deployment-info.png)
    
Het is geen probleem om dezelfde sjabloon te gebruiken om bronnen te maken of om bestaande bronnen bij te werken. Wanneer u opdrachten gebruikt om sjablonen te implementeren, u aangeven welke [modus](../../resource-group-template-deploy.md) u wilt gebruiken. De modus kan worden ingesteld op **Voltooien** of **Incrementeel**. De standaardinstelling is om incrementele updates uit te voeren. Wees voorzichtig bij het gebruik van de **modus Voltooien,** omdat u per ongeluk bronnen verwijderen. Wanneer u de modus instelt op **Voltooien,** verwijdert Resourcemanager alle bronnen in de resourcegroep die niet in de sjabloon staan.

## <a name="next-steps"></a>Volgende stappen

- Maak uw eigen sjabloon met [Azure Resource Manager-sjablonen voor ontwerpen.](../../resource-group-authoring-templates.md)
- Implementeer de sjabloon die u hebt gemaakt met [een virtuele Windows-machine maken met een resourcemanagersjabloon.](ps-template.md)
- Meer informatie over het beheren van de VM's die u hebt gemaakt door [Windows VM's maken en beheren met de Azure PowerShell-module](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Zie [Azure Resource Manager-sjabloonverwijzing](/azure/templates/)voor de syntaxis en eigenschappen van JSON in sjablonen.
