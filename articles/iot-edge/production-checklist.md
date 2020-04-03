---
title: Bereid u voor om uw oplossing in productie te implementeren - Azure IoT Edge
description: Lees hoe u uw Azure IoT Edge-oplossing van ontwikkeling naar productie brengen, inclusief het instellen van uw apparaten met de juiste certificaten en het maken van een implementatieplan voor toekomstige code-updates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: dd24631f8e6b4f3f87438bf22654016dd7699950
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618306"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Bereid u voor om uw IoT Edge-oplossing in productie te implementeren

Wanneer u klaar bent om uw IoT Edge-oplossing van ontwikkeling naar productie te brengen, moet u ervoor zorgen dat deze is geconfigureerd voor doorlopende prestaties.

De informatie in dit artikel is niet allemaal gelijk. Om u te helpen prioriteiten te stellen, begint elke sectie met lijsten die het werk in twee secties verdelen: **belangrijk** om te voltooien voordat u naar de productie gaat, of **nuttig** voor u om te weten.

## <a name="device-configuration"></a>Apparaatconfiguratie

IoT Edge-apparaten kunnen van alles zijn, van een Raspberry Pi tot een laptop tot een virtuele machine die op een server draait. U hebt mogelijk fysiek of via een virtuele verbinding toegang tot het apparaat, of het kan voor langere tijd worden geïsoleerd. Hoe dan ook, u wilt ervoor zorgen dat het is geconfigureerd om op de juiste manier te werken.

* **Belangrijk**
  * Productiecertificaten installeren
  * Een apparaatbeheerplan hebben
  * Moby gebruiken als containermotor

* **Nuttig**
  * Kies upstream-protocol

### <a name="install-production-certificates"></a>Productiecertificaten installeren

Elk IoT Edge-apparaat in productie heeft een CA-certificaat (Device Certificate Authority Authority) nodig. Dat CA-certificaat wordt vervolgens gedeclareerd naar de Runtime van IoT Edge in het config.yaml-bestand. Voor ontwikkel- en testscenario's maakt de IoT Edge-runtime tijdelijke certificaten als er geen certificaten worden gedeclareerd in het config.yaml-bestand. Deze tijdelijke certificaten verlopen echter na drie maanden en zijn niet veilig voor productiescenario's.

Zie [Hoe Azure IoT Edge certificaten gebruikt](iot-edge-certs.md)om inzicht te krijgen in de rol van het CA-certificaat van het apparaat.

Zie [Productiecertificaten installeren op een IoT Edge-apparaat](how-to-manage-device-certificates.md)voor meer informatie over het installeren van certificaten op een IoT Edge-apparaat en deze te bekijken vanuit het config.yaml-bestand.

### <a name="have-a-device-management-plan"></a>Een apparaatbeheerplan hebben

Voordat u een apparaat in productie neemt, moet u weten hoe u toekomstige updates gaat beheren. Voor een IoT Edge-apparaat kan de lijst met onderdelen die moeten worden bijgewerkt:

* Firmware van apparaat
* Besturingssysteembibliotheken
* Containermotor, zoals Moby
* IoT Edge daemon
* CA-certificaten

Zie [De runtime van IoT Edge bijwerken](how-to-update-iot-edge.md)voor meer informatie. De huidige methoden voor het bijwerken van de IoT Edge-daemon vereisen fysieke of SSH-toegang tot het IoT Edge-apparaat. Als u veel apparaten moet bijwerken, u overwegen de updatestappen toe te voegen aan een script of een automatiseringstool zoals Ansible te gebruiken.

### <a name="use-moby-as-the-container-engine"></a>Moby gebruiken als containermotor

Een containermotor is een voorwaarde voor elk IoT Edge-apparaat. Alleen moby-engine wordt ondersteund in de productie. Andere containermotoren, zoals Docker, werken met IoT Edge en het is ok om deze motoren te gebruiken voor ontwikkeling. De moby-engine kan worden herverdeeld wanneer deze wordt gebruikt met Azure IoT Edge en Microsoft biedt onderhoud voor deze engine.

### <a name="choose-upstream-protocol"></a>Kies upstream-protocol

U het protocol (dat de gebruikte poort bepaalt) configureren voor upstreamcommunicatie naar IoT Hub voor zowel de IoT Edge-agent als de IoT Edge-hub. Het standaardprotocol is AMQP, maar u dat wijzigen, afhankelijk van uw netwerkinstelling.

De twee runtime modules hebben beide een **UpstreamProtocol** omgevingsvariabele. De geldige waarden voor de variabele zijn:

* MQTT
* AMQP
* MQTTWS MQTTWS
* AMQPWS AMQPWS

Configureer de variabele UpstreamProtocol voor de IoT Edge-agent in het bestand config.yaml op het apparaat zelf. Als uw IoT Edge-apparaat zich bijvoorbeeld achter een proxyserver bevindt die AMQP-poorten blokkeert, moet u mogelijk de IoT Edge-agent configureren om AMQP via WebSocket (AMQPWS) te gebruiken om de eerste verbinding met IoT Hub tot stand te brengen.

Zodra uw IoT Edge-apparaat verbinding maakt, moet u de UpstreamProtocol-variabele voor beide runtime-modules blijven configureren in toekomstige implementaties. Een voorbeeld van dit proces wordt aangeboden in [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver.](how-to-configure-proxy-support.md)

## <a name="deployment"></a>Implementatie

* **Nuttig**
  * In overeenstemming zijn met het upstream-protocol
  * Hostopslag instellen voor systeemmodules
  * Verminder de geheugenruimte die wordt gebruikt door de IoT Edge-hub
  * Foutopsporingsversies van moduleafbeeldingen niet gebruiken

### <a name="be-consistent-with-upstream-protocol"></a>In overeenstemming zijn met het upstream-protocol

Als u de IoT Edge-agent op uw IoT Edge-apparaat hebt geconfigureerd om een ander protocol te gebruiken dan de standaard AMQP, moet u hetzelfde protocol declareren in alle toekomstige implementaties. Als uw IoT Edge-apparaat zich bijvoorbeeld achter een proxyserver bevindt die AMQP-poorten blokkeert, hebt u het apparaat waarschijnlijk geconfigureerd om verbinding te maken via AMQP via WebSocket (AMQPWS). Wanneer u modules op het apparaat implementeert, configureert u hetzelfde AMQPWS-protocol voor de IoT Edge-agent en iot edge-hub, anders overschrijft de standaard AMQP de instellingen en voorkomt u opnieuw verbinding maken.

U hoeft alleen de upstreamprotocol-omgevingsvariabele te configureren voor de IoT Edge-agent en IoT Edge-hubmodules. Eventuele extra modules nemen elk protocol is ingesteld in de runtime modules.

Een voorbeeld van dit proces wordt aangeboden in [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver.](how-to-configure-proxy-support.md)

### <a name="set-up-host-storage-for-system-modules"></a>Hostopslag instellen voor systeemmodules

De IoT Edge-hub- en agentmodules gebruiken lokale opslag om de status te behouden en berichten tussen modules, apparaten en de cloud mogelijk te maken. Voor een betere betrouwbaarheid en prestaties configureert u de systeemmodules om opslag op het hostbestandssysteem te gebruiken.

Zie [Hoststorage voor systeemmodules voor](how-to-access-host-storage-from-module.md)meer informatie.

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Geheugenruimte verminderen die wordt gebruikt door de IoT Edge-hub

Als u beperkte apparaten met beperkt geheugen implementeert, u de IoT Edge-hub zo configureren dat deze in een meer gestroomlijnde capaciteit wordt uitgevoerd en minder schijfruimte gebruiken. Deze configuraties beperken echter wel de prestaties van de IoT Edge-hub, dus vind de juiste balans die werkt voor uw oplossing.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Optimaliseren voor prestaties op apparaten met beperkte beperking

De IoT Edge-hub is standaard geoptimaliseerd voor prestaties, dus probeert grote stukken geheugen toe te wijzen. Deze configuratie kan stabiliteitsproblemen veroorzaken op kleinere apparaten zoals de Raspberry Pi. Als u apparaten met beperkte resources implementeert, u de omgevingsvariabele **OptimizeForPerformance** op **false** instellen op de IoT Edge-hub.

Wanneer **OptimizeForPerformance** is ingesteld op **true,** gebruikt de MQTT-protocolkop de PooledByteBufferAllocator, die betere prestaties heeft, maar meer geheugen toewijst. De allocator werkt niet goed op 32-bits besturingssystemen of op apparaten met weinig geheugen. Bovendien, wanneer geoptimaliseerd voor prestaties, RocksDb wijst meer geheugen voor zijn rol als de lokale opslagprovider.

Zie [Stabiliteitsproblemen op apparaten met beperkte resources](troubleshoot.md#stability-issues-on-resource-constrained-devices)voor meer informatie .

#### <a name="disable-unused-protocols"></a>Ongebruikte protocollen uitschakelen

Een andere manier om de prestaties van de IoT Edge-hub te optimaliseren en het geheugengebruik ervan te verminderen, is door de protocolkoppen uit te schakelen voor protocollen die u niet gebruikt in uw oplossing.

Protocolkoppen worden geconfigureerd door booleaanse omgevingsvariabelen in te stellen voor de IoT Edge-hubmodule in uw implementatiemanifesten. De drie variabelen zijn:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Alle drie variabelen hebben *twee underscores* en kunnen worden ingesteld op waar of onwaar.

#### <a name="reduce-storage-time-for-messages"></a>De opslagtijd voor berichten verkorten

De IoT Edge hub-module slaat berichten tijdelijk op als ze om welke reden dan ook niet aan IoT Hub kunnen worden geleverd. U instellen hoe lang de IoT Edge-hub vasthoudt aan niet-afgeleverde berichten voordat deze verlopen. Als u geheugenproblemen op uw apparaat hebt, u de **timeToLiveSecs-waarde** verlagen in de dellmodulevan IoT Edge.

De standaardwaarde van de parameter timeToLiveSecs is 7200 seconden, dat is twee uur.

### <a name="do-not-use-debug-versions-of-module-images"></a>Foutopsporingsversies van moduleafbeeldingen niet gebruiken

Wanneer u van testscenario's naar productiescenario's gaat, moet u defoutfoutconfiguraties verwijderen uit implementatiemanifesten. Controleer of geen van de moduleafbeeldingen in ** \.** de implementatiemanifesten het debug-achtervoegsel heeft. Als u opties hebt toegevoegd om poorten in de modules voor foutopsporing bloot te leggen, verwijdert u deze opties ook.

## <a name="container-management"></a>Containerbeheer

* **Belangrijk**
  * Toegang tot uw containerregister beheren
  * Tags gebruiken om versies te beheren

### <a name="manage-access-to-your-container-registry-with-a-service-principal"></a>Toegang tot uw containerregister beheren met een serviceprincipal

Voordat u modules implementeert voor de productie van IoT Edge-apparaten, moet u ervoor zorgen dat u de toegang tot uw containerregister beheert, zodat buitenstaanders geen toegang hebben tot uw containerafbeeldingen of deze niet kunnen aanbrengen. Gebruik een privé- en niet-openbaar containerregister om containerafbeeldingen te beheren.

In de zelfstudies en andere documentatie instrueren we u om dezelfde containerregisterreferenties te gebruiken op uw IoT Edge-apparaat als u op uw ontwikkelingsmachine gebruikt. Deze instructies zijn alleen bedoeld om u te helpen bij het eenvoudiger instellen van test- en ontwikkelingsomgevingen en mogen niet worden gevolgd in een productiescenario. Azure Container Registry raadt [aan om met serviceprincipals te authenticeren](../container-registry/container-registry-auth-service-principal.md) wanneer toepassingen of services containerafbeeldingen op een geautomatiseerde of anderszins onbeheerde manier (headless) optrekken, zoals IoT Edge-apparaten doen.

Als u een serviceprincipal wilt maken, voert u de twee scripts uit zoals beschreven in [het maken van een serviceprincipal](../container-registry/container-registry-auth-aci.md#create-a-service-principal). Deze scripts voeren de volgende taken uit:

* In het eerste script wordt de serviceprincipal gemaakt. Hiermee wordt de servicehoofd-id en het hoofdwachtwoord van de service uitgevoerd. Bewaar deze waarden veilig in uw administratie.

* Het tweede script maakt roltoewijzingen die u aan de serviceprincipal verlenen, die vervolgens kan worden uitgevoerd indien nodig. We raden u aan de **acrPull-gebruikersrol** toe te passen voor de `role` parameter. Zie [Azure Container Registry-rollen en machtigingen](../container-registry/container-registry-roles.md) voor een lijst met rollen

Als u wilt verifiëren met behulp van een serviceprincipal, geeft u de servicehoofds-id en het wachtwoord op dat u hebt verkregen uit het eerste script.

* Geef voor de gebruikersnaam of client-id de servicehoofd-id op.

* Geef voor het wachtwoord- of clientgeheim het hoofdwachtwoord van de service op.

Zie [Verifiëren met de serviceprincipal](../container-registry/container-registry-auth-aci.md#authenticate-using-the-service-principal)voor een voorbeeld van het starten van een containerinstantie met Azure CLI.

### <a name="use-tags-to-manage-versions"></a>Tags gebruiken om versies te beheren

Een tag is een dockerconcept dat u gebruiken om onderscheid te maken tussen versies van dockercontainers. Tags zijn achtervoegsels zoals **1.0** die aan het einde van een containeropslagplaats gaan. bijvoorbeeld **mcr.microsoft.com/azureiotedge-agent:1.0**. Tags zijn veranderlijk en kunnen op elk gewenst moment naar een andere container worden gericht, dus uw team moet een overeenkomst overeenkomen om te volgen terwijl u uw moduleafbeeldingen bijwerkt.

Met tags u ook updates afdwingen op uw IoT Edge-apparaten. Wanneer u een bijgewerkte versie van een module naar uw containerregister duwt, verhoogt u de tag. Vervolgens push je een nieuwe implementatie naar je apparaten met de tag verhoogd. De containermotor herkent de toename van de tag als een nieuwe versie en trekt de nieuwste moduleversie naar uw apparaat.

Zie De runtime van [IoT Edge bijwerken](how-to-update-iot-edge.md#understand-iot-edge-tags) voor een voorbeeld van een tagconventie voor meer informatie over het gebruik van rolling tags en specifieke tags om versies bij te houden.

## <a name="networking"></a>Netwerken

* **Nuttig**
  * Uitgaande/binnenkomende configuratie controleren
  * Verbindingen toestaan vanaf IoT Edge-apparaten
  * Communicatie configureren via een proxy

### <a name="review-outboundinbound-configuration"></a>Uitgaande/binnenkomende configuratie controleren

Communicatiekanalen tussen Azure IoT Hub en IoT Edge zijn altijd geconfigureerd om uitgaand te zijn. Voor de meeste IoT Edge-scenario's zijn slechts drie verbindingen nodig. De containermotor moet verbinding maken met het containerregister (of registers) dat de moduleafbeeldingen bevat. De IoT Edge-runtime moet verbinding maken met IoT Hub om apparaatconfiguratie-informatie op te halen en berichten en telemetrie te verzenden. En als u automatische inrichting gebruikt, moet de IoT Edge-daemon verbinding maken met de Service Voor het inrichten van apparaten. Zie [Firewall- en poortconfiguratieregels](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)voor meer informatie.

### <a name="allow-connections-from-iot-edge-devices"></a>Verbindingen toestaan vanaf IoT Edge-apparaten

Als uw netwerkinstelling vereist dat u verbindingen die zijn gemaakt met IoT Edge-apparaten expliciet toestaat, controleert u de volgende lijst met IoT Edge-componenten:

* **IoT Edge-agent** opent een permanente AMQP/MQTT-verbinding met IoT Hub, mogelijk via WebSockets.
* **IoT Edge-hub** opent één permanente AMQP-verbinding of meerdere MQTT-verbindingen met IoT Hub, mogelijk via WebSockets.
* **IoT Edge daemon** voert intermitterende HTTPS-oproepen naar IoT Hub.

In alle drie de gevallen komt \*de DNS-naam overeen met het patroon .azure-devices.net.

Daarnaast voert de **Container-engine** gesprekken met containerregisters via HTTPS. Als u de iot edge runtime-containerafbeeldingen wilt ophalen, wordt de DNS-naam mcr.microsoft.com. De containerengine maakt verbinding met andere registers zoals geconfigureerd in de implementatie.

Deze checklist is een startpunt voor firewallregels:

   | URL\* ( = wildcard) | Uitgaande TCP-poorten | Gebruik |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft-containerregister |
   | global.azure-devices-provisioning.net  | 443 | DPS-toegang (optioneel) |
   | \*.azurecr.io | 443 | Persoonlijke containerregisters en containers van derden |
   | \*.blob.core.windows.net | 443 | Azure Container Registry-afbeeldingdelta's downloaden vanuit blobopslag |
   | \*.azure-devices.net | 5671, 8883, 443 | IoT Hub-toegang |
   | \*.docker.io  | 443 | Docker Hub-toegang (optioneel) |

Sommige van deze firewallregels zijn overgenomen van Azure Container Registry. Zie [Regels configureren om toegang te krijgen tot een Azure-containerregister achter een firewall](../container-registry/container-registry-firewall-access-rules.md)voor meer informatie.

### <a name="configure-communication-through-a-proxy"></a>Communicatie configureren via een proxy

Als uw apparaten worden geïmplementeerd op een netwerk dat een proxyserver gebruikt, moeten ze via de proxy kunnen communiceren om IoT Hub- en containerregisters te bereiken. Zie [Een IoT Edge-apparaat configureren om te communiceren via een proxyserver](how-to-configure-proxy-support.md)voor meer informatie.

## <a name="solution-management"></a>Oplossingsbeheer

* **Nuttig**
  * Logboeken en diagnose instellen
  * Denk aan tests en CI/CD-pijplijnen

### <a name="set-up-logs-and-diagnostics"></a>Logboeken en diagnose instellen

Op Linux gebruikt de IoT Edge daemon tijdschriften als standaardregistratiestuurprogramma. U het opdrachtregelgereedschap `journalctl` gebruiken om de daemonlogboeken op te vragen. In Windows maakt de IoT Edge-daemon gebruik van PowerShell-diagnostiek. Met `Get-IoTEdgeLog` gebruiken om logboeken van de daemon op te vragen. IoT Edge-modules gebruiken het JSON-stuurprogramma voor logboekregistratie, wat de standaardinstelling is.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Wanneer u een IoT Edge-implementatie test, u meestal toegang krijgen tot uw apparaten om logboeken op te halen en problemen op te lossen. In een implementatiescenario hebt u die optie mogelijk niet. Bedenk hoe u informatie gaat verzamelen over uw apparaten in productie. Een optie is het gebruik van een logging module die informatie verzamelt van de andere modules en stuurt deze naar de cloud. Een voorbeeld van een logging module is [logspout-loganalytics,](https://github.com/veyalla/logspout-loganalytics)of u uw eigen ontwerpen.

### <a name="place-limits-on-log-size"></a>Limieten plaatsen voor de logboekgrootte

Standaard stelt de Moby containerengine geen limieten voor containerlogboeken in. Na verloop van tijd kan dit ertoe leiden dat het apparaat zich vult met logboeken en geen schijfruimte meer heeft. Overweeg de volgende opties om dit te voorkomen:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Optie: Globale limieten instellen die van toepassing zijn op alle containermodules

U de grootte van alle containerlogboekbestanden beperken in de opties voor containerenginelogboeken. In het volgende voorbeeld `json-file` wordt het logboekstuurprogramma ingesteld op (aanbevolen) met beperkingen op de grootte en het aantal bestanden:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Voeg deze informatie toe aan een `daemon.json` met de naam genoemd bestand (of voeg deze toe en plaats deze de juiste locatie voor uw apparaatplatform.

| Platform | Locatie |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

De containermotor moet opnieuw worden gestart om de wijzigingen van kracht te laten worden.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Optie: Logboekinstellingen aanpassen voor elke containermodule

U dit doen in de **createOptions** van elke module. Bijvoorbeeld:

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

#### <a name="additional-options-on-linux-systems"></a>Extra opties op Linux-systemen

* Configureer de containerengine `systemd` om logboeken naar [logboeken](https://docs.docker.com/config/containers/logging/journald/) te verzenden door in te stellen `journald` als het standaardlogboekstuurprogramma.

* Verwijder periodiek oude logboeken van uw apparaat door een gereedschap logboeken te installeren. Gebruik de volgende bestandsspecificatie:

   ```txt
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

### <a name="consider-tests-and-cicd-pipelines"></a>Denk aan tests en CI/CD-pijplijnen

Voor het meest efficiënte IoT Edge-implementatiescenario u overwegen uw productie-implementatie te integreren in uw test- en CI/CD-pijplijnen. Azure IoT Edge ondersteunt meerdere CI/CD-platforms, waaronder Azure DevOps. Zie [Continue integratie en continue implementatie naar Azure IoT Edge](how-to-ci-cd.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [automatische implementatie van IoT Edge](module-deployment-monitoring.md).
* Ontdek hoe IoT Edge [continue integratie en continue implementatie](how-to-ci-cd.md)ondersteunt.
