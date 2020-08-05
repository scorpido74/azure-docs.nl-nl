---
title: 'Zelfstudie: Apparaatgegevens ontvangen via Azure IoT Hub'
description: In deze zelfstudie leert u hoe u routering van apparaatgegevens inschakelt van IoT Hub naar Azure API for FHIR via de IoT-connector.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 06/27/2020
ms.author: punagpal
ms.openlocfilehash: aeed0c90eeecd59e23a1d87a3ebc1e1a836a84ec
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116841"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Zelfstudie: Apparaatgegevens ontvangen via Azure IoT Hub

IoT-connector biedt u de mogelijkheid om gegevens van Internet of Medical Things (IoMT)-apparaten op te nemen in Azure API for FHIR. Met de quickstart [IoT-Connector implementeren (preview) met Azure Portal](iot-fhir-portal-quickstart.md) ziet u een voorbeeld van een apparaat dat wordt beheerd door [het verzenden van telemetrie](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) door Azure IoT Central naar IoT-connector. De IoT-connector kan ook worden gebruikt voor apparaten die zijn ingericht en beheerd via Azure IoT Hub. In deze zelfstudie wordt de procedure geboden om apparaatgegevens van Azure IoT Hub te verbinden en te routeren naar IoT-connector.

## <a name="prerequisites"></a>Vereisten

- Een actief Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR-resource met ten minste één IoT-connector- [Een IoT-Connector implementeren (preview) met Azure Portal](iot-fhir-portal-quickstart.md)
- Azure IoT Hub-resource verbonden met een of meer gesimuleerde apparaat/apparaten - [Een IoT-hub maken met behulp van de Azure-portal](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Als u een met Azure IoT Hub gesimuleerde apparaat-app gebruikt, kunt u de gewenste toepassing kiezen in verschillende ondersteunde talen en systemen.

## <a name="get-connection-string-for-iot-connector-preview"></a>Verbindingsreeks ophalen voor IoT-connector (preview)

Azure IoT Hub vereist een verbindingsreeks om veilig verbinding te maken met uw IoT-connector. Maak een nieuwe verbindingsreeks voor uw IoT-connector zoals beschreven in [Een verbindingsreeks genereren](iot-fhir-portal-quickstart.md#generate-a-connection-string). Bewaar deze verbindingsreeks voor gebruik in de volgende stap.

De IoT-connector gebruikt een Azure Event Hub-exemplaar voor het ontvangen van apparaatberichten. De hierboven gemaakte verbindingsreeks is in principe de verbindingsreeks voor deze onderliggende Event Hub.

## <a name="connect-azure-iot-hub-with-the-iot-connector-preview"></a>Azure IoT Hub verbinden met de IoT-connector (preview)

Azure IoT Hub ondersteunt een functie met de naam [berichtroutering](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) die de mogelijkheid biedt om gegevens van apparaten te verzenden naar verschillende Azure-Services zoals Event Hub, Opslagaccount en Service Bus. IoT Connector gebruikt deze functie om gegevens van apparaten te verbinden en te verzenden vanuit Azure IoT Hub naar het Event Hub-eindpunt.

> [!NOTE] 
> Momenteel kunt u PowerShell of CLI-opdrachten alleen gebruiken om [berichtroutering te maken](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) omdat de Event Hub van de IoT-connector niet wordt gehost op het klantabonnement. Daarom is het voor u niet zichtbaar via de Azure-portal. Hoewel de objecten voor berichtroute worden toegevoegd met PowerShell of CLI, zijn ze zichtbaar op de Azure-portal en kunnen ze vanuit deze locatie worden beheerd.

Het instellen van een berichtroutering bestaat uit twee stappen.

### <a name="add-an-endpoint"></a>Een eindpunt toevoegen
Deze stap definieert een eindpunt waar de IoT Hub de gegevens aan zou routeren. Maak dit eindpunt met behulp van PowerShell-opdracht [add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) of CLI-opdracht [az iot hub routing-endpoint create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create), op basis van uw voorkeur.

Hier volgt de lijst met parameters die moeten worden gebruikt met de opdracht voor het maken van een eindpunt:

|PowerShell-parameter|CLI-parameter|Beschrijving|
|---|---|---|
|ResourceGroupName|resource-group|De naam van de resourcegroep van uw IoT Hub-resource.|
|Naam|hub-name|Naam van uw IoT Hub-resource.|
|EndpointName|endpoint-name|Gebruik een naam die u wilt toewijzen aan het eindpunt dat wordt gemaakt.|
|EndpointType|endpoint-type|Type eindpunt waarmee IoT Hub verbinding moet maken. Gebruik de letterlijke waarde van 'EventHub' voor PowerShell en 'eventhub' voor CLI.|
|EndpointResourceGroup|endpoint-resource-group|De naam van de resourcegroep voor de Azure API for FHIR-resource van uw IoT-connector. U kunt deze waarde ophalen via de pagina Overzicht van de Azure API for FHIR.|
|EndpointSubscriptionId|endpoint-subscription-id|Abonnement-id voor de Azure API for FHIR-resource van uw IoT-connector. U kunt deze waarde ophalen via de pagina Overzicht van de Azure API for FHIR.|
|Connectionstring|connection-string|Verbindingsreeks voor uw IoT-connector. Gebruik de waarde die u in de vorige stap hebt verkregen.|

### <a name="add-a-message-route"></a>Een berichtenroute toevoegen
In deze stap wordt een berichtroute gedefinieerd op basis van het eindpunt dat hierboven is gemaakt. Maak een route met behulp van PowerShell-opdracht [add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) of CLI-opdracht [az iot hub route create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create), op basis van uw voorkeur.

Hier volgt de lijst met parameters die moeten worden gebruikt met de opdracht voor het maken van een eindpunt:

|PowerShell-parameter|CLI-parameter|Beschrijving|
|---|---|---|
|ResourceGroupName|g|De naam van de resourcegroep van uw IoT Hub-resource.|
|Naam|hub-name|Naam van uw IoT Hub-resource.|
|EndpointName|endpoint-name|De naam van het eindpunt dat u hierboven hebt gemaakt.|
|RouteName|route-name|Een naam die u wilt toewijzen aan de berichtroute die wordt gemaakt.|
|Bron|source-type|Het type gegevens dat naar het eindpunt moet worden verzonden. Gebruik de letterlijke waarde 'DeviceMessages' voor PowerShell en 'devicemessages' voor CLI.|

## <a name="send-device-message-to-iot-hub"></a>Apparaatbericht verzenden naar IoT Hub

Gebruik uw apparaat (echt of gesimuleerd) om het voorbeeldbericht over hartslag te verzenden dat hieronder wordt weergegeven in Azure IoT Hub. Dit bericht wordt doorgestuurd naar de IoT-connector, waarbij het bericht wordt omgezet in een FHIR Observation-resource en opgeslagen in de Azure API for FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Zorg ervoor dat u het apparaatbericht verzendt dat voldoet aan de [toewijzingssjablonen](iot-mapping-templates.md) die zijn geconfigureerd met uw IoT-connector.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Apparaatgegevens weergeven in Azure API for FHIR

U kunt de FHIR Observation-resource(s) die is/zijn gemaakt door IoT-connector op de Azure API for FHIR met behulp van Postman weergeven. Stel uw [Postman in om toegang te krijgen tot de Azure API for FHIR](access-fhir-postman-tutorial.md) en maak een `GET`-aanvraag naar `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` om Observation FHIR-resources met de hartslagwaarde weer te geven die is verzonden in het bovenstaande voorbeeldbericht.

> [!TIP]
> Zorg ervoor dat uw gebruiker de juiste toegang heeft tot de Azure API for FHIR-gegevensvlak. Gebruik [Op rollen gebaseerd toegangsbeheer van Azure](configure-azure-rbac.md) om gegevenslaagrollen toe te wijzen.


## <a name="next-steps"></a>Volgende stappen

In deze quistartgids kunt u Azure IoT Hub instellen om apparaatgegevens te routeren naar de IoT-connector. Selecteer een van de onderstaande volgende stappen voor meer informatie over IoT-connector:

Meer informatie over verschillende stadia van de gegevensstroom in IoT-connector.

>[!div class="nextstepaction"]
>[Gegevensstroom van IoT-connector](iot-data-flow.md)

Meer informatie over het configureren van IoT-connector met behulp van FHIR-toewijzingssjablonen.

>[!div class="nextstepaction"]
>[Toewijzingssjablonen voor IoT-connector](iot-mapping-templates.md)

FHIR is het gedeponeerde handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.

