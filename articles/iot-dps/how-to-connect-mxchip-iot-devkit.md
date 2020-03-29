---
title: Autovoorzieningsservice voor Azure IoT Hub-apparaatvoorziening gebruiken om de MXChip IoT DevKit te registreren met IoT Hub | Microsoft Documenten
description: Autovoorziening seinen van Azure IoT Hub Device Provisioning Service (DPS) gebruiken om de MXChip IoT DevKit te registreren met IoT Hub.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975649"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Automatische inrichting van azure IoT-hub-apparaatinrichtingsservice gebruiken om de MXChip IoT DevKit te registreren met IoT Hub

In dit artikel wordt beschreven hoe u [de autoprovisioning](concepts-auto-provisioning.md)service voor Azure IoT Hub-inrichting gebruiken om de MXChip IoT DevKit te registreren met Azure IoT Hub. In deze zelfstudie leert u het volgende:

* Configureer het algemene eindpunt van de service Apparaatinrichting op een apparaat.
* Gebruik een uniek apparaatgeheim (UDS) om een X.509-certificaat te genereren.
* Schrijf een individueel apparaat in.
* Controleer of het apparaat is geregistreerd.

De [MXChip IoT DevKit](https://aka.ms/iot-devkit) is een alles-in-één Arduino-compatibel bord met rijke randapparatuur en sensoren. U hiervoor ontwikkelen met [Azure IoT Device Workbench](https://aka.ms/iot-workbench) of [Azure IoT Tools](https://aka.ms/azure-iot-tools) extension pack in Visual Studio Code. De DevKit wordt geleverd met een groeiende [projectcatalogus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) om uw prototype Internet of Things (IoT)-oplossingen te begeleiden die profiteren van Azure-services.

## <a name="before-you-begin"></a>Voordat u begint

Voer eerst de volgende taken uit om de stappen in deze zelfstudie uit te voeren:

* Configureer de Wi-Fi van uw DevKit en bereid uw ontwikkelomgeving voor door de sectie stappen 'De ontwikkelomgeving voorbereiden' in [IoT DevKit AZ3166 verbinden met Azure IoT Hub in de cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment)te volgen.
* Upgrade naar de nieuwste firmware (1.3.0 of hoger) met de [devKit-firmware-zelfstudie bijwerken.](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)
* Maak en koppel een IoT-hub aan een apparaatinrichtingsservice-instantie door de stappen te volgen in [De IoT Hub Device Provisioning Service instellen met de Azure-portal.](/azure/iot-dps/quick-setup-auto-provision)

## <a name="open-sample-project"></a>Voorbeeldproject openen

1. Zorg ervoor dat uw IoT DevKit **niet is verbonden met** uw computer. Start VS Code eerst en sluit de DevKit aan op uw computer.

1. Klik `F1` hierom het opdrachtpalet te openen, de **Werkbank voor Azure IoT-apparaat te**typen en te selecteren: Voorbeelden openen... . Selecteer vervolgens **IoT DevKit** als bord.

1. Zoek **apparaatregistratie met DPS op** de pagina Voorbeeld van IoT-werkbank en klik op **Voorbeeld openen**. Hiermee selecteert u het standaardpad om de voorbeeldcode te downloaden.
    ![Voorbeeld openen](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Een uniek apparaatgeheim opslaan op de beveiliging van het apparaat

Automatische inrichting kan worden geconfigureerd op een apparaat op basis van het [attestmechanisme van](concepts-security.md#attestation-mechanism)het apparaat. De MXChip IoT DevKit maakt gebruik van de [Device Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) van de Trusted Computing [Group.](https://trustedcomputinggroup.org) Een **Unique Device Secret** (UDS) opgeslagen in een STSAFE-beveiligingschip[(STSAFE-A100)](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)op de DevKit wordt gebruikt om het unieke [X.509-certificaat](concepts-security.md#x509-certificates)van het apparaat te genereren. Het certificaat wordt later gebruikt voor het inschrijvingsproces in de service Apparaatvoorziening en tijdens de registratie tijdens de uitvoering.

Een typische UDS is een tekenreeks van 64 tekens, zoals te zien in het volgende voorbeeld:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Ga als lid van het Op- en/uit voor een UDS op de DevKit:

1. Klik in VS-code op de statusbalk om de COM-poort voor de DevKit te selecteren.
  ![COM-poort selecteren](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. Houd op DevKit **de knop A**ingedrukt, druk op de **resetknop** en laat vervolgens **de knop A**los. Uw DevKit gaat in de configuratiemodus.

1. Klik `F1` hierom het opdrachtpalet te openen, de Werkbank voor Azure IoT-apparaat te typen en te **selecteren: Apparaatinstellingen configureren... > Config Unique Device String (UDS)**.
  ![UDS configureren](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Noteer de gegenereerde UDS-tekenreeks. Je hebt het nodig om het X.509-certificaat te genereren. Druk `Enter`dan op .
  ![UDS kopiëren](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Bevestig uit de melding dat UDS is geconfigureerd op de STSAFE.
  ![UDS-succes configureren](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> U u ook UDS configureren via de seriële poort met behulp van hulpprogramma's zoals Putty. Volg [De configuratiemodus](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) gebruiken om dit te doen.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Het eindpunt en de id-scope voor globale apparaten bijwerken

In apparaatcode moet u het eindpunt en de id-scope [voor apparaatinrichting](/azure/iot-dps/concepts-service#device-provisioning-endpoint) opgeven om de tenantisolatie te garanderen.

1. Selecteer in de Azure-portal het **overzichtsvenster** van uw service voor apparaatinrichting en noteer de **waarden Global device endpoint** en **ID Scope.**
  ![Global Endpoint- en ID-scope van apparaatinrichting](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Open **DevKitDPS.ino**. Zoek en `[Global Device Endpoint]` `[ID Scope]` vervang en met de waarden die u zojuist hebt genoteerd.
  ![Eindpunt van apparaatinrichtingsservice](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Vul `registrationId` de variabele in de code. Alleen alfanumerieke, kleine letters en koppeltekens combinatie met een maximum van 128 tekens is toegestaan. Ook opgemerkt vaststelling van de waarde.
  ![Registratie-id](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klik `F1`op , typ en selecteer **Azure IoT Device Workbench: Upload Device Code**. Het begint met het compileren en uploaden van de code naar DevKit.
  ![Device Upload](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>X.509-certificaat genereren

Het [attestmechanisme](/azure/iot-dps/concepts-device#attestation-mechanism) dat door dit monster wordt gebruikt, is x.509-certificaat. Je moet een hulpprogramma gebruiken om het te genereren.

1. Klik in VS-code op `F1`Nieuwe terminal openen om **het** terminalvenster te openen.

1. Uitvoeren `dps_cert_gen.exe` `tool` in map.

1. Geef de gecompileerde binaire bestandslocatie op als `..\.build\DevKitDPS`. Plak vervolgens de **UDS** en **registrationId** die je net hebt genoteerd. 
  ![X.509 genereren](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Een `.pem` X.509-certificaat genereert in dezelfde map.
  ![X.509-bestand](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Een vermelding voor apparaatinschrijving maken

1. Open in de Azure-portal de service voor apparaatvoorziening, navigeer naar de sectie Inschrijvingen beheren en klik op **Afzonderlijke inschrijving toevoegen**.
  ![Individuele inschrijving toevoegen](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Klik op bestandspictogram naast **primair certificaat .pem- of .cer-bestand** om het `.pem` gegenereerde bestand te uploaden.
  ![Upload .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Controleren of de DevKit is geregistreerd bij Azure IoT Hub

Druk op de knop **Opnieuw instellen** op je DevKit. U zou **DPS Verbonden** moeten zien! op het DevKit-scherm. Nadat het apparaat opnieuw is opgestart, vinden de volgende acties plaats:

1. Vanaf het apparaat wordt een registratie-aanvraag verzonden naar Device Provisioning Service.
1. De service Apparaatvoorziening stuurt een registratieuitdaging terug waarop uw apparaat reageert.
1. Bij succesvolle registratie stuurt de service Apparaatvoorziening de IoT Hub URI, apparaat-ID en de versleutelde sleutel terug naar het apparaat.
1. De IoT Hub-clienttoepassing op het apparaat maakt verbinding met uw hub.
1. Bij een geslaagde verbinding met de hub ziet u het apparaat worden weergegeven in de IoT Hub Device Explorer.
  ![Apparaat geregistreerd](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleegt u de [veelgestelde vragen](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)van Iot DevKit of neem contact op met de volgende kanalen voor ondersteuning:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een apparaat veilig in te schrijven bij de Apparaatinrichtingsservice met behulp van de Device Identity Composition Engine, zodat het apparaat zich automatisch kan registreren bij Azure IoT Hub. 

Samengevat heb je geleerd hoe je:

> [!div class="checklist"]
> * Configureer het algemene eindpunt van de service Apparaatinrichting op een apparaat.
> * Gebruik een uniek apparaatgeheim om een X.509-certificaat te genereren.
> * Schrijf een individueel apparaat in.
> * Controleer of het apparaat is geregistreerd.

Meer informatie over het [maken en inrichten van een gesimuleerd apparaat](./quick-create-simulated-device.md).

