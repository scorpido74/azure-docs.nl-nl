---
title: Stream live met Media Services v3
titleSuffix: Azure Media Services
description: Meer informatie over het streamen van live met Azure Media Services v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 0b6667965ddd1fce30bb2da2593e2a9274b595ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472013"
---
# <a name="tutorial-stream-live-with-media-services"></a>Zelfstudie: Live streamen met Media Services

> [!NOTE]
> Hoewel de zelfstudie [.NET SDK-voorbeelden](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) gebruikt, zijn de algemene stappen hetzelfde voor [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)of andere ondersteunde [SDK's](media-services-apis-overview.md#sdks).

In Azure Media Services zijn [livegebeurtenissen](https://docs.microsoft.com/rest/api/media/liveevents) verantwoordelijk voor het verwerken inhoud voor live streamen. Een livegebeurtenis biedt een invoereindpunt (de URL voor opnemen) dat u vervolgens doorgeeft aan een live-encoder. De livegebeurtenis ontvangt live-invoerstromen van de live-encoder en maakt deze beschikbaar voor streaming via een of meer [streaming-eindpunten](https://docs.microsoft.com/rest/api/media/streamingendpoints). Livegebeurtenissen bieden ook een preview-eindpunt (voorbeeld-URL) dat u kunt gebruiken om een voorbeeld van de stream te bekijken en deze te valideren voordat deze verder wordt verwerkt en geleverd. In deze zelfstudie ziet u hoe u .NET Core gebruikt om een **pass-through**-type van een live-gebeurtenis te maken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Download de voorbeeld-app die in het onderwerp wordt beschreven.
> * Bekijk de code die live streaming uitvoert.
> * Bekijk de gebeurtenis met [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net)Azure Media [Player](https://amp.azure.net/libs/amp/latest/docs/index.html) op .
> * Resources opschonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien:

- Installeer Visual Studio Code of Visual Studio.
- [Een Azure Media Services-account maken](create-account-cli-how-to.md).<br/>Zorg ervoor dat u de waarden onthoudt die u gebruikt voor de naam van de brongroep en de naam van het Media Services-account.
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. Je moet ze gebruiken om toegang te krijgen tot de API.
- Een camera of een apparaat (zoals een laptop) die wordt gebruikt om een gebeurtenis uit te zenden.
- Een on-premises live-encoder die signalen van de camera omzet naar streams die worden verzonden naar de live streamingservice van Media Services, zie [aanbevolen on-premises live encoders](recommended-on-premises-live-encoders.md). De stroom moet de **RTMP**- of **Smooth Streaming**-indeling hebben.

> [!TIP]
> Zorg dat u [Live streamen met Media Services v3](live-streaming-overview.md) hebt gelezen voordat u verder gaat. 

## <a name="download-and-configure-the-sample"></a>Het voorbeeld downloaden en configureren

Gebruik de volgende opdracht om een GitHub-opslagplaats te klonen op uw computer die het .NET-voorbeeld voor het streamen van video bevat:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Het voorbeeld voor live streamen staat in de map [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Open [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) in uw gedownloade project. Vervang de waarden door de referenties die u hebt gekregen van [de toegang tot API's](access-api-cli-how-to.md).

> [!IMPORTANT]
> In dit voorbeeld wordt voor elke resource een uniek achtervoegsel gebruikt. Als je de foutopsporing annuleert of de app beëindigt zonder deze door te voeren, krijg je meerdere live-evenementen in je account. <br/>Zorg dat u de actieve livegebeurtenissen stopt. Anders wordt er **rekening gebracht!**

## <a name="examine-the-code-that-performs-live-streaming"></a>De code bestuderen die live streamen uitvoert

In dit gedeelte worden de functies bekeken die zijn gedefinieerd in het bestand [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) van het project *MediaV3LiveApp*.

Het voorbeeld maakt een uniek achtervoegsel voor elke resource, zodat u geen naambotsingen hebt als u het monster meerdere keren uitvoert zonder op te ruimen.

> [!IMPORTANT]
> In dit voorbeeld wordt voor elke resource een uniek achtervoegsel gebruikt. Als je de foutopsporing annuleert of de app beëindigt zonder deze door te voeren, krijg je meerdere live-evenementen in je account. <br/>
> Zorg dat u de actieve livegebeurtenissen stopt. Anders wordt er **rekening gebracht!**

### <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Als u wilt starten met Media Services API's met .NET, moet u een **AzureMediaServicesClient**-object maken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Een livegebeurtenis maken

In deze sectie wordt beschreven hoe u een livegebeurtenis van het type **pass-through** maakt (LiveEventEncodingType ingesteld op None). Zie [Typen Live-evenementen](live-events-outputs-concept.md#live-event-types)voor meer informatie over de beschikbare typen live-evenementen. 
 
Sommige dingen die u mogelijk wilt opgeven bij het maken van de live-gebeurtenis zijn:

* Locatie mediaservices.
* Het streaming-protocol voor de livegebeurtenis (momenteel worden de protocollen RTMP en Smooth Streaming ondersteund).<br/>U de protocoloptie niet wijzigen terwijl de live-gebeurtenis of de bijbehorende live-uitvoer worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke Live Event voor elk streamingprotocol.  
* IP-beperkingen voor de opname en voorbeeldweergave. U kunt de IP-adressen definiëren die zijn toegestaan om een video van deze livegebeurtenis op te nemen. Toegestane IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld 10.0.0.1), een IP-adresbereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-adresbereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld , ' 10.0.0.1(255.255.252.0)').<br/>Als er geen IP-adressen zijn opgegeven en er geen regeldefinitie is, is er geen IP-adres toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.<br/>De IP-adressen moeten zich in een van de volgende formaten bevinden: IpV4-adres met vier nummers of CIDR-adresbereik.
* Wanneer u de gebeurtenis maakt, u opgeven om deze automatisch te starten. <br/>Wanneer autostart is ingesteld op True, wordt de Live gebeurtenis gestart na het maken ervan. Dat betekent dat de facturering begint zodra het live-evenement wordt gestart. U moet expliciet Stop aanroepen in de resource van de livegebeurtenis om verdere facturering stop te zetten. Zie [Live Event states and billing](live-event-states-billing.md) (Statussen en facturering voor livegebeurtenissen) voor meer informatie.
* Als u een inname-URL voorspellend wilt maken, stelt u de modus 'ijdelheid' in. Zie [Live Event-inname van URL's voor](live-events-outputs-concept.md#live-event-ingest-urls)meer informatie.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>URL’s voor opnemen ophalen

Zodra het Live Event is gemaakt, u url's krijgen die u aan de live-encoder verstrekt. Het coderingsprogramma gebruikt deze URL's voor het invoeren van een live stream.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>De voorbeeld-URL ophalen

Gebruik het previewEndpoint als u een voorbeeld wilt bekijken en wilt controleren of de invoer van de encoder daadwerkelijk wordt ontvangen.

> [!IMPORTANT]
> Zorg ervoor dat de video naar de URL van Voorbeeld stroomt voordat u verdergaat.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Livegebeurtenissen en live-uitvoer maken en beheren

Wanneer de stream naar de livegebeurtenis stroomt, kunt u de streaming-gebeurtenis starten door een Asset, live-uitvoer en streaming-locator te maken. Hiermee wordt de stream gearchiveerd en beschikbaar gesteld aan kijkers via het streaming-eindpunt.

#### <a name="create-an-asset"></a>Een Asset maken

Maak een Asset die u met de live-uitvoer wilt gebruiken.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Een live-uitvoer maken

Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd. Wanneer u de live-uitvoer verwijdert, verwijdert u de onderliggende asset en inhoud in het item niet.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Een streaming-locator maken

> [!NOTE]
> Wanneer uw Media Services-account wordt gemaakt, wordt een **standaard** eindpunt voor streaming toegevoegd aan uw account in de status **Gestopt.** Als u inhoud wilt streamen en gebruik wilt maken van [dynamische pakketten](dynamic-packaging-overview.md) en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben.

Als u de Asset van de live-uitvoer publiceert met een streaming-locator, blijft de livegebeurtenis (tot de maximale DVR-duur) zichtbaar tot de streaming-locator verloopt of wordt verwijderd (welke het eerst van toepassing is).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Als u klaar bent met het streamen van gebeurtenissen en de eerder ingerichte resources wilt opschonen, volgt u de volgende procedure:

* Stop het pushen van de stream vanuit het coderingsprogramma.
* Stop de livegebeurtenis. Zodra het live-evenement is gestopt, worden er geen kosten in rekening gebracht. Als u het kanaal opnieuw wilt starten, wordt dezelfde URL voor opnemen gebruikt, zodat u het coderingsprogramma niet opnieuw hoeft te configureren.
* U kunt uw streaming-eindpunt stoppen, tenzij u het archief van uw live gebeurtenis wilt blijven leveren als stream op aanvraag. Als het live-evenement in een gestopte status is, worden er geen kosten in rekening gebracht.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>De gebeurtenis bekijken

Als u de gebeurtenis wilt bekijken, kopieert u de streaming-URL die u hebt gekregen toen u code hebt uitgevoerd die is beschreven in Een streaminglocator maken. U gebruik maken van een mediaspeler van uw keuze. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) is beschikbaar https://ampdemo.azureedge.netom uw stream te testen op .

De livegebeurtenis wordt automatisch geconverteerd naar inhoud op aanvraag wanneer deze wordt gestopt. Zelfs nadat u de gebeurtenis hebt gestopt en verwijderd, kunnen gebruikers uw gearchiveerde inhoud als video op aanvraag streamen zolang u het item niet verwijdert. Een item kan niet worden verwijderd als het door een gebeurtenis wordt gebruikt. de gebeurtenis moet eerst worden verwijderd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Zolang de livegebeurtenis loopt, worden er kosten in rekening gebracht. Als het project/programma vastloopt of om welke reden dan ook wordt afgesloten, is het mogelijk dat de livegebeurtenis in een factureringsstaat staat.

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

[Bestanden streamen](stream-files-tutorial-with-api.md)
 
