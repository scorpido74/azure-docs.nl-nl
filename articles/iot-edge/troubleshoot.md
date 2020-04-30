---
title: Problemen oplossen-Azure IoT Edge | Microsoft Docs
description: In dit artikel vindt u informatie over de standaard diagnostische vaardig heden voor Azure IoT Edge, zoals het ophalen van onderdeel status en Logboeken, en het oplossen van veelvoorkomende problemen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2e15dffac73b4a50b1ef9288feaeb6073dea91e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086518"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Veelvoorkomende problemen en oplossingen voor Azure IoT Edge

Als u problemen hebt met het uitvoeren van Azure IoT Edge in uw omgeving, kunt u dit artikel als richtlijn gebruiken voor het oplossen van problemen.

## <a name="run-the-iotedge-check-command"></a>Voer de opdracht ' check ' iotedge uit

De eerste stap bij het oplossen van problemen met IoT Edge moet `check` de opdracht gebruiken, waarmee een verzameling configuratie-en connectiviteits tests voor veelvoorkomende problemen wordt uitgevoerd. De `check` opdracht is beschikbaar in [Release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) en hoger.

U kunt de `check` opdracht als volgt uitvoeren, of de `--help` vlag opnemen om een volledige lijst met opties weer te geven:

* Op Linux:

  ```bash
  sudo iotedge check
  ```

* In Windows:

  ```powershell
  iotedge check
  ```

Het hulp programma voor probleem oplossing voert veel controles uit die in de volgende drie categorieën zijn gesorteerd:

* Configuratie controles: Hiermee wordt informatie onderzocht waarmee kan worden voor komen dat edge-apparaten verbinding maken met de Cloud, met inbegrip van problemen met *config. yaml* en de container engine.
* Verbinding controleren: controleert of de IoT Edge runtime toegang heeft tot poorten op het hostapparaat en dat alle IoT Edge onderdelen verbinding kunnen maken met de IoT Hub.
* Productie gereedheids controles: er wordt gezocht naar aanbevolen productie best practices, zoals de status van certificaten voor de certificaat instantie van de apparaat en de configuratie van het logboek bestand van de module.

Zie [IOT Edge problemen met controles oplossen](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)voor meer informatie over elk van de diagnostische gegevens die door dit hulp programma worden uitgevoerd, waaronder wat u moet doen als er een fout of waarschuwing wordt weer gegeven.

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Informatie over fout opsporing verzamelen met de opdracht ' ondersteunings bundel ' van iotedge

Wanneer u logboeken van een IoT Edge apparaat wilt verzamelen, kunt u de `support-bundle` opdracht het beste gebruiken. Deze opdracht verzamelt standaard module, IoT Edge Security Manager en container engine-logboeken, ' iotedge check ' JSON output en andere nuttige informatie over fout opsporing. Ze worden gecomprimeerd tot één bestand, zodat het eenvoudig kan worden gedeeld. De `support-bundle` opdracht is beschikbaar in [Release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) en hoger.

Voer de `support-bundle` opdracht uit met `--since` de vlag om op te geven hoelang van het verleden u logboeken wilt ophalen. U krijgt `6h` bijvoorbeeld Logboeken sinds de laatste zes uur, `6d` sinds de laatste zes dagen sinds `6m` de laatste 6 minuten, enzovoort. Neem de `--help` vlag op om een volledige lijst met opties weer te geven.

* Op Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* In Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> De uitvoer van `support-bundle` de opdracht kan de namen van host, apparaat en module bevatten, informatie die is vastgelegd door uw modules, enzovoort. Houd rekening met het volgende als u de uitvoer in een openbaar forum deelt.

## <a name="standard-diagnostic-steps"></a>Standaard diagnostische stappen

Als u een probleem ondervindt, kunt u meer informatie over de status van uw IoT Edge-apparaat bekijken door de container logboeken en de berichten die door en van het apparaat worden door gegeven. Gebruik de opdrachten en hulpprogramma's in deze sectie om informatie te verzamelen.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Controleer de status van de IoT Edge Security Manager en de bijbehorende logboeken

Op Linux:

* De status van de IoT Edge Security Manager weer geven:

   ```bash
   sudo systemctl status iotedge
   ```

* De logboeken van de IoT Edge Security Manager weer geven:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Meer gedetailleerde logboeken van de IoT Edge Security Manager weer geven:

  * Bewerk de iotedge-daemon-instellingen:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Werk de volgende regels bij:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Start de IoT Edge-beveiligings-daemon opnieuw op:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

In Windows:

* De status van de IoT Edge Security Manager weer geven:

   ```powershell
   Get-Service iotedge
   ```

* De logboeken van de IoT Edge Security Manager weer geven:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Als IoT Edge Security Manager niet wordt uitgevoerd, controleert u het configuratie bestand van uw yaml

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

### <a name="check-container-logs-for-issues"></a>Raadpleeg container logboeken voor problemen

Zodra de IoT Edge Security daemon wordt uitgevoerd, bekijkt u de logboeken van de containers om problemen te detecteren. Begin met uw geïmplementeerde containers en bekijk vervolgens de containers waaruit de IoT Edge runtime: edgeAgent en edgeHub. De logboeken van de IoT Edge-agent bevatten doorgaans informatie over de levens cyclus van elke container. De IoT Edge hub-logboeken bieden informatie over berichten en route ring.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>De berichten weer geven die via de IoT Edge hub worden verzonden

U kunt de berichten van de IoT Edge hub bekijken en inzichten verzamelen uit uitgebreide logboeken van de runtime-containers. Als u uitgebreide logboeken op deze containers wilt inschakelen, `RuntimeLogLevel` stelt u in het configuratie bestand van uw yaml in. Het bestand openen:

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

Vervangen `env: {}` door:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-bestanden kunnen geen tabs bevatten als identation. Gebruik in plaats daarvan 2 spaties. Items op het hoogste niveau kunnen geen voorloop spaties bevatten.

Sla het bestand op en start het IoT Edge Security Manager opnieuw.

U kunt ook de berichten controleren die worden verzonden tussen IoT Hub en de IoT Edge-apparaten. Bekijk deze berichten met behulp van de [Azure IOT hub-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Zie [het handige hulp programma bij het ontwikkelen met Azure IOT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)voor meer informatie.

### <a name="restart-containers"></a>Containers opnieuw opstarten

Nadat u de logboeken en berichten voor informatie hebt onderzocht, kunt u containers opnieuw starten:

```cmd
iotedge restart <container name>
```

Start de IoT Edge runtime-containers opnieuw op:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Start IoT Edge Security Manager opnieuw

Als er nog steeds een probleem blijft bestaan, kunt u proberen om de IoT Edge Security Manager opnieuw op te starten.

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

**Hoofdoorzaak**

Een netwerk configuratie op het doelnet werk verhindert dat de IoT Edge agent het netwerk bereikt. De agent probeert eerst verbinding maken via AMQP (poort 5671). Als de verbinding is mislukt, probeert de websockets (poort 443).

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

**Hoofdoorzaak**

Poort 443 is bezig met een ander proces op de hostmachine. De IoT Edge hub wijst poorten 5671 en 443 toe voor gebruik in Gateway scenario's. Deze poorttoewijzing mislukt als er al een ander proces bezig is op deze poort.

**Afsluiting**

Zoek en stop het proces dat poort 443 gebruikt. Dit proces is meestal een webserver.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agent heeft geen toegang tot de installatie kopie van een module (403)

Een container kan niet worden uitgevoerd, en in de edgeAgent-Logboeken wordt een 403-fout weer gegeven.

**Hoofdoorzaak**

De IoT Edge-agent heeft geen machtigingen voor toegang tot de installatie kopie van een module.

**Afsluiting**

Controleer of de register referenties correct zijn opgegeven in het implementatie manifest

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge Security daemon mislukt met een ongeldige hostnaam

De opdracht `sudo journalctl -u iotedge` mislukt en het volgende bericht wordt afgedrukt:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Hoofdoorzaak**

De runtime van IoT Edge kan alleen hostnamen ondersteunen die korter zijn dan 64 tekens. Fysieke machines hebben doorgaans geen lange hostnamen, maar het probleem komt vaker voor op een virtuele machine. De automatisch gegenereerde hostnamen voor virtuele Windows-machines die worden gehost in azure, met name meestal lang.

**Afsluiting**

Wanneer u deze fout ziet, kunt u deze oplossen door de DNS-naam van de virtuele machine te configureren en vervolgens de DNS-naam als hostnaam in te stellen in de installatie opdracht.

1. Ga in het Azure Portal naar de overzichts pagina van de virtuele machine.
2. Selecteer **configureren** onder DNS-naam. Als er al een DNS-naam is geconfigureerd op de virtuele machine, hoeft u geen nieuwe te configureren.

   ![De DNS-naam van de virtuele machine configureren](./media/troubleshoot/configure-dns.png)

3. Geef een waarde op voor het label voor de **DNS-naam** en selecteer **Opslaan**.
4. Kopieer de nieuwe DNS-naam met de indeling ** \<DNSnamelabel\>.\< vmlocation\>. cloudapp.Azure.com**.
5. Gebruik in de virtuele machine de volgende opdracht om de IoT Edge runtime in te stellen met uw DNS-naam:

   * Op Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * In Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Stabiliteits problemen op apparaten die zijn beperkt

Er kunnen stabiliteits problemen optreden op beperkte apparaten, zoals de Raspberry Pi, met name wanneer deze wordt gebruikt als een gateway. Voor beelden van problemen met uitzonde ringen in de Edge hub-module kunnen downstream-apparaten geen verbinding maken of stopt het apparaat het verzenden van telemetrie-berichten na enkele uren.

**Hoofdoorzaak**

De IoT Edge hub, die deel uitmaakt van de IoT Edge runtime, is standaard geoptimaliseerd voor prestaties en probeert grote delen van geheugen toe te wijzen. Deze optimalisatie is niet ideaal voor beperkte edge-apparaten en kan stabiliteits problemen veroorzaken.

**Afsluiting**

Stel voor de IoT Edge hub een omgevings variabele **OptimizeForPerformance** in op **False**. Er zijn twee manieren om omgevings variabelen in te stellen:

In Azure Portal:

Selecteer in uw IOT hub uw IOT edge-apparaat en op de pagina Details van apparaat en selecteer > **runtime-instellingen**voor **modules instellen**. Maak een omgevings variabele voor de Edge hub-module met de naam *OptimizeForPerformance* die is ingesteld op *False*.

![OptimizeForPerformance ingesteld op False](./media/troubleshoot/optimizeforperformance-false.png)

**OF**

In het implementatie manifest:

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

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Kan de IoT Edge daemon-logboeken niet ophalen in Windows

Als u een EventLogException krijgt wanneer u `Get-WinEvent` op Windows gebruikt, controleert u de Register vermeldingen.

**Hoofdoorzaak**

De `Get-WinEvent` Power shell-opdracht is afhankelijk van een register vermelding die aanwezig moet zijn om logboeken `ProviderName`te zoeken op basis van een specifieke.

**Afsluiting**

Stel een register vermelding voor de IoT Edge-daemon in. Maak een **iotedge. reg** -bestand met de volgende inhoud en importeer het in het Windows-REGI ster door erop te dubbel klikken of `reg import iotedge.reg` door de opdracht te gebruiken:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge-module kan geen bericht verzenden naar de edgeHub met de fout 404

Een aangepaste IoT Edge module kan geen bericht verzenden naar de edgeHub met een 404 `Module not found` -fout. De IoT Edge-daemon drukt het volgende bericht in de logboeken af:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Hoofdoorzaak**

De IoT Edge-daemon dwingt proces identificatie af voor alle modules die om veiligheids redenen verbinding maken met de edgeHub. Hiermee wordt gecontroleerd of alle berichten die worden verzonden door een module afkomstig zijn van de hoofd proces-ID van de module. Als een bericht wordt verzonden door een module van een andere proces-ID dan oorspronkelijk tot stand is gebracht, wordt het bericht met een 404-fout bericht geweigerd.

**Afsluiting**

Vanaf versie 1.0.7 zijn alle module processen gemachtigd om verbinding te maken. Als u een upgrade naar 1.0.7 niet mogelijk hebt, voert u de volgende stappen uit. Zie [1.0.7 release wijzigingen logboek](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)voor meer informatie.

Zorg ervoor dat dezelfde proces-ID altijd wordt gebruikt door de aangepaste IoT Edge module om berichten naar de edgeHub te verzenden. Zorg er bijvoorbeeld voor dat u `ENTRYPOINT` in het `CMD` docker-bestand in plaats van de `CMD` opdracht gebruikt, omdat het één proces-id voor de module heeft en een andere proces-id voor de bash `ENTRYPOINT` opdracht die het hoofd programma uitvoert. Dit leidt tot één proces-id.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Firewall-en poort configuratie regels voor de implementatie van IoT Edge

Zie [een communicatie protocol kiezen](../iot-hub/iot-hub-devguide-protocols.md)Azure IOT Edge communicatie van een on-premises server naar Azure-Cloud met ondersteunde IOT hub protocollen toestaat. Voor een betere beveiliging worden communicatie kanalen tussen Azure IoT Edge en Azure IoT Hub altijd geconfigureerd als uitgaand verkeer. Deze configuratie is gebaseerd op het [service-ondersteunde communicatie patroon](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), waarmee de kwets baarheid voor een kwaadwillende entiteit wordt geminimaliseerd. Inkomende communicatie is alleen vereist voor specifieke scenario's waarbij Azure-IoT Hub berichten naar het Azure IoT Edge apparaat moet pushen. Cloud-naar-apparaat-berichten worden beveiligd met behulp van beveiligde TLS-kanalen en kunnen verder worden beveiligd met X. 509-certificaten en TPM-apparaat modules. De Azure IoT Edge Security Manager bepaalt hoe deze communicatie tot stand kan worden gebracht. Zie [IOT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Hoewel IoT Edge een verbeterde configuratie biedt voor het beveiligen van Azure IoT Edge runtime en geïmplementeerde modules, is het nog steeds afhankelijk van de onderliggende computer-en netwerk configuratie. Daarom is het van cruciaal belang om ervoor te zorgen dat de juiste netwerk-en firewall regels worden ingesteld voor beveiligde Edge-to-Cloud communicatie. De volgende tabel kan worden gebruikt als richt lijn bij de configuratie van firewall regels voor de onderliggende servers waarop Azure IoT Edge runtime wordt gehost:

|Protocol|Poort|Binnenkomende|Verzendt|Richtlijnen|
|--|--|--|--|--|
|MQTT|8883|GEBLOKKEERD (standaard)|GEBLOKKEERD (standaard)|<ul> <li>Configureer uitgaand (uitgaand) dat moet worden geopend wanneer MQTT wordt gebruikt als communicatie protocol.<li>1883 voor MQTT wordt niet ondersteund door IoT Edge. <li>Binnenkomende (binnenkomende) verbindingen moeten worden geblokkeerd.</ul>|
|AMQP|5671|GEBLOKKEERD (standaard)|OPEN (standaard)|<ul> <li>Het standaard communicatie protocol voor IoT Edge. <li> Moet worden geconfigureerd om te worden geopend als Azure IoT Edge niet is geconfigureerd voor andere ondersteunde protocollen of als AMQP het gewenste communicatie protocol is.<li>5672 voor AMQP wordt niet ondersteund door IoT Edge.<li>Deze poort blok keren wanneer Azure IoT Edge gebruikmaakt van een ander IoT Hub ondersteund protocol.<li>Binnenkomende (binnenkomende) verbindingen moeten worden geblokkeerd.</ul></ul>|
|HTTPS|443|GEBLOKKEERD (standaard)|OPEN (standaard)|<ul> <li>Configureer uitgaand (uitgaand) dat moet worden geopend op 443 voor het inrichten van IoT Edge. Deze configuratie is vereist wanneer u hand matige scripts of een Azure IoT Device Provisioning Service (DPS) gebruikt. <li>Binnenkomende (binnenkomende) verbinding moet alleen worden geopend voor specifieke scenario's: <ul> <li>  Als u een transparante gateway hebt met Leaf-apparaten die methode aanvragen kunnen verzenden. In dit geval hoeft u poort 443 niet te openen voor externe netwerken om verbinding te maken met IoTHub of IoTHub-services te bieden via Azure IoT Edge. Daarom kan de binnenkomende regel worden beperkt tot het openen van binnenkomende (binnenkomende) vanuit het interne netwerk. <li> Voor C2D-scenario's (client to Device).</ul><li>80 voor HTTP wordt niet ondersteund door IoT Edge.<li>Als niet-HTTP-protocollen (bijvoorbeeld AMQP of MQTT) niet kunnen worden geconfigureerd in de onderneming; de berichten kunnen via websockets worden verzonden. Poort 443 wordt in dat geval gebruikt voor WebSocket-communicatie.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>De Edge Agent-module rapporteert voortdurend het lege configuratie bestand en er worden geen modules gestart op het apparaat

Het apparaat heeft geen problemen met het starten van modules die in de implementatie zijn gedefinieerd. Alleen de edgeAgent wordt uitgevoerd, maar doorlopend een leeg configuratie bestand...

**Hoofdoorzaak**

Standaard worden modules IoT Edge in hun eigen geïsoleerde container netwerk gestart. Het apparaat heeft mogelijk problemen met de DNS-naam omzetting binnen dit particuliere netwerk.

**Afsluiting**

**Optie 1: DNS-server instellen in container Engine-instellingen**

Geef de DNS-server voor uw omgeving op in de instellingen van de container-engine, die van toepassing is op alle container modules die zijn gestart door de engine. Maak een bestand met `daemon.json` de naam en geef de DNS-server op die moet worden gebruikt. Bijvoorbeeld:

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

Als de locatie al een `daemon.json` bestand bevat, voegt u de **DNS-** sleutel hieraan toe en slaat u het bestand op.

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
