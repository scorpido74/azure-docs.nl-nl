---
title: 'Snelstartgids: inrichten van apparaten instellen in de Azure Portal'
description: 'Azure-Quick Start: de Azure-IoT Hub Device Provisioning Service instellen in de Azure Portal'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 498ec758fe8ad5d596a62d3541b39d860336e726
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151294"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Snelstartgids: Stel de IoT Hub Device Provisioning Service in met de Azure Portal

Deze stappen laten zien hoe u de Azure-cloudresources in de portal instelt voor het inrichten van uw apparaten. Dit artikel omvat stappen voor het maken van uw IoT-hub, het maken van een nieuwe IoT Hub Device Provisioning Service en het aan elkaar koppelen van de twee services. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Een nieuw exemplaar van de IoT Hub Device Provisioning Service maken

1. Selecteer de knop **+ een resource maken** opnieuw.

2. *Zoek op de Marketplace* naar de **Device Provisioning Service**. Selecteer **IOT hub Device Provisioning Service** en klik op de knop **maken** . 

3. Geef de volgende informatie op voor het nieuwe Device Provisioning service-exemplaar en klik op **Create**.

    * **Naam:** geef een unieke naam op voor uw nieuwe instantie van de Device Provisioning Service. Als de door u opgegeven naam beschikbaar is, verschijnt er een groen vinkje.
    * **Abonnement**: kies het abonnement dat u wilt gebruiken om deze instantie van de Device Provisioning Service te maken.
    * **Resourcegroep:** dit veld kunt u gebruiken om een nieuwe resourcegroep te maken of het nieuwe exemplaar aan een bestaande resourcegroep toe te voegen. Kies dezelfde resourcegroep als die de IoT-hub bevat die u hierboven hebt gemaakt, zoals **TestResources**. Door alle gerelateerde resources samen in een groep te plaatsen, kunt u ze samen beheren. Als u bijvoorbeeld de resourcegroep verwijdert, worden alle resources verwijderd die deze groep bevat. Zie [Azure Resource Manager-resourcegroepen beheren](../azure-resource-manager/manage-resource-groups-portal.md) voor meer informatie.
    * **Locatie**: selecteer de locatie die het dichtst bij uw apparaten in de buurt is.

      ![Basis informatie over uw Device Provisioning service-exemplaar invoeren op de portal-Blade](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Selecteer de meldings knop om het maken van het resource-exemplaar te bewaken. Zodra de service is geïmplementeerd, selecteert **u vastmaken aan dash board**en **gaat**u naar resource.

    ![De implementatiemelding bewaken](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>De IoT-hub en uw Device Provisioning Service koppelen

In deze sectie gaat u een configuratie toevoegen aan het Device Provisioning service-exemplaar. Deze configuratie bepaalt welke IoT-hub wordt ingericht voor welke apparaten.

1. Selecteer de knop **alle resources** in het menu aan de linkerkant van het Azure Portal. Selecteer het Device Provisioning Service-exemplaar dat u in de voorgaande sectie hebt gemaakt.  

2. Selecteer in het menu Device Provisioning Service de optie **gekoppelde IOT-hubs**. Klik bovenaan op de knop **toevoegen** . 

3. Geef, op de pagina **Koppeling toevoegen aan IoT-hub**, de volgende informatie op om uw nieuwe instantie van de Device Provisioning Service te koppelen aan een IoT-hub. Druk vervolgens op **Opslaan**. 

    * **Abonnement:** selecteer het abonnement dat de IoT-hub bevat die u wilt koppelen aan uw nieuwe Device Provisioning Service-exemplaar.
    * **IoT-hub:** selecteer de IoT-hub die u wilt koppelen aan uw nieuwe Device Provisioning Service-exemplaar.
    * **Toegangsbeleid:** selecteer **iothubowner** als de referenties om de koppeling met de IoT-hub tot stand te brengen.  

      ![Koppel de naam van de hub om deze te koppelen aan het Device Provisioning service-exemplaar op de Blade Portal](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Nu ziet u de geselecteerde hub onder de blade **Gekoppelde IoT-hubs**. Mogelijk moet u op **vernieuwen klikken** om het weer te geven.


## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze Quick Start in Azure Portal zijn gemaakt.

1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw Device Provisioning Service. Selecteer boven in het detail venster van het apparaat **verwijderen**.  
2. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw IOT-hub. Selecteer **verwijderen**boven in het detail venster van de hub.  

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een IoT-hub en een Device Provisioning service-exemplaar geïmplementeerd en zijn de twee resources gekoppeld. Als u wilt weten hoe u deze installatie kunt gebruiken om een gesimuleerd apparaat in te richten, gaat u verder met de Quick start voor het maken van een gesimuleerd apparaat.

> [!div class="nextstepaction"]
> [Quick start voor het maken van een gesimuleerd apparaat](./quick-create-simulated-device.md)
