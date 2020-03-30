---
title: Problemen oplossen - Azure IoT Edge | Microsoft Documenten
description: Gebruik dit artikel om standaarddiagnostische vaardigheden voor Azure IoT Edge te leren, zoals het ophalen van componentstatus en logboeken en het oplossen van veelvoorkomende problemen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13eab175356ed1ec20caa3263ba00d0563384f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064376"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Veelvoorkomende problemen en oplossingen voor Azure IoT Edge

Als u problemen hebt met het uitvoeren van Azure IoT Edge in uw omgeving, kunt u dit artikel als richtlijn gebruiken voor het oplossen van problemen.

## <a name="run-the-iotedge-check-command"></a>De opdracht iotedge 'check' uitvoeren

Uw eerste stap bij het oplossen van `check` problemen met IoT Edge moet zijn om de opdracht te gebruiken, die een verzameling configuratie- en connectiviteitstests uitvoert voor veelvoorkomende problemen. De `check` opdracht is beschikbaar in [versie 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) en hoger.

U `check` de opdracht als volgt `--help` uitvoeren of de vlag opnemen om een volledige lijst met opties te bekijken:

* Op Linux:

  ```bash
  sudo iotedge check
  ```

* In Windows:

  ```powershell
  iotedge check
  ```

De soorten controles die door het hulpprogramma worden uitgevoerd, kunnen worden geclassificeerd als:

* Configuratiecontroles: onderzoekt details die kunnen voorkomen dat Edge-apparaten verbinding maken met de cloud, inclusief problemen met *config.yaml* en de containerengine.
* Verbindingscontroles: hiermee controleert u of de runtime van IoT Edge toegang heeft tot poorten op het hostapparaat en kunnen alle IoT Edge-componenten verbinding maken met de IoT-hub.
* Production readiness checks: Looks for recommended production best practices, such as the state of device certificate authority (CA) certificates and module log file configuration.

Zie Ingebouwde functionaliteit voor [probleemoplossing](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)voor een volledige lijst met diagnostische controles.

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Verzamel foutopsporingsinformatie met de opdracht 'support-bundle' van iotedge

Wanneer u logboeken van een IoT Edge-apparaat moet verzamelen, is de handigste manier om de `support-bundle` opdracht te gebruiken. Standaard verzamelt deze opdracht module, IoT Edge Security Manager en containerenginelogs, 'iotedge check' JSON-uitvoer en andere nuttige foutopsporingsinformatie. Het comprimeert ze in een enkel bestand voor eenvoudig delen. De `support-bundle` opdracht is beschikbaar in [versie 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) en hoger.

Voer `support-bundle` de opdracht `--since` met de vlag uit om aan te geven hoe lang vanaf het verleden u logboeken wilt ophalen. Bijvoorbeeld `6h` krijgt logs sinds de laatste `6d` 6 uur, `6m` sinds de laatste 6 dagen, sinds de laatste 6 minuten en ga zo maar door. Voeg `--help` de vlag toe om een volledige lijst met opties te bekijken.


* Op Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* In Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> Uitvoer van `support-bundle` de opdracht kan host, apparaat en module namen, informatie die door uw modules etc. Houd er rekening mee als het delen van de output in een openbaar forum.

## <a name="standard-diagnostic-steps"></a>Standaard diagnostische stappen

Als u een probleem ondervindt, u meer te weten komen over de status van uw IoT Edge-apparaat door de containerlogboeken en de berichten die van en naar het apparaat worden verzonden, te bekijken. Gebruik de opdrachten en hulpprogramma's in deze sectie om informatie te verzamelen.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Controleer de status van de IoT Edge Security Manager en de logboeken

Op Linux:

* Ga als een overzicht van de status van De IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Ga als het gaat om de logboeken van Het IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Ga als eerste voor meer gedetailleerde logboeken van Het IoT Edge Security Manager:

  * Bewerk de instellingen van de iotedge daemon:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Werk de volgende regels bij:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Start de IoT Edge Security Daemon opnieuw:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

In Windows:

* Ga als een overzicht van de status van De IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Ga als het gaat om de logboeken van Het IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Als het IoT Edge Security Manager niet wordt uitgevoerd, controleert u het yaml-configuratiebestand

> [!WARNING]
> YAML-bestanden mogen geen tabbladen als inspringing bevatten. Gebruik in plaats daarvan 2 spaties. Elementen op het hoogste niveau mogen geen leidende spaties hebben.

Op Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Containerlogboeken controleren op problemen

Zodra de IoT Edge Security Daemon wordt uitgevoerd, bekijkt u de logboeken van de containers om problemen op te sporen. Begin met de geïmplementeerde containers en kijk vervolgens naar de containers die deel uitmaken van de IoT Edge-runtime: edgeAgent en edgeHub. De IoT Edge-agentlogboeken bieden doorgaans informatie over de levenscyclus van elke container. De IoT Edge-hublogboeken bieden informatie over berichten en routering.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Bekijk de berichten die via de IoT Edge-hub gaan

U de berichten die door de IoT Edge-hub gaan bekijken en inzichten verzamelen uit boselogboeken uit de runtime-containers. Als u verbose-logboeken op `RuntimeLogLevel` deze containers wilt inschakelen, stelt u het yaml-configuratiebestand in. Ga als u het bestand openen:

Op Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Het `agent` element ziet er standaard uit als het volgende voorbeeld:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Vervangen `env: {}` door:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-bestanden kunnen geen tabbladen bevatten als identatie. Gebruik in plaats daarvan 2 spaties. Items op het hoogste niveau kunnen geen leidende witruimte hebben.

Sla het bestand op en start de IoT Edge-beveiligingsmanager opnieuw.

U kunt ook de berichten controleren die worden verzonden tussen IoT Hub en de IoT Edge-apparaten. Bekijk deze berichten met de [Azure IoT Hub-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Zie Handig hulpmiddel voor meer informatie [wanneer u zich ontwikkelt met Azure IoT.](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)

### <a name="restart-containers"></a>Containers opnieuw opstarten

Nadat u de logboeken en berichten voor informatie hebt onderzocht, u proberen containers opnieuw op te starten:

```cmd
iotedge restart <container name>
```

Start de runtime-containers van IoT Edge opnieuw:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>De Beveiligingsmanager van IoT Edge opnieuw starten

Als het probleem nog steeds aanhoudt, u proberen de IoT Edge-beveiligingsmanager opnieuw te starten.

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

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge-agent stopt na ongeveer een minuut

De edgeAgent-module start en wordt ongeveer een minuut lang met succes uitgevoerd en stopt vervolgens. De logboeken geven aan dat de IoT Edge-agent probeert verbinding te maken met IoT Hub via AMQP en vervolgens probeert verbinding te maken via AMQP via WebSocket. Als dat niet lukt, wordt de IoT Edge-agent afgesloten.

Voorbeeld edgeAgent-logboeken:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Hoofdoorzaak**

Een netwerkconfiguratie op het hostnetwerk voorkomt dat de IoT Edge-agent het netwerk bereikt. De agent probeert eerst verbinding maken via AMQP (poort 5671). Als de verbinding mislukt, probeert het WebSockets (poort 443).

De IoT Edge-runtime stelt een netwerk in voor elk van de modules waarmee moet worden gecommuniceerd. In Linux is dit netwerk een brugnetwerk. In Windows wordt NAT gebruikt. Dit probleem komt vaker voor op Windows-apparaten die gebruikmaken van Windows-containers die het NAT-netwerk gebruiken.

**Afsluiting**

Zorg ervoor dat er een route naar internet is voor de IP-adressen die aan deze brug/dit NAT-netwerk zijn toegewezen. Soms heeft een VPN-configuratie op de host voorrang op het IoT Edge-netwerk.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge-hub gaat niet door

De edgeHub-module start niet en drukt het volgende bericht af op de logboeken:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Hoofdoorzaak**

Poort 443 is bezig met een ander proces op de hostmachine. De IoT Edge-hub brengt poorten 5671 en 443 in kaart voor gebruik in gatewayscenario's. Deze poorttoewijzing mislukt als er al een ander proces bezig is op deze poort.

**Afsluiting**

Zoek en stop het proces dat poort 443 gebruikt. Dit proces is meestal een webserver.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge-agent heeft geen toegang tot de afbeelding van een module (403)

Een container wordt niet uitgevoerd en de edgeAgent-logboeken vertonen een fout van 403.

**Hoofdoorzaak**

De IoT Edge-agent heeft geen machtigingen voor toegang tot de afbeelding van een module.

**Afsluiting**

Zorg ervoor dat uw registerreferenties correct zijn opgegeven in uw implementatiemanifest

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge security daemon mislukt met een ongeldige hostname

De `sudo journalctl -u iotedge` opdracht mislukt en drukt het volgende bericht af:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Hoofdoorzaak**

De IoT Edge-runtime kan alleen hostnamen ondersteunen die korter zijn dan 64 tekens. Fysieke machines hebben meestal geen lange hostnamen, maar het probleem komt vaker voor op een virtuele machine. De automatisch gegenereerde hostnamen voor virtuele Windows-machines die in Azure worden gehost, zijn meestal lang.

**Afsluiting**

Wanneer u deze fout ziet, u deze oplossen door de DNS-naam van uw virtuele machine te configureren en vervolgens de DNS-naam als hostnaam in de installatieopdracht in te stellen.

1. Navigeer in de Azure-portal naar de overzichtspagina van uw virtuele machine.
2. Selecteer **configureren** onder DNS-naam. Als uw virtuele machine al een DNS-naam heeft geconfigureerd, hoeft u geen nieuwe te configureren.

   ![DNS-naam van virtuele machine configureren](./media/troubleshoot/configure-dns.png)

3. Geef een waarde op voor **dns-naamlabel** en selecteer **Opslaan**.
4. Kopieer de nieuwe DNS-naam, die moet worden weergegeven in het formaat ** \<\>DNSnamelabel\< . vmlocatie\>.cloudapp.azure.com**.
5. Gebruik in de virtuele machine de volgende opdracht om de IoT Edge-runtime in te stellen met uw DNS-naam:

   * Op Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * In Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Stabiliteitsproblemen op apparaten met beperkte resources

U stabiliteitsproblemen ondervinden op apparaten met beperkte beperkingen, zoals de Raspberry Pi, vooral wanneer u als gateway wordt gebruikt. Symptomen zijn uitzonderingen buiten het geheugen in de edge hub-module, downstream-apparaten kunnen geen verbinding maken of het apparaat stopt na een paar uur met het verzenden van telemetrieberichten.

**Hoofdoorzaak**

De IoT Edge-hub, die deel uitmaakt van de IoT Edge-runtime, is standaard geoptimaliseerd voor prestaties en probeert grote stukken geheugen toe te wijzen. Deze optimalisatie is niet ideaal voor apparaten met beperkte rand en kan stabiliteitsproblemen veroorzaken.

**Afsluiting**

Stel voor de IoT Edge-hub een omgevingsvariabele **OptimizeForPerformance** in op **false**. Er zijn twee manieren om omgevingsvariabelen in te stellen:

In Azure Portal:

Selecteer in uw IoT-hub uw IoT Edge-apparaat en selecteer op de pagina apparaatdetails en selecteer Instellingen voor > **runtime-instellingen**voor **modules instellen.** Maak een omgevingsvariabele voor de Edge Hub-module met de naam *OptimizeForPerformance* die is ingesteld op *false.*

![OptimizeForPerformance ingesteld op false](./media/troubleshoot/optimizeforperformance-false.png)

**Of**

In het implementatiemanifest:

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

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Kan de IoT Edge daemon-logboeken niet op Windows krijgen

Als u een EventLogException `Get-WinEvent` krijgt wanneer u deze gebruikt op Windows, controleert u uw registervermeldingen.

**Hoofdoorzaak**

De `Get-WinEvent` PowerShell-opdracht is afhankelijk van een registervermelding die `ProviderName`aanwezig moet zijn om logboeken te vinden door een specifieke .

**Afsluiting**

Stel een registervermelding in voor de IoT Edge-daemon. Maak een **iotedge.reg-bestand** met de volgende inhoud en importeer in het Windows-register door erop te dubbelklikken of de `reg import iotedge.reg` opdracht te gebruiken:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge-module stuurt geen bericht naar de edgeHub met een fout van 404

Een aangepaste IoT Edge-module stuurt geen bericht naar de `Module not found` edgeHub met een 404-fout. De Daemon IoT Edge drukt het volgende bericht af op de logboeken:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Hoofdoorzaak**

De IoT Edge daemon dwingt procesidentificatie af voor alle modules die om veiligheidsredenen verbinding maken met de edgeHub. Het controleert of alle berichten die door een module worden verzonden, afkomstig zijn van de hoofdproces-ID van de module. Als een bericht wordt verzonden door een module vanuit een andere proces-ID dan in eerste instantie is vastgesteld, wordt het bericht geweigerd met een foutbericht van 404.

**Afsluiting**

Vanaf versie 1.0.7 zijn alle moduleprocessen toegestaan om verbinding te maken. Als upgraden naar 1.0.7 niet mogelijk is, voert u de volgende stappen uit. Zie voor meer informatie de [changelog van 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Zorg ervoor dat dezelfde proces-ID altijd wordt gebruikt door de aangepaste IoT Edge-module om berichten naar de edgeHub te verzenden. Zorg er bijvoorbeeld `ENTRYPOINT` voor `CMD` dat u in plaats `CMD` van opdracht in uw Docker-bestand, want zal leiden tot `ENTRYPOINT` een proces-ID voor de module en een ander proces-ID voor de bash-opdracht die het hoofdprogramma uitvoert, terwijl dit zal leiden tot een enkele proces-ID.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Configuratieregels voor firewall en poort voor IoT Edge-implementatie

Azure IoT Edge maakt communicatie mogelijk van een on-premises server naar Azure-cloud met behulp van ondersteunde IoT Hub-protocollen, zie [het kiezen van een communicatieprotocol.](../iot-hub/iot-hub-devguide-protocols.md) Voor verbeterde beveiliging zijn communicatiekanalen tussen Azure IoT Edge en Azure IoT Hub altijd geconfigureerd om uitgaand te zijn. Deze configuratie is gebaseerd op het [services assisted communication-patroon](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), dat het aanvalsoppervlak minimaliseert voor een kwaadwillende entiteit om te verkennen. Binnenkomende communicatie is alleen vereist voor specifieke scenario's waarin Azure IoT Hub berichten naar het Azure IoT Edge-apparaat moet pushen. Cloud-to-device-berichten worden beveiligd via beveiligde TLS-kanalen en kunnen verder worden beveiligd met X.509-certificaten en TPM-apparaatmodules. De Azure IoT Edge Security Manager bepaalt hoe deze communicatie tot stand kan worden gebracht, zie [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Hoewel IoT Edge verbeterde configuratie biedt voor het beveiligen van Azure IoT Edge-runtime en geïmplementeerde modules, is het nog steeds afhankelijk van de onderliggende machine- en netwerkconfiguratie. Daarom is het noodzakelijk om ervoor te zorgen dat de juiste netwerk- en firewallregels worden ingesteld voor veilige edge-to-cloudcommunicatie. De volgende tabel kan als richtlijn worden gebruikt wanneer configuratiefirewallregels voor de onderliggende servers waar Azure IoT Edge-runtime wordt gehost:

|Protocol|Poort|Binnenkomende|Uitgaande|Richtlijnen|
|--|--|--|--|--|
|MQTT|8883|GEBLOKKEERD (standaard)|GEBLOKKEERD (standaard)|<ul> <li>Configureer Uitgaande (Uitgaande) om geopend te zijn wanneer u MQTT als communicatieprotocol gebruikt.<li>1883 voor MQTT wordt niet ondersteund door IoT Edge. <li>Binnenkomende (binnenkomende) verbindingen moeten worden geblokkeerd.</ul>|
|AMQP|5671|GEBLOKKEERD (standaard)|OPEN (Standaard)|<ul> <li>Standaardcommunicatieprotocol voor IoT Edge. <li> Moet zijn geconfigureerd om te worden geopend als Azure IoT Edge niet is geconfigureerd voor andere ondersteunde protocollen of AMQP het gewenste communicatieprotocol is.<li>5672 voor AMQP wordt niet ondersteund door IoT Edge.<li>Blokkeer deze poort wanneer Azure IoT Edge een ander door IoT Hub ondersteund protocol gebruikt.<li>Binnenkomende (binnenkomende) verbindingen moeten worden geblokkeerd.</ul></ul>|
|HTTPS|443|GEBLOKKEERD (standaard)|OPEN (Standaard)|<ul> <li>Configureer Uitgaande (Uitgaande) om geopend te zijn op 443 voor IoT Edge provisioning. Deze configuratie is vereist bij het gebruik van handmatige scripts of Azure IoT Device Provisioning Service (DPS). <li>Binnenkomende (binnenkomende) verbinding moet alleen worden geopend voor specifieke scenario's: <ul> <li>  Als u een transparante gateway hebt met bladapparaten die methodeaanvragen kunnen verzenden. In dit geval hoeft poort 443 niet open te staan voor externe netwerken om verbinding te maken met IoTHub of IoTHub-services te leveren via Azure IoT Edge. De inkomende regel kan dus worden beperkt tot alleen openen binnenkomend (Binnenkomend) vanuit het interne netwerk. <li> Voor C2D-scenario's (Client to Device).</ul><li>80 voor HTTP wordt niet ondersteund door IoT Edge.<li>Als niet-HTTP-protocollen (bijvoorbeeld AMQP of MQTT) niet kunnen worden geconfigureerd in de onderneming; de berichten kunnen via WebSockets worden verzonden. Poort 443 zal in dat geval worden gebruikt voor WebSocket-communicatie.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge Agent-module rapporteert voortdurend 'leeg config-bestand' en er starten geen modules op het apparaat

Het apparaat heeft problemen met het starten van modules gedefinieerd in de implementatie. Alleen de edgeAgent wordt uitgevoerd, maar voortdurend rapportage 'lege config bestand ...'.

**Hoofdoorzaak**

IoT Edge start standaard modules in hun eigen geïsoleerde containernetwerk. Mogelijk heeft het apparaat problemen met dns-naamomzetting binnen dit privénetwerk.

**Afsluiting**

**Optie 1: DNS-server instellen in containerengine-instellingen**

Geef de DNS-server voor uw omgeving op in de instellingen van de containerengine, die van toepassing is op alle containermodules die door de engine zijn gestart. Maak een `daemon.json` bestand met de naam waarin de DNS-server wordt opgegeven die moet worden gebruikt. Bijvoorbeeld:

```json
{
    "dns": ["1.1.1.1"]
}
```

In het bovenstaande voorbeeld wordt de DNS-server ingesteld op een openbaar toegankelijke DNS-service. Als het edge-apparaat geen toegang heeft tot dit IP vanuit zijn omgeving, vervangt u het door dns-serveradres dat toegankelijk is.

Plaats `daemon.json` op de juiste locatie voor uw platform:

| Platform | Locatie |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-host met Windows-containers | `C:\ProgramData\iotedge-moby\config` |

Als de locatie `daemon.json` al bestand bevat, voegt u de **dns-sleutel** eraan toe en slaat u het bestand op.

Start de containerengine opnieuw op om de updates van kracht te laten worden.

| Platform | Opdracht |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Beheerder Powershell) | `Restart-Service iotedge-moby -Force` |

**Optie 2: DNS-server instellen in IoT Edge-implementatie per module**

U DNS-server instellen voor de *createOptions* van elke module in de IoT Edge-implementatie. Bijvoorbeeld:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Zorg ervoor dat u deze configuratie ook instelt voor de *edgeAgent-* en *edgeHub-modules.*

## <a name="next-steps"></a>Volgende stappen

Denkt u dat u een fout op het IoT Edge-platform hebt gevonden? [Dien een probleem](https://github.com/Azure/iotedge/issues) in zodat we kunnen blijven verbeteren.

Als u meer vragen hebt, maakt u een [ondersteuningsverzoek](https://portal.azure.com/#create/Microsoft.Support) voor hulp.
