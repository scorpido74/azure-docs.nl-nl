---
title: Testcertificaten maken - Azure IoT Edge | Microsoft Documenten
description: Maak testcertificaten en leer hoe u ze installeren op een Azure IoT Edge-apparaat om u voor te bereiden op productie-implementatie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 364846f6cef196f6cefa7872af48f262b387db4f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393823"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Democertificaten maken om de functies van IoT Edge-apparaten te testen

IoT Edge-apparaten hebben certificaten nodig voor veilige communicatie tussen de runtime, de modules en alle downstream-apparaten.
Als u geen certificaatautoriteit hebt om de vereiste certificaten te maken, u democertificaten gebruiken om IoT Edge-functies in uw testomgeving uit te proberen.
In dit artikel wordt de functionaliteit beschreven van de scripts voor het genereren van certificaten die IoT Edge biedt voor het testen.

Deze certificaten verlopen binnen 30 dagen en mogen niet worden gebruikt in een productiescenario.

U certificaten maken op elke machine en deze vervolgens kopiëren naar uw IoT Edge-apparaat.
Het is eenvoudiger om uw primaire machine te gebruiken om de certificaten te maken in plaats van ze te genereren op uw IoT Edge-apparaat zelf.
Door uw primaire machine te gebruiken, u de scripts één keer instellen en vervolgens het proces herhalen om certificaten voor meerdere apparaten te maken.

Volg de volgende stappen om democertificaten te maken voor het testen van uw IoT Edge-scenario:

1. [Scripts instellen](#set-up-scripts) voor het genereren van certificaten op uw apparaat.
2. [Maak het basis-CA-certificaat](#create-root-ca-certificate) dat u gebruikt om alle andere certificaten voor uw scenario te ondertekenen.
3. Genereer de certificaten die u nodig hebt voor het scenario dat u wilt testen:
   * [Maak identiteitscertificaten voor IoT Edge-apparaten](#create-iot-edge-device-identity-certificates) om automatische inrichting te testen met de IoT Hub Device Provisioning Service.
   * [Maak CA-certificaten van IoT Edge-apparaat](#create-iot-edge-device-ca-certificates) om productiescenario's of gatewayscenario's te testen.
   * [Maak downstream-apparaatcertificaten](#create-downstream-device-certificates) om downstream-apparaten te verifiëren naar IoT Hub in een gatewayscenario.

## <a name="prerequisites"></a>Vereisten

Een ontwikkelmachine met Git geïnstalleerd.

## <a name="set-up-scripts"></a>Scripts instellen

De IoT Edge-repository op GitHub bevat scripts voor het genereren van certificaten die u gebruiken om democertificaten te maken.
In deze sectie vindt u instructies voor het voorbereiden van de scripts die op uw computer worden uitgevoerd, op Windows of Linux.
Als je op een Linux-machine zit, ga je verder naar [Instellen op Linux.](#set-up-on-linux)

### <a name="set-up-on-windows"></a>Instellen op Windows

Als u democertificaten wilt maken op een Windows-apparaat, moet u OpenSSL installeren en vervolgens de generatiescripts klonen en deze instellen om lokaal in PowerShell te worden uitgevoerd.

#### <a name="install-openssl"></a>OpenSSL installeren

Installeer OpenSSL voor Windows op de machine die u gebruikt om de certificaten te genereren.
Als U OpenSSL al op uw Windows-apparaat hebt geïnstalleerd, u deze stap overslaan, maar ervoor zorgen dat openssl.exe beschikbaar is in de variabele PATH-omgeving.

Er zijn verschillende manieren om OpenSSL te installeren, waaronder de volgende opties:

* **Eenvoudiger:** Download en installeer alle [OpenSSL-binaries](https://wiki.openssl.org/index.php/Binaries)van derden, bijvoorbeeld van [OpenSSL op SourceForge.](https://sourceforge.net/projects/openssl/) Voeg het volledige pad toe aan openssl.exe aan de variabele PATH-omgeving.

* **Aanbevolen:** Download de OpenSSL broncode en bouw de binaire bestanden op uw machine door uzelf of via [vcpkg](https://github.com/Microsoft/vcpkg). De onderstaande instructies gebruiken vcpkg om broncode te downloaden, te compileren en OpenSSL met eenvoudige stappen op uw Windows-machine te installeren.

   1. Navigeer naar een map waar u vcpkg wilt installeren. Volg de instructies om [vcpkg](https://github.com/Microsoft/vcpkg)te downloaden en te installeren.

   2. Zodra vcpkg is geïnstalleerd, voert u de volgende opdracht uit vanaf een PowerShell-prompt om het OpenSSL-pakket voor Windows x64 te installeren. De installatie duurt meestal ongeveer 5 minuten om te voltooien.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Voeg `<vcpkg path>\installed\x64-windows\tools\openssl` toe aan de variabele PATH-omgeving, zodat het openssl.exe-bestand beschikbaar is voor aanroep.

#### <a name="prepare-scripts-in-powershell"></a>Scripts voorbereiden in PowerShell

De Azure IoT Edge git-repository bevat scripts die u gebruiken om testcertificaten te genereren.
In deze sectie kloont u de IoT Edge repo en voert u de scripts uit.

1. Open een PowerShell-venster in de beheerdersmodus.

2. Kloon de IoT Edge git repo, die scripts bevat om democertificaten te genereren. Gebruik `git clone` de opdracht of [download de ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigeer naar de map waarin u wilt werken. In dit artikel noemen we deze directory * \<WRKDIR>. * Alle certificaten en sleutels worden gemaakt in deze werkmap.

4. Kopieer de configuratie- en scriptbestanden van de gekloonde repo naar uw werkmap.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Als u de repo als zip hebt gedownload, is `iotedge-master` de naam van de map en is de rest van het pad hetzelfde.

5. Schakel PowerShell in om de scripts uit te voeren.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Breng de functies die door de scripts worden gebruikt naar de wereldwijde naamruimte van PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   In het PowerShell-venster wordt een waarschuwing weergegeven dat de certificaten die door dit script worden gegenereerd, alleen voor testdoeleinden zijn en niet mogen worden gebruikt in productiescenario's.

7. Controleer of OpenSSL correct is geïnstalleerd en controleer of er geen naambotsingen zijn met bestaande certificaten. Als er problemen zijn, moet de scriptuitvoer beschrijven hoe u deze op uw systeem oplossen.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Instellen op Linux

Als u democertificaten wilt maken op een Windows-apparaat, moet u de generatiescripts klonen en deze instellen om lokaal in bash uit te voeren.

1. Kloon de IoT Edge git repo, die scripts bevat om democertificaten te genereren.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigeer naar de map waarin u wilt werken. We zullen verwijzen naar deze directory in het hele artikel als * \<WRKDIR>*. Alle certificaat- en sleutelbestanden worden in deze map gemaakt.
  
3. Kopieer de config- en scriptbestanden van de gekloonde IoT Edge-repo naar uw werkmap.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Root CA-certificaat maken

Het hoofd-CA-certificaat wordt gebruikt om alle andere democertificaten te maken voor het testen van een IoT Edge-scenario.
U hetzelfde basis-CA-certificaat blijven gebruiken om democertificaten te maken voor meerdere IoT Edge- of downstream-apparaten.

Als u al één basis-CA-certificaat in uw werkmap hebt, maakt u geen nieuw certificaat.
Het nieuwe root CA-certificaat overschrijft het oude en eventuele downstreamcertificaten die van het oude certificaat zijn gemaakt, werken niet meer.
Als u meerdere basisCA-certificaten wilt, moet u deze beheren in afzonderlijke mappen.

Volg de stappen in de sectie Scripts [instellen](#set-up-scripts) om een werkmap voor te bereiden met de scripts voor het genereren van democertificaten voordat u verdergaat met de stappen in deze sectie.

### <a name="windows"></a>Windows

1. Navigeer naar de werkmap waar u de scripts voor het genereren van certificaten hebt geplaatst.

1. Maak het basis-CA-certificaat en laat het één tussencertificaat ondertekenen. De certificaten worden allemaal in uw werkmap geplaatst.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt, maar wanneer artikelen om het **basis-CA-certificaat**vragen, gebruikt u het volgende bestand:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navigeer naar de werkmap waar u de scripts voor het genereren van certificaten hebt geplaatst.

1. Maak het basis-CA-certificaat en één tussencertificaat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt, maar wanneer artikelen om het **basis-CA-certificaat**vragen, gebruikt u het volgende bestand:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Ca-certificaten voor IoT Edge-apparaten maken

Elk IoT Edge-apparaat dat naar productie gaat, heeft een CA-certificaat van het apparaat nodig dat wordt vermeld in het config.yaml-bestand.
Het CA-certificaat van het apparaat is verantwoordelijk voor het maken van certificaten voor modules die op het apparaat worden uitgevoerd.
Het is ook hoe het IoT Edge-apparaat zijn identiteit verifieert wanneer het verbinding maakt met downstream-apparaten.

Apparaat CA-certificaten worden gebruikt in de sectie **Certificaat** van het config.yaml-bestand op het IoT Edge-apparaat.

Volg de stappen in de secties Scripts [instellen](#set-up-scripts) en [Root CA-certificaat maken](#create-root-ca-certificate) voordat u verdergaat met de stappen in deze sectie.

### <a name="windows"></a>Windows

1. Navigeer naar de werkmap met de scripts voor het genereren van certificaten en het basis-CA-certificaat.

2. Maak het CA-certificaat en de privésleutel van het IoT Edge-apparaat met de volgende opdracht. Geef een naam op voor het CA-certificaat, bijvoorbeeld **MyEdgeDeviceCA,** dat wordt gebruikt om de uitvoerbestanden een naam te geven.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt. Het volgende certificaat en sleutelpaar moet worden gekopieerd naar een IoT Edge-apparaat en wordt verwezen in het config.yaml-bestand:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

De naam van het gateway-apparaat dat in deze scripts wordt doorgegeven, mag niet hetzelfde zijn als de parameter hostname in config.yaml of de id van het apparaat in IoT Hub.
De scripts helpen u problemen te voorkomen door een ".ca"-tekenreeks toe te schrijven aan de naam van het gateway-apparaat om de naambotsing te voorkomen in het geval een gebruiker IoT Edge op beide plaatsen met dezelfde naam instelt.
Het is echter een goede gewoonte om te voorkomen dat u dezelfde naam gebruikt.

### <a name="linux"></a>Linux

1. Navigeer naar de werkmap met de scripts voor het genereren van certificaten en het basis-CA-certificaat.

2. Maak het CA-certificaat en de privésleutel van het IoT Edge-apparaat met de volgende opdracht. Geef een naam op voor het CA-certificaat, bijvoorbeeld **MyEdgeDeviceCA,** dat wordt gebruikt om de uitvoerbestanden een naam te geven.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt. Het volgende certificaat en sleutelpaar moet worden gekopieerd naar een IoT Edge-apparaat en wordt verwezen in het config.yaml-bestand:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

De naam van het gateway-apparaat dat in deze scripts wordt doorgegeven, mag niet hetzelfde zijn als de parameter hostname in config.yaml of de id van het apparaat in IoT Hub.
De scripts helpen u problemen te voorkomen door een ".ca"-tekenreeks toe te schrijven aan de naam van het gateway-apparaat om de naambotsing te voorkomen in het geval een gebruiker IoT Edge op beide plaatsen met dezelfde naam instelt.
Het is echter een goede gewoonte om te voorkomen dat u dezelfde naam gebruikt.

## <a name="create-iot-edge-device-identity-certificates"></a>Identiteitscertificaten voor IoT Edge-apparaten maken

Apparaatidentiteitscertificaten worden gebruikt voor het inrichten van IoT Edge-apparaten via de [DPS (Azure IoT Hub Device Provisioning Service).](../iot-dps/index.yml)

Apparaatidentiteitscertificaten worden gebruikt in het gedeelte Inrichting van **het** config.yaml-bestand op het IoT Edge-apparaat.

Volg de stappen in de secties Scripts [instellen](#set-up-scripts) en [Root CA-certificaat maken](#create-root-ca-certificate) voordat u verdergaat met de stappen in deze sectie.

### <a name="windows"></a>Windows

Maak het iE-identiteitscertificaat en privésleutel van het IoT Edge-apparaat met de volgende opdracht:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

De naam die u aan deze opdracht doorgeeft, is de apparaat-id voor het IoT Edge-apparaat in IoT Hub.

Met de opdracht Nieuwe apparaatidentiteit worden verschillende certificaat- en sleutelbestanden gemaakt, waaronder drie die u gebruikt bij het maken van een afzonderlijke inschrijving in DPS en het installeren van de runtime van IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Maak het iE-identiteitscertificaat en privésleutel van het IoT Edge-apparaat met de volgende opdracht:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

De naam die u aan deze opdracht doorgeeft, is de apparaat-id voor het IoT Edge-apparaat in IoT Hub.

Het script maakt verschillende certificaat- en sleutelbestanden, waaronder drie die u gebruikt bij het maken van een afzonderlijke inschrijving in DPS en het installeren van de Runtime van IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>Downstreamapparaatcertificaten maken

Als u een downstream IoT-apparaat instelt voor een gatewayscenario, u democertificaten genereren voor X.509-verificatie.
Er zijn twee manieren om een IoT-apparaat te verifiëren met X.509-certificaten: met behulp van zelfondertekende certs of het gebruik van certificaatautoriteit (CA) ondertekende certs.
Voor X.509 zelfondertekende verificatie, ook wel thumbprint-verificatie genoemd, moet u nieuwe certificaten maken om op uw IoT-apparaat te plaatsen.
Deze certificaten hebben een duimafdruk die u deelt met IoT Hub voor verificatie.
Voor door X.509-certificaatautoriteit (CA) ondertekende verificatie hebt u een basis-CA-certificaat nodig dat is geregistreerd in IoT Hub en dat u gebruikt om certificaten voor uw IoT-apparaat te ondertekenen.
Elk apparaat dat gebruik maakt van een certificaat dat is uitgegeven door het basis-CA-certificaat of een van de tussentijdse certificaten, mag zich verifiëren.

Met de scripts voor het genereren van certificaten u democertificaten maken om een van deze verificatiescenario's uit te testen.

Volg de stappen in de secties Scripts [instellen](#set-up-scripts) en [Root CA-certificaat maken](#create-root-ca-certificate) voordat u verdergaat met de stappen in deze sectie.

### <a name="self-signed-certificates"></a>Zelfondertekende certificaten

Wanneer u een IoT-apparaat verifieert met zelfondertekende certificaten, moet u apparaatcertificaten maken op basis van het basis-CA-certificaat voor uw oplossing.
Vervolgens haalt u een hexadecimale "vingerafdruk" op uit de certificaten die u aan IoT Hub moet verstrekken.
Uw IoT-apparaat heeft ook een kopie van de apparaatcertificaten nodig, zodat het kan verifiëren met IoT Hub.

#### <a name="windows"></a>Windows

1. Navigeer naar de werkmap met de scripts voor het genereren van certificaten en het basis-CA-certificaat.

2. Maak twee certificaten (primair en secundair) voor het downstream-apparaat. Een eenvoudige naamgevingsconventie om te gebruiken is het maken van de certificaten met de naam van het IoT-apparaat en vervolgens het primaire of secundaire label. Bijvoorbeeld:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt. Het volgende certificaat en de volgende sleutelparen moeten worden gekopieerd naar het downstream IoT-apparaat en worden verwezen in de toepassingen die verbinding maken met IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Haal de SHA1-vingerafdruk (een duimafdruk in IoT Hub-contexten genoemd) uit elk certificaat. De vingerafdruk is een 40 hexadecimale teken string. Gebruik de volgende opdracht openssl om het certificaat te bekijken en de vingerafdruk te vinden:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Voer deze opdracht twee keer uit, één keer voor het primaire certificaat en één keer voor het secundaire certificaat. U verstrekt vingerafdrukken voor beide certificaten wanneer u een nieuw IoT-apparaat registreert met behulp van zelfondertekende X.509-certificaten.

#### <a name="linux"></a>Linux

1. Navigeer naar de werkmap met de scripts voor het genereren van certificaten en het basis-CA-certificaat.

2. Maak twee certificaten (primair en secundair) voor het downstream-apparaat. Een eenvoudige naamgevingsconventie om te gebruiken is het maken van de certificaten met de naam van het IoT-apparaat en vervolgens het primaire of secundaire label. Bijvoorbeeld:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt. Het volgende certificaat en de volgende sleutelparen moeten worden gekopieerd naar het downstream IoT-apparaat en worden verwezen in de toepassingen die verbinding maken met IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Haal de SHA1-vingerafdruk (een duimafdruk in IoT Hub-contexten genoemd) uit elk certificaat. De vingerafdruk is een 40 hexadecimale teken string. Gebruik de volgende opdracht openssl om het certificaat te bekijken en de vingerafdruk te vinden:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   U verstrekt zowel de primaire als de secundaire vingerafdruk wanneer u een nieuw IoT-apparaat registreert met behulp van zelfondertekende X.509-certificaten.

### <a name="ca-signed-certificates"></a>CA-ondertekende certificaten

Wanneer u een IoT-apparaat verifieert met zelfondertekende certificaten, moet u het basis-CA-certificaat voor uw oplossing uploaden naar IoT Hub.
Vervolgens voert u een verificatie uit om aan IoT Hub te bewijzen dat u eigenaar bent van het basis-CA-certificaat.
Ten slotte gebruikt u hetzelfde basis-CA-certificaat om apparaatcertificaten te maken om op uw IoT-apparaat te plaatsen, zodat het kan verifiëren met IoT Hub.

De certificaten in deze sectie zijn voor de stappen in [X.509-beveiliging instellen in uw Azure IoT-hub.](../iot-hub/iot-hub-security-x509-get-started.md)

#### <a name="windows"></a>Windows

1. Upload het bestand met het basis-CA-certificaat vanuit uw werkmap `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`naar uw IoT-hub.

2. Gebruik de code in de Azure-portal om te controleren of u eigenaar bent van dat basis-CA-certificaat.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Maak een certificaatketen voor uw downstream-apparaat. Gebruik dezelfde apparaat-id waarmee het apparaat is geregistreerd in IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt. Het volgende certificaat en de volgende sleutelparen moeten worden gekopieerd naar het downstream IoT-apparaat en worden verwezen in de toepassingen die verbinding maken met IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Upload het bestand met het basis-CA-certificaat vanuit uw werkmap `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`naar uw IoT-hub.

2. Gebruik de code in de Azure-portal om te controleren of u eigenaar bent van dat basis-CA-certificaat.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Maak een certificaatketen voor uw downstream-apparaat. Gebruik dezelfde apparaat-id waarmee het apparaat is geregistreerd in IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt. Het volgende certificaat en de volgende sleutelparen moeten worden gekopieerd naar het downstream IoT-apparaat en worden verwezen in de toepassingen die verbinding maken met IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
