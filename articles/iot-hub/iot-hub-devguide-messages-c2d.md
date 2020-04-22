---
title: Informatie over berichten over Azure IoT Hub-cloud naar apparaat | Microsoft Documenten
description: In deze handleiding voor ontwikkelaars wordt besproken hoe u berichten over cloud-to-device gebruiken met uw IoT-hub. Het bevat informatie over de levenscyclus van het bericht en configuratie-opties.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt
ms.openlocfilehash: 307ab47c1f7498f71e61108a616d35ef1d4f61c9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729999"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Cloud-naar-apparaatberichten verzenden vanaf een IoT-hub

Als u eenmalige meldingen naar een apparaat-app wilt verzenden vanaf de back-end van uw oplossing, stuurt u berichten van cloud naar apparaat van uw IoT-hub naar uw apparaat. Zie Communicatierichtlijnen voor [cloud-to-device](iot-hub-devguide-c2d-guidance.md)voor een bespreking van andere cloud-to-device-opties die worden ondersteund door Azure IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

U verzendt berichten van cloud naar apparaat via een servicegericht eindpunt, */berichten/apparaatgebonden.* Een apparaat ontvangt de berichten vervolgens via een apparaatspecifiek eindpunt, */apparaten/{deviceId}/berichten/devicebound.*

Als u elk cloud-to-device-bericht op één apparaat wilt targeten, stelt uw IoT-hub de eigenschap **aan** */apparaten/{deviceId}/berichten/apparaatgebonden*in.

Elke apparaatwachtrij bevat maximaal 50 cloud-to-device-berichten. Als u wilt proberen meer berichten naar hetzelfde apparaat te verzenden, resulteert dit in een fout.

## <a name="the-cloud-to-device-message-life-cycle"></a>De levenscyclus van de cloud-to-device-berichten

Om ten minste één keer berichtte bezorging te garanderen, blijft uw IoT-hub cloud-to-device-berichten in wachtrijen per apparaat vasthouden. Als de IoT-hub de berichten uit de wachtrij wil verwijderen, moeten de apparaten de *voltooiing*expliciet erkennen. Deze aanpak garandeert tolerantie tegen connectiviteit en apparaatstoringen.

De grafiek met de status van de levenscyclus wordt weergegeven in het volgende diagram:

![Levenscyclus van berichten van cloud naar apparaat](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Wanneer de IoT-hubservice een bericht naar een apparaat verzendt, stelt de service de berichtstatus in op *Enqueued*. Wanneer een apparaat een bericht wil *ontvangen,* *vergrendelt* de IoT-hub het bericht door de status in te stellen op *Onzichtbaar*. Met deze status kunnen andere threads op het apparaat andere berichten ontvangen. Wanneer een apparaatthread de verwerking van een bericht voltooit, wordt de IoT-hub hiervan op de hoogte gesteld door het bericht *in te vullen.* De IoT-hub stelt de status vervolgens *in op Voltooid*.

Een apparaat kan ook:

* Het bericht *weigeren,* waardoor de IoT-hub het instelt op de *status Met een dode letter.* Apparaten die verbinding maken via het Message Queuing Telemetry Transport (MQTT)-protocol, kunnen berichten van cloud tot apparaat niet weigeren.

* *Verlaat* het bericht, waardoor de IoT-hub het bericht weer in de wachtrij plaatst, waarbij de status is ingesteld op *Enqueued*. Apparaten die verbinding maken via het MQTT-protocol, kunnen berichten van cloud tot apparaat niet opgeven.

Een thread kan een bericht niet verwerken zonder de IoT-hub hiervan op de hoogte te stellen. In dit geval worden berichten automatisch overgezet van de status *Onzichtbaar* terug naar de status *Enqueued* na een time-out van *het zicht* (of time-out *vergrendelen).* De waarde van deze time-out is één minuut en kan niet worden gewijzigd.

De **eigenschap maximumlevering op** de IoT-hub bepaalt het maximum aantal keren dat een bericht kan worden overgezet tussen de status *Squeued* en *Onzichtbaar.* Na dat aantal overgangen stelt de IoT-hub de status van het bericht in op *Dode letters*. Op dezelfde manier stelt de IoT-hub de status van een bericht in *op Dead lettered* na de vervaldatum. Zie [Tijd om te leven voor](#message-expiration-time-to-live)meer informatie.

In het artikel [Hoe u cloud-naar-device-berichten verzendt met het IoT Hub-artikel,](iot-hub-csharp-csharp-c2d.md) ziet u hoe u cloud-to-device-berichten vanuit de cloud verzenden en op een apparaat ontvangen.

Een apparaat voltooit gewoonlijk een cloud-to-device-bericht wanneer het verlies van het bericht geen invloed heeft op de toepassingslogica. Een voorbeeld hiervan is wanneer het apparaat de inhoud van het bericht lokaal heeft gehandhaafd of een bewerking heeft uitgevoerd. Het bericht kan ook tijdelijke informatie bevatten, waarvan het verlies geen invloed zou hebben op de functionaliteit van de toepassing. Soms u voor langlopende taken:

* Voer het bericht van cloud-to-device in nadat het apparaat de taakbeschrijving in lokale opslag heeft gehandhaafd.

* Stel de oplossing back-end met een of meer device-to-cloud berichten in verschillende stadia van de voortgang van de taak.

## <a name="message-expiration-time-to-live"></a>Bericht verlopen (tijd om te leven)

Elk cloud-to-device bericht heeft een vervaldatum. Deze tijd wordt ingesteld door een van de volgende:

* De eigenschap **ExpiryTimeUtc** in de service
* De IoT-hub, door de *standaardtijd te* gebruiken om te leven die is opgegeven als een IoT-hubeigenschap

Zie [configuratieopties voor cloud-naar-apparaat](#cloud-to-device-configuration-options).

Een veelvoorkomende manier om te profiteren van het verlopen van een bericht en om te voorkomen dat berichten naar losgekoppelde apparaten worden verzonden, is door korte tijd in te stellen *op levende* waarden. Deze aanpak bereikt hetzelfde resultaat als het onderhouden van de verbinding van het apparaat, maar het is efficiënter. Wanneer u berichtbevestigingen aanvraagt, waarschuwt de IoT-hub u welke apparaten:

* In staat om berichten te ontvangen.
* Zijn niet online of hebben gefaald.

## <a name="message-feedback"></a>Feedback van berichten

Wanneer u een cloud-naar-apparaatbericht verzendt, kan de service de levering van feedback per bericht over de uiteindelijke status van dat bericht aanvragen. U doet dit door de eigenschap **iothub-ack-toepassing** in te stellen in het cloud-to-device-bericht dat naar een van de volgende vier waarden wordt verzonden:

| Waarde van ack-onroerend goed | Gedrag |
| ------------ | -------- |
| geen     | De IoT-hub genereert geen feedbackbericht (standaardgedrag). |
| positief | Als het cloud-to-device-bericht de *status Voltooid* bereikt, genereert de IoT-hub een feedbackbericht. |
| negatief | Als het cloud-to-device-bericht de status *Met hoofdletters* heeft bereikt, genereert de IoT-hub een feedbackbericht. |
| Volledige     | De IoT-hub genereert in beide gevallen een feedbackbericht. |

Als de **Ack-waarde** *vol*is en u geen feedbackbericht ontvangt, betekent dit dat het feedbackbericht is verlopen. De service kan niet weten wat er met het oorspronkelijke bericht is gebeurd. In de praktijk moet een service ervoor zorgen dat deze de feedback kan verwerken voordat deze verloopt. De maximale vervaldatum is twee dagen, waardoor de tijd overblijft om de service weer te laten draaien als er een storing optreedt.

Zoals uitgelegd in [Endpoints,](iot-hub-devguide-endpoints.md)levert de IoT-hub feedback via een servicegericht eindpunt, */berichten/servicebound/feedback,* als berichten. De semantiek voor het ontvangen van feedback is dezelfde als voor berichten van cloud naar apparaat. Waar mogelijk wordt berichtfeedback in één bericht weergegeven, met de volgende indeling:

| Eigenschap     | Beschrijving |
| ------------ | ----------- |
| Indewachtrijtijd | Een tijdstempel die aangeeft wanneer het feedbackbericht door de hub is ontvangen |
| UserId       | `{iot hub name}` |
| Contenttype  | `application/vnd.microsoft.iothub.feedback.json` |

De body is een JSON-serie records, elk met de volgende eigenschappen:

| Eigenschap           | Beschrijving |
| ------------------ | ----------- |
| InqueuedTimeUtc    | Een tijdstempel die aangeeft wanneer de uitkomst van het bericht is gebeurd (bijvoorbeeld de hub heeft het feedbackbericht ontvangen of het oorspronkelijke bericht is verlopen) |
| OriginalMessageId  | De *MessageId* van het cloud-to-device bericht waarop deze feedback-informatie betrekking heeft |
| Statuscode         | Een vereiste tekenreeks die wordt gebruikt in feedbackberichten die worden gegenereerd door de IoT-hub: <br/> *Geslaagd* <br/> *Verlopen* <br/> *DeliveryCountExceeded* <br/> *Geweigerd* <br/> *Verwijderd* |
| Beschrijving        | Tekenreekswaarden voor *StatusCode* |
| DeviceId           | De *DeviceId* van het doelapparaat van het cloud-to-device-bericht waarop dit stukje feedback betrekking heeft |
| DeviceGenerationId | De *DeviceGenerationId* van het doelapparaat van het cloud-to-device-bericht waarop dit stukje feedback betrekking heeft |

Als het cloud-to-device-bericht de feedback correleert met het oorspronkelijke bericht, moet de service een *MessageId*opgeven.

De hoofdtekst van een feedbackbericht wordt weergegeven in de volgende code:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**In behandeling zijnde feedback voor verwijderde apparaten**

Wanneer een apparaat wordt verwijderd, wordt ook alle in behandeling zijnde feedback verwijderd. Apparaatfeedback wordt in batches verzonden. Als een apparaat wordt verwijderd in het smalle venster (vaak minder dan 1 seconde) tussen het moment waarop het apparaat de ontvangst van het bericht bevestigt en wanneer de volgende feedbackbatch wordt voorbereid, vindt de feedback niet plaats.

U dit gedrag aanpakken door te wachten op een periode van in behandeling zijnde feedback voordat u uw apparaat verwijderd. Gerelateerde berichtfeedback moet verloren gaan zodra een apparaat is verwijderd.

## <a name="cloud-to-device-configuration-options"></a>Configuratieopties voor cloud-naar-apparaat

Elke IoT-hub legt de volgende configuratieopties bloot voor berichten over cloud-to-device:

| Eigenschap                  | Beschrijving | Bereik en standaard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standaard TTL voor cloud-to-device-berichten | ISO_8601 interval tot 2 dagen (minimaal 1 minuut); standaard: 1 uur |
| maxDeliveryCount maxDeliveryCount maxDeliveryCount maxDelivery          | Maximaal aantal levering voor wachtrijen per apparaat per apparaat | 1 tot 100; standaard: 10 |
| feedback.ttlAsIso8601     | Behoud voor servicegebonden feedbackberichten | ISO_8601 interval tot 2 dagen (minimaal 1 minuut); standaard: 1 uur |
| feedback.maxDeliveryCount | Maximaal aantal levering voor de feedbackwachtrij | 1 tot 100; standaard: 10 |
| feedback.lockDurationAsIso8601 | Maximaal aantal levering voor de feedbackwachtrij | ISO_8601 interval van 5 tot 300 seconden (minimaal 5 seconden); standaard: 60 seconden. |

U de configuratieopties op een van de volgende manieren instellen:

* **Azure-portal:** Selecteer onder **Instellingen** op uw **IoT-hub ingebouwde eindpunten** en vouw Cloud uit **naar apparaatberichten.** (Het instellen van de eigenschappen **feedback.maxDeliveryCount** en **feedback.lockDurationAsIso8601** wordt momenteel niet ondersteund in azure-portal.)

    ![Configuratieopties instellen voor cloud-to-device berichten in de portal](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Gebruik de opdracht [az iot-hub-update:](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure IoT SDKs](iot-hub-devguide-sdks.md)voor informatie over de SDK's die u gebruiken om berichten van cloud naar apparaat te ontvangen.

Zie de zelfstudie Van [cloud-naar-apparaat verzenden](iot-hub-csharp-csharp-c2d.md) als u berichten van cloud naar apparaat wilt ontvangen.
