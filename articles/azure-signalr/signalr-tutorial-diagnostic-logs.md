---
title: Diagnostische logboeken voor Azure SignalR-service
description: Meer informatie over het instellen van diagnostische logboeken voor Azure SignalR Service en hoe u deze gebruiken om zelf problemen op te lossen.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536731"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Diagnostische logboeken voor Azure SignalR-service

Deze zelfstudie behandelt wat diagnostische logboeken zijn voor Azure SignalR Service en hoe diagnostische logboeken moeten worden ingesteld en hoe u problemen oplossen met diagnostische logboeken.

## <a name="prerequisites"></a>Vereisten
Als u diagnostische logboeken wilt inschakelen, moet u uw logboekgegevens ergens opslaan. Deze zelfstudie maakt gebruik van Azure Storage en Log Analytics.

* [Azure-opslag](../azure-monitor/platform/resource-logs-collect-storage.md) - Behoudt diagnostische logboeken voor beleidscontrole, statische analyse of back-up.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) - Een flexibele tool voor zoeken en analyseren van logboeken die het mogelijk maakt om ruwe logboeken te analyseren die worden gegenereerd door een Azure-bron.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Diagnostische logboeken instellen voor een Azure SignalR-service

U diagnostische logboeken voor Azure SignalR Service weergeven. Deze logboeken bieden een rijkere weergave van de connectiviteit met uw Azure SignalR Service-instantie. De diagnostische logboeken geven gedetailleerde informatie over elke verbinding. Bijvoorbeeld basisinformatie (gebruikersnaam, verbindings-ID en transporttype enzovoort) en gebeurtenisgegevens (verbinding, loskoppeling en afbreken gebeurtenis enzovoort) van de verbinding. Diagnostische logboeken kunnen worden gebruikt voor probleemidentificatie, verbindingstracking en analyse.

### <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Diagnostische logboeken zijn standaard uitgeschakeld. Voer de volgende stappen uit om diagnostische logboeken in te schakelen:

1. Klik in de [Azure-portal](https://portal.azure.com)onder **Controle**op **Diagnostische instellingen**.

    ![Navigatie van deelvenster naar diagnostische instellingen](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Klik vervolgens op **Diagnostische instelling toevoegen**.

    ![Diagnostische logboeken toevoegen](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Stel het gewenste archiefdoel in. Momenteel ondersteunen we **Archive naar een opslagaccount** en **Send to Log Analytics**.

1. Selecteer de logboeken die u wilt archiveren.

    ![Het deelvenster Diagnostische instellingen](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Sla de nieuwe diagnostische instellingen op.

Nieuwe instellingen worden van kracht in ongeveer 10 minuten. Daarna worden logboeken weergegeven in het geconfigureerde archiefdoel in het **deelvenster Logboeken diagnostische gegevens.**

Zie het overzicht van Diagnostische [logboeken van Azure](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostische gegevens.

### <a name="diagnostic-logs-categories"></a>Categorieën diagnostische logboeken

Azure SignalR-service legt diagnostische logboeken vast in één categorie:

* **Alle logboeken:** houd verbindingen bij die verbinding maken met Azure SignalR Service. De logboeken geven informatie over de verbinding/ontkoppeling, authenticatie en beperking. Zie de volgende sectie voor meer informatie.

### <a name="archive-to-a-storage-account"></a>Archiveren naar een opslagaccount

Logboeken worden opgeslagen in het opslagaccount dat is geconfigureerd in **het logboekenvenster Diagnostische gegevens.** Er wordt `insights-logs-alllogs` automatisch een container met de naam gemaakt om diagnostische logboeken op te slaan. In de container worden logboeken `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`opgeslagen in het bestand. Kortom, het pad `resource ID` wordt `Date Time`gecombineerd door en . De logbestanden worden `hour`gesplitst door . Daarom zijn `m=00`de minuten altijd .

Alle logboeken worden opgeslagen in de JSON-indeling (JavaScript Object Notation). Elk item heeft tekenreeksvelden die de notatie gebruiken die in de volgende secties is beschreven.

Json-tekenreeksen voor archieflogboeken bevatten elementen die in de volgende tabellen worden vermeld:

**Formaat**

Name | Beschrijving
------- | -------
tijd | Gebeurtenistijd van logboeklogboek
niveau | Gebeurtenisniveau logboek
resourceId | Resource-id van uw Azure SignalR-service
location | Locatie van uw Azure SignalR-service
category | Categorie van de logboekgebeurtenis
operationName | Bedrijfsnaam van de gebeurtenis
callerIpAddress | IP-adres van uw server/client
properties | Gedetailleerde eigenschappen met betrekking tot deze logboekgebeurtenis. Zie de onderstaande eigenschappentabel voor meer informatie

**Eigenschappentabel**

Name | Beschrijving
------- | -------
type | Type van de logboekgebeurtenis. Momenteel bieden we informatie over connectiviteit met de Azure SignalR-service. Alleen `ConnectivityLogs` type is beschikbaar
verzameling | Verzameling van de loggebeurtenis. Toegestane waarden `Connection`zijn: `Authorization` , en`Throttling`
verbindingId | Identiteit van de verbinding
transportType | Transporttype van de verbinding. Toegestane waarden zijn: `Websockets` \| `ServerSentEvents` \|`LongPolling`
connectionType | Type van de verbinding. Toegestane waarden zijn: `Server` \| `Client`. `Server`: verbinding vanaf serverzijde; `Client`: verbinding vanaf clientzijde
userId | Identiteit van de gebruiker
message | Gedetailleerd bericht van logboekgebeurtenis

De volgende code is een voorbeeld van een JSON-tekenreeks voor archieflogboeken:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Schema voor logboeken archiveren voor Logboekanalyse

Voer de volgende stappen uit om diagnostische logboeken weer te geven:

1. Klik `Logs` in uw doel Log Analytics.

    ![Menu-item Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Voer `SignalRServiceDiagnosticLogs` het tijdsbereik in en selecteer deze voor diagnostische logboeken. Zie [Aan de slag met Logboekanalyse in Azure-monitor](../azure-monitor/log-query/get-started-portal.md) voor geavanceerde query's

    ![Querylog in Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Archieflogboekkolommen bevatten elementen in de volgende tabel:

Name | Beschrijving
------- | ------- 
TimeGenerated | Gebeurtenistijd van logboeklogboek
Verzameling | Verzameling van de loggebeurtenis. Toegestane waarden `Connection`zijn: `Authorization` , en`Throttling`
OperationName | Bedrijfsnaam van de gebeurtenis
Locatie | Locatie van uw Azure SignalR-service
Niveau | Gebeurtenisniveau logboek
CallerIpAddress | IP-adres van uw server/client
Bericht | Gedetailleerd bericht van logboekgebeurtenis
UserId | Identiteit van de gebruiker
ConnectionId | Identiteit van de verbinding
ConnectionType | Type van de verbinding. Toegestane waarden zijn: `Server` \| `Client`. `Server`: verbinding vanaf serverzijde; `Client`: verbinding vanaf clientzijde
TransportType | Transporttype van de verbinding. Toegestane waarden zijn: `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Probleemoplossing met diagnostische logboeken

Als u problemen wilt oplossen voor Azure SignalR Service, u logboeken aan de server-/clientzijde inschakelen om fouten vast te leggen. Op dit moment stelt Azure SignalR Service diagnostische logboeken bloot, u ook logboeken inschakelen voor servicezijde.

Wanneer u een onverwachte groei- of droppingsituatie voor verbinding tegenkomt, u gebruik maken van diagnostische logboeken om problemen op te lossen.

Typische problemen gaan vaak over onverwachte hoeveelheidswijzigingen van verbindingen, verbindingen bereiken verbindingslimieten en autorisatiefouten. Bekijk de volgende secties over het oplossen van problemen.

#### <a name="unexpected-connection-number-changes"></a>Onverwachte wijzigingen in het verbindingsnummer

##### <a name="unexpected-connection-dropping"></a>Onverwachte verbinding laten vallen

Als u onverwachte verbindingen tegenkomt, schakelt u eerst logboeken in service-, server- en clientzijden in.

Als de verbinding wordt verbroken, worden deze loskoppelende `ConnectionAborted` `ConnectionEnded` gebeurtenis `operationName`in de diagnostische logboeken geregistreerd, u ziet of in .

Het verschil `ConnectionAborted` `ConnectionEnded` tussen `ConnectionEnded` en is dat is een verwachte loskoppeling die wordt geactiveerd door client of server kant. Terwijl `ConnectionAborted` de is meestal een onverwachte verbinding laten vallen `message`gebeurtenis, en afbreken reden zal worden verstrekt in .

De afbreekredenen worden vermeld in de volgende tabel:

Reden | Beschrijving
------- | ------- 
Aantal verbindingen bereikt limiet | Het aantal verbindingen bereikt de limiet van uw huidige prijslaag. Overweeg de service-eenheid opschalen
Toepassingsserver heeft de verbinding gesloten | App server activeert de abortus. Het kan worden beschouwd als een verwachte abortus
Time-out van pingverbinding | Meestal wordt het veroorzaakt door netwerkprobleem. Overweeg de beschikbaarheid van uw app-server vanaf internet te controleren
Service herladen, opnieuw verbinden | Azure SignalR-service wordt opnieuw geladen. Azure SignalR ondersteunt automatisch opnieuw verbinden, u wachten tot u opnieuw verbinding maakt of handmatig opnieuw verbinding maken met Azure SignalR Service
Tijdelijke fout van interne server | Tijdelijke fout treedt op in Azure SignalR Service, moet automatisch worden hersteld
Serververbinding verbroken | Serververbinding daalt met onbekende fout, overweeg zelfoplossend probleemmet service / server / client side log eerste. Probeer basisproblemen uit te sluiten (bijvoorbeeld netwerkprobleem, probleem met de kant van de app-server, enzovoort). Als het probleem niet is opgelost, neem dan contact met ons op voor verdere hulp. Zie [Help-sectie opvragen](#get-help) voor meer informatie. 

##### <a name="unexpected-connection-growing"></a>Onverwachte verbinding groeit

Om problemen op te lossen over onverwachte verbinding groeien, het eerste wat je hoeft te doen is om te filteren uit de extra verbindingen. U unieke testgebruikers-id toevoegen aan uw testclientverbinding. Controleer het vervolgens in met diagnostische logboeken, als u ziet dat meer dan één clientverbindingen dezelfde testgebruikersnaam of IP hebben, is het waarschijnlijk dat de clientzijde meer verbindingen maakt en tot stand komt dan verwacht. Controleer je klantenkant.

#### <a name="authorization-failure"></a>Autorisatiefout

Als u 401 ongeautoriseerde geretourneerde clientaanvragen krijgt, controleert u uw diagnostische logboeken. Als je `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`dit tegenkomt, betekent dit dat alle doelgroepen in je toegangstoken ongeldig zijn. Probeer de geldige doelgroepen te gebruiken die in het logboek worden voorgesteld.


#### <a name="throttling"></a>Beperking

Als u merkt dat u geen SignalR-clientverbindingen met Azure SignalR-service tot stand brengen, controleert u uw diagnostische logboeken. Als u `Connection count reaches limit` in het diagnostisch logboek een ontmoeting tegenkomt, worden er te veel verbindingen met SignalR Service ingesteld, die de limiet voor het aantal verbindingen bereiken. Overweeg uw SignalR-service op te schalen. Als u `Message count reaches limit` in het diagnostisch logboek tegenkomt, betekent dit dat u de gratis laag gebruikt en dat u het quotum van berichten gebruikt. Als u meer berichten wilt verzenden, u overwegen uw SignalR-service te wijzigen in standaardlaag om extra berichten te verzenden. Zie [Azure SignalR Service Pricing](https://azure.microsoft.com/pricing/details/signalr-service/)voor meer informatie.

### <a name="get-help"></a>Help opvragen

We raden je aan om eerst zelf problemen op te lossen. De meeste problemen worden veroorzaakt door problemen met de app-server of het netwerk. Volg [de handleiding voor probleemoplossing met diagnostische logboek](#troubleshooting-with-diagnostic-logs) en [basisproblemen schieten gids](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) om de oorzaak te vinden.
Als het probleem nog steeds niet kan worden opgelost, u overwegen een probleem in GitHub te openen of een ticket te maken in Azure Portal.
Geef op:
1. Tijdsbereik ongeveer 30 minuten wanneer het probleem optreedt
2. Resource-id van Azure SignalR-service
3. Probleemgegevens, zo specifiek mogelijk: appserver verzendt bijvoorbeeld geen berichten, de clientverbinding daalt en ga zo maar door
4. Logboeken verzameld van server/clientzijde en ander materiaal dat nuttig kan zijn
5. [Optioneel] Repro-code

> [!NOTE]
> Als u probleem in GitHub opent, houdt u uw gevoelige gegevens (bijvoorbeeld bron-id, server-/clientlogboeken) privé en verzendt u alleen privé naar leden in de Microsoft-organisatie.