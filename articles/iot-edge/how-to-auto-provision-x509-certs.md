---
title: Apparaten automatisch inrichten met DPS met X.509-certificaten - Azure IoT Edge | Microsoft Documenten
description: X.509-certificaten gebruiken om automatische apparaatinrichting voor Azure IoT Edge te testen met de service voor apparaatinrichting
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48c8179af4a4b69924fb943ac98918b48d3a2008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537360"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Een IoT Edge-apparaat maken en inrichten met X.509-certificaten

Met de [Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml)u iot edge-apparaten automatisch inrichten met X.509-certificaten. Als u niet bekend bent met het proces van automatisch inrichten, bekijkt u de [concepten voor automatisch inrichten](../iot-dps/concepts-auto-provisioning.md) voordat u verdergaat.

In dit artikel ziet u hoe u een inschrijving voor de Apparaatinrichtingsservice maakt met X.509-certificaten op een IoT Edge-apparaat met de volgende stappen:

* Certificaten en sleutels genereren.
* Maak een afzonderlijke inschrijving voor een apparaat of een groepsinschrijving voor een set apparaten.
* Installeer de Runtime van IoT Edge en registreer het apparaat met IoT Hub.

Het gebruik van X.509-certificaten als attestmechanisme is een uitstekende manier om de productie te schalen en de inrichting van apparaten te vereenvoudigen. Doorgaans zijn X.509-certificaten gerangschikt in een vertrouwensketen van certificaten. Beginnend met een zelfondertekend of vertrouwd rootcertificaat, tekent elk certificaat in de keten het volgende lagere certificaat. Met dit patroon wordt een gedelegeerde vertrouwensketen gemaakt van het hoofdcertificaat naar beneden via elk tussencertificaat tot het uiteindelijke "blad"-certificaat dat op een apparaat is geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

* Een actieve IoT Hub.
* Een fysiek of virtueel apparaat dat het IoT Edge-apparaat moet zijn.
* De nieuwste versie van [Git](https://git-scm.com/download/) geïnstalleerd.
* Een exemplaar van de IoT Hub Device Provisioning Service in Azure, gekoppeld aan uw IoT-hub.
  * Als u geen apparaatinrichtingsservice-instantie hebt, volgt u de instructies in [Het instellen van de IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).
  * Nadat u de service voor apparaatinrichting hebt uitgevoerd, kopieert u de waarde van **id-bereik** vanaf de overzichtspagina. U gebruikt deze waarde wanneer u de Runtime van IoT Edge configureert.

## <a name="generate-device-identity-certificates"></a>Apparaatidentiteitscertificaten genereren

Het apparaatidentiteitscertificaat is een bladcertificaat dat via een vertrouwensketen van een certificaat verbinding maakt met het bovenste CA-certificaat (X.509-certificaat). Het apparaatidentiteitscertificaat moet de algemene naam (CN) hebben ingesteld op de apparaat-id die u wilt dat het apparaat in uw IoT-hub heeft.

Apparaatidentiteitscertificaten worden alleen gebruikt voor het inrichten van het IoT Edge-apparaat en het verifiëren van het apparaat met Azure IoT Hub. Ze ondertekenen geen certificaten, in tegenstelling tot de CA-certificaten die het IoT Edge-apparaat aan modules of leaf-apparaten presenteert voor verificatie. Zie de [gebruiksdetails van Azure IoT Edge-certificaat voor](iot-edge-certs.md)meer informatie .

Nadat u het apparaatidentiteitscertificaat hebt gemaakt, moet u twee bestanden hebben: een .cer- of .pem-bestand dat het openbare gedeelte van het certificaat bevat, en een .cer- of .pem-bestand met de privésleutel van het certificaat. Als u groepsinschrijving in DPS wilt gebruiken, hebt u ook het openbare gedeelte van een tussen- of basis-CA-certificaat nodig in dezelfde vertrouwensketen.

### <a name="use-test-certificates"></a>Testcertificaten gebruiken

Als u geen certificaatautoriteit beschikbaar hebt om nieuwe identiteitscertificaten te maken en dit scenario wilt uitproberen, bevat de Azure IoT Edge git-repository scripts die u gebruiken om testcertificaten te genereren. Deze certificaten zijn alleen ontworpen voor ontwikkelingstests en mogen niet worden gebruikt voor de productie.

Als u testcertificaten wilt maken, voert u de stappen uit in Democertificaten maken om de functies van [IoT Edge-apparaten te testen.](how-to-create-test-certificates.md) Vul de twee vereiste secties in om de scripts voor het genereren van certificaten in te stellen en een hoofd-CA-certificaat te maken. Volg vervolgens de stappen om een apparaatidentiteitscertificaat te maken. Wanneer u klaar bent, moet u de volgende certificaatketen en sleutelpaar hebben:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

U hebt beide certificaten nodig op het IoT Edge-apparaat. Als u individuele inschrijving en inschrijving in DPS gaat gebruiken, uploadt u het .cert.pem-bestand. Als u groepsinschrijving in DPS gaat gebruiken, hebt u ook een tussen- of basis-CA-certificaat nodig in dezelfde vertrouwensketen om te uploaden. Als u democertificaten gebruikt, gebruikt `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` u het certificaat voor groepsinschrijving.

## <a name="create-a-dps-individual-enrollment"></a>Een individuele DPS-inschrijving maken

Gebruik uw gegenereerde certificaten en sleutels om een afzonderlijke inschrijving in DPS te maken voor één IoT Edge-apparaat. Individuele inschrijvingen nemen het openbare gedeelte van het identiteitscertificaat van een apparaat en koppelen dat aan het certificaat op het apparaat.

Als u meerdere IoT Edge-apparaten wilt inrichten, voert u de stappen in de volgende sectie [uit, Een DPS-groepsinschrijving maken](#create-a-dps-group-enrollment).

Wanneer u een inschrijving maakt in DPS, u een **eerste apparaattweelingstatus**declareren. In de apparaattweeling u tags instellen op apparaten groeperen op elke statistiek die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om [automatische implementaties](how-to-deploy-monitor.md)te maken.

Zie [Apparaatinschrijvingen beheren](../iot-dps/how-to-manage-enrollments.md)voor meer informatie over inschrijvingen in de service voor apparaatinrichting.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw exemplaar van de IoT Hub Device Provisioning Service.

1. Selecteer **onder Instellingen**de optie **Inschrijvingen beheren**.

1. Selecteer **Afzonderlijke inschrijving toevoegen** en voer de volgende stappen uit om de inschrijving te configureren:  

   * **Mechanisme**: Selecteer **X.509**.

   * **Primair certificaat .pem- of .cer-bestand**: Upload het openbare bestand vanaf het apparaatidentiteitscertificaat. Als u de scripts hebt gebruikt om een testcertificaat te genereren, kiest u het volgende bestand:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`

   * **IoT Hub Device ID:** Geef een ID voor uw apparaat als u wilt. U apparaat-id's gebruiken om een individueel apparaat te targeten voor de implementatie van modules. Als u geen apparaat-id verstrekt, wordt de algemene naam (CN) in het X.509-certificaat gebruikt.

   * **IoT Edge-apparaat:** Selecteer **Waar** om te declareren dat de inschrijving voor een IoT Edge-apparaat is.

   * **Selecteer de IoT-hubs waaraan dit apparaat kan worden toegewezen:** Kies de gekoppelde IoT-hub waarmee u uw apparaat wilt verbinden. U meerdere hubs kiezen en het apparaat wordt aan een van deze hubs toegewezen volgens het geselecteerde toewijzingsbeleid.

   * **Eerste apparaat twin state:** Voeg een tag waarde worden toegevoegd aan het apparaat twin als je wilt. U tags gebruiken om doelgroepen van apparaten te gebruiken voor automatische implementatie. Bijvoorbeeld:

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

1. Selecteer **Opslaan**.

Nu er een inschrijving voor dit apparaat bestaat, kan de IoT Edge-runtime het apparaat tijdens de installatie automatisch inrichten. Ga verder met [de runtime-sectie IoT Edge installeren](#install-the-iot-edge-runtime) om uw IoT Edge-apparaat in te stellen.

## <a name="create-a-dps-group-enrollment"></a>Een DPS-groepsinschrijving maken

Gebruik uw gegenereerde certificaten en sleutels om een groepsinschrijving te maken in DPS voor meerdere IoT Edge-apparaten. Groepsinschrijvingen gebruiken een tussenliggend of basis-CA-certificaat uit de vertrouwensketen van het certificaat dat wordt gebruikt om de identiteitscertificaten voor afzonderlijke apparaten te genereren.

Als u in plaats daarvan één IoT Edge-apparaat wilt inrichten, volgt u de stappen in de vorige sectie, [Een individuele inschrijving voor DPS maken](#create-a-dps-individual-enrollment).

Wanneer u een inschrijving maakt in DPS, u een **eerste apparaattweelingstatus**declareren. In de apparaattweeling u tags instellen op apparaten groeperen op elke statistiek die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om [automatische implementaties](how-to-deploy-monitor.md)te maken.

### <a name="verify-your-root-certificate"></a>Uw basiscertificaat verifiëren

Wanneer u een inschrijvingsgroep maakt, u een geverifieerd certificaat gebruiken. U een certificaat verifiëren met DPS door aan te tonen dat u eigenaar bent van het rootcertificaat. Zie [Hoe u bewijs van bezit voor X.509 CA-certificaten doen](../iot-dps/how-to-verify-certificates.md)voor meer informatie.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw exemplaar van de IoT Hub Device Provisioning Service.

1. Selecteer **Certificaten** in het linkermenu.

1. Selecteer **Toevoegen** om een nieuw certificaat toe te voegen.

1. Voer een vriendelijke naam in voor uw certificaat en blader naar het .cer- of .pem-bestand dat het openbare deel van uw X.509-certificaat vertegenwoordigt.

   Als u de democertificaten gebruikt, `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` uploadt u het certificaat.

1. Selecteer **Opslaan**.

1. Uw certificaat moet nu worden vermeld op de pagina **Certificaten.** Selecteer deze optie om de certificaatgegevens te openen.

1. Selecteer **Verificatiecode genereren** en kopieer de gegenereerde code.

1. Of u nu uw eigen CA-certificaat hebt meegenomen of de democertificaten gebruikt, u de verificatietool in de IoT Edge-repository gebruiken om het bewijs van bezit te verifiëren. Het verificatiehulpprogramma gebruikt uw CA-certificaat om een nieuw certificaat te ondertekenen met de opgegeven verificatiecode als onderwerpnaam.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Upload op dezelfde pagina met certificaatgegevens in de Azure-portal het nieuw gegenereerde verificatiecertificaat.

1. Selecteer **Verifiëren**.

### <a name="create-enrollment-group"></a>Inschrijvingsgroep maken

Zie [Apparaatinschrijvingen beheren](../iot-dps/how-to-manage-enrollments.md)voor meer informatie over inschrijvingen in de service voor apparaatinrichting.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw exemplaar van de IoT Hub Device Provisioning Service.

1. Selecteer **onder Instellingen**de optie **Inschrijvingen beheren**.

1. Selecteer **Inschrijvingsgroep toevoegen** en voer de volgende stappen uit om de inschrijving te configureren:

   * **Groepsnaam:** geef een gedenkwaardige naam op voor deze groepsinschrijving.

   * **Type attest :** Selecteer **Certificaat**.

   * **IoT Edge-apparaat:** **Selecteer True**. Voor een groepsinschrijving moeten alle apparaten IoT Edge-apparaten zijn of geen van deze apparaten.

   * **Certificaattype:** selecteer **CA-certificaat** als u een geverifieerd CA-certificaat hebt opgeslagen bij DPS of **Intermediate Certificate** als u een nieuw bestand wilt uploaden voor alleen deze inschrijving.

   * **Primair certificaat:** Als u IN de laatste sectie het CA-certificaat hebt gekozen, kiest u uw certificaat in de vervolgkeuzelijst. Als u tussencertificaat hebt gekozen, uploadt u het openbare bestand van een CA-certificaat in de vertrouwensketen van het certificaat dat is gebruikt om de apparaatidentiteitscertificaten te genereren.

   * **Selecteer de IoT-hubs waaraan dit apparaat kan worden toegewezen:** Kies de gekoppelde IoT-hub waarmee u uw apparaat wilt verbinden. U meerdere hubs kiezen en het apparaat wordt aan een van deze hubs toegewezen volgens het geselecteerde toewijzingsbeleid.

   * **Eerste apparaat twin state:** Voeg een tag waarde worden toegevoegd aan het apparaat twin als je wilt. U tags gebruiken om doelgroepen van apparaten te gebruiken voor automatische implementatie. Bijvoorbeeld:

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

1. Selecteer **Opslaan**.

Nu er een inschrijving voor dit apparaat bestaat, kan de IoT Edge-runtime het apparaat tijdens de installatie automatisch inrichten. Ga verder naar de volgende sectie om uw IoT Edge-apparaat in te stellen.

## <a name="install-the-iot-edge-runtime"></a>De runtime van IoT Edge installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden uitgevoerd in containers en u extra containers op het apparaat implementeren, zodat u code aan de rand uitvoeren.

X.509-inrichting met DPS wordt alleen ondersteund in IoT Edge-versie 1.0.9 of nieuwer.

U hebt de volgende informatie nodig bij het inrichten van uw apparaat:

* De waarde van de DPS **ID-scope.** U deze waarde ophalen op de overzichtspagina van uw DPS-exemplaar in de Azure-portal.
* Het apparaat-identiteitscertificaatbestand op het apparaat.
* Het apparaat-identiteitssleutelbestand op het apparaat.
* Een optionele registratie-ID (getrokken uit de algemene naam in het apparaat identiteitsbewijs als niet geleverd).

### <a name="linux-device"></a>Linux-apparaat

Gebruik de volgende koppeling om de Azure IoT Edge-runtime op uw apparaat te installeren met behulp van de opdrachten die geschikt zijn voor de architectuur van uw apparaat. Wanneer u bij de sectie over het configureren van de beveiligingsdaemon komt, configureert u de IoT Edge-runtime voor X.509-automatische, niet handmatige, inrichting. U moet alle informatie en certificaatbestanden hebben die u nodig hebt na het voltooien van de vorige delen van dit artikel.

[De Azure IoT Edge-runtime op Linux installeren](how-to-install-iot-edge-linux.md)

Wanneer u het X.509-certificaat en de belangrijkste informatie toevoegt aan het config.yaml-bestand, moeten de paden worden opgegeven als bestands-URI's. Bijvoorbeeld:

* `file:///<path>/identity_certificate.pem`
* `file:///<path>/identity_key.pem`

De sectie in het configuratiebestand voor X.509 automatische inrichting ziet er als volgt uit:

```yaml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Vervang de tijdelijke `scope_id`aanduidingswaarden voor , `identity_cert`door `identity_pk` de scope-id van uw DPS-exemplaar en de URI's naar de cert- en sleutelbestandslocaties op uw apparaat. Geef `registration_id` een voor het apparaat als u wilt, of laat deze regel commentaar uit om het apparaat te registreren met de CN-naam van het identiteitscertificaat.

Start de beveiligingsdaemon altijd opnieuw na het bijwerken van het config.yaml-bestand.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows-apparaat

Installeer de IoT Edge-runtime op het apparaat waarvoor u het identiteitscertificaat en de identiteitssleutel hebt gegenereerd. U configureert de 3T Edge-runtime voor automatische, niet handmatige inprovisioning.

Zie De runtime van [Azure IoT Edge installeren op Windows](how-to-install-iot-edge-windows.md)voor meer gedetailleerde informatie over het installeren van IoT Edge op Windows, inclusief vereisten en instructies voor taken zoals het beheren van containers en het bijwerken van IoT Edge.

1. Open een PowerShell-venster in de beheerdersmodus. Zorg ervoor dat u een AMD64-sessie van PowerShell gebruikt bij het installeren van IoT Edge, niet PowerShell (x86).

1. De opdracht **Deploy-IoTEdge** controleert of uw Windows-machine een ondersteunde versie heeft, schakelt de functie containers in en downloadt vervolgens de moby-runtime en de IoT Edge-runtime. De opdracht is standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Op dit moment kunnen IoT Core-apparaten automatisch opnieuw worden opgestart. Andere Windows 10- of Windows Server-apparaten kunnen u vragen om opnieuw op te starten. Start het apparaat nu opnieuw op. Zodra uw apparaat klaar is, voert u PowerShell opnieuw uit als beheerder.

1. Met de opdracht **Initialize-IoTEdge** configureert u de runtime van IoT Edge op uw machine. De opdracht is standaard ingesteld op `-Dps` handmatige inprovisioning, tenzij u de vlag gebruikt om automatische inrichting te gebruiken.

   Vervang de tijdelijke `{scope_id}`aanduidingswaarden voor , `{identity cert path}`en `{identity key path}` door de juiste waarden uit uw DPS-exemplaar en de bestandspaden op uw apparaat. Als u de registratie-ID `-RegistrationId {registration_id}` wilt opgeven, moet u ook de tijdelijke aanduiding vervangen.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Het config.yaml-bestand slaat uw certificaat en belangrijke informatie op als bestands-URI's. De opdracht Initialize-IoTEdge verwerkt deze opmaakstap echter voor u, zodat u het absolute pad naar het certificaat en de belangrijkste bestanden op uw apparaat bieden.

## <a name="verify-successful-installation"></a>Geslaagde installatie verifiëren

Als de runtime succesvol is gestart, u naar uw IoT-hub gaan en IoT Edge-modules op uw apparaat implementeren.

U controleren of de afzonderlijke inschrijving die u hebt gemaakt in de service voor apparaatinrichting is gebruikt. Navigeer naar de instantie Device Provisioning Service in de Azure-portal. Open de inschrijvingsgegevens voor de afzonderlijke inschrijving die u hebt gemaakt. De status van de inschrijving is **toegewezen** en de apparaat-id wordt weergegeven.

Gebruik de volgende opdrachten op uw apparaat om te controleren of de runtime is geïnstalleerd en gestart.

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

## <a name="next-steps"></a>Volgende stappen

Met het inschrijvingsproces voor apparaatinrichtingsservice u de apparaat-id en de dubbele tags van het apparaat instellen op hetzelfde moment als het nieuwe apparaat. U deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te targeten met automatisch apparaatbeheer. Meer informatie over het [implementeren en bewaken van IoT Edge-modules op schaal met behulp van de Azure-portal](how-to-deploy-monitor.md) of het gebruik van Azure [CLI.](how-to-deploy-monitor-cli.md)
