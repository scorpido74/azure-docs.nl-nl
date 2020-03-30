---
title: Zelfstudie voor sjabloon voor micro-fulfillment center-apps | Microsoft Documenten
description: Een zelfstudie over de toepassingssjabloon voor het micro-fulfillmentcenter voor Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 93906f582f1edc351088f8b4c453bf9ebda54f83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77369822"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Zelfstudie: Een toepassingssjabloon voor micro-fulfillment center implementeren en doorlopen

In deze zelfstudie gebruikt u de azure IoT Central-toepassingssjabloon voor microafhandelingscentrum om een retailoplossing te bouwen. U leert hoe u de sjabloon implementeert, wat erin is opgenomen en wat u vervolgens wilt doen.

## <a name="prerequisites"></a>Vereisten
Als u deze zelfstudiereeks wilt voltooien, hebt u een Azure-abonnement nodig. U optioneel een gratis proefperiode van 7 dagen gebruiken. Als u geen Azure-abonnement hebt, u er een maken op de [aanmeldingspagina van Azure.](https://aka.ms/createazuresubscription)

## <a name="create-an-application"></a>Een app maken 
In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de hele zelfstudiereeks om een complete oplossing te bouwen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Ga naar de website [azure IoT Central-toepassingsbeheer.](https://aka.ms/iotcentral)
1. Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om toegang te krijgen. Meld u anders aan met een Microsoft-account:

   ![Schermafbeelding van het aanmeldingsdialoogvenster van Microsoft-account](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**.

1. Selecteer **Detailhandel**.  Op de retailpagina worden verschillende retailtoepassingssjablonen weergegeven.

Ga als lid van het volgende over een nieuwe toepassing voor microafhandelingscentrum met voorbeeldfuncties:  
1. Selecteer de **toepassingssjabloon Micro-fulfillment center.** Deze sjabloon bevat apparaatsjablonen voor alle apparaten die in de zelfstudie worden gebruikt. De sjabloon biedt ook een operator dashboard voor het bewaken van de omstandigheden binnen uw fulfillment center, evenals de voorwaarden voor uw robotdragers. 

    ![Schermafbeelding van Azure IoT Central Uw IoT-toepassingspagina bouwen](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Kies eventueel een vriendelijke **toepassingsnaam.** De applicatie sjabloon is gebaseerd op de fictieve bedrijf Northwind Traders. 

    >[!NOTE]
    >Als u een vriendelijke toepassingsnaam gebruikt, moet u nog steeds een unieke waarde gebruiken voor de URL van de toepassing.

1. Als u een Azure-abonnement hebt, voert u uw directory, Azure-abonnement en regio in. Als u geen abonnement hebt, u een gratis proefperiode van 7 dagen inschakelen en de vereiste contactgegevens invullen.  

    Zie Snel aan de [slag](../preview/quick-deploy-iot-central.md) voor meer informatie over mappen en abonnementen.

1. Selecteer **Maken**.

    ![Schermafbeelding van de nieuwe toepassingspagina van Azure IoT Central](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Loop door de applicatie 

Nadat u de app-sjabloon met succes hebt geïmplementeerd, ziet u het dashboard van het **Microfulfillment Center van Northwind Traders.** Northwind Traders is een fictieve retailer die een micro-fulfillment center heeft dat wordt beheerd in deze Azure IoT Central-toepassing. Op dit operatordashboard ziet u informatie en telemetrie over de apparaten in deze sjabloon, samen met een reeks opdrachten, taken en acties die u uitvoeren. Het dashboard is logisch verdeeld in twee secties. Aan de linkerkant u de omgevingsomstandigheden binnen de vervullingsstructuur controleren en aan de rechterkant u de gezondheid van een robotdrager binnen de faciliteit controleren.  

Vanuit het dashboard u:
   * Zie telemetrie van het apparaat, zoals het aantal picks, het aantal verwerkte orders en eigenschappen, zoals de status van het structuursysteem.  
   * Bekijk de plattegrond en locatie van de robotdragers binnen de vervullingsstructuur.
   * Triggeropdrachten, zoals het opnieuw instellen van het besturingssysteem, het bijwerken van de firmware van de provider en het opnieuw configureren van het netwerk.

     ![Schermafbeelding van het dashboard van het Northwind Traders micro-fulfillment center](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Zie een voorbeeld van het dashboard dat een operator kan gebruiken om de omstandigheden in het fulfillmentcenter te controleren. 
   * Controleer de status van de payloads die worden uitgevoerd op het gateway-apparaat in het fulfillmentcenter.    

     ![Schermafbeelding van het dashboard van het Northwind Traders micro-fulfillment center](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Apparaatsjabloon
Als u het tabblad apparaatsjablonen selecteert, ziet u dat er twee verschillende apparaattypen zijn die deel uitmaken van de sjabloon: 
   * **Robotic Carrier:** Deze apparaatsjabloon vertegenwoordigt de definitie voor een functionerende robotdrager die is geïmplementeerd in de fulfillmentstructuur en de juiste opslag- en ophaalbewerkingen uitvoert. Als u de sjabloon selecteert, ziet u dat de robot apparaatgegevens verzendt, zoals temperatuur en aspositie, en eigenschappen zoals de status van de robotdrager. 
   * **Structuurvoorwaardebewaking:** deze apparaatsjabloon vertegenwoordigt een apparaatverzameling waarmee u de toestand van de omgeving controleren, evenals het gatewayapparaat dat verschillende randworkloads host om uw fulfillmentcenter van stroom te voorzien. Het apparaat verzendt telemetriegegevens, zoals de temperatuur, het aantal picks en het aantal orders. Het stuurt ook informatie over de status en status van de compute-workloads die in uw omgeving worden uitgevoerd. 

     ![Apparaatsjablonen voor microafhandelingscentrum](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Als u het tabblad apparaatgroepen selecteert, ziet u ook dat er automatisch apparaatgroepen voor deze apparaatsjablonen zijn gemaakt.

## <a name="rules"></a>Regels
Op het tabblad **Regels** ziet u een voorbeeldregel die bestaat in de toepassingssjabloon om de temperatuuromstandigheden voor de robotdrager te controleren. U deze regel gebruiken om de operator te waarschuwen als een specifieke robot in de faciliteit oververhit raakt en offline moet worden gehaald voor onderhoud. 

Gebruik de voorbeeldregel als inspiratie om regels te definiëren die beter geschikt zijn voor uw bedrijfsfuncties.

![Schermafbeelding van het tabblad Regels](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u de toepassingssjabloon. Ga naar **instellingen voor beheertoepassingen** > **Application settings**en selecteer **Verwijderen**.

![Schermafbeelding van de pagina Toepassingsinstellingen voor microafhandelingscentrum](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de architectuur van de [oplossing seinen voor micro-fulfilmentcenters](./architecture-micro-fulfillment-center-pnp.md).
* Meer informatie over andere [Azure IoT Central-retailsjablonen](./overview-iot-central-retail-pnp.md).
* Lees het [azure IoT Central-overzicht](../preview/overview-iot-central.md).
