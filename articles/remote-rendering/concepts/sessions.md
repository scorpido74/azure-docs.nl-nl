---
title: Remote Rendering-sessies
description: Beschrijft wat een externe rendering-sessie is
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 509375459d019ead5a7992b808044a75e2666393
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83758857"
---
# <a name="remote-rendering-sessions"></a>Remote Rendering-sessies

In azure remote rendering (ARR) is een *sessie* een belang rijk concept. In dit artikel wordt uitgelegd wat een sessie precies is.

## <a name="overview"></a>Overzicht

Externe rendering van Azure werkt door het offloaden van complexe weergave taken in de Cloud. Deze rendering-taken kunnen niet worden afgehandeld door alleen een server, omdat de meeste Cloud servers geen Gpu's hebben. Vanwege de hoeveelheid betrokken gegevens en de harde nood zaak van het produceren van resultaten met een interactieve frame snelheid, kan de verantwoordelijkheid die de server afhandelt dat de gebruikers aanvraag ook niet aan een andere computer kan worden verstrekt, op voor waarde dat dit mogelijk is voor veelvoorkomende webverkeer.

Dit betekent dat wanneer u de externe rendering van Azure gebruikt, een Cloud Server met de benodigde hardware-functionaliteit alleen moet worden gereserveerd om uw rendering-aanvragen af te handelen. Een *sessie* verwijst naar alles met betrekking tot de interactie met deze server. Het begint met de eerste aanvraag om een computer te reserveren (*leasen*) voor uw gebruik, gaat door met alle opdrachten voor het laden en bewerken van modellen en eindigt met het vrijgeven van de lease op de Cloud Server.

## <a name="managing-sessions"></a>Sessies beheren

Er zijn meerdere manieren om sessies te beheren en te gebruiken. De taal onafhankelijke manier om sessies te maken, bij te werken en af te sluiten, is via [de sessie beheer rest API](../how-tos/session-rest-api.md). In C# en C++ worden deze bewerkingen weer gegeven via de klassen `AzureFrontend` en `AzureSession` . Voor Unity-toepassingen zijn er verdere hulp functies van het `ARRServiceUnity` onderdeel beschikbaar.

Zodra u *verbinding hebt gemaakt* met een actieve sessie, worden bewerkingen zoals het [laden van modellen](models.md) en interactie met de scène weer gegeven via de- `AzureSession` klasse.

### <a name="managing-multiple-sessions-simultaneously"></a>Meerdere sessies tegelijk beheren

Het is niet mogelijk om volledig *verbinding te maken* met meerdere sessies vanaf één apparaat. U kunt echter zoveel sessies maken, observeren en afsluiten als u maar wilt van één toepassing. Zolang de app niet is bedoeld om ooit verbinding te maken met een sessie, hoeft deze niet te worden uitgevoerd op een apparaat zoals HoloLens 2. Een gebruiks voorbeeld voor een dergelijke implementatie is mogelijk als u sessies wilt beheren via een centraal mechanisme. Een voor beeld: een web-app waarmee meerdere Tablets en HoloLenses zich kunnen aanmelden. Vervolgens kan de app opties weer geven op de tablets, zoals welk CAD-model u wilt weer geven. Als een gebruiker een selectie maakt, wordt deze informatie aan alle HoloLenses gecommuniceerd om een gedeelde ervaring te creëren.

## <a name="session-phases"></a>Sessie fasen

Elke sessie ondergaat meerdere fasen.

### <a name="session-startup"></a>Sessie starten

Wanneer u ARR vraagt om [een nieuwe sessie te maken](../how-tos/session-rest-api.md#create-a-session), is het voor de eerste keer dat u een sessie- [uuid](https://en.wikipedia.org/wiki/Universally_unique_identifier)retourneert. Met deze UUID kunt u informatie opvragen over de sessie. De UUID en enkele basis informatie over de sessie worden gedurende 30 dagen bewaard, zodat u deze gegevens zelfs nadat de sessie is gestopt, kunt opvragen. Op dit moment wordt de **sessie status** gerapporteerd als **starten**.

Vervolgens probeert de externe rendering van Azure een server te vinden die uw sessie kan hosten. Er zijn twee para meters voor deze zoek opdracht. Eerst worden alleen servers in uw [regio](../reference/regions.md)gereserveerd. Dat komt doordat de netwerk latentie voor verschillende regio's te hoog is om een goede ervaring te garanderen. De tweede factor is de gewenste *grootte* die u hebt opgegeven. In elke regio is er een beperkt aantal servers dat kan voldoen aan de aanvraag voor de *standaard* -of *Premium* -grootte. Als alle servers van de gevraagde grootte momenteel worden gebruikt in uw regio, mislukt het maken van de sessie. De reden voor de fout [kan worden opgevraagd](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Als u een *standaard* -VM-grootte aanvraagt en de aanvraag mislukt als gevolg van een hoge vraag, betekent dat niet dat er een *Premium* -server kan worden aangevraagd. Als dit een optie voor u is, kunt u proberen terug te gaan naar een *Premium* -VM.

Wanneer de service een geschikte server vindt, moet de juiste virtuele machine (VM) ernaar worden gekopieerd om deze in te scha kelen op een externe rendering-host van Azure. Dit proces duurt enkele minuten. Daarna wordt de virtuele machine opgestart en verandert de **sessie status** in **gereed**.

Op dit moment wacht de server alleen op uw invoer. Dit is ook het punt van waaruit de service wordt gefactureerd.

### <a name="connecting-to-a-session"></a>Verbinding maken met een sessie

Zodra de sessie is *voltooid*, kunt u er *verbinding mee maken* . Tijdens de verbinding kan het apparaat opdrachten verzenden om modellen te laden en te wijzigen. Elke ARR-host fungeert nooit altijd één client apparaat tegelijk, dus wanneer een client verbinding maakt met een sessie, heeft deze exclusieve controle over de gerenderde inhoud. Dit betekent ook dat de prestatie van de rendering nooit kan verschillen voor de oorzaken van het besturings element.

> [!IMPORTANT]
> Hoewel er slechts één client *verbinding kan maken* met een sessie, kan basis informatie over sessies, zoals de huidige status, worden opgevraagd zonder verbinding te maken.

Wanneer een apparaat is verbonden met een sessie, mislukken pogingen van andere apparaten om verbinding te maken. Wanneer het aangesloten apparaat echter wordt losgekoppeld, hetzij vrijwillig of als gevolg van een soort fout, accepteert de sessie een andere verbindings aanvraag. Alle vorige statussen (geladen modellen en dergelijke) worden verwijderd, waardoor het volgende aangesloten apparaat een schone pastel krijgt. Sessies kunnen daarom vaak opnieuw worden gebruikt, door verschillende apparaten. het is mogelijk dat de opstart overhead van de sessie van de eind gebruiker in de meeste gevallen kan worden verborgen.

> [!IMPORTANT]
> De externe server wijzigt nooit de status van client-side gegevens. Alle mutaties van gegevens (zoals updates voor trans formatie en laad aanvragen) moeten worden uitgevoerd door de client toepassing. Alle acties werken onmiddellijk de client status bij.

### <a name="session-end"></a>Einde van sessie

Wanneer u een nieuwe sessie aanvraagt, geeft u een *maximum lease tijd*op, meestal binnen het bereik van een tot acht uur. Dit is de duur waarin de host uw invoer accepteert.

Er zijn twee regel matige redenen om een sessie te beëindigen. U vraagt de sessie hand matig te stoppen of de maximale lease tijd verloopt. In beide gevallen wordt een actieve verbinding met de host meteen gesloten en wordt de service afgesloten op die server. De server wordt vervolgens teruggegeven aan de Azure-groep en kan worden gevraagd voor andere doel einden. Het stoppen van een sessie kan niet ongedaan worden gemaakt of worden geannuleerd. Het uitvoeren van een query op de **sessie status** van een gestopte sessie is **gestopt** of **verlopen**, afhankelijk van of deze hand matig is afgesloten of omdat de maximale lease tijd is bereikt.

Een sessie kan ook worden gestopt wegens een storing.

In alle gevallen wordt u niet meer gefactureerd wanneer een sessie wordt gestopt.

> [!WARNING]
> Hiermee wordt aangegeven of u verbinding maakt met een sessie en hoe lang dit geen invloed heeft op de facturering. Wat u betaalt voor de service is afhankelijk van de *sessie duur*, dat wil zeggen de tijd dat een server uitsluitend voor u is gereserveerd en de aangevraagde hardware-mogelijkheden (de VM-grootte). Als u een sessie start, maakt u vijf minuten verbinding en stopt u de sessie, zodat deze actief blijft totdat de lease verloopt, wordt u gefactureerd voor de volledige sessie lease tijd. De *maximale lease tijd* is daarentegen doorgaans een veiligheids-net. Het maakt niet uit of u een sessie met een lease tijd van acht uur aanvraagt en deze vervolgens vijf minuten gebruikt, als u de sessie later hand matig stopt.

#### <a name="extend-a-sessions-lease-time"></a>De lease tijd van een sessie verlengen

U kunt [de lease tijd](../how-tos/session-rest-api.md#update-a-session) van een actieve sessie verlengen als deze uitvalt.

## <a name="example-code"></a>Voorbeeldcode

In de volgende code ziet u een eenvoudige implementatie van het starten van een sessie, het wachten op de status *gereed* , verbinding maken en de verbinding verbreken en vervolgens opnieuw.

```cs
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

Meerdere `AzureFrontend` en `AzureSession` exemplaren kunnen worden onderhouden, gemanipuleerd en uit code worden opgevraagd. Maar er kan slechts één apparaat tegelijk verbinding maken `AzureSession` .

De levens duur van een virtuele machine is niet gekoppeld aan het `AzureFrontend` exemplaar of het `AzureSession` exemplaar. `AzureSession.StopAsync`moet worden aangeroepen om een sessie te stoppen.

De permanente sessie-ID kan worden opgevraagd `AzureSession.SessionUUID()` en lokaal in de cache worden opgeslagen. Met deze ID kan een toepassing een `AzureFrontend.OpenSession` binding met die sessie aanroepen.

Wanneer `AzureSession.IsConnected` True is, `AzureSession.Actions` retourneert een exemplaar van `RemoteManager` , dat de functies bevat voor het [laden van modellen](models.md), het bewerken van [entiteiten](entities.md)en het [opvragen van informatie](../overview/features/spatial-queries.md) over de gerenderde scène.

## <a name="next-steps"></a>Volgende stappen

* [Entiteiten](entities.md)
* [Modellen](models.md)
