---
title: Apparaten configureren voor netwerkproxy's - Azure IoT Edge | Microsoft Documenten
description: De Azure IoT Edge-runtime en alle iot edge-modules op internet configureren om via een proxyserver te communiceren.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 270e6a0173ed0088ff5d37c989947f5272634200
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687188"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Een IoT Edge-apparaat configureren om te communiceren via een proxyserver

IoT Edge-apparaten verzenden HTTPS-verzoeken om te communiceren met IoT Hub. Als uw apparaat is verbonden met een netwerk dat een proxyserver gebruikt, moet u de IoT Edge-runtime configureren om via de server te communiceren. Proxyservers kunnen ook van invloed zijn op afzonderlijke IoT Edge-modules als ze HTTP- of HTTPS-verzoeken indienen die niet via de IoT Edge-hub worden doorgestuurd.

In dit artikel worden de volgende vier stappen doorlopen om een IoT Edge-apparaat achter een proxyserver te configureren en vervolgens te beheren:

1. **Installeer de Runtime van IoT Edge op uw apparaat.**

   De IoT Edge-installatiescripts halen pakketten en bestanden van het internet, zodat uw apparaat via de proxyserver moet communiceren om die verzoeken te kunnen uitvoeren. Zie De [runtime installeren via een proxygedeelte](#install-the-runtime-through-a-proxy) van dit artikel voor gedetailleerde stappen. Voor Windows-apparaten biedt het installatiescript ook een [offline installatieoptie.](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

   Deze stap is een eenmalig proces dat wordt uitgevoerd op het IoT Edge-apparaat wanneer u het voor het eerst instelt. Dezelfde verbindingen zijn ook vereist wanneer u de Runtime van IoT Edge bijwerkt.

2. **Configureer de Docker daemon en de IoT Edge daemon op uw apparaat.**

   IoT Edge maakt gebruik van twee daemons op het apparaat, die beide webverzoeken moeten indienen via de proxyserver. De IoT Edge daemon is verantwoordelijk voor de communicatie met IoT Hub. De Moby daemon is verantwoordelijk voor het containerbeheer, dus communiceert met containerregisters. Zie het gedeelte [Daemons van](#configure-the-daemons) dit artikel configureren voor gedetailleerde stappen.

   Deze stap is een eenmalig proces dat wordt uitgevoerd op het IoT Edge-apparaat wanneer u het voor het eerst instelt.

3. **Configureer de eigenschappen van de IoT Edge-agent in het config.yaml-bestand op uw apparaat.**

   De IoT Edge daemon start de edgeAgent-module in eerste instantie, maar vervolgens is de edgeAgent-module verantwoordelijk voor het ophalen van het implementatiemanifest van IoT Hub en het starten van alle andere modules. Als u de IoT Edge-agent de eerste verbinding met IoT Hub wilt maken, configureert u de omgevingsvariabelen van de EdgeAgent-module handmatig op het apparaat zelf. Na de eerste verbinding u de edgeAgent-module op afstand configureren. Zie De sectie [IoT Edge-agent configureren](#configure-the-iot-edge-agent) van dit artikel voor gedetailleerde stappen.

   Deze stap is een eenmalig proces dat wordt uitgevoerd op het IoT Edge-apparaat wanneer u het voor het eerst instelt.

4. **Stel omgevingsvariabelen in voor alle toekomstige moduleimplementaties voor elke module die via de proxy communiceert.**

   Zodra uw IoT Edge-apparaat is ingesteld en verbonden met IoT Hub via de proxyserver, moet u de verbinding in alle toekomstige moduleimplementaties onderhouden. Zie het gedeelte [Implementatiemanifesten configureren](#configure-deployment-manifests) van dit artikel voor gedetailleerde stappen.

   Deze stap is een doorlopend proces dat op afstand wordt uitgevoerd, zodat elke nieuwe module of implementatie-update de mogelijkheid van het apparaat behoudt om te communiceren via de proxyserver.

## <a name="know-your-proxy-url"></a>Ken uw proxy-URL

Voordat u begint met een van de stappen in dit artikel, moet u uw proxy-URL kennen.

Proxy-URL's hebben de volgende indeling: **protocol**://**proxy_host**:**proxy_port**.

* Het **protocol** is HTTP of HTTPS. De Docker daemon kan beide protocollen gebruiken, afhankelijk van uw instellingen voor containerregister, maar de IoT Edge-daemon- en runtime-containers moeten altijd HTTP gebruiken om verbinding te maken met de proxy.

* De **proxy_host** is een adres voor de proxyserver. Als uw proxyserver verificatie vereist, u uw referenties als onderdeel van de proxyhost de volgende indeling geven: **gebruiker:****wachtwoord**\@**proxy_host**.

* De **proxy_port** is de netwerkpoort waarop de proxy reageert op netwerkverkeer.

## <a name="install-the-runtime-through-a-proxy"></a>De runtime installeren via een proxy

Of uw IoT Edge-apparaat nu op Windows of Linux draait, u moet toegang krijgen tot de installatiepakketten via de proxyserver. Volg afhankelijk van uw besturingssysteem de stappen om de IoT Edge-runtime via een proxyserver te installeren.

### <a name="linux-devices"></a>Linux-apparaten

Als u de Runtime van IoT Edge installeert op een Linux-apparaat, configureert u de package manager om via uw proxyserver toegang te krijgen tot het installatiepakket. Stel bijvoorbeeld [apt-get in om een http-proxy te gebruiken.](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy) Zodra uw package manager is geconfigureerd, volgt u de instructies in [Azure IoT Edge-runtime installeren op Linux](how-to-install-iot-edge-linux.md) zoals gewoonlijk.

### <a name="windows-devices"></a>Windows-apparaten

Als u de IoT Edge-runtime op een Windows-apparaat installeert, moet u twee keer via de proxyserver gaan. De eerste verbinding downloadt het installatiescriptbestand en de tweede verbinding is tijdens de installatie om de benodigde onderdelen te downloaden. U proxy-informatie configureren in Windows-instellingen of uw proxy-informatie rechtstreeks opnemen in de PowerShell-opdrachten.

In de volgende stappen wordt een `-proxy` voorbeeld van een windows-installatie getoond met het argument:

1. De opdracht Aanroepen-WebRequest heeft proxy-informatie nodig om toegang te krijgen tot het installatiescript. Vervolgens heeft de opdracht Deploy-IoTEdge de proxy-informatie nodig om de installatiebestanden te downloaden.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. De opdracht Initialize-IoTEdge hoeft niet via de proxyserver te gaan, dus de tweede stap vereist alleen proxy-informatie voor Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Als u ingewikkelde referenties hebt voor de proxyserver die niet in `-ProxyCredential` de `-InvokeWebRequestParameters`URL kan worden opgenomen, gebruikt u de parameter binnen . Bijvoorbeeld:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Zie [Aanroepen-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)voor meer informatie over proxyparameters. Zie [Azure IoT Edge-runtime installeren op Windows](how-to-install-iot-edge-windows.md)voor meer informatie over windows-installatieopties, waaronder offline installatie.

## <a name="configure-the-daemons"></a>De daemons configureren

IoT Edge is afhankelijk van twee daemons die op het IoT Edge-apparaat worden uitgevoerd. De Moby daemon doet webverzoeken om containerafbeeldingen uit containerregisters te halen. De IoT Edge daemon doet webverzoeken om te communiceren met IoT Hub.

Zowel de Moby- als de IoT Edge-daemons moeten worden geconfigureerd om de proxyserver te gebruiken voor doorlopende apparaatfunctionaliteit. Deze stap vindt plaats op het IoT Edge-apparaat tijdens de eerste installatie van het apparaat.

### <a name="moby-daemon"></a>Moby daemon

Aangezien Moby is gebouwd op Docker, raadpleegt u de Docker-documentatie om de Moby-daemon te configureren met omgevingsvariabelen. De meeste containerregisters (waaronder DockerHub en Azure Container Registries) ondersteunen HTTPS-aanvragen, zodat de parameter die u moet instellen **HTTPS_PROXY**is. Als u afbeeldingen uit een register haalt dat tls (transportlayer security) niet ondersteunt, moet u de **parameter HTTP_PROXY** instellen.

Kies het artikel dat van toepassing is op het besturingssysteem voor IoT Edge-apparaten:

* [Docker daemon configureren op Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) De Moby daemon op Linux apparaten behoudt de naam Docker.
* [Docker-daemon configureren op Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) De Moby daemon op Windows-apparaten heet iotedge-moby. De namen zijn anders omdat het mogelijk is om zowel Docker Desktop als Moby parallel op een Windows-apparaat uit te voeren.

### <a name="iot-edge-daemon"></a>IoT Edge daemon

De IoT Edge daemon is op een vergelijkbare manier geconfigureerd als de Moby daemon. Gebruik de volgende stappen om een omgevingsvariabele voor de service in te stellen op basis van uw besturingssysteem.

De IoT Edge daemon gebruikt altijd HTTPS om verzoeken naar IoT Hub te verzenden.

#### <a name="linux"></a>Linux

Open een editor in de terminal om de IoT Edge-daemon te configureren.

```bash
sudo systemctl edit iotedge
```

Voer de volgende tekst in en vervang ** \<de proxy-URL>** door uw proxyserveradres en -poort. Sla en verlaat dan.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Vernieuw de servicemanager om de nieuwe configuratie voor IoT Edge op te halen.

```bash
sudo systemctl daemon-reload
```

Start IoT Edge opnieuw op om de wijzigingen van kracht te laten worden.

```bash
sudo systemctl restart iotedge
```

Controleer of uw omgevingsvariabele is gemaakt en of de nieuwe configuratie is geladen.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Open een PowerShell-venster als beheerder en voer de volgende opdracht uit om het register te bewerken met de nieuwe omgevingsvariabele. Vervang ** \<de proxy-url>** door uw proxyserveradres en -poort.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Start IoT Edge opnieuw op om de wijzigingen van kracht te laten worden.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>De IoT Edge-agent configureren

De IoT Edge-agent is de eerste module die op een IoT Edge-apparaat wordt gestart. Het is voor het eerst gestart op basis van de informatie in het IoT Edge config.yaml-bestand. De IoT Edge-agent maakt vervolgens verbinding met IoT Hub om implementatiemanifesten op te halen, die aangeven welke andere modules op het apparaat moeten worden geïmplementeerd.

Deze stap vindt eenmaal plaats op het IoT Edge-apparaat tijdens de eerste apparaatconfiguratie.

1. Open het config.yaml-bestand op uw IoT Edge-apparaat. Op Linux-systemen bevindt dit bestand zich op **/etc/iotedge/config.yaml**. Op Windows-systemen bevindt dit bestand zich op **C:\ProgramData\iotedge\config.yaml**. Het configuratiebestand is beveiligd, dus u hebt beheerdersrechten nodig om er toegang toe te krijgen. Gebruik op Linux-systemen de `sudo` opdracht voordat u het bestand opent in de gewenste teksteditor. Open in Windows een teksteditor zoals Kladblok als beheerder en open het bestand.

2. Zoek in het config.yaml-bestand de sectie **Edge Agent-modulespecificaties.** De definitie van iot edge-agent bevat een **env-parameter** waar u omgevingsvariabelen toevoegen.

3. Verwijder de krullende haakjes die tijdelijke aanduidingen zijn voor de parameter env en voeg de nieuwe variabele toe aan een nieuwe regel. Houd er rekening mee dat inspringingen in YAML twee spaties zijn.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. De IoT Edge runtime maakt standaard gebruik van AMQP om met IoT Hub te praten. Sommige proxyservers blokkeren AMQP-poorten. Als dat het geval is, moet u edgeAgent ook configureren om AMQP via WebSocket te gebruiken. Voeg een tweede omgevingsvariabele toe.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent-definitie met omgevingsvariabelen](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Sla de wijzigingen op in config.yaml en sluit de editor. Start IoT Edge opnieuw op om de wijzigingen van kracht te laten worden.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Implementatiemanifesten configureren  

Zodra uw IoT Edge-apparaat is geconfigureerd om met uw proxyserver te werken, moet u de HTTPS_PROXY omgevingsvariabele blijven declareren in toekomstige implementatiemanifesten. U implementatiemanifesten bewerken met de wizard Azure-portal of door een JSON-bestand voor implementatiemanifest te bewerken.

Configureer altijd de twee runtime-modules, edgeAgent en edgeHub, om te communiceren via de proxyserver, zodat ze een verbinding met IoT Hub kunnen onderhouden. Als u de proxy-informatie uit de edgeAgent-module verwijdert, u de verbinding alleen herstellen door het config.yaml-bestand op het apparaat te bewerken, zoals beschreven in de vorige sectie.

Naast de edgeAgent- en edgeHub-modules hebben andere modules mogelijk de proxyconfiguratie nodig. Dit zijn modules die toegang moeten krijgen tot Azure-bronnen naast de IoT Hub, zoals blob-opslag, en die moeten de HTTPS_PROXY variabele hebben die voor die module is opgegeven in het manifestbestand voor implementatie.

De volgende procedure is van toepassing gedurende de hele levensduur van het IoT Edge-apparaat.

### <a name="azure-portal"></a>Azure Portal

Wanneer u de wizard **Modules instellen** gebruikt om implementaties voor IoT Edge-apparaten te maken, heeft elke module een sectie **Omgevingsvariabelen** die u gebruiken om proxyserververbindingen te configureren.

Als u de IoT Edge-agent en IoT Edge-hubmodules wilt configureren, selecteert u **Runtime-instellingen** in de eerste stap van de wizard.

![Geavanceerde Edge Runtime-instellingen configureren](./media/how-to-configure-proxy-support/configure-runtime.png)

Voeg de **https_proxy** omgevingsvariabele toe aan zowel de IoT Edge-agent als de IoT Edge-hubmoduledefinities. Als u de **upstreamprotocol-omgevingsvariabele** opnam in het config.yaml-bestand op uw IoT Edge-apparaat, voegt u dat ook toe aan de definitie van de IoT Edge-agentmodule.

![Https_proxy omgevingsvariabele instellen](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Alle andere modules die u aan een implementatiemanifest toevoegt, volgen hetzelfde patroon. Op de pagina waar u de naam en afbeelding van de module instelt, is er een sectie omgevingsvariabelen.

### <a name="json-deployment-manifest-files"></a>Json-manifestbestanden voor implementatie

Als u implementaties maakt voor IoT Edge-apparaten met behulp van de sjablonen in Visual Studio Code of door handmatig JSON-bestanden te maken, u de omgevingsvariabelen rechtstreeks aan elke moduledefinitie toevoegen.

Gebruik de volgende JSON-indeling:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Met de meegeleverde omgevingsvariabelen moet de moduledefinitie er uitzien als het volgende edgeHub-voorbeeld:

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

Als u de **upstreamprotocol-omgevingsvariabele** opneemt in het bestand confige.yaml op uw IoT Edge-apparaat, voegt u dat ook toe aan de definitie van de IoT Edge-agentmodule.

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

Meer informatie over de rollen van de [IoT Edge-runtime](iot-edge-runtime.md).

Installatie- en configuratiefouten oplossen met [veelvoorkomende problemen en oplossingen voor Azure IoT Edge](troubleshoot.md)
