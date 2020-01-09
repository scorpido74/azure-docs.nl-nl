---
title: Bereid u voor op het implementeren van uw oplossing in productie-Azure IoT Edge
description: Meer informatie over hoe u uw Azure IoT Edge oplossing van ontwikkeling tot productie kunt nemen, inclusief het instellen van uw apparaten met de juiste certificaten en het maken van een implementatie plan voor toekomstige code-updates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1213ab4c623cb1d475dff1d71e65439b1d08f5c1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429435"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Voorbereidingen voor het implementeren van uw IoT Edge-oplossing in productie

Wanneer u klaar zijn bent voor uw IoT Edge-oplossing van ontwikkeling naar productie, zorg ervoor dat deze geconfigureerd voor continue uitvoering.

De informatie in dit artikel is niet allemaal gelijk. Als u wilt, kunt u prioriteiten elke sectie begint met lijsten die het werk te in twee secties verdelen: **belangrijk** om te voltooien voordat u doorgaat naar de productie, of **nuttig** voor u.

## <a name="device-configuration"></a>Apparaatconfiguratie

IoT Edge-apparaten kunnen van alles uit een Raspberry Pi tot een laptop met een virtuele machine op een server zijn. U hebt mogelijk toegang tot het apparaat fysiek of via een virtuele-verbinding of deze kan worden geïsoleerd gedurende langere tijd wordt opgelost. In beide gevallen moet u er zeker van zijn dat deze zo zijn geconfigureerd dat ze correct werken. 

* **Belangrijk**
    * Productiecertificaten installeren
    * Plan voor een apparaat hebt
    * Moby gebruiken als de container-engine

* **Nuttige**
    * Kies upstream protocol

### <a name="install-production-certificates"></a>Productiecertificaten installeren

Elke IoT Edge-apparaat in de productieomgeving moet een certificaat apparaten certificaat certificeringsinstantie (CA) is geïnstalleerd. Deze CA-certificaat is vervolgens naar de IoT Edge-runtime in het bestand config.yaml gedeclareerd. Als u wilt maken, ontwikkelen en testen vereenvoudigen, de IoT Edge maakt runtime tijdelijke certificaten als er geen certificaten zijn gedefinieerd in het bestand config.yaml. Deze tijdelijke certificaten zijn echter verloopt na drie maanden en niet is beveiligd voor productiescenario's. 

Zie voor meer informatie over de rol van het CA-apparaatcertificaat, [hoe Azure IoT Edge maakt gebruik van certificaten](iot-edge-certs.md).

Zie [Production certificaten installeren op een IOT edge-apparaat](how-to-create-transparent-gateway.md)voor meer informatie over het installeren van certificaten op een IOT edge apparaat en om ernaar te verwijzen vanuit het bestand config. yaml. 

### <a name="have-a-device-management-plan"></a>Plan voor een apparaat hebt

Voordat u een apparaat in de productieomgeving moet u weten hoe u van plan bent om toekomstige updates te beheren. Voor een IoT Edge-apparaat, de lijst van onderdelen om bij te werken kan omvatten:

* Apparaatfirmware
* Besturingssysteem-bibliotheken
* Container-engine, zoals Moby
* IoT Edge-daemon
* CA-certificaten

Zie [de IOT Edge runtime bijwerken](how-to-update-iot-edge.md)voor meer informatie. De huidige methoden voor het bijwerken van de IoT Edge-daemon is vereist voor fysieke of SSH-toegang tot de IoT Edge-apparaat. Als u veel apparaten wilt bijwerken, kunt u overwegen om de update stappen toe te voegen aan een script of een automatiserings programma zoals Ansible te gebruiken.

### <a name="use-moby-as-the-container-engine"></a>Moby gebruiken als de container-engine

Een container engine is een vereiste voor een IoT Edge apparaat. Alleen moby-engine wordt ondersteund in de productieomgeving. Andere container-engines, zoals Docker, werken met IoT Edge en het is om deze engines gebruiken voor ontwikkeling. De moby-engine kan opnieuw worden gedistribueerd, gebruikt in combinatie met Azure IoT Edge en Microsoft biedt onderhoud van deze engine.

### <a name="choose-upstream-protocol"></a>Kies upstream protocol

Het Protocol (en dus de poort die wordt gebruikt) voor de upstream-communicatie met IoT Hub kan worden geconfigureerd voor zowel de IoT Edge agent als de IoT Edge hub. Het standaard-protocol is AMQP, maar u mogelijk wilt wijzigen, afhankelijk van uw netwerkinstellingen. 

De twee runtimemodules beide hebben een **UpstreamProtocol** omgevingsvariabele. De geldige waarden voor de variabele zijn: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configureer de variabele UpstreamProtocol voor de IoT Edge agent in het bestand config. yaml op het apparaat zelf. Als uw IoT Edge-apparaat bijvoorbeeld zich achter een proxy server bevindt die AMQP poorten blokkeert, moet u mogelijk de IoT Edge agent configureren voor het gebruik van AMQP via WebSocket (AMQPWS) om de eerste verbinding met IoT Hub tot stand te brengen. 

Zodra uw IoT Edge-apparaat verbinding maakt, moet u gaan met het configureren van de variabele UpstreamProtocol voor beide runtimemodules in latere implementaties. Een voorbeeld van dit proces vindt u in [een IoT Edge-apparaat om te communiceren via een proxyserver configureren](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Implementatie

* **Nuttige**
    * Consistent zijn met upstream-protocol
    * Host Storage instellen voor systeem modules
    * De geheugen ruimte beperken die wordt gebruikt door de IoT Edge hub
    * Gebruik geen foutopsporingsversies van module afbeeldingen

### <a name="be-consistent-with-upstream-protocol"></a>Consistent zijn met upstream-protocol

Als u de IoT Edge-agent op uw IoT Edge apparaat zodanig hebt geconfigureerd dat er een ander protocol dan de standaard AMQP wordt gebruikt, moet u hetzelfde protocol in alle toekomstige implementaties declareren. Bijvoorbeeld, als uw IoT Edge-apparaat zich achter een proxyserver die blokkeert AMQP-poorten, u waarschijnlijk geconfigureerd het apparaat verbinding maakt via AMQP via WebSocket (AMQPWS). Wanneer u modules op het apparaat implementeert, configureert u hetzelfde AMQPWS-protocol voor de IoT Edge agent en IoT Edge hub, anders worden de instellingen door de standaard-AMQP overschreven en wordt voor komen dat u weer verbinding maakt. 

U hoeft alleen de omgevings variabele UpstreamProtocol te configureren voor de IoT Edge agent en IoT Edge hub-modules. Alle aanvullende modules vast welke protocol is ingesteld in de runtimemodules. 

Een voorbeeld van dit proces vindt u in [een IoT Edge-apparaat om te communiceren via een proxyserver configureren](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Host Storage instellen voor systeem modules

De modules IoT Edge hub en agent gebruiken lokale opslag voor het onderhouden van de status en het inschakelen van berichten tussen modules, apparaten en de Cloud. Voor betere betrouw baarheid en prestaties moet u de systeem modules configureren voor het gebruik van opslag op het host-bestands systeem.

Zie [opslag van hosts voor systeem modules](how-to-access-host-storage-from-module.md)voor meer informatie.

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>De geheugen ruimte die wordt gebruikt door IoT Edge hub beperken

Als u beperkte apparaten implementeert waarvoor beperkt geheugen beschikbaar is, kunt u IoT Edge hub configureren zodat deze wordt uitgevoerd in een meer gestroomlijnde capaciteit en minder schijf ruimte gebruiken. Deze configuraties begrenzen echter de prestaties van de IoT Edge hub, dus zoek het juiste saldo dat geschikt is voor uw oplossing. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Geen optimale prestaties op beperkte apparaten

De IoT Edge hub is standaard geoptimaliseerd voor prestaties, zodat er grote delen van het geheugen worden toegewezen. Deze configuratie kan stabiliteitsproblemen veroorzaken op kleinere apparaten, zoals de Raspberry Pi. Als u apparaten met beperkte resources implementeert, kunt u de omgevings variabele **OptimizeForPerformance** instellen op **false** in de IOT Edge hub. 

Wanneer **OptimizeForPerformance** is ingesteld op **True**, gebruikt de MQTT-protocol kop de PooledByteBufferAllocator met betere prestaties, maar wordt er meer geheugen toegewezen. De allocator werkt niet goed op 32-bits besturings systemen of op apparaten met weinig geheugen. Daarnaast wijst RocksDb, wanneer deze is geoptimaliseerd voor prestaties, meer geheugen toe voor de rol van de lokale opslag provider. 

Zie voor meer informatie, [stabiliteitsproblemen voor de resource beperkte apparaten](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Niet-gebruikte protocollen uitschakelen

Een andere manier om de prestaties van de IoT Edge hub te optimaliseren en het geheugen gebruik te verminderen, is om de protocol koppen uit te scha kelen voor protocollen die u niet gebruikt in uw oplossing. 

Protocol koppen worden geconfigureerd door Boole-omgevings variabelen in te stellen voor de module IoT Edge hub in uw implementatie manifesten. Zijn de drie variabelen:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Alle drie variabelen hebben *twee onderstrepingstekens* en kan worden ingesteld op waar of ONWAAR. 

#### <a name="reduce-storage-time-for-messages"></a>Minder opslagtijd van berichten

De IoT Edge hub-module slaat berichten tijdelijk op als deze om een of andere reden niet aan IoT Hub worden geleverd. U kunt configureren hoe lang de IoT Edge hub op niet-bezorgde berichten vasthoudt voordat ze verlopen. Als er geheugen problemen zijn op uw apparaat, kunt u de **timeToLiveSecs** -waarde verlagen in de IOT Edge hub-module dubbele. 

De standaardwaarde van de parameter timeToLiveSecs is 7200 seconden, die twee uur is. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Gebruik geen foutopsporingsversies van module afbeeldingen

Bij het verplaatsen van testscenario's naar productie scenario's, moet u foutopsporing configuraties van de manifesten implementatie verwijderen. Controleer of geen van de module-installatiekopieën in de manifesten implementatie hebben de  **\.debug** achtervoegsel. Als u opties om de poorten in de modules voor foutopsporing, beschikbaar te maken hebt toegevoegd en opties voor verwijderen die zijn gemaakt. 

## <a name="container-management"></a>Containerbeheer

* **Belangrijk**
    * Toegang tot uw containerregister beheren
    * Tags gebruiken voor het beheren van versies

### <a name="manage-access-to-your-container-registry"></a>Toegang tot uw containerregister beheren

Voordat u modules naar productie IoT Edge-apparaten implementeert, controleert u of toegang tot uw container registry te beheren zodat andere gebruikers geen toegang tot of wijzigingen in uw containerinstallatiekopieën aanbrengen. Een persoonlijke, niet-openbare container registry gebruiken voor het beheren van containerinstallatiekopieën. 

In de zelfstudies en andere documentatie vertelt u dezelfde container registerreferenties op uw IoT Edge-apparaat gebruiken als u op uw ontwikkelcomputer. Deze instructies zijn alleen bedoeld om u te helpen bij het instellen van test- en ontwikkelomgevingen eenvoudiger en niet in een productiescenario voor moeten worden gevolgd. Azure Container Registry raadt [verificatie met service-principals](../container-registry/container-registry-auth-service-principal.md) als toepassingen of services Haal containerinstallatiekopieën op in een geautomatiseerde of anderszins onbeheerd, zoals IoT Edge-apparaten. Een service-principal maken met alleen-lezen toegang tot uw containerregister, en die gebruikersnaam opgeven en het wachtwoord in het manifest van de implementatie.

### <a name="use-tags-to-manage-versions"></a>Tags gebruiken voor het beheren van versies

Een tag is een docker-concept dat u kunt gebruiken om onderscheid te maken tussen versies van docker-containers. Tags zijn achtervoegsels, zoals **1.0** die gaat u aan het einde van de containeropslagplaats van een. Bijvoorbeeld, **mcr.microsoft.com/azureiotedge-agent:1.0**. Tags zijn veranderlijke en kunnen worden gewijzigd om te verwijzen naar een andere container op elk gewenst moment, zodat uw team op een overeenkomst te volgen bij het bijwerken van uw module installatiekopieën vooruit moet overeenkomen. 

Labels helpen u om af te dwingen van updates op uw IoT Edge-apparaten. Wanneer u een bijgewerkte versie van een module naar het containerregister hebt gepusht, verhogen de tag. Push vervolgens een nieuwe implementatie naar uw apparaten met de tag verhoogd. De container-engine herkent de tag verhoogd als een nieuwe versie en de meest recente moduleversie naar uw apparaat wordt opgehaald. 

Zie voor een voorbeeld van een overeenkomst tag [bijwerken van de IoT Edge-runtime](how-to-update-iot-edge.md#understand-iot-edge-tags) voor meer informatie over hoe IoT Edge rolling tags en specifieke tags gebruikt voor het bijhouden van versies. 

## <a name="networking"></a>Networking

* **Nuttige**
    * Uitgaand/inkomende configuratie controleren
    * Verbindingen van IoT Edge apparaten toestaan
    * Communicatie via een proxy configureren

### <a name="review-outboundinbound-configuration"></a>Uitgaand/inkomende configuratie controleren

Communicatiekanalen tussen Azure IoT Hub en IoT Edge worden altijd geconfigureerd om uitgaande. Voor de meeste scenario's voor IoT Edge zijn alleen voor de drie verbindingen nodig. De container-engine moet verbinding maken met het containerregister (of registers) waarin de module-installatiekopieën. IoT Edge-runtime moet verbinding maken met IoT Hub voor het ophalen van informatie over de configuratie van apparaten en voor het verzenden van berichten en telemetrie. En als u automatische inrichting, het IoT Edge-daemon moet verbinding maken met de Device Provisioning Service. Zie voor meer informatie, [Firewall en configuratieregels poort](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Verbindingen van IoT Edge apparaten toestaan

Als uw netwerk installatie vereist dat u verbindingen die zijn gemaakt vanaf IoT Edge apparaten expliciet toestaat, raadpleegt u de volgende lijst met IoT Edge onderdelen:

* **IoT Edge agent** een permanente verbinding via AMQP/MQTT naar IoT-Hub, mogelijk wordt geopend via WebSockets. 
* **IoT Edge hub** mogelijk meerdere MQTT-verbindingen naar IoT Hub, of één permanente AMQP-verbinding opent via WebSockets. 
* **IoT Edge-daemon** doet onregelmatige HTTPS-aanroepen naar IoT Hub. 

In alle drie gevallen moet de DNS-naam kan overeenkomen met het patroon \*.azure-devices.net. 

Bovendien de **Container engine** containerregisters opgeroepen via HTTPS. Als u wilt de containerinstallatiekopieën van de IoT Edge-runtime ophalen, is de DNS-naam mcr.microsoft.com. De container-engine maakt verbinding met andere registers zoals geconfigureerd in de implementatie. 

Deze controle lijst is een start punt voor firewall regels:

   | URL (\* = Joker teken) | Uitgaande TCP-poorten | Gebruik |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Micro soft container Registry |
   | global.azure-devices-provisioning.net  | 443 | DPS-toegang (optioneel) |
   | \*.azurecr.io | 443 | Persoonlijke en container registers van derden |
   | \*.blob.core.windows.net | 443 | Azure Container Registry afbeeldings verschillen van Blob-opslag downloaden  | 
   | \*.azure-devices.net | 5671, 8883, 443 | Toegang IoT Hub |
   | \*.docker.io  | 443 | Toegang voor docker hub (optioneel) |

Sommige van deze firewall regels worden overgenomen van Azure Container Registry. Zie [regels configureren voor toegang tot een Azure container Registry achter een firewall](../container-registry/container-registry-firewall-access-rules.md)voor meer informatie.

### <a name="configure-communication-through-a-proxy"></a>Communicatie via een proxy configureren

Als uw apparaten worden geïmplementeerd op een netwerk dat gebruikmaakt van een proxyserver, moeten ze kunnen communiceren via de proxy om IoT-Hub en containerregisters te bereiken. Zie voor meer informatie, [een IoT Edge-apparaat om te communiceren via een proxyserver configureren](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Voor oplossingsbeheer

* **Nuttige**
    * Instellen van Logboeken en diagnostische gegevens
    * U kunt tests en CI/CD-pijplijnen

### <a name="set-up-logs-and-diagnostics"></a>Instellen van Logboeken en diagnostische gegevens

In Linux gebruikt de IoT Edge-daemon journalen als het standaard stuur programma voor logboek registratie. U kunt het opdrachtregelhulpprogramma `journalctl` query uitvoeren op de daemon-Logboeken. Op Windows gebruikt de IoT Edge-daemon PowerShell diagnostische gegevens. Gebruik `Get-IoTEdgeLog` naar Logboeken vanuit de daemon voor query's. IoT Edge-modules gebruiken het JSON-stuur programma voor logboek registratie. Dit is de standaard instelling.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Wanneer u een IoT Edge-implementatie test, kunt u uw apparaten op te halen van Logboeken en oplossen van meestal openen. In een implementatiescenario, hebt u mogelijk geen die optie. Houd rekening met hoe u gegevens verzamelen over uw apparaten in productie gaat. Een optie is het gebruik van een logboekregistratiemodule die u verzamelt gegevens van de andere modules en verzendt dit naar de cloud. Een voorbeeld van een logboekregistratiemodule is [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics), of u kunt ze zelf kunt ontwerpen. 

### <a name="place-limits-on-log-size"></a>Limieten voor logboek grootte

De Moby-container engine stelt standaard de limieten voor de container logboek grootte niet in. Na verloop van tijd kan dit ertoe leiden dat het apparaat wordt opgevuld met Logboeken en er onvoldoende schijf ruimte beschikbaar is. Houd rekening met de volgende opties om dit te voor komen:

**Optie: globale limieten instellen die van toepassing zijn op alle container modules**

U kunt de grootte van alle container logboeken beperken in de logboek opties van de container-engine. In het volgende voor beeld wordt het stuur programma van het logboek ingesteld op `json-file` (aanbevolen) met limieten voor grootte en aantal bestanden:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Voeg deze gegevens toe aan een bestand met de naam `daemon.json` en plaats het op de juiste locatie voor het platform van uw apparaat.

| Platform | Locatie |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

De container engine moet opnieuw worden gestart om de wijzigingen van kracht te laten worden.

**Optie: logboek instellingen voor elke container module aanpassen**

U kunt dit doen in de **createOptions** van elke module. Bijvoorbeeld:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

**Aanvullende opties op Linux-systemen**

* Configureer de container-engine voor het verzenden van logboeken naar `systemd` [logboek](https://docs.docker.com/config/containers/logging/journald/) door `journald` in te stellen als het standaard stuur programma voor logboek registratie. 

* Verwijder regel matig oude logboeken van uw apparaat door een logrotate-hulp programma te installeren. Gebruik de specificatie van het volgende bestand: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>U kunt tests en CI/CD-pijplijnen

Voor de meest efficiënte implementatiescenario van de IoT Edge kunt u uw productie-implementatie integreren in uw test- en CI/CD-pijplijnen. Azure IoT Edge biedt ondersteuning voor meerdere CI/CD-platformen, waaronder Azure DevOps. Zie voor meer informatie, [continue integratie en continue implementatie voor Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [automatische implementatie van IoT Edge](module-deployment-monitoring.md).
* Zie hoe IoT Edge ondersteunt [continue integratie en continue implementatie](how-to-ci-cd.md).
