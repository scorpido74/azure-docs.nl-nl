---
title: Externe renderingsessies
description: Beschrijft wat een sessie op afstand renderen is
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681686"
---
# <a name="remote-rendering-sessions"></a>Externe renderingsessies

In Azure Remote Rendering (ARR) is een *sessie* een belangrijk concept. In dit artikel wordt uitgelegd wat een sessie precies is.

## <a name="overview"></a>Overzicht

Azure Remote Rendering werkt door complexe renderingtaken in de cloud te ontladen. Deze renderingtaken kunnen niet door zomaar een server worden uitgevoerd, omdat de meeste cloudservers geen GPU's hebben. Vanwege de hoeveelheid gegevens die betrokken zijn en de harde eis van het produceren van resultaten bij interactieve frame rates, de verantwoordelijkheid welke server behandelt die gebruiker verzoek ook niet kan worden overhandigd aan een andere machine on-the-fly, zoals mogelijk is voor meer voorkomende webverkeer.

Dat betekent dat wanneer u Azure Remote Rendering gebruikt, een cloudserver met de benodigde hardwaremogelijkheden uitsluitend moet worden gereserveerd om uw renderingverzoeken af te handelen. Een *sessie* verwijst naar alles wat met de interactie met deze server te maken heeft. Het begint met het eerste verzoek om *(lease)* een machine te reserveren voor uw gebruik, gaat verder met alle opdrachten voor het laden en manipuleren van modellen en eindigt met het vrijgeven van de lease op de cloudserver.

## <a name="managing-sessions"></a>Sessies beheren

Er zijn meerdere manieren om sessies te beheren en ermee om te gaan. De taalonafhankelijke manier om sessies te maken, bij te werken en af te sluiten is via [de REST API voor sessiebeheer.](../how-tos/session-rest-api.md) In C# en C++ worden deze bewerkingen weergegeven via de klassen `AzureFrontend` en `AzureSession`. Voor Unity-toepassingen zijn er nog `ARRServiceUnity` andere hulpprogrammafuncties die door het onderdeel worden geleverd.

Zodra u *bent verbonden met* een actieve sessie, worden bewerkingen zoals [laadmodellen](models.md) en interactie met de scène via de `AzureSession` klasse weergegeven.

### <a name="managing-multiple-sessions-simultaneously"></a>Meerdere sessies tegelijk beheren

Het is niet mogelijk om vanaf één apparaat volledig verbinding te *maken met* meerdere sessies. U echter zoveel sessies maken, observeren en afsluiten als u wilt vanuit één toepassing. Zolang de app niet bedoeld is om ooit verbinding te maken met een sessie, hoeft deze ook niet te draaien op een apparaat als HoloLens 2. Een use case voor een dergelijke implementatie kan zijn als u sessies via een centraal mechanisme wilt controleren. Je zou bijvoorbeeld een web-app kunnen bouwen, waar meerdere tablets en HoloLenses op kunnen inloggen. Vervolgens kan de app opties weergeven op de tablets, zoals welk CAD-model moet worden weergegeven. Als een gebruiker een selectie maakt, wordt deze informatie doorgegeven aan alle HoloLenses om een gedeelde ervaring te creëren.

## <a name="session-phases"></a>Sessiefasen

Elke sessie ondergaat meerdere fasen.

### <a name="session-startup"></a>Opstarten van sessie

Wanneer u ARR vraagt om een nieuwe sessie te [maken,](../how-tos/session-rest-api.md#create-a-session)is het eerste wat het doet om een sessie [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)terug te sturen. Met deze UUID u informatie over de sessie opvragen. De UUID en sommige basisinformatie over de sessie blijven 30 dagen bestaan, zodat u die informatie ook opvragen nadat de sessie is gestopt. Op dit moment wordt de **sessiestatus** gerapporteerd als **Start**.

Vervolgens probeert Azure Remote Rendering een server te vinden die uw sessie kan hosten. Er zijn twee parameters voor deze zoekopdracht. Ten eerste zal het alleen servers reserveren in uw [regio.](../reference/regions.md) Dat komt omdat de netwerklatentie tussen regio's mogelijk te hoog is om een fatsoenlijke ervaring te garanderen. De tweede factor is de gewenste *grootte* die u hebt opgegeven. In elke regio is er een beperkt aantal servers die kunnen voldoen aan de *standaard-* of *Premium-aanvraag.* Als alle servers van de gevraagde grootte momenteel in uw regio worden gebruikt, mislukt het maken van sessies. De reden voor het mislukken [kan worden opgevraagd.](../how-tos/session-rest-api.md#get-sessions-properties)

> [!IMPORTANT]
> Als u een *standaard* VM-grootte aanvraagt en de aanvraag mislukt vanwege de grote vraag, betekent dit niet dat het aanvragen van een *Premium-server* ook mislukt. Dus als het een optie voor u is, u proberen terug te vallen naar een *Premium* VM.

Wanneer de service een geschikte server vindt, moet deze de juiste virtuele machine (VM) kopiëren om deze om te zetten in een Azure Remote Rendering-host. Dit proces duurt enkele minuten. Daarna wordt de VM opgestart en wordt de **sessiestatus** overgezet naar **Ready**.

Op dit moment wacht de server uitsluitend op uw invoer. Dit is ook het punt van waaruit op u voor de service gefactureerd.

### <a name="connecting-to-a-session"></a>Verbinding maken met een sessie

Zodra de sessie *klaar*is, u *verbinding maken.* Terwijl het apparaat is verbonden, kan het opdrachten verzenden om modellen te laden en te wijzigen. Elke ARR-host bedient slechts één clientapparaat tegelijk, dus wanneer een client verbinding maakt met een sessie, heeft hij exclusieve controle over de gerenderde inhoud. Dat betekent ook dat de weergaveprestaties nooit zullen variëren om redenen buiten uw controle.

> [!IMPORTANT]
> Hoewel slechts één client verbinding kan *maken met* een sessie, kan basisinformatie over sessies, zoals de huidige status, worden opgevraagd zonder verbinding te maken.

Terwijl een apparaat is verbonden met een sessie, mislukken pogingen van andere apparaten om verbinding te maken. Zodra het aangesloten apparaat echter de verbinding verbreekt, vrijwillig of als gevolg van een storing, accepteert de sessie een ander verbindingsverzoek. Alle vorige status (geladen modellen en dergelijke) wordt verwijderd zodanig dat de volgende verbinding apparaat krijgt een schone lei. Zo kunnen sessies vele malen worden hergebruikt, door verschillende apparaten en kan het mogelijk zijn om de sessie opstarten overhead te verbergen van de eindgebruiker in de meeste gevallen.

> [!IMPORTANT]
> De externe server wijzigt nooit de status van clientgegevens. Alle mutaties van gegevens (zoals transformatie-updates en laadverzoeken) moeten worden uitgevoerd door de clienttoepassing. Alle acties werken de clientstatus onmiddellijk bij.

### <a name="session-end"></a>Einde sessie

Wanneer u een nieuwe sessie aanvraagt, geeft u een *maximale leasetijd*op, meestal in het bereik van één tot acht uur. Dit is de duur waarin de host uw inbreng accepteert.

Er zijn twee regelmatige redenen om een sessie te beëindigen. Of u vraagt handmatig om de sessie te stoppen of de maximale leasetijd verloopt. In beide gevallen wordt elke actieve verbinding met de host meteen gesloten en wordt de service op die server afgesloten. De server wordt vervolgens teruggegeven aan de Azure-groep en kan worden gevorderd voor andere doeleinden. Het stoppen van een sessie kan niet ongedaan worden gemaakt of geannuleerd. Als u de **sessiestatus** opeenstelt op een gestopte sessie, wordt **gestopt** of **verlopen,** afhankelijk van of deze handmatig is afgesloten of omdat de maximale leasetijd is bereikt.

Een sessie kan ook worden gestopt vanwege een storing.

In alle gevallen wordt u niet verder gefactureerd zodra een sessie is gestopt.

> [!WARNING]
> Of u verbinding maakt met een sessie en hoe lang, heeft geen invloed op facturering. Wat u voor de service betaalt, is afhankelijk van de *sessieduur,* dat betekent de tijd dat een server uitsluitend voor u is gereserveerd en de gevraagde hardwaremogelijkheden (de VM-grootte). Als u een sessie start, vijf minuten verbinding maakt en de sessie vervolgens niet stopt, zodat deze blijft draaien totdat de lease verloopt, wordt u gefactureerd voor de volledige leaseperiode. Omgekeerd is de *maximale leasetijd* meestal een vangnet. Het maakt niet uit of u een sessie aanvraagt met een leasetijd van acht uur, en deze dan slechts vijf minuten gebruikt, als u de sessie daarna handmatig stopt.

#### <a name="extend-a-sessions-lease-time"></a>De leasetijd van een sessie verlengen

U [de leasetijd](../how-tos/session-rest-api.md#update-a-session) van een actieve sessie verlengen, als blijkt dat u deze langer nodig hebt.

## <a name="example-code"></a>Voorbeeldcode

De onderstaande code toont een eenvoudige implementatie van het starten van een sessie, wachten op de *klaar* staat, verbinding maken, en vervolgens loskoppelen en afsluiten weer.

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Meerdere `AzureFrontend` `AzureSession` exemplaren en instanties kunnen worden onderhouden, gemanipuleerd en opgevraagd vanuit code. Maar slechts één apparaat kan `AzureSession` verbinding maken met een tegelijk.

De levensduur van een virtuele machine `AzureFrontend` is niet `AzureSession` gekoppeld aan de instantie of de instantie. `AzureSession.StopAsync`moet worden opgeroepen om een sessie te stoppen.

De permanente sessie-ID kan `AzureSession.SessionUUID()` lokaal worden opgevraagd en in de cache worden opgeslagen. Met deze ID kan `AzureFrontend.OpenSession` een toepassing bellen om zich aan die sessie te binden.

Wanneer `AzureSession.IsConnected` dit `AzureSession.Actions` waar is, `RemoteManager`retourneert u een instantie van , die de functies bevat om modellen te [laden,](models.md) [entiteiten](entities.md)te manipuleren en informatie over de gerenderde scène [op te vragen.](../overview/features/spatial-queries.md)

## <a name="next-steps"></a>Volgende stappen

* [Entiteiten](entities.md)
* [Modellen](models.md)
