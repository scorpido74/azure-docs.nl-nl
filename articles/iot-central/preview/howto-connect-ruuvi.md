---
title: Verbinding maken tussen een RuuviTag in azure IoT Central | Microsoft Docs
description: Meer informatie over hoe u een RuuviTag-omgevings sensor verbindt met uw IoT Central-toepassing.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 9be8fbad8811d758f9ac4205c0d1e60e0d82e07f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895496"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Een RuuviTag-sensor verbinden met uw Azure IoT Central-toepassing

In dit artikel wordt beschreven hoe u, als een oplossings functie voor oplossingen, een RuuviTag-sensor kunt verbinden met uw Microsoft Azure IoT Central toepassing.

Wat is een Ruuvi-tag?

RuuviTag is een geavanceerd open-source sensor baken platform dat is ontworpen om te voldoen aan de behoeften van zakelijke klanten, ontwikkel aars, makers, studenten en hobbyisten. Het apparaat is zo ingesteld dat het goed werkt wanneer u het uit de doos haalt en u klaar bent om het te implementeren waar u het nodig hebt. Het is een Bluetooth LE Beacon met een omgevings sensor en een ingebouwde versnellings meter.

RuuviTag communiceert over een filiaal (Bluetooth Low Energy) en vereist een gateway apparaat om te praten met Azure IoT Central. Zorg ervoor dat u een gateway apparaat hebt, zoals Rigado Cascade 500, Setup om een RuuviTag in te scha kelen om verbinding te maken met IoT Central.

Volg de [onderstaande instructies](./howto-connect-rigado-cascade-500.md) als u een Rigado trapsgewijs 500-gateway apparaat wilt instellen.

## <a name="prerequisites"></a>Vereisten

Als u RuuviTag Sens oren wilt verbinden, hebt u de volgende resources nodig:

* Een RuuviTag-sensor. Ga naar [RuuviTag](https://ruuvi.com/)voor meer informatie.
* Een Rigado-trapsgewijs 500-apparaat of een andere uitstaande gateway. Ga naar [Rigado](https://www.rigado.com/)voor meer informatie.
* Een Azure IoT Central-toepassing die is gemaakt op basis van een van de preview-toepassings sjablonen. Zie voor meer informatie de [nieuwe toepassing maken](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Een sjabloon voor een RuuviTag-apparaat toevoegen

Als u een RuuviTag-sensor wilt toevoegen aan uw Azure IoT Central-toepassings exemplaar, moet u een bijbehorende apparaatprofiel in uw toepassing configureren.

Een sjabloon voor een RuuviTag-apparaat toevoegen:

1. Ga naar het tabblad ***device templates*** in het linkerdeel venster en selecteer **+ Nieuw**: ![nieuwe sjabloon maken](./media/howto-connect-ruuvi/devicetemplate-new.png) de pagina bevat een optie voor het ***maken van een aangepaste sjabloon*** of voor het gebruik van ***een vooraf geconfigureerde apparaatprofiel***
1. Selecteer de sjabloon RuuviTag in de lijst met vooraf geconfigureerde Apparaatinstellingen, zoals hieronder wordt weer gegeven: ![Selecteer RuuviTag Device Temp late](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Selecteer ***volgende: aanpassen*** om door te gaan naar de volgende stap.
1. Selecteer in het volgende scherm de optie ***maken*** om de C500-apparaatprofiel voor te bereiden in uw IOT Central-toepassing.

## <a name="connect-a-ruuvitag-sensor"></a>Een RuuviTag-sensor verbinden

Zoals eerder vermeld, moet u een gateway apparaat instellen om de RuuviTag te verbinden met uw IoT Central-toepassing. In de onderstaande stappen wordt ervan uitgegaan dat u een Rigado Cascade 500-gateway apparaat hebt ingesteld.  

1. Schakel uw Rigado Cascade 500-apparaat uit en sluit het aan op uw netwerk verbinding (via Ethernet of draadloos)
1. Pop de bedekking van de RuuviTag en haal het tabblad plastic op om de verbinding met de accu te beveiligen.
1. Plaats de RuuviTag dicht bij een Rigado Cascade 500-gateway die al in uw IoT Central-toepassing is geconfigureerd.
1. Binnen een paar seconden moet uw RuuviTag worden weer gegeven in de lijst met apparaten in IoT Central.  
    ![Lijst met RuuviTag apparaten](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

U kunt deze RuuviTag nu gebruiken binnen uw IoT Central-toepassing.  

## <a name="create-a-simulated-ruuvitag"></a>Een gesimuleerde RuuviTag maken

Als u geen fysiek RuuviTag-apparaat hebt, kunt u een gesimuleerde RuuviTag-sensor maken die moet worden gebruikt voor het testen van uw Azure IoT Central-toepassing.

Een gesimuleerde RuuviTag maken:

1. Selecteer **apparaten > RuuviTag**.
1. Selecteer **+ Nieuw**.
1. Geef een unieke **apparaat-id** en een beschrijvende **apparaatnaam**op.  
1. Schakel de **gesimuleerde** instelling in.
1. Selecteer **Maken**.  

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een RuuviTag verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [aanpassen van uw IOT Central toepassing](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) om een end-to-end-oplossing te bouwen.
