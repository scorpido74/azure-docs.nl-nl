---
title: Connectiviteit van apparaten in azure IoT Central | Microsoft Docs
description: In dit artikel vindt u belang rijke concepten met betrekking tot de connectiviteit van apparaten in azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: dd38ba8feb2d424eb95e74df73100367ad49090c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954836"
---
# <a name="device-connectivity-in-azure-iot-central-preview-features"></a>Connectiviteit van apparaten in azure IoT Central (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In dit artikel vindt u belang rijke concepten met betrekking tot de connectiviteit van apparaten in Microsoft Azure IoT Central.

Azure IoT Central maakt gebruik van de [azure IOT hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) om alle apparaatregistratie en-verbinding te beheren.

Met DPS kunt u het volgende doen:

- IoT Central voor het ondersteunen van onboarding en het verbinden van apparaten op schaal.
- U kunt de referenties van het apparaat genereren en de apparaten offline configureren zonder de apparaten te registreren via IoT Central-gebruikers interface.
- Apparaten om verbinding te maken met behulp van Shared Access signatures (SAS).
- Apparaten om verbinding te maken met behulp van de industrie standaard X. 509-certificaten.
- U kunt uw eigen apparaat-Id's gebruiken om apparaten te registreren in IoT Central. Het gebruik van uw eigen apparaat-Id's vereenvoudigt de integratie met bestaande back-office-systemen.
- Een enkele, consistente manier om apparaten te verbinden met IoT Central.

In dit artikel worden de volgende gebruiks voorbeelden beschreven:

- [Snel verbinding maken met één apparaat met SAS](#connect-a-single-device)
- [Apparaten op schaal aansluiten met SAS](#connect-devices-at-scale-using-sas)
- [Apparaten op schaal aansluiten met X. 509-certificaten](#connect-devices-using-x509-certificates) dit is de aanbevolen benadering voor productie omgevingen.
- [Verbinding maken zonder eerst apparaten te registreren](#connect-without-registering-devices)
- [Apparaten verbinden met IoT Plug en Play-functies](#connect-devices-with-iot-plug-and-play)

## <a name="connect-a-single-device"></a>Eén apparaat verbinden

Deze aanpak is nuttig wanneer u experimenteert met IoT Central of apparaten testen. Met de Device Provisioning Service (DPS) kunt u de verbindings gegevens van uw IoT Central gebruiken om een apparaat te verbinden met uw IoT Central-toepassing. U kunt de client code voor voorbeeld DPS-apparaten vinden voor de volgende talen:

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

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

In een productie omgeving, met behulp van X. 509-certificaten is het aanbevolen mechanisme voor verificatie van apparaten voor IoT Central. Zie voor meer informatie [apparaat-verificatie met behulp van X. 509 CA-certificaten](../../iot-hub/iot-hub-x509ca-overview.md).

In de volgende stappen wordt beschreven hoe u apparaten verbindt met IoT Central met behulp van X. 509-certificaten:

1. Voeg in uw IoT Central-toepassing _het tussenliggende of basis X. 509-certificaat toe_ dat u gebruikt voor het genereren van apparaat certificaten:

    - Ga naar **beheer > apparaat verbinding > certificaten (X. 509)** en voeg het basis-of tussenliggende certificaat van X. 509 toe dat u gebruikt voor het genereren van de certificaten van het Leaf-apparaat.

      ![Verbindingsinstellingen](media/concepts-connectivity-pnp/connection-settings.png)

      Als u een inbreuk op de beveiliging hebt of als uw primaire certificaat is ingesteld op verlopen, gebruikt u het secundaire certificaat om de downtime te verminderen. U kunt door gaan met het inrichten van apparaten met behulp van het secundaire certificaat wanneer u het primaire certificaat bijwerkt.

    - Verifiëren van eigendom van het certificaat zorgt ervoor dat de uploader van het certificaat de persoonlijke sleutel van het certificaat bevat. Het certificaat verifiëren:
        - Selecteer de knop naast **verificatie code** om een code te genereren.
        - Maak een X. 509-verificatie certificaat met de verificatie code die u in de vorige stap hebt gegenereerd. Sla het certificaat op als een CER-bestand.
        - Upload het ondertekende verificatie certificaat en selecteer **verifiëren**.

          ![Verbindingsinstellingen](media/concepts-connectivity-pnp/verify-cert.png)

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

![Verbindingsinstellingen](media/concepts-connectivity-pnp/device-connection-flow1.png)

In de volgende stappen wordt dit proces uitvoeriger beschreven. De stappen variëren enigszins, afhankelijk van of u SAS of X. 509-certificaten gebruikt voor het verifiëren van apparaten:

1. Configureer uw Verbindings instellingen:

    - **X. 509-certificaten:** [Voeg het hoofd-/tussenliggende certificaat toe en controleer](#connect-devices-using-x509-certificates) dit en gebruik het voor het genereren van de apparaten in de volgende stap.
    - **SAS:** Kopieer de primaire sleutel. Deze sleutel is de groeps-SAS-sleutel voor de IoT Central-toepassing. Gebruik de sleutel om de SAS-sleutels van het apparaat in de volgende stap te genereren.
    SAS-](media/concepts-connectivity-pnp/connection-settings-sas.png) ![Verbindings instellingen

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

1. Het verbonden apparaat wordt in eerste instantie weer gegeven als niet- **gekoppeld** op de pagina **apparaten** . De inrichtings status van het apparaat is **geregistreerd**. **Migreer** het apparaat naar de juiste Device-sjabloon en keur het apparaat goed om verbinding te maken met uw IOT Central-toepassing. Het apparaat kan vervolgens een connection string ophalen van IoT Hub en beginnen met het verzenden van gegevens. Het inrichten van apparaten is nu voltooid en de inrichtings status is nu **ingericht**.

## <a name="individual-enrollment-based-device-connectivity"></a>Individuele inschrijving op basis van een apparaat

Voor klanten die apparaten verbinden die verificatie referenties hebben die per apparaat afzonderlijke inschrijving zijn, is de optie. Een afzonderlijke inschrijving is een vermelding voor één apparaat dat verbinding kan maken. Afzonderlijke inschrijvingen kunnen X. 509 Leaf-certificaten of SAS-tokens (van een fysieke of virtuele TPM) gebruiken als Attestation-mechanismen. De apparaat-ID (ook wel registratie-ID) in een afzonderlijke inschrijving is alfanumerieke tekens, kleine letters en kan afbreek streepjes bevatten. Meer informatie over afzonderlijke inschrijvingen [vindt u hier](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Wanneer u een afzonderlijke inschrijving voor een apparaat maakt, heeft dit voor rang op de standaard registraties op basis van de groeps inschrijving (SAS, x509) in uw app.

### <a name="creating-individual-enrollments"></a>Afzonderlijke inschrijvingen maken
IoT Central ondersteunt de volgende Attestation-mechanismen

1. **Attestation van symmetrische sleutels:** Symmetrische-sleutel attest is een eenvoudige benadering voor het verifiëren van een apparaat met een Device Provisioning service-exemplaar. Voor het maken van een afzonderlijke inschrijving met symmetrische sleutels. Open het dialoog venster verbinden, Selecteer individuele inschrijving en mechanisme "SA'S" en voer de primaire en secundaire sleutels in. SAS-sleutels moeten base64-gecodeerd zijn. Hier vindt u de [koppeling](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) naar code voorbeelden om uw apparaatcode te helpen bij het inrichten van apparaten met behulp van symmetrische sleutels en afzonderlijke inschrijvingen.
1. **X. 509-certificaten:** X. 509-certificaten als titel wordt voorgesteld door een certificaat gebaseerd Attestation-mechanisme, een uitstekende manier om productie te schalen. Als u een afzonderlijke registratie met symmetrische sleutels wilt maken, selecteert u afzonderlijke registratie en mechanisme "X. 509" en uploadt u de primaire en secundaire certificaten en slaat u de registratie op om de inschrijving te maken. Hier vindt u de [koppeling](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) naar code voorbeelden om uw apparaatcode te helpen bij het inrichten van apparaten met x509. Voor apparaats certificaten die worden gebruikt met een [afzonderlijke inschrijvings](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) vermelding moet de onderwerpnaam worden ingesteld op de apparaat-id (ook wel registratie-id) van de afzonderlijke inschrijvings vermelding.
1. **TPM-Attestation:** TPM staat voor Trusted Platform Module en is een type hardware Security module (HSM) en is een van de veiligste manieren om uw apparaten te verbinden.  In dit artikel wordt ervan uitgegaan dat u een afzonderlijke, firmware of geïntegreerde TPM gebruikt. Software geëmuleerde Tpm's zijn geschikt voor het maken van prototypen of tests, maar bieden geen hetzelfde beveiligings niveau als discrete, firmware of geïntegreerde Tpm's. Het is niet raadzaam om software Tpm's in productie te gebruiken. Als u een afzonderlijke registratie met symmetrische sleutels wilt maken, selecteert u afzonderlijke registratie en mechanisme ' TPM ' en voert u de goedkeurings sleutels in om de registratie te maken. Meer informatie over de typen Tpm's vindt u [hier](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)meer informatie over TPM-Attestation. Hier vindt u de [koppeling](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) naar code voorbeelden om uw apparaatcode te helpen bij het inrichten van apparaten met behulp van TPM. Als u een TPM-Attestation wilt maken, typt u de goedkeurings sleutel en slaat u deze op.

## <a name="connect-devices-with-iot-plug-and-play"></a>Apparaten verbinden met IoT-Plug en Play

Een van de belangrijkste functies van IoT Plug en Play met IoT Central is de mogelijkheid om automatisch Apparaatbeheer te koppelen aan de apparaat-verbinding. Samen met de referenties van het apparaat kunnen apparaten de **CapabilityModelId** nu verzenden als onderdeel van de aanroep van de apparaatregistratie en IOT Central wordt de sjabloon voor het apparaat gedetecteerd en gekoppeld. Het detectie proces volgt de volgende volg orde:

1. Hiermee wordt gekoppeld aan de sjabloon voor het apparaat als deze al in de IoT Central toepassing is gepubliceerd.
1. Haalt uit de open bare opslag plaats van gepubliceerde en gecertificeerde mogelijkhedens modellen.

Hieronder ziet u de indeling van de extra Payload die het apparaat zou verzenden tijdens de registratie oproep DPS

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> Houd er rekening mee dat de optie automatisch goed keuren moet zijn ingeschakeld voor apparaten om automatisch verbinding te maken, het model te ontdekken en gegevens te verzenden.

## <a name="device-status"></a>Apparaatstatus

Wanneer een echt apparaat verbinding maakt met uw IoT Central-toepassing, verandert de apparaatstatus als volgt:

1. De apparaatstatus wordt eerst **geregistreerd**. Deze status betekent dat het apparaat wordt gemaakt in IoT Central en een apparaat-ID heeft. Een apparaat wordt geregistreerd wanneer:
    - Er wordt een nieuw echt apparaat toegevoegd op de pagina **apparaten** .
    - Er wordt een set apparaten toegevoegd met behulp van **importeren** op de pagina **apparaten** .

1. De apparaatstatus wordt gewijzigd in **ingericht** wanneer het apparaat dat is verbonden met uw IOT Central-toepassing met geldige referenties de inrichtings stap heeft voltooid. In deze stap haalt het apparaat een connection string op uit IoT Hub. Het apparaat kan nu verbinding maken met IoT Hub en beginnen met het verzenden van gegevens.

1. Een operator kan een apparaat blok keren. Wanneer een apparaat is geblokkeerd, kunnen er geen gegevens worden verzonden naar uw IoT Central-toepassing. Geblokkeerde apparaten hebben de status **geblokkeerd**. Een operator moet het apparaat opnieuw instellen om het verzenden van gegevens te hervatten. Wanneer een operator de blok kering van een apparaat opheffen, wordt de status teruggezet naar de vorige waarde, **geregistreerd** of **ingericht**.

1. De apparaatstatus **wacht op goed keuring** , wat betekent dat de optie **automatisch goed keuren** is uitgeschakeld en dat alle apparaten die verbinding maken met IOT Central expliciet worden goedgekeurd door een operator. Apparaten die niet hand matig zijn geregistreerd op de pagina **apparaten** , maar die zijn verbonden met geldige referenties, hebben de status van het apparaat **wacht op goed keuring**. Opera tors kunnen deze apparaten goed keuren op de pagina **apparaten** met behulp van de knop **goed keuren** .

1. De apparaatstatus is niet **gekoppeld** , wat betekent dat er aan de apparaten die verbinding maken met IOT Central geen sjabloon voor het apparaat is gekoppeld. Dit gebeurt meestal in de volgende scenario's:
    - Er wordt een set apparaten toegevoegd met behulp van **importeren** op de pagina **apparaten** zonder de sjabloon voor het apparaat op te geven
    - Apparaten die niet hand matig zijn geregistreerd op de pagina **apparaten** die zijn verbonden met geldige referenties, maar zonder de sjabloon-id op te geven tijdens de registratie.  
De operator kan een apparaat koppelen aan een sjabloon op de pagina **apparaten** met behulp van de knop **migreren** .

## <a name="sdk-support"></a>SDK-ondersteuning

De Sdk's van het Azure-apparaat bieden u de eenvoudigste manier voor het implementeren van uw apparaatcode. De volgende Sdk's voor apparaten zijn beschikbaar:

- [Azure IoT SDK voor C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK voor python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK voor node. js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK voor Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK voor .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-functies en IoT Hub connectiviteit

Alle communicatie van apparaten met IoT Hub gebruikt de volgende IoT Hub connectiviteits opties:

- [Apparaat-naar-Cloud-berichten](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Apparaat apparaatdubbels](../../iot-hub/iot-hub-devguide-device-twins.md)

De volgende tabel bevat een overzicht van de manier waarop functies van Azure IoT Central-apparaten worden toegewezen aan IoT Hub-functies:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Meting: telemetrie | Apparaat-naar-Cloud-berichten |
| Apparaateigenschappen | Dubbele gerapporteerde eigenschappen van het apparaat |
| Instellingen | Dubbele gewenste en gerapporteerde eigenschappen van het apparaat |

Zie voor meer informatie over het gebruik van de apparaat-Sdk's [een DevDiv Kit-apparaat verbinden met uw Azure IOT Central-toepassing](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor voorbeeld code.

### <a name="protocols"></a>Protocollen

De Sdk's van het apparaat ondersteunen de volgende netwerk protocollen om verbinding te maken met een IoT-hub:

- MQTT
- AMQP
- HTTPS

Zie voor meer informatie over deze protocollen en richt lijnen voor het kiezen van een [communicatie protocol](../../iot-hub/iot-hub-devguide-protocols.md).

Als uw apparaat een van de ondersteunde protocollen niet kan gebruiken, kunt u Azure IoT Edge gebruiken om Protocol conversie uit te voeren. IoT Edge ondersteunt andere intelligentie-in-Edge-scenario's voor het verenigen van de verwerking naar de rand vanuit de Azure IoT Central-toepassing.

## <a name="security"></a>Beveiliging

Alle gegevens die worden uitgewisseld tussen apparaten en uw Azure-IoT Central, worden versleuteld. IoT Hub verifieert elke aanvraag van een apparaat dat verbinding maakt met een van de IoT Hub-eind punten van het apparaat. Om te voor komen dat referenties via de kabel worden uitgewisseld, gebruikt een apparaat ondertekende tokens om te verifiëren. Zie [toegang tot IOT hub beheren](../../iot-hub/iot-hub-devguide-security.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over de connectiviteit van apparaten in azure IoT Central, zijn dit de voorgestelde volgende stappen:

- [Een DevKit-apparaat voorbereiden en aansluiten](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [C SDK: de client-SDK voor de apparaat inrichten](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
