---
title: Zelf studie over de app-sjabloon van micro fulfill Center | Microsoft Docs
description: Een zelf studie over de micro-fulfillment Center-toepassings sjabloon voor Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 93906f582f1edc351088f8b4c453bf9ebda54f83
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77369822"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Zelf studie: een toepassings sjabloon voor micro uitvoerings centrum implementeren en door lopen

In deze zelf studie gebruikt u de Azure IoT Central micro fulfillment Center-toepassings sjabloon om een handels oplossing te bouwen. U leert hoe u de sjabloon implementeert, wat erin is opgenomen en wat u mogelijk wilt doen.

## <a name="prerequisites"></a>Vereisten
U hebt een Azure-abonnement nodig om deze reeks zelf studies te volt ooien. U kunt eventueel een gratis proef versie van 7 dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken op de [Azure-aanmeldings pagina](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Een app maken 
In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de reeks zelf studies om een volledige oplossing te bouwen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Ga naar de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) .
1. Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt voor toegang tot de app. Als dat niet het geval is, meldt u zich aan met een Microsoft-account:

   ![Scherm afbeelding van het dialoog venster Microsoft-account-aanmelding](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**.

1. Selecteer **detail handel**.  Op de pagina Retail worden verschillende sjablonen voor de retail toepassing weer gegeven.

Een nieuw micro fulfillment Center-toepassing maken die gebruikmaakt van preview-functies:  
1. Selecteer de toepassings sjabloon **micro fulfillment Center** . Deze sjabloon bevat sjablonen voor alle apparaten die in de zelf studie worden gebruikt. De sjabloon biedt ook een operator dashboard voor het bewaken van voor waarden binnen het fulfillment Center en de voor waarden voor uw geautomatiseerde vervoerders. 

    ![Scherm opname van Azure IoT Central uw IoT-toepassings pagina bouwen](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. U kunt desgewenst een beschrijvende **toepassings naam**kiezen. De toepassings sjabloon is gebaseerd op het fictieve bedrijf North Wind Traders. 

    >[!NOTE]
    >Als u een beschrijvende toepassings naam gebruikt, moet u nog steeds een unieke waarde voor de URL van de toepassing gebruiken.

1. Als u een Azure-abonnement hebt, voert u uw adres lijst, het Azure-abonnement en de regio in. Als u geen abonnement hebt, kunt u een gratis proef versie van 7 dagen inschakelen en de vereiste contact gegevens volt ooien.  

    Zie de Snelstartgids [een toepassing maken](../preview/quick-deploy-iot-central.md) voor meer informatie over directory's en abonnementen.

1. Selecteer **Maken**.

    ![Scherm afbeelding van de pagina nieuwe toepassing IoT Central van Azure](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Door loop de toepassing 

Nadat u de app-sjabloon hebt geïmplementeerd, ziet u het **dash board van het micro fulfillment Center van North Wind Traders**. North Wind Traders is een fictieve detail handel met een micro-fulfillment-centrum dat wordt beheerd in deze Azure IoT Central-toepassing. Op dit dash board van de operator ziet u informatie en telemetrie over de apparaten in deze sjabloon, samen met een set opdrachten, taken en acties die u kunt uitvoeren. Het dash board is logisch gesplitst in twee secties. Aan de linkerkant kunt u de omgevings voorwaarden in de fulfillment-structuur bewaken en aan de rechter kant kunt u de status van een automatische provider in de faciliteit bewaken.  

Vanuit het dash board kunt u het volgende doen:
   * Zie telemetrie van apparaten, zoals het aantal picks, het aantal verwerkte orders en eigenschappen, zoals de status van het structuur systeem.  
   * Bekijk het vloer plan en de locatie van de geautomatiseerde vervoerders binnen de fulfillment-structuur.
   * Trigger opdrachten, zoals het opnieuw instellen van het besturings systeem, het bijwerken van de firmware van de provider en het opnieuw configureren van het netwerk.

     ![Scherm afbeelding van het micro fulfillment Center-dash board van noorden wind](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Bekijk een voor beeld van het dash board dat een operator kan gebruiken om de voor waarden in het fulfillment Center te bewaken. 
   * Controleer de status van de nettoladingen die worden uitgevoerd op het gateway apparaat binnen het fulfillment-centrum.    

     ![Scherm afbeelding van het micro fulfillment Center-dash board van noorden wind](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Apparaatprofiel
Als u het tabblad Device templates selecteert, ziet u dat er twee verschillende typen apparaten zijn die deel uitmaken van de sjabloon: 
   * **Robot-provider**: deze Device-sjabloon vertegenwoordigt de definitie voor een werkende automatische provider die is geïmplementeerd in de verwerkings structuur, en voert de juiste opslag-en ophaal bewerkingen uit. Als u de sjabloon selecteert, ziet u dat de robot apparaatgegevens verzendt, zoals de positie van de Tempe ratuur en de as en eigenschappen, zoals de status van de geautomatiseerde transporteur. 
   * **Bewaking van de structuur voorwaarde**: deze Device-sjabloon vertegenwoordigt een verzameling apparaten waarmee u de omgevings voorwaarde kunt bewaken, evenals het gateway apparaat dat als host fungeert voor de verschillende werk belasting van de rand om uw fulfillment Center te verbeteren. Het apparaat verzendt telemetriegegevens, zoals de Tempe ratuur, het aantal picks en het aantal orders. Er wordt ook informatie verzonden over de status en status van de reken werkbelastingen die in uw omgeving worden uitgevoerd. 

     ![Micro-fulfillment Center-sjablonen voor apparaten](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Als u het tabblad apparaatgroepen selecteert, kunt u ook zien dat voor deze Apparaatinstellingen automatisch apparaatgroepen worden gemaakt.

## <a name="rules"></a>Regels
Op het tabblad **regels** ziet u een voorbeeld regel die voor komt in de toepassings sjabloon om de temperatuur voorwaarden voor de automatische provider te controleren. U kunt deze regel gebruiken om de operator te waarschuwen als een specifieke robot in de faciliteit oververhit is en offline moet worden gehaald voor onderhoud. 

Gebruik de voorbeeld regel als inspiratie om regels te definiëren die geschikter zijn voor uw bedrijfs functies.

![Scherm afbeelding van het tabblad regels](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u de toepassings sjabloon. Ga naar **beheer** > **Toepassings instellingen**en selecteer **verwijderen**.

![Scherm afbeelding van de pagina Toepassings instellingen van micro fulfill Center](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [oplossings architectuur voor micro-fulfillment Center](./architecture-micro-fulfillment-center-pnp.md).
* Meer informatie over andere [sjablonen voor Azure IOT Central Retail](./overview-iot-central-retail-pnp.md).
* Lees het [overzicht van Azure IOT Central](../preview/overview-iot-central.md).
