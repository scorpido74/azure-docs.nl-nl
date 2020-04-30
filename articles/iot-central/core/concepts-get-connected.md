---
title: Connectiviteit van apparaten in azure IoT Central | Microsoft Docs
description: In dit artikel vindt u belang rijke concepten met betrekking tot de connectiviteit van apparaten in azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 1398169c44dadcd11ad037e4e3a1cc0132e21f13
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024690"
---
# <a name="get-connected-to-azure-iot-central"></a>Maak verbinding met Azure IoT Central

*Dit artikel is van toepassing op Opera tors en ontwikkel aars van apparaten.*

In dit artikel worden de opties beschreven voor het verbinden van uw apparaten met een Azure IoT Central-toepassing.

Normaal gesp roken moet u een apparaat registreren in uw toepassing voordat u verbinding kunt maken. IoT Central ondersteunt echter scenario's waarbij [apparaten verbinding kunnen maken zonder eerst te worden geregistreerd](#connect-without-registering-devices).

IoT Central maakt gebruik van de [Azure IOT hub Device Provisioning-Service (DPS)](../../iot-dps/about-iot-dps.md) om het verbindings proces te beheren. Een apparaat maakt eerst verbinding met een DPS-eind punt om de informatie op te halen die nodig is om verbinding te maken met uw toepassing. Intern gebruikt uw IoT Central-toepassing een IoT-hub voor het afhandelen van connectiviteit met apparaten. Met DPS kunt u het volgende doen:

- IoT Central voor het ondersteunen van onboarding en het verbinden van apparaten op schaal.
- U kunt de referenties van het apparaat genereren en de apparaten offline configureren zonder de apparaten te registreren via IoT Central-gebruikers interface.
- U kunt uw eigen apparaat-Id's gebruiken om apparaten te registreren in IoT Central. Het gebruik van uw eigen apparaat-Id's vereenvoudigt de integratie met bestaande back-office-systemen.
- Een enkele, consistente manier om apparaten te verbinden met IoT Central.

IoT Central ondersteunt zowel Shared Access signatures (SAS) als X. 509-certificaten om de communicatie tussen een apparaat en uw toepassing te beveiligen. X. 509-certificaten worden aanbevolen in productie omgevingen.

In dit artikel worden de volgende gebruiks voorbeelden beschreven:

- [Eén apparaat verbinden met SAS](#connect-a-single-device)
- [Apparaten op schaal aansluiten met SAS](#connect-devices-at-scale-using-sas)
- [Apparaten op schaal aansluiten met X. 509-certificaten](#connect-devices-using-x509-certificates) : de aanbevolen benadering voor productie omgevingen.
- [Apparaten verbinden zonder deze eerst te registreren](#connect-without-registering-devices)
- [Apparaten verbinden die individuele inschrijvingen voor DPS gebruiken](#individual-enrollment-based-device-connectivity)
- [Apparaten verbinden met IoT Plug en Play-functies (preview)](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Eén apparaat verbinden

Deze aanpak is nuttig wanneer u experimenteert met IoT Central of apparaten testen. U kunt de SAS-sleutels voor de verbinding met uw IoT Central-toepassing gebruiken om een apparaat te verbinden met uw IoT Central-toepassing. Kopieer de _SAS-sleutel_ van het apparaat uit de verbindings gegevens voor een geregistreerd apparaat:

![SAS-sleutels voor een afzonderlijk apparaat](./media/concepts-get-connected/single-device-sas.png)

Zie voor meer informatie de zelf studie [een node. js-client toepassing maken en verbinden met uw Azure IOT Central-toepassing](./tutorial-connect-device-nodejs.md) .

## <a name="connect-devices-at-scale-using-sas"></a>Apparaten op schaal aansluiten met SAS

Als u apparaten wilt verbinden met IoT Central op schaal met SAS-sleutels, moet u de apparaten registreren en vervolgens instellen:

### <a name="register-devices-in-bulk"></a>Apparaten bulksgewijs registreren

Als u een groot aantal apparaten wilt registreren bij uw IoT Central-toepassing, gebruikt u een CSV-bestand om [apparaat-id's en apparaatnamen te importeren](howto-manage-devices.md#import-devices).

Als u de verbindings gegevens voor de geïmporteerde apparaten wilt ophalen, [exporteert u een CSV-bestand van uw IOT Central-toepassing](howto-manage-devices.md#export-devices). Het geëxporteerde CSV-bestand bevat de apparaat-Id's en de SAS-sleutels.

### <a name="set-up-your-devices"></a>Uw apparaten instellen

Gebruik de verbindings gegevens van het export bestand in uw apparaatcode om uw apparaten in staat te stellen verbinding te maken en gegevens naar IoT te verzenden naar uw IoT Central-toepassing. U hebt ook het DPS **-id-bereik** voor uw toepassing nodig. U kunt deze waarde vinden in **beheer > apparaat verbinding**.

> [!NOTE]
> Zie [verbinding maken zonder eerst apparaten te registreren](#connect-without-registering-devices)voor meer informatie over het verbinden van apparaten zonder deze eerst te registreren in IOT Central.

## <a name="connect-devices-using-x509-certificates"></a>Apparaten verbinden met X. 509-certificaten

In een productie omgeving, met behulp van X. 509-certificaten is het aanbevolen mechanisme voor verificatie van apparaten voor IoT Central. Zie voor meer informatie [apparaat-verificatie met behulp van X. 509 CA-certificaten](../../iot-hub/iot-hub-x509ca-overview.md).

Voordat u een apparaat verbindt met een X. 509-certificaat, moet u een tussenliggend of basis X. 509-certificaat toevoegen en verifiëren in uw toepassing. Apparaten moeten Leaf X. 509-certificaten gebruiken die zijn gegenereerd op basis van het hoofd-of tussenliggende certificaat.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Een basis-of tussenliggend certificaat toevoegen en verifiëren

Ga naar **beheer > apparaat verbinding > het primaire certificaat te beheren** en voeg het X. 509 basis-of tussen certificaat toe dat u gebruikt voor het genereren van de certificaten van het apparaat.

![Verbindingsinstellingen](media/concepts-get-connected/manage-x509-certificate.png)

Verifiëren van eigendom van het certificaat zorgt ervoor dat de persoon die het certificaat uploadt, de persoonlijke sleutel van het certificaat heeft. Het certificaat verifiëren:

  1. Selecteer de knop naast **verificatie code** om een code te genereren.
  1. Maak een X. 509-verificatie certificaat met de verificatie code die u in de vorige stap hebt gegenereerd. Sla het certificaat op als een CER-bestand.
  1. Upload het ondertekende verificatie certificaat en selecteer **verifiëren**. Het certificaat is gemarkeerd als **gecontroleerd** wanneer de verificatie is geslaagd.

Als u een inbreuk op de beveiliging hebt of als uw primaire certificaat is ingesteld op verlopen, gebruikt u het secundaire certificaat om de downtime te verminderen. U kunt door gaan met het inrichten van apparaten met behulp van het secundaire certificaat wanneer u het primaire certificaat bijwerkt.

### <a name="register-and-connect-devices"></a>Apparaten registreren en verbinden

Als u apparaten bulksgewijs wilt verbinden met X. 509-certificaten, moet u eerst de apparaten in uw toepassing registreren met behulp van een CSV-bestand om [de apparaat-id's en apparaatnamen te importeren](howto-manage-devices.md#import-devices). De apparaat-Id's moeten in kleine letters worden gereduceerd.

Genereer X. 509-blad certificaten voor uw apparaten met het geüploade basis certificaat Gebruik de **apparaat-id** als `CNAME` de waarde in de blad certificaten. De apparaatcode heeft de waarde voor **id-bereik** nodig voor uw toepassing, de **apparaat-id**en het bijbehorende certificaat van het apparaat.

### <a name="for-testing-purposes-only"></a>Alleen voor test doeleinden

Voor alleen testen kunt u de volgende hulpprogram ma's gebruiken om basis-, tussenliggende en apparaat certificaten te genereren:

- [Hulpprogram ma's voor de apparaat-SDK van Azure IOT Device Provisioning](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): een verzameling node. js-hulpprogram ma's die u kunt gebruiken om X. 509-certificaten en-sleutels te genereren en te controleren.
- Als u een DevKit-apparaat gebruikt, wordt met dit [opdracht regel programma](https://aka.ms/iotcentral-docs-dicetool) een CA-certificaat gegenereerd dat u aan uw IOT Central-toepassing kunt toevoegen om de certificaten te controleren.
- [Testen van CA-certificaten voor voor beelden en zelf studies beheren](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): een verzameling Power shell-en bash-scripts voor het volgende:
  - Maak een certificaat keten.
  - Sla de certificaten op als CER-bestanden die u wilt uploaden naar uw IoT Central-toepassing.
  - Gebruik de verificatie code uit de IoT Central toepassing om het verificatie certificaat te genereren.
  - Maak blad certificaten voor uw apparaten met behulp van uw apparaat-Id's als een para meter voor het hulp programma.

### <a name="further-reference"></a>Aanvullende Naslag informatie

- [Voorbeeld implementatie voor RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Voor beeld van apparaatclient in C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Verbinding maken zonder apparaten te registreren

De eerder beschreven scenario's vereisen dat u apparaten in uw toepassing registreert voordat ze verbinding maken. Met IoT Central kunnen Oem's ook apparaten massaal produceren waarmee verbinding kan worden gemaakt zonder eerst te worden geregistreerd. Een OEM genereert de juiste referenties voor het apparaat en configureert de apparaten in de fabriek. Wanneer een klant een apparaat voor de eerste keer inschakelt, wordt er verbinding gemaakt met DPS, waarna het apparaat automatisch wordt verbonden met de juiste IoT Central-toepassing. Een IoT Central-operator moet het apparaat goed keuren voordat het begint met het verzenden van gegevens naar de toepassing.

De stroom wijkt enigszins af van de vraag of de apparaten SAS-tokens of X. 509-certificaten gebruiken:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Apparaten koppelen die gebruikmaken van SAS-tokens zonder te registreren

1. Kopieer de primaire sleutel van de IoT Central toepassings groep:

    ![Primaire SAS-sleutel van toepassings groep](media/concepts-get-connected/group-sas-keys.png)

1. Gebruik het hulp programma [DPS-keygen](https://www.npmjs.com/package/dps-keygen) om de SAS-sleutels van het apparaat te genereren. Gebruik de primaire sleutel van de groep uit de vorige stap. De apparaat-Id's moeten kleine letters zijn:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. De OEM knippert elk apparaat met een apparaat-ID, een gegenereerde SAS-sleutel voor het apparaat en de waarde voor het bereik van de toepassings **-id** .

1. Wanneer u overschakelt op een apparaat, maakt het eerst verbinding met DPS om de IoT Central registratie gegevens op te halen.

    Het apparaat heeft in eerste instantie een Apparaatstatus **die niet is gekoppeld** op de pagina **apparaten** en is niet toegewezen aan een sjabloon voor het apparaat. Op de pagina **apparaten** **migreert** u het apparaat naar de juiste Device-sjabloon. Het inrichten van apparaten is nu voltooid, de apparaatstatus is nu **ingericht**en het apparaat kan beginnen met het verzenden van gegevens.

    Op de pagina **beheer > apparaat** wordt met de optie **automatisch goed keuren** bepaald of u het apparaat hand matig moet goed keuren voordat het gegevens kan verzenden.

    > [!NOTE]
    > Zie [apparaten verbinden met IoT Plug en Play (preview)](#connect-devices-with-iot-plug-and-play-preview)voor meer informatie over het automatisch koppelen van een apparaat aan een apparaataccount.

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Apparaten verbinden die X. 509-certificaten gebruiken zonder te registreren

1. [Voeg en verifieer een basis-of tussenliggende X. 509-certificaat](#connect-devices-using-x509-certificates) aan uw IOT Central-toepassing. (#connect-apparaten-gebruiken-x509-certificaten)

1. Genereer de blad certificaten voor uw apparaten met behulp van het basis-of tussenliggende certificaat dat u aan uw IoT Central-toepassing hebt toegevoegd. Gebruik kleine-case apparaat-Id's als `CNAME` de in de Blade certificaten.

1. De OEM knippert elk apparaat met een apparaat-ID, een gegenereerd links X. 509-certificaat en de waarde voor het bereik van de toepassings **-id** .

1. Wanneer u overschakelt op een apparaat, maakt het eerst verbinding met DPS om de IoT Central registratie gegevens op te halen.

    Het apparaat heeft in eerste instantie een Apparaatstatus **die niet is gekoppeld** op de pagina **apparaten** en is niet toegewezen aan een sjabloon voor het apparaat. Op de pagina **apparaten** **migreert** u het apparaat naar de juiste Device-sjabloon. Het inrichten van apparaten is nu voltooid, de apparaatstatus is nu **ingericht**en het apparaat kan beginnen met het verzenden van gegevens.

    Op de pagina **beheer > apparaat** wordt met de optie **automatisch goed keuren** bepaald of u het apparaat hand matig moet goed keuren voordat het gegevens kan verzenden.

    > [!NOTE]
    > Zie [apparaten verbinden met IoT Plug en Play (preview)](#connect-devices-with-iot-plug-and-play-preview)voor meer informatie over het automatisch koppelen van een apparaat aan een apparaataccount.

## <a name="individual-enrollment-based-device-connectivity"></a>Afzonderlijke op registratie gebaseerde connectiviteit van apparaten

Gebruik individuele inschrijvingen voor klanten die elk hun eigen verificatie referenties verbinden. Een afzonderlijke inschrijving is een vermelding voor één apparaat dat verbinding mag maken. Afzonderlijke inschrijvingen kunnen X. 509 Leaf-certificaten of SAS-tokens (van een fysieke of virtuele Trusted Platform Module) gebruiken als Attestation-mechanismen. De apparaat-ID (ook wel registratie-ID genoemd) in een afzonderlijke inschrijving is alfanumerieke tekens en kan afbreek streepjes bevatten. Zie voor meer informatie [DPS individuele inschrijving](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Wanneer u een afzonderlijke inschrijving voor een apparaat maakt, heeft dit voor rang op de standaard opties voor het inschrijven van groepen in uw IoT Central-toepassing.

### <a name="creating-individual-enrollments"></a>Afzonderlijke inschrijvingen maken

IoT Central ondersteunt de volgende Attestation-mechanismen voor individuele inschrijvingen:

- **Attestation van symmetrische sleutels:** Symmetrische-sleutel attest is een eenvoudige benadering voor het verifiëren van een apparaat met het DPS-exemplaar. Als u een afzonderlijke inschrijving wilt maken die gebruikmaakt van symmetrische sleutels, opent u de pagina **verbinding met apparaat** , selecteert u **afzonderlijke inschrijving** als de verbindings methode en de **Shared Access Signature (SAS)** als mechanisme. Voer Base64 Encoded Primary en secundaire sleutels in en sla uw wijzigingen op. Gebruik de **id-Scope**, de **apparaat-id**en de primaire of secundaire sleutel om verbinding te maken met uw apparaat.

    > [!TIP]
    > Voor testen kunt u **openssl** gebruiken om base64-gecodeerde sleutels te genereren:`openssl rand -base64 64`

- **X. 509-certificaten:** Als u een afzonderlijke registratie met X. 509-certificaten wilt maken, opent u de pagina **verbinding met apparaat** , selecteert u **afzonderlijke inschrijving** als de verbindings methode en **certificaten (X. 509)** als mechanisme. Voor apparaats certificaten die worden gebruikt met een afzonderlijke inschrijvings vermelding is vereist dat de certificaat verlener en het onderwerp CN worden ingesteld op de apparaat-ID.

    > [!TIP]
    > Voor het testen kunt u [Hulpprogram ma's voor de Azure IOT Device Provisioning Device SDK voor node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) gebruiken om een zelfondertekend certificaat te genereren:`node create_test_cert.js device "mytestdevice"`

- **Attestation van trusted platform module (TPM):** Een [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) is een type hardware Security module. Het gebruik van een TPM is een van de veiligste manieren om verbinding te maken met een apparaat. In dit artikel wordt ervan uitgegaan dat u een afzonderlijke, firmware of geïntegreerde TPM gebruikt. Software geëmuleerde Tpm's zijn goed geschikt voor het maken van prototypen of tests, maar bieden geen hetzelfde beveiligings niveau als discrete, firmware of geïntegreerde Tpm's. Gebruik geen software-Tpm's in productie. Als u een afzonderlijke inschrijving wilt maken die gebruikmaakt van een TPM, opent u de pagina **verbinding met apparaat** , selecteert u **afzonderlijke inschrijving** als de verbindings methode en **TPM** als mechanisme. Voer de TPM-goedkeurings sleutel in en sla de verbindings gegevens van het apparaat op.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Apparaten verbinden met IoT-Plug en Play (preview-versie)

Een van de belangrijkste functies van IoT Plug en Play (preview) met IoT Central is de mogelijkheid om automatisch apparaat sjablonen te koppelen aan de apparaat-verbinding. Samen met de referenties van het apparaat kunnen apparaten de **CapabilityModelId** nu verzenden als onderdeel van de aanroep van het apparaat. Met deze mogelijkheid kunnen IoT Central de sjabloon van het apparaat detecteren en koppelen aan het apparaat. Het detectie proces werkt als volgt:

1. Hiermee wordt gekoppeld aan de sjabloon voor het apparaat als deze al in de IoT Central toepassing is gepubliceerd.
1. Haalt uit de open bare opslag plaats van gepubliceerde en gecertificeerde mogelijkhedens modellen.

Hieronder ziet u de indeling van de extra Payload die het apparaat zou verzenden tijdens de registratie oproep DPS

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Houd er rekening mee dat de optie **automatisch goed keuren** op **beheer > apparaat verbinding** moet zijn ingeschakeld voor apparaten om automatisch verbinding te maken, de sjabloon voor het apparaat te detecteren en gegevens te verzenden.

## <a name="device-status-values"></a>Waarden van apparaatstatus

Wanneer een echt apparaat verbinding maakt met uw IoT Central-toepassing, verandert de apparaatstatus als volgt:

1. De apparaatstatus wordt eerst **geregistreerd**. Deze status betekent dat het apparaat wordt gemaakt in IoT Central en een apparaat-ID heeft. Een apparaat wordt geregistreerd wanneer:
    - Er wordt een nieuw echt apparaat toegevoegd op de pagina **apparaten** .
    - Er wordt een set apparaten toegevoegd met behulp van **importeren** op de pagina **apparaten** .

1. De apparaatstatus wordt gewijzigd in **ingericht** wanneer het apparaat dat is verbonden met uw IOT Central-toepassing met geldige referenties de inrichtings stap heeft voltooid. In deze stap gebruikt het apparaat DPS om automatisch een connection string op te halen uit het IoT Hub dat door uw IoT Central-toepassing wordt gebruikt. Het apparaat kan nu verbinding maken met IoT Central en beginnen met het verzenden van gegevens.

1. Een operator kan een apparaat blok keren. Wanneer een apparaat is geblokkeerd, kunnen er geen gegevens worden verzonden naar uw IoT Central-toepassing. Geblokkeerde apparaten hebben de status **geblokkeerd**. Een operator moet het apparaat opnieuw instellen om het verzenden van gegevens te hervatten. Wanneer een operator de blok kering van een apparaat opheffen, wordt de status teruggezet naar de vorige waarde, **geregistreerd** of **ingericht**.

1. Als de apparaatstatus wacht op **goed keuring**, betekent dit dat de optie **automatisch goed keuren** is uitgeschakeld. Een operator moet een apparaat expliciet goed keuren voordat er gegevens worden verzonden. Apparaten die niet hand matig zijn geregistreerd op de pagina **apparaten** , maar die zijn verbonden met geldige referenties, hebben de status van het apparaat **wacht op goed keuring**. Opera tors kunnen deze apparaten goed keuren op de pagina **apparaten** met behulp van de knop **goed keuren** .

1. Als de apparaatstatus niet is **gekoppeld**, betekent dit dat er voor het apparaat waarmee verbinding wordt gemaakt met IOT Central geen sjabloon voor het apparaat is gekoppeld. Deze situatie treedt doorgaans op in de volgende scenario's:

    - Er wordt een set apparaten toegevoegd met behulp van **importeren** op de pagina **apparaten** zonder de sjabloon voor het apparaat op te geven.
    - Een apparaat is hand matig geregistreerd op de pagina **apparaten** zonder de sjabloon voor het apparaat op te geven. Het apparaat is vervolgens verbonden met geldige referenties.  

    De operator kan een apparaat koppelen aan een sjabloon op de pagina **apparaten** met behulp van de knop **migreren** .

## <a name="best-practices"></a>Aanbevolen procedures

Zorg dat het apparaat niet persistent is of niet in de cache is opgeslagen connection string dat DPS retourneert wanneer u het apparaat voor het eerst aansluit. Als u opnieuw verbinding wilt maken met een apparaat, gaat u naar de standaard apparaat registratie stroom om de juiste connection string van het apparaat op te halen. Als de connection string door het apparaat wordt opgeslagen in de cache, wordt de software van het apparaat uitgevoerd met een verouderd connection string als IoT Central de onderliggende Azure IoT-hub bijwerkt die wordt gebruikt.

## <a name="sdk-support"></a>SDK-ondersteuning

De Sdk's van het Azure-apparaat bieden u de eenvoudigste manier voor het implementeren van uw apparaatcode. De volgende Sdk's voor apparaten zijn beschikbaar:

- [Azure IoT-SDK voor C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT-SDK voor Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT-SDK voor Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT-SDK voor Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT-SDK voor .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-functies en IoT Hub connectiviteit

Alle communicatie van apparaten met IoT Hub gebruikt de volgende IoT Hub connectiviteits opties:

- [Apparaat-naar-Cloud-berichten](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Apparaat apparaatdubbels](../../iot-hub/iot-hub-devguide-device-twins.md)

De volgende tabel bevat een overzicht van de manier waarop functies van Azure IoT Central-apparaten worden toegewezen aan IoT Hub-functies:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetrie | Apparaat-naar-Cloud-berichten |
| Eigenschap | Dubbele gerapporteerde eigenschappen van het apparaat |
| Eigenschap (schrijfbaar) | Dubbele gewenste en gerapporteerde eigenschappen van het apparaat |
| Opdracht | Directe methoden |

Zie voor meer informatie over het gebruik van de apparaat-Sdk's [een DevDiv Kit-apparaat verbinden met uw Azure IOT Central-toepassing](howto-connect-devkit.md) voor voorbeeld code.

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

Als u een ontwikkelaar van een apparaat bent, kunt u het volgende doen:

- Meer informatie over het [controleren van de connectiviteit van apparaten met behulp van Azure cli](./howto-monitor-devices-azure-cli.md)
- Meer informatie over het [definiëren van een nieuw IOT-apparaattype in uw Azure IOT Central-toepassing](./howto-set-up-template.md)
- Meer informatie over [Azure IOT edge apparaten en Azure IOT Central](./concepts-iot-edge.md)
