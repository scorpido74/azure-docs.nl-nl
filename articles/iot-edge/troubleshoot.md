---
title: Problemen oplossen - Azure IoT Edge | Microsoft Docs
description: Gebruik dit artikel voor meer informatie over standard diagnostische vaardigheden voor Azure IoT Edge, net als bij het ophalen van Onderdeelstatus en logboeken en oplossen van veelvoorkomende problemen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 98d75f75a985fca3448becab216ad6570d948468
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284822"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Veelvoorkomende problemen en oplossingen voor Azure IoT Edge

Als u problemen hebt met het uitvoeren van Azure IoT Edge in uw omgeving, kunt u dit artikel als richtlijn gebruiken voor het oplossen van problemen.

## <a name="run-the-iotedge-check-command"></a>Voer de opdracht ' check ' iotedge uit

De eerste stap bij het oplossen van problemen met IoT Edge moet de `check` opdracht gebruiken, waarmee een verzameling configuratie-en connectiviteits tests voor veelvoorkomende problemen wordt uitgevoerd. De `check` opdracht is beschikbaar in [Release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) en hoger.

U kunt de `check` opdracht als volgt uitvoeren, of de `--help`-vlag opnemen om een volledige lijst met opties weer te geven:

* Op Linux:

  ```bash
  sudo iotedge check
  ```

* In Windows:

  ```powershell
  iotedge check
  ```

De typen controles die door het hulp programma worden uitgevoerd, kunnen als volgt worden geclassificeerd:

* Configuratie controles: Hiermee wordt informatie onderzocht waarmee kan worden voor komen dat edge-apparaten verbinding maken met de Cloud, met inbegrip van problemen met *config. yaml* en de container engine.
* Verbinding controleren: controleert of de IoT Edge runtime toegang heeft tot poorten op het hostapparaat en dat alle IoT Edge onderdelen verbinding kunnen maken met de IoT Hub.
* Productie gereedheids controles: er wordt gezocht naar aanbevolen productie best practices, zoals de status van certificaten voor de certificaat instantie van de apparaat en de configuratie van het logboek bestand van de module.

Zie voor een volledige lijst met diagnostische gegevens [ingebouwde probleemoplossings functies](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Standaard diagnostische stappen

Als u een probleem ondervindt, kunt u meer informatie over de status van uw IoT Edge-apparaat bekijken door de container logboeken en de berichten die door en van het apparaat worden door gegeven. Gebruik de opdrachten en hulpprogramma's in deze sectie om informatie te verzamelen.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Controleer de status van de IoT Edge Security Manager en de bijbehorende logboeken

Op Linux:

* De status van de IoT Edge Security Manager weergeven:

   ```bash
   sudo systemctl status iotedge
   ```

* De logboeken van de IoT Edge Security Manager weergeven:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Gedetailleerde logboeken van de IoT Edge Security Manager om informatie weer te geven:

  * De instellingen van de daemon iotedge bewerken:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Werk de volgende regels:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * De IoT Edge Security-Daemon opnieuw:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

In Windows:

* De status van de IoT Edge Security Manager weergeven:

   ```powershell
   Get-Service iotedge
   ```

* De logboeken van de IoT Edge Security Manager weergeven:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Als de IoT Edge Security Manager wordt niet uitgevoerd, controleert u of uw yaml-configuratiebestand

> [!WARNING]
> YAML-bestanden kunnen geen tabbladen als inspringing bevatten. Gebruik in plaats daarvan 2 spaties. Elementen op het hoogste niveau mogen geen voorloop spaties bevatten.

Op Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Raadpleeg de containerlogboeken voor problemen

Zodra de IoT Edge Security-Daemon wordt uitgevoerd, bekijkt u de logboeken van de containers om problemen te detecteren. Begin met uw geïmplementeerde containers en bekijk vervolgens de containers waaruit de IoT Edge runtime: edgeAgent en edgeHub. De logboeken van de IoT Edge-agent bevatten doorgaans informatie over de levens cyclus van elke container. De IoT Edge hub-logboeken bieden informatie over berichten en route ring.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>De berichten weer geven die via de IoT Edge hub worden verzonden

U kunt de berichten van de IoT Edge hub bekijken en inzichten verzamelen uit uitgebreide logboeken van de runtime-containers. Als u uitgebreide logboeken op deze containers wilt inschakelen, stelt u `RuntimeLogLevel` in het configuratie bestand van uw yaml in. Open het bestand:

Op Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Het `agent` element ziet er standaard uit als in het volgende voor beeld:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Vervang `env: {}` door:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-bestanden mag geen tabbladen als inspringing bevatten. Gebruik in plaats daarvan 2 spaties. Items op het hoogste niveau kunnen geen voorloop spaties bevatten.

Sla het bestand op en start de IoT Edge security manager opnieuw.

U kunt ook de berichten controleren die worden verzonden tussen IoT Hub en de IoT Edge-apparaten. Bekijk deze berichten met behulp van de [Azure IOT hub-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Zie [het handige hulp programma bij het ontwikkelen met Azure IOT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)voor meer informatie.

### <a name="restart-containers"></a>Opnieuw opstarten van containers

Na onderzoek van de logboeken en berichten voor meer informatie, kunt u proberen opnieuw te starten containers:

```cmd
iotedge restart <container name>
```

Start opnieuw op de IoT Edge-runtime-containers:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Opnieuw opstarten van de IoT Edge-beveiligingsbeheer

Als het probleem nog steeds in de persistent maken, kunt u proberen de IoT Edge security manager opnieuw te starten.

Op Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

In Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agent stopt na ongeveer een minuut

De edgeAgent-module wordt ongeveer een minuut gestart en uitgevoerd en vervolgens gestopt. In de logboeken wordt aangegeven dat de IoT Edge-agent probeert verbinding te maken met IoT Hub via AMQP en vervolgens probeert verbinding te maken via AMQP via WebSocket. Als dit mislukt, wordt de IoT Edge-agent afgesloten.

Voor beeld van edgeAgent-logboeken:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Hoofd oorzaak**

Een netwerk configuratie op het doelnet werk verhindert dat de IoT Edge agent het netwerk bereikt. De agent probeert eerst verbinding maken via AMQP (poort 5671). Als de verbinding is mislukt, probeert deze WebSockets (poort 443).

De IoT Edge-runtime stelt een netwerk in voor elk van de modules waarmee moet worden gecommuniceerd. In Linux is dit netwerk een brugnetwerk. In Windows wordt NAT gebruikt. Dit probleem komt vaker voor op Windows-apparaten die gebruikmaken van Windows-containers die het NAT-netwerk gebruiken.

**Afsluiting**

Zorg ervoor dat er een route naar internet is voor de IP-adressen die aan deze brug/dit NAT-netwerk zijn toegewezen. Soms heeft een VPN-configuratie op de host voorrang op het IoT Edge-netwerk.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub kan niet worden gestart

De edgeHub-module kan niet worden gestart en het volgende bericht wordt in de logboeken afgedrukt:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Hoofd oorzaak**

Poort 443 is bezig met een ander proces op de hostmachine. De IoT Edge hub wijst poorten 5671 en 443 toe voor gebruik in Gateway scenario's. Deze poorttoewijzing mislukt als er al een ander proces bezig is op deze poort.

**Afsluiting**

Zoek en stop het proces dat poort 443 gebruikt. Dit proces is meestal een webserver.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agent heeft geen toegang tot de installatie kopie van een module (403)

Een container kan niet worden uitgevoerd, en in de edgeAgent-Logboeken wordt een 403-fout weer gegeven.

**Hoofd oorzaak**

De IoT Edge-agent heeft geen machtigingen voor toegang tot de installatie kopie van een module.

**Afsluiting**

Zorg ervoor dat de registerreferenties van uw juist zijn opgegeven in uw manifest implementatie

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge security-daemon is mislukt met een ongeldige hostnaam

De opdracht `sudo journalctl -u iotedge` mislukt en het volgende bericht wordt afgedrukt:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Hoofd oorzaak**

IoT Edge-runtime biedt alleen ondersteuning voor hostnamen die korter zijn dan 64 tekens lang zijn. Fysieke machines gewoonlijk lang hostnamen niet hebben, maar het probleem komt vaker voor op een virtuele machine. De automatisch gegenereerde hostnamen voor Windows virtuele machines die worden gehost in Azure, meestal in het bijzonder te lang.

**Afsluiting**

Wanneer u deze fout ziet, kunt u deze kunt oplossen door de DNS-naam van uw virtuele machine configureren en vervolgens de DNS-naam als de hostnaam in de setup-opdracht in te stellen.

1. Navigeer naar de overzichtspagina van de virtuele machine in Azure portal.
2. Selecteer **configureren** onder DNS-naam. Als uw virtuele machine is al een DNS-naam die is geconfigureerd, hoeft u niet te configureren van een nieuwe.

   ![DNS-naam van de virtuele machine configureren](./media/troubleshoot/configure-dns.png)

3. Geef een waarde op voor het label voor de **DNS-naam** en selecteer **Opslaan**.
4. Kopieer de nieuwe DNS-naam, die de indeling **\<DNSnamelabel\>moet hebben.\<vmlocation\>. cloudapp.Azure.com**.
5. Gebruik de volgende opdracht voor het instellen van de IoT Edge-runtime met uw DNS-naam in de virtuele machine:

   * Op Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * In Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemen met de stabiliteit van bron beperkte apparaten

U kunt de stabiliteitsproblemen op beperkte apparaten, zoals de Raspberry Pi, ondervinden met name wanneer die wordt gebruikt als een gateway. Symptomen zijn buiten het geheugen uitzonderingen in de edge hub-module, downstream apparaten kunnen geen verbinding maken of het apparaat reageert verzenden van berichten over telemetrie na een paar uur.

**Hoofd oorzaak**

De IoT Edge hub, die deel uitmaakt van de IoT Edge runtime, is standaard geoptimaliseerd voor prestaties en probeert grote delen van geheugen toe te wijzen. Deze optimalisatie is niet ideaal voor beperkte edge-apparaten en stabiliteitsproblemen kan veroorzaken.

**Afsluiting**

Stel voor de IoT Edge hub een omgevings variabele **OptimizeForPerformance** in op **False**. Er zijn twee manieren om omgevings variabelen in te stellen:

In Azure Portal:

Selecteer uw IoT Edge apparaat en op de pagina Details van apparaat in uw IoT Hub en selecteer **modules instellen** > **runtime-instellingen**. Maak een omgevings variabele voor de Edge hub-module met de naam *OptimizeForPerformance* die is ingesteld op *False*.

![OptimizeForPerformance ingesteld op false](./media/troubleshoot/optimizeforperformance-false.png)

**OF**

In het manifest van de implementatie:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Kan niet op Windows op de IoT Edge daemon-logboeken ophalen

Als u een EventLogException krijgt wanneer u `Get-WinEvent` in Windows gebruikt, controleert u de Register vermeldingen.

**Hoofd oorzaak**

De `Get-WinEvent` Power shell-opdracht is afhankelijk van een register vermelding die aanwezig moet zijn om logboeken te zoeken op basis van een specifieke `ProviderName`.

**Afsluiting**

Een registervermelding voor de IoT Edge-daemon instellen. Maak een **iotedge. reg** -bestand met de volgende inhoud en importeer het in het Windows-REGI ster door erop te dubbel klikken of door de `reg import iotedge.reg` opdracht te gebruiken:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge-module is mislukt voor het verzenden van een bericht naar de edgeHub met 404-fout

Een aangepaste IoT Edge module kan geen bericht verzenden naar de edgeHub met een 404 `Module not found` fout. De IoT Edge-daemon afdrukken van het volgende bericht naar de logboeken:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Hoofd oorzaak**

De IoT Edge-daemon wordt afgedwongen proces-id in voor alle modules die verbinding maken met de edgeHub uit veiligheidsoverwegingen. Hiermee wordt gecontroleerd dat alle berichten worden verzonden door een module afkomstig van de belangrijkste proces-ID van de module zijn. Als een bericht wordt verzonden door een module van de ID van een ander proces dan in eerste instantie tot stand gebracht, wordt deze het bericht met een 404-fout-bericht negeren.

**Afsluiting**

Vanaf versie 1.0.7 zijn alle module processen gemachtigd om verbinding te maken. Als u een upgrade naar 1.0.7 niet mogelijk hebt, voert u de volgende stappen uit. Zie [1.0.7 release wijzigingen logboek](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)voor meer informatie.

Zorg ervoor dat de hetzelfde proces-ID altijd door de aangepaste IoT Edge-module gebruikt wordt om berichten te verzenden naar de edgeHub. Zorg er bijvoorbeeld voor dat `ENTRYPOINT` in plaats van `CMD` opdracht in uw docker-bestand, omdat `CMD` leidt tot één proces-ID voor de module en een andere proces-ID voor de bash-opdracht die het hoofd programma uitvoert, terwijl `ENTRYPOINT` tot één proces-ID leidt.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Firewall- en poortinstellingen configuratieregels voor IoT Edge-implementatie

Zie [een communicatie protocol kiezen](../iot-hub/iot-hub-devguide-protocols.md)Azure IOT Edge communicatie van een on-premises server naar Azure-Cloud met ondersteunde IOT hub protocollen toestaat. Voor een betere beveiliging zijn altijd communicatiekanalen tussen Azure IoT Edge en Azure IoT-Hub geconfigureerd om uitgaand. Deze configuratie is gebaseerd op het [service-ondersteunde communicatie patroon](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), waarmee de kwets baarheid voor een kwaadwillende entiteit wordt geminimaliseerd. Binnenkomende communicatie is alleen vereist voor specifieke scenario's waar Azure IoT Hub moeten push-berichten naar de Azure IoT Edge-apparaat. Cloud-naar-apparaat-berichten worden beschermd met behulp van beveiligde TLS-kanalen en verder kunnen beveiligd met X.509-certificaten en -modules voor TPM-apparaat. De Azure IoT Edge Security Manager bepaalt hoe deze communicatie tot stand kan worden gebracht. Zie [IOT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Hoewel IoT Edge verbeterde configuratie biedt voor het beveiligen van Azure IoT Edge-runtime en modules geïmplementeerd, is het nog steeds afhankelijk van de onderliggende machine en het netwerk. Daarom is het van cruciaal belang om ervoor te zorgen dat de juiste netwerk-en firewall regels worden ingesteld voor beveiligde Edge-to-Cloud communicatie. De volgende tabel kan worden gebruikt als richt lijn bij de configuratie van firewall regels voor de onderliggende servers waarop Azure IoT Edge runtime wordt gehost:

|Protocol|Poort|binnenkomende|Uitgaand|Richtlijnen|
|--|--|--|--|--|
|MQTT|8883|GEBLOKKEERDE (standaard)|GEBLOKKEERDE (standaard)|<ul> <li>Uitgaand (uitgaand) te openen bij gebruik van MQTT als het communicatieprotocol configureren.<li>. 1883 voor MQTT wordt niet ondersteund door de IoT Edge. <li>Binnenkomende verbindingen voor (inkomend) moeten worden geblokkeerd.</ul>|
|AMQP|5671|GEBLOKKEERDE (standaard)|OPEN (standaard)|<ul> <li>Standaard communicatieprotocol voor IoT Edge. <li> Moet worden geconfigureerd om te worden geopend als Azure IoT Edge is niet geconfigureerd voor andere ondersteunde protocollen of AMQP het gewenste communicatieprotocol is.<li>5672 voor AMQP wordt niet ondersteund door de IoT Edge.<li>Deze poort blokkeren wanneer Azure IoT Edge maakt gebruik van een andere IoT-Hub ondersteund protocol.<li>Binnenkomende verbindingen voor (inkomend) moeten worden geblokkeerd.</ul></ul>|
|HTTPS|443|GEBLOKKEERDE (standaard)|OPEN (standaard)|<ul> <li>Configureer uitgaande (uitgaand) te openen op 443 voor IoT Edge wordt ingericht. Deze configuratie is vereist bij het gebruik van handmatige scripts of Azure IoT Device Provisioning Service (DPS). <li>Inkomende (inkomend) verbinding moet Open zijn alleen voor specifieke scenario's: <ul> <li>  Als u een transparante gateway met leaf-apparaten die methodeaanvragen kunnen worden verzonden hebt. Poort 443 hoeft in dit geval niet te worden geopend voor externe netwerken verbinding maken met IoTHub of IoTHub-services leveren via Azure IoT Edge. De binnenkomende regel kan zo worden beperkt tot alleen binnenkomende (binnenkomend) openen van het interne netwerk. <li> Voor de Client naar apparaat (C2D)-scenario's.</ul><li>80 voor HTTP wordt niet ondersteund door de IoT Edge.<li>Als niet-HTTP-protocollen (bijvoorbeeld AMQP of MQTT) kunnen niet worden geconfigureerd in de onderneming; de berichten kunnen worden verzonden via WebSockets. In dat geval wordt poort 443 gebruikt voor communicatie van de WebSocket.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>De Edge Agent-module rapporteert voortdurend het lege configuratie bestand en er worden geen modules gestart op het apparaat

Het apparaat heeft geen problemen met het starten van modules die in de implementatie zijn gedefinieerd. Alleen de edgeAgent wordt uitgevoerd, maar doorlopend een leeg configuratie bestand...

**Hoofd oorzaak**

Standaard worden modules IoT Edge in hun eigen geïsoleerde container netwerk gestart. Het apparaat heeft mogelijk problemen met de DNS-naam omzetting binnen dit particuliere netwerk.

**Afsluiting**

**Optie 1: DNS-server instellen in container Engine-instellingen**

Geef de DNS-server voor uw omgeving op in de instellingen van de container-engine, die van toepassing is op alle container modules die zijn gestart door de engine. Maak een bestand met de naam `daemon.json` Geef de DNS-server op die moet worden gebruikt. Bijvoorbeeld:

```json
{
    "dns": ["1.1.1.1"]
}
```

In het bovenstaande voor beeld wordt de DNS-server ingesteld op een openbaar toegankelijke DNS-service. Als het edge-apparaat geen toegang kan krijgen tot dit IP-adres in de omgeving, vervangt u het door de DNS-server die toegankelijk is.

Plaats `daemon.json` op de juiste locatie voor uw platform:

| Platform | Locatie |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-host met Windows-containers | `C:\ProgramData\iotedge-moby\config` |

Als de locatie al `daemon.json` bestand bevat, voegt u de **DNS-** sleutel hieraan toe en slaat u het bestand op.

Start de container-engine opnieuw op om de updates van kracht te laten worden.

| Platform | Opdracht |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (admin Power shell) | `Restart-Service iotedge-moby -Force` |

**Optie 2: DNS-server instellen in IoT Edge-implementatie per module**

U kunt de DNS-server voor de *createOptions* van elke module instellen in de implementatie van IOT Edge. Bijvoorbeeld:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Zorg ervoor dat u deze configuratie ook instelt voor de *edgeAgent* -en *edgeHub* -modules.

## <a name="next-steps"></a>Volgende stappen

Denkt u dat u een fout op het IoT Edge-platform hebt gevonden? [Dien een probleem](https://github.com/Azure/iotedge/issues) in zodat we kunnen blijven verbeteren.

Als u meer vragen hebt, maakt u een [ondersteuningsaanvraag](https://portal.azure.com/#create/Microsoft.Support) voor hulp.
