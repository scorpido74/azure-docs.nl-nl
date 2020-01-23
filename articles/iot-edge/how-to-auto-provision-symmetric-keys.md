---
title: Apparaat inrichten met behulp van symmetrische sleutel Attestation-Azure IoT Edge
description: Symmetrische-sleutel attest gebruiken om het automatisch inrichten van apparaten te testen voor Azure IoT Edge met Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3adefbdf248deaec6170037521ab65890356d184
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510886"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Een IoT Edge apparaat maken en inrichten met behulp van symmetrische sleutel attest

Azure IoT Edge-apparaten kunnen worden automatisch kan worden ingericht met behulp van de [Device Provisioning Service](../iot-dps/index.yml) net als bij apparaten die niet Microsoft edge-functionaliteit. Als u niet bekend met het proces van automatische inrichting bent, raadpleegt u de [concepten voor automatische inrichting](../iot-dps/concepts-auto-provisioning.md) voordat u doorgaat.

In dit artikel wordt beschreven hoe u een individuele inschrijving voor een Device Provisioning Service kunt maken met behulp van symmetrische sleutel attest op een IoT Edge apparaat, met de volgende stappen:

* Maak een exemplaar van de IoT Hub Device Provisioning Service (DPS).
* Maak een afzonderlijke inschrijving voor het apparaat.
* Installeer de IoT Edge runtime en maak verbinding met de IoT Hub.

Symmetrische-sleutel attest is een eenvoudige benadering voor het verifiëren van een apparaat met een Device Provisioning service-exemplaar. Deze Attestation-methode vertegenwoordigt een ' Hello World '-ervaring voor ontwikkel aars die nieuw zijn voor het inrichten van apparaten of waarvoor geen strikte beveiligings vereisten gelden. Attestation van apparaten met behulp van een [TPM](../iot-dps/concepts-tpm-attestation.md) of [X. 509-certificaten](../iot-dps/concepts-security.md#x509-certificates) is veiliger en moet worden gebruikt voor strengere beveiligings vereisten.

## <a name="prerequisites"></a>Vereisten

* Een actieve IoT Hub
* Een fysiek of virtueel apparaat

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en een koppeling naar uw IoT hub. U kunt de instructies in [instellen van de IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

Nadat u de Device Provisioning Service die wordt uitgevoerd hebt, kopieert u de waarde van **ID-bereik** van de overzichtspagina. U gebruikt deze waarde bij het configureren van de IoT Edge-runtime.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Een unieke registratie-ID voor het apparaat kiezen

Er moet een unieke registratie-ID worden gedefinieerd om elk apparaat te identificeren. U kunt het MAC-adres, serie nummer of unieke informatie van het apparaat gebruiken.

In dit voor beeld gebruiken we een combi natie van een MAC-adres en serie nummer met de volgende teken reeks voor een registratie-ID: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`.

Maak een unieke registratie-ID voor uw apparaat. Geldige tekens zijn kleine letters en streepjes ('-').

## <a name="create-a-dps-enrollment"></a>Maken van een DPS-inschrijving

Gebruik de registratie-ID van uw apparaat om een afzonderlijke inschrijving in DPS te maken.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om te declareren een **oorspronkelijke Apparaatdubbelstatus**. U kunt in de apparaatdubbel labels instellen om apparaten te groeperen door elke meetwaarde die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie, of het apparaat. Deze tags zijn gebruikt voor het maken [automatische implementaties](how-to-deploy-monitor.md).

> [!TIP]
> Groeps registraties zijn ook mogelijk bij het gebruik van symmetrische sleutel attest en dezelfde beslissingen als afzonderlijke inschrijvingen.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw exemplaar van IOT hub Device Provisioning Service.

1. Onder **instellingen**, selecteer **registraties beheren**.

1. Selecteer **afzonderlijke registratie toevoegen** voltooit u de volgende stappen om de registratie te configureren:  

   1. Selecteer voor **mechanisme** **symmetrische sleutel**.

   1. Schakel het selectie vakje **sleutels automatisch genereren** in.

   1. Geef de **registratie-id** op die u hebt gemaakt voor uw apparaat.

   1. Geef een **IOT hub apparaat-id** voor uw apparaat op als u wilt. Apparaat-id's kunt u richten op een afzonderlijk apparaat voor de implementatie van beleidsmodule. Als u geen apparaat-ID opgeeft, wordt de registratie-ID gebruikt.

   1. Selecteer **waar** om te declareren dat de inschrijving voor een IOT edge apparaat is. Voor de registratie van een groep moeten alle apparaten worden IoT Edge apparaten of geen van beide.

   1. Accepteer de standaard waarde van het toewijzings beleid van de Device Provisioning Service voor de **manier waarop u apparaten aan hubs wilt toewijzen** of kies een andere waarde die specifiek is voor deze inschrijving.

   1. Kies de gekoppelde **IoT-Hub** dat u wilt verbinding maken met uw apparaat. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze op basis van het geselecteerde toewijzings beleid.

   1. Kies **hoe u wilt dat apparaatgegevens worden verwerkt bij het opnieuw inrichten** wanneer apparaten na de eerste keer worden ingericht.

   1. Een tagwaarde toevoegen aan de **oorspronkelijke Apparaatdubbelstatus** als u wilt. U kunt tags voor doelgroepen van apparaten gebruiken voor de implementatie van beleidsmodule. Bijvoorbeeld:

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

   1. Zorg ervoor dat **vermelding inschakelen** is ingesteld op **inschakelen**.

   1. Selecteer **Opslaan**.

Nu een inschrijving voor dit apparaat bestaat, kan de IoT Edge runtime automatisch het apparaat inrichten tijdens de installatie. Zorg ervoor dat u de waarde van de **primaire sleutel** van uw registratie kopieert om te gebruiken tijdens de installatie van de IOT Edge runtime, of dat u een apparaatcode wilt maken voor gebruik met een groeps registratie.

## <a name="derive-a-device-key"></a>Een apparaatcode afleiden

> [!NOTE]
> Deze sectie is alleen vereist als u een groeps registratie gebruikt.

Elk apparaat gebruikt de afgeleide apparaatwachtwoord met uw unieke registratie-ID voor het uitvoeren van de symmetrische sleutel attest met de inschrijving tijdens het inrichten. Als u de apparaatcode wilt genereren, gebruikt u de sleutel die u hebt gekopieerd uit uw DPS-inschrijving om een [HMAC-sha256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-id voor het apparaat te berekenen en zet u het resultaat om in Base64-indeling.

Neem de primaire of secundaire sleutel van uw inschrijving niet op in uw apparaatcode.

### <a name="linux-workstations"></a>Linux-werk stations

Als u een Linux-werk station gebruikt, kunt u openssl gebruiken om uw afgeleide apparaatwachtwoord te genereren, zoals wordt weer gegeven in het volgende voor beeld.

Vervang de waarde van **Key** door de **primaire sleutel** die u eerder hebt genoteerd.

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

### <a name="windows-based-workstations"></a>Windows-werk stations

Als u een Windows-werk station gebruikt, kunt u Power shell gebruiken om uw afgeleide apparaatwachtwoord te genereren, zoals wordt weer gegeven in het volgende voor beeld.

Vervang de waarde van **Key** door de **primaire sleutel** die u eerder hebt genoteerd.

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

## <a name="install-the-iot-edge-runtime"></a>IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen in containers worden uitgevoerd, en kunnen u extra containers implementeren op het apparaat, zodat u kunt de code aan de rand uitvoeren.

U hebt de volgende informatie nodig bij het inrichten van uw apparaat:

* De waarde voor het bereik van de DPS **-id**
* De **registratie-id** van het apparaat dat u hebt gemaakt
* De **primaire sleutel** die u hebt gekopieerd uit de DPS-inschrijving

> [!TIP]
> Voor groeps registraties hebt u de [afgeleide sleutel](#derive-a-device-key) van elk apparaat nodig in plaats van de registratie sleutel voor DPS.

### <a name="linux-device"></a>Linux-apparaat

Volg de instructies voor de architectuur van uw apparaat. Zorg ervoor dat u de IoT Edge-runtime voor het inrichten van automatische, niet handmatig configureren.

[Installeer de Azure IoT Edge runtime op Linux](how-to-install-iot-edge-linux.md)

De sectie in het configuratie bestand voor het inrichten van symmetrische sleutels ziet er als volgt uit:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Vervang de waarden van de tijdelijke aanduidingen voor `{scope_id}`, `{registration_id}`en `{symmetric_key}` met de gegevens die u eerder hebt verzameld. Zorg ervoor dat het **inrichten:** de regel heeft geen voorafgaande spatie en dat geneste items met twee spaties worden inge sprongen.

### <a name="windows-device"></a>Windows-apparaat

Installeer de IoT Edge runtime op het apparaat waarvoor u een afgeleide-apparaatwachtwoord hebt gegenereerd. U configureert de IoT Edge runtime voor automatisch, niet hand matig, provisioning.

Zie [install the Azure IOT Edge runtime on Windows](how-to-install-iot-edge-windows.md)(Engelstalig) voor meer informatie over het installeren van IOT Edge op Windows, inclusief vereisten en instructies voor taken zoals het beheren van containers en het bijwerken van IOT Edge.

1. Open een PowerShell-venster in de beheerdersmodus. Zorg ervoor dat u een AMD64-sessie van Power shell gebruikt bij het installeren van IoT Edge, niet in Power shell (x86).

1. Met de opdracht **Deploy-IoTEdge** wordt gecontroleerd of de Windows-computer een ondersteunde versie heeft, wordt de functie containers ingeschakeld, waarna de Moby-runtime en de IOT Edge-runtime worden gedownload. De opdracht wordt standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Op dit moment kunnen IoT-kern apparaten automatisch opnieuw worden opgestart. Op andere Windows 10-of Windows Server-apparaten wordt u mogelijk gevraagd om opnieuw op te starten. Als dit het geval is, start u het apparaat nu opnieuw op. Zodra het apparaat klaar is, voert u Power shell als beheerder opnieuw uit.

1. De **initialisatie-IoTEdge-** opdracht configureert de IOT Edge runtime op de computer. De opdracht wordt standaard ingesteld op hand matig inrichten met Windows-containers, tenzij u de vlag `-Dps` gebruikt om automatische inrichting te gebruiken.

   Vervang de waarden van de tijdelijke aanduidingen voor `{scope_id}`, `{registration_id}`en `{symmetric_key}` met de gegevens die u eerder hebt verzameld.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Controleer of geslaagde installatie

Als de runtime is gestart, kunt u met ingang van uw IoT-Hub en begin met het implementeren van IoT Edge-modules naar uw apparaat. Gebruik de volgende opdrachten op uw apparaat om te controleren dat de runtime geïnstalleerd en gestart.

### <a name="linux-device"></a>Linux-apparaat

Controleer de status van de IoT Edge-service.

```cmd/sh
systemctl status iotedge
```

Bekijk service Logboeken.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lijst met modules.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows-apparaat

Controleer de status van de IoT Edge-service.

```powershell
Get-Service iotedge
```

Bekijk service Logboeken.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lijst met modules.

```powershell
iotedge list
```

U kunt controleren of de afzonderlijke registratie die u hebt gemaakt in Device Provisioning Service is gebruikt. Navigeer naar het Device Provisioning service-exemplaar in het Azure Portal. Open de inschrijvings gegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de registratie is **toegewezen** en dat de apparaat-id wordt vermeld.

## <a name="next-steps"></a>Volgende stappen

Het inschrijvingsproces Device Provisioning Service kunt u instellen de apparaat-ID en het apparaat dubbele tags op hetzelfde moment als u het nieuwe apparaat inrichten. Deze waarden kunt u afzonderlijke apparaten of groepen van apparaten met behulp van automatische Apparaatbeheer. Meer informatie over het [implementeren en te bewaken IoT Edge-modules op schaal met Azure portal](how-to-deploy-monitor.md) of [met behulp van Azure CLI](how-to-deploy-monitor-cli.md).
