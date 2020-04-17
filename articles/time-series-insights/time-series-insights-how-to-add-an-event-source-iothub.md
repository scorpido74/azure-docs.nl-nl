---
title: Een IoT-hubgebeurtenisbron toevoegen - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het toevoegen van een IoT-hub-gebeurtenisbron aan uw Time Series Insights-omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: a0a2f703d9224b8b9dd77c80b2b6a7faee70f5bb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538100"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Een IoT-hubgebeurtenisbron toevoegen aan uw Time Series Insights-omgeving

In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om een gebeurtenisbron toe te voegen die gegevens uit Azure IoT Hub leest aan uw Azure Time Series Insights-omgeving.

> [!NOTE]
> De instructies in dit artikel zijn van toepassing op zowel Azure Time Series Insights GA als op Time Series Insights Preview-omgevingen.

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure Time Series Insights-omgeving](time-series-insights-update-create-environment.md).
* Maak een [IoT-hub met behulp van de Azure-portal.](../iot-hub/iot-hub-create-through-portal.md)
* De IoT-hub moet actieve berichtgebeurtenissen hebben die worden verzonden.
* Maak een speciale consumentengroep in de IoT-hub voor de Time Series Insights-omgeving om uit te consumeren. Elke Time Series Insights-gebeurtenisbron moet een eigen speciale consumentengroep hebben die niet wordt gedeeld met andere consumenten. Als meerdere lezers gebeurtenissen uit dezelfde consumentengroep consumeren, zullen alle lezers waarschijnlijk fouten vertonen. Lees voor meer informatie de [azure IoT Hub-ontwikkelaarshandleiding](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Een consumentengroep toevoegen aan uw IoT-hub

Toepassingen gebruiken consumentengroepen om gegevens uit Azure IoT Hub te halen. Als u gegevens van uw IoT-hub betrouwbaar wilt lezen, biedt u een speciale consumentengroep die alleen wordt gebruikt door deze Time Series Insights-omgeving.

Ga als volgende over een nieuwe consumentengroep naar uw IoT-hub:

1. Zoek en open uw IoT-hub in de [Azure-portal.](https://portal.azure.com)

1. Selecteer **onder Instellingen** **ingebouwde eindpunten**en selecteer vervolgens het eindpunt **Gebeurtenissen.**

   [![Selecteer op de pagina Ingebouwde eindpunten de knop Gebeurtenissen](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. Voer **onder Consumentengroepen**een unieke naam in voor de consumentengroep. Gebruik dezelfde naam in uw Time Series Insights-omgeving wanneer u een nieuwe gebeurtenisbron maakt.

1. Selecteer **Opslaan**.

## <a name="add-a-new-event-source"></a>Een nieuwe gebeurtenisbron toevoegen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle resources** in het menu aan de linkerkant. Selecteer uw Time Series Insights-omgeving.

1. Selecteer **onder Instellingen** **gebeurtenisbronnen**en selecteer **Vervolgens Toevoegen**.

   [![Selecteer Gebeurtenisbronnen en selecteer de knop Toevoegen](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. Voer in het deelvenster **Nieuwe gebeurtenisbron** voor **de naam van de gebeurtenisbron**een naam in die uniek is voor deze time-serie Insights-omgeving. Voer bijvoorbeeld **eventstream**in .

1. Selecteer **IoT Hub**voor **Bron**.

1. Selecteer een waarde voor **import, optie:**

   * Als u al een IoT-hub in een van uw abonnementen hebt, selecteert u **IoT Hub gebruiken via beschikbare abonnementen**. Deze optie is de makkelijkste aanpak.
   
     [![Opties selecteren in het deelvenster Nieuwe gebeurtenisbron](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * In de volgende tabel worden de eigenschappen beschreven die nodig zijn voor de optie **IoT-hub gebruiken vanuit de optie Beschikbare abonnementen:**

       [![Nieuw deelvenster gebeurtenisbron - Eigenschappen die u wilt instellen in de optie IoT-hub gebruiken vanuit de optie Beschikbare abonnementen](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnement | Het abonnement waar de gewenste iot hub toe behoort. |
       | Naam van iot-hub | De naam van de geselecteerde iot-hub. |
       | Naam iot-hubbeleid | Selecteer het beleid voor gedeelde toegang. U vindt het beleid voor gedeelde toegang op het tabblad Instellingen voor IoT-hub. Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. Het beleid voor gedeelde toegang voor uw gebeurtenisbron *moet* **serviceverbindingsmachtigingen** hebben. |
       | IoT-hubbeleidssleutel | De sleutel is vooraf ingevuld. |

    * Als de IoT-hub zich buiten uw abonnementen bevindt of als u geavanceerde opties wilt kiezen, selecteert u **Handmatig Instellingen voor IoT-hub opgeven.**

      In de volgende tabel worden de vereiste eigenschappen voor de **instellingen voor IoT-hub handmatig beschreven:**

       | Eigenschap | Beschrijving |
       | --- | --- |
       | Abonnements-id | Het abonnement waar de gewenste iot hub toe behoort. |
       | Resourcegroep | De naam van de resourcegroep waarin de IoT-hub is gemaakt. |
       | Naam van iot-hub | De naam van uw IoT-hub. Toen u uw IoT-hub hebt gemaakt, hebt u een naam voor de IoT-hub ingevoerd. |
       | Naam iot-hubbeleid | Het beleid voor gedeelde toegang. U het beleid voor gedeelde toegang maken op het tabblad Instellingen voor IoT-hub. Elk beleid voor gedeelde toegang heeft een naam, machtigingen die u instelt en toegangssleutels. Het beleid voor gedeelde toegang voor uw gebeurtenisbron *moet* **serviceverbindingsmachtigingen** hebben. |
       | IoT-hubbeleidssleutel | De gedeelde toegangssleutel die wordt gebruikt om de toegang tot de naamruimte van azure servicebus te verifiëren. Voer hier de primaire of secundaire sleutel in. |

    * Beide opties delen de volgende configuratieopties:

       | Eigenschap | Beschrijving |
       | --- | --- |
       | IoT hub consumentengroep | De consumentengroep die gebeurtenissen leest vanuit de IoT-hub. We raden u ten zeerste aan een speciale consumentengroep te gebruiken voor uw gebeurtenisbron. |
       | Serialisatie-indeling voor gebeurtenissen | Momenteel is JSON de enige beschikbare serialisatie-indeling. De gebeurtenisberichten moeten in deze indeling zijn of er kunnen geen gegevens worden gelezen. |
       | Naam van de timestamp-eigenschap | Als u deze waarde wilt bepalen, moet u inzicht krijgen in de berichtindeling van de berichtgegevens die naar de IoT-hub worden verzonden. Deze waarde is de **naam** van de specifieke gebeurteniseigenschap in de berichtgegevens die u als gebeurtenistijdstempel wilt gebruiken. De waarde is hoofdlettergevoelig. Als deze niet leeg is gelaten, wordt de **gebeurteniswachtrijtijd** in de gebeurtenisbron gebruikt als gebeurtenistijdstempel. |


1. Voeg de speciale Time Series Insights-consumentengroepnaam toe die u aan uw IoT-hub hebt toegevoegd.

1. Selecteer **Maken**.

1. Nadat u de gebeurtenisbron hebt gemaakt, wordt Time Series Insights automatisch gestart met het streamen van gegevens naar uw omgeving.

## <a name="next-steps"></a>Volgende stappen

* [Definieer beleid voor gegevenstoegang](time-series-insights-data-access.md) om de gegevens te beveiligen.

* [Gebeurtenissen naar](time-series-insights-send-events.md) de gebeurtenisbron verzenden.

* Toegang tot uw omgeving in de [Time Series Insights explorer.](https://insights.timeseries.azure.com)
