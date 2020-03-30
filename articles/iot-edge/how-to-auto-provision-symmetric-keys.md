---
title: Apparaat inrichten met symmetrische sleutelattest - Azure IoT Edge
description: Symmetrische sleutelattest gebruiken om automatische apparaatinrichting voor Azure IoT Edge te testen met de service voor apparaatinrichting
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9944308d00c9cfecbd38a6443efb49913148806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535915"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Een IoT Edge-apparaat maken en inrichten met behulp van symmetrische sleutelattest

Azure IoT Edge-apparaten kunnen automatisch worden ingericht met de [Service Voor het inrichten van apparaten,](../iot-dps/index.yml) net als apparaten die niet randfunctionaliteit hebben. Als u niet bekend bent met het proces van automatisch inrichten, bekijkt u de [concepten voor automatisch inrichten](../iot-dps/concepts-auto-provisioning.md) voordat u verdergaat.

In dit artikel ziet u hoe u een individuele inschrijving voor apparaatinrichtingsservice maakt met behulp van symmetrische sleutelattest op een IoT Edge-apparaat met de volgende stappen:

* Een exemplaar van de IoT Hub Device Provisioning Service (DPS) maken.
* Maak een individuele inschrijving voor het apparaat.
* Installeer de Runtime van IoT Edge en maak verbinding met de IoT-hub.

Symmetrische sleutelattest is een eenvoudige benadering voor het verifiëren van een apparaat met een apparaatvoorzieningsservice-instantie. Deze attestmethode vertegenwoordigt een "Hello world"-ervaring voor ontwikkelaars die nieuw zijn in het inrichten van apparaten of geen strenge beveiligingsvereisten hebben. Apparaatattest met behulp van een [TPM-](../iot-dps/concepts-tpm-attestation.md) of [X.509-certificaten](../iot-dps/concepts-security.md#x509-certificates) is veiliger en moet worden gebruikt voor strengere beveiligingsvereisten.

## <a name="prerequisites"></a>Vereisten

* Een actieve IoT-hub
* Een fysiek of virtueel apparaat

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De Service voor het inrichten van IoT-hub-apparaten instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U de instructies volgen in [Het instellen van de IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

Nadat u de service voor apparaatinrichting hebt uitgevoerd, kopieert u de waarde van **id-bereik** vanaf de overzichtspagina. U gebruikt deze waarde wanneer u de Runtime van IoT Edge configureert.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Kies een unieke registratie-id voor het apparaat

Er moet een unieke registratie-ID worden gedefinieerd om elk apparaat te identificeren. U het MAC-adres, het serienummer of unieke informatie van het apparaat gebruiken.

In dit voorbeeld gebruiken we een combinatie van een MAC-adres en `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`serienummer dat de volgende tekenreeks vormt voor een registratie-id: .

Maak een unieke registratie-id voor uw apparaat. Geldige tekens zijn alfanumerieke kleine letters en streepjes ('-').

## <a name="create-a-dps-enrollment"></a>Een DPS-inschrijving maken

Gebruik de registratie-id van uw apparaat om een individuele inschrijving in DPS te maken.

Wanneer u een inschrijving maakt in DPS, u een **eerste apparaattweelingstatus**declareren. In de apparaattweeling u tags instellen op apparaten groeperen op elke statistiek die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om [automatische implementaties](how-to-deploy-monitor.md)te maken.

> [!TIP]
> Groepsinschrijvingen zijn ook mogelijk bij het gebruik van symmetrische sleutelattest en omvatten dezelfde beslissingen als individuele inschrijvingen.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw exemplaar van de IoT Hub Device Provisioning Service.

1. Selecteer **onder Instellingen**de optie **Inschrijvingen beheren**.

1. Selecteer **Afzonderlijke inschrijving toevoegen** en voer de volgende stappen uit om de inschrijving te configureren:  

   1. Selecteer **voor Mechanisme**symmetrische **sleutel**.

   1. Schakel het selectievakje **Sleutels automatisch genereren** in.

   1. Geef de **registratie-id** op die u voor uw apparaat hebt gemaakt.

   1. Geef desgevraagd een **IoT Hub-apparaat-id** voor uw apparaat op. U apparaat-id's gebruiken om een individueel apparaat te targeten voor de implementatie van modules. Als u geen apparaat-id verstrekt, wordt de registratie-ID gebruikt.

   1. Selecteer **Waar** om aan te geven dat de inschrijving voor een IoT Edge-apparaat is. Voor een groepsinschrijving moeten alle apparaten IoT Edge-apparaten zijn of geen van deze apparaten.

   1. Accepteer de standaardwaarde van het toewijzingsbeleid van de Apparaatinrichtingsservice voor **hoe u apparaten aan hubs wilt toewijzen** of kies een andere waarde die specifiek is voor deze inschrijving.

   1. Kies de gekoppelde **IoT-hub** waarmee u uw apparaat wilt verbinden. U meerdere hubs kiezen en het apparaat wordt aan een van deze hubs toegewezen volgens het geselecteerde toewijzingsbeleid.

   1. Kies **hoe u wilt dat apparaatgegevens worden verwerkt bij het opnieuw inrichten** wanneer apparaten na de eerste keer om inrichting vragen.

   1. Voeg desgevraagd een tagwaarde toe aan de **twinstate van het eerste apparaat.** U tags gebruiken om doelgroepen van apparaten te gebruiken voor het implementeren van modules. Bijvoorbeeld:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Zorg ervoor **dat Invoer inschakelen** is ingesteld op **Inschakelen**.

   1. Selecteer **Opslaan**.

Nu er een inschrijving voor dit apparaat bestaat, kan de IoT Edge-runtime het apparaat tijdens de installatie automatisch inrichten. Zorg ervoor dat u de primaire **sleutelwaarde** van uw inschrijving kopieert die u gebruiken bij het installeren van de runtime van IoT Edge of dat u apparaatsleutels gaat maken voor gebruik met een groepsinschrijving.

## <a name="derive-a-device-key"></a>Een apparaatsleutel afleiden

> [!NOTE]
> Deze sectie is alleen vereist als u een groepsinschrijving gebruikt.

Elk apparaat gebruikt de afgeleide apparaatsleutel met uw unieke registratie-ID om symmetrische sleutelattest uit te voeren met de inschrijving tijdens de inrichting. Als u de apparaatsleutel wilt genereren, gebruikt u de sleutel die u hebt gekopieerd uit uw DPS-inschrijving om een [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-id voor het apparaat te berekenen en het resultaat om te zetten in Base64-indeling.

Neem de primaire of secundaire sleutel van uw inschrijving niet op in de code van uw apparaat.

### <a name="linux-workstations"></a>Linux-werkstations

Als u een Linux-werkstation gebruikt, u openssl gebruiken om uw afgeleide apparaatsleutel te genereren, zoals in het volgende voorbeeld wordt weergegeven.

Vervang de waarde van **KEY** door de **primaire sleutel die** u eerder hebt opgemerkt.

Vervang de waarde van **REG_ID** door de registratie-id van uw apparaat.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows-werkstations

Als u een windows-werkstation gebruikt, u PowerShell gebruiken om uw afgeleide apparaatsleutel te genereren, zoals in het volgende voorbeeld wordt weergegeven.

Vervang de waarde van **KEY** door de **primaire sleutel die** u eerder hebt opgemerkt.

Vervang de waarde van **REG_ID** door de registratie-id van uw apparaat.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>De runtime van IoT Edge installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden uitgevoerd in containers en u extra containers op het apparaat implementeren, zodat u code aan de rand uitvoeren.

U hebt de volgende informatie nodig bij het inrichten van uw apparaat:

* De waarde van het **DPS-id-bereik**
* De **apparaatregistratie-id** die u hebt gemaakt
* De **primaire sleutel die** u hebt gekopieerd uit de DPS-inschrijving

> [!TIP]
> Voor groepsinschrijvingen hebt u de [afgeleide sleutel](#derive-a-device-key) van elk apparaat nodig in plaats van de DPS-inschrijvingssleutel.

### <a name="linux-device"></a>Linux-apparaat

Volg de instructies voor de architectuur van uw apparaat. Zorg ervoor dat u de 3T Edge-runtime configureert voor automatische, niet handmatige inprovisioning.

[De Azure IoT Edge-runtime op Linux installeren](how-to-install-iot-edge-linux.md)

De sectie in het configuratiebestand voor symmetrische sleutelinrichting ziet er als volgt uit:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

Vervang de tijdelijke `<SCOPE_ID>`aanduidingswaarden voor , `<REGISTRATION_ID>`en `<SYMMETRIC_KEY>` door de gegevens die u eerder hebt verzameld. Zorg ervoor dat de **inrichting:** lijn heeft geen voorafgaande witruimte en dat geneste items worden ingesprongen door twee spaties.

### <a name="windows-device"></a>Windows-apparaat

Installeer de IoT Edge-runtime op het apparaat waarvoor u een afgeleide apparaatsleutel hebt gegenereerd. U configureert de 3T Edge-runtime voor automatische, niet handmatige inprovisioning.

Zie De runtime van [Azure IoT Edge installeren op Windows](how-to-install-iot-edge-windows.md)voor meer gedetailleerde informatie over het installeren van IoT Edge op Windows, inclusief vereisten en instructies voor taken zoals het beheren van containers en het bijwerken van IoT Edge.

1. Open een PowerShell-venster in de beheerdersmodus. Zorg ervoor dat u een AMD64-sessie van PowerShell gebruikt bij het installeren van IoT Edge, niet PowerShell (x86).

1. De opdracht **Deploy-IoTEdge** controleert of uw Windows-machine een ondersteunde versie heeft, schakelt de functie containers in en downloadt vervolgens de moby-runtime en de IoT Edge-runtime. De opdracht is standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Op dit moment kunnen IoT Core-apparaten automatisch opnieuw worden opgestart. Andere Windows 10- of Windows Server-apparaten kunnen u vragen om opnieuw op te starten. Start het apparaat nu opnieuw op. Zodra uw apparaat klaar is, voert u PowerShell opnieuw uit als beheerder.

1. Met de opdracht **Initialize-IoTEdge** configureert u de runtime van IoT Edge op uw machine. De opdracht is standaard ingesteld op handmatige `-Dps` inrichting met Windows-containers, tenzij u de vlag gebruikt om automatische inrichting te gebruiken.

   Vervang de tijdelijke `{scope_id}`aanduidingswaarden voor , `{registration_id}`en `{symmetric_key}` door de gegevens die u eerder hebt verzameld.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Geslaagde installatie verifiëren

Als de runtime succesvol is gestart, u naar uw IoT-hub gaan en IoT Edge-modules op uw apparaat implementeren. Gebruik de volgende opdrachten op uw apparaat om te controleren of de runtime is geïnstalleerd en gestart.

### <a name="linux-device"></a>Linux-apparaat

Controleer de status van de IoT Edge-service.

```cmd/sh
systemctl status iotedge
```

Bestudeer servicelogboeken.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lijst met lopende modules.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows-apparaat

Controleer de status van de IoT Edge-service.

```powershell
Get-Service iotedge
```

Bestudeer servicelogboeken.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lijst met lopende modules.

```powershell
iotedge list
```

U controleren of de afzonderlijke inschrijving die u hebt gemaakt in de service voor apparaatinrichting is gebruikt. Navigeer naar de instantie Device Provisioning Service in de Azure-portal. Open de inschrijvingsgegevens voor de afzonderlijke inschrijving die u hebt gemaakt. De status van de inschrijving is **toegewezen** en de apparaat-id wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

Met het inschrijvingsproces voor apparaatinrichtingsservice u de apparaat-id en de dubbele tags van het apparaat instellen op hetzelfde moment als het nieuwe apparaat. U deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te targeten met automatisch apparaatbeheer. Meer informatie over het [implementeren en bewaken van IoT Edge-modules op schaal met behulp van de Azure-portal](how-to-deploy-monitor.md) of het gebruik van Azure [CLI.](how-to-deploy-monitor-cli.md)
