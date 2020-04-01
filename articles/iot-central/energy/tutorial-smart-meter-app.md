---
title: 'Zelfstudie: Een app voor slimme meteranalyse maken met IoT Central'
description: 'Zelfstudie: meer informatie over het maken van een toepassing voor het bewaken van slimme meters met Azure IoT Central-toepassingssjablonen.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9e954e9c1a7efa43a19849b1c5b40284ec84eeed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77015997"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Zelfstudie: De sjabloon voor het bewaken van slimme meters maken en doorlopen 



Deze zelfstudie begeleidt u bij het maken van de toepassing voor het bewaken van slimme meters, die een voorbeeldapparaatmodel met gesimuleerde gegevens bevat. In deze zelfstudie leert u:

> [!div class="checklist"]
> * Maak de Smart Meter App gratis
> * Applicatie walk-through
> * Resources opschonen


Als je geen abonnement hebt, [maak je een gratis proefaccount aan](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Vereisten
- Geen
- Azure-abonnement wordt aanbevolen, maar niet vereist om uit te proberen

## <a name="create-a-smart-meter-monitoring-app"></a>Een app maken om slimme meters te bewaken 

U deze toepassing in drie eenvoudige stappen maken:

1. Open [de startpagina van Azure IoT Central](https://apps.azureiotcentral.com) en klik op **Bouwen** om een nieuwe toepassing te maken. 

2. Selecteer het tabblad **Energie** en klik op **App maken** onder de toepassingstegel **Slimme meterbewaking.**

    > [!div class="mx-imgBorder"]
    > ![App bouwen](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. **Als u de app maken,** wordt het **formulier Nieuw aanvraagformulier** geopend. Vul de gevraagde gegevens in zoals aangegeven in de onderstaande figuur:
    * **Toepassingsnaam:** Kies een naam voor uw IoT Central-toepassing. 
    * **URL**: Kies een IoT Central URL, het platform controleert de uniciteit ervan.
    * Gratis proefperiode van 7 dagen : Als u al een **Azure-abonnement**hebt, wordt de standaardinstelling aanbevolen. Als u geen Azure-abonnement hebt, begint u met een gratis proefversie.
    * **Factureringsgegevens:** De toepassing zelf is gratis. De directory-, Azure-abonnementen en regiogegevens zijn vereist om de bronnen voor uw app in te richten.
    * Klik op De knop **Maken** onder aan de pagina en uw app wordt zo gemaakt.

        ![Nieuw aanvraagformulier](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Factureringsgegevens voor nieuwe aanvraagformulieren](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>De toepassing en gesimuleerde gegevens verifiëren

De nieuw gemaakte slimme meter-app is uw app en u deze op elk gewenst moment wijzigen. Laten we ervoor zorgen dat de app wordt geïmplementeerd en werkt zoals verwacht voordat u deze wijzigt.

Als u de app-creatie en gegevenssimulatie wilt verifiëren, gaat u naar het **dashboard**. Als u de tegels met bepaalde gegevens zien, is de implementatie van uw app geslaagd. De gegevenssimulatie kan een paar minuten duren om de gegevens te genereren, dus geef het 1-2 minuten. 

## <a name="application-walk-through"></a>Applicatie walk-through
Nadat u de app-sjabloon hebt geïmplementeerd, wordt deze geleverd met voorbeeldapparaten voor slimme meters, een apparaatmodel en een dashboard. 

Adatum is een fictief energiebedrijf, dat slimme meters monitort en beheert. Op het dashboard voor het bewaken van slimme meters ziet u slimme metereigenschappen, gegevens en voorbeeldopdrachten. Het stelt operators en ondersteuningsteams in staat om proactief de volgende activiteiten uit te voeren voordat het omgaat in ondersteuningsincidenten: 
* Bekijk de laatste meterinformatie en de geïnstalleerde locatie op de kaart
* Controleer proactief het meternetwerk en de verbindingsstatus 
* Monitor Min en Max spanningsmetingen voor de gezondheid van het netwerk 
* Bekijk de energie-, energie- en spanningstrends om afwijkende patronen te vangen 
* Het totale energieverbruik bijhouden voor plannings- en factureringsdoeleinden
* Command and control operations zoals reconnect meter en update firmware versie. In de sjabloon tonen de opdrachtknoppen de mogelijke functionaliteiten en verzenden ze geen echte opdrachten. 

> [!div class="mx-imgBorder"]
> ![Dashboard voor slimme meterbewaking](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Apparaten
De app wordt geleverd met een voorbeeld slimme meter apparaat. U de apparaatgegevens bekijken door op het tabblad **Apparaten** te klikken.

> [!div class="mx-imgBorder"]
> ![Slimme meterapparaten](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Klik op de link **SM0123456789** om de gegevens van het apparaat te bekijken. U de beschrijfbare eigenschappen van het apparaat bijwerken op de pagina **Eigenschappen bijwerken** en de bijgewerkte waarden op het dashboard visualiseren.

> [!div class="mx-imgBorder"]
> ![Eigenschappen van slimme meter](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Apparaatsjabloon
Klik op het tabblad **Apparaatsjablonen** om het model van het slimme meterapparaat weer te geven. Het model heeft een vooraf definiërende interface voor gegevens, eigenschappen, opdrachten en weergaven.

> [!div class="mx-imgBorder"]
> ![Sjablonen voor slimme meterapparaten](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u besluit deze toepassing niet verder te gebruiken, verwijdert u de volgende stappen met de volgende stappen:

1. Open het tabblad Beheer in het linkerdeelvenster
2. Selecteer Toepassingsinstellingen en klik onder aan de pagina op Verwijderen. 

    > [!div class="mx-imgBorder"]
    > ![Toepassing verwijderen](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de architectuur van slimme meter-apps verwijzen naar [het conceptartikel](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Gratis sjablonen voor slimme metertoepassingen maken: [slimme meter-app](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Meer informatie over IoT Central, zie [IoT Central-overzicht](https://docs.microsoft.com/azure/iot-central/)
