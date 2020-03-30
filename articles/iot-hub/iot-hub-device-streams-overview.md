---
title: Azure IoT Hub-apparaatstreams | Microsoft Documenten
description: Overzicht van Azure IoT Hub-apparaatstreams, die veilige bidirectionele TCP-tunnels mogelijk maken voor verschillende communicatiescenario's van cloud tot apparaat.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: ff738e56226f7cbb720a754573a9d8607e0e3247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890458"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub-apparaatstreams (voorbeeld)

Azure IoT *Hub-apparaatstreams* vergemakkelijken het maken van veilige bidirectionele TCP-tunnels voor verschillende communicatiescenario's van cloud tot apparaat. Een apparaatstream wordt bemiddeld door een IoT Hub *streaming endpoint* dat fungeert als een proxy tussen uw apparaat en serviceeindpunten. Deze instelling, afgebeeld in het onderstaande diagram, is vooral handig wanneer apparaten zich achter een netwerkfirewall bevinden of zich in een privénetwerk bevinden. Als zodanig helpen IoT Hub-apparaatstromen de behoefte van klanten om IoT-apparaten op een firewallvriendelijke manier te bereiken en zonder dat u in het algemeen inkomende of uitgaande netwerkfirewallpoorten hoeft te openen.

!["IoT Hub-apparaat streamt overzicht"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Met behulp van IoT Hub-apparaatstreams blijven apparaten veilig en hoeven ze alleen uitgaande TCP-verbindingen te openen voor het streaming-eindpunt van iot-hub via poort 443. Zodra een stream is ingesteld, hebben de service-side en device-side applicaties elk programmatische toegang tot een WebSocket client object om onbewerkte bytes naar elkaar te verzenden en te ontvangen. De betrouwbaarheid en bestelgaranties van deze tunnel zijn gelijk aan TCP.

## <a name="benefits"></a>Voordelen

IoT Hub-apparaatstreams bieden de volgende voordelen:

* **Firewall-vriendelijke beveiligde connectiviteit:** IoT-apparaten kunnen worden bereikt vanaf serviceeindpunten zonder de inkomende firewallpoort op het apparaat of de netwerkperimeters te openen (alleen uitgaande connectiviteit met IoT Hub is nodig via poort 443).

* **Verificatie:** Zowel de apparaat- als de servicezijden van de tunnel moeten verifiëren met IoT Hub met behulp van hun bijbehorende referenties.

* **Versleuteling:** Standaard maken IoT Hub-apparaatstreams gebruik van TLS-verbindingen. Dit zorgt ervoor dat het verkeer altijd versleuteld is, ongeacht of de toepassing versleuteling gebruikt of niet.

* **Eenvoud van connectiviteit:** In veel gevallen elimineert het gebruik van apparaatstromen de noodzaak van complexe installatie van Virtual Private Networks om connectiviteit met IoT-apparaten mogelijk te maken.

* **Compatibiliteit met TCP/IP-stack:** IoT Hub-apparaatstreams kunnen tcp/IP-toepassingsverkeer bieden. Dit betekent dat een breed scala aan gepatenteerde en op standaarden gebaseerde protocollen gebruik kunnen maken van deze functie.

* **Gebruiksgemak in privénetwerkinstellingen:** Service kan communiceren met een apparaat door te verwijzen naar de apparaat-ID, in plaats van het IP-adres van het apparaat. Dit is handig in situaties waarin een apparaat zich in een privénetwerk bevindt en een privé-IP-adres heeft, of het IP-adres dynamisch is toegewezen en onbekend is aan de servicezijde.

## <a name="device-stream-workflows"></a>Werkstromen voor apparaatstromen

Er wordt een apparaatstream gestart wanneer de service aanvraagt verbinding te maken met een apparaat door de apparaat-id aan te bieden. Deze workflow past met name in een client/server communicatiemodel, inclusief SSH en RDP, waarbij een gebruiker op afstand verbinding wil maken met de SSH- of RDP-server die op het apparaat wordt uitgevoerd met behulp van een SSH- of RDP-clientprogramma.

Het proces voor het maken van apparaatstromen omvat een onderhandeling tussen het apparaat, de service, de hoofd- en streaming-eindpunten van de IoT-hub. Terwijl het hoofdeindpunt van de IoT-hub het maken van een apparaatstream orkestreert, verwerkt het streaming-eindpunt het verkeer dat tussen de service en het apparaat stroomt.

### <a name="device-stream-creation-flow"></a>Stroom voor het maken van apparaatstromen

Het programmatisch maken van een apparaatstream met behulp van de SDK omvat de volgende stappen, die ook in de onderstaande afbeelding worden weergegeven:

!["Apparaatstream handshakeproces"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. De apparaattoepassing registreert vooraf een callback om te worden gemeld wanneer een nieuwe apparaatstream naar het apparaat wordt gestart. Deze stap vindt meestal plaats wanneer het apparaat wordt opgestart en verbinding maakt met IoT Hub.

2. Het service-side programma initieert een apparaatstream wanneer dat nodig is door de apparaat-ID _(niet_ het IP-adres) te verstrekken.

3. IoT-hub waarschuwt het apparaat-side programma door een beroep te doen op de callback geregistreerd in stap 1. Het apparaat kan het initiatieverzoek voor de stream accepteren of weigeren. Deze logica kan specifiek zijn voor uw toepassingsscenario. Als het streamverzoek door het apparaat wordt afgewezen, informeert IoT Hub de service dienovereenkomstig; anders volgen de onderstaande stappen.

4. Het apparaat maakt een veilige uitgaande TCP-verbinding met het streaming-eindpunt via poort 443 en verbetert de verbinding met een WebSocket. De URL van het streaming-eindpunt en de referenties die moeten worden gebruikt om te verifiëren, worden beide door IoT Hub aan het apparaat verstrekt als onderdeel van het verzoek dat in stap 3 wordt verzonden.

5. De service wordt op de hoogte gesteld van het resultaat van het apparaat dat de stream accepteert en gaat over tot het maken van een eigen WebSocket-client naar het streaming-eindpunt. Op dezelfde manier ontvangt het de URL van het streaming eindpunt en verificatiegegevens van IoT Hub.

In het handdrukproces hierboven:

* Het handshakeproces moet binnen 60 seconden zijn voltooid (stap 2 tot en met 5), anders mislukt de handdruk met een time-out en wordt de service dienovereenkomstig gemeld.

* Nadat de stroom creatie van de stream hierboven is voltooid, fungeert het streaming-eindpunt als een proxy en wordt het verkeer tussen de service en het apparaat overhun respectieve WebSockets overgebracht.

* Apparaat en service hebben beide uitgaande connectiviteit nodig met het hoofdeindpunt van IoT Hub en het streaming-eindpunt via poort 443. De URL van deze eindpunten is beschikbaar op het tabblad *Overzicht* op de portal van de IoT Hub.

* De betrouwbaarheid en bestelgaranties van een gevestigde stroom zijn gelijk aan TCP.

* Alle verbindingen met IoT Hub en streaming endpoint gebruiken TLS en zijn versleuteld.

### <a name="termination-flow"></a>Beëindigingsstroom

Een gevestigde stroom wordt beëindigd wanneer een van de TCP-verbindingen met de gateway wordt losgekoppeld (door de service of het apparaat). Dit kan vrijwillig gebeuren door de WebSocket te sluiten op het apparaat of serviceprogramma's, of onwillekeurig in het geval van een time-out van netwerkconnectiviteit of procesuitval. Na beëindiging van de verbinding van een apparaat of service met het streaming-eindpunt, wordt de andere TCP-verbinding ook (met kracht) beëindigd en zijn de service en het apparaat verantwoordelijk om de stream opnieuw te maken, indien nodig.

## <a name="connectivity-requirements"></a>Connectiviteitsvereisten

Zowel het apparaat als de servicezijden van een apparaatstream moeten in staat zijn om TLS-verbindingen tot stand te brengen met IoT Hub en het streaming-eindpunt. Dit vereist uitgaande connectiviteit over poort 443 naar deze eindpunten. De hostnaam die aan deze eindpunten is gekoppeld, is te vinden op het tabblad *Overzicht* van IoT Hub, zoals weergegeven in de onderstaande afbeelding:

!["Eindpunten voor apparaatstroom"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Als alternatief kunnen de eindpuntgegevens worden opgehaald met Azure CLI onder de `property.hostname` `property.deviceStreams` sectie eigenschappen van de hub, met name en sleutels.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

De uitvoer is een JSON-object van alle eindpunten waarmee mogelijk verbinding moet worden gemaakt met het apparaat en de service van uw hub om een apparaatstream tot stand te brengen.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Zorg ervoor dat u Azure CLI-versie 2.0.57 of nieuwer hebt geïnstalleerd. U de nieuwste versie downloaden van de pagina [Azure CLI installeren.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Uitgaande connectiviteit toestaan voor de streamingeindpunten van het apparaat

Zoals aan het begin van dit artikel is vermeld, maakt uw apparaat een uitgaande verbinding met het streaming-eindpunt van IoT Hub tijdens het initiatieproces van apparaatstreams. Uw firewalls op het apparaat of het netwerk moeten uitgaande connectiviteit met de streaminggateway via poort 443 mogelijk maken (houd er rekening mee dat communicatie plaatsvindt via een WebSocket-verbinding die is versleuteld met TLS).

De hostnaam van het eindpunt voor het streamen van apparaten is ![te vinden op de Azure IoT Hub-portal onder het tabblad Overzicht.](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

U deze informatie ook vinden met Azure CLI:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Zorg ervoor dat u Azure CLI-versie 2.0.57 of nieuwer hebt geïnstalleerd. U de nieuwste versie downloaden van de pagina [Azure CLI installeren.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Problemen oplossen via activiteitslogboeken voor apparaatstromen

U Azure Monitor-logboeken instellen om het activiteitenlogboek van apparaatstromen in uw IoT-hub te verzamelen. Dit kan zeer nuttig zijn bij het oplossen van problemen scenario's.

Volg de onderstaande stappen om Azure Monitor-logboeken te configureren voor de apparaatstreamactiviteiten van uw IoT Hub:

1. Navigeer naar het tabblad *Diagnostische instellingen* in uw IoT-hub en klik op De koppeling Diagnostische *gegevens inschakelen.*

   !["Diagnostische logboeken inschakelen"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Geef een naam op voor uw diagnostische instellingen en kies De optie *Verzenden naar Logboekanalyse.* U wordt begeleid om een bestaande Log Analytics-werkruimtebron te kiezen of een nieuwe resource te maken. Controleer bovendien de *DeviceStreams* in de lijst.

    !['Logboeken van apparaatstreams inschakelen'](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. U nu uw logboeken voor apparaatstreams openen onder het tabblad *Logboeken* in de portal van uw IoT Hub. Er worden activiteitslogboeken `AzureDiagnostics` voor `Category=DeviceStreams`apparaatstromen weergegeven in de tabel en hebben .

   Zoals hieronder weergegeven, is de identiteit van het doelapparaat en het resultaat van de bewerking ook beschikbaar in de logboeken.

   !["Logboeken van apparaatstream openen"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Regionale beschikbaarheid

Tijdens de openbare preview zijn IoT Hub-apparaatstreams beschikbaar in de regio's Centraal-VS, Centraal-VS EUAP, Noord-Europa en Zuidoost-Azië. Zorg ervoor dat u uw hub maakt in een van deze regio's.

## <a name="sdk-availability"></a>Beschikbaarheid van SDK

Twee zijden van elke stream (aan de apparaat- en servicezijde) gebruiken de IoT Hub SDK om de tunnel vast te stellen. Tijdens de openbare preview kunnen klanten kiezen uit de volgende SDK-talen:

* Het ondersteuningsapparaat van de C en C# SDK streamt aan de apparaatzijde.

* Het NodeJS- en C# SDK-ondersteuningsapparaat streamt aan de servicezijde.

## <a name="iot-hub-device-stream-samples"></a>Voorbeeld van IoT Hub-apparaatstream

Er zijn twee [quickstart-voorbeelden](/azure/iot-hub) beschikbaar op de IoT Hub-pagina. Deze tonen het gebruik van apparaatstromen door toepassingen aan.

* Het *echo-voorbeeld* toont programmatisch gebruik van apparaatstromen aan (door de SDK API's rechtstreeks aan te roepen).

* Het *lokale proxyvoorbeeld* toont de tunneling van kant-en-klare client/servertoepassingsverkeer (zoals SSH, RDP of web) via apparaatstromen.

Deze monsters worden hieronder nader behandeld.

### <a name="echo-sample"></a>Echo-voorbeeld

Het echo-voorbeeld toont programmatisch gebruik van apparaatstromen om bytes tussen service- en apparaattoepassingen te verzenden en te ontvangen. Houd er rekening mee dat u service- en apparaatprogramma's in verschillende talen gebruiken. U bijvoorbeeld het C-apparaatprogramma gebruiken met het C#-serviceprogramma.

Hier zijn de echo monsters:

* [C# service- en serviceprogramma](quickstart-device-streams-echo-csharp.md)

* [Node.js serviceprogramma](quickstart-device-streams-echo-nodejs.md)

* [C-apparaatprogramma](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Lokale proxysample (voor SSH of RDP)

Het lokale proxyvoorbeeld toont een manier om tunneling van het verkeer van een bestaande toepassing mogelijk te maken, waarbij communicatie tussen een client en een serverprogramma wordt betrokken. Deze set-up werkt voor client/server protocollen zoals SSH en RDP, waar de service-side fungeert als een client (met SSH of RDP client programma's), en het apparaat-kant fungeert als de server (met SSH daemon of RDP server programma's).

In deze sectie wordt het gebruik van apparaatstromen beschreven om de gebruiker in staat te stellen SSH naar een apparaat via apparaatstreams te maken (de aanvraag voor RDP of andere client/server-toepassingen is vergelijkbaar met behulp van de bijbehorende poort van het protocol).

De setup maakt gebruik van twee lokale proxy-programma's weergegeven in de figuur hieronder, namelijk *apparaat-lokale proxy* en *local proxy* *service-lokale proxy*. De lokale proxyprogramma's zijn verantwoordelijk voor het uitvoeren van de [apparaatstream initiatiehandshake](#device-stream-creation-flow) met IoT Hub en interactie met SSH-client en SSH-daemon met behulp van reguliere client/serversockets.

!["Device stream proxy setup for SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. De gebruiker voert service-lokale proxy uit om een apparaatstream naar het apparaat te starten.

2. De apparaat-lokale proxy accepteert het verzoek tot het starten van de stream en de tunnel is ingesteld op het streaming-eindpunt van IoT Hub (zoals hierboven besproken).

3. De apparaat-lokale proxy maakt verbinding met het SSH daemon endpoint luisteren op poort 22 op het apparaat.

4. De service-lokale proxy luistert op een aangewezen poort in afwachting van nieuwe SSH-verbindingen van de gebruiker (poort 2222 gebruikt in het voorbeeld, maar dit kan worden geconfigureerd naar elke andere beschikbare poort). De gebruiker wijst de SSH-client naar de service-local proxy-poort op localhost.

### <a name="notes"></a>Opmerkingen

* De bovenstaande stappen voltooien een end-to-end tunnel tussen de SSH client (rechts) naar de SSH daemon (links). Onderdeel van deze end-to-end connectiviteit bestaat uit het verzenden van verkeer via een apparaatstream naar IoT Hub.

* De pijlen in de afbeelding geven de richting aan waarin verbindingen tussen eindpunten worden gemaakt. Houd er in het bijzonder rekening mee dat er geen inkomende verbindingen naar het apparaat gaan (dit wordt vaak geblokkeerd door een firewall).

* De keuze van het gebruik van poort 2222 op de service-lokale proxy is een willekeurige keuze. De proxy kan worden geconfigureerd om elke andere beschikbare poort te gebruiken.

* De keuze van poort 22 is protocolafhankelijk en specifiek voor SSH in dit geval. Voor RDP moet de poort 3389 worden gebruikt. Dit kan worden geconfigureerd in de meegeleverde voorbeeldprogramma's.

Gebruik de onderstaande links voor instructies over het uitvoeren van de lokale proxyprogramma's in uw taal naar keuze. Net als bij het [echomonster](#echo-sample)u apparaat- en service-lokale proxyprogramma's in verschillende talen uitvoeren omdat ze volledig interoperabel zijn.

* [C# service- en serviceprogramma](quickstart-device-streams-proxy-csharp.md)

* [Node.js serviceprogramma](quickstart-device-streams-proxy-nodejs.md)

* [C-apparaatprogramma](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Volgende stappen

Gebruik de onderstaande koppelingen voor meer informatie over apparaatstreams.

> [!div class="nextstepaction"]
> [Apparaatstreams op IoT-show (Kanaal 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
