---
title: Transparante gateway-apparaat - Azure IoT Edge maken | Microsoft Docs
description: Een Azure IoT Edge-apparaat gebruiken als een transparante gateway waarmee gegevens uit de downstream-apparaten kan worden verwerkt.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 467ec25bb9e41180da36f118094324e4fea48cf8
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266105"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Een IoT Edge-apparaat om te fungeren als een transparante gateway configureren

In dit artikel vindt u gedetailleerde instructies voor het configureren van een IoT Edge apparaat om te werken als een transparante gateway zodat andere apparaten kunnen communiceren met IoT Hub. In dit artikel wordt de term *IoT Edge-gateway* verwijst naar een IoT Edge-apparaat gebruikt als een transparante gateway. Zie [How a IOT edge-apparaten kunnen worden gebruikt als een gateway](./iot-edge-as-gateway.md)voor meer informatie.

>[!NOTE]
>Op dit moment:
> * Edge-apparaten kunnen geen verbinding maken met IoT Edge-gateways. 
> * Aangesloten apparaten niet uploaden van bestanden gebruiken.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de eerste stap behandeld:

1. **Het gateway apparaat moet in staat zijn om veilig verbinding te maken met downstream-apparaten, communicaties te ontvangen van downstream-apparaten en berichten te routeren naar de juiste bestemming.**
2. Het downstream-apparaat moet een apparaat-id hebben om te kunnen worden geverifieerd met IoT Hub en u moet communiceren via het gateway apparaat. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.
3. Het downstream-apparaat moet veilig verbinding kunnen maken met het gateway apparaat. Zie voor meer informatie, [een downstream-apparaat verbinden met Azure IoT Edge-gateway](how-to-connect-downstream-device.md).


Een apparaat kan alleen als gateway functioneren als het op een veilige manier verbinding kan maken met de downstream-apparaten. Azure IoT Edge kunt u een openbare-sleutelinfrastructuur (PKI) gebruiken voor het instellen van beveiligde verbindingen tussen apparaten. In dit geval toestemming we een downstream apparaat verbinding maakt met een IoT Edge-apparaat als een transparante gateway fungeert. Om redelijke beveiliging te behouden, moet het downstream-apparaat de identiteit van het gateway-apparaat bevestigen. Met deze identiteits controle voor komt u dat uw apparaten verbinding maken met mogelijk schadelijke gateways.

Een downstream-apparaat in een transparant Gateway scenario kan elke toepassing of elk platform zijn met een identiteit die is gemaakt met de [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub) -Cloud service. In veel gevallen gebruikt u deze toepassingen de [Azure IoT device-SDK](../iot-hub/iot-hub-devguide-sdks.md). Praktische overwegingen, een downstream apparaat mogelijk ook een toepassing die wordt uitgevoerd op het IoT Edge-gateway-apparaat zelf. Een IoT Edge apparaat kan echter niet worden downstream van een IoT Edge gateway. 

U kunt een certificaatinfrastructuur waarmee de vertrouwensrelatie die vereist zijn voor de topologie van uw apparaat-gateway maken. In dit artikel wordt ervan uitgegaan dat u dezelfde certificaat instelling gebruikt voor het inschakelen van [X. 509 ca-beveiliging](../iot-hub/iot-hub-x509ca-overview.md) in IOT hub, waarbij een X. 509 CA-certificaat is gekoppeld aan een specifieke IOT-hub (de basis-CA van de IOT-hub), een reeks certificaten die zijn ondertekend met deze certificerings instantie en een certificerings instantie voor het IoT Edge apparaat.

![Certificaat-gatewayupgrade](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>De term ' basis-CA ' die in dit artikel wordt gebruikt, verwijst naar het open bare certificaat van de bovenste certificerings instantie van de PKI-certificaat keten, en niet noodzakelijkerwijs de hoofdmap van het certificaat van een extern gepubliceerde certificerings instantie. In veel gevallen is het eigenlijk een openbaar certificaat van een tussenliggend CA. 

De gateway geeft het CA-certificaat van IoT Edge apparaat aan het downstream-apparaat tijdens de start van de verbinding. Het downstream-apparaat controleert of het CA-certificaat van IoT Edge apparaat is ondertekend door het basis-CA-certificaat. Met dit proces kan het downstream-apparaat controleren of de gateway afkomstig is van een vertrouwde bron.

De volgende stappen leiden u door het proces van het maken van de certificaten en het installeren ervan op de juiste plaatsen op de gateway. U kunt elke virtuele machine gebruiken voor het genereren van de certificaten, en kopieer deze vervolgens naar uw IoT Edge-apparaat. 

## <a name="prerequisites"></a>Vereisten

* Een ontwikkelings machine voor het maken van certificaten. 
* Een Azure IoT Edge-apparaat configureren als een gateway. Gebruik de IoT Edge installatie stappen voor een van de volgende besturings systemen:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Genereren van certificaten met Windows

Gebruik de stappen in deze sectie om test certificaten te genereren in Windows. U kunt een Windows-machine gebruiken om de certificaten te genereren en deze vervolgens kopiëren naar een IoT Edge apparaat dat wordt uitgevoerd op elk ondersteund besturings systeem. 

De certificaten die zijn gegenereerd in deze sectie zijn bedoeld alleen voor testdoeleinden. 

### <a name="install-openssl"></a>OpenSSL installeren

OpenSSL voor Windows installeren op de computer die u gebruikt voor het genereren van de certificaten. Als OpenSSL al op uw Windows-apparaat is geïnstalleerd, kunt u deze stap overs Laan, maar u moet ervoor zorgen dat openssl. exe beschikbaar is in de omgevings variabele PATH. 

Er zijn verschillende manieren om OpenSSL te installeren, met inbegrip van:

* **Simpele** Down load en installeer eventuele [binaire bestanden van openssl](https://wiki.openssl.org/index.php/Binaries)van derden, bijvoorbeeld van [openssl op sourceforge](https://sourceforge.net/projects/openssl/). Het volledige pad toevoegen aan openssl.exe aan de omgevingsvariabele PATH. 
   
* **Aanbevelingen** Down load de OpenSSL-bron code en bouw de binaire bestanden op uw machine door uzelf of via [vcpkg](https://github.com/Microsoft/vcpkg). De onderstaande instructies gebruiken vcpkg broncode, compileren en OpenSSL installeren op uw Windows-computer met eenvoudige stappen downloadt.

   1. Ga naar een map waar u vcpkg installeren. Verwijzen we naar deze map als  *\<VCPKGDIR >* . Volg de instructies voor het downloaden en installeren [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Zodra vcpkg is geïnstalleerd, voert u de volgende opdracht uit vanuit een Power shell-prompt om het OpenSSL-pakket voor Windows x64 te installeren. De installatie duurt meestal ongeveer 5 minuten.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Voeg `<VCPKGDIR>\installed\x64-windows\tools\openssl` aan de omgevingsvariabele PATH zodat het bestand openssl.exe beschikbaar voor de aanroep is.

### <a name="prepare-creation-scripts"></a>Voorbereiden van scripts voor het maken

De Azure IoT Edge Git-opslag plaats bevat scripts die u kunt gebruiken voor het genereren van test certificaten. In deze sectie kloont u de IoT Edge opslag plaats en voert u de scripts uit. 

1. Open een PowerShell-venster in de beheerdersmodus. 

2. Kloon de git-opslagplaats met scripts voor het genereren van certificaten voor niet-productie. Deze scripts kunnen u de benodigde certificaten voor het instellen van een transparante gateway maken. Gebruik de `git clone` opdracht of [downloaden van het ZIP-bestand](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Ga naar de map waarin u wilt werken. In dit artikel noemen we deze directory  *\<WRKDIR >* . Alle certificaten en sleutels worden gemaakt in deze werkmap.

4. Kopieer de configuratie en script bestanden van de gekloonde opslag plaats naar uw werkmap. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Als u de opslag plaats hebt gedownload als een zip-bestand, is de `iotedge-master` naam van de map en is de rest van het pad hetzelfde. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Schakel in PowerShell de scripts uit te voeren.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Breng de functies die worden gebruikt door de scripts in de globale naam ruimte van Power shell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   In het Power shell-venster wordt een waarschuwing weer gegeven dat de certificaten die zijn gegenereerd door dit script alleen voor test doeleinden zijn en niet mogen worden gebruikt in productie scenario's.

8. Controleer of OpenSSL correct is geïnstalleerd en controleer of er geen naam conflicten met bestaande certificaten zijn. Als er problemen zijn, moet het script voor het oplossen van deze op uw systeem te beschrijven.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Certificaten maken

In deze sectie maakt u drie certificaten maken en vervolgens verbinding maken in een keten. De certificaten in een keten-bestand plaatsen, kunt u eenvoudig installeren op uw IoT Edge-gateway-apparaat en eventuele downstream-apparaten.  

1. Maak het basis-CA-certificaat en laat het één tussenliggend certificaat ondertekenen. De certificaten worden allemaal in uw werkmap geplaatst.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Met deze script opdracht maakt u een aantal certificaat-en sleutel bestanden, maar we verwijzen naar een met name verderop in dit artikel:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Maak het CA-certificaat en de persoonlijke sleutel van het IoT Edge met de volgende opdracht. Geef een naam op voor het CA-certificaat, bijvoorbeeld **MyEdgeDeviceCA**. De naam wordt gebruikt voor het benoemen van de bestanden en tijdens het genereren van het certificaat. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   Met deze script opdracht maakt u een aantal certificaat-en sleutel bestanden, waaronder twee waarnaar wordt verwezen verderop in dit artikel:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Als u een andere naam opgeeft dan **MyEdgeDeviceCA**, worden deze naam door de certificaten en sleutels die zijn gemaakt met deze opdracht weer gegeven. 

Nu u de certificaten hebt, gaat u verder met het [installeren van certificaten op de gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Genereren van certificaten met Linux

Gebruik de stappen in deze sectie om test certificaten te genereren op Linux. U kunt een Linux-machine gebruiken om de certificaten te genereren en deze vervolgens kopiëren naar een IoT Edge apparaat dat wordt uitgevoerd op elk ondersteund besturings systeem. 

De certificaten die zijn gegenereerd in deze sectie zijn bedoeld alleen voor testdoeleinden. 

### <a name="prepare-creation-scripts"></a>Voorbereiden van scripts voor het maken

De Azure IoT Edge Git-opslag plaats bevat scripts die u kunt gebruiken voor het genereren van test certificaten. In deze sectie kloont u de IoT Edge opslag plaats en voert u de scripts uit. 

1. Kloon de git-opslagplaats met scripts voor het genereren van certificaten voor niet-productie. Deze scripts kunnen u de benodigde certificaten voor het instellen van een transparante gateway maken. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Ga naar de map waarin u wilt werken. We verwijzen naar deze map in het hele artikel als  *\<WRKDIR >* . Alle certificaat-en sleutel bestanden worden in deze map gemaakt.
  
3. Kopieer de configuratie-en script bestanden van de gekloonde IoT Edge opslag plaats naar uw werkmap.

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

### <a name="create-certificates"></a>Certificaten maken

In deze sectie maakt u drie certificaten maken en vervolgens verbinding maken in een keten. Het plaatsen van de certificaten in een keten-bestand kunt eenvoudig installeren op uw IoT Edge-gateway-apparaat en eventuele downstream-apparaten.  

1. Maak het basis-CA-certificaat en één tussenliggend certificaat. Deze certificaten worden geplaatst  *\<WRKDIR >* .

   Als u al hoofd-en tussen certificaten hebt gemaakt in deze werkmap, voert u dit script niet opnieuw uit. Als u dit script opnieuw uitvoert, worden de bestaande certificaten overschreven. In plaats daarvan gaat u verder met de volgende stap. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Het script maakt verschillende certificaten en sleutels. Noteer een van de twee, waarnaar wordt verwezen in de volgende sectie:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Maak het CA-certificaat en de persoonlijke sleutel van het IoT Edge met de volgende opdracht. Geef een naam op voor het CA-certificaat, bijvoorbeeld **MyEdgeDeviceCA**. De naam wordt gebruikt voor het benoemen van de bestanden en tijdens het genereren van het certificaat. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   Het script maakt verschillende certificaten en sleutels. Noteer twee, waarnaar wordt verwezen in de volgende sectie: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Als u een andere naam opgeeft dan **MyEdgeDeviceCA**, worden deze naam door de certificaten en sleutels die zijn gemaakt met deze opdracht weer gegeven. 

## <a name="install-certificates-on-the-gateway"></a>Certificaten installeren op de gateway

Nu dat u een certificaatketen genomen hebt, moet u deze op het IoT Edge-gateway-apparaat installeren en configureren van de IoT Edge-runtime om te verwijzen naar de nieuwe certificaten. 

1. Kopieer de volgende bestanden uit  *\<WRKDIR >* . Sla deze ergens op uw IoT Edge-apparaat. Verwijzen we naar de doelmap op uw IoT Edge-apparaat als  *\<CERTDIR >* . 

   * Device CA-certificaat-  `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * De persoonlijke sleutel Device CA- `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Basis-CA-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.  Als u de certificaten op het IoT Edge apparaat zelf hebt gegenereerd, kunt u deze stap overs Laan en het pad naar de werkmap gebruiken.

2. Open het bestand met IoT Edge security daemon-configuratie. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Stel de **certificaat** eigenschappen in het bestand config. yaml in op het volledige pad naar het certificaat en de sleutel bestanden op het IOT edge apparaat. Verwijder het `#` teken vóór de eigenschappen van het certificaat om de vier regels op te heffen. Houd er rekening mee dat inspringingen in YAML twee spaties bevatten.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Controleer op Linux-apparaten of de gebruiker **iotedge** Lees machtigingen heeft voor de directory die de certificaten bevat. 

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub naar de gateway implementeren

Wanneer u IoT Edge voor het eerst op een apparaat installeert, wordt slechts één systeem module automatisch gestart: de IoT Edge agent. Voor uw apparaat om te werken als een gateway, moet u beide systeemmodules. Als u nog geen modules voor uw gateway apparaat hebt geïmplementeerd, maakt u een eerste implementatie voor uw apparaat om de tweede systeem module, de IoT Edge hub, te starten. De implementatie ziet er leeg uit omdat u geen modules in de wizard toevoegt, maar dit zorgt ervoor dat beide systeem modules worden uitgevoerd. 

U kunt controleren welke modules worden uitgevoerd op een apparaat met de opdracht `iotedge list`. Als de lijst alleen de module **edgeAgent** retourneert zonder **edgeHub**, gebruikt u de volgende stappen:

1. Ga in Azure Portal naar uw IoT-hub.

2. Ga naar **IoT Edge** en selecteert u uw IoT Edge-apparaat dat u wilt gebruiken als een gateway.

3. Selecteer **Modules instellen**.

4. Selecteer **Volgende**.

5. In de **routes opgeven** pagina, moet u hebt een standaardroute dat alle berichten van alle modules naar IoT Hub verzendt. Voeg anders de volgende code toe en selecteer **Volgende**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. In de **sjabloon controleren** weergeeft, schakelt **indienen**.

## <a name="open-ports-on-gateway-device"></a>Poorten op gateway apparaat openen

Standard IoT Edge-apparaten hebben geen binnenkomende verbinding nodig om te kunnen werken, omdat alle communicatie met IoT Hub geschiedt via uitgaande verbindingen. Gateway apparaten wijken af omdat ze berichten van hun downstream-apparaten moeten ontvangen. Als er een firewall is tussen de downstream-apparaten en het gateway apparaat, moet communicatie ook mogelijk zijn via de firewall.

Een gateway scenario werkt alleen als ten minste een van de ondersteunde protocollen van de IoT Edge hub open is voor inkomend verkeer van downstream-apparaten. De ondersteunde protocollen zijn MQTT, AMQP en HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Routeren van berichten van downstream-apparaten
IoT Edge-runtime kunt versturen berichten worden verzonden van downstream apparaten net als bij berichten die worden verzonden door modules. Met deze functie kunt u analyses uitvoeren in een module die op de gateway wordt uitgevoerd voordat u gegevens naar de Cloud verzendt. 

Op dit moment is de manier waarop u berichten die worden verzonden door downstream-apparaten versturen door ze verschillen van berichten die worden verzonden door modules. Berichten van alle modules bevatten een systeemeigenschap, genaamd **connectionModuleId** maar niet voor berichten die worden verzonden door downstream-apparaten. De WHERE-component van de route kunt u alle berichten met die systeemeigenschap uitsluiten. 

De onderstaande route is een voor beeld waarmee berichten van elk downstream-apparaat naar een module met `ai_insights`de naam worden verzonden `ai_insights` en vervolgens worden IOT hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Zie voor meer informatie over het routeren van berichten, [implementeren modules en routes tot stand brengen](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Uitgebreide offline bewerking inschakelen

Vanaf de release van de [v-1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) van de IOT Edge runtime kunnen het gateway apparaat en de downstream-apparaten waarmee verbinding wordt gemaakt, worden geconfigureerd voor een uitgebreide offline bewerking. 

Met deze mogelijkheid kunnen lokale modules of downstream-apparaten opnieuw worden geverifieerd met het IoT Edge apparaat en met elkaar communiceren met behulp van berichten en methoden, zelfs wanneer de verbinding met de IoT-hub is verbroken. Zie voor meer informatie, [begrijpen uitgebreid offline-mogelijkheden voor IoT Edge-apparaten, modules en onderliggende apparaten](offline-capabilities.md).

Als u uitgebreide offline mogelijkheden wilt inschakelen, stelt u een relatie tussen een bovenliggend/onderliggend item in tussen een IoT Edge gateway apparaat en downstream-apparaten waarmee er verbinding mee wordt gemaakt. Deze stappen worden uitgebreid beschreven in [een downstream-apparaat verifiëren bij Azure IOT hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge-apparaat werkt als een transparante gateway hebt, moet u uw downstream-apparaten voor het vertrouwen van de gateway en berichten te verzenden. Ga door met het [verifiëren van een downstream-apparaat bij Azure IOT hub](how-to-authenticate-downstream-device.md) voor de volgende stappen in het instellen van uw transparante Gateway scenario. 
