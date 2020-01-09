---
title: Apparaten configureren voor netwerk-proxy's - Azure IoT Edge | Microsoft Docs
description: Klik hier voor meer informatie over het configureren van de Azure IoT Edge-runtime en een internetgerichte IoT Edge-modules om te communiceren via een proxyserver.
author: kgremban
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6cc37875b84e215066c52ca8daef0fa0d879c54f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434477"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Een IoT Edge-apparaat om te communiceren via een proxyserver configureren

IoT Edge-apparaten verzenden HTTPS-aanvragen om te communiceren met IoT Hub. Als uw apparaat is verbonden met een netwerk dat gebruikmaakt van een proxyserver, moet u de IoT Edge-runtime om te communiceren via de-server configureren. Proxy servers kunnen ook van invloed zijn op afzonderlijke IoT Edge modules als ze HTTP-of HTTPS-aanvragen maken die niet via de IoT Edge hub worden gerouteerd.

In dit artikel worden de volgende vier stappen door lopen om een IoT Edge apparaat te configureren en vervolgens te beheren achter een proxy server:

1. **Installeer de IoT Edge runtime op het apparaat.**

   Met de IoT Edge-installatie scripts worden pakketten en bestanden van het internet opgehaald, zodat uw apparaat moet communiceren via de proxy server om die aanvragen te kunnen uitvoeren. Zie de sectie [runtime installeren via een proxy](#install-the-runtime-through-a-proxy) in dit artikel voor gedetailleerde stappen. Voor Windows-apparaten biedt het installatie script ook een [offline-installatie](how-to-install-iot-edge-windows.md#offline-installation) optie.

   Deze stap is een eenmalig proces dat wordt uitgevoerd op het IoT Edge apparaat wanneer u het voor het eerst instelt. Dezelfde verbindingen zijn ook vereist wanneer u de IoT Edge runtime bijwerkt.

2. **Configureer de docker-daemon en de IoT Edge daemon op het apparaat.**

   IoT Edge gebruikt twee daemons op het apparaat, beide die webaanvragen via de proxy server moeten maken. De IoT Edge-daemon is verantwoordelijk voor communicatie met IoT Hub. De Moby-daemon is verantwoordelijk voor container beheer, dus communiceert met container registers. Zie de sectie [de daemons configureren](#configure-the-daemons) in dit artikel voor gedetailleerde stappen.

   Deze stap is een eenmalig proces dat wordt uitgevoerd op het IoT Edge apparaat wanneer u het voor het eerst instelt.

3. **Configureer de eigenschappen van de IoT Edge-agent in het bestand config. yaml op het apparaat.**

   De IoT Edge-daemon start de module edgeAgent in eerste instantie, maar vervolgens is de edgeAgent-module verantwoordelijk voor het ophalen van het implementatie manifest van IoT Hub en het starten van alle andere modules. Configureer de edgeAgent-module omgevings variabelen hand matig op het apparaat zelf om de IoT Edge-agent in te stellen voor de eerste verbinding met IoT Hub. Na de eerste verbinding kunt u de edgeAgent-module op afstand configureren. Zie de sectie [de IOT Edge agent configureren](#configure-the-iot-edge-agent) in dit artikel voor gedetailleerde stappen.

   Deze stap is een eenmalig proces dat wordt uitgevoerd op het IoT Edge apparaat wanneer u het voor het eerst instelt.

4. **Voor alle toekomstige module-implementaties stelt u omgevings variabelen in voor elke module die communiceert via de proxy.**

   Nadat uw IoT Edge apparaat is ingesteld en via de proxy server is verbonden met IoT Hub, moet u de verbinding in alle toekomstige module-implementaties onderhouden. Zie de sectie [implementatie manifesten configureren](#configure-deployment-manifests) in dit artikel voor gedetailleerde stappen. 

   Deze stap is een doorlopend proces dat op afstand wordt uitgevoerd, zodat elke nieuwe module of implementatie-update de mogelijkheid biedt om te communiceren via de proxy server.

## <a name="know-your-proxy-url"></a>De URL van uw proxy weet

Voordat u aan de slag gaat met een van de stappen in dit artikel, moet u weten wat uw proxy-URL is.

Proxy-URL's worden de volgende indeling: **protocol**://**proxy_host**:**proxy_poort**.

* De **protocol** via HTTP of HTTPS is. De docker-daemon kan gebruikmaken van een van beide protocollen, afhankelijk van de Register instellingen van uw container, maar de IoT Edge-daemon-en runtime-containers moeten altijd HTTP gebruiken om verbinding te maken met de proxy.

* De **proxy_host** is een adres voor de proxyserver. Als voor uw proxy server verificatie is vereist, kunt u uw referenties opgeven als onderdeel van de proxy-host met de volgende indeling: **gebruiker**:**wacht woord**\@**proxy_host**.

* De **proxy_poort** is de netwerkpoort waarop de proxy op netwerkverkeer reageert.

## <a name="install-the-runtime-through-a-proxy"></a>De runtime installeren via een proxy

Of uw IoT Edge-apparaat wordt uitgevoerd op Windows of Linux, u hebt toegang tot de installatie pakketten via de proxy server. Afhankelijk van uw besturings systeem voert u de stappen uit om de IoT Edge-runtime te installeren via een proxy server.

### <a name="linux-devices"></a>Linux-apparaten

Als u de IoT Edge-runtime op een Linux-apparaat installeert, configureert u de package manager om te gaan via de proxyserver voor toegang tot het installatiepakket. Bijvoorbeeld, [apt-get ingesteld voor het gebruik van een http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Als uw pakket beheer is geconfigureerd, volgt u de instructies in [Install Azure IOT Edge runtime op Linux](how-to-install-iot-edge-linux.md) zoals gebruikelijk.

### <a name="windows-devices"></a>Windows-apparaten

Als u de IoT Edge runtime op een Windows-apparaat installeert, moet u de proxy server twee keer door lopen. De eerste verbinding downloadt het installatie script bestand en de tweede verbinding tijdens de installatie om de benodigde onderdelen te downloaden. U kunt proxy gegevens in Windows-instellingen configureren of uw proxy gegevens rechtstreeks in de Power shell-opdrachten toevoegen.

In de volgende stappen ziet u een voor beeld van een Windows-installatie met behulp van het argument `-proxy`:

1. De opdracht invoke-WebRequest heeft proxy gegevens nodig om toegang te krijgen tot het installatie script. Vervolgens moet de opdracht Deploy-IoTEdge de proxy gegevens hebben om de installatie bestanden te downloaden. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. De opdracht initialiseren-IoTEdge hoeft de proxy server niet te passeren, dus de tweede stap vereist alleen proxy gegevens voor invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Als u gecompliceerde referenties hebt voor de proxy server die niet kan worden opgenomen in de URL, gebruikt u de para meter `-ProxyCredential` in `-InvokeWebRequestParameters`. Bijvoorbeeld:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Zie [invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)voor meer informatie over proxy parameters. Zie voor meer informatie over Windows-installatie opties, waaronder offline-installatie [Azure IOT Edge runtime installeren in Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>De daemons configureren

IoT Edge is afhankelijk van twee daemons die op het IoT Edge apparaat worden uitgevoerd. De Moby-daemon maakt webaanvragen voor het ophalen van container installatie kopieën vanuit container registers. De daemon IoT Edge maakt webaanvragen om te communiceren met IoT Hub.

Zowel de Moby-als de IoT Edge-daemons moeten worden geconfigureerd voor het gebruik van de proxy server voor doorlopende functionaliteit van het apparaat. Deze stap vindt plaats op het IoT Edge-apparaat tijdens de eerste installatie van het apparaat.

### <a name="moby-daemon"></a>Moby-daemon

Omdat Moby is gebouwd op docker, raadpleegt u de docker-documentatie voor het configureren van de Moby-daemon met omgevings variabelen. De meeste containerregisters (inclusief DockerHub en Azure Container Registry) ondersteuning voor HTTPS-aanvragen, zodat de parameter die u moet instellen is **HTTPS_PROXY**. Als u bent installatiekopieën binnenhaalt vanuit een register dat biedt geen ondersteuning voor transport layer security (TLS), dan moet u instellen de **gebruikt** parameter.

Kies het artikel dat van toepassing is op uw IoT Edge besturings systeem van het apparaat:

* [Docker-daemon configureren op Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) De Moby-daemon op Linux-apparaten behoudt de naam docker.
* [Docker-daemon configureren in Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) De Moby-daemon op Windows-apparaten heet iotedge-Moby. De namen verschillen, omdat het mogelijk is om zowel docker Desktop als Moby parallel op een Windows-apparaat uit te voeren.

### <a name="iot-edge-daemon"></a>IoT Edge-daemon

De IoT Edge-daemon is op een vergelijk bare manier geconfigureerd als de Moby-daemon. Gebruik de volgende stappen uit om in te stellen van een omgevingsvariabele voor de service, op basis van uw besturingssysteem.

De IoT Edge-daemon maakt altijd gebruik van HTTPS voor het verzenden van aanvragen naar IoT Hub.

#### <a name="linux"></a>Linux

Een editor openen in de terminal om te configureren van de IoT Edge-daemon.

```bash
sudo systemctl edit iotedge
```

Voer de volgende tekst vervangen  **\<proxy-URL >** uw proxyserveradres en de poort. Vervolgens opslaan en sluiten.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Vernieuw de Service Manager om de nieuwe configuratie voor IoT Edge op te halen.

```bash
sudo systemctl daemon-reload
```

Start opnieuw op IoT Edge om de wijzigingen te laten treden.

```bash
sudo systemctl restart iotedge
```

Controleer of de omgevingsvariabele is gemaakt, en de nieuwe configuratie is geladen.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Open een PowerShell-venster als beheerder en voer de volgende opdracht naar het register bewerken met de nieuwe omgevingsvariabele. Vervang  **\<proxy-url >** uw proxyserveradres en de poort.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Start opnieuw op IoT Edge om de wijzigingen te laten treden.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>De IoT Edge-agent configureren

De IoT Edge-agent is de eerste module die op een IoT Edge apparaat kan worden gestart. Deze gestart voor het eerst op basis van de informatie in de IoT Edge config.yaml-bestand. De IoT Edge agent maakt vervolgens verbinding met IoT Hub om implementatie manifesten op te halen die declareren welke andere modules op het apparaat moeten worden geïmplementeerd.

Deze stap vindt eenmaal plaats op het IoT Edge apparaat tijdens de eerste installatie van het apparaat. 

1. Open het bestand config.yaml op uw IoT Edge-apparaat. Op Linux-systemen, dit bestand bevindt zich in **/etc/iotedge/config.yaml**. Op Windows-systemen, dit bestand bevindt zich in **C:\ProgramData\iotedge\config.yaml**. Het configuratiebestand is beveiligd, moet u de Administrator-bevoegdheden om deze te openen. Op Linux-systemen gebruikt u de opdracht `sudo` voordat u het bestand opent in de gewenste tekst editor. Open in Windows een tekst editor zoals Klad blok als beheerder en open vervolgens het bestand. 

2. Zoek in het bestand config.yaml de **Edge Agent-module spec** sectie. De definitie van de IoT Edge-agent bevat een **env** -para meter waar u omgevings variabelen kunt toevoegen. 

3. Verwijder de accolades die tijdelijke aanduidingen voor de parameter env zijn, en voeg de nieuwe variabele op een nieuwe regel toe. Houd er rekening mee dat streepjes in YAML twee spaties zijn. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge-runtime gebruikt AMQP standaard om te communiceren met IoT Hub. Sommige proxyservers blokkeren AMQP poorten. Als dat het geval is, klikt u vervolgens moet u ook het configureren van edgeAgent voor het gebruik van AMQP via WebSocket. Een tweede omgevingsvariabele toevoegen.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent definitie met omgevingsvariabelen](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Sla de wijzigingen in config.yaml en sluit de editor. Start opnieuw op IoT Edge om de wijzigingen te laten treden. 

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Manifesten implementatie configureren  

Zodra uw IoT Edge apparaat is geconfigureerd voor gebruik met uw proxy server, moet u de omgevings variabelen in toekomstige implementatie manifesten blijven declareren. U kunt implementatie manifesten bewerken met behulp van de wizard Azure Portal of door een JSON-bestand van het implementatie manifest te bewerken.

De twee runtime modules, edgeAgent en edgeHub, altijd configureren om te communiceren via de proxy server zodat ze een verbinding met IoT Hub kunnen onderhouden. Als u de proxy gegevens uit de edgeAgent-module verwijdert, is de enige manier om de verbinding te herstellen door het bestand config. yaml op het apparaat te bewerken, zoals beschreven in de vorige sectie.

Andere IoT Edge-modules die verbinding maken met Internet moeten worden geconfigureerd om te communiceren via de proxy server. Modules die hun berichten via edgeHub routeren of die alleen communiceren met andere modules op het apparaat, hebben echter geen informatie over de proxy server nodig.

Deze stap is doorlopend gedurende de levens duur van het IoT Edge apparaat.

### <a name="azure-portal"></a>Azure Portal

Wanneer u gebruikt de **modules instellen** wizard om implementaties te maken voor IoT Edge-apparaten, elke module heeft een **omgevingsvariabelen** sectie die u gebruiken kunt om proxy serververbindingen te configureren.

Als u de IoT Edge agent en IoT Edge hub-modules wilt configureren, selecteert u **runtime-instellingen** in de eerste stap van de wizard.

![Geavanceerde runtime-instellingen voor Edge configureren](./media/how-to-configure-proxy-support/configure-runtime.png)

Voeg de **https_proxy** -omgevings variabele toe aan de definities van de IOT Edge agent en IOT Edge hub-module. Als u de omgevings variabele **UpstreamProtocol** in het bestand config. yaml op uw IOT edge-apparaat hebt opgenomen, moet u die ook toevoegen aan de module definitie van de IOT Edge agent.

![Set https_proxy-omgevingsvariabele](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Alle andere modules die u aan een manifest van de implementatie toevoegt volgen hetzelfde patroon. Op de pagina waar u de modulenaam van de en de installatiekopie instellen, moet u er een sectie van de variabelen voor de omgeving is.

### <a name="json-deployment-manifest-files"></a>JSON-implementatie-manifestbestanden

Als u implementaties voor IoT Edge-apparaten die gebruikmaken van de sjablonen in Visual Studio Code of door het handmatig maken van JSON-bestanden maakt, kunt u de omgevingsvariabelen rechtstreeks naar de definitie van elke module kunt toevoegen.

Gebruik de volgende JSON-indeling:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Met de omgevingsvariabelen die is opgenomen, moet de definitie van de module edgeHub volgt uitzien:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Als u de omgevings variabele **UpstreamProtocol** in het bestand config. yaml op uw IOT edge-apparaat hebt opgenomen, moet u die ook toevoegen aan de module definitie van de IOT Edge agent.

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de functies van de [IoT Edge-runtime](iot-edge-runtime.md).

Installatie en configuratie van fouten met [veelvoorkomende problemen en oplossingen voor Azure IoT Edge](troubleshoot.md)
