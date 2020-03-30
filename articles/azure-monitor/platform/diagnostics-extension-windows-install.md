---
title: Windows Azure-diagnostische extensie (WAD) installeren en configureren
description: Meer informatie over het verzamelen van Azure-diagnostische gegevens in een Azure Storage-account, zodat u deze bekijken met een van de verschillende beschikbare hulpprogramma's.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672256"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Windows Azure-diagnostische extensie (WAD) installeren en configureren
Azure-diagnostische extensie is een agent in Azure Monitor die bewakingsgegevens verzamelt van het gastbesturingssysteem en workloads van Azure virtuele machines en andere rekenbronnen. In dit artikel vindt u informatie over het installeren en configureren van de Windows-diagnostische extensie en een beschrijving van de manier waarop de gegevens zijn opgeslagen en het Azure Storage-account.

De diagnostische extensie wordt geïmplementeerd als een [extensie voor virtuele machines](../../virtual-machines/extensions/overview.md) in Azure, zodat deze dezelfde installatieopties ondersteunt met behulp van Resource Manager-sjablonen, PowerShell en CLI. Zie [Virtuele machine-extensies en -functies voor Windows](../../virtual-machines/extensions/features-windows.md) voor meer informatie over het installeren en onderhouden van virtuele machine-extensies.

## <a name="install-with-azure-portal"></a>Installeren met Azure-portal
U de diagnostische extensie installeren en configureren op een afzonderlijke virtuele machine in de Azure-portal, die u een interface biedt in plaats van rechtstreeks met de configuratie te werken. Wanneer u de diagnostische extensie inschakelt, wordt automatisch een standaardconfiguratie gebruikt met de meest voorkomende prestatiemeteritems en gebeurtenissen. U deze standaardconfiguratie aanpassen aan uw specifieke vereisten.

> [!NOTE]
> Er zijn instellingen voor diagnostische extensie die u niet configureren met behulp van de Azure-portal, inclusief het verzenden van gegevens naar Azure Event Hubs. U moet een van de andere configuratiemethoden gebruiken voor deze instellingen.

1. Open het menu voor een virtuele machine in de Azure-portal.
2. Klik op **Diagnostische instellingen** in de sectie **Controle** van het VM-menu.
3. Klik **op Controle op gastniveau inschakelen** als de diagnostische extensie nog niet is ingeschakeld.
4. Er wordt een nieuw Azure Storage-account gemaakt voor de VM met de naam, gebaseerd op de naam van de resourcegroep voor de VM. U de vm aan een ander opslagaccount koppelen door het tabblad **Agent te** selecteren.

![Diagnostische instellingen](media/diagnostics-extension-windows-install/diagnostic-settings.png)


U de standaardconfiguratie wijzigen zodra de diagnostische extensie is ingeschakeld. In de volgende tabel worden de opties beschreven die u in de verschillende tabbladen wijzigen. Sommige opties hebben een **opdracht Aangepast** waarmee u een meer gedetailleerde configuratie opgeven. zie [windows-extensieschema](diagnostics-extension-schema-windows.md) voor diagnostische gegevens voor meer informatie over verschillende instellingen.

| Tab | Beschrijving |
|:---|:---|
| Overzicht | Hiermee wordt de huidige configuratie met koppelingen naar de andere tabbladen weergegeven. |
| Prestatiemeteritems | Selecteer de prestatiemeteritems die u wilt verzamelen en de samplesnelheid voor elk.  |
| Logboeken | Selecteer de logboekgegevens die u wilt verzamelen. Dit omvat Windows Event logs, IIS logs, .NET applicatie logs en ETW gebeurtenissen.  |
| Crashdumps | Schakel crashdump in voor verschillende processen. |
| Putten | Schakel gegevenssinks in om gegevens naar bestemmingen te verzenden naast Azure Storage.<br>Azure Monitor - Stuurt prestatiegegevens naar Azure Monitor Metrics.<br>Application Insights - Gegevens verzenden naar een Application Insights-toepassing. |
| Agent | Wijzig de volgende configuratie voor de agent:<br>- Wijzig het opslagaccount.<br>- Geef de maximale lokale schijf op die voor de agent wordt gebruikt.<br>- Logboeken configureren voor de status van het agent zelf.|


> [!NOTE]
> Hoewel de configuratie voor diagnostische extensie kan worden opgemaakt in JSON of XML, wordt elke configuratie die in de Azure-portal wordt uitgevoerd altijd opgeslagen als JSON. Als u XML met een andere configuratiemethode gebruikt en vervolgens uw configuratie wijzigt met de Azure-portal, worden de instellingen gewijzigd in JSON.

## <a name="resource-manager-template"></a>Resource Manager-sjabloon
Zie [Controle en diagnose gebruiken met een Windows VM- en Azure Resource Manager-sjablonen](../../virtual-machines/extensions/diagnostics-template.md) voor het implementeren van de diagnostische extensie met Azure Resource Manager-sjablonen. 

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie
De Azure CLI kan worden gebruikt om de Azure Diagnostics-extensie te implementeren op een bestaande virtuele machine met behulp van [az vm-extensie ingesteld](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) als in het volgende voorbeeld. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

De beveiligde instellingen worden gedefinieerd in het [PrivateConfig-element](diagnostics-extension-schema-windows.md#privateconfig-element) van het configuratieschema. Hieronder volgt een minimaal voorbeeld van een bestand met beveiligde instellingen dat het opslagaccount definieert. Zie [Voorbeeldconfiguratie](diagnostics-extension-schema-windows.md#privateconfig-element) voor volledige details van de privé-instellingen.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
De openbare instellingen worden gedefinieerd in het [element Openbaar](diagnostics-extension-schema-windows.md#publicconfig-element) van het configuratieschema. Hieronder volgt een minimaal voorbeeld van een bestand met openbare instellingen waarmee diagnostische infrastructuurlogboeken, één prestatiemeteren en één gebeurtenislogboek kunnen worden opgehaald. Zie [Voorbeeldconfiguratie](diagnostics-extension-schema-windows.md#publicconfig-element) voor volledige details van de openbare instellingen.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell-implementatie
PowerShell kan worden gebruikt om de Azure Diagnostics-extensie te implementeren op een bestaande virtuele machine met [set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) zoals in het volgende voorbeeld. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

De privé-instellingen worden gedefinieerd in het [element PrivateConfig,](diagnostics-extension-schema-windows.md#privateconfig-element)terwijl de openbare instellingen worden gedefinieerd in het [element Openbaar](diagnostics-extension-schema-windows.md#publicconfig-element) van het configuratieschema. U er ook voor kiezen om de details van het opslagaccount op te geven als parameters van de cmdlet Set-AzVMDiagnosticsExtension in plaats van ze op te nemen in de privé-instellingen.

Hieronder volgt een minimaal voorbeeld van een configuratiebestand waarmee diagnostische infrastructuurlogboeken, één prestatiemeteren en één gebeurtenislogboek kunnen worden opgehaald. Zie [Voorbeeldconfiguratie](diagnostics-extension-schema-windows.md#publicconfig-element) voor volledige details van de privé- en openbare instellingen. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Zie ook [PowerShell gebruiken om Azure Diagnostics in te schakelen in een virtuele machine met Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Gegevensopslag
In de volgende tabel worden de verschillende soorten gegevens weergegeven die zijn verzameld uit de diagnostische extensie en of ze zijn opgeslagen als een tabel of een blob. De gegevens die in tabellen zijn opgeslagen, kunnen ook worden opgeslagen in blobs, afhankelijk van de [instelling StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) in uw openbare configuratie.


| Gegevens | Opslagtype | Beschrijving |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabel | Diagnostische monitor- en configuratiewijzigingen. |
| WADDirectoriesTable | Tabel | Mappen die de diagnostische monitor controleert.  Dit omvat IIS-logboeken, IIS-logboeken met mislukte aanvragen en aangepaste mappen.  De locatie van het bloblogboekbestand wordt opgegeven in het veld Container en de naam van de blob bevindt zich in het veld Relativepath.  Het veld AbsolutePath geeft de locatie en naam van het bestand aan zoals het bestond op de virtuele Azure-machine. |
| WadLogsTable | Tabel | Logboeken geschreven in code met behulp van de trace listener. |
| WADPerformanceCountersTabel | Tabel | Prestatiemeteritems. |
| TABEL WADWindowsEventLogs | Tabel | Windows Event-logboeken. |
| wad-iis-failedreqlogfiles | Blob | Bevat informatie uit logboeken met mislukte aanvraag van IIS. |
| wad-iis-logfiles | Blob | Bevat informatie over IIS-logboeken. |
| "aangepaste" | Blob | Een aangepaste container op basis van het configureren van mappen die worden gecontroleerd door de diagnostische monitor.  De naam van deze blobcontainer wordt opgegeven in WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Hulpprogramma's voor het weergeven van diagnostische gegevens
Er zijn verschillende tools beschikbaar om de gegevens te bekijken nadat deze naar opslag zijn overgebracht. Bijvoorbeeld:

* Server Explorer in Visual Studio - Als u de Azure Tools voor Microsoft Visual Studio hebt geïnstalleerd, u het Azure-opslagknooppunt in Server Explorer gebruiken om alleen-lezen blob- en tabelgegevens van uw Azure-opslagaccounts weer te geven. U gegevens weergeven van uw emulatoraccount voor lokale opslag en ook van opslagaccounts die u voor Azure hebt gemaakt. Zie [Opslagbronnen bladeren en beheren met Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)voor meer informatie.
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is een zelfstandige app waarmee u eenvoudig werken met Azure Storage-gegevens op Windows, OSX en Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) bevat Azure Diagnostics Manager waarmee u de diagnostische gegevens bekijken, downloaden en beheren die worden verzameld door de toepassingen die op Azure worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen
- Zie [Gegevens van windows Azure-diagnostische extensie verzenden naar Gebeurtenishubs](diagnostics-extension-stream-event-hubs.md) voor meer informatie over het doorsturen van bewakingsgegevens naar Azure Event Hubs.
