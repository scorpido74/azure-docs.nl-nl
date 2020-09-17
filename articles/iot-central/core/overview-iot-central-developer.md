---
title: Ontwikkeling van apparaten voor Azure IoT Central | Microsoft Docs
description: Azure IoT Central is een IoT-toepassingsplatform waarmee het eenvoudiger is om IoT-oplossingen te maken. Dit artikel bevat een overzicht van het ontwikkelen van apparaten om verbinding te maken met uw IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 6fabd7d8cf5c19f05bd31c2d0b12863fd6e25382
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017520"
---
# <a name="iot-central-device-development-overview"></a>Overzicht van het ontwikkelen van IoT Central-apparaten

*Dit artikel is bedoeld voor ontwikkelaars van apparaten.*

Met een IoT Central-toepassing kunt u miljoenen apparaten tijdens hun levenscyclus bewaken en beheren. Dit overzicht is bedoeld voor ontwikkelaars van apparaten die code implementeren om uit te voeren op apparaten die verbinding maken met IoT Central.

Apparaten communiceren met een IoT Central-toepassing met behulp van de volgende primitieven:

- _Telemetrie_ zijn de gegevens die een apparaat naar IoT Central verzendt. Bijvoorbeeld een stroom van de temperatuurwaarden van een ingebouwde sensor.
- _Eigenschappen_ zijn statuswaarden die een apparaat rapporteert aan IoT Central. Bijvoorbeeld de huidige firmwareversie van het apparaat. U kunt ook schrijfbare eigenschappen hebben die door IoT Central op het apparaat kunnen worden bijgewerkt.
- _Opdrachten_ worden aangeroepen vanuit IoT Central om het gedrag van een apparaat te bepalen. Uw IoT Central-toepassing kan bijvoorbeeld een opdracht aanroepen om een apparaat opnieuw op te starten.

Een oplossingsbouwer is verantwoordelijk voor de configuratie van dashboards en weergaven in de webgebruikersinterface van IoT Central om telemetrie te visualiseren, eigenschappen te beheren en opdrachten aan te roepen.

## <a name="types-of-device"></a>Apparaattypen

In de volgende secties worden de belangrijkste apparaattypen beschreven die u kunt verbinden met een IoT Central-toepassing:

### <a name="standalone-device"></a>Zelfstandig apparaat

Een zelfstandig apparaat maakt rechtstreeks verbinding met IoT Central. Een zelfstandig apparaat verzendt doorgaans telemetriegegevens vanaf de ingebouwde of verbonden sensoren naar uw IoT Central-toepassing. Zelfstandige apparaten kunnen ook eigenschapswaarden rapporteren, schrijfbare eigenschapswaarden ontvangen en reageren op opdrachten.

### <a name="gateway-device"></a>Gatewayapparaat

Een gatewayapparaat beheert een of meer downstreamapparaten die verbinding maken met uw IoT Central-toepassing. U gebruikt IoT Central voor de configuratie van de relaties tussen de downstreamapparaten en het gatewayapparaat. Zie [Define a new IoT gateway device type in your Azure IoT Central application](./tutorial-define-gateway-device-type.md) (Een nieuw IoT-gatewayapparaattype definiëren in uw Azure IoT Central-toepassing) voor meer informatie.

### <a name="edge-device"></a>Edge-apparaat

Een edge-apparaat maakt rechtstreeks verbinding met IoT Central, maar fungeert als intermediair voor andere apparaten die _leaf-apparaten_ worden genoemd. Een edge-apparaat bevindt zich doorgaans dicht bij de leaf-apparaten waarvoor het fungeert als intermediair. Scenario's die gebruikmaken van edge-apparaten zijn onder andere:

- Apparaten inschakelen die niet rechtstreeks verbinding kunnen maken met IoT Central om verbinding te maken via het edge-apparaat. Een leaf-apparaat kan bijvoorbeeld gebruikmaken van Bluetooth om verbinding te maken met het edge-apparaat, dat vervolgens verbinding maakt via internet met IoT Central.
- Verzamel telemetrie voordat deze wordt verzonden naar IoT Central. Met deze aanpak kunt u de kosten van het verzenden van gegevens naar IoT Central verlagen.
- Beheer leaf-apparaten lokaal om de latentie te voorkomen die samenhangt met het maken van verbinding met IoT Central via internet.

Een edge-apparaat kan ook zijn eigen telemetrie verzenden, de eigenschappen ervan rapporteren en reageren op beschrijfbare updates van eigenschappen en opdrachten.

IoT Central ziet alleen het edge-apparaat, niet de leaf-apparaten die zijn verbonden met het edge-apparaat.

Zie [Add an Azure IoT Edge device to your Azure IoT Central application](./tutorial-add-edge-as-leaf-device.md) (Een Azure IoT Edge-apparaat toevoegen aan uw Azure IoT Central-toepassing) voor meer informatie.

## <a name="connect-a-device"></a>Een apparaat verbinden

Azure IoT Central maakt gebruik van de [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) om alle apparaatregistratie en -verbindingen te beheren.

Met DPS kunt u het volgende doen:

- IoT Central voor het ondersteunen van onboarding en op schaal verbinding van apparaten.
- U kunt de referenties van het apparaat genereren en de apparaten offline configureren zonder de apparaten te registreren via IoT Central-gebruikersinterface.
- U kunt uw eigen apparaat-id's gebruiken om apparaten te registreren in IoT Central. Het gebruik van uw eigen apparaat-id's vereenvoudigt de integratie met bestaande back-officesystemen.
- Een enkele, consistente manier om apparaten te verbinden met IoT Central.

Zie [Get connected to Azure IoT Central](./concepts-get-connected.md) (Verbinding maken met Azure IoT Central) voor meer informatie.

### <a name="security"></a>Beveiliging

De verbinding tussen een apparaat en uw IoT Central-toepassing wordt beveiligd met behulp van [handtekeningen voor gedeelde toegang](./concepts-get-connected.md#connect-devices-at-scale-using-sas) of de industriestandaard [X. 509-certificaten](./concepts-get-connected.md#connect-devices-using-x509-certificates).

### <a name="communication-protocols"></a>Communicatieprotocollen

Communicatieprotocollen die een apparaat kan gebruiken om verbinding te maken met IoT Central zijn MQTT, AMQP en HTTPS. Intern gebruikt IoT Central een IoT-hub om connectiviteit van apparaten in te schakelen. Zie [Een communicatieprotocol kiezen](../../iot-hub/iot-hub-devguide-protocols.md) voor meer informatie over de communicatieprotocollen die door IoT Hub worden ondersteund voor connectiviteit van apparaten.

## <a name="implement-the-device"></a>Het apparaat implementeren

Gebruik een van de [SDK's voor Azure IoT-apparaten](#languages-and-sdks) om het gedrag van uw apparaat te implementeren. De code moet:

- Het apparaat registreren met DPS en de informatie van DPS gebruiken om verbinding te maken met de interne IoT-hub in uw IoT Central-toepassing.
- Telemetrie verzenden in de indeling die door de apparaatsjabloon in IoT Central wordt opgegeven. IoT Central gebruikt de apparaatsjabloon om te bepalen hoe u de telemetrie gebruikt voor visualisaties en analyses.
- De eigenschapswaarden synchroniseren tussen het apparaat en IoT Central. In de apparaatsjabloon worden de eigenschapsnamen en gegevenstypen opgegeven, zodat IoT Central de informatie kan weergeven.
- Opdrachthandlers implementeren voor de opdrachten die worden opgegeven in de apparaatsjabloon. In de apparaatsjabloon worden de opdrachtnamen en parameters opgegeven die het apparaat moet gebruiken.

Zie [Wat zijn apparaatsjablonen?](./concepts-device-templates.md) voor meer informatie over de rol van apparaatsjablonen.

Zie [Een Node.js-clienttoepassing maken en verbinden](./tutorial-connect-device-nodejs.md) of [Een Python-clienttoepassing maken en verbinden](./tutorial-connect-device-python.md) voor een voorbeeld van code.

### <a name="languages-and-sdks"></a>Talen en SDK's

Zie [Understand and use Azure IoT Hub device SDKs](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) (SDK's voor Azure IoT Hub-apparaten begrijpen en gebruiken) voor meer informatie over de ondersteunde talen en SDK's.

## <a name="next-steps"></a>Volgende stappen

Als u apparaatontwikkelaar bent en u meer wilt weten over coderen, kunt u het beste de stap [Create and connect a client application to your Azure IoT Central application](./tutorial-connect-device-nodejs.md) (Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing) volgen.

Als u meer wilt weten over het gebruik van IoT Central, kunt u de volgende stappen uitvoeren om de quickstarts te proberen, te beginnen met [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md).
