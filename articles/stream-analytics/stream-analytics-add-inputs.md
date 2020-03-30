---
title: Inzicht in invoer voor Azure Stream Analytics
description: In dit artikel wordt het concept van invoer in een Azure Stream Analytics-taak beschreven, waarbij streaming-invoer wordt vergeleken met referentiegegevensinvoer.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426437"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Inzicht in invoer voor Azure Stream Analytics

Azure Stream Analytics-taken maken verbinding met een of meer gegevensinvoer. Elke invoer definieert een verbinding met een bestaande gegevensbron. Stream Analytics accepteert gegevens die binnenkomen van verschillende soorten gebeurtenisbronnen, waaronder Gebeurtenishubs, IoT Hub en Blob-opslag. De ingangen worden op naam verwezen in de streaming SQL-query die u voor elke taak schrijft. In de query u meerdere ingangen gebruiken om gegevens te verwerken of streaminggegevens te vergelijken met een opzoekinformatie naar referentiegegevens en de resultaten doorgeven aan uitvoer. 

Stream Analytics heeft eersteklas integratie met drie soorten resources als ingang:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT-hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) 

Deze invoerbronnen kunnen worden gebruikt in hetzelfde Azure-abonnement als uw Stream Analytics-taak of met een ander abonnement.

U de [Azure-portal](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput) [.NET API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions) [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)en [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) gebruiken om streamanalytics-taakinvoer te maken, bewerken en testen.

## <a name="stream-and-reference-inputs"></a>Stroom- en referentieingangen
Als gegevens naar een gegevensbron worden gepusht, wordt deze verbruikt door de streamanalytics-taak en in realtime verwerkt. Invoer wordt onderverdeeld in twee typen: gegevensstroominvoer en verwijzingsgegevensinvoer.

### <a name="data-stream-input"></a>Gegevensstroominvoer
Een gegevensstroom is een onbegrensde reeks gebeurtenissen in de loop van de tijd. Stream Analytics-taken moeten minimaal één gegevensstroominvoer bevatten. Event Hubs, IoT Hub en Blob-opslag worden ondersteund als gegevensstroominvoerbronnen. Gebeurtenishubs worden gebruikt om gebeurtenisstreams te verzamelen van meerdere apparaten en services. Deze streams kunnen activiteitenfeeds voor sociale media, informatie over de handel in aandelen of gegevens van sensoren bevatten. IoT-hubs zijn geoptimaliseerd om gegevens van verbonden apparaten te verzamelen in IoT-scenario's (Internet of Things).  Blob-opslag kan worden gebruikt als invoerbron voor het opnemen van bulkgegevens als stream, zoals logboekbestanden.  

Zie Gegevens streamen als invoer in Stream Analytics voor meer informatie over het streamen van [gegevens.](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referentiegegevensinvoer
Stream Analytics ondersteunt ook invoer die ook *referentiegegevens*wordt genoemd. Referentiegegevens zijn volledig statisch of veranderen langzaam. Het wordt meestal gebruikt om correlatie en lookups uit te voeren. U bijvoorbeeld gegevens aansluiten in de gegevensstroominvoer naar gegevens in de referentiegegevens, net zoals u een SQL-join uitvoert om statische waarden op te zoeken. Azure Blob-opslag en Azure SQL Database worden momenteel ondersteund als invoerbronnen voor referentiegegevens. Referentiegegevensbronblobs hebben een limiet van maximaal 300 MB, afhankelijk van de complexiteit van de query en toegewezen streamingeenheden (zie de sectie [Groottebeperking](stream-analytics-use-reference-data.md#size-limitation) van de referentiegegevensdocumentatie voor meer details).

Zie [Referentiegegevens gebruiken voor opzoekingen in Stream Analytics](stream-analytics-use-reference-data.md) voor meer informatie over invoer van referentiegegevens

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
