---
title: Apparaatconnectiviteit in Azure IoT Central | Microsoft Documenten
description: In dit artikel worden belangrijke concepten geïntroduceerd met betrekking tot apparaatconnectiviteit in Azure IoT Central
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
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024690"
---
# <a name="get-connected-to-azure-iot-central"></a>Verbinding maken met Azure IoT Central

*Dit artikel is van toepassing op operators en apparaatontwikkelaars.*

In dit artikel worden de opties beschreven voor het verbinden van uw apparaten met een Azure IoT Central-toepassing.

Doorgaans moet u een apparaat in uw toepassing registreren voordat het verbinding kan maken. IoT Central ondersteunt echter scenario's waarin [apparaten verbinding kunnen maken zonder eerst geregistreerd te worden.](#connect-without-registering-devices)

IoT Central gebruikt de [DPS (Azure IoT Hub Device Provisioning service)](../../iot-dps/about-iot-dps.md) om het verbindingsproces te beheren. Een apparaat maakt eerst verbinding met een DPS-eindpunt om de informatie op te halen die het nodig heeft om verbinding te maken met uw toepassing. Intern gebruikt uw IoT Central-toepassing een IoT-hub om de apparaatconnectiviteit te verwerken. Het gebruik van DPS maakt:

- IoT Central ondersteunt onboarding en het verbinden van apparaten op schaal.
- U genereert apparaatreferenties en configureert de apparaten offline zonder de apparaten te registreren via de IoT Central UI.
- U gebruikt uw eigen apparaat-id's om apparaten in IoT Central te registreren. Het gebruik van uw eigen apparaat-id vereenvoudigt de integratie met bestaande back-officesystemen.
- Eén consistente manier om apparaten aan te sluiten op IoT Central.

Om de communicatie tussen een apparaat en uw toepassing te beveiligen, ondersteunt IoT Central zowel SAS- als X.509-certificaten voor gedeelde toegangshandtekeningen (SAS). X.509-certificaten worden aanbevolen in productieomgevingen.

In dit artikel worden de volgende use cases beschreven:

- [Eén apparaat verbinden met SAS](#connect-a-single-device)
- [Apparaten op schaal verbinden met SAS](#connect-devices-at-scale-using-sas)
- [Verbind apparaten op schaal met X.509-certificaten](#connect-devices-using-x509-certificates) - de aanbevolen aanpak voor productieomgevingen.
- [Apparaten verbinden zonder ze eerst te registreren](#connect-without-registering-devices)
- [Apparaten verbinden die afzonderlijke INSCHRIJVINGen van DPS gebruiken](#individual-enrollment-based-device-connectivity)
- [Apparaten verbinden met functies voor IoT Plug and Play (preview)](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Eén apparaat verbinden

Deze aanpak is handig wanneer u experimenteert met IoT Central of testapparaten. U de SAS-sleutels van uw IoT Central-verbinding gebruiken om een apparaat aan te sluiten op uw IoT Central-toepassing. Kopieer de _SAS-sleutel_ van het apparaat uit de verbindingsgegevens van een geregistreerd apparaat:

![SAS-sleutels voor een individueel apparaat](./media/concepts-get-connected/single-device-sas.png)

Zie de [clienttoepassing Node.js maken en verbinden met uw azure IoT Central-toepassingszelfstudie](./tutorial-connect-device-nodejs.md) voor meer informatie.

## <a name="connect-devices-at-scale-using-sas"></a>Apparaten op schaal verbinden met SAS

Als u apparaten op schaal wilt verbinden met IoT Central met SAS-toetsen, moet u de apparaten registreren en vervolgens instellen:

### <a name="register-devices-in-bulk"></a>Apparaten in bulk registreren

Als u een groot aantal apparaten wilt registreren met uw IoT Central-toepassing, gebruikt u een CSV-bestand om [apparaat-id's en apparaatnamen](howto-manage-devices.md#import-devices)te importeren.

Als u de verbindingsgegevens voor de geïmporteerde apparaten wilt ophalen, [exporteert u een CSV-bestand uit uw IoT Central-toepassing](howto-manage-devices.md#export-devices). Het geëxporteerde CSV-bestand bevat de apparaat-id's en de SAS-sleutels.

### <a name="set-up-your-devices"></a>Uw apparaten instellen

Gebruik de verbindingsgegevens uit het exportbestand in uw apparaatcode om uw apparaten in staat te stellen verbinding te maken en gegevens naar IoT naar uw IoT Central-toepassing te verzenden. U hebt ook de DPS **ID-scope** nodig voor uw toepassing. U deze waarde vinden in **beheer > apparaatverbinding.**

> [!NOTE]
> Zie Verbinding maken zonder apparaten eerst te registreren voor meer informatie over hoe u apparaten verbinden zonder ze eerst te [registreren.](#connect-without-registering-devices)

## <a name="connect-devices-using-x509-certificates"></a>Apparaten verbinden met X.509-certificaten

In een productieomgeving is het gebruik van X.509-certificaten het aanbevolen apparaatverificatiemechanisme voor IoT Central. Zie [Apparaatverificatie met X.509 CA-certificaten](../../iot-hub/iot-hub-x509ca-overview.md)voor meer informatie.

Voordat u een apparaat verbindt met een X.509-certificaat, voegt u een tussen- of hoofdx.509-certificaat toe aan uw toepassing. Apparaten moeten blad X.509-certificaten gebruiken die zijn gegenereerd uit het basis- of tussencertificaat.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Een basis- of tussencertificaat toevoegen en verifiëren

Navigeer naar **Beheer > Apparaatverbinding > Primair certificaat beheren** en voeg het X.509-root- of intermediatecertificaat toe dat u gebruikt om de apparaatcertificaten te genereren.

![Verbindingsinstellingen](media/concepts-get-connected/manage-x509-certificate.png)

Het verifiëren van het eigendom van het certificaat zorgt ervoor dat de persoon die het certificaat uploadt de privésleutel van het certificaat heeft. Ga als volgt te werk om het certificaat te verifiëren:

  1. Selecteer de knop naast **Verificatiecode** om een code te genereren.
  1. Maak een X.509-verificatiecertificaat met de verificatiecode die u in de vorige stap hebt gegenereerd. Sla het certificaat op als een .cer-bestand.
  1. Upload het ondertekende verificatiecertificaat en selecteer **Verifiëren**. Het certificaat wordt gemarkeerd als **Geverifieerd** wanneer de verificatie is geslaagd.

Als u een beveiligingslek hebt of als uw primaire certificaat verloopt, gebruikt u het secundaire certificaat om downtime te verminderen. U apparaten blijven inrichten met behulp van het secundaire certificaat terwijl u het primaire certificaat bijwerkt.

### <a name="register-and-connect-devices"></a>Apparaten registreren en verbinden

Als u apparaten in bulk wilt verbinden met X.509-certificaten, registreert u eerst de apparaten in uw toepassing door een CSV-bestand te gebruiken om [de apparaat-id's en apparaatnamen](howto-manage-devices.md#import-devices)te importeren. De apparaat-id's moeten allemaal in kleine letters.

X.509 bladcertificaten genereren voor uw apparaten met behulp van het geüploade root- of intermediatecertificaat. Gebruik de **apparaat-id** als waarde `CNAME` in de bladcertificaten. Uw apparaatcode heeft de waarde van de **id-scope** nodig voor uw toepassing, de **apparaat-id**en het bijbehorende apparaatcertificaat.

### <a name="for-testing-purposes-only"></a>Alleen voor testdoeleinden

Voor alleen testen u de volgende hulpprogramma's gebruiken om root-, intermediate- en apparaatcertificaten te genereren:

- [Hulpprogramma's voor de Azure IoT Device Provisioning Device SDK:](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)een verzameling node.js-hulpprogramma's die u gebruiken om X.509-certificaten en -sleutels te genereren en te verifiëren.
- Als u een DevKit-apparaat gebruikt, genereert dit [opdrachtregelgereedschap een CA-certificaat](https://aka.ms/iotcentral-docs-dicetool) dat u aan uw IoT Central-toepassing toevoegen om de certificaten te verifiëren.
- [Beheer test CA-certificaten voor voorbeelden en zelfstudies:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)een verzameling PowerShell- en Bash-scripts om:
  - Maak een certificaatketen.
  - Sla de certificaten op als .cer-bestanden om te uploaden naar uw IoT Central-toepassing.
  - Gebruik de verificatiecode van de IoT Central-toepassing om het verificatiecertificaat te genereren.
  - Maak bladcertificaten voor uw apparaten met uw apparaat-id's als parameter voor het gereedschap.

### <a name="further-reference"></a>Verdere verwijzing

- [Voorbeeldimplementatie voor RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Voorbeeldapparaatclient in C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Verbinding maken zonder apparaten te registreren

De eerder beschreven scenario's vereisen allemaal dat u apparaten in uw toepassing registreert voordat ze verbinding maken. IoT Central stelt OEM's ook in staat om apparaten in massa te produceren die verbinding kunnen maken zonder eerst te worden geregistreerd. Een OEM genereert geschikte apparaatreferenties en configureert de apparaten in de fabriek. Wanneer een klant een apparaat voor de eerste keer inschakelt, maakt het verbinding met DPS, dat het apparaat vervolgens automatisch verbindt met de juiste IoT Central-toepassing. Een IoT Central-operator moet het apparaat goedkeuren voordat het gegevens naar de toepassing gaat verzenden.

De stroom is iets anders, afhankelijk van of de apparaten SAS-tokens of X.509-certificaten gebruiken:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Apparaten verbinden die SAS-tokens gebruiken zonder zich te registreren

1. Kopieer de primaire sleutel van de IoT Central-toepassing:

    ![Primaire SAS-sleutel voor toepassingsgroep](media/concepts-get-connected/group-sas-keys.png)

1. Gebruik het gereedschap [dps-keygen](https://www.npmjs.com/package/dps-keygen) om de SAS-sleutels van het apparaat te genereren. Gebruik de primaire groepssleutel uit de vorige stap. De apparaat-geïdentificeerde gegevens moeten in kleine letters worden uitgevoerd:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. De OEM knippert elk apparaat met een apparaat-id, een gegenereerde SAS-sleutel voor het apparaat en de scopewaarde van de **toepassings-id.**

1. Wanneer u een apparaat inschakelt, maakt het eerst verbinding met DPS om de IoT Central-registratiegegevens op te halen.

    Het apparaat heeft in eerste instantie een apparaatstatus **die niet is gekoppeld op** de pagina **Apparaten** en is niet toegewezen aan een apparaatsjabloon. Migreer het apparaat **op** de pagina **Apparaten** naar de juiste apparaatsjabloon. Apparaatinrichting is nu voltooid, de apparaatstatus is nu **ingericht**en het apparaat kan beginnen met het verzenden van gegevens.

    Op de **verbindingspagina Beheer > apparaat** bepaalt de optie **Automatisch goedkeuren** of u het apparaat handmatig moet goedkeuren voordat het gegevens kan verzenden.

    > [!NOTE]
    > Zie Apparaten verbinden [met IoT Plug and Play (preview)](#connect-devices-with-iot-plug-and-play-preview)voor meer informatie over het automatisch koppelen van een apparaat aan een apparaatsjabloon.

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Apparaten verbinden die X.509-certificaten gebruiken zonder registratie

1. [Voeg een root- of intermediate X.509-certificaat](#connect-devices-using-x509-certificates) toe aan uw IoT Central-toepassing en verifieer deze. (#connect-apparaten-gebruiken-x509-certificaten)

1. Genereer de bladcertificaten voor uw apparaten met behulp van het root- of intermediatecertificaat dat u aan uw IoT Central-toepassing hebt toegevoegd. Gebruik kleine -case apparaat-id's als de `CNAME` in de bladcertificaten.

1. De OEM knippert elk apparaat met een apparaat-id, een gegenereerd x.509-certificaat en de scopewaarde van de **toepassings-id.**

1. Wanneer u een apparaat inschakelt, maakt het eerst verbinding met DPS om de IoT Central-registratiegegevens op te halen.

    Het apparaat heeft in eerste instantie een apparaatstatus **die niet is gekoppeld op** de pagina **Apparaten** en is niet toegewezen aan een apparaatsjabloon. Migreer het apparaat **op** de pagina **Apparaten** naar de juiste apparaatsjabloon. Apparaatinrichting is nu voltooid, de apparaatstatus is nu **ingericht**en het apparaat kan beginnen met het verzenden van gegevens.

    Op de **verbindingspagina Beheer > apparaat** bepaalt de optie **Automatisch goedkeuren** of u het apparaat handmatig moet goedkeuren voordat het gegevens kan verzenden.

    > [!NOTE]
    > Zie Apparaten verbinden [met IoT Plug and Play (preview)](#connect-devices-with-iot-plug-and-play-preview)voor meer informatie over het automatisch koppelen van een apparaat aan een apparaatsjabloon.

## <a name="individual-enrollment-based-device-connectivity"></a>Apparaatconnectiviteit op basis van individuele inschrijvingen

Voor klanten die apparaten verbinden die elk hun eigen verificatiereferenties hebben, gebruikt u afzonderlijke inschrijvingen. Een individuele inschrijving is een vermelding voor één apparaat dat verbinding mag maken. Individuele inschrijvingen kunnen X.509 leaf-certificaten of SAS-tokens (van een fysieke of virtuele vertrouwde platformmodule) gebruiken als attestmechanismen. De apparaat-ID (ook wel registratie-ID genoemd) in een individuele inschrijving is alfanumeriek, kleine letters en kan koppeltekens bevatten. Zie [DPS individuele inschrijving](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)voor meer informatie .

> [!NOTE]
> Wanneer u een afzonderlijke inschrijving voor een apparaat maakt, heeft deze voorrang op de standaardopties voor groepsinschrijving in uw IoT Central-toepassing.

### <a name="creating-individual-enrollments"></a>Afzonderlijke inschrijvingen maken

IoT Central ondersteunt de volgende attestmechanismen voor individuele inschrijvingen:

- **Symmetrische sleutelattest:** Symmetrische sleutelattest is een eenvoudige benadering van het verifiëren van een apparaat met de DPS-instantie. Als u een afzonderlijke inschrijving wilt maken die symmetrische toetsen gebruikt, opent u de pagina **Apparaatverbinding,** selecteert u **Individuele inschrijving** als verbindingsmethode en **Gedeelde toegangshandtekening (SAS)** als mechanisme. Voer basis64 gecodeerde primaire en secundaire toetsen in en sla de wijzigingen op. Gebruik de **ID-scope**, **apparaat-id**en de primaire of secundaire sleutel om uw apparaat aan te sluiten.

    > [!TIP]
    > Voor het testen u **OpenSSL** gebruiken om basis64 gecodeerde sleutels te genereren:`openssl rand -base64 64`

- **X.509-certificaten:** Als u een afzonderlijke inschrijving met X.509-certificaten wilt maken, opent u de pagina **Apparaatverbinding,** selecteert u **Individuele inschrijving** als verbindingsmethode en **Certificaten (X.509)** als mechanisme. Apparaatcertificaten die worden gebruikt bij een afzonderlijke inschrijvingsvermelding, hebben de vereiste dat de uitgever en de cn van het onderwerp zijn ingesteld op de apparaat-id.

    > [!TIP]
    > Voor het testen u [Hulpprogramma's voor de Azure IoT Device Provisioning Device SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) gebruiken om een zelfondertekend certificaat te genereren:`node create_test_cert.js device "mytestdevice"`

- **Attest van trusted platform module (TPM):** Een [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) is een type hardwarebeveiligingsmodule. Het gebruik van een TPM is een van de veiligste manieren om een apparaat aan te sluiten. In dit artikel wordt ervan uitgegaan dat u een discrete firmware of geïntegreerde TPM gebruikt. Software geëmuleerde TPM's zijn zeer geschikt voor prototypen of testen, maar ze bieden niet hetzelfde beveiligingsniveau als discrete, firmware of geïntegreerde TPM's. Gebruik geen software TPM's in productie. Als u een afzonderlijke inschrijving wilt maken die een TPM gebruikt, opent u de pagina **Apparaatverbinding,** selecteert u **Individuele inschrijving** als verbindingsmethode en **TPM** als mechanisme. Voer de tpm-goedkeuringssleutel in en sla de verbindingsgegevens van het apparaat op.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Apparaten verbinden met IoT Plug and Play (voorbeeld)

Een van de belangrijkste functies van IoT Plug and Play (preview) met IoT Central is de mogelijkheid om apparaatsjablonen automatisch te koppelen aan de apparaatverbinding. Samen met apparaatreferenties kunnen apparaten nu de **CapabilityModelId** verzenden als onderdeel van het apparaatregistratiegesprek. Met deze mogelijkheid kan IoT Central de apparaatsjabloon aan het apparaat ontdekken en koppelen. Het detectieproces werkt als volgt:

1. Hiermee wordt de apparaatsjabloon gekoppeld als deze al is gepubliceerd in de IoT Central-toepassing.
1. Haalt uit de openbare repository van gepubliceerde en gecertificeerde capaciteitsmodellen.

Hieronder is het formaat van de extra payload het apparaat zou sturen tijdens de DPS registratie oproep

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Houd er rekening mee dat de optie **Automatisch goedkeuren** op beheer **> apparaatverbinding** moet zijn ingeschakeld voor apparaten om automatisch verbinding te maken, de apparaatsjabloon te ontdekken en gegevens te verzenden.

## <a name="device-status-values"></a>Statuswaarden van het apparaat

Wanneer een echt apparaat verbinding maakt met uw IoT Central-toepassing, verandert de status van het apparaat als volgt:

1. De apparaatstatus wordt eerst **geregistreerd**. Deze status betekent dat het apparaat is gemaakt in IoT Central en een apparaat-id heeft. Een apparaat wordt geregistreerd wanneer:
    - Een nieuw echt apparaat wordt toegevoegd op de pagina **Apparaten.**
    - Er wordt een set apparaten toegevoegd **met importeren** op de pagina **Apparaten.**

1. De status van het apparaat wordt gewijzigd **in Ingerichte** wanneer het apparaat dat is verbonden met uw IoT Central-toepassing met geldige referenties, de ininrichtingsstap voltooit. In deze stap gebruikt het apparaat DPS om automatisch een verbindingstekenreeks op te halen uit de IoT-hub die wordt gebruikt door uw IoT Central-toepassing. Het apparaat kan nu verbinding maken met IoT Central en beginnen met het verzenden van gegevens.

1. Een operator kan een apparaat blokkeren. Wanneer een apparaat is geblokkeerd, kan het geen gegevens naar uw IoT Central-toepassing verzenden. Geblokkeerde apparaten hebben de status **Geblokkeerd.** Een operator moet het apparaat opnieuw instellen voordat het de verzendende gegevens kan hervatten. Wanneer een operator de blokkering van een apparaat deinstodert, keert de status terug naar de vorige waarde, **geregistreerd** of **ingerichte .**

1. Als de apparaatstatus **wacht op goedkeuring,** betekent dit dat de optie **Automatisch goedkeuren** is uitgeschakeld. Een operator moet een apparaat expliciet goedkeuren voordat het gegevens gaat verzenden. Apparaten die niet handmatig zijn geregistreerd op de pagina **Apparaten,** maar die zijn gekoppeld aan geldige referenties, hebben de apparaatstatus **wachten op goedkeuring.** Operators kunnen deze apparaten goedkeuren op de pagina **Apparaten** met de knop **Goedkeuren.**

1. Als de apparaatstatus **niet is gekoppeld,** betekent dit dat het apparaat dat verbinding maakt met IoT Central geen bijbehorende apparaatsjabloon heeft. Deze situatie gebeurt meestal in de volgende scenario's:

    - Er wordt een set apparaten toegevoegd met **importeren** op de pagina **Apparaten** zonder de apparaatsjabloon op te geven.
    - Een apparaat is handmatig geregistreerd op de pagina **Apparaten** zonder de apparaatsjabloon op te geven. Het apparaat is vervolgens verbonden met geldige referenties.  

    De operator kan een apparaat vanaf de pagina **Apparaten** koppelen aan een apparaatsjabloon met de knop **Migreren.**

## <a name="best-practices"></a>Aanbevolen procedures

Blijf niet bestaan of cache de apparaatverbinding string die DPS retourneert wanneer u het apparaat voor het eerst aansluit. Als u een apparaat opnieuw wilt verbinden, gaat u door de standaardapparaatregistratiestroom om de juiste apparaatverbindingstekenreeks te krijgen. Als het apparaat de verbindingstekenreeks in de cache opslaat, loopt de apparaatsoftware het risico op een verouderde verbindingstekenreeks als IoT Central de onderliggende Azure IoT-hub bijwerkt die wordt gebruikt.

## <a name="sdk-support"></a>SDK-ondersteuning

De Azure Device SDKs bieden u de eenvoudigste manier om uw apparaatcode te implementeren. De volgende apparaat-SDK's zijn beschikbaar:

- [Azure IoT-SDK voor C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT-SDK voor Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT-SDK voor Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT-SDK voor Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT-SDK voor .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-functies en IoT Hub-connectiviteit

Alle apparaatcommunicatie met IoT Hub maakt gebruik van de volgende IoT Hub-connectiviteitsopties:

- [Device-to-cloud berichten](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [De tweelingen van het apparaat](../../iot-hub/iot-hub-devguide-device-twins.md)

In de volgende tabel wordt samengevat hoe azure IoT Central-apparaatfuncties worden toegewezen aan IoT Hub-functies:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetrie | Device-to-cloud berichten |
| Eigenschap | Gemelde eigenschappen van apparaattweeling |
| Eigenschap (schrijfbaar) | Apparaat tweeling gewenste en gerapporteerde eigenschappen |
| Opdracht | Directe methoden |

Zie [Een DevDiv-kitapparaat aansluiten op uw Azure IoT Central-toepassing](howto-connect-devkit.md) voor meer informatie over het gebruik van de Apparaat-SDKs.

### <a name="protocols"></a>Protocollen

De Device SDKs ondersteunen de volgende netwerkprotocollen voor verbinding maken met een IoT-hub:

- MQTT
- AMQP
- HTTPS

Zie [Een communicatieprotocol kiezen](../../iot-hub/iot-hub-devguide-protocols.md)voor informatie over deze verschillende protocollen en richtlijnen voor het kiezen van een protocol.

Als uw apparaat geen van de ondersteunde protocollen kan gebruiken, u Azure IoT Edge gebruiken om protocolconversie uit te brengen. IoT Edge ondersteunt andere intelligence-on-the-edge scenario's om de verwerking te ontladen tot de rand van de Azure IoT Central-toepassing.

## <a name="security"></a>Beveiliging

Alle gegevens die tussen apparaten en uw Azure IoT Central worden uitgewisseld, zijn versleuteld. IoT Hub verifieert elk verzoek van een apparaat dat verbinding maakt met een van de op het apparaat gerichte IoT Hub-eindpunten. Om te voorkomen dat referenties over de draad worden verwisseld, gebruikt een apparaat ondertekende tokens om te verifiëren. Zie Toegang tot [IoT Hub](../../iot-hub/iot-hub-devguide-security.md)beheren voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u een apparaatontwikkelaar bent, moeten enkele voorgestelde volgende stappen:

- Meer informatie over [het bewaken van apparaatconnectiviteit met Azure CLI](./howto-monitor-devices-azure-cli.md)
- Meer informatie over het [definiëren van een nieuw IoT-apparaattype in uw Azure IoT Central-toepassing](./howto-set-up-template.md)
- Lees meer over [Azure IoT Edge-apparaten en Azure IoT Central](./concepts-iot-edge.md)
