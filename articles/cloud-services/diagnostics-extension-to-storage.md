---
title: Diagnostische gegevens opslaan en weergeven in Azure Storage
description: Meer informatie over het verzamelen van Azure-diagnostische gegevens in een Azure Storage-account, zodat u deze bekijken met een van de verschillende beschikbare hulpprogramma's.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 17430330d068fb55b45f073afecb8ba348286cb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472672"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Diagnostische gegevens opslaan en weergeven in Azure Storage
Diagnostische gegevens worden niet permanent opgeslagen, tenzij u deze overzet naar de Microsoft Azure-opslagemulator of naar Azure-opslag. Eenmaal in opslag, kan het worden bekeken met een van de verschillende beschikbare tools.

## <a name="specify-a-storage-account"></a>Een opslagaccount opgeven
U geeft het opslagaccount op dat u wilt gebruiken in het bestand ServiceConfiguration.cscfg. De accountgegevens worden gedefinieerd als een verbindingstekenreeks in een configuratie-instelling. In het volgende voorbeeld wordt de standaardverbindingstekenreeks weergegeven die is gemaakt voor een nieuw Cloud Service-project in Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

U deze verbindingstekenreeks wijzigen om accountgegevens voor een Azure-opslagaccount te verstrekken.

Afhankelijk van het type diagnostische gegevens dat wordt verzameld, gebruikt Azure Diagnostics de Blob-service of de tabelservice. In de volgende tabel worden de gegevensbronnen weergegeven die blijven bestaan en de indeling ervan.

| Gegevensbron | Opslagindeling |
| --- | --- |
| Azure-logboeken |Tabel |
| IIS 7.0-logboeken |Blob |
| Logboeken van Azure Diagnostics-infrastructuur |Tabel |
| Logboeken van mislukte aanvraagtracering |Blob |
| Logboeken voor Windows-gebeurtenissen |Tabel |
| Prestatiemeteritems |Tabel |
| Crashdumps |Blob |
| Aangepaste foutenlogboeken |Blob |

## <a name="transfer-diagnostic-data"></a>Diagnostische gegevens overbrengen
Voor SDK 2.5 en hoger kan het verzoek om diagnostische gegevens over te dragen via het configuratiebestand plaatsvinden. U diagnostische gegevens overdragen met geplande intervallen zoals opgegeven in de configuratie.

Voor SDK 2.4 en vorige u vragen om de diagnostische gegevens zowel via het configuratiebestand als programmatisch over te dragen. De programmatische aanpak stelt u ook in staat om on-demand transfers te doen.

> [!IMPORTANT]
> Wanneer u diagnostische gegevens overdraagt naar een Azure-opslagaccount, brengt u kosten met zich mee voor de opslagbronnen die uw diagnostische gegevens gebruiken.
> 
> 

## <a name="store-diagnostic-data"></a>Diagnostische gegevens opslaan
Logboekgegevens worden opgeslagen in blob- of tabelopslag met de volgende namen:

**Tabellen**

* **WadLogsTable** - Logs geschreven in code met behulp van de trace listener.
* **WADDiagnosticInfrastructureLogsTable** - Diagnostische monitor- en configuratiewijzigingen.
* **WADDirectoriesTable** – Mappen die de diagnostische monitor controleert.  Dit omvat IIS-logboeken, IIS-logboeken met mislukte aanvragen en aangepaste mappen.  De locatie van het bloblogboekbestand wordt opgegeven in het veld Container en de naam van de blob bevindt zich in het veld Relativepath.  Het veld AbsolutePath geeft de locatie en naam van het bestand aan zoals het bestond op de virtuele Azure-machine.
* **WADPerformanceCountersTable** – Prestatiemeteritems.
* **WADWindowsEventLogsTable** – Windows Event logs.

**Blobs**

* **wad-control-container** – (Alleen voor SDK 2.4 en vorige) Bevat de XML-configuratiebestanden die de Azure-diagnose regelt.
* **wad-iis-failedreqlogfiles** – Bevat informatie uit IIS Failed Request-logboeken.
* **wad-iis-logfiles** – Bevat informatie over IIS-logboeken.
* **'op maat'** – Een aangepaste container op basis van het configureren van mappen die worden gecontroleerd door de diagnostische monitor.  De naam van deze blobcontainer wordt opgegeven in WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Hulpprogramma's voor het weergeven van diagnostische gegevens
Er zijn verschillende tools beschikbaar om de gegevens te bekijken nadat deze naar opslag zijn overgebracht. Bijvoorbeeld:

* Server Explorer in Visual Studio - Als u de Azure Tools voor Microsoft Visual Studio hebt geïnstalleerd, u het Azure-opslagknooppunt in Server Explorer gebruiken om alleen-lezen blob- en tabelgegevens van uw Azure-opslagaccounts weer te geven. U gegevens weergeven van uw emulatoraccount voor lokale opslag en ook van opslagaccounts die u voor Azure hebt gemaakt. Zie [Opslagbronnen bladeren en beheren met Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)voor meer informatie.
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een zelfstandige app waarmee u eenvoudig werken met Azure Storage-gegevens op Windows, OSX en Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) bevat Azure Diagnostics Manager waarmee u de diagnostische gegevens bekijken, downloaden en beheren die worden verzameld door de toepassingen die op Azure worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen
[De stroom in een Cloud Services-toepassing traceren met Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


