---
title: IoT Hub-connectiviteit beheren & betrouwbare messaging-w/device-SDK's
description: Meer informatie over het verbeteren van de connectiviteit en berichten van uw apparaat wanneer u de SDK's van het Azure IoT Hub-apparaat gebruikt
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 1ca7219824a00a5af0bed7d42da75fc06ce2010d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284445"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Connectiviteit en betrouwbare berichten beheren met Behulp van Azure IoT Hub-apparaat SDKs

Dit artikel biedt richtlijnen op hoog niveau om u te helpen apparaattoepassingen te ontwerpen die veerkrachtiger zijn. Het laat u zien hoe u profiteren van de connectiviteit en betrouwbare berichtenfuncties in Azure IoT-apparaat-SDK's. Het doel van deze handleiding is om u te helpen bij het beheren van de volgende scenario's:

* Een verbroken netwerkverbinding herstellen

* Schakelen tussen verschillende netwerkverbindingen

* Opnieuw verbinding maken vanwege tijdelijke verbindingsfouten van de service

Implementatiegegevens kunnen per taal verschillen. Zie de API-documentatie of specifieke SDK voor meer informatie:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (Betrouwbaarheid nog niet geïmplementeerd)

## <a name="designing-for-resiliency"></a>Ontwerpen voor flexibiliteit

IoT-apparaten zijn vaak afhankelijk van niet-continue of onstabiele netwerkverbindingen (bijvoorbeeld GSM of satelliet). Er kunnen fouten optreden wanneer apparaten communiceren met cloudservices vanwege de beschikbaarheid van de service en infrastructuur- of tijdelijke fouten. Een toepassing die op een apparaat wordt uitgevoerd, moet de mechanismen voor verbinding, opnieuw verbinding en de logica opnieuw proberen voor het verzenden en ontvangen van berichten beheren. Ook zijn de vereisten voor de strategie voor nieuwe apparaten sterk afhankelijk van het IoT-scenario, de context en de mogelijkheden van het apparaat.

De Azure IoT Hub-apparaat SDK's is bedoeld om het verbinden en communiceren van cloud-to-device en device-to-cloud te vereenvoudigen. Deze SDK's bieden een robuuste manier om verbinding te maken met Azure IoT Hub en een uitgebreide set opties voor het verzenden en ontvangen van berichten. Ontwikkelaars kunnen ook bestaande implementatieaanpassen om een betere strategie voor een bepaald scenario aan te passen.

De relevante SDK-functies die connectiviteit en betrouwbare berichten ondersteunen, worden in de volgende secties behandeld.

## <a name="connection-and-retry"></a>Verbinding en opnieuw proberen

In deze sectie vindt u een overzicht van de re-connection- en retry-patronen die beschikbaar zijn bij het beheren van verbindingen. Het bevat implementatierichtlijnen voor het gebruik van een ander beleid voor opnieuw proberen in uw apparaattoepassing en bevat relevante API's van de sdks van het apparaat.

### <a name="error-patterns"></a>Foutpatronen

Verbindingsfouten kunnen op veel niveaus optreden:

* Netwerkfouten: fouten bij losgekoppelde socket en naamomzetting

* Fouten op protocolniveau voor HTTP-, AMQP- en MQTT-transport: koppelingen of verlopen sessies

* Fouten op toepassingsniveau die het gevolg zijn van lokale fouten: ongeldige referenties of servicegedrag (bijvoorbeeld het overschrijden van het quotum of beperking)

De sdk's van het apparaat detecteren fouten op alle drie de niveaus. OS-gerelateerde fouten en hardwarefouten worden niet gedetecteerd en verwerkt door de sdks van het apparaat. Het SDK-ontwerp is gebaseerd op [de tijdelijke foutafhandelingsrichtlijnen](/azure/architecture/best-practices/transient-faults#general-guidelines) van het Azure Architecture Center.

### <a name="retry-patterns"></a>Patronen opnieuw proberen

In de volgende stappen wordt het proces voor het opnieuw proberen beschreven wanneer verbindingsfouten worden gedetecteerd:

1. De SDK detecteert de fout en de bijbehorende fout in het netwerk, protocol of toepassing.

2. De SDK gebruikt het foutfilter om het fouttype te bepalen en te beslissen of een nieuwe poging nodig is.

3. Als de SDK een **onherstelbare fout**identificeert, worden bewerkingen zoals verbinding, verzenden en ontvangen gestopt. De SDK waarschuwt de gebruiker. Voorbeelden van onherstelbare fouten zijn een verificatiefout en een fout in het eindpunt.

4. Als de SDK een **herstelbare fout**identificeert, wordt deze opnieuw geprobeerd volgens het opgegeven beleid voor nieuwe pogingen totdat de gedefinieerde time-out verstrijkt.  Houd er rekening mee dat de SDK **standaard exponentiële back-off** gebruikt met jitter-beleid opnieuw proberen.
5. Wanneer de gedefinieerde time-out verloopt, stopt de SDK met het proberen verbinding te maken of te verzenden. Het waarschuwt de gebruiker.

6. Met de SDK kan de gebruiker een callback koppelen om wijzigingen in de verbindingsstatus te ontvangen.

De SDK's bieden drie beleid voor nieuwe technieken:

* **Exponentiële back-off met jitter:** Deze standaard retry beleid heeft de neiging om agressief te zijn bij het begin en vertragen na verloop van tijd totdat het een maximale vertraging bereikt. Het ontwerp is gebaseerd op [richtlijnen voor opnieuw proberen van Azure Architecture Center.](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific) 

* **Aangepaste poging:** voor sommige SDK-talen u een aangepast beleid voor nieuwe poging ontwerpen dat beter geschikt is voor uw scenario en het vervolgens injecteren in het retrybeleid. Aangepaste nieuwe poging is niet beschikbaar op de C SDK en wordt momenteel niet ondersteund op de Python SDK. De Python SDK maakt indien nodig opnieuw verbinding.

* **Geen nieuwe poging:** U het beleid opnieuw proberen instellen op 'geen nieuwe poging', waardoor de logica voor opnieuw proberen wordt uitgeschakeld. De SDK probeert één keer verbinding te maken en een bericht te verzenden, ervan uitgaande dat de verbinding tot stand is gebracht. Dit beleid wordt meestal gebruikt in scenario's met bandbreedte- of kostenproblemen. Als u deze optie kiest, gaan berichten die niet worden verzonden, verloren en kunnen ze niet worden hersteld.

### <a name="retry-policy-apis"></a>BeleidsAPI's opnieuw proberen

   | SDK | SetRetryPolicy, methode | Beleidsuitvoeringen | Uitvoeringsrichtsnoeren |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Standaard**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Aangepast:** gebruik beschikbare [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Geen nieuwe poging:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/iOS-implementatie](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Standaard:** [de klasse ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Aangepast:** [interface RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java) implementeren<BR>**Geen nieuwe poging:** [NoRetry klasse](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-implementatie](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Standaard:** [exponentieel back-offklasse](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Aangepast:** [iRetryPolicy-interface](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet) implementeren<BR>**Geen nieuwe poging:** [NoRetry klasse](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# implementatie](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Knooppunt| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Standaard:** [de klasse ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Aangepast:** [interface RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest) implementeren<BR>**Geen nieuwe poging:** [NoRetry klasse](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Node implementatie](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Momenteel niet ondersteund | Momenteel niet ondersteund | Momenteel niet ondersteund |

De volgende codevoorbeelden illustreren deze stroom:

#### <a name="net-implementation-guidance"></a>Richtlijnen voor de implementatie van .NET

In het volgende voorbeeld van de code ziet u hoe u het standaardbeleid voor nieuwe try's definieert en instelt:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Om een hoog CPU-gebruik te voorkomen, worden de pogingen beperkt als de code onmiddellijk mislukt. Bijvoorbeeld als er geen netwerk of route naar de bestemming is. De minimale tijd om de volgende poging uit te voeren is 1 seconde.

Als de service reageert met een beperkingsfout, is het beleid voor opnieuw proberen anders en kan het niet worden gewijzigd via openbare API:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Het mechanisme voor `DefaultOperationTimeoutInMilliseconds`het opnieuw proberen stopt na , die momenteel is ingesteld op 4 minuten.

#### <a name="other-languages-implementation-guidance"></a>Richtlijnen voor de implementatie van andere talen

Voor codevoorbeelden in andere talen controleert u de volgende implementatiedocumenten. De repository bevat voorbeelden die het gebruik van retry policy API's aantonen.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>Volgende stappen

* [Apparaat- en service-SDK's gebruiken](./iot-hub-devguide-sdks.md)

* [De IoT-apparaat-SDK voor C gebruiken](./iot-hub-device-sdk-c-intro.md)

* [Ontwikkelen voor beperkte apparaten](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Ontwikkelen voor mobiele apparaten](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Problemen met apparaatverbindingen oplossen](iot-hub-troubleshoot-connectivity.md)
