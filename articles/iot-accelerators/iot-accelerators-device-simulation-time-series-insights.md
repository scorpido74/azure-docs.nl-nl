---
title: Gesimuleerde telemetrie visualiseren met Time Series Insights - Azure | Microsoft Documenten
description: Meer informatie over het configureren van uw Time Series Insights-omgeving om telemetrie te verkennen en te analyseren die wordt gegenereerd door de apparaatsimulatieoplossingsversneller.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889333"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Gebruik Time Series Insights om telemetrie te visualiseren die wordt verzonden vanaf de device simulation oplossingsversneller

Met de device simulation-oplossingsversneller u telemetrie genereren van gesimuleerde apparaten om uw IoT-oplossingen te testen. Deze handleiding laat u zien hoe u de gesimuleerde telemetrie visualiseren en analyseren met behulp van een Time Series Insights-omgeving.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt volgen, hebt u een actief Azure-abonnement nodig. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

De stappen in deze handleiding gaan ervan uit dat u de accelerator voor apparaatsimulatieoplossingen hebt geïmplementeerd in uw Azure-abonnement. Als u de oplossingsversneller nog niet hebt geïmplementeerd, voert u de stappen in de [implementatie uit en voert u snel een cloudsimulatieoplossing uit.](quickstart-device-simulation-deploy.md)

Dit artikel gaat ervan uit dat de naam van uw oplossingsversneller **contoso-simulatie**is. Vervang **contoso-simulatie** door de naam van uw oplossingsversneller terwijl u de volgende stappen voltooit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Een consumentengroep maken

U moet een speciale consumentengroep maken in uw IoT-hub om telemetrie naar Time Series Insights te streamen. Een gebeurtenisbron in Time Series Insights moet exclusief gebruik maken van een IoT Hub-consumentengroep.

De volgende stappen gebruiken de Azure CLI in azure cloud shell om de consumentengroep te maken:

1. De IoT-hub is een van de vele bronnen die zijn gemaakt toen u de apparaatsimulatieoplossingsversneller hebt geïmplementeerd. Voer de volgende opdracht uit en zoek de naam van uw IoT-hub - vergeet niet de naam van uw oplossingsversneller te gebruiken:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    De IoT-hub is de bron van het type **Microsoft.Devices/IotHubs**.

1. Voeg een consumentengroep genaamd **devicesimulationtsi** toe aan de hub. Gebruik in de volgende opdracht de naam van uw hub en oplossingsversneller:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    U nu de Azure Cloud Shell sluiten.

## <a name="create-a-new-time-series-insights-environment"></a>Een nieuwe Time Series Insights-omgeving maken

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) is een volledig beheerde analyse-, opslag- en visualisatieservice voor het beheren van iot-scale tijdreeksgegevens in de cloud. Ga als lid van het nieuws naar een nieuwe Time Series Insights-omgeving:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer Inzicht in internet > of**things-timeseries****Internet of Things** >  **maken:**

    ![Inzichten in nieuwe tijdseries](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Als u uw Time Series Insights-omgeving wilt maken in dezelfde resourcegroep als uw oplossingsversneller, gebruikt u de waarden in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van omgeving | De volgende screenshot maakt gebruik van de naam **Contoso-TSI**. Kies uw eigen unieke naam wanneer u deze stap voltooit. |
    | Abonnement | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Resourcegroep | **contoso-simulatie**. Gebruik de naam van uw oplossingsversneller. |
    | Locatie | In dit voorbeeld wordt gebruik gebruikt **van Oost-VS**. Maak uw omgeving in dezelfde regio als uw apparaatsimulatieversneller. |
    | Sku |**S1** |
    | Capaciteit | **1** |

    ![Inzichten in de tijdreeks maken](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Als u de time series Insights-omgeving toevoegt aan dezelfde resourcegroep als de oplossingsversneller, wordt deze verwijderd wanneer u de oplossingsversneller verwijdert.

1. Klik **op Maken**. Het kan enkele minuten duren voordat de omgeving is gemaakt.

## <a name="create-event-source"></a>Gebeurtenisbron maken

Maak een nieuwe gebeurtenisbron om verbinding te maken met uw IoT-hub. Gebruik de consumentengroep die u in de vorige stappen hebt gemaakt. Een gebeurtenisbron van Time Series Insights vereist een speciale consumentengroep die niet door een andere service wordt gebruikt.

1. Navigeer in de Azure-portal naar uw nieuwe omgeving voor tijdreeksen.

1. Klik aan de linkerkant op **Gebeurtenisbronnen:**

    ![Gebeurtenisbronnen weergeven](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klik **op Toevoegen:**

    ![Gebeurtenisbron toevoegen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Als u uw IoT-hub wilt configureren als een nieuwe gebeurtenisbron, gebruikt u de waarden in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van gebeurtenisbron | De volgende screenshot maakt gebruik van de naam **contoso-iot-hub**. Gebruik je eigen unieke naam wanneer je deze stap voltooit. |
    | Bron | **IoT-hub** |
    | Importoptie | **IoT Hub gebruiken via beschikbare abonnementen** |
    | Abonnements-id | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Iot-hubnaam | **contoso-simulatie7d894**. Gebruik de naam van uw IoT-hub van uw device simulation-oplossingsversneller. |
    | Naam van het IoT-hub-beleid | **iothubowner** |
    | Iot hub beleidssleutel | Dit veld wordt automatisch ingevuld. |
    | IoT-hub-consumentengroep | **devicesimulationtsi** |
    | Serialisatie-indeling voor gebeurtenissen | **Json** |
    | Naam van de timestamp-eigenschap | Leeg laten |

    ![Gebeurtenisbron maken](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klik **op Maken**.

> [!NOTE]
> U [extra gebruikers toegang verlenen tot](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) de Time Series Insights-verkenner.

## <a name="start-a-simulation"></a>Een simulatie starten

Voordat u Time Series Insights explorer gebruikt, configureert u de apparaatsimulatieoplossingsversneller om bepaalde telemetrie te genereren. De volgende screenshot, toont een lopende simulatie met 10 koeler apparaten:

![Apparaatsimulatie uitvoeren](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Verkenner van Time Series Insights

De Time Series Insights explorer is een web-app die u gebruiken om uw telemetrie te visualiseren.

1. Selecteer in de Azure-portal het tabblad **Overzicht** van time-series.

1. Als u de web-app Van Time Series Insights explorer wilt openen, klikt u op **Ga naar omgeving:**

    ![Verkenner van Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Selecteer In het tijdselectiepaneel **Laatste 30 minuten** in het menu Snelle tijden en klik op **Zoeken:**

    ![Zoeken naar inzichten in de time-serie](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Selecteer in het deelvenster Termen aan de linkerkant **de temperatuur** als **meting** en **iothub-verbinding-apparaat-id** als de **waarde Splitsen door:**

    ![Query van Time Series Insights explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Klik met de rechtermuisknop op de grafiek en selecteer **Gebeurtenissen verkennen:**

    ![Time Series Insights explorer-gebeurtenissen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. De gebeurtenisgegevens worden weergegeven in een raster:

    ![Explorer-tabel met Inzichten in de tijdreeks](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klik op de knop perspectiefweergave:

    ![Perspectief van Time Series Insights explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klik **+** hier om een nieuwe query toe te voegen aan het perspectief:

    ![Time Series Insights explorer Query toevoegen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecteer **Laatste 30 minuten** als de tijdspanne, **Vochtigheid** als **meting**en **iothub-connection-device-id** als de **waarde Gesplitst door:**

    ![Query van Time Series Insights explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Klik op de knop perspectiefweergave om het telemetriedashboard van uw apparaat weer te geven:

    ![Dashboard van Time Series Insights explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te verkennen, laat de oplossing versneller ingezet.

Als u de oplossingsversneller niet meer nodig hebt, verwijdert u deze van de pagina [Ingerichte oplossingen](https://www.azureiotsolutions.com/Accelerators#dashboard) door deze te selecteren en vervolgens op **Oplossing verwijderen**.

Als u de Time Series Insights-omgeving hebt toegevoegd aan de resourcegroep van de oplossingsversneller, wordt deze automatisch verwijderd wanneer u de oplossingsversneller verwijdert. Anders moet u de Time Series Insights-omgeving handmatig verwijderen uit de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

Zie Azure Time Series Insights explorer voor meer informatie over het verkennen en opvragen van gegevens in de Verkenner van Time Series [Insights.](../time-series-insights/time-series-insights-explorer.md)
