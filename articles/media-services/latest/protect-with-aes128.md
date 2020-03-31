---
title: Video versleutelen met AES-128
titleSuffix: Azure Media Services
description: Meer informatie over het versleutelen van video met AES 128-bits versleuteling en hoe u de service voor sleutellevering in Azure Media Services gebruiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974169"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Zelfstudie: Video versleutelen met AES-128 en gebruik de service voor het leveren van sleutels

> [!NOTE]
> Hoewel de zelfstudie de [.NET SDK-voorbeelden](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) gebruikt, zijn de algemene stappen hetzelfde voor [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)of andere ondersteunde [SDK's](media-services-apis-overview.md#sdks).

U Media Services gebruiken om HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming versleuteld met de AES te leveren met behulp van 128-bits encryptiesleutels. Media Services biedt ook de belangrijke leveringsservice die versleutelingssleutels levert aan geautoriseerde gebruikers. Als u wilt dat Media Services uw video dynamisch versleutelt, koppelt u de versleutelingssleutel aan een Streaming Locator en configureert u ook het beleid voor inhoudssleutel. Wanneer een stream wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met AES-128. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

U kunt elke asset met meerdere versleutelingstypen versleutelen (AES-128, PlayReady, Widevine, FairPlay). Zie [Streamingprotocollen en versleutelingstypen](content-protection-overview.md#streaming-protocols-and-encryption-types) voor nuttige combinaties. Zie ook [Hoe te beschermen met DRM](protect-with-drm.md).

De uitvoer uit het voorbeeld van dit artikel bevat een URL naar de Azure Media Player, manifest-URL en het AES-token dat nodig is om de inhoud af te spelen. In het voorbeeld wordt de vervaldatum van het JSON-webtoken (JWT) ingesteld op 1 uur. U een browser openen en de resulterende URL plakken om de demopagina van Azure Media ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```Player te starten met de URL en het token dat al voor u is ingevuld in de volgende indeling:.

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Download het [EncryptWithAES-voorbeeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) dat in het artikel wordt beschreven.
> * Ga mediaservices-API's gebruiken met .NET SDK.
> * Maak een uitvoeritem.
> * Maak een coderingstransformatie.
> * Dien een job in.
> * Wacht tot de taak is voltooid.
> * Een inhoudssleutelbeleid maken
> * Configureer het beleid om JWT-tokenbeperking te gebruiken.
> * Maak een streaminglocator.
> * Configureer de streaminglocator om de video te versleutelen met AES (ClearKey).
> * Neem een testtoken.
> * Een streaming-URL bouwen.
> * Resources opschonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien.

* Lees het artikel [Content protection overview](content-protection-overview.md) (Overzicht inhoudsbeveiliging).
* Installeer Visual Studio Code of Visual Studio.
* [Een Azure Media Services-account maken](create-account-cli-quickstart.md).
* Ontvang referenties die nodig zijn om API's van Media Services te gebruiken door [toegangsAPI's te](access-api-cli-how-to.md)volgen.

## <a name="download-code"></a>Code downloaden

Kloon met de volgende opdracht een GitHub-opslagplaats met het volledige .NET-voorbeeld dat in dit artikel wordt beschreven, op de computer:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Het voorbeeld 'Versleutelen met AES-128' bevindt zich in de map [EncryptWithAES.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)

> [!NOTE]
> Het voorbeeld creëert unieke bronnen elke keer dat u de app uitvoert. Doorgaans gebruikt u bestaande bronnen zoals transformaties en beleidsregels opnieuw (als bestaande resources configuraties vereisen).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Als u API's van Media Services wilt gaan gebruiken met .NET, maakt u een **AzureMediaServicesClient-object.** Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Een uitvoeractivum maken  

In de [uitvoerasset](https://docs.microsoft.com/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Een coderingstransformatie verkrijgen of maken

Bij het maken van een nieuw [transformatie](https://docs.microsoft.com/rest/api/media/transforms)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter is een **TransformOutput**-object, zoals weergegeven in de onderstaande code. Elke **transformatie-uitvoer** bevat een **voorinstelling**. **Voorinstelling** bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste **TransformOutput**. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming **. De preset codeert de invoervideo in een automatisch gegenereerde bitrateladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitrate en produceert vervolgens ISO MP4-bestanden met H.264-video en OC-audio die overeenkomen met elk paar met bitrate-resolutie.

Controleer voordat u een nieuwe [transformatie maakt,](https://docs.microsoft.com/rest/api/media/transforms)eerst of er al een bestaat met de methode **Get,** zoals wordt weergegeven in de code die volgt. In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Taak indienen

Zoals eerder vermeld, is het [transformatie](https://docs.microsoft.com/rest/api/media/transforms)-object het recept en is de [taak](https://docs.microsoft.com/rest/api/media/jobs) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** geeft informatie op, zoals de locatie van de invoervideo en de locatie voor de uitvoer.

In deze zelfstudie maken we de invoer van de taak op basis van een bestand dat rechtstreeks wordt ingenomen vanuit een URL van de [HTTPs-bron.](job-input-from-http-how-to.md)

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

De klus duurt enige tijd. Als dat het zo is, wilt u op de hoogte worden gesteld. In het onderstaande codevoorbeeld ziet u hoe de status van de [taak](https://docs.microsoft.com/rest/api/media/jobs) kan worden opgevraagd in de service. Polling is geen aanbevolen aanbevolen best practice voor productie-apps vanwege mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken. Zie [Gebeurtenissen naar een aangepast webeindpunt leiden voor](job-state-events-cli-how-to.md)meer informatie.

De **taak** doorloopt meestal de volgende statussen: **gepland**, **in wachtrij**, **wordt verwerkt**, **voltooid** (definitieve status). Als de taak een fout is opgelopen, krijgt u de **status Fout.** Als de taak wordt geannuleerd, wordt u **geannuleerd** en **geannuleerd** wanneer deze is uitgevoerd.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Een inhoudssleutelbeleid maken

Een inhoudssleutel biedt veilige toegang tot uw assets. U moet een **inhoudssleutelbeleid** maken dat configureert hoe de inhoudssleutel aan eindclients wordt geleverd. De inhoudssleutel is gekoppeld aan de **streaminglocator**. Media Services biedt ook de belangrijke leveringsservice die versleutelingssleutels levert aan geautoriseerde gebruikers.

Wanneer een stream door een speler wordt aangevraagd, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen (in dit geval met Behulp van AES-versleuteling.) Om de stream te decoderen, vraagt de speler de sleutel van de belangrijke leveringsservice. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Een streaming-locator maken

Nadat de codering is voltooid en het inhoudssleutelbeleid is ingesteld, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U maakt de video beschikbaar in twee stappen:

1. Een [streaminglocator maken.](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. De streaming-URL's bouwen die clients kunnen gebruiken.

Het proces van het maken van de **Streaming Locator** heet publiceren. Standaard is de **Streaming Locator** direct geldig nadat u de API-aanroepen hebt gemaakt. Het duurt tot het is verwijderd, tenzij u de optionele begin- en eindtijden configureert.

Wanneer u een [streaminglocator](https://docs.microsoft.com/rest/api/media/streaminglocators)maakt, moet u de gewenste **StreamingPolicyName**opgeven. In deze zelfstudie gebruiken we een van de vooraf gedefinieerde streamingbeleidsregels, waarmee Azure Media Services wordt uitgelegd hoe de inhoud voor streaming moet worden gepubliceerd. In dit voorbeeld wordt de AES Envelope-versleuteling toegepast (deze versleuteling wordt ook wel ClearKey-versleuteling genoemd omdat de sleutel via HTTPS aan de afspeelclient wordt geleverd en geen DRM-licentie).

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid gebruikt,](https://docs.microsoft.com/rest/api/media/streamingpolicies)moet u een beperkte set van dergelijke beleidsregels voor uw Media Service-account ontwerpen en deze opnieuw gebruiken voor uw streaminglocators wanneer dezelfde versleutelingsopties en -protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal StreamingPolicy-vermeldingen. U moet geen nieuw streamingbeleid maken voor elke streaminglocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Een test-token ophalen

In deze zelfstudie geven we voor het inhoudssleutelbeleid op dat het een tokenbeperking heeft. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in het [JWT-formaat](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) en dat is wat we configureren in het voorbeeld.

De ContentKeyIdentifierClaim wordt gebruikt in het **beleid voor inhoudssleutel,** wat betekent dat het token dat wordt gepresenteerd aan de key delivery-service de id van de inhoudssleutel moet bevatten. In het voorbeeld hebben we geen inhoudssleutel opgegeven bij het maken van de Streaming Locator, het systeem heeft een willekeurige voor ons gemaakt. Om het testtoken te genereren, moeten we de ContentKeyId in de ContentKeyIdentifierClaim-claim laten plaatsen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Een DASH-streaming-URL bouwen

Nu de [Streaming Locator](https://docs.microsoft.com/rest/api/media/streaminglocators) is gemaakt, u de streaming URL's krijgen. Als u een URL wilt maken, moet u de hostnaam [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) en het **pad van streaming locator met** zich meebrengt. In dit voorbeeld wordt het *standaard* **streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard* **streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen, behalve objecten die u opnieuw wilt gebruiken (meestal gebruikt u transformaties, streaminglocators, enzovoort). Als u wilt dat uw account schoon is na het experimenteren, verwijdert u de bronnen die u niet opnieuw wilt gebruiken. De volgende code verwijdert bijvoorbeeld Taken:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Beschermen met DRM](protect-with-drm.md)
