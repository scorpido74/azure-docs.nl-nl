---
title: Zelf studie over de app-sjabloon van micro fulfill Center | Microsoft Docs
description: Een zelf studie van de toepassings sjabloon voor micro-fulfillment Center voor IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 2ed6f37bc3b00397fa6331a501e1b16c8d622b5f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027788"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Zelf studie: een toepassings sjabloon voor micro uitvoerings centrum implementeren en door lopen

In deze zelf studie maken we gebruik van de toepassings sjabloon Azure IoT Central ***micro-fulfillment Center*** om u te laten zien hoe u een handels oplossing bouwt. U leert hoe u de MFC-sjabloon implementeert, wat is opgenomen in het vak en wat u mogelijk wilt doen.

In deze zelfstudie leert u het volgende: 
> [!div class="checklist"]
> * De Azure IoT Central **micro fulfillment Center-** sjabloon gebruiken om een retail-toepassing te maken
> * Door loop van de toepassing 

## <a name="prerequisites"></a>Vereisten
Voor het volt ooien van deze reeks zelf studies hebt u het volgende nodig:
* Een Azure-abonnement. U kunt eventueel een gratis proef versie van 7 dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Een app maken 
In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de reeks zelf studies om een volledige oplossing te bouwen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Ga naar de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) .
1. Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om het te openen. u kunt zich ook aanmelden met een Microsoft-account:

   ![Voer uw organisatieaccount in](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**.

1. Selecteer **detail handel**.  Op de pagina Retail worden verschillende sjablonen voor de retail toepassing weer gegeven.

Een nieuw micro fulfillment Center-toepassing maken die gebruikmaakt van preview-functies:  
1. Selecteer de toepassings sjabloon **micro fulfillment Center** . Deze sjabloon bevat sjablonen voor alle apparaten die in de zelf studie worden gebruikt. De sjabloon biedt ook een operator dashboard voor het bewaken van voor waarden binnen het fulfillment Center en de voor waarden voor uw robot-providers. 

    > [!div class="mx-imgBorder"]
    > ![type micro-fulfillment-toepassing](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. U kunt desgewenst een beschrijvende **toepassings naam**kiezen.  De toepassings sjabloon is gebaseerd op het fictieve bedrijf North Wind Traders. 

    > [!NOTE]
    > Als u een beschrijvende **toepassings naam**gebruikt, moet u nog steeds een unieke waarde voor de **URL**van de toepassing gebruiken.

1. Als u een Azure-abonnement hebt, voert u uw *adres lijst, het Azure-abonnement en de regio*in. Als u geen abonnement hebt, kunt u een **gratis proef versie van 7 dagen** inschakelen en de vereiste contact gegevens volt ooien.  

    Zie de [snelstart over het maken van een toepassing](../preview/quick-deploy-iot-central.md)voor meer informatie over directory's en abonnementen.

1. Selecteer **Maken**.

> [!div class="mx-imgBorder"]
> ![micro](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png) toepassing maken

## <a name="walk-through-the-application"></a>door loop de toepassing 

### <a name="dashboard"></a>Dashboard 

Nadat de app-sjabloon is geïmplementeerd, gaat u eerst naar het **dash board van het micro upcentrum voor North Wind Traders**. Noorden wind van het bedrijf is een fictieve detail handel met een micro-fulfillment-centrum dat wordt beheerd in deze IoT Central-toepassing. Op dit dash board van de operator ziet u informatie en telemetrie over de apparaten in deze sjabloon, samen met een set opdrachten, taken en acties die u kunt uitvoeren. Het dash board is logisch gesplitst in twee secties (links en rechts). Aan de linkerkant hebt u de mogelijkheid om de omgevings voorwaarden te bewaken in de uitvoerings structuur en aan de rechter kant, kunt u de status van een automatische vervoerder in de faciliteit bewaken.  

Vanuit het dash board kunt u het volgende doen:
   * Zie telemetrie van apparaten, zoals het aantal picks, het aantal verwerkte orders en eigenschappen, zoals de status van de structuur systeem, enzovoort.  
   * Bekijk het **vloer plan** en de locatie van de geautomatiseerde vervoerders binnen de fulfillment-structuur.
   * Trigger-opdrachten zoals het opnieuw instellen van het besturings systeem, het bijwerken van de firmware van de transporteur, het opnieuw configureren van het netwerk, enzovoort.

> [!div class="mx-imgBorder"]
> ![micro upcenter-dash board](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Bekijk een voor beeld van het dash board dat een operator kan gebruiken voor het bewaken van de voor waarden in het fulfillment Center. 
   * Controleer de status van de nettoladingen die worden uitgevoerd op het gateway apparaat binnen het fulfillment-centrum.    

> [!div class="mx-imgBorder"]
> ![micro upcenter-dash board](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Apparaatprofiel
Als u op het tabblad Device templates klikt, ziet u dat er twee verschillende typen apparaten zijn die deel uitmaken van de sjabloon: 
   * **Robot-provider**: deze Device-sjabloon vertegenwoordigt de definitie voor een werkende robot-provider die is geïmplementeerd in de fulfillment-structuur en die de juiste opslag-en herstel bewerkingen uitvoert. Als u op de sjabloon klikt, ziet u dat de robot apparaatgegevens, zoals de Tempe ratuur, de positie van de as en eigenschappen, zoals de status van de gerobotde transporteur, enzovoort, verzendt. 
   * **Bewaking van de structuur voorwaarde**: deze Device-sjabloon vertegenwoordigt een verzameling apparaten waarmee u de omgevings voorwaarde kunt bewaken en het gateway apparaat dat als host fungeert voor verschillende werk belastingen om uw fulfillment Center te verbeteren. Het apparaat verzendt telemetriegegevens, zoals de Tempe ratuur, het aantal picks, het aantal orders enzovoort, en de status en status van de reken werkbelastingen die in uw omgeving worden uitgevoerd. 

> [!div class="mx-imgBorder"]
> ![micro-fulfillment Center-sjablonen voor apparaten](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Als u op het tabblad apparaatgroepen klikt, ziet u ook dat voor deze Apparaatinstellingen automatisch apparaatgroepen worden gemaakt.

## <a name="rules"></a>Regels
Wanneer u naar het tabblad regels gaat, ziet u een voor beeld van een regel in de toepassings sjabloon om de Tempe ratuur voor de automatische provider te controleren. U kunt deze regel gebruiken om de operator te waarschuwen als een specifieke robot in de faciliteit oververhit is en offline moet worden gehaald voor onderhoud. 

Maak gebruik van de voorbeeld regel als inspiratie om regels te definiëren die geschikter zijn voor uw bedrijfs functies.

   - **Robot verloopt te warm**: deze regel wordt geactiveerd als de automatische provider een drempel waarde voor de Tempe ratuur van een bepaalde tijd bereikt. 

> [!div class="mx-imgBorder"]
> regels van ![micro](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassings sjabloon door naar **beheer** > **Toepassings instellingen** te gaan en op **verwijderen**te klikken.

> [!div class="mx-imgBorder"]
> ![toepassing voor het opschonen van micro fulfill Center-apps](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [oplossings architectuur voor micro-fulfillment Center](./architecture-micro-fulfillment-center-pnp.md)
* Meer informatie over andere [IOT Central Retail-sjablonen](./overview-iot-central-retail-pnp.md)
* Raadpleeg [IOT Central Overview](../preview/overview-iot-central.md) voor meer informatie over IOT Central