---
title: 'Zelf studie: een slimme meter analyse-app maken met IoT Central'
description: 'Zelf studie: informatie over het maken van een toepassing voor slimme meter bewaking met behulp van Azure IoT Central-toepassings sjablonen.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9e954e9c1a7efa43a19849b1c5b40284ec84eeed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015997"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Zelf studie: de sjabloon voor het bewakings programma voor slimme meters maken en door lopen 



In deze zelf studie wordt u begeleid bij het maken van de toepassing voor het controleren van slimme meters, die een voorbeeld model bevat met gesimuleerde gegevens. In deze zelfstudie leert u:

> [!div class="checklist"]
> * Maak de app slimme meter gratis
> * Toepassings door lopen
> * Resources opschonen


Als u geen abonnement hebt, [maakt u een account voor een gratis proef versie](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Vereisten
- Geen
- Azure-abonnement wordt aanbevolen, maar is niet vereist voor het uitproberen

## <a name="create-a-smart-meter-monitoring-app"></a>Een app maken om slimme meters te bewaken 

U kunt deze toepassing in drie eenvoudige stappen maken:

1. Open de [Start pagina van Azure IOT Central](https://apps.azureiotcentral.com) en klik op **Build** om een nieuwe toepassing te maken. 

2. Selecteer tabblad **energie** en klik op **app maken** onder toepassing tegel voor **slimme meter bewaking** .

    > [!div class="mx-imgBorder"]
    > ![build-app](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. Bij het maken van de **app** wordt het **nieuwe toepassings** formulier geopend. Vul de gevraagde gegevens in, zoals wordt weer gegeven in de afbeelding hieronder:
    * **Toepassings naam**: Kies een naam voor uw IOT Central-toepassing. 
    * **URL**: kies een IOT Central URL, het platform controleert de uniekheid ervan.
    * **gratis proef versie van 7 dagen**: als u al een Azure-abonnement hebt, wordt de standaard instelling aanbevolen. Als u geen Azure-abonnement hebt, kunt u beginnen met een gratis proef versie.
    * **Facturerings gegevens**: de toepassing zelf is gratis. De informatie over de adres lijst, het Azure-abonnement en de regio is vereist voor het inrichten van de resources voor uw app.
    * Klik op de knop **maken** onder aan de pagina en uw app wordt binnen een minuut gemaakt.

        ![Nieuw toepassings formulier](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Nieuwe facturerings gegevens voor het toepassings formulier](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>De toepassing en gesimuleerde gegevens controleren

De zojuist gemaakte slimme meter-app is uw app en u kunt deze op elk gewenst moment wijzigen. Laten we controleren of de app is geïmplementeerd en werkt zoals verwacht voordat u deze wijzigt.

Ga naar het **dash board**om te controleren of de app is gemaakt en gesimuleerd. Als u de tegels met enkele gegevens kunt zien, is de implementatie van uw app geslaagd. Het duurt enkele minuten voordat de gegevens simulatie is gegenereerd. Geef deze 1-2 minuten. 

## <a name="application-walk-through"></a>Toepassings door lopen
Nadat u de app-sjabloon hebt geïmplementeerd, wordt er een voor beeld van een Smart meter apparaat, model apparaat en een dash board geleverd. 

Adatum is een fictief energie bedrijf dat slimme meters bewaakt en beheert. Op het dash board voor slimme meter bewaking ziet u eigenschappen, gegevens en voorbeeld opdrachten voor slimme meters. Hiermee kunnen Opera tors en ondersteunings teams de volgende activiteiten proactief uitvoeren voordat ze ondersteuning bieden aan ondersteunings incidenten: 
* Bekijk de nieuwste meter gegevens en de geïnstalleerde locatie op de kaart
* Het meter netwerk en de verbindings status proactief controleren 
* Minimale en maximale spannings aflezingen voor de netwerk status controleren 
* Bekijk de energie, kracht en spannings trends om afwijkende patronen te ondervangen 
* Het totale energie verbruik bijhouden voor planning en facturerings doeleinden
* Opdracht-en controle bewerkingen, zoals reconnect meter en firmware versie bijwerken. In de sjabloon worden met de opdracht knoppen de mogelijke functionaliteiten weer gegeven en worden geen echte opdrachten verzonden. 

> [!div class="mx-imgBorder"]
> ![dash board voor slimme meter bewaking](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Apparaten
De app bevat een voor beeld van een Smart meter apparaat. U kunt de details van het apparaat bekijken door te klikken op het tabblad **apparaten** .

> [!div class="mx-imgBorder"]
> apparaten met ![slimme meter](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Klik op de koppeling voor beeld van apparaat **SM0123456789** om de details van het apparaat weer te geven. U kunt de Beschrijf bare eigenschappen van het apparaat bijwerken op de pagina **Eigenschappen bijwerken** en de bijgewerkte waarden in het dash board visualiseren.

> [!div class="mx-imgBorder"]
> eigenschappen van slimme meter ![](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Apparaatprofiel
Klik op het tabblad **device templates** om het apparaat model voor slimme meters weer te geven. Het model heeft een vooraf gedefinieerde interface voor gegevens, eigenschappen, opdrachten en weer gaven.

> [!div class="mx-imgBorder"]
> Device-sjablonen voor ![Smart meter](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u besluit deze toepassing niet te blijven gebruiken, verwijdert u uw toepassing met de volgende stappen:

1. Open het tabblad beheer vanuit het linkerdeel venster.
2. Selecteer toepassings instellingen en klik onder aan de pagina op de knop verwijderen. 

    > [!div class="mx-imgBorder"]
    > toepassings](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png) ![verwijderen


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de architectuur van de app slimme meter raadpleegt u [het concept artikel](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Maak slimme meter toepassings sjablonen gratis: [slimme meter-app](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Zie [IOT Central-overzicht](https://docs.microsoft.com/azure/iot-central/) voor meer informatie over IOT Central
