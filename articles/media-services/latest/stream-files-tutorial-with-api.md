---
title: Uploaden, coderen en streamen met Media Services v3
titleSuffix: Azure Media Services
description: Zelfstudie met informatie over het uploaden van een bestand, het coderen van video en het streamen van inhoud met Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: juliako
ms.openlocfilehash: 4e40d26e392219fb751328bc54855d87e80bae19
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346000"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Zelfstudie: Video's uploaden, coderen en streamen met Media Services v3

> [!NOTE]
> Hoewel deze zelfstudie [.NET SDK-voorbeelden](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) gebruikt, zijn de algemene stappen hetzelfde voor [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)of andere ondersteunde [SDK's.](media-services-apis-overview.md#sdks)

Met Azure Media Services u uw mediabestanden coderen in indelingen die worden afgespeeld op een breed scala aan browsers en apparaten. Zo kunt u bijvoorbeeld inhoud streamen in de indelingen Apple HLS of MPEG DASH. Voordat u gaat streamen, moet u uw digitale mediabestand van hoge kwaliteit coderen. Zie [Coderingsconcept voor](encoding-concept.md)hulp bij het coderen. In deze zelfstudie wordt een lokaal videobestand geüpload en wordt het geüploade bestand gecodeerd. U ook inhoud coderen die u toegankelijk maakt via een HTTPS-URL. Zie [Taakinvoer maken via een HTTP(s)-URL](job-input-from-http-how-to.md) voor meer informatie.

![Een video afspelen met Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Download de voorbeeld-app die in het onderwerp wordt beschreven.
> * Controleer de code die uploadt, codeert en streamt.
> * Voer de app uit.
> * Test de streaming-URL.
> * Resources opschonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- Als u Visual Studio niet hebt geïnstalleerd, u [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)krijgen.
- [Een Azure Media Services-account maken](create-account-cli-how-to.md).<br/>Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. Je moet ze gebruiken om toegang te krijgen tot de API.

## <a name="download-and-set-up-the-sample"></a>Het voorbeeld downloaden en instellen

Kloon een GitHub-repository met het streaming .NET-voorbeeld naar uw machine met de volgende opdracht:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Het voorbeeld bevindt zich de map [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Open [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) in uw gedownloade project. Vervang de waarden door referenties die u hebt verkregen via [toegang tot API's](access-api-cli-how-to.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>De code controleren die wordt geüpload, gecodeerd en gestreamd

In dit gedeelte worden de functies bekeken die zijn gedefinieerd in het bestand [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) van het project *UploadEncodeAndStreamFiles*.

In het voorbeeld worden de volgende acties uitgevoerd:

1. Er wordt een nieuwe **transformatie** gemaakt (eerst wordt gecontroleerd of de opgegeven transformatie bestaat).
2. Hiermee maakt u een **uitvoeritem** dat wordt gebruikt als de uitvoer van de **coderingstaak.**
3. Er wordt een invoer**asset** gemaakt en het opgegeven lokale videobestand wordt erin geladen. De asset wordt gebruikt als de invoer voor de taak.
4. De coderingstaak wordt met behulp van de gemaakte invoer en uitvoer verzonden.
5. De status van de taak controleren.
6. Er wordt een **streaming-locator** gemaakt.
7. Er worden streaming-URL's samengesteld.

### <a name="start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />Starten met het gebruik van Media Services API's met .NET SDK

Als u wilt starten met Media Services API's met .NET, moet u een **AzureMediaServicesClient**-object maken. Als u het object wilt maken, moet u referenties leveren die nodig zijn om de client verbinding te maken met Azure via Azure AD. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Een invoerasset maken en er een lokaal bestand in uploaden

Met de functie **CreateInputAsset** wordt een nieuwe [invoerasset](https://docs.microsoft.com/rest/api/media/assets) gemaakt en het opgegeven lokale videobestand wordt hierin geladen. Dit **item** wordt gebruikt als invoer voor uw coderingstaak. In Media Services v3 kan de invoer naar een **taak** een **asset** of inhoud zijn die u via HTTPS-URL's beschikbaar stelt voor uw Media Services-account. Zie [dit](job-input-from-http-how-to.md) artikel voor meer informatie over het coderen van een HTTPS-URL.

In Media Services v3 kunt u Azure Storage-API's gebruiken om bestanden te uploaden. Het volgende .NET-fragment laat zien hoe.

De volgende functie voert deze acties uit:

* Hiermee maakt u een **actief**.
* Hiermee krijgt u een beschrijfbare [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) naar de container van het actief [in opslag.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)

    Als u de [listcontainersas-functie](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) van asset gebruikt om SAS-URL's te krijgen, moet u er rekening mee houden dat de functie meerdere SAS-URL's retourneert omdat er twee opslagaccountsleutels voor elk opslagaccount zijn. Een opslagaccount heeft twee sleutels omdat het zorgt voor een naadloze rotatie van opslagaccountsleutels (bijvoorbeeld de ene keer tijdens het gebruik van de andere, en begint vervolgens de nieuwe sleutel te gebruiken en de andere sleutel te roteren). De 1e SAS URL staat voor storage key1 en de tweede opslagsleutel2.
* Uploadt het bestand naar de container in opslag met behulp van de SAS URL.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Een uitvoerasset maken voor het opslaan van het resultaat van een taak

In de [uitvoerasset](https://docs.microsoft.com/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen. Het project definieert de functie **DownloadResults** die de resultaten van deze uitvoerasset naar de uitvoermap downloadt zodat u kunt zien wat u hebt gekregen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Een transformatie en een taak maken die het geüploade bestand codeert

Bij het coderen of verwerken van inhoud in Media Services is het een veelvoorkomend patroon om de coderingsinstellingen als recept in te stellen. U dient vervolgens een **taak** in te dienen om het recept toe te passen op een video. Door nieuwe vacatures in te dienen voor elke nieuwe video, pas je dat recept toe op alle video's in je bibliotheek. Een recept in Media Services wordt een **Transformatie**genoemd. Zie [Transformeren en taken voor](transform-concept.md)meer informatie. Het voorbeeld dat wordt beschreven in deze zelfstudie definieert een recept dat de video codeert om het te streamen naar tal van iOS- en Android-apparaten.

#### <a name="transform"></a>Transformeren

Bij het maken van een nieuw [transformatie](https://docs.microsoft.com/rest/api/media/transforms)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter is een **TransformOutput**-object, zoals weergegeven in de onderstaande code. Elke **transformatie-uitvoer** bevat een **voorinstelling**. **Voorinstelling** bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste **TransformOutput**. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming **. De voorinstelling codeert de invoervideo in een automatisch gegenereerde bitrate-ladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitsnelheid en produceert ISO MP4-bestanden met H.264-video en AAC-audio die overeenkomen met elk bitrate-resolutiepaar. Zie [een bitrate-ladder automatisch genereren](autogen-bitrate-ladder.md) voor meer informatie over deze voorinstelling.

U kunt een ingebouwde EncoderNamedPreset gebruiken of aangepaste voorinstellingen gebruiken. Zie [Coderingsvoorinstellingen aanpassen](customize-encoder-presets-how-to.md) voor meer informatie.

Bij het maken van een [transformatie](https://docs.microsoft.com/rest/api/media/transforms) moet u controleren of er al een bestaat met de methode **Ophalen** zoals weergegeven in de volgende code. In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Taak

Zoals eerder vermeld, is het [transformatie](https://docs.microsoft.com/rest/api/media/transforms)-object het recept en is de [taak](https://docs.microsoft.com/rest/api/media/jobs) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** bevat informatie zoals de locatie van de invoervideo en de locatie voor de uitvoer.

In dit voorbeeld is de invoervideo geüpload naar uw lokale machine. Zie [dit](job-input-from-http-how-to.md) artikel als u wilt weten hoe u een HTTPS-URL coderen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

De taak neemt enige tijd in beslag en wanneer deze is voltooid, wordt u hiervan op de hoogte gesteld. In het onderstaande codevoorbeeld ziet u hoe de status van de [taak](https://docs.microsoft.com/rest/api/media/jobs) kan worden opgevraagd in de service. Polling is geen aanbevolen aanbevolen best practice voor productie-apps vanwege mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken.

Event Grid is ontworpen voor hoge beschikbaarheid, consistente prestaties en dynamisch schalen. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. Eenvoudige, op HTTP gebaseerde reactieve gebeurtenisafhandeling maakt het mogelijk om efficiënte oplossingen te bouwen met intelligente filtering en routering van gebeurtenissen.  Zie [Gebeurtenissen routeren naar een aangepast eindpunt](job-state-events-cli-how-to.md).

De **taak** doorloopt meestal de volgende statussen: **gepland**, **in wachtrij**, **wordt verwerkt**, **voltooid** (definitieve status). Als bij de taak een fout is opgetreden is, krijgt u de status **Fout**. Als de taak wordt geannuleerd, wordt u **geannuleerd** en **geannuleerd** wanneer deze is uitgevoerd.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Een streaming-locator ophalen

Nadat de codering is voltooid, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U het beschikbaar maken in twee stappen: maak eerst een [streaminglocator](https://docs.microsoft.com/rest/api/media/streaminglocators)en ten tweede de streaming-URL's die clients kunnen gebruiken.

Het proces van het maken van een **streaming-locator** wordt publiceren genoemd. Standaard is de **Streaming Locator** geldig direct nadat u de API-aanroepen hebt gedaan en duurt totdat deze is verwijderd, tenzij u de optionele begin- en eindtijden configureert.

Wanneer u een [StreamingLocator maakt,](https://docs.microsoft.com/rest/api/media/streaminglocators)moet u de gewenste **StreamingPolicyName**opgeven. In dit voorbeeld streamt u in-the-clear (of niet-versleutelde inhoud), zodat het vooraf gedefinieerde duidelijke streamingbeleid **(PredefinedStreamingPolicy.ClearStreamingOnly)** wordt gebruikt.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](https://docs.microsoft.com/rest/api/media/streamingpolicies) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal streaming-beleidsvermeldingen. U moet geen nieuw streamingbeleid maken voor elke streaminglocator.

De volgende code gaat ervan uit dat u de functie aanroept met een unieke locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Terwijl in het voorbeeld in dit onderwerp streaming wordt behandeld, kunt u dezelfde aanroep gebruiken om een streaming-locator te maken voor het leveren van video via progressief downloaden.

### <a name="get-streaming-urls"></a>Streaming-URL's ophalen

Nu de [streaming-locator](https://docs.microsoft.com/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen, zoals weergegeven in **GetStreamingURLs**. Als u een URL wilt samenstellen, moet u de hostnaam van het [streaming-eindpunt](https://docs.microsoft.com/rest/api/media/streamingendpoints) en het pad van de **streaming-locator** samenvoegen. In dit voorbeeld wordt het *standaard* **streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard* **streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

> [!NOTE]
> In deze methode hebt u de locatorName nodig die is gebruikt bij het maken van de **streaming-locator** voor de uitvoerasset.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen, behalve objecten die u opnieuw wilt gebruiken (meestal gebruikt u transformaties opnieuw en blijft u StreamingLocators, enz.) Als u wilt dat uw account schoon is na het experimenteren, verwijdert u de bronnen die u niet opnieuw wilt gebruiken. De volgende code verwijdert bijvoorbeeld Taken:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

1. Druk op Ctrl+F5 om de app *EncodeAndStreamFiles* uit te voeren.
2. Kopieer een van de streaming-URL's van de console.

In dit voorbeeld worden URL's weergegeven die kunnen worden gebruikt om de video met verschillende protocollen af te spelen:

![Voorbeelduitvoer met URL's voor streamingvideo mediaservices](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>De streaming-URL testen

In dit artikel gebruiken we Azure Media Player om de stream te testen.

> [!NOTE]
> Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

1. Open een webbrowser en [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)navigeer naar .
2. Plak in het **vak URL:** een van de streaming URL-waarden die u hebt toen u de app hebt uitgevoerd.
3. Selecteer **Speler bijwerken**.

Azure Media Player kan worden gebruikt voor het testen, maar mag niet worden gebruikt in een productieomgeving.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

De Azure Media Services v3 SDKs zijn niet thread-safe. Bij het ontwikkelen van een multithread-app moet u per thread een nieuw AzureMediaServicesClient-object genereren en gebruiken.

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw video kunt uploaden, coderen en streamen, kunt u doorgaan naar het volgende artikel: 

> [!div class="nextstepaction"]
> [Video's analyseren](analyze-videos-tutorial-with-api.md)
