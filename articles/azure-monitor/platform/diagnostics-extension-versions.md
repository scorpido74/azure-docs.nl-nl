---
title: Wad-configuratieschemaversiegeschiedenis (Windows Azure Diagnostics Extension Extension Extension)
description: Relevant voor het verzamelen van perf-tellers in Azure Virtual Machines, VM-schaalsets, Servicefabric en Cloud Services.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 4dd91363cdebf18e6303238816e8269065a6a317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672239"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Wad-configuratieschemaversies en -geschiedenis (Windows Azure Diagnostics Extension Extension)
In dit artikel vindt u de versiegeschiedenis van de schemaversies azure [diagnostics-extensie voor Windows (WAD)](diagnostics-extension-overview.md) die worden verzonden als onderdeel van de Microsoft Azure SDK.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK- en diagnostische versies verzendgrafiek  

|Azure SDK-versie | Diagnostische extensieversie | Model|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |Insteekmodule|  
|2.0 - 2.4         |1.0                            |Insteekmodule|  
|2,5               |1.2                            |Extensie|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2,8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Azure Diagnostics versie 1.0 wordt voor het eerst verzonden in een plug-inmodel, wat betekent dat wanneer u de Azure SDK installeerde, u de versie van Azure-diagnose hebt geleverd.  

 Vanaf SDK 2.5 (diagnostische versie 1.2) ging Azure-diagnostiek naar een extensiemodel. De hulpprogramma's voor het gebruik van nieuwe functies waren alleen beschikbaar in nieuwere Azure-SDK's, maar elke service met Azure-diagnose zou de nieuwste verzendversie rechtstreeks van Azure ophalen. Iedereen die SDK 2.5 nog gebruikt, laadt bijvoorbeeld de nieuwste versie in de vorige tabel, ongeacht of ze de nieuwere functies gebruiken.  

## <a name="schemas-index"></a>Schema's-index  
Verschillende versies van Azure-diagnostiek maken gebruik van verschillende configuratieschema's. Schema 1.0 en 1.2 zijn afgeschaft. Zie [Diagnostics 1.3 en hoger Configuratieschema](diagnostics-extension-schema-windows.md) voor meer informatie over versie 1.3 en hoger  

## <a name="version-history"></a>Versiegeschiedenis

### <a name="diagnostics-extension-111"></a>Diagnostische extensie 1.11
Ondersteuning toegevoegd voor de Azure Monitor-sink. Deze gootsteen is alleen van toepassing op prestatiemeteritems. Hiermee u prestatiemeteritems die zijn verzameld op uw VM-, VMSS- of cloudservice naar Azure Monitor verzenden als aangepaste statistieken. De Azure Monitor-sink ondersteunt:
* Alle prestatiemeteritems ophalen die naar Azure Monitor worden verzonden via de [API's voor Azure Monitor-statistieken.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Waarschuwingen voor alle prestatiemeteritems die naar Azure Monitor worden verzonden via de nieuwe [uniforme waarschuwingenervaring](../../azure-monitor/platform/alerts-overview.md) in Azure Monitor
* De jokeroperator in prestatiemeteritems behandelen als de dimensie 'Instantie' op uw statistiek. Als u bijvoorbeeld de teller\*'Logische schijf(/DiskWrites/sec hebt verzameld, u de dimensie 'Instantie' filteren en splitsen om te plotten of te waarschuwen op de schijfschrijf-en -seconde voor elke logische schijf (C:, D:, enz.)

Azure Monitor definiëren als een nieuwe sink in de configuratie van uw diagnose-extensie
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Voor het configureren van de Azure Monitor-sink voor klassieke VM's en Classic CLoud Service moeten meer parameters worden gedefinieerd in de privé-config van de Diagnostische extensie.
>
> Voor meer informatie verwijzen wij u naar de [gedetailleerde documentatie van het diagnose-extensieschema.](diagnostics-extension-schema-windows.md)

Vervolgens u de prestatiemeteritems configureren om naar de Azure Monitor Sink te worden doorgestuurd.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Diagnostische extensie 1.9
Docker-ondersteuning toegevoegd.


### <a name="diagnostics-extension-181"></a>Diagnostische extensie 1.8.1
Kan een SAS-token opgeven in plaats van een opslagaccountsleutel in de privé-config. Als er een SAS-token wordt verstrekt, wordt de opslagaccountsleutel genegeerd.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Diagnostische extensie 1.8
Opslagtype toegevoegd aan PublicConfig. StorageType kan *tabel*, *blob,* *tableAndblob*zijn . *Tabel* is de standaardtabel.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Diagnostische extensie 1.7
Toegevoegd de mogelijkheid om een route naar EventHub.

### <a name="diagnostics-extension-15"></a>Diagnose-extensie 1,5
Toegevoegd de sinks element en de mogelijkheid om diagnostische gegevens te sturen naar [Application Insights](../../azure-monitor/app/cloudservices.md) waardoor het gemakkelijker is om problemen te diagnosticeren in uw toepassing, evenals het systeem en infrastructuur niveau.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 en diagnostische extensie 1.3
Voor Cloud Service-projecten in Visual Studio zijn de volgende wijzigingen aangebracht. (Deze wijzigingen zijn ook van toepassing op latere versies van Azure SDK.)

* De lokale emulator ondersteunt nu diagnostiek. Deze wijziging betekent dat u diagnostische gegevens verzamelen en ervoor zorgen dat uw toepassing de juiste sporen maakt terwijl u zich ontwikkelt en test in Visual Studio. Met de `UseDevelopmentStorage=true` verbindingstekenreeks u diagnostische gegevens verzamelen terwijl u uw cloudserviceproject uitvoert in Visual Studio met behulp van de Azure-opslagemulator. Alle diagnostische gegevens worden verzameld in het opslagaccount (Development Storage).
* De tekenreeks voor de verbinding met de diagnostische opslagaccount (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) wordt opnieuw opgeslagen in het bestand serviceconfiguratie (.cscfg). In Azure SDK 2.5 is het diagnostische opslagaccount opgegeven in het bestand diagnostics.wadcfgx.

Er zijn enkele opmerkelijke verschillen tussen hoe de verbindingstekenreeks werkte in Azure SDK 2.4 en eerder en hoe deze werkt in Azure SDK 2.6 en hoger.

* In Azure SDK 2.4 en eerder werd de verbindingstekenreeks tijdens runtime gebruikt door de diagnostische plug-in om de opslagaccountgegevens te krijgen voor het overbrengen van diagnostische logboeken.
* In Azure SDK 2.6 en hoger gebruikt Visual Studio de tekenreeks voor diagnostische verbindingen om de diagnostische extensie te configureren met de juiste opslagaccountgegevens tijdens het publiceren. Met de verbindingstekenreeks u verschillende opslagaccounts definiëren voor verschillende serviceconfiguraties die Visual Studio bij het publiceren zal gebruiken. Omdat de diagnostische plug-in echter niet meer beschikbaar is (na Azure SDK 2.5), kan het .cscfg-bestand op zichzelf de diagnostische extensie niet inschakelen. U moet de extensie afzonderlijk inschakelen via tools zoals Visual Studio of PowerShell.
* Om het configureren van de diagnostische extensie met PowerShell te vereenvoudigen, bevat de pakketuitvoer van Visual Studio ook de openbare configuratie-XML voor de diagnostische extensie voor elke rol. Visual Studio gebruikt de tekenreeks voor diagnostische verbindingen om de opslagaccountgegevens in de openbare configuratie in te vullen. De openbare config-bestanden worden gemaakt in `PaaSDiagnostics.<RoleName>.PubConfig.xml`de map Extensies en volgen het patroon . Alle Op PowerShell gebaseerde implementaties kunnen dit patroon gebruiken om elke configuratie aan een rol toe te stellen.
* De verbindingstekenreeks in het CSCFG-bestand wordt ook gebruikt door de Azure-portal om toegang te krijgen tot de diagnostische gegevens, zodat deze kan worden weergegeven op het tabblad **Controle.** De verbindingstekenreeks is nodig om de service te configureren om uitgebreide bewakingsgegevens in de portal weer te geven.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Projecten migreren naar Azure SDK 2.6 en hoger
Bij het migreren van Azure SDK 2.5 naar Azure SDK 2.6 of hoger, als u een diagnostisch opslagaccount had opgegeven in het .wadcfgx-bestand, blijft het daar. Als u wilt profiteren van de flexibiliteit van het gebruik van verschillende opslagaccounts voor verschillende opslagconfiguraties, moet u handmatig de verbindingstekenreeks aan uw project toevoegen. Als u een project migreert van Azure SDK 2.4 of eerder naar Azure SDK 2.6, blijven de tekenreeksen van de diagnostische verbinding behouden. Let echter op de wijzigingen in de manier waarop verbindingstekenreeksen worden behandeld in Azure SDK 2.6, zoals opgegeven in de vorige sectie.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hoe Visual Studio het opslagaccount voor diagnostiek bepaalt
* Als een tekenreeks voor diagnostische verbindingen is opgegeven in het CSCFG-bestand, gebruikt Visual Studio deze om de diagnostische extensie te configureren bij het publiceren en bij het genereren van xml-bestanden voor openbare configuratie tijdens het verpakken.
* Als er geen diagnostische verbindingstekenreeks is opgegeven in het .cscfg-bestand, wordt Visual Studio teruggezet naar het gebruik van het opslagaccount dat is opgegeven in het .wadcfgx-bestand om de diagnostische extensie te configureren bij het publiceren en het genereren van de xml-bestanden voor openbare configuratie bij verpakking.
* De tekenreeks diagnostische verbinding in het .cscfg-bestand heeft voorrang op het opslagaccount in het .wadcfgx-bestand. Als een tekenreeks voor diagnostische verbindingen is opgegeven in het .cscfg-bestand, gebruikt Visual Studio dat en negeert het opslagaccount in .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Wat doet de tekenreeksen voor de opslagverbinding 'Ontwikkelopslagverbindingen bijwerken'..." checkbox doen?
Het selectievakje voor **De opslagverbindingstekenreeksen voor ontwikkeling bijwerken voor Diagnostische en Caching met Microsoft Azure-opslagaccountreferenties bij publicatie naar Microsoft Azure** biedt u een handige manier om alle tekenreeksen van de ontwikkelingsopslagaccount bij te werken met het Azure-opslagaccount dat tijdens de publicatie is opgegeven.

Stel dat u dit selectievakje inschakelt en `UseDevelopmentStorage=true`de tekenreeks voor de verbinding voor de diagnose opgeeft . Wanneer u het project publiceert naar Azure, werkt Visual Studio automatisch de tekenreeks voor diagnostische verbindingen bij met het opslagaccount dat u hebt opgegeven in de wizard Publiceren. Als er echter een echt opslagaccount is opgegeven als de tekenreeks voor de verbinding met de diagnose, wordt dat account in plaats daarvan gebruikt.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Verschillen in diagnostische functionaliteit tussen Azure SDK 2.4 en eerder en Azure SDK 2.5 en hoger
Als u uw project upgradet van Azure SDK 2.4 naar Azure SDK 2.5 of hoger, moet u rekening houden met de volgende verschillen in diagnostische functionaliteit.

* **Configuratie-API's worden afgeschaft** - De programmatische configuratie van diagnostische gegevens is beschikbaar in Azure SDK 2.4 of eerdere versies, maar wordt afgeschaft in Azure SDK 2.5 en hoger. Als uw diagnostische configuratie momenteel in code is gedefinieerd, moet u deze instellingen opnieuw configureren in het gemigreerde project om de diagnose te laten werken. Het configuratiebestand voor Azure SDK 2.4 is diagnostics.wadcfg en diagnostics.wadcfgx voor Azure SDK 2.5 en hoger.
* **Diagnostiek voor cloudservicetoepassingen kan alleen worden geconfigureerd op rolniveau, niet op instantieniveau.**
* **Elke keer dat u uw app implementeert, wordt de diagnoseconfiguratie bijgewerkt:** dit kan pariteitsproblemen veroorzaken als u uw diagnostische configuratie wijzigt vanuit Server Explorer en uw app vervolgens opnieuw implementeert.
* **In Azure SDK 2.5 en hoger worden crashdumps geconfigureerd in het configuratiebestand voor diagnose, niet in code** - Als u crashdumps in code hebt geconfigureerd, moet u de configuratie handmatig van code naar het configuratiebestand overbrengen, omdat de crashdumps niet worden overgedragen tijdens de migratie naar Azure SDK 2.6.

