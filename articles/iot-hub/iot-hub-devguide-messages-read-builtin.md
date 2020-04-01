---
title: Inzicht in het ingebouwde eindpunt van Azure IoT Hub | Microsoft Documenten
description: Ontwikkelaarshandleiding - beschrijft hoe u het ingebouwde, Event Hub-compatibele eindpunt gebruiken om device-to-cloudberichten te lezen.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: a2674ca0f4808cb6f01781565e57369ca5d3ac37
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478783"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Apparaat-naar-cloud-berichten lezen van het geïntegreerde eindpunt

Standaard worden berichten doorgestuurd naar het ingebouwde servicegerichte eindpunt **(berichten/gebeurtenissen)** dat compatibel is met [gebeurtenishubs.](https://azure.microsoft.com/documentation/services/event-hubs/) Dit eindpunt wordt momenteel alleen belicht met behulp van het [AMQP-protocol](https://www.amqp.org/) op poort 5671. Een IoT-hub legt de volgende eigenschappen bloot waarmee u de ingebouwde gebeurtenishubcompatibele **eindpuntberichten/gebeurtenissen beheren.**

| Eigenschap            | Beschrijving |
| ------------------- | ----------- |
| **Aantal partities** | Stel deze eigenschap in bij het maken om het aantal [partities](../event-hubs/event-hubs-features.md#partitions) voor inname van gebeurtenisvan apparaat tot cloud te definiëren. |
| **Bewaartijd**  | Deze eigenschap geeft aan hoe lang berichten in dagen worden bewaard door IoT Hub. De standaardinstelling is één dag, maar kan worden verhoogd tot zeven dagen. |

IoT Hub maakt het mogelijk om gegevens maximaal 7 dagen te bewaren in de ingebouwde Event Hubs. U de bewaartijd instellen tijdens het maken van uw IoT Hub. De bewaartijd van gegevens in IoT Hub is afhankelijk van uw IoT-hublaag en eenheidstype. In termen van grootte kunnen de ingebouwde gebeurtenishubs berichten van de maximale berichtgrootte tot ten minste 24 uur quotum behouden. Voor IoT Hub van 1 S1-eenheid biedt bijvoorbeeld voldoende opslagruimte om elk ten minste 400K-berichten van elk 4k-formaat te behouden. Als uw apparaten kleinere berichten verzenden, kunnen ze langer (tot 7 dagen) worden bewaard, afhankelijk van hoeveel opslagruimte wordt verbruikt. Wij garanderen minimaal het bewaren van de gegevens voor de opgegeven bewaartijd. Berichten verlopen en zijn niet toegankelijk nadat de bewaartijd is verstreken. 

Met IoT Hub u ook consumentengroepen beheren op het ingebouwde device-to-cloud-eindpunt. U maximaal 20 consumentengroepen hebben voor elke IoT-hub.

Als u [berichtroutering](iot-hub-devguide-messages-d2c.md) gebruikt en de [teruglooproute](iot-hub-devguide-messages-d2c.md#fallback-route) is ingeschakeld, gaan alle berichten die niet overeenkomen met een query op een route naar het ingebouwde eindpunt. Als u deze terugvalroute uitschakelt, worden berichten die niet overeenkomen met een query verwijderd.

U de bewaartijd wijzigen, hetzij programmatisch met behulp van de REST API's van de [IoT Hub-bronprovider](/rest/api/iothub/iothubresource)of met de [Azure-portal](https://portal.azure.com).

IoT Hub onthult het ingebouwde **eindpunt voor berichten/gebeurtenissen** voor uw back-endservices om de apparaat-naar-cloudberichten te lezen die door uw hub zijn ontvangen. Dit eindpunt is Event Hub-compatibel, waarmee u een van de mechanismen gebruiken die de Event Hubs-service ondersteunt voor het lezen van berichten.

## <a name="read-from-the-built-in-endpoint"></a>Lezen vanaf het ingebouwde eindpunt

Sommige productintegraties en Event Hubs SDK's zijn zich bewust van IoT Hub en laten u uw IoT-hubserviceverbindingstekenreeks gebruiken om verbinding te maken met het ingebouwde eindpunt.

Wanneer u Event Hubs SDKs of productintegraties gebruikt die niet op de hoogte zijn van IoT Hub, hebt u een Event Hub-compatibel eindpunt en eventhubcompatibele naam nodig. U deze waarden als volgt uit de portal halen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.

2. Klik **op Ingebouwde eindpunten**.

3. De sectie **Gebeurtenissen** bevat de volgende waarden: **Partities**, **Gebeurtenishub-compatibele naam,** **Event Hub-compatibel eindpunt,** **Retentietijd**en **Consumentengroepen**.

    ![Instellingen voor apparaat-naar-cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

In de portal bevat het domeinpuntveld dat compatibel is met eventhubs een volledige verbindingstekenreeks voor gebeurtenishubs die eruitziet als: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/; SharedAccessKeyName=iothubowner; SharedAccessKey=keykeykeykeykeykey=; EntityPath=iothub-ehub-abcd-1234-123456**. Als de SDK die u gebruikt andere waarden vereist, dan zijn ze:

| Name | Waarde |
| ---- | ----- |
| Eindpunt | sb://abcd1234namespace.servicebus.windows.net/ |
| Hostnaam | abcd1234namespace.servicebus.windows.net |
| Naamruimte | abcd1234namespace |

U vervolgens elk gedeeld toegangsbeleid gebruiken met de **ServiceConnect-machtigingen** om verbinding te maken met de opgegeven gebeurtenishub.

De SDK's die u gebruiken om verbinding te maken met het ingebouwde Event Hub-compatibele eindpunt dat IoT Hub blootlegt, zijn:

| Taal | SDK | Voorbeeld | Opmerkingen |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Snelstartgids](quickstart-send-telemetry-dotnet.md) | Gebruikt informatie die compatibel is met gebeurtenishubs |
 Java | https://github.com/Azure/azure-event-hubs-java | [Snelstartgids](quickstart-send-telemetry-java.md) | Gebruikt informatie die compatibel is met gebeurtenishubs |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Snelstartgids](quickstart-send-telemetry-node.md) | Maakt gebruik van de verbindingstekenreeks van IoT-hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Maakt gebruik van de verbindingstekenreeks van IoT-hub |

De productintegraties die u gebruiken met het ingebouwde Event Hub-compatibele eindpunt dat IoT Hub blootlegt, zijn:

* [Azure-functies](https://docs.microsoft.com/azure/azure-functions/). Zie [Gegevens verwerken vanuit IoT Hub met Azure-functies](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Zie [Gegevens streamen als invoer in Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Inzichten](https://docs.microsoft.com/azure/time-series-insights/). Zie [Een IoT-hubgebeurtenisbron toevoegen aan uw Time Series Insights-omgeving](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm tuit](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). U de [uitloopbron](https://github.com/apache/storm/tree/master/external/storm-eventhubs) bekijken op GitHub.
* [Apache Spark integratie](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Volgende stappen

* Zie [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md)voor meer informatie over eindpunten van IoT Hub.

* De [Quickstarts](quickstart-send-telemetry-node.md) laten u zien hoe u apparaat-naar-cloudberichten verzendt vanaf gesimuleerde apparaten en de berichten van het ingebouwde eindpunt lezen. 

Zie de handleiding [voor IoT Hub device-to-cloud van IoT Hub met routes.](tutorial-routing.md)

* Zie [Berichtroutes en aangepaste eindpunten gebruiken voor device-to-cloud-berichten](iot-hub-devguide-messages-read-custom.md)als u uw apparaat-naar-cloudberichten wilt doorsturen naar aangepaste eindpunten.
