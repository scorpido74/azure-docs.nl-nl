---
title: OPC Publisher uitvoeren - Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u OPC Publisher uitvoeren en debuggen. Het richt zich ook op prestaties en geheugen overwegingen.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4f5d57bab51d537b64ce4b800737219663c3d7a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198783"
---
# <a name="run-opc-publisher"></a>OPC Publisher uitvoeren

In dit artikel wordt beschreven hoe u ad-foutopsporing OPC Publisher weergeven. Het richt zich ook op prestaties en geheugen overwegingen.

## <a name="command-line-options"></a>Opdrachtregelopties

Toepassingsgebruik wordt als `--help` volgt weergegeven met de opdrachtregeloptie:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Meestal geeft u de verbindingstekenreeks van de IoT Hub-hub alleen op bij de eerste run van de toepassing. De verbindingstekenreeks wordt versleuteld en opgeslagen in het platformcertificaatarchief. Op latere uitvoeringen leest de toepassing de verbindingstekenreeks uit het certificaatarchief. Als u de verbindingstekenreeks opgeeft bij elke run, wordt het apparaat dat is gemaakt voor de toepassing in het IoT Hub-apparaatregister verwijderd en opnieuw gemaakt.

## <a name="run-natively-on-windows"></a>Native uitvoeren op Windows

Open het **opcpublisher.sln-project** met Visual Studio, bouw de oplossing en publiceer het. U de toepassing als volgt starten in de **doelmap die** u hebt gepubliceerd:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Gebruik een zelfgebouwde container

Bouw je eigen container en start deze als volgt:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Een container gebruiken vanuit Microsoft Container Registry

Er is een vooraf gebouwde container beschikbaar in het Microsoft Container Registry. Start het als volgt:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Controleer [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) om de ondersteunde besturingssystemen en processorarchitecturen te bekijken. Als uw BE- en CPU-architectuur wordt ondersteund, selecteert Docker automatisch de juiste container.

## <a name="run-as-an-azure-iot-edge-module"></a>Uitvoeren als Azure IoT Edge-module

OPC Publisher is klaar om te worden gebruikt als [Azure IoT Edge-module.](https://docs.microsoft.com/azure/iot-edge) Wanneer u OPC Publisher als IoT Edge-module gebruikt, worden de enige ondersteunde transportprotocollen **Amqp_Tcp_Only** en **Mqtt_Tcp_Only.**

Als u OPC Publisher als module wilt toevoegen aan uw IoT Edge-implementatie, gaat u naar uw IoT-hub-instellingen in de Azure-portal en voert u de volgende stappen uit:

1. Ga naar **IoT Edge** en maak of selecteer uw IoT Edge-apparaat.
1. Selecteer **Modules instellen**.
1. Selecteer **Toevoegen** onder **Implementatiemodules** en vervolgens **de IoT Edge-module**.
1. Voer in het veld **Naam** **uitgever**in .
1. Voer in het veld **Afbeelding URI**`mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. U vindt de beschikbare tags op [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Plak de volgende JSON in het veld **Opties voor containermaken:**

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Met deze configuratie configureert IoT Edge om een container met de naam **Publisher** te starten met de afbeelding VAN OPC Publisher. De hostnaam van het systeem van de container is ingesteld op **publisher**. OPC Publisher wordt aangeroepen met het `--aa`volgende argument opdrachtregel: . Met deze optie vertrouwt OPC Publisher op de certificaten van de OPC UA-servers waarmee het verbinding maakt. U alle opdrachtregelopties van OPC Publisher gebruiken. De enige beperking is de grootte van de **opties voor containermaken** die worden ondersteund door IoT Edge.

1. Laat de overige instellingen ongewijzigd en selecteer **Opslaan**.
1. Als u de uitvoer van de OPC Publisher lokaal wilt verwerken met een andere IoT Edge-module, gaat u terug naar de pagina **Modules instellen.** Ga vervolgens naar het tabblad **Routes opgeven** en voeg een nieuwe route toe die lijkt op de volgende JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Terug in de pagina **Modules instellen** selecteert u **Volgende**totdat u de laatste pagina van de configuratie bereikt.
1. Selecteer **Verzenden** om uw configuratie naar IoT Edge te verzenden.
1. Wanneer u IoT Edge op uw edge-apparaat hebt gestart en de **uitgever** van de dockercontainer `docker logs -f publisher` wordt uitgevoerd, u de logboekuitvoer van OPC Publisher bekijken door het logboekbestand te gebruiken of te controleren. In het vorige voorbeeld bevindt `d:\iiotegde\publisher-publisher.log`het logboekbestand zich boven . U ook het [hulpprogramma voor iot-edge-opc-publisher-diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)gebruiken.

### <a name="make-the-configuration-files-accessible-on-the-host"></a>De configuratiebestanden toegankelijk maken op de host

Als u de configuratiebestanden van de IoT Edge-module toegankelijk wilt maken in het hostbestandssysteem, gebruikt u de volgende **opties voor containermaken**. Het volgende voorbeeld is een implementatie met Linux-containers voor Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Met deze opties leest OPC Publisher de knooppunten `./pn.json` die het moet publiceren uit `/appdata` het bestand en is de werkmap van de container ingesteld op bij het opstarten. Met deze instellingen leest OPC `/appdata/pn.json` Publisher het bestand uit de container om de configuratie te krijgen. Zonder `--pf` de optie probeert OPC Publisher het `./publishednodes.json`standaardconfiguratiebestand te lezen.

Het logboekbestand, met `publisher-publisher.log`de standaardnaam, wordt geschreven en `/appdata` de `CertificateStores` map wordt ook gemaakt in deze map.

Om al deze bestanden beschikbaar te maken in het hostbestandssysteem, vereist de containerconfiguratie een bindingsmountvolume. De `d://iiotedge:/appdata` binding brengt `/appdata`de map , de huidige werkmap voor `d://iiotedge`het opstarten van containers, in de hostmap . Zonder deze optie worden geen bestandsgegevens weergegeven wanneer de container vervolgens wordt gestart.

Als u Windows-containers gebruikt, is `Binds` de syntaxis van de parameter anders. Bij het opstarten van `c:\appdata`containers is de werkmap . Als u het configuratiebestand `d:\iiotedge`in de map op de `HostConfig` host wilt plaatsen, geeft u de volgende toewijzing op in de sectie:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Als u Linux-containers op Linux gebruikt, `Binds` is de syntaxis van de parameter opnieuw anders. Bij het opstarten van `/appdata`containers is de werkmap . Als u het configuratiebestand `/iiotedge` in de map op de `HostConfig` host wilt plaatsen, geeft u de volgende toewijzing op in de sectie:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Overwegingen bij het gebruik van een container

In de volgende secties worden enkele dingen weergegeven om in gedachten te houden wanneer u een container gebruikt:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Toegang tot de OPC Publisher OPC UA-server

Standaard luistert de OPC Publisher OPC UA-server op poort 62222. Als u deze binnenkomende poort in een container wilt blootleggen, gebruikt u de volgende opdracht:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Intercontainernaamresolutie inschakelen

Als u naamomzetting vanuit de container naar andere containers wilt inschakelen, maakt u een `--network` dockerbrugnetwerk voor gebruikers en sluit u de container met de optie aan op dit netwerk. Wijs de container ook `--name` als volgt een naam toe met de optie:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

De container is nu bereikbaar `publisher` met de naam van andere containers op hetzelfde netwerk.

### <a name="access-other-systems-from-within-the-container"></a>Toegang tot andere systemen vanuit de container

Andere containers kunnen worden bereikt met behulp van de parameters beschreven in de vorige sectie. Als het besturingssysteem waarop Docker wordt gehost DNS is ingeschakeld, heeft u toegang tot alle systemen die bekend zijn bij DNS-werken.

Schakel in netwerken die NetBIOS-naamomzetting gebruiken, toegang tot `--add-host` andere systemen in door uw container met de optie te starten. Met deze optie wordt een vermelding in het hostbestand van de container toegevoegd:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Een hostnaam toewijzen

OPC Publisher gebruikt de hostnaam van de machine waarop het wordt uitgevoerd voor certificaat- en eindpuntgeneratie. Docker kiest een willekeurige hostnaam als deze `-h` niet is ingesteld door de optie. In het volgende voorbeeld ziet u hoe u `publisher`de interne hostnaam van de container instelt op:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Bindingssteunen gebruiken (gedeeld bestandssysteem)

In plaats van het containerbestandssysteem te gebruiken, u het hostbestandssysteem kiezen om configuratiegegevens en logboekbestanden op te slaan. Als u deze optie `-v` wilt `docker run` configureren, gebruikt u de optie in de bindingshoudermodus.

## <a name="opc-ua-x509-certificates"></a>OPC UA X.509-certificaten

OPC UA gebruikt X.509-certificaten om de OPC UA-client en -server te verifiëren wanneer ze een verbinding tot stand brengen en om de communicatie tussen deze certificaten te versleutelen. OPC Publisher gebruikt certificaatwinkels die worden onderhouden door de OPC UA-stack om alle certificaten te beheren. Bij het opstarten controleert OPC Publisher of er een certificaat voor zichzelf is. Als er geen certificaat in het certificaatarchief staat en er geen certificaat is dat is doorgegeven aan de opdrachtregel, maakt OPC Publisher een zelfondertekend certificaat. Zie de methode **InitApplicationSecurityAsync** in `OpcApplicationConfigurationSecurity.cs`.

Zelfondertekende certificaten bieden geen beveiliging, omdat ze niet zijn ondertekend door een vertrouwde CA.

OPC Publisher biedt opdrachtregelopties om:

- Ophalen van MVO-informatie van het huidige toepassingscertificaat dat wordt gebruikt door OPC Publisher.
- Opc Publisher voorzien van een CA ondertekend certificaat.
- Opc Publisher voorzien van een nieuw sleutelpaar en een overeenkomend CA-ondertekend certificaat.
- Voeg certificaten toe aan een vertrouwd peer- of trusted issuer certificate store.
- Voeg een CRL toe.
- Verwijder een certificaat uit het certificaatarchief vertrouwde peer- of vertrouwde emittenten.

Met al deze opties u parameters doorgeven met behulp van bestanden of basis64 gecodeerde tekenreeksen.

Het standaardopslagtype voor alle certificaatopslag is het bestandssysteem, dat u wijzigen met behulp van opdrachtregelopties. Omdat de container geen permanente opslag biedt in het bestandssysteem, moet u een ander winkeltype kiezen. Gebruik de `-v` optie Docker om de certificaatopslag in het hostbestandssysteem of op een Docker-volume voort te houden. Als u een Docker-volume gebruikt, u certificaten doorgeven met behulp van door basis64 gecodeerde tekenreeksen.

De runtime-omgeving is van invloed op de manier waarop certificaten worden gehandhaafd. Vermijd het maken van nieuwe certificaatopslag telkens wanneer u de toepassing uitvoert:

- Als u native op Windows wordt uitgevoerd, `Directory` u geen typetoepassingscertificaatarchief gebruiken omdat de toegang tot de privésleutel mislukt. Gebruik in dit geval `--at X509Store`de optie .
- Als u als Linux-dockercontainer wordt uitgevoerd, u de certificaatopslag toewijzen aan het hostbestandssysteem met de dockerrunoptie. `-v <hostdirectory>:/appdata` Met deze optie wordt het certificaat persistent voor toepassingwordt uitgevoerd.
- Als u als Linux dockercontainer wordt uitgevoerd en u wilt een X509-winkel gebruiken voor het toepassingscertificaat, gebruikt u de dockerrunoptie `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` en de toepassingsoptie`--at X509Store`

## <a name="performance-and-memory-considerations"></a>Prestatie- en geheugenoverwegingen

In deze sectie worden opties voor het beheren van geheugen en prestaties besproken:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Opdrachtregelparameters om prestaties en geheugen te beheren

Wanneer u OPC Publisher uitvoert, moet u op de hoogte zijn van uw prestatievereisten en de geheugenbronnen die beschikbaar zijn op uw host.

Geheugen en prestaties zijn onderling afhankelijk en beide zijn afhankelijk van de configuratie van het aantal knooppunten dat u configureert om te publiceren. Zorg ervoor dat de volgende parameters aan uw eisen voldoen:

- IoT-hub verzendt interval:`--si`
- IoT Hub-berichtgrootte `1`(standaard):`--ms`
- Bewaakte wachtrijcapaciteit voor items:`--mq`

De `--mq` parameter regelt de bovengrens van de capaciteit van de interne wachtrij, die alle opc-knooppuntwaardewijzigingsmeldingen buffert. Als OPC Publisher niet snel genoeg berichten naar IoT Hub kan verzenden, buffert deze wachtrij de meldingen. De parameter stelt het aantal meldingen in dat kan worden gebufferd. Als u het aantal items in deze wachtrij in uw testritten ziet toenemen, moet u het belangrijkste zijn om berichten te verliezen:

- Het interval voor het verzenden van iot-hub's verminderen
- De grootte van het IoT Hub-bericht vergroten

De `--si` parameter dwingt OPC Publisher om berichten naar IoT Hub te sturen met het opgegeven interval. OPC Publisher stuurt een bericht zodra de `--ms` door de parameter opgegeven berichtgrootte is `--si` bereikt, of zodra het interval dat door de parameter is opgegeven is bereikt. Als u de optie `--ms 0`berichtgrootte wilt uitschakelen, gebruikt u . In dit geval gebruikt OPC Publisher de grootst mogelijke IoT Hub-berichtgrootte van 256 kB om gegevens te batchen.

Met `--ms` de parameter u berichten batchen die naar IoT Hub worden verzonden. Het protocol dat u gebruikt, bepaalt of de overhead van het verzenden van een bericht naar IoT Hub hoog is in vergelijking met de werkelijke tijd van het verzenden van de payload. Als uw scenario latentie toestaat wanneer gegevens worden ingenomen door IoT Hub, configureert U OPC Publisher om de grootste berichtgrootte van 256 kB te gebruiken.

Voordat u OPC Publisher gebruikt in productiescenario's, test u de prestaties en het geheugengebruik onder productieomstandigheden. U `--di` de parameter gebruiken om het interval op te geven, in enkele seconden, dat OPC Publisher diagnostische informatie schrijft.

### <a name="test-measurements"></a>Testmetingen

In het volgende voorbeeld toont `--si` diagnostiek `--ms` metingen met verschillende waarden voor en parameters die 500 knooppunten publiceren met een OPC-publicatieinterval van 1 seconde.  De test gebruikte een OPC Publisher-foutopsporingsbuild op Windows 10 native gedurende 120 seconden. Het IoT Hub-protocol was het standaard MQTT-protocol.

#### <a name="default-configuration---si-10---ms-262144"></a>Standaardconfiguratie (--si 10 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

De standaardconfiguratie stuurt elke 10 seconden gegevens naar IoT Hub of wanneer er 256 kB aan gegevens beschikbaar is voor de inname van IoT Hub. Deze configuratie voegt een matige latentie van ongeveer 10 seconden toe, maar heeft de laagste kans om gegevens te verliezen vanwege de grote berichtgrootte. De diagnostische uitvoer laat zien dat er `monitored item notifications enqueue failure: 0`geen verloren OPC-knooppuntupdates zijn: .

#### <a name="constant-send-interval---si-1---ms-0"></a>Constant verzendinterval (--si 1 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Wanneer de berichtgrootte is ingesteld op 0, batchet OPC Publisher intern gegevens met de grootste ondersteunde IoT Hub-berichtgrootte, namelijk 256 kB. De diagnostische uitvoer geeft aan dat de gemiddelde berichtgrootte 115.019 bytes bedraagt. In deze configuratie verliest OPC Publisher geen opc-nodewaarde-updates en in vergelijking met de standaard-latentie heeft het een lagere latentie.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Elke opc-knooppuntwaarde-update verzenden (--si 0 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Deze configuratie verzendt voor elke OPC-knooppuntwaarde een bericht wijzigen naar IoT Hub. De diagnose geeft aan dat de gemiddelde berichtgrootte 234 bytes bedraagt, wat klein is. Het voordeel van deze configuratie is dat OPC Publisher geen latentie toevoegt. Het aantal verloren OPC-knooppuntwaarde-updates (`monitored item notifications enqueue failure: 44624`) is hoog, waardoor deze configuratie ongeschikt is voor scenario's met grote volumes telemetrie die moeten worden gepubliceerd.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximale batching (--si 0 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Deze configuratie batches zo veel OPC node waarde updates mogelijk. De maximale IoT Hub-berichtgrootte is 256 kB, die hier is geconfigureerd. Er is geen verzendinterval aangevraagd, wat betekent dat de hoeveelheid gegevens die IoT Hub moet opnemen, de latentie bepaalt. Deze configuratie heeft de minste kans op verlies van opc-knooppuntwaarden en is geschikt voor het publiceren van een groot aantal knooppunten. Wanneer u deze configuratie gebruikt, moet u ervoor zorgen dat uw scenario geen voorwaarden heeft waarin hoge latentie wordt geïntroduceerd als de berichtgrootte van 256 kB niet wordt bereikt.

## <a name="debug-the-application"></a>Fouten in de toepassing opsporen

Als u de toepassing wilt debuggen, opent u het **oplossingsbestand opcpublisher.sln** met Visual Studio en gebruikt u de foutopsporingstools van Visual Studio.

Als u toegang nodig hebt tot de OPC UA-server in de OPC Publisher, moet u ervoor zorgen dat uw firewall toegang geeft tot de poort waarop de server luistert. De standaardpoort is: 62222.

## <a name="control-the-application-remotely"></a>Bedien de toepassing op afstand

Het configureren van de knooppunten om te publiceren kan worden gedaan met behulp van IoT Hub directe methoden.

OPC Publisher implementeert een paar extra IoT Hub directe methode oproepen om te lezen:

- Algemene informatie.
- Diagnostische informatie over OPC-sessies, abonnementen en bewaakte items.
- Diagnostische informatie over IoT Hub-berichten en -gebeurtenissen.
- Het startlogboek.
- De laatste 100 regels van het logboek.
- Sluit de toepassing af.

De volgende GitHub-repositories bevatten hulpprogramma's om [de knooppunten te configureren om](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) de diagnostische informatie te publiceren en te [lezen.](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) Beide tools zijn ook beschikbaar als containers in Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Een voorbeeld van EEN OPC UA-server gebruiken

Als u geen echte OPC UA-server hebt, u het [voorbeeld OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) gebruiken om aan de slag te gaan. Deze sample PLC is ook beschikbaar op Docker Hub.

Het implementeert een aantal tags, die willekeurige gegevens en tags met afwijkingen genereren. U het voorbeeld uitbreiden als u extra tagwaarden wilt simuleren.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Publisher uitvoeren, zijn de aanbevolen volgende stappen om meer te weten te komen over [OPC Twin](overview-opc-twin.md) en [OPC Vault.](overview-opc-vault.md)
