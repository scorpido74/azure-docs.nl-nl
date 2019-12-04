---
title: Transparant gateway apparaat maken-Azure IoT Edge | Microsoft Docs
description: Een Azure IoT Edge apparaat gebruiken als een transparante gateway die informatie kan verwerken vanaf downstream-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c005dcd91412552e2b10c27a7809ca4bc46d4709
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792333"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Een IoT Edge apparaat configureren om te fungeren als transparante gateway

In dit artikel vindt u gedetailleerde instructies voor het configureren van een IoT Edge apparaat om te werken als een transparante gateway zodat andere apparaten kunnen communiceren met IoT Hub. In dit artikel verwijst de term *IOT Edge gateway* naar een IOT edge apparaat dat wordt gebruikt als transparante gateway. Zie [How a IOT edge-apparaten kunnen worden gebruikt als een gateway](./iot-edge-as-gateway.md)voor meer informatie.

>[!NOTE]
>Dat
> * Apparaten met rand mogelijkheden kunnen geen verbinding maken met IoT Edge gateways. 
> * Downstream-apparaten kunnen het uploaden van bestanden niet gebruiken.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de eerste stap behandeld:

1. **Het gateway apparaat moet in staat zijn om veilig verbinding te maken met downstream-apparaten, communicaties te ontvangen van downstream-apparaten en berichten te routeren naar de juiste bestemming.**
2. Het downstream-apparaat moet een apparaat-id hebben om te kunnen worden geverifieerd met IoT Hub en u moet communiceren via het gateway apparaat. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.
3. Het downstream-apparaat moet veilig verbinding kunnen maken met het gateway apparaat. Zie [verbinding maken tussen een downstream-apparaat en een Azure IOT Edge-gateway](how-to-connect-downstream-device.md)voor meer informatie.


Een apparaat kan alleen als gateway functioneren als het op een veilige manier verbinding kan maken met de downstream-apparaten. Met Azure IoT Edge kunt u een open bare-sleutel infrastructuur (PKI) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval kunnen we een downstream-apparaat verbinding laten maken met een IoT Edge apparaat dat als transparante gateway fungeert. Om redelijke beveiliging te behouden, moet het downstream-apparaat de identiteit van het gateway-apparaat bevestigen. Met deze identiteits controle voor komt u dat uw apparaten verbinding maken met mogelijk schadelijke gateways.

Een downstream-apparaat in een transparant Gateway scenario kan elke toepassing of elk platform zijn met een identiteit die is gemaakt met de [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub) -Cloud service. In veel gevallen gebruiken deze toepassingen de [Azure IOT Device SDK](../iot-hub/iot-hub-devguide-sdks.md). Voor alle praktische doel einden kan een downstream-apparaat zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge gateway apparaat zelf. Een IoT Edge apparaat kan echter niet worden downstream van een IoT Edge gateway. 

U kunt een certificaat infrastructuur maken waarmee de vertrouwens relatie die is vereist voor de gateway topologie van uw apparaat wordt ingeschakeld. In dit artikel wordt ervan uitgegaan dat u dezelfde certificaat instelling gebruikt voor het inschakelen van [x. 509 ca-beveiliging](../iot-hub/iot-hub-x509ca-overview.md) in IOT hub, waarbij een x. 509 CA-certificaat is gekoppeld aan een specifieke IOT-hub (de basis-CA van de IOT-hub), een reeks certificaten die zijn ondertekend met deze certificerings instantie en een certificerings instantie voor het IOT edge apparaat.

![Installatie van Gateway certificaat](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>De term ' basis-CA ' die in dit artikel wordt gebruikt, verwijst naar het open bare certificaat van de bovenste certificerings instantie van de PKI-certificaat keten, en niet noodzakelijkerwijs de hoofdmap van het certificaat van een extern gepubliceerde certificerings instantie. In veel gevallen is het eigenlijk een openbaar certificaat van een tussenliggend CA. 

De gateway geeft het CA-certificaat van IoT Edge apparaat aan het downstream-apparaat tijdens de start van de verbinding. Het downstream-apparaat controleert of het CA-certificaat van IoT Edge apparaat is ondertekend door het basis-CA-certificaat. Met dit proces kan het downstream-apparaat controleren of de gateway afkomstig is van een vertrouwde bron.

De volgende stappen leiden u door het proces van het maken van de certificaten en het installeren ervan op de juiste plaatsen op de gateway. U kunt elke computer gebruiken om de certificaten te genereren en deze vervolgens naar uw IoT Edge apparaat kopiëren. 

## <a name="prerequisites"></a>Vereisten

* Een ontwikkelings machine voor het maken van certificaten. 
* Een Azure IoT Edge apparaat dat als gateway moet worden geconfigureerd. Gebruik de IoT Edge installatie stappen voor een van de volgende besturings systemen:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Certificaten genereren met Windows

Gebruik de stappen in deze sectie om test certificaten te genereren in Windows. U kunt een Windows-machine gebruiken om de certificaten te genereren en deze vervolgens kopiëren naar een IoT Edge apparaat dat wordt uitgevoerd op elk ondersteund besturings systeem. 

De in deze sectie gegenereerde certificaten zijn alleen bedoeld voor test doeleinden. 

### <a name="install-openssl"></a>OpenSSL installeren

Installeer OpenSSL voor Windows op de computer die u gebruikt om de certificaten te genereren. Als OpenSSL al op uw Windows-apparaat is geïnstalleerd, kunt u deze stap overs Laan, maar u moet ervoor zorgen dat openssl. exe beschikbaar is in de omgevings variabele PATH. 

Er zijn verschillende manieren om OpenSSL te installeren, met inbegrip van:

* **Eenvoudiger:** Down load en installeer eventuele [binaire bestanden van openssl](https://wiki.openssl.org/index.php/Binaries)van derden, bijvoorbeeld van [openssl op sourceforge](https://sourceforge.net/projects/openssl/). Voeg het volledige pad naar openssl. exe toe aan de omgevings variabele PATH. 
   
* **Aanbevolen:** Down load de OpenSSL-bron code en bouw de binaire bestanden op uw machine door uzelf of via [vcpkg](https://github.com/Microsoft/vcpkg). De onderstaande instructies gebruiken vcpkg voor het downloaden van de bron code, het compileren en installeren van OpenSSL op uw Windows-computer, met eenvoudige stappen.

   1. Ga naar een map waarin u vcpkg wilt installeren. We verwijzen naar deze map als *\<VCPKGDIR >* . Volg de instructies om [vcpkg](https://github.com/Microsoft/vcpkg)te downloaden en te installeren.
   
   2. Zodra vcpkg is geïnstalleerd, voert u de volgende opdracht uit vanuit een Power shell-prompt om het OpenSSL-pakket voor Windows x64 te installeren. De installatie duurt doorgaans ongeveer 5 minuten om te volt ooien.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Voeg `<VCPKGDIR>\installed\x64-windows\tools\openssl` toe aan de omgevings variabele PATH om ervoor te zorgen dat het bestand openssl. exe beschikbaar is voor aanroep.

### <a name="prepare-creation-scripts"></a>Maken van scripts voorbereiden

De Azure IoT Edge Git-opslag plaats bevat scripts die u kunt gebruiken voor het genereren van test certificaten. In deze sectie kloont u de IoT Edge opslag plaats en voert u de scripts uit. 

1. Open een Power shell-venster in de beheerders modus. 

2. Kloon de Git-opslag plaats die scripts bevat voor het genereren van niet-productie certificaten. Deze scripts helpen u bij het maken van de vereiste certificaten voor het instellen van een transparante gateway. Gebruik de opdracht `git clone` of [down load de zip](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Ga naar de map waarin u wilt werken. In dit artikel noemen we deze directory *\<WRKDIR >* . Alle certificaten en sleutels worden gemaakt in deze werkmap.

4. Kopieer de configuratie en script bestanden van de gekloonde opslag plaats naar uw werkmap. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Als u de opslag plaats hebt gedownload als een ZIP-bestand, is de mapnaam `iotedge-master` en is de rest van het pad hetzelfde. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Schakel Power shell in om de scripts uit te voeren.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Breng de functies die worden gebruikt door de scripts in de globale naam ruimte van Power shell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   In het Power shell-venster wordt een waarschuwing weer gegeven dat de certificaten die zijn gegenereerd door dit script alleen voor test doeleinden zijn en niet mogen worden gebruikt in productie scenario's.

8. Controleer of OpenSSL correct is geïnstalleerd en controleer of er geen naam conflicten met bestaande certificaten zijn. Als er problemen zijn, moet u in het script beschrijven hoe u deze op uw systeem kunt oplossen.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Certificaten maken

In deze sectie maakt u drie certificaten en verbindt u deze vervolgens in een keten. Als u de certificaten in een keten bestand plaatst, kunt u ze eenvoudig op uw IoT Edge gateway apparaat en op downstream-apparaten installeren.  

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

## <a name="generate-certificates-with-linux"></a>Certificaten genereren met Linux

Gebruik de stappen in deze sectie om test certificaten te genereren op Linux. U kunt een Linux-machine gebruiken om de certificaten te genereren en deze vervolgens kopiëren naar een IoT Edge apparaat dat wordt uitgevoerd op elk ondersteund besturings systeem. 

De in deze sectie gegenereerde certificaten zijn alleen bedoeld voor test doeleinden. 

### <a name="prepare-creation-scripts"></a>Maken van scripts voorbereiden

De Azure IoT Edge Git-opslag plaats bevat scripts die u kunt gebruiken voor het genereren van test certificaten. In deze sectie kloont u de IoT Edge opslag plaats en voert u de scripts uit. 

1. Kloon de Git-opslag plaats die scripts bevat voor het genereren van niet-productie certificaten. Deze scripts helpen u bij het maken van de vereiste certificaten voor het instellen van een transparante gateway. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Ga naar de map waarin u wilt werken. We verwijzen naar deze map in het hele artikel als *\<WRKDIR >* . Alle certificaat-en sleutel bestanden worden in deze map gemaakt.
  
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

In deze sectie maakt u drie certificaten en verbindt u deze vervolgens in een keten. Als u de certificaten in een keten bestand plaatst, kunt u deze eenvoudig op uw IoT Edge gateway apparaat en op downstream-apparaten installeren.  

1. Maak het basis-CA-certificaat en één tussenliggend certificaat. Deze certificaten worden geplaatst in *\<WRKDIR >* .

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

Nu u een certificaat keten hebt gemaakt, moet u deze installeren op het IoT Edge gateway-apparaat en de IoT Edge-runtime configureren om te verwijzen naar de nieuwe certificaten. 

1. Kopieer de volgende bestanden van *\<WRKDIR >* . Sla ze op een wille keurige plaats op uw IoT Edge apparaat op. We verwijzen naar de doelmap op uw IoT Edge-apparaat als *\<CERTDIR >* . 

   * CA-certificaat van apparaat-`<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Persoonlijke sleutel van de apparaat-CA-`<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Basis-CA-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.  Als u de certificaten op het IoT Edge apparaat zelf hebt gegenereerd, kunt u deze stap overs Laan en het pad naar de werkmap gebruiken.

2. Open het IoT Edge Security daemon config-bestand. 

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

## <a name="deploy-edgehub-to-the-gateway"></a>EdgeHub implementeren op de gateway

Wanneer u IoT Edge voor het eerst op een apparaat installeert, wordt slechts één systeem module automatisch gestart: de IoT Edge agent. Als u uw apparaat als een gateway wilt gebruiken, hebt u beide systeem modules nodig. Als u nog geen modules voor uw gateway apparaat hebt geïmplementeerd, maakt u een eerste implementatie voor uw apparaat om de tweede systeem module, de IoT Edge hub, te starten. De implementatie ziet er leeg uit omdat u geen modules in de wizard toevoegt, maar dit zorgt ervoor dat beide systeem modules worden uitgevoerd. 

U kunt controleren welke modules worden uitgevoerd op een apparaat met behulp van de opdracht `iotedge list`. Als de lijst alleen de module **edgeAgent** retourneert zonder **edgeHub**, gebruikt u de volgende stappen:

1. Ga in Azure Portal naar uw IoT-hub.

2. Ga naar **IOT Edge** en selecteer het IOT edge apparaat dat u als gateway wilt gebruiken.

3. Selecteer **Modules instellen**.

4. Selecteer **Next**.

5. Op de pagina **routes opgeven** moet u een standaard route hebben waarmee alle berichten van alle modules naar IOT hub worden verzonden. Voeg anders de volgende code toe en selecteer **Volgende**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Selecteer op de pagina **controle sjabloon** de optie **verzenden**.

## <a name="open-ports-on-gateway-device"></a>Poorten op gateway apparaat openen

Standard IoT Edge-apparaten hebben geen binnenkomende verbinding nodig om te kunnen werken, omdat alle communicatie met IoT Hub geschiedt via uitgaande verbindingen. Gateway apparaten wijken af omdat ze berichten van hun downstream-apparaten moeten ontvangen. Als er een firewall is tussen de downstream-apparaten en het gateway apparaat, moet communicatie ook mogelijk zijn via de firewall.

Een gateway scenario werkt alleen als ten minste een van de ondersteunde protocollen van de IoT Edge hub open is voor inkomend verkeer van downstream-apparaten. De ondersteunde protocollen zijn MQTT, AMQP, HTTPS, MQTT over websockets en AMQP over websockets. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Berichten van downstream-apparaten routeren
De IoT Edge runtime kan berichten verzenden die zijn verzonden vanaf downstream-apparaten, net als berichten die door modules worden verzonden. Met deze functie kunt u analyses uitvoeren in een module die op de gateway wordt uitgevoerd voordat u gegevens naar de Cloud verzendt. 

Op dit moment kunt u berichten die door downstream-apparaten worden verzonden, op een andere manier onderscheiden van berichten die door modules worden verzonden. Berichten die door modules worden verzonden, bevatten een systeem eigenschap met de naam **connectionModuleId** , maar berichten die door downstream-apparaten worden verzonden, worden niet uitgevoerd. U kunt de component WHERE van de route gebruiken om berichten met die systeem eigenschap uit te sluiten. 

De onderstaande route is een voor beeld waarmee berichten van een wille keurig downstream-apparaat naar een module met de naam `ai_insights`worden verzonden en vervolgens van `ai_insights` naar IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Zie [modules implementeren en routes instellen](./module-composition.md#declare-routes)voor meer informatie over het routeren van berichten.


## <a name="enable-extended-offline-operation"></a>Uitgebreide offline bewerking inschakelen

Vanaf de release van de [v-1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) van de IOT Edge runtime kunnen het gateway apparaat en de downstream-apparaten waarmee verbinding wordt gemaakt, worden geconfigureerd voor een uitgebreide offline bewerking. 

Met deze mogelijkheid kunnen lokale modules of downstream-apparaten opnieuw worden geverifieerd met het IoT Edge apparaat en met elkaar communiceren met behulp van berichten en methoden, zelfs wanneer de verbinding met de IoT-hub is verbroken. Zie voor meer informatie [uitgebreide offline mogelijkheden voor IOT edge apparaten, modules en onderliggende apparaten begrijpen](offline-capabilities.md).

Als u uitgebreide offline mogelijkheden wilt inschakelen, stelt u een relatie tussen een bovenliggend/onderliggend item in tussen een IoT Edge gateway apparaat en downstream-apparaten waarmee er verbinding mee wordt gemaakt. Deze stappen worden uitgebreid beschreven in [een downstream-apparaat verifiëren bij Azure IOT hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge apparaat werkt als een transparante gateway, moet u uw downstream-apparaten configureren om de gateway te vertrouwen en er berichten naar toe te sturen. Ga door met het [verifiëren van een downstream-apparaat bij Azure IOT hub](how-to-authenticate-downstream-device.md) voor de volgende stappen in het instellen van uw transparante Gateway scenario. 
