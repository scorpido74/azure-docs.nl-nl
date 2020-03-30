---
title: Azure IoT Edge op Linux installeren | Microsoft Documenten
description: Installatie-instructies voor Azure IoT Edge op Linux-apparaten met Ubuntu of Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535898"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>De Azure IoT Edge-runtime op op Debian gebaseerde Linux-systemen installeren

De Azure IoT Edge-runtime maakt van een apparaat een IoT Edge-apparaat. De runtime kan worden geïmplementeerd op apparaten zo klein als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, u bedrijfslogica vanuit de cloud implementeren. Zie De runtime van [Azure IoT Edge en de architectuur ervan begrijpen](iot-edge-runtime.md)voor meer informatie.

In dit artikel worden de stappen weergegeven voor het installeren van de Azure IoT Edge-runtime op een X64-, ARM32- of ARM64-Linux-apparaat. Wij bieden installatiepakketten voor Ubuntu Server 16.04, Ubuntu Server 18.04 en Raspbian Stretch. Raadpleeg [azure IoT Edge-ondersteunde systemen](support.md#operating-systems) voor een lijst met ondersteunde Linux-besturingssystemen en -architecturen.

> [!NOTE]
> Pakketten in de Linux software repositories zijn onderworpen aan de licentievoorwaarden in elk pakket (/usr/share/doc/*package-name).* Lees de licentievoorwaarden voordat u het pakket gebruikt. Uw installatie en gebruik van het pakket vormt uw aanvaarding van deze voorwaarden. Als u het niet eens bent met de licentievoorwaarden, gebruik dan het pakket niet.

## <a name="install-the-latest-runtime-version"></a>De nieuwste runtime-versie installeren

Gebruik de volgende secties om de meest recente versie van de Azure IoT Edge-runtime op uw apparaat te installeren.

>[!NOTE]
>Ondersteuning voor ARM64-apparaten is in [openbare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft-sleutel- en softwarerepository-feed registreren

Bereid uw apparaat voor op de installatie van de Runtime van IoT Edge.

Installeer de repository-configuratie. Kies de opdracht **16.04** of **18.04** die overeenkomt met het besturingssysteem van uw apparaat:

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Kopieer de gegenereerde lijst.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG-openbare sleutel installeren

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>De runtime van de container installeren

Azure IoT Edge is gebaseerd op een [OCI-compatibele](https://www.opencontainers.org/) containerruntime. Voor productiescenario's raden we u aan de [onderstaande Moby-engine](https://mobyproject.org/) te gebruiken. De Moby-engine is de enige containermotor die officieel wordt ondersteund met Azure IoT Edge. Docker CE/EE-containerafbeeldingen zijn compatibel met de Moby-runtime.

Pakketlijsten bijwerken op uw apparaat.

   ```bash
   sudo apt-get update
   ```

Installeer de Moby-motor.

   ```bash
   sudo apt-get install moby-engine
   ```

Installeer de Moby command-line interface (CLI). De CLI is handig voor ontwikkeling, maar optioneel voor productie-implementaties.

   ```bash
   sudo apt-get install moby-cli
   ```

Als er fouten optreden bij het installeren van de runtime van de Moby-container, voert u de stappen uit om [uw Linux-kernel te verifiëren voor Moby-compatibiliteit,](#verify-your-linux-kernel-for-moby-compatibility)die later in dit artikel wordt verstrekt.

### <a name="install-the-azure-iot-edge-security-daemon"></a>De Azure IoT Edge Security Daemon installeren

De **IoT Edge security daemon** biedt en handhaaft beveiligingsstandaarden op het IoT Edge-apparaat. De daemon start op elke boot en start het apparaat door de rest van de IoT Edge runtime te starten.

De installatie opdracht installeert ook de standaard versie van de **libiothsm** indien niet al aanwezig.

Pakketlijsten bijwerken op uw apparaat.

   ```bash
   sudo apt-get update
   ```

Installeer de beveiliging daemon. Het pakket is `/etc/iotedge/`geïnstalleerd op .

   ```bash
   sudo apt-get install iotedge
   ```

Zodra IoT Edge is geïnstalleerd, wordt u door de uitvoer gevraagd om het configuratiebestand bij te werken. Volg de stappen in de sectie [De beveiligingsdaemon configureren](#configure-the-security-daemon) om apparaatinrichting te voltooien.

## <a name="install-a-specific-runtime-version"></a>Een specifieke runtime-versie installeren

Als u een specifieke versie van Moby en de Azure IoT Edge-runtime wilt installeren in plaats van de nieuwste versies te gebruiken, u de componentbestanden rechtstreeks vanuit de IoT Edge GitHub-opslagplaats targeten. Gebruik de volgende stappen om alle IoT Edge-componenten op uw apparaat te krijgen: de Moby-engine en CLI, de libiothsm en ten slotte de IoT Edge-beveiligingsdaemon. Ga naar de volgende sectie, [Configureer de beveiligingsdaemon](#configure-the-security-daemon)als u niet wilt overschakelen naar een specifieke runtime-versie.

1. Navigeer naar de [Azure IoT Edge-releases](https://github.com/Azure/azure-iotedge/releases)en zoek de releaseversie die u wilt targeten.

2. Vouw de sectie **Activa** voor die versie uit.

3. Er kunnen al dan niet updates voor de Moby-engine in een bepaalde release. Als u bestanden ziet die beginnen met **moby-engine** en **moby-cli,** gebruikt u de volgende opdrachten om deze onderdelen bij te werken. Als u geen Moby-bestanden ziet, gaat u terug naar de oudere release-elementen totdat u de meest recente versie hebt gevonden.

   1. Zoek het **moby-engine-bestand** dat overeenkomt met de architectuur van uw IoT Edge-apparaat. Klik met de rechtermuisknop op de bestandskoppeling en kopieer het linkadres.

   2. Gebruik de gekopieerde koppeling in de volgende opdracht om die versie van de Moby-engine te installeren:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Zoek het **moby-cli-bestand** dat overeenkomt met de architectuur van uw IoT Edge-apparaat. De Moby CLI is een optioneel onderdeel, maar kan nuttig zijn tijdens de ontwikkeling. Klik met de rechtermuisknop op de bestandskoppeling en kopieer het linkadres.

   4. Gebruik de gekopieerde koppeling in de volgende opdracht om die versie van de Moby CLI te installeren:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Elke release moet nieuwe bestanden bevatten voor de IoT Edge security daemon en de hsmlib. Gebruik de volgende opdrachten om deze onderdelen bij te werken.

   1. Zoek het **libiothsm-std-bestand** dat overeenkomt met de architectuur van uw IoT Edge-apparaat. Klik met de rechtermuisknop op de bestandskoppeling en kopieer het linkadres.

   2. Gebruik de gekopieerde koppeling in de volgende opdracht om die versie van de hsmlib te installeren:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Zoek het **iotedge-bestand** dat overeenkomt met de architectuur van uw IoT Edge-apparaat. Klik met de rechtermuisknop op de bestandskoppeling en kopieer het linkadres.

   4. Gebruik de gekopieerde koppeling in de volgende opdracht om die versie van de IoT Edge-beveiligingsdaemon te installeren.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Zodra IoT Edge is geïnstalleerd, wordt u door de uitvoer gevraagd om het configuratiebestand bij te werken. Volg de stappen in de volgende sectie om apparaatinrichting te voltooien.

## <a name="configure-the-security-daemon"></a>De beveiligingsdaemon configureren

Configureer de 3T Edge-runtime om uw fysieke apparaat te koppelen aan een apparaatidentiteit die bestaat in een Azure IoT-hub.

De daemon kan worden geconfigureerd met `/etc/iotedge/config.yaml`behulp van het configuratiebestand op . Het bestand is standaard beveiligd met schrijven, u hebt mogelijk verhoogde machtigingen nodig om het te bewerken.

Eén IoT Edge-apparaat kan handmatig worden ingericht met behulp van een tekenreeks voor apparaatverbindingen die door IoT Hub worden geleverd. U de service voor apparaatinrichting ook gebruiken om apparaten automatisch in te richten, wat handig is wanneer u veel apparaten hebt om in te richten. Afhankelijk van uw inrichtende keuze kiest u het juiste installatiescript.

### <a name="option-1-manual-provisioning"></a>Optie 1: Handmatige inlevering

Als u een apparaat handmatig wilt inrichten, moet u het apparaateen tekenreeks voor [apparaatverbinding](how-to-register-device.md#register-in-the-azure-portal) verstrekken die u maken door een nieuw apparaat in uw IoT-hub te registreren.

Open het configuratiebestand.

```bash
sudo nano /etc/iotedge/config.yaml
```

Zoek de inrichtingsconfiguraties van het bestand en maak geen commentaar op de sectie **Handmatige inrichtingsconfiguratie.** Werk de waarde van **device_connection_string** bij met de verbindingstekenreeks vanaf uw IoT Edge-apparaat. Zorg ervoor dat andere inprovisioning secties worden becommentarieerd. Zorg ervoor dat de **inrichting:** lijn heeft geen voorafgaande witruimte en dat geneste items worden ingesprongen door twee spaties.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Als u de inhoud `Shift+Right Click` van `Shift+Insert`het klembord in Nano wilt plakken of op .

Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

Nadat u de provisioning-informatie in het configuratiebestand hebt ingevoerd, start u de daemon opnieuw op:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Optie 2: Automatische inrichting

IoT Edge-apparaten kunnen automatisch worden ingericht met de [DPS (Azure IoT Hub Device Provisioning Service).](../iot-dps/index.yml) Momenteel ondersteunt IoT Edge twee attestmechanismen bij het gebruik van automatische inrichting, maar uw hardwarevereisten kunnen van invloed zijn op uw keuzes. Raspberry Pi-apparaten worden bijvoorbeeld niet standaard geleverd met een TPM-chip (Trusted Platform Module). Raadpleeg voor meer informatie de volgende artikelen:

* [Een IoT Edge-apparaat maken en inrichten met een virtuele TPM op een Linux-vm](how-to-auto-provision-simulated-device-linux.md)
* [Een IoT Edge-apparaat maken en inrichten met X.509-certificaten](how-to-auto-provision-x509-certs.md)
* [Een IoT Edge-apparaat maken en inrichten met behulp van symmetrische sleutelattest](how-to-auto-provision-symmetric-keys.md)

Deze artikelen lopen u door het opzetten van inschrijvingen in DPS, en het genereren van de juiste certificaten of sleutels voor attestation. Ongeacht welk attestation-mechanisme u kiest, de provisioning-informatie wordt toegevoegd aan het IoT Edge-configuratiebestand op uw IoT Edge-apparaat.

Open het configuratiebestand.

```bash
sudo nano /etc/iotedge/config.yaml
```

Zoek de inrichtingsconfiguraties van het bestand en geef geen commentaar op de sectie die geschikt is voor uw attestmechanisme. Zorg ervoor dat andere inprovisioning secties worden becommentarieerd. De **inrichting:** lijn mag geen voorafgaande witruimte hebben en geneste items moeten worden ingesprongen door twee spaties. Werk de waarde van **scope_id** bij met de waarde van uw IoT Hub Device Provisioning Service-instantie en geef de juiste waarden voor de attestvelden.

TPM-attest:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

X.509 attest:

```yml
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

Symmetrische sleutelattest:

```yml
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

Als u de inhoud `Shift+Right Click` van `Shift+Insert`het klembord in Nano wilt plakken of op .

Sla het bestand op en sluit het. `CTRL + X`, `Y`, `Enter`

Nadat u de provisioning-informatie in het configuratiebestand hebt ingevoerd, start u de daemon opnieuw op:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Geslaagde installatie verifiëren

Als u de **handmatige configuratiestappen** in de vorige sectie hebt gebruikt, moet de TIoT Edge-runtime met succes worden ingericht en op uw apparaat worden uitgevoerd. Als u de **automatische configuratiestappen** hebt gebruikt, moet u enkele aanvullende stappen uitvoeren, zodat de runtime uw apparaat namens u kan registreren bij uw IoT-hub. Zie Voor de volgende stappen [een gesimuleerd TPM IoT Edge-apparaat maken en inrichten op een virtuele Linux-machine](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

U de status van de IoT Edge Daemon controleren:

```bash
systemctl status iotedge
```

Onderzoek daemon logs:

```bash
journalctl -u iotedge --no-pager --no-full
```

Voer een geautomatiseerde controle uit op de meest voorkomende configuratie- en netwerkfouten:

```bash
sudo iotedge check
```

Totdat u uw eerste module op IoT Edge op uw apparaat implementeert, wordt de **$edgeHub** systeemmodule niet op het apparaat geïmplementeerd. Als gevolg hiervan zal de geautomatiseerde `Edge Hub can bind to ports on host` controle een fout voor de verbindingscontrole retourneren. Deze fout kan worden genegeerd, tenzij deze optreedt nadat u een module op het apparaat hebt geïmplementeerd.

Tot slot, lijst met lopende modules:

```bash
sudo iotedge list
```

Na het installeren van IoT Edge op uw apparaat, is **edgeAgent**de enige module die u moet zien uitvoeren. Zodra u uw eerste implementatie hebt gemaakt, start de andere systeemmodule **$edgeHub** ook op het apparaat. Zie [IoT Edge-modules implementeren](how-to-deploy-modules-portal.md)voor meer informatie.

## <a name="tips-and-troubleshooting"></a>Tips en probleemoplossing

U hebt verhoogde bevoegdheden nodig om `iotedge`-opdrachten uit te voeren. Na het installeren van de runtime meldt u zich af bij uw machine en meldt u zich weer aan om uw machtigingen automatisch bij te werken. Tot die tijd, gebruik **sudo** in de voorkant van een `iotedge` van de commando's.

Op apparaten met beperkte resources wordt ten zeerste aanbevolen dat u de omgevingsvariabele *OptimizeForPerformance* instelt op *false* volgens de instructies in de [handleiding voor probleemoplossing.](troubleshoot.md)

Als uw netwerk met een proxyserver de stappen volgt in [Uw IoT Edge-apparaat configureren om te communiceren via een proxyserver.](how-to-configure-proxy-support.md)

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Controleer uw Linux-kernel op Moby-compatibiliteit

Veel fabrikanten van ingesloten apparaten verzenden apparaatafbeeldingen die aangepaste Linux-kernels bevatten zonder de functies die nodig zijn voor compatibiliteit met runtime van containers. Als u problemen ondervindt tijdens het installeren van de aanbevolen runtime van moby-container, u mogelijk uw Linux-kernelconfiguratie oplossen met behulp van het [check-config-script](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) van de officiële [Moby GitHub-opslagplaats.](https://github.com/moby/moby) Voer de volgende opdrachten uit op het apparaat om de configuratie van uw kernel te controleren:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Deze opdracht biedt een gedetailleerde uitvoer die de status bevat van kernelfuncties die worden gebruikt door de Moby-runtime. U wilt ervoor zorgen dat `Generally Necessary` `Network Drivers` alle items onder en zijn ingeschakeld om ervoor te zorgen dat uw kernel volledig compatibel is met de Moby runtime.  Als u ontbrekende functies hebt geïdentificeerd, schakelt u deze in door uw kernel van bron te herbouwen en de bijbehorende modules te selecteren voor opname in de juiste kernel .config.  Evenzo, als u gebruik maakt `defconfig` van `menuconfig`een kernel configuratie generator zoals of , vinden en inschakelen van de respectieve functies en uw kernel dienovereenkomstig opnieuw op te bouwen.  Zodra u uw nieuw gewijzigde kernel hebt geïmplementeerd, voert u het check-config-script opnieuw uit om te controleren of alle vereiste functies zijn ingeschakeld.

## <a name="uninstall-iot-edge"></a>IoT-rand verwijderen

Als u de IoT Edge-installatie van uw Linux-apparaat wilt verwijderen, gebruikt u de volgende opdrachten van de opdrachtregel.

De IoT Edge-runtime verwijderen.

```bash
sudo apt-get remove --purge iotedge
```

Wanneer de runtime van IoT Edge wordt verwijderd, worden de containers die het heeft gemaakt gestopt, maar nog steeds op uw apparaat. Bekijk alle containers om te zien welke nog over blijven.

```bash
sudo docker ps -a
```

Verwijder de containers van uw apparaat, inclusief de twee runtime-containers.

```bash
sudo docker rm -f <container name>
```

Verwijder ten slotte de runtime van de container van uw apparaat.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge-apparaat hebt ingericht met de runtime geïnstalleerd, u [IoT Edge-modules implementeren.](how-to-deploy-modules-portal.md)

Als u problemen ondervindt met het correct installeren van de Runtime van IoT Edge, raadpleegt u de [pagina probleemoplossing.](troubleshoot.md)

Zie [De IoT Edge-beveiligingsdaemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IoT Edge.
