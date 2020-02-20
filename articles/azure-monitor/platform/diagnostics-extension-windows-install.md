---
title: De Windows Azure Diagnostics-extensie (WAD) installeren en configureren
description: Meer informatie over het verzamelen van diagnostische gegevens van Azure in een Azure Storage-account, zodat u deze kunt bekijken met een van de beschik bare hulpprogram ma's.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 5b3cc4cbaa663b7932609e85c544378a7cca69ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472685"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>De Windows Azure Diagnostics-extensie (WAD) installeren en configureren
Azure Diagnostics-extensie is een agent in Azure Monitor die bewakings gegevens van het gast besturingssysteem en werk belastingen van virtuele Azure-machines en andere reken bronnen verzamelt. Dit artikel bevat informatie over het installeren en configureren van de Windows diagnostische uitbrei ding en een beschrijving van de manier waarop de gegevens worden opgeslagen in en Azure Storage-account.

De uitbrei ding van diagnostische gegevens wordt geïmplementeerd als extensie van een [virtuele machine](/virtual-machines/extensions/overview) in azure, zodat deze dezelfde installatie opties ondersteunt met Resource Manager-sjablonen, Power shell en cli. Zie [extensies en functies voor virtuele machines voor Windows](/virtual-machines/extensions/features-windows) voor meer informatie over het installeren en onderhouden van extensies van virtuele machines.

## <a name="install-with-azure-portal"></a>Installeren met Azure Portal
U kunt de diagnostische extensie installeren en configureren op een afzonderlijke virtuele machine in de Azure Portal die u een interface biedt in plaats van rechtstreeks met de configuratie te werken. Wanneer u de uitbrei ding voor diagnostische gegevens inschakelt, wordt automatisch een standaard configuratie gebruikt met de meest voorkomende prestatie meter items en-gebeurtenissen. U kunt deze standaard configuratie aanpassen op basis van uw specifieke vereisten.

> [!NOTE]
> Er zijn instellingen voor diagnostische uitbrei dingen die u niet kunt configureren met de Azure Portal, zoals het verzenden van gegevens naar Azure Event Hubs. U moet een van de andere configuratie methoden voor deze instellingen gebruiken.

1. Open het menu voor een virtuele machine in de Azure Portal.
2. Klik op **Diagnostische instellingen** in de sectie **bewaking** van het VM-menu.
3. Klik op **bewaking op gast niveau inschakelen** als de uitbrei ding voor diagnostische gegevens nog niet is ingeschakeld.
4. Er wordt een nieuw Azure Storage-account gemaakt voor de virtuele machine met de naam die is gebaseerd op de naam van de resource groep voor de virtuele machine. U kunt de virtuele machine koppelen aan een ander opslag account door het tabblad **agent** te selecteren.

![Diagnostische instellingen](media/diagnostics-extension-windows-install/diagnostic-settings.png)


U kunt de standaard configuratie wijzigen nadat de diagnostische uitbrei ding is ingeschakeld. In de volgende tabel worden de opties beschreven die u kunt wijzigen op de verschillende tabbladen. Sommige opties hebben een **aangepaste** opdracht waarmee u meer gedetailleerde configuratie kunt opgeven. Zie het [uitbreidings schema voor Windows diagnostische](diagnostics-extension-schema-windows.md) gegevens voor meer informatie over de verschillende instellingen.

| Tabbesturingselement | Beschrijving |
|:---|:---|
| Overzicht | Hiermee wordt de huidige configuratie met koppelingen naar de andere tabbladen weer gegeven. |
| Prestatiemeteritems | Selecteer de prestatie meter items die u wilt verzamelen en de sampling frequentie voor elke.  |
| Logboeken | Selecteer de logboek gegevens die u wilt verzamelen. Dit omvat Windows-gebeurtenis logboeken, IIS-logboeken, .NET-toepassings logboeken en ETW-gebeurtenissen.  |
| Crashdumps | Crash dump inschakelen voor verschillende processen. |
| Wastafel | Schakel gegevens filters in om naast Azure Storage ook gegevens naar bestemmingen te verzenden.<br>Azure Monitor: verzendt prestatie gegevens naar Azure Monitor meet waarden.<br>Application Insights: gegevens verzenden naar een Application Insights-toepassing. |
| Agent | Wijzig de volgende configuratie voor de agent:<br>-Wijzig het opslag account.<br>-Geef de maximale lokale schijf op die wordt gebruikt voor de agent.<br>-Logboeken configureren voor de status van de agent zelf.|


> [!NOTE]
> Hoewel de configuratie voor de diagnostische gegevens in JSON of XML kan worden geformatteerd, worden alle configuraties die in de Azure Portal worden uitgevoerd, altijd opgeslagen als JSON. Als u XML gebruikt met een andere configuratie methode en vervolgens uw configuratie wijzigt met de Azure Portal, worden de instellingen gewijzigd in JSON.

## <a name="resource-manager-template"></a>Resource Manager-sjabloon
Zie [controle en diagnose gebruiken met een Windows-VM en Azure Resource Manager sjablonen](../../virtual-machines/extensions/diagnostics-template.md) voor het implementeren van de diagnostische uitbrei ding met Azure Resource Manager sjablonen. 

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie
De Azure CLI kan worden gebruikt om de Azure Diagnostics-extensie op een bestaande virtuele machine te implementeren met behulp van [AZ VM extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) , zoals in het volgende voor beeld. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

De beveiligde instellingen worden gedefinieerd in het [PrivateConfig-element](diagnostics-extension-schema-windows.md#privateconfig-element) van het configuratie schema. Hieronder volgt een mini maal voor beeld van een bestand met beveiligde instellingen waarmee het opslag account wordt gedefinieerd. Bekijk de [voorbeeld configuratie](diagnostics-extension-schema-windows.md#privateconfig-element) voor de volledige details van de persoonlijke instellingen.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
De open bare instellingen worden gedefinieerd in het [open bare element](diagnostics-extension-schema-windows.md#publicconfig-element) van het configuratie schema. Hieronder volgt een mini maal voor beeld van een bestand met open bare instellingen waarmee u logboeken met diagnostische infra structuur, één prestatie meter item en één gebeurtenis logboek kunt verzamelen. Bekijk de [voorbeeld configuratie](diagnostics-extension-schema-windows.md#publicconfig-element) voor de volledige details van de open bare instellingen.

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



## <a name="powershell-deployment"></a>Power shell-implementatie
Power shell kan worden gebruikt om de Azure Diagnostics-extensie te implementeren op een bestaande virtuele machine met behulp van [set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) , zoals in het volgende voor beeld. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

De persoonlijke instellingen worden gedefinieerd in het [PrivateConfig-element](diagnostics-extension-schema-windows.md#privateconfig-element), terwijl de open bare instellingen worden gedefinieerd in het [open bare element](diagnostics-extension-schema-windows.md#publicconfig-element) van het configuratie schema. U kunt er ook voor kiezen om de details van het opslag account op te geven als para meters van de cmdlet Set-AzVMDiagnosticsExtension in plaats van deze op te nemen in de persoonlijke instellingen.

Hieronder volgt een mini maal voor beeld van een configuratie bestand dat het verzamelen van logboeken met diagnostische infra structuur, één prestatie meter item en één gebeurtenis logboek mogelijk maakt. Bekijk de [voorbeeld configuratie](diagnostics-extension-schema-windows.md#publicconfig-element) voor de volledige details van de persoonlijke en open bare instellingen. 

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

Zie ook [Power shell gebruiken om Azure Diagnostics in te scha kelen op een virtuele machine waarop Windows wordt uitgevoerd](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Gegevensopslag
De volgende tabel geeft een lijst van de verschillende typen gegevens die worden verzameld uit de diagnostische uitbrei ding en of ze worden opgeslagen als een tabel of BLOB. De gegevens die zijn opgeslagen in tabellen kunnen ook worden opgeslagen in blobs, afhankelijk van de [instelling para](diagnostics-extension-schema-windows.md#publicconfig-element) in uw open bare configuratie.


| Gegevens | Opslagtype | Beschrijving |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabel | Diagnostische monitor-en configuratie wijzigingen. |
| WADDirectoriesTable | Tabel | Mappen die door de diagnostische monitor worden bewaakt.  Dit zijn onder andere IIS-logboeken, IIS-aanvraag logboeken en aangepaste directory's.  De locatie van het BLOB-logboek bestand wordt opgegeven in het container veld en de naam van de BLOB bevindt zich in het veld RelativePath.  Het veld AbsolutePath geeft de locatie en de naam van het bestand aan zoals het aanwezig is op de virtuele Azure-machine. |
| WadLogsTable | Tabel | Logboeken geschreven in code met behulp van de traceer-listener. |
| WADPerformanceCountersTable | Tabel | Prestatie meter items. |
| WADWindowsEventLogsTable | Tabel | Windows-gebeurtenis Logboeken. |
| wad-IIS-failedreqlogfiles | Blob | Bevat informatie uit logboeken voor mislukte aanvragen van IIS. |
| wad-IIS-logboek bestanden | Blob | Bevat informatie over IIS-logboeken. |
| instel | Blob | Een aangepaste container die is gebaseerd op het configureren van mappen die worden bewaakt door de diagnostische monitor.  De naam van deze BLOB-container wordt opgegeven in WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Hulpprogram ma's voor het weer geven van diagnostische gegevens
Er zijn verschillende hulpprogram ma's beschikbaar voor het weer geven van de gegevens nadat deze zijn overgebracht naar opslag. Bijvoorbeeld:

* Server Explorer in Visual Studio: als u de Azure-Hulpprogram Ma's voor micro soft Visual Studio hebt geïnstalleerd, kunt u het knoop punt Azure Storage in Server Explorer gebruiken om alleen-lezen Blob-en tabel gegevens uit uw Azure-opslag accounts weer te geven. U kunt gegevens weer geven vanuit uw lokale opslag emulator-account en ook van opslag accounts die u hebt gemaakt voor Azure. Zie [opslag resources doorzoeken en beheren met Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)voor meer informatie.
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is een zelfstandige app waarmee u eenvoudig met Azure Storage gegevens kunt werken in Windows, OSX en Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) bevat Azure Diagnostics Manager waarmee u de diagnostische gegevens kunt weer geven, downloaden en beheren die zijn verzameld door de toepassingen die worden uitgevoerd op Azure.

## <a name="next-steps"></a>Volgende stappen
- Zie [gegevens verzenden van de Windows Azure Diagnostics-extensie naar Event hubs](diagnostics-extension-stream-event-hubs.md) voor meer informatie over het door sturen van bewakings gegevens naar Azure Event hubs.
