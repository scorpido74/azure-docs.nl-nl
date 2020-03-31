---
title: Monitoring & diagnose toevoegen aan een virtuele Azure-machine
description: Gebruik een Azure Resource Manager-sjabloon om een nieuwe virtuele Windows-machine te maken met Azure-diagnostische extensie.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2490c3de60e0deac6a1a4ddc5abc95cb46e240b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073848"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Controle en diagnose gebruiken met een Windows VM- en Azure Resource Manager-sjablonen
De Azure Diagnostics Extension biedt de bewakings- en diagnostische mogelijkheden op een windows-gebaseerde Virtuele Machine op Azure. U deze mogelijkheden op de virtuele machine inschakelen door de extensie op te nemen als onderdeel van de sjabloon Azure Resource Manager. Zie [Azure Resource Manager-sjablonen ontwerpen met VM-extensies](../windows/template-description.md#extensions) voor meer informatie over het opnemen van een extensie als onderdeel van een sjabloon voor virtuele machines. In dit artikel wordt beschreven hoe u de Azure Diagnostics-extensie toevoegen aan een sjabloon voor virtuele windows-machines.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>De Azure Diagnostics-extensie toevoegen aan de VM-brondefinitie
Als u de diagnostische extensie op een Virtuele Windows-machine wilt inschakelen, moet u de extensie toevoegen als VM-bron in de sjabloon Resourcebeheer.

Voeg voor een eenvoudige Virtuele Machine op basis van Resource Manager de extensieconfiguratie toe aan de *array resources* voor de virtuele machine: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
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
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Een andere veelvoorkomende conventie is het toevoegen van de extensieconfiguratie aan het knooppunt rootresources van de sjabloon in plaats van deze te definiëren onder het resources-knooppunt van de virtuele machine. Met deze benadering moet u expliciet een hiërarchische relatie opgeven tussen de extensie en de virtuele machine met de *naam-* en *typewaarden.* Bijvoorbeeld: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

De extensie is altijd gekoppeld aan de virtuele machine, u deze direct definiëren onder het resourceknooppunt van de virtuele machine of deze op het basisniveau definiëren en de hiërarchische naamgevingsconventie gebruiken om deze te koppelen aan de virtuele machine.

Voor virtuele machineschaalsets wordt de configuratie van extensies opgegeven in de eigenschap *extensionProfile* van de *VirtualMachineProfile*.

De *eigenschap publisher* met de waarde van **Microsoft.Azure.Diagnostics** en de *eigenschap type* met de waarde van **IaaSDiagnostics** identificeren op unieke wijze de Azure Diagnostics-extensie.

De waarde van de *eigenschap naam* kan worden gebruikt om te verwijzen naar de extensie in de resourcegroep. Als u deze specifiek instelt op **Microsoft.Insights.VMDiagnosticsSettings,** kan deze eenvoudig worden geïdentificeerd door de Azure-portal, zodat de bewakingsdiagrammen correct worden weergegeven in de Azure-portal.

De *typeHandlerVersion* geeft de versie aan van de extensie die u wilt gebruiken. Het instellen van de secundaire versie van *autoUpgradeMinorVersion* **op true** zorgt ervoor dat u de nieuwste Secundaire versie van de beschikbare extensie krijgt. Het is sterk aanbevolen dat u altijd *autoUpgradeMinorVersion* instelt om altijd **waar** te zijn, zodat u altijd de nieuwste beschikbare diagnostische extensie gebruiken met alle nieuwe functies en bugfixes. 

Het *instellingen-element* bevat configuratieseigenschappen voor de extensie die kunnen worden ingesteld en teruggelezen van de extensie (soms aangeduid als openbare configuratie). De eigenschap *xmlcfg* bevat xml-configuratie voor de diagnostische logboeken, prestatiemeteritems enz. Zie [Configuratieschema voor diagnostische gegevens](https://msdn.microsoft.com/library/azure/dn782207.aspx) voor meer informatie over het xml-schema zelf. Een gangbare praktijk is om de werkelijke xml-configuratie op te slaan als een variabele in de Azure Resource Manager-sjabloon en vervolgens te koppelen en te coderen64 om de waarde voor *xmlcfg*in te stellen. Zie de sectie over [configuratievariabelen](#diagnostics-configuration-variables) voor diagnostiek om meer te weten te komen over het opslaan van de xml in variabelen. De eigenschap *storageAccount* geeft de naam op van het opslagaccount waarnaar diagnostische gegevens worden overgedragen. 

De eigenschappen in *protectedSettings* (ook wel privéconfiguratie genoemd) kunnen worden ingesteld, maar kunnen niet worden teruggelezen nadat ze zijn ingesteld. Het alleen-schrijven-karakter van *protectedSettings* maakt het handig voor het opslaan van geheimen, zoals de opslagaccountsleutel waar de diagnostische gegevens worden geschreven.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Het opslagaccount voor diagnostische gegevens opgeven als parameters
De diagnostische extensie json fragment hierboven gaat uit van twee parameters *bestaandediagnosticsStorageAccountName* en *bestaandediagnosticsStorageResourceGroup* om de diagnostische opslag account waar diagnostische gegevens worden opgeslagen op te geven. Als u het diagnoseopslagaccount als parameter opgeeft, u het diagnoseopslagaccount eenvoudig wijzigen in verschillende omgevingen, bijvoorbeeld voor het testen van een ander diagnostisch opslagaccount en een ander account voor uw productie-implementatie.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Het is de beste gewoonte om een diagnostisch opslagaccount op te geven in een andere resourcegroep dan de resourcegroep voor de virtuele machine. Een resourcegroep kan worden beschouwd als een implementatie-eenheid met een eigen levensduur, een virtuele machine kan worden geïmplementeerd en opnieuw worden geïmplementeerd als nieuwe configuraties updates worden gemaakt om het, maar u doorgaan met het opslaan van de diagnostische gegevens in dezelfde opslag account over die virtuele machine-implementaties. Als het opslagaccount in een andere resource is, kan het opslagaccount gegevens van verschillende implementaties van virtuele machines accepteren, zodat problemen in de verschillende versies eenvoudig kunnen worden opgelost.

> [!NOTE]
> Als u een sjabloon voor de virtuele machine van Windows maakt vanuit Visual Studio, wordt het standaardopslagaccount mogelijk ingesteld op hetzelfde opslagaccount waarop de virtuele machine VHD wordt geüpload. Dit is om de initiële installatie van de VM te vereenvoudigen. Houd de sjabloon opnieuw in om een ander opslagaccount te gebruiken dat als parameter kan worden doorgegeven. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Configuratievariabelen voor diagnostiek
Het vorige json-fragment voor diagnostische gegevens definieert een *accountid-variabele* om het verkrijgen van de opslagaccountsleutel voor de diagnostische opslag te vereenvoudigen:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

De eigenschap *xmlcfg* voor de diagnostische extensie wordt gedefinieerd met behulp van meerdere variabelen die samen zijn samengevoegd. De waarden van deze variabelen zijn in xml, zodat ze correct moeten worden ontsnapt bij het instellen van de json variabelen.

In het volgende voorbeeld worden de xml voor diagnostische configuratie beschreven die prestatiemeteritems op standaardsysteemniveau verzamelt, samen met enkele logboeken voor windows-gebeurtenislogboeken en logboeken van diagnostische infrastructuur. Het is ontsnapt en correct geformatteerd, zodat de configuratie direct kan worden geplakt in de variabelen sectie van uw sjabloon. Zie het [configuratieschema](https://msdn.microsoft.com/library/azure/dn782207.aspx) voor diagnostiek voor een menselijker leesbaar voorbeeld van de configuratie-xml.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Het xml-knooppunt met metrische definitie in de bovenstaande configuratie is een belangrijk configuratie-element, omdat het definieert hoe de prestatiemeteritems die eerder in het xml-knooppunt in het knooppunt *PerformanceCounter* zijn gedefinieerd, worden samengevoegd en opgeslagen. 

> [!IMPORTANT]
> Deze statistieken zijn de aansturen van de controlegrafieken en waarschuwingen in de Azure-portal.  Het knooppunt **Metrische gegevens** met de *resource-ID* en **metricAggregation** moet worden opgenomen in de diagnostische configuratie voor uw vm als u de VM-bewakingsgegevens in de Azure-portal wilt zien. 
> 
> 

In het volgende voorbeeld worden de xml voor metrische gegevensdefinities weergegeven: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Het *kenmerk resourceID* identificeert op unieke wijze de virtuele machine in uw abonnement. Zorg ervoor dat u de functies subscription() en resourceGroup() gebruikt, zodat de sjabloon deze waarden automatisch bijwerkt op basis van het abonnement en de resourcegroep waarnaar u implementeert.

Als u meerdere virtuele machines in een lus maakt, moet u de *resource-ID-waarde* vullen met een functie copyIndex() om elke afzonderlijke VM correct te onderscheiden. De *xmlCfg-waarde* kan als volgt worden bijgewerkt om dit als volgt te ondersteunen:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

De metricaggregation-waarde van *PT1M* en *PT1H* betekent een aggregatie over respectievelijk een minuut en een aggregatie over een uur.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics-tabellen in opslag
De bovenstaande configuratie met statistieken genereert tabellen in uw diagnostische opslagaccount met de volgende naamgevingsconventies:

* **WADMetrics**: Standaard voorvoegsel voor alle WADMetrics-tabellen
* **PT1H** of **PT1M:** betekent dat de tabel geaggregeerde gegevens over 1 uur of 1 minuut bevat
* **P10D**: Betekent dat de tabel gegevens bevat gedurende 10 dagen vanaf het moment dat de tabel gegevens begon te verzamelen
* **V2S**: Snaarconstante
* **yyyymmdd**: De datum waarop de tabel begon met het verzamelen van gegevens

Voorbeeld: *WADMetricsPT1HP10DV2S20151108* bevat metrische gegevens die gedurende 10 dagen gedurende 10 dagen zijn samengevoegd vanaf 11 november-2015    

Elke tabel WADMetrics bevat de volgende kolommen:

* **PartitionKey:** De partitiesleutel is opgebouwd op basis van de *resourceID-waarde* om de VM-bron op unieke wijze te identificeren. Bijvoorbeeld: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey:** volgt `<Descending time tick>:<Performance Counter Name>`de notatie. De berekening van de dalende tijdteek is max tijdteken minus de tijd van het begin van de aggregatieperiode. Als de voorbeeldperiode bijvoorbeeld is gestart op 10-nov-2015 en 00:00 Hrs UTC, dan is de berekening: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Voor de prestatiesvan de beschikbare prestaties van de beschikbare bytes ziet de rijtoets eruit als:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName:** Is de naam van het prestatiemeterrecht. Dit komt overeen met de *tellerSpecifier* gedefinieerd in de xml config.
* **Maximum**: De maximale waarde van het prestatiemeterrecht gedurende de aggregatieperiode.
* **Minimum**: De minimumwaarde van het prestatiemeterrecht gedurende de aggregatieperiode.
* **Totaal**: De som van alle waarden van het prestatiemeteritem gerapporteerd over de aggregatieperiode.
* **Aantal:** het totale aantal gerapporteerde waarden voor het prestatiemeteritem.
* **Gemiddelde**: De gemiddelde (totaal/telling) waarde van het prestatieteller over de aggregatieperiode.

## <a name="next-steps"></a>Volgende stappen
* Zie [201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension) voor een volledige voorbeeldsjabloon van een virtuele Windows-machine met diagnose-extensie   
* De sjabloon Azure Resource Manager implementeren met [Azure PowerShell](../windows/ps-template.md) of [Azure Command Line](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Meer informatie over [het maken van Azure Resource Manager-sjablonen](../../resource-group-authoring-templates.md)
