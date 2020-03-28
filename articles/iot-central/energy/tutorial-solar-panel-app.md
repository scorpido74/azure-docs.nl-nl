---
title: 'Zelfstudie: Maak een zonne-strafrechtelijke monitoring app met IoT Central'
description: 'Zelfstudie: Meer informatie over het maken van een toepassing voor zonnepanelen met Azure IoT Central-toepassingssjablonen.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77025772"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Zelfstudie: De sjabloon voor het bewaken van het zonnepaneel-monitoring-app maken en doorlopen 



Deze zelfstudie begeleidt u door het proces van het maken van de toepassing voor het bewaken van zonnepanelen, die een voorbeeldapparaatmodel met gesimuleerde gegevens bevat. In deze zelfstudie leert u:


> [!div class="checklist"]
> * Maak de zonnepaneel-app gratis
> * Applicatie walk-through
> * Resources opschonen


Als je geen abonnement hebt, [maak je een gratis proefaccount aan](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Vereisten
- Geen
- Azure-abonnement wordt aanbevolen, maar niet vereist om uit te proberen


## <a name="create-a-solar-panel-monitoring-app"></a>Een app maken om zonnepanelen te bewaken 

U deze toepassing in drie eenvoudige stappen maken:

1. Open [de startpagina van Azure IoT Central](https://apps.azureiotcentral.com) en klik op **Bouwen** om een nieuwe toepassing te maken. 

2. Selecteer het tabblad **Energie** en klik op **App maken** onder **toepassingstegel voor het bewaken van het zonnepaneel.** 

    > [!div class="mx-imgBorder"]
    > ![App bouwen](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Als u de app maakt,** wordt **nieuw aanvraagformulier** geopend. Vul de gevraagde gegevens in zoals aangegeven in de onderstaande figuur:
    * **Toepassingsnaam:** Kies een naam voor uw IoT Central-toepassing. 
    * **URL**: Kies een IoT Central URL, het platform controleert de uniciteit ervan.
    * Gratis proefperiode van 7 dagen : Als u al een **Azure-abonnement**hebt, wordt de standaardinstelling aanbevolen. Als u geen Azure-abonnement hebt, begint u met een gratis proefversie.
    * **Factureringsgegevens:** De toepassing zelf is gratis. De directory-, Azure-abonnementen en regiogegevens zijn vereist om de bronnen voor uw app in te richten.
    * Klik op De knop **Maken** onder aan de pagina en uw app wordt zo gemaakt.
        ![Nieuw aanvraagformulier](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Factureringsgegevens voor nieuwe aanvraagformulieren](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>De toepassing en gesimuleerde gegevens verifiëren

De nieuw gemaakte zonnepaneel app is uw app en u het op elk gewenst moment wijzigen. Laten we ervoor zorgen dat de app wordt geïmplementeerd en werkt zoals verwacht voordat u deze wijzigt.

Als u de app-creatie en gegevenssimulatie wilt verifiëren, gaat u naar het **dashboard**. Als u de tegels met bepaalde gegevens zien, is de implementatie van uw app geslaagd. De gegevenssimulatie kan een paar minuten duren om de gegevens te genereren, dus geef het 1-2 minuten. 

## <a name="application-walk-through"></a>Applicatie walk-through
Nadat u de app-sjabloon hebt geïmplementeerd, wordt deze geleverd met voorbeeldvan een slimme meterapparaat, apparaatmodel en dashboard.

Adatum is een fictief energiebedrijf, dat zonnepanelen monitort en beheert. Op het dashboard voor het bewaken van het zonnepaneel ziet u de eigenschappen, gegevens en voorbeeldopdrachten van zonnepanelen. Het stelt operators en ondersteuningsteams in staat om proactief de volgende activiteiten uit te voeren voordat het omgaat in ondersteuningsincidenten:
* Bekijk de laatste panelinformatie en de geïnstalleerde locatie op de kaart
* Controleer proactief de status van het paneel en de verbindingsstatus
* Bekijk de energieopwekking s- en temperatuurtrends om afwijkende patronen te vangen
* De totale energieopwekking bijhouden voor plannings- en factureringsdoeleinden
* Command and control operations zoals activate panel en update firmware version. In de sjabloon tonen de opdrachtknoppen de mogelijke functionaliteiten en verzenden ze geen echte opdrachten.

> [!div class="mx-imgBorder"]
> ![Dashboard voor het bewaken van zonnepanelen](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Apparaten
De app wordt geleverd met een voorbeeld zonnepaneel apparaat. U de apparaatgegevens bekijken door op het tabblad **Apparaten** te klikken.

> [!div class="mx-imgBorder"]
> ![Zonnepaneelapparaten](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Klik op de koppeling **SP0123456789** om de details van het apparaat te bekijken. Op de pagina **Eigenschappen bijwerken** u de beschrijfbare eigenschappen van het apparaat bijwerken en de bijgewerkte waarden op het dashboard visualiseren. 

> [!div class="mx-imgBorder"]
> ![Eigenschappen van zonnepanelen](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Apparaatsjabloon
Klik op het tabblad **Apparaatsjablonen** om het model van het zonnepaneelapparaat weer te geven. Het model heeft een vooraf definiërende interface voor gegevens, eigenschappen, opdrachten en weergaven.

> [!div class="mx-imgBorder"]
> ![Sjabloon voor apparaten voor zonnepanelen](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u besluit deze toepassing niet verder te gebruiken, verwijdert u de volgende stappen met de volgende stappen:

1. Open het tabblad Beheer in het linkerdeelvenster
2. Selecteer Toepassingsinstellingen en klik onder aan de pagina op Verwijderen. 

    > [!div class="mx-imgBorder"]
    > ![Toepassing verwijderen](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over zonnepaneel app architectuur verwijzen naar [het concept artikel](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Maak zonnepaneel applicatie sjablonen gratis: [zonnepaneel app](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Meer informatie over IoT Central, zie [IoT Central-overzicht](https://docs.microsoft.com/azure/iot-central/)

