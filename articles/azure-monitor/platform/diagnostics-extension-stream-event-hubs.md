---
title: Gegevens van Windows Azure-diagnostische extensie verzenden naar Azure Event Hubs
description: Configureer diagnostische extensie in Azure Monitor om gegevens naar Azure Event Hub te verzenden, zodat u deze doorsturen naar locaties buiten Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672528"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Gegevens van Windows Azure-diagnostische extensie verzenden naar Azure Event Hubs
Azure-diagnostische extensie is een agent in Azure Monitor die bewakingsgegevens verzamelt van het gastbesturingssysteem en workloads van Azure virtuele machines en andere rekenbronnen. In dit artikel wordt beschreven hoe u gegevens van de Wad (Windows Azure Diagnostic extension) naar [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) verzendt, zodat u doorsturen naar locaties buiten Azure.

## <a name="supported-data"></a>Ondersteunde gegevens

De gegevens die worden verzameld van het gastbesturingssysteem die naar gebeurtenishubs kunnen worden verzonden, bevatten de volgende gegevens. Andere gegevensbronnen die door WAD worden verzameld, waaronder IIS-logboeken en crashdumps, kunnen niet naar gebeurtenishubs worden verzonden.

* ETW-gebeurtenissen (Event Tracing for Windows)
* Prestatiemeteritems
* Windows-gebeurtenislogboeken, inclusief toepassingslogboeken in het Windows-gebeurtenislogboek
* Logboeken van Azure Diagnostics-infrastructuur

## <a name="prerequisites"></a>Vereisten

* Windows diagnostische extensie 1.6 of hoger. Zie [de configuratieschemaversies en -geschiedenis van Azure Diagnostics-extensie](diagnostics-extension-versions.md) voor een versiegeschiedenis en azure [diagnostics-extensieoverzicht](diagnostics-extension-overview.md) voor ondersteunde resources.
* Naamruimte voor gebeurtenishubs moet altijd worden ingericht. Zie [Aan de slag met Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) voor meer informatie.


## <a name="configuration-schema"></a>Configuratieschema
Zie [Wad (Installand Configure)](diagnostics-extension-windows-install.md) voor verschillende opties voor het inschakelen en configureren van de diagnostische extensie en [het configuratieschema](diagnostics-extension-schema-windows.md) azure diagnostics voor een verwijzing naar het configuratieschema. In de rest van dit artikel wordt beschreven hoe u deze configuratie gebruiken om gegevens naar een gebeurtenishub te verzenden. 

Azure Diagnostics stuurt altijd logboeken en statistieken naar een Azure Storage-account. U een of meer *gegevensputten* configureren die gegevens naar extra locaties verzenden. Elke gootsteen wordt gedefinieerd in het [SinksConfig-element](diagnostics-extension-schema-windows.md#sinksconfig-element) van de openbare configuratie met gevoelige informatie in de privéconfiguratie. Deze configuratie voor gebeurtenishubs gebruikt de waarden in de volgende tabel.

| Eigenschap | Beschrijving |
|:---|:---|
| Name | Beschrijvende naam voor de gootsteen. Wordt in de configuratie gebruikt om aan te geven welke gegevensbronnen naar de gootsteen moeten worden verzonden. |
| URL  | Url van de gebeurtenishub \<in de form\>event-hubs-namespace .servicebus.windows.net/\<gebeurtenis-hubnaam\>.          |
| SharedAccessKeyName | Naam van een gedeeld toegangsbeleid voor de gebeurtenishub die ten minste de bevoegdheid **voor verzenden** heeft. |
| SharedAccessKey     | Primaire of secundaire sleutel van het beleid voor gedeelde toegang voor de gebeurtenishub. |

Voorbeeld van openbare en privéconfiguraties worden hieronder weergegeven. Dit is een minimale configuratie met één prestatiemeter en gebeurtenislogboek om te illustreren hoe u de gebeurtenishubgegevenssink configureren en gebruiken. Zie [configuratieschema Azure Diagnostics](diagnostics-extension-schema-windows.md) voor een complexer voorbeeld.

### <a name="public-configuration"></a>Openbare configuratie

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Privéconfiguratie

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Configuratie-opties
Als u gegevens naar een gegevenssink wilt verzenden, geeft u het **eigenschapsinks** op het knooppunt van de gegevensbron op. Waar u het eigenschap **sinks** plaatst, bepaalt het bereik van de toewijzing. In het volgende voorbeeld wordt het kenmerk **Sinks** gedefinieerd in het knooppunt **Prestatiemeteritems,** waardoor alle prestatiemeteritems voor kinderen naar de gebeurtenishub worden verzonden.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


In het volgende voorbeeld wordt het eigenschap **sinks** rechtstreeks toegepast op drie tellers, waardoor alleen die prestatiemeteritems naar de gebeurtenishub worden verzonden. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Configuratie valideren
U verschillende methoden gebruiken om te valideren dat gegevens naar de gebeurtenishub worden verzonden. ne eenvoudige methode is het gebruik van Event Hubs vastleggen zoals beschreven in [Capture gebeurtenissen via Azure Event Hubs in Azure Blob Storage of Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Problemen met gebeurtenishubs oplossen

- Kijk naar de AZURE Storage-tabel **WADDiagnosticInfrastructureLogsTable** met logboeken en fouten voor Azure Diagnostics zelf. Een optie is om een hulpprogramma zoals [Azure Storage Explorer](https://www.storageexplorer.com) te gebruiken om verbinding te maken met dit opslagaccount, deze tabel weer te geven en een query voor TimeStamp toe te voegen in de afgelopen 24 uur. U het hulpprogramma gebruiken om een CSV-bestand te exporteren en te openen in een toepassing zoals Microsoft Excel. Excel maakt het eenvoudig om te zoeken naar telefoonkaarten, zoals **EventHubs,** om te zien welke fout wordt gerapporteerd.  

- Controleer of uw gebeurtenishub is ingericht. Alle verbindingsgegevens in het **gedeelte PrivateConfig** van de configuratie moeten overeenkomen met de waarden van uw bron zoals die in de portal worden gezien. Zorg ervoor dat u een SAS-beleid hebt gedefinieerd *(SendRule* in het voorbeeld) in de portal en dat *Toestemming verzenden* wordt verleend.  

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van gebeurtenishubs](../../event-hubs/event-hubs-about.md)
* [Een gebeurtenishub maken](../../event-hubs/event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



