---
title: Diagnostische logboeken voor de Azure signalerings service
description: Meer informatie over het instellen van Diagnostische logboeken voor de Azure signalerings service en hoe u deze kunt gebruiken om zichzelf op te lossen.
author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 33d9a338e12fa4b3d2449f0c5b0576895364c3cf
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750261"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Diagnostische logboeken voor de Azure signalerings service

In deze zelf studie wordt beschreven wat zijn Diagnostische logboeken voor de Azure signalerings service en het instellen van Diagnostische logboeken en het oplossen van problemen met Diagnostische logboeken.

## <a name="prerequisites"></a>Vereisten
Als u Diagnostische logboeken wilt inschakelen, moet u uw logboek gegevens ergens opslaan. In deze zelf studie wordt gebruikgemaakt van Azure Storage en Log Analytics.

* [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) : behoudt Diagnostische logboeken voor beleids controle, statische analyse of back-up.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) : een flexibel hulp programma voor logboek zoeken en analyse waarmee onbewerkte logboeken kunnen worden geanalyseerd die door een Azure-resource zijn gegenereerd.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Diagnostische logboeken instellen voor een Azure signalerings service

U kunt Diagnostische logboeken weer geven voor de Azure signalerings service. Deze logboeken bieden een uitgebreidere weer gave van de connectiviteit met uw exemplaar van de Azure signalerings service. De diagnostische logboeken bevatten gedetailleerde informatie over elke verbinding. Bijvoorbeeld basis informatie (gebruikers-ID, verbindings-ID en transport type enzovoort) en gebeurtenis gegevens (verbinding maken, verbinding verbreken en afbreken) van de verbinding. Diagnostische logboeken kunnen worden gebruikt voor het identificeren van problemen, het bijhouden van verbindingen en de analyse.

### <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Diagnostische logboeken zijn standaard uitgeschakeld. Volg deze stappen zodat logboeken met diagnostische gegevens:

1.  Klik in de [Azure Portal](https://portal.azure.com)onder **bewaking**op **Diagnostische instellingen**.

    ![Deel venster navigatie naar Diagnostische instellingen](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1.  Klik vervolgens op **Diagnostische instelling toevoegen**.

    ![Diagnostische logboeken toevoegen](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1.  Stel het gewenste archief doel in. Momenteel ondersteunen we **archiveren in een opslag account** en **verzenden naar log Analytics**.

1. Selecteer de logboeken die u wilt archiveren.

    ![Het deelvenster Diagnostische instellingen](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1.  De nieuwe instellingen voor diagnostische gegevens opslaan.

Nieuwe instellingen van kracht in ongeveer 10 minuten. Hierna logboeken worden weergegeven in de geconfigureerde archivering doel, in de **diagnoselogboeken** deelvenster.

Zie voor meer informatie over het configureren van diagnostische gegevens over de [overzicht van diagnostische logboeken in Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Categorieën van diagnostische logboeken

Met de Azure signalerings service worden Diagnostische logboeken in één categorie vastgelegd:

* **Alle logboeken**: verbindingen volgen die verbinding maken met de Azure signalerings service. De logboeken bevatten informatie over de verbinding maken/verbreken, authenticatie en beperken. Zie de volgende sectie voor meer informatie.

### <a name="archive-to-a-storage-account"></a>Archiveren naar een opslagaccount

Logboeken worden opgeslagen in het opslag account dat is geconfigureerd in het deel venster **Diagnostische logboeken** . Er wordt automatisch een container met de naam `insights-logs-alllogs` gemaakt om Diagnostische logboeken op te slaan. In de container worden Logboeken opgeslagen in het bestand `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. In principe wordt het pad gecombineerd door `resource ID` en `Date Time`. De logboek bestanden zijn gesplitst door `hour`. Daarom worden de minuten altijd `m=00`.

Alle logboeken worden opgeslagen in JavaScript Object Notation (JSON)-indeling. Elk item heeft tekenreeksvelden die gebruikmaken van de indeling die wordt beschreven in de volgende secties.

De JSON-teken reeksen van het archief logboek bevatten elementen die in de volgende tabellen worden weer gegeven:

**Formatteer**

Name | Beschrijving
------- | -------
tijd | Tijd van logboek gebeurtenis
level | Niveau van logboek gebeurtenis
resourceId | Resource-ID van uw Azure signalerings service
location | Locatie van uw Azure signalerings service
category | De categorie van de logboek gebeurtenis
operationName | De naam van de bewerking van de gebeurtenis
callerIpAddress | IP-adres van uw server/client
properties | Gedetailleerde eigenschappen die betrekking hebben op deze logboek gebeurtenis. Zie de onderstaande tabel eigenschappen voor meer informatie.

**Eigenschappen tabel**

Name | Beschrijving
------- | -------
type | Het type logboek gebeurtenis. Op dit moment bieden we informatie over de verbinding met de Azure signalerings service. Alleen `ConnectivityLogs` type is beschikbaar
verzameling | Verzameling van de logboek gebeurtenis. Toegestane waarden zijn: `Connection`, `Authorization` en `Throttling`
connectionId | Identiteit van de verbinding
Transport type | Het transport type van de verbinding. Toegestane waarden zijn: `Websockets` \| `ServerSentEvents` \| `LongPolling`
Connection type | Het type verbinding. Toegestane waarden zijn: `Server` \| `Client`. `Server`: verbinding vanaf server zijde; `Client`: verbinding van client zijde
userId | Identiteit van de gebruiker
message | Gedetailleerd bericht van logboek gebeurtenis

De volgende code is een voorbeeld van een logboek archiveren JSON-tekenreeks:

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

### <a name="archive-logs-schema-for-log-analytics"></a>Schema voor archief logboeken voor Log Analytics

Als u Diagnostische logboeken wilt weer geven, voert u de volgende stappen uit:

1. Klik op `Logs` in de doel Log Analytics.

    ![Menu-item Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Voer `SignalRServiceDiagnosticLogs` en selecteer tijds bereik voor het opvragen van Diagnostische logboeken. Zie [aan de slag met log Analytics in azure monitor](../azure-monitor/log-query/get-started-portal.md) voor geavanceerde query's.

    ![Log Analytics van de query aanmelden](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

De kolommen van het archief logboek bevatten elementen die in de volgende tabel worden weer gegeven:

Name | Beschrijving
------- | ------- 
TimeGenerated | Tijd van logboek gebeurtenis
Verzameling | Verzameling van de logboek gebeurtenis. Toegestane waarden zijn: `Connection`, `Authorization` en `Throttling`
OperationName | De naam van de bewerking van de gebeurtenis
Locatie | Locatie van uw Azure signalerings service
Niveau | Niveau van logboek gebeurtenis
CallerIpAddress | IP-adres van uw server/client
Bericht | Gedetailleerd bericht van logboek gebeurtenis
UserID | Identiteit van de gebruiker
ConnectionId | Identiteit van de verbinding
Connection type | Het type verbinding. Toegestane waarden zijn: `Server` \| `Client`. `Server`: verbinding vanaf server zijde; `Client`: verbinding van client zijde
Transport type | Het transport type van de verbinding. Toegestane waarden zijn: `Websockets` \| `ServerSentEvents` \| `LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Problemen oplossen met Diagnostische logboeken

Voor het oplossen van problemen met de Azure signalerings service kunt u Logboeken voor de server/client inschakelen om fouten vast te leggen. Op dit moment worden in de Azure signalerings service Diagnostische logboeken weer gegeven. u kunt ook logboeken voor de service zijde inschakelen.

Als er verbinding is met een onverwachte groei-of verwijderings situatie, kunt u gebruikmaken van diagnostische Logboeken om problemen op te lossen.

Veelvoorkomende problemen zijn vaak de verwachte hoeveelheid wijzigingen in verbindingen, verbindingen bereiken verbindings limieten en autorisatie fout. Zie de volgende secties voor informatie over het oplossen van problemen.

#### <a name="unexpected-connection-number-changes"></a>Onverwachte wijzigingen in het verbindings nummer

##### <a name="unexpected-connection-dropping"></a>Onverwachte verbinding bij verwijderen

Als er onverwachte verbindingen worden gevonden, moet u eerst Logboeken in de service, de server-en client zijde inschakelen.

Als een verbinding wordt verbroken, worden in de diagnostische Logboeken de gebeurtenis voor het verbreken van de verbinding vastgelegd. u ziet `ConnectionAborted` of `ConnectionEnded` in `operationName`.

Het verschil tussen `ConnectionAborted` en `ConnectionEnded` is dat `ConnectionEnded` een verwachte verbreken van de verbinding is die wordt geactiveerd door client-of server zijde. Hoewel de `ConnectionAborted` meestal een onverwachte gebeurtenis voor het weghalen van de verbinding is en de oorzaak van het afbreken wordt weer gegeven in `message`.

De redenen voor afbreken worden weer gegeven in de volgende tabel:

Reden | Beschrijving
------- | ------- 
Limiet voor aantal verbindingen bereikt | Het aantal verbindingen heeft de limiet van uw huidige prijs categorie bereikt. Overweeg service-eenheid omhoog te schalen
Toepassings server heeft de verbinding gesloten | App server activeert de Abortion. Het kan worden beschouwd als een verwacht Abortion
Time-out voor verbinding ping | Dit wordt meestal veroorzaakt door een netwerk probleem. Overweeg de beschik baarheid van uw app-server op internet te controleren
Service opnieuw laden, opnieuw verbinding maken | De Azure signalerings service wordt opnieuw geladen. Azure-Signa lering ondersteunt automatisch opnieuw verbinden, u kunt wachten tot opnieuw verbinding is gemaakt of hand matig opnieuw verbinding maken met de Azure signalerings service
Tijdelijke fout met interne server | Er is een tijdelijke fout opgetreden in de Azure signalerings service. deze moet automatisch worden hersteld
Server verbinding verbroken | De server verbinding is verbroken met een onbekende fout. Overweeg het probleem zelf op te lossen met Service/Server/client-logboek eerst. Probeer basis problemen op te lossen (bijvoorbeeld netwerk probleem, probleem met de app-server zijde, enzovoort). Als het probleem niet is opgelost, neemt u contact met ons op voor meer hulp. Zie voor meer informatie de sectie [hulp opvragen](#get-help) . 

##### <a name="unexpected-connection-growing"></a>Onverwachte verbinding groeit

Voor het oplossen van problemen met een onverwachte verbinding, moet u eerst de extra verbindingen filteren. U kunt een unieke test gebruiker-ID toevoegen aan de client verbinding testen. Vervolgens kunt u deze in met Diagnostische logboeken controleren als u meer dan één client verbinding met dezelfde test gebruikers-ID of hetzelfde IP-adres hebt, is het waarschijnlijk dat aan de client zijde meer verbindingen worden gemaakt dan verwacht. Controleer de client zijde.

#### <a name="authorization-failure"></a>Autorisatiefout

Als u 401 niet-geautoriseerde retour neren voor client aanvragen, controleert u de diagnostische Logboeken. Als u `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`tegen komt, betekent dit dat alle doel groepen in uw toegangs token ongeldig zijn. Probeer de geldige doel groepen te gebruiken die in het logboek zijn voorgesteld.


#### <a name="throttling"></a>Beperking

Als u merkt dat u geen signalerings client verbindingen met de Azure signalerings service kunt instellen, controleert u de diagnostische Logboeken. Als u in diagnostische logboek `Connection count reaches limit` tegen komt, moet u te veel verbindingen met de signaal service tot stand brengen, waardoor de limiet voor het aantal verbindingen is bereikt. U kunt uw signalerings service verg Roten of verkleinen. Als u in diagnostische logboek `Message count reaches limit` tegen komt, betekent dit dat u de gratis laag gebruikt en de quota van berichten gebruikt. Als u meer berichten wilt verzenden, kunt u eventueel uw signalerings service wijzigen in de laag standaard om extra berichten te verzenden. Zie [prijzen van Azure signalerings Services](https://azure.microsoft.com/pricing/details/signalr-service/)voor meer informatie.

### <a name="get-help"></a>Hulp krijgen

U wordt aangeraden eerst door uzelf problemen op te lossen. De meeste problemen worden veroorzaakt door app-server-of netwerk problemen. Volg de richt lijnen voor het [oplossen van problemen met Diagnostische logboeken](#troubleshooting-with-diagnostic-logs) en de [richt lijnen](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) voor probleem oplossing om de hoofd oorzaak te achterhalen.
Als het probleem nog steeds niet kan worden opgelost, kunt u een probleem openen in GitHub of ticket maken in azure Portal.
Geleverd
1. Tijds bereik ongeveer 30 minuten wanneer het probleem optreedt
2. Resource-ID van de Azure signalerings service
3. Details van probleem, indien mogelijk: appserver verzendt bijvoorbeeld geen berichten, de client verbinding is verbroken.
4. Logboeken die zijn verzameld van Server/client, en ander materiaal dat nuttig kan zijn
5. Beschrijving Reproduceren-code

> Opmerking: als u probleem in GitHub opent, moet u uw gevoelige informatie (bijvoorbeeld bron-ID, Server/client-Logboeken) privé houden, alleen verzenden naar leden in micro soft-organisaties.  
