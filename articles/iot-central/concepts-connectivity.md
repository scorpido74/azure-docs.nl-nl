---
title: Connectiviteit van apparaten in azure IoT Central | Microsoft Docs
description: In dit artikel vindt u belang rijke concepten met betrekking tot de connectiviteit van apparaten in azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0088cc2c2bd30748a8a62217c76f962dd1b174f8
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019991"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connectiviteit van apparaten in azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

In dit artikel vindt u belang rijke concepten met betrekking tot de connectiviteit van apparaten in Microsoft Azure IoT Central.

Azure IoT Central maakt gebruik van de [azure IOT hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) om alle apparaatregistratie en-verbinding te beheren.

Met DPS kunt u het volgende doen:

- IoT Central voor het ondersteunen van onboarding en het verbinden van apparaten op schaal.
- U kunt de referenties van het apparaat genereren en de apparaten offline configureren zonder de apparaten te registreren via IoT Central-gebruikers interface.
- Apparaten om verbinding te maken met behulp van Shared Access signatures (SAS).
- Apparaten om verbinding te maken met behulp van de industrie standaard X. 509-certificaten.
- U kunt uw eigen apparaat-Id's gebruiken om apparaten te registreren in IoT Central. Het gebruik van uw eigen apparaat-Id's vereenvoudigt de integratie met bestaande back-office-systemen.
- Een enkele, consistente manier om apparaten te verbinden met IoT Central.

In dit artikel worden de volgende vier gebruiks voorbeelden beschreven:

1. [Snel verbinding maken met één apparaat met SAS](#connect-a-single-device)
1. [Apparaten op schaal aansluiten met SAS](#connect-devices-at-scale-using-sas)
1. [Apparaten op schaal aansluiten met X. 509-certificaten](#connect-devices-using-x509-certificates) dit is de aanbevolen benadering voor productie omgevingen.
1. [Verbinding maken zonder eerst apparaten te registreren](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Eén apparaat verbinden

Deze aanpak is nuttig wanneer u experimenteert met IoT Central of apparaten testen. U kunt de verbindings gegevens van het apparaat gebruiken van uw IoT Central-toepassing om de connection string voor een apparaat te genereren. Zie [How to generate a device Connection String om verbinding te maken met een Azure IOT Central-toepassing](howto-generate-connection-string.md)voor gedetailleerde stappen.

## <a name="connect-devices-at-scale-using-sas"></a>Apparaten op schaal aansluiten met SAS

Als u apparaten wilt verbinden met IoT Central op schaal met behulp van SAS, moet u de apparaten registreren en vervolgens instellen:

### <a name="register-devices-in-bulk"></a>Apparaten bulksgewijs registreren

Als u een groot aantal apparaten wilt registreren bij uw IoT Central-toepassing, gebruikt u een CSV-bestand om [apparaat-id's en apparaatnamen te importeren](howto-manage-devices.md#import-devices).

Als u de verbindings gegevens voor de geïmporteerde apparaten wilt ophalen, [exporteert u een CSV-bestand van uw IOT Central-toepassing](howto-manage-devices.md#export-devices).

> [!NOTE]
> Zie [verbinding maken zonder eerst apparaten te registreren](#connect-without-registering-devices)voor meer informatie over het verbinden van apparaten zonder deze eerst te registreren in IOT Central.

### <a name="set-up-your-devices"></a>Uw apparaten instellen

Gebruik de verbindings gegevens van het export bestand in uw apparaatcode om uw apparaten in staat te stellen verbinding te maken en gegevens naar IoT te verzenden naar uw IoT Central-toepassing. Zie [volgende stappen](#next-steps)voor meer informatie over het verbinden van apparaten.

## <a name="connect-devices-using-x509-certificates"></a>Apparaten verbinden met X. 509-certificaten

In een productie omgeving, met behulp van X. 509-certificaten is het aanbevolen mechanisme voor verificatie van apparaten voor IoT Central. Zie voor meer informatie [apparaat-verificatie met behulp van X. 509 CA-certificaten](../iot-hub/iot-hub-x509ca-overview.md).

In de volgende stappen wordt beschreven hoe u apparaten verbindt met IoT Central met behulp van X. 509-certificaten:

1. Voeg in uw IoT Central-toepassing _het tussenliggende of basis X. 509-certificaat toe_ dat u gebruikt voor het genereren van apparaat certificaten:

    - Ga naar **beheer > apparaat verbinding > certificaten (X. 509)** en voeg het basis-of tussenliggende certificaat van X. 509 toe dat u gebruikt voor het genereren van de certificaten van het Leaf-apparaat.

      ![Verbindingsinstellingen](media/concepts-connectivity/connection-settings.png)

      Als u een inbreuk op de beveiliging hebt of als uw primaire certificaat is ingesteld op verlopen, gebruikt u het secundaire certificaat om de downtime te verminderen. U kunt door gaan met het inrichten van apparaten met behulp van het secundaire certificaat wanneer u het primaire certificaat bijwerkt.

    - Verifiëren van eigendom van het certificaat zorgt ervoor dat de uploader van het certificaat de persoonlijke sleutel van het certificaat bevat. Het certificaat verifiëren:
        - Selecteer de knop naast **verificatie code** om een code te genereren.
        - Maak een X. 509-verificatie certificaat met de verificatie code die u in de vorige stap hebt gegenereerd. Sla het certificaat op als een CER-bestand.
        - Upload het ondertekende verificatie certificaat en selecteer **verifiëren**.

          ![Verbindingsinstellingen](media/concepts-connectivity/verify-cert.png)

1. Gebruik een CSV-bestand om _apparaten te importeren en registreren_ in uw IOT Central-toepassing.

1. _Stel uw apparaten in._ Genereer de blad certificaten met het geüploade basis certificaat. Gebruik de **apparaat-id** als de CNAME-waarde in de blad certificaten. De apparaat-ID mag niet alleen uit kleine letters bestaan. Programeer vervolgens uw apparaten met inrichtings service-informatie. Wanneer een apparaat voor het eerst wordt ingeschakeld, haalt het de verbindings gegevens voor uw IoT Central-toepassing op uit DPS.

### <a name="further-reference"></a>Aanvullende Naslag informatie

- Voorbeeld implementatie voor [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Voor beeld van apparaatclient in C.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Alleen voor test doeleinden

Voor alleen testen kunt u deze hulpprogram ma's gebruiken voor het genereren van CA-certificaten en apparaat certificaten.

- Als u een DevKit-apparaat gebruikt, wordt met dit [opdracht regel programma](https://aka.ms/iotcentral-docs-dicetool) een CA-certificaat gegenereerd dat u aan uw IOT Central-toepassing kunt toevoegen om de certificaten te controleren.

- Gebruik dit [opdracht regel programma](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) voor het volgende:
  - Maak een certificaat keten. Volg stap 2 in het GitHub-artikel.
  - Sla de certificaten op als CER-bestanden die u wilt uploaden naar uw IoT Central-toepassing.
  - Gebruik de verificatie code uit de IoT Central toepassing om het verificatie certificaat te genereren. Volg stap 3 in het GitHub-artikel.
  - Maak blad certificaten voor uw apparaten met behulp van uw apparaat-Id's als een para meter voor het hulp programma. Volg stap 4 in het GitHub-artikel.

## <a name="connect-without-registering-devices"></a>Verbinding maken zonder apparaten te registreren

Met een belang rijk scenario IoT Central kunnen Oem's apparaten massaal produceren die verbinding kunnen maken met een IoT Central toepassing zonder eerst te worden geregistreerd. Een fabrikant moet de juiste referenties genereren en de apparaten in de fabriek configureren. Wanneer een apparaat voor de eerste keer wordt ingeschakeld, wordt automatisch verbinding gemaakt met een IoT Central-toepassing. Een IoT Central-operator moet het apparaat goed keuren voordat het een Statie gegevens kan verzenden.

In het volgende diagram wordt deze stroom beschreven:

![Verbindingsinstellingen](media/concepts-connectivity/device-connection-flow1.png)

In de volgende stappen wordt dit proces uitvoeriger beschreven. De stappen variëren enigszins, afhankelijk van of u SAS of X. 509-certificaten gebruikt voor het verifiëren van apparaten:

1. Configureer uw Verbindings instellingen:

    - **X. 509-certificaten:** [Voeg het hoofd-/tussenliggende certificaat toe en controleer](#connect-devices-using-x509-certificates) het en gebruik het om de certificaten in de volgende stap te genereren.
    - **SAS:** Kopieer de primaire sleutel. Deze sleutel is de groeps-SAS-sleutel voor de IoT Central-toepassing. Gebruik de sleutel om de SAS-sleutels van het apparaat in de volgende stap te genereren.
    ![Verbindings instellingen SAS](media/concepts-connectivity/connection-settings-sas.png)

1. De referenties van uw apparaat genereren
    - **Certificaten X. 509:** Genereer de blad certificaten voor uw apparaten met behulp van het basis-of tussenliggende certificaat dat u aan uw IoT Central-toepassing hebt toegevoegd. Zorg ervoor dat u de **apparaat-id** met kleine letters gebruikt als de CNAME in de blad certificaten. Voor test doeleinden gebruikt u dit [opdracht regel programma](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) voor het genereren van apparaat certificaten.
    - **SAS:** Gebruik dit [opdracht regel programma](https://www.npmjs.com/package/dps-keygen) om SAS-sleutels voor apparaten te genereren. Gebruik de **primaire sleutel** van de groep uit de vorige stap. De apparaat-ID moet kleine letters zijn.

      Voer de volgende opdracht uit om het [hulp programma voor sleutel Generator](https://github.com/Azure/dps-keygen)te installeren:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Als u een apparaatcode wilt genereren op basis van de primaire sleutel van de groep SA'S, voert u de volgende opdracht uit:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Als u uw apparaten wilt instellen, moet u elk apparaat flashen met de **scope-id**, **apparaat-id**en **X. 509-certificaat** of **SAS-sleutel**.

1. Schakel vervolgens het apparaat in om verbinding te maken met uw IoT Central-toepassing. Wanneer u overschakelt op een apparaat, maakt het eerst verbinding met DPS om de IoT Central registratie gegevens op te halen.

1. Het verbonden apparaat wordt in eerste instantie weer gegeven als een niet- **gekoppeld apparaat** op de pagina **device Explorer** . De inrichtings status van het apparaat is **geregistreerd**. **Koppel** het apparaat aan de juiste Device-sjabloon en keur het apparaat goed om verbinding te maken met uw IOT Central-toepassing. Het apparaat kan vervolgens een connection string ophalen van IoT Hub en beginnen met het verzenden van gegevens. Het inrichten van apparaten is nu voltooid en de inrichtings status is nu **ingericht**.

## <a name="provisioning-status"></a>Inrichtingsstatus

Wanneer een echt apparaat verbinding maakt met uw IoT Central-toepassing, wordt de inrichtings status als volgt gewijzigd:

1. De inrichtings status van het apparaat is voor het eerst **geregistreerd**. Deze status betekent dat het apparaat wordt gemaakt in IoT Central en een apparaat-ID heeft. Een apparaat wordt geregistreerd wanneer:
    - Er wordt een nieuw echt apparaat toegevoegd op de pagina **device Explorer** .
    - Er wordt een set apparaten toegevoegd met behulp van **importeren** op de pagina **device Explorer** .
    - Een apparaat is niet hand matig geregistreerd op de **device Explorer** pagina, maar is verbonden met geldige referenties en wordt weer gegeven als een niet- **gekoppeld** apparaat op de **device Explorer** pagina.

1. De inrichtings status van het apparaat wordt gewijzigd in **ingericht** wanneer het apparaat dat is verbonden met uw IOT Central-toepassing met geldige referenties de inrichtings stap heeft voltooid. In deze stap haalt het apparaat een connection string op uit IoT Hub. Het apparaat kan nu verbinding maken met IoT Hub en beginnen met het verzenden van gegevens.

1. Een operator kan een apparaat blok keren. Wanneer een apparaat is geblokkeerd, kunnen er geen gegevens worden verzonden naar uw IoT Central-toepassing. Geblokkeerde apparaten hebben de inrichtings status **geblokkeerd**. Een operator moet het apparaat opnieuw instellen om het verzenden van gegevens te hervatten. Wanneer een operator de blok kering van een apparaat opheffen, wordt de inrichtings status teruggezet naar de vorige waarde, **geregistreerd** of **ingericht**.

## <a name="sdk-support"></a>SDK-ondersteuning

De Sdk's van het Azure-apparaat bieden u de eenvoudigste manier voor het implementeren van uw apparaatcode. De volgende Sdk's voor apparaten zijn beschikbaar:

- [Azure IoT SDK voor C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK voor python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK voor node. js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK voor Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK voor .NET](https://github.com/azure/azure-iot-sdk-csharp)

Elk apparaat maakt verbinding met behulp van een unieke connection string waarmee het apparaat wordt geïdentificeerd. Een apparaat kan alleen verbinding maken met de IoT-hub als het is geregistreerd. Wanneer u een echt apparaat in uw Azure IoT Central-toepassing maakt, genereert de toepassing de informatie die u nodig hebt om een `dps-keygen`Connection String te maken met.

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-functies en IoT Hub connectiviteit

Alle communicatie van apparaten met IoT Hub gebruikt de volgende IoT Hub connectiviteits opties:

- [Apparaat-naar-Cloud-berichten](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Apparaat apparaatdubbels](../iot-hub/iot-hub-devguide-device-twins.md)

De volgende tabel bevat een overzicht van de manier waarop functies van Azure IoT Central-apparaten worden toegewezen aan IoT Hub-functies:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Maten Telemetrie | Apparaat-naar-Cloud-berichten |
| Apparaateigenschappen | Dubbele gerapporteerde eigenschappen van het apparaat |
| Instellingen | Dubbele gewenste en gerapporteerde eigenschappen van het apparaat |

Voor meer informatie over het gebruik van de apparaat-Sdk's raadpleegt u een van de volgende artikelen voor voorbeeld code:

- [Een generieke Node.js-client verbinden met uw Azure IoT Central-toepassing](howto-connect-nodejs.md)
- [Een Raspberry Pi-apparaat verbinden met uw Azure IoT Central-toepassing](howto-connect-raspberry-pi-python.md)
- [Verbind een DevDiv Kit-apparaat met uw Azure IOT Central-toepassing](howto-connect-devkit.md).

### <a name="protocols"></a>Protocollen

De Sdk's van het apparaat ondersteunen de volgende netwerk protocollen om verbinding te maken met een IoT-hub:

- MQTT
- AMQP
- HTTPS

Zie voor meer informatie over deze protocollen en richt lijnen voor het kiezen van een [communicatie protocol](../iot-hub/iot-hub-devguide-protocols.md).

Als uw apparaat een van de ondersteunde protocollen niet kan gebruiken, kunt u Azure IoT Edge gebruiken om Protocol conversie uit te voeren. IoT Edge ondersteunt andere intelligentie-in-Edge-scenario's voor het verenigen van de verwerking naar de rand vanuit de Azure IoT Central-toepassing.

## <a name="security"></a>Beveiliging

Alle gegevens die worden uitgewisseld tussen apparaten en uw Azure-IoT Central, worden versleuteld. IoT Hub verifieert elke aanvraag van een apparaat dat verbinding maakt met een van de IoT Hub-eind punten van het apparaat. Om te voor komen dat referenties via de kabel worden uitgewisseld, gebruikt een apparaat ondertekende tokens om te verifiëren. Zie [toegang tot IOT hub beheren](../iot-hub/iot-hub-devguide-security.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over de connectiviteit van apparaten in azure IoT Central, zijn dit de voorgestelde volgende stappen:

- [Een DevKit-apparaat voorbereiden en aansluiten](howto-connect-devkit.md)
- [Raspberry Pi voorbereiden en verbinden](howto-connect-raspberry-pi-python.md)
- [Een generieke Node.js-client verbinden met uw Azure IoT Central-toepassing](howto-connect-nodejs.md)
- [C SDK: Client-SDK voor apparaat inrichten](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
