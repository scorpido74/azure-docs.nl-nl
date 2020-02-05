---
title: 'Zelf studie: een zonne-straf bewakings-app maken met IoT Central'
description: 'Zelf studie: informatie over het maken van een zonne-paneel toepassing met behulp van Azure IoT Central-toepassings sjablonen.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025772"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Zelf studie: de sjabloon voor het bewaken van het zonne deel venster maken en door lopen 



Deze zelf studie leidt u door het proces van het maken van de toepassing voor het bewaken van het zonne deel venster, dat een voorbeeld model bevat met gesimuleerde gegevens. In deze zelfstudie leert u:


> [!div class="checklist"]
> * Maak de app voor het zonne deel venster gratis
> * Toepassings door lopen
> * Resources opschonen


Als u geen abonnement hebt, [maakt u een account voor een gratis proef versie](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Vereisten
- Geen
- Azure-abonnement wordt aanbevolen, maar is niet vereist voor het uitproberen


## <a name="create-a-solar-panel-monitoring-app"></a>Een app maken om zonnepanelen te bewaken 

U kunt deze toepassing in drie eenvoudige stappen maken:

1. Open de [Start pagina van Azure IOT Central](https://apps.azureiotcentral.com) en klik op **Build** om een nieuwe toepassing te maken. 

2. Selecteer het tabblad **energie** en klik op **app maken** onder **Zons paneel bewaking** toepassings tegel. 

    > [!div class="mx-imgBorder"]
    > ![build-app](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. Bij het maken van de **app** wordt het **nieuwe toepassings** formulier geopend. Vul de gevraagde gegevens in, zoals wordt weer gegeven in de afbeelding hieronder:
    * **Toepassings naam**: Kies een naam voor uw IOT Central-toepassing. 
    * **URL**: kies een IOT Central URL, het platform controleert de uniekheid ervan.
    * **gratis proef versie van 7 dagen**: als u al een Azure-abonnement hebt, wordt de standaard instelling aanbevolen. Als u geen Azure-abonnement hebt, kunt u beginnen met een gratis proef versie.
    * **Facturerings gegevens**: de toepassing zelf is gratis. De informatie over de adres lijst, het Azure-abonnement en de regio is vereist voor het inrichten van de resources voor uw app.
    * Klik op de knop **maken** onder aan de pagina en uw app wordt binnen een minuut gemaakt.
        ![nieuw toepassings formulier](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Nieuwe facturerings gegevens voor het toepassings formulier](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>De toepassing en gesimuleerde gegevens controleren

De zojuist gemaakte toepassing voor zonne deel venster is uw app en u kunt deze op elk gewenst moment wijzigen. Laten we controleren of de app is geïmplementeerd en werkt zoals verwacht voordat u deze wijzigt.

Ga naar het **dash board**om te controleren of de app is gemaakt en gesimuleerd. Als u de tegels met enkele gegevens kunt zien, is de implementatie van uw app geslaagd. Het duurt enkele minuten voordat de gegevens simulatie is gegenereerd. Geef deze 1-2 minuten. 

## <a name="application-walk-through"></a>Toepassings door lopen
Nadat u de app-sjabloon hebt geïmplementeerd, wordt er een voor beeld van een Smart meter apparaat, model apparaat en dash board geleverd.

Adatum is een fictief energie bedrijf dat zonne panelen bewaakt en beheert. Op het dash board voor Zons bewaking ziet u de eigenschappen, gegevens en voorbeeld opdrachten van een zonne deel venster. Hiermee kunnen Opera tors en ondersteunings teams de volgende activiteiten proactief uitvoeren voordat ze ondersteuning bieden aan ondersteunings incidenten:
* Bekijk de meest recente informatie over het paneel en de geïnstalleerde locatie op de kaart
* Proactief de status en verbindings status van het paneel controleren
* Bekijk de energie generatie en temperatuur trends om afwijkende patronen te ondervangen
* De totale energie generatie voor planningen en facturerings doeleinden volgen
* Opdracht-en besturings bewerkingen, zoals het paneel activeren en de firmware versie bijwerken. In de sjabloon worden met de opdracht knoppen de mogelijke functionaliteiten weer gegeven en worden geen echte opdrachten verzonden.

> [!div class="mx-imgBorder"]
> bewakings dashboard ![zonne deel venster](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Apparaten
In de app wordt een voor beeld van een zonne-panel apparaat geleverd. U kunt de details van het apparaat bekijken door te klikken op het tabblad **apparaten** .

> [!div class="mx-imgBorder"]
> ![zonne-paneel apparaten](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Klik op de koppeling voor beeld van apparaat **SP0123456789** om de details van het apparaat weer te geven. Op de pagina **Eigenschappen bijwerken** kunt u de Beschrijf bare eigenschappen van het apparaat bijwerken en de bijgewerkte waarden in het dash board visualiseren. 

> [!div class="mx-imgBorder"]
> eigenschappen van ![zonne deel venster](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Apparaatprofiel
Klik op het tabblad **Apparaatinstellingen** om het model met zonne-panel apparaten weer te geven. Het model heeft een vooraf gedefinieerde interface voor gegevens, eigenschappen, opdrachten en weer gaven.

> [!div class="mx-imgBorder"]
> ![sjabloon voor zonne panel apparaten](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Resources opschonen
Als u besluit deze toepassing niet te blijven gebruiken, verwijdert u uw toepassing met de volgende stappen:

1. Open het tabblad beheer vanuit het linkerdeel venster.
2. Selecteer toepassings instellingen en klik onder aan de pagina op de knop verwijderen. 

    > [!div class="mx-imgBorder"]
    > toepassings](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png) ![verwijderen


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de architectuur van een zonne paneel-app raadpleegt u [het concept artikel](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Maak zonne-paneel toepassings sjablonen gratis: [zonne-paneel app](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Zie [IOT Central-overzicht](https://docs.microsoft.com/azure/iot-central/) voor meer informatie over IOT Central

