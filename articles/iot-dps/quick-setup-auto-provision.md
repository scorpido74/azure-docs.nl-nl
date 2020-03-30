---
title: IoT Hub Device Provisioning Service instellen in de Azure-portal
description: Snelstart - De DPS (Azure IoT Hub Device Provisioning Service) instellen in de Azure-portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 765b7e5bb7341974a6a16d3f1ed0bcabf415f9f3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76029178"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Snelstart: de Service voor het inrichten van IoT-hub-apparaten instellen met de Azure-portal

De IoT Hub Device Provisioning Service kan worden gebruikt met IoT Hub om zero-touch, just-in-time inrichten op de gewenste IoT-hub mogelijk te maken zonder menselijke tussenkomst, zodat klanten miljoenen IoT-apparaten op een veilige en schaalbare manier kunnen inrichten. Azure IoT Hub Device Provisioning Service ondersteunt IoT-apparaten met TPM-, symmetrische sleutel- en X.509-certificaatverificaties. Zie voor meer informatie het [overzicht van de IoT Hub Device Provisioning Service](./about-iot-dps.md)

In deze snelstart leert u hoe u de IoT Hub Device Provisioning Service in de Azure Portal instelt voor het inrichten van uw apparaten met de volgende stappen:
> [!div class="checklist"]
> * De Azure-portal gebruiken om een IoT-hub te maken
> * Azure Portal gebruiken voor het maken van een IoT Hub Device Provisioning Service en het ophalen van het id-bereik
> * De IoT-hub koppelen aan de Device Provisioning Service


Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.


## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Een nieuwe IoT Hub-service voor het inrichten van apparaten maken

1. Selecteer opnieuw de knop **+ Een resource** maken.

2. *Zoek op de Marktplaats* naar de **service voor apparaatinrichting**. Selecteer **de Service voor het inrichten van IoT-hubapparaten** en druk op de knop **Maken.** 

3. Geef de volgende informatie op voor uw nieuwe apparaatinrichtingsservice-instantie en druk op **Maken**.

    * **Naam:** Geef een unieke naam op voor uw nieuwe apparaatinrichtingsservice.Provide a unique name for your new Device Provisioning Service instance. Als de door u opgegeven naam beschikbaar is, verschijnt er een groen vinkje.
    * **Abonnement:** Kies het abonnement dat u wilt gebruiken om dit apparaatinrichtingsservice-exemplaar te maken.
    * **Resourcegroep:** dit veld kunt u gebruiken om een nieuwe resourcegroep te maken of het nieuwe exemplaar aan een bestaande resourcegroep toe te voegen. Kies dezelfde resourcegroep als die de IoT-hub bevat die u hierboven hebt gemaakt, zoals **TestResources**. Door alle gerelateerde resources samen in een groep te plaatsen, kunt u ze samen beheren. Als u bijvoorbeeld de resourcegroep verwijdert, worden alle resources verwijderd die deze groep bevat. Zie [Azure Resource Manager-resourcegroepen beheren](../azure-resource-manager/management/manage-resource-groups-portal.md) voor meer informatie.
    * **Locatie:** Selecteer de dichtstbijzijnde locatie voor uw apparaten.

      ![Voer basisgegevens in over de instantie Device Provisioning Service in het portalblade](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Selecteer de meldingsknop om de aanmaak van de resourceinstantie te controleren. Zodra de service is geïmplementeerd, selecteert u **Vastmaken aan het dashboard**en **vervolgens Naar resource gaan.**

    ![De implementatiemelding bewaken](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>De IoT-hub en uw service voor het inrichten van apparaten koppelen

In deze sectie voegt u een configuratie toe aan de instantie Device Provisioning Service. Deze configuratie bepaalt welke IoT-hub wordt ingericht voor welke apparaten.

1. Selecteer de knop **Alle bronnen** in het linkermenu van de Azure-portal. Selecteer het Device Provisioning Service-exemplaar dat u in de voorgaande sectie hebt gemaakt. 

    Als uw menu is geconfigureerd met **Flyout** in plaats van de **docked-modus** in de portalinstellingen, moet u op de 3 regels linksboven klikken om het portalmenu aan de linkerkant te openen.  

2. Selecteer **linked IoT-hubs**in het menu van de Apparaatinrichtingsservice. Druk op de **+ Add** knop gezien aan de bovenkant. 

3. Geef op de pagina **Koppeling toevoegen aan IoT-hub** de volgende informatie om uw nieuwe apparaatinrichtingsservice-instantie te koppelen aan een IoT-hub. Druk vervolgens op **Opslaan.** 

    * **Abonnement:** Selecteer het abonnement met de IoT-hub die u wilt koppelen aan het nieuwe exemplaar van de Apparaatinrichtingsservice.
    * **Iot-hub:** Selecteer de IoT-hub die u wilt koppelen aan het nieuwe exemplaar van de apparaatinrichtingsservice.
    * **Toegangsbeleid:** selecteer **iothubowner** als de referenties om de koppeling met de IoT-hub tot stand te brengen.  

      ![De hubnaam koppelen aan een koppeling naar de instantie Apparaatinrichtingsservice in het portalblad](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Nu ziet u de geselecteerde hub onder de blade **Gekoppelde IoT-hubs**. Mogelijk moet u op **Vernieuwen** drukken om deze weer te geven.


## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze Quick Start in Azure Portal zijn gemaakt.

1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens de service voor apparaatinrichting. Selecteer Boven aan het detailvenster van het apparaat de optie **Verwijderen**.  
2. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens uw IoT-hub. Selecteer Boven aan het detailvenster van de hub de optie **Verwijderen**.  

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een IoT-hub en een instantie voor apparaatinrichtingsservice geïmplementeerd en de twee resources gekoppeld. Ga door met de snelle start voor het maken van een gesimuleerd apparaat om deze instelling te gebruiken voor het inrichten van een gesimuleerd apparaat.

> [!div class="nextstepaction"]
> [Snel start om een gesimuleerd apparaat te maken](./quick-create-simulated-device-symm-key.md)
