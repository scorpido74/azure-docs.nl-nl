---
title: Een RuuviTag aansluiten in Azure IoT Central | Microsoft Documenten
description: Meer informatie over het aansluiten van een RuuviTag-omgevingssensor op uw IoT Central-toepassing.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 93e4d3d0bed9090573d2b6ee87a29b86ccd72e42
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758952"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Een RuuviTag-sensor verbinden met uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op oplossingsbouwers en apparaatontwikkelaars.*

In dit artikel wordt beschreven hoe u als oplossingsbouwer een RuuviTag-sensor aansluiten op uw Microsoft Azure IoT Central-toepassing.

Wat is een Ruuvi tag?

RuuviTag is een geavanceerd open-source sensorbeaconplatform dat is ontworpen om te voldoen aan de behoeften van zakelijke klanten, ontwikkelaars, makers, studenten en hobbyisten. Het apparaat is ingesteld om te werken zodra u het uit de doos haalt en is klaar voor u om het te implementeren waar u het nodig hebt. Het is een Bluetooth LE-baken met een omgevingssensor en versnellingsmeter ingebouwd.

RuuviTag communiceert via BLE (Bluetooth Low Energy) en vereist een gateway-apparaat om met Azure IoT Central te praten. Zorg ervoor dat u een gateway-apparaat hebt, zoals de Rigado Cascade 500, die is ingesteld om een RuuviTag in staat te stellen verbinding te maken met IoT Central.

Volg hier de [instructies](./howto-connect-rigado-cascade-500.md) als u een Rigado Cascade 500-gateway-apparaat wilt instellen.

## <a name="prerequisites"></a>Vereisten

Als u RuuviTag-sensoren wilt aansluiten, hebt u de volgende bronnen nodig:

* Een RuuviTag sensor. Ga voor meer informatie naar [RuuviTag.](https://ruuvi.com/)
* Een Rigado Cascade 500-apparaat of een andere BLE-gateway. Voor meer informatie u terecht op [Rigado](https://www.rigado.com/).
* Een Azure IoT Central-toepassing. Zie het maken [van een nieuwe toepassing voor](./quick-deploy-iot-central.md)meer informatie.

## <a name="add-a-ruuvitag-device-template"></a>Een ruuvitag-apparaatsjabloon toevoegen

Als u een RuuviTag-sensor wilt ingebouwde in uw Azure IoT Central-toepassingsexemplaar, moet u een bijbehorende apparaatsjabloon in uw toepassing configureren.

Ga als lid van het ruuvitag-apparaat:

1. Navigeer naar het tabblad ***Apparaatsjablonen*** in het ![linkerdeelvenster,](./media/howto-connect-ruuvi/devicetemplate-new.png) selecteer **+ Nieuw:** Nieuwe apparaatsjabloon maken De pagina geeft u de optie om ***een aangepaste sjabloon*** te maken of een ***vooraf geconfigureerde apparaatsjabloon te gebruiken***
1. Selecteer de ruuvitag-apparaatsjabloon in de lijst met vooraf ![geconfigureerde apparaatsjablonen zoals hieronder weergegeven: RuuviTag-apparaatsjabloon selecteren](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Selecteer ***Volgende: Aanpassen*** om door te gaan naar de volgende stap.
1. Selecteer in het volgende scherm ***Maken*** om de c500-apparaatsjabloon aan boord te nemen in uw IoT Central-toepassing.

## <a name="connect-a-ruuvitag-sensor"></a>Een RuuviTag-sensor aansluiten

Zoals eerder vermeld, moet u een gateway-apparaat instellen om de RuuviTag met uw IoT Central-toepassing te verbinden. In de onderstaande stappen wordt ervan uitgegaan dat u een Rigado Cascade 500-gateway-apparaat hebt ingesteld.  

1. Werk aan op uw Rigado Cascade 500-apparaat en sluit deze aan op uw netwerkverbinding (via Ethernet of draadloos)
1. Pop de cover van de RuuviTag en trek het plastic tabblad om de verbinding met de batterij te beveiligen.
1. Plaats de RuuviTag in de buurt van een Rigado Cascade 500-gateway die al is geconfigureerd in uw IoT Central-toepassing.
1. In slechts een paar seconden moet uw RuuviTag worden weergegeven in uw lijst met apparaten binnen IoT Central.  
    ![RuuviTag-apparaatlijst](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

U deze RuuviTag nu gebruiken in uw IoT Central-toepassing.  

## <a name="create-a-simulated-ruuvitag"></a>Een gesimuleerde RuuviTag maken

Als u geen fysiek RuuviTag-apparaat hebt, u een gesimuleerde RuuviTag-sensor maken die u gebruiken voor het testen binnen uw Azure IoT Central-toepassing.

Ga als lid van het nieuws naar een gesimuleerde RuuviTag:

1. Selecteer **Apparaten > RuuviTag**.
1. Selecteer **+ Nieuw**.
1. Geef een unieke **apparaat-id** en een vriendelijke **apparaatnaam op.**  
1. Schakel de **instelling Gesimuleerd** in.
1. Selecteer **Maken**.  

## <a name="next-steps"></a>Volgende stappen

Als u een apparaatontwikkelaar bent, moeten enkele voorgestelde volgende stappen:

- Lees meer over [apparaatconnectiviteit in Azure IoT Central](./concepts-get-connected.md)
- Meer informatie over [het bewaken van apparaatconnectiviteit met Azure CLI](./howto-monitor-devices-azure-cli.md)
