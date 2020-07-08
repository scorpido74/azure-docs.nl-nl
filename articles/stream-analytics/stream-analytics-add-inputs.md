---
title: Wat is invoer van Azure Stream Analytics?
description: In dit artikel wordt het concept van invoer in een Azure Stream Analytics-taak beschreven, waarbij de invoer van streaming wordt vergeleken met de invoer van gegevens.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75426437"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Wat is invoer van Azure Stream Analytics?

Azure Stream Analytics-taken maken verbinding met een of meer gegevens invoer. Elke invoer definieert een verbinding met een bestaande gegevens bron. Stream Analytics accepteert gegevens die binnenkomen van verschillende soorten gebeurtenis bronnen, waaronder Event Hubs-, IoT Hub-en Blob-opslag. Er wordt naar de invoer verwezen met de naam in de streaming SQL-query die u voor elke taak schrijft. In de query kunt u meerdere invoer bewerkingen uitvoeren om gegevens te combi neren of streaminggegevens te vergelijken met een zoek opdracht om te verwijzen naar gegevens en de resultaten door te geven aan uitvoer. 

Stream Analytics heeft eersteklas integratie met drie soorten resources als invoer:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Deze invoer resources kunnen in hetzelfde Azure-abonnement wonen als uw Stream Analytics-taak of vanuit een ander abonnement.

U kunt de [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-input), [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [.net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [rest API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)en [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) gebruiken voor het maken, bewerken en testen van stream Analytics taak invoer.

## <a name="stream-and-reference-inputs"></a>Invoer van Stream en referentie
Wanneer gegevens worden gepusht naar een gegevens bron, wordt deze door de Stream Analytics-taak verbruikt en in realtime verwerkt. Invoer wordt onderverdeeld in twee typen: gegevensstroominvoer en verwijzingsgegevensinvoer.

### <a name="data-stream-input"></a>Invoer van gegevens stroom
Een gegevens stroom is na verloop van tijd een niet-gebonden reeks gebeurtenissen. Stream Analytics-taken moeten minimaal één gegevensstroominvoer bevatten. Event Hubs, IoT Hub en Blob-opslag worden ondersteund als gegevensstroominvoerbronnen. Event Hubs worden gebruikt voor het verzamelen van gebeurtenis stromen van meerdere apparaten en services. Deze stromen kunnen activiteiten voor sociale media, aandelen handel of gegevens van Sens oren bevatten. IoT-hubs zijn geoptimaliseerd voor het verzamelen van gegevens van verbonden apparaten in Internet of Things-scenario's (IoT).  Blob-opslag kan worden gebruikt als invoer bron voor het opnemen van bulk gegevens als een stroom, zoals logboek bestanden.  

Zie [gegevens streamen als invoer in stream Analytics](stream-analytics-define-inputs.md) voor meer informatie over het streamen van gegevens.

### <a name="reference-data-input"></a>Invoer van referentie gegevens
Stream Analytics biedt ook ondersteuning voor invoer die bekend staat als *referentie gegevens*. Referentie gegevens zijn volledig statisch of veranderen langzaam. Het wordt doorgaans gebruikt om correlatie en lookups uit te voeren. U kunt bijvoorbeeld gegevens in de gegevens stroom invoer toevoegen aan gegevens in de referentie gegevens, net zoals u een SQL-koppeling uitvoert om statische waarden op te zoeken. Azure Blob-opslag en Azure SQL Database worden momenteel ondersteund als invoer bronnen voor referentie gegevens. Referentie gegevens bron-blobs hebben een limiet van Maxi maal 300 MB, afhankelijk van de query complexiteit en toegewezen streaming-eenheden (Zie de sectie [grootte beperking](stream-analytics-use-reference-data.md#size-limitation) van de documentatie voor referentie gegevens voor meer informatie).

Zie [referentie gegevens gebruiken voor zoek acties in stream Analytics](stream-analytics-use-reference-data.md) voor meer informatie over het invoeren van referentie gegevens.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
