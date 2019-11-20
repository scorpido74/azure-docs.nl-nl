---
title: Video versleutelen met AES-128
titleSuffix: Azure Media Services
description: Meer informatie over het versleutelen van video met AES 128-bits versleuteling en het gebruik van de key delivery service in Azure Media Services.
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
ms.openlocfilehash: 3b56747d9bc8c8ae5884d4fb654c20d49527fed5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186076"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Zelf studie: video versleutelen met AES-128 en de key delivery service gebruiken

> [!NOTE]
> Hoewel in de zelf studie de [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) -voor beelden worden gebruikt, zijn de algemene stappen hetzelfde voor [rest API](https://docs.microsoft.com/rest/api/media/liveevents), [cli](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)of andere ondersteunde [sdk's](media-services-apis-overview.md#sdks).

U kunt Media Services gebruiken om HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming te versleutelen met de AES door gebruik te maken van 128-bits coderings sleutels. Media Services biedt ook de key delivery service die versleutelings sleutels levert aan geautoriseerde gebruikers. Als u wilt dat Media Services uw video dynamisch versleutelt, koppelt u de versleutelings sleutel aan een streaming-Locator en configureert u ook het beleid voor de inhouds sleutel. Wanneer een stroom wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met AES-128. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

U kunt elke asset met meerdere versleutelingstypen versleutelen (AES-128, PlayReady, Widevine, FairPlay). Zie [Streamingprotocollen en versleutelingstypen](content-protection-overview.md#streaming-protocols-and-encryption-types) voor nuttige combinaties. Zie ook [hoe u met DRM kunt beveiligen](protect-with-drm.md).

De uitvoer van het voor beeld dit artikel bevat een URL naar de Azure Media Player, manifest-URL en het AES-token dat nodig is voor het afspelen van de inhoud. In het voor beeld wordt de verval datum van het token van de JSON Web Token (JWT) ingesteld op 1 uur. U kunt een browser openen en de resulterende URL plakken om de Azure Media Player demo pagina te starten met de URL en het token die u hebt ingevuld, al in de volgende indeling: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Down load het [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) -voor beeld dat wordt beschreven in het artikel.
> * Begin met het gebruik van Media Services Api's met .NET SDK.
> * Maak een uitvoer activum.
> * Een coderings transformatie maken.
> * Een taak verzenden.
> * Wacht tot de taak is voltooid.
> * Een inhouds sleutel beleid maken
> * Configureer het beleid om de JWT-token beperking te gebruiken.
> * Maak een streaming-Locator.
> * Configureer de streaming-Locator voor het versleutelen van de video met AES (ClearKey).
> * Een test Token ophalen.
> * Een streaming-URL bouwen.
> * Resources opschonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien.

* Lees het artikel [Content protection overview](content-protection-overview.md) (Overzicht inhoudsbeveiliging).
* Installeer Visual Studio Code of Visual Studio.
* [Een Azure Media Services-account maken](create-account-cli-quickstart.md).
* Referenties ophalen die nodig zijn voor het gebruik van Media Services Api's door [toegangs-api's](access-api-cli-how-to.md)te volgen.

## <a name="download-code"></a>Code downloaden

Kloon met de volgende opdracht een GitHub-opslagplaats met het volledige .NET-voorbeeld dat in dit artikel wordt beschreven, op de computer:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Het voor beeld ' Encrypt with AES-128 ' bevindt zich in de map [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) .

> [!NOTE]
> In het voor beeld worden elke keer dat u de app uitvoert, unieke bronnen gemaakt. Normaal gesp roken gebruikt u bestaande resources als trans formaties en beleids regels (als de bestaande resource vereiste configuraties heeft).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Maak een **AzureMediaServicesClient** -object om Media Services-api's met .net te gebruiken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Een uitvoeractivum maken  

In de [uitvoerasset](https://docs.microsoft.com/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Een coderingstransformatie verkrijgen of maken

Bij het maken van een nieuw [transformatie](https://docs.microsoft.com/rest/api/media/transforms)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter is een **TransformOutput**-object, zoals weergegeven in de onderstaande code. Elke **transformatie-uitvoer** bevat een **voorinstelling**. **Voorinstelling** bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste **TransformOutput**. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming** . De voor instelling codeert de invoer video in een automatisch gegenereerde bitrate-ladder (paren voor de omzetting van de bitsnelheid) op basis van de invoer resolutie en bitrate, en produceert vervolgens ISO MP4-bestanden met H. 264 video en AAC-audio die overeenkomt met elke combi natie van de verlaagings oplossing.

Voordat u een nieuwe [trans formatie](https://docs.microsoft.com/rest/api/media/transforms)maakt, moet u eerst controleren of er al een bestaat met behulp van de **Get** -methode, zoals wordt weer gegeven in de volgende code. In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Taak indienen

Zoals eerder vermeld, is het [transformatie](https://docs.microsoft.com/rest/api/media/transforms)-object het recept en is de [taak](https://docs.microsoft.com/rest/api/media/jobs) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** geeft informatie op zoals de locatie van de invoer video en de locatie voor de uitvoer.

In deze zelf studie maken we de invoer van de taak op basis van een bestand dat rechtstreeks vanuit een [https-bron-URL](job-input-from-http-how-to.md)wordt opgenomen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

Het duurt enige tijd voordat de taak is voltooid. Wanneer dit het geval is, wilt u een melding ontvangen. In het onderstaande codevoorbeeld ziet u hoe de status van de [taak](https://docs.microsoft.com/rest/api/media/jobs) kan worden opgevraagd in de service. Polling is geen aanbevolen best practice voor productie-apps vanwege een mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken. Zie voor meer informatie [route gebeurtenissen naar een aangepast webeindpunt](job-state-events-cli-how-to.md).

De **taak** doorloopt meestal de volgende statussen: **gepland**, **in wachtrij**, **wordt verwerkt**, **voltooid** (definitieve status). Als er een fout is opgetreden in de taak, krijgt u de **fout** status. Als de taak wordt geannuleerd, wordt deze **geannuleerd** en **geannuleerd** wanneer deze is voltooid...

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Een inhouds sleutel beleid maken

Een inhoudssleutel biedt veilige toegang tot uw assets. U moet een beleid voor **inhouds sleutels** maken dat configureert hoe de inhouds sleutel wordt geleverd aan de eind clients. De inhouds sleutel is gekoppeld aan de **streaming-Locator**. Media Services biedt ook de key delivery service die versleutelings sleutels levert aan geautoriseerde gebruikers.

Wanneer een stroom wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen (in dit geval door gebruik te maken van AES-versleuteling). Voor het ontsleutelen van de stroom vraagt de speler de sleutel aan bij de key delivery service. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Een streaming-locator maken

Nadat de codering is voltooid en het inhoudssleutelbeleid is ingesteld, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U maakt de video beschikbaar in twee stappen:

1. Maak een [streaming-Locator](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. De streaming-URL's bouwen die clients kunnen gebruiken.

Het proces voor het maken van de **streaming-Locator** wordt publicatie genoemd. De **streaming-Locator** is standaard onmiddellijk geldig nadat u de API-aanroepen hebt gemaakt. De laatste keer dat deze wordt verwijderd, tenzij u de optionele begin-en eind tijden configureert.

Wanneer u een [streaming-Locator](https://docs.microsoft.com/rest/api/media/streaminglocators)maakt, moet u de gewenste **StreamingPolicyName**opgeven. In deze zelf studie gebruiken we een van de PredefinedStreamingPolicies, die vertelt Azure Media Services hoe de inhoud voor streaming moet worden gepubliceerd. In dit voor beeld wordt de AES-envelop versleuteling toegepast (deze versleuteling wordt ook wel ClearKey-versleuteling genoemd, omdat de sleutel wordt geleverd aan de Play-client via HTTPS en niet een DRM-licentie).

> [!IMPORTANT]
> Wanneer u een aangepaste [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)gebruikt, dient u een beperkt aantal beleids regels te ontwerpen voor uw media service-account en deze opnieuw te gebruiken voor uw streaming-Locators wanneer dezelfde versleutelings opties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal StreamingPolicy-vermeldingen. Het is niet mogelijk om voor elke streaming-Locator een nieuwe StreamingPolicy te maken.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Een test-token ophalen

In deze zelfstudie geven we voor het inhoudssleutelbeleid op dat het een tokenbeperking heeft. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) -indeling en dat is wat we configureren in het voor beeld.

De ContentKeyIdentifierClaim wordt gebruikt in het **beleid voor de inhouds sleutel**. Dit betekent dat het token dat wordt gepresenteerd aan de key delivery service, de id van de inhouds sleutel moet hebben. In het voor beeld hebben we geen inhouds sleutel opgegeven bij het maken van de streaming-Locator, het systeem heeft een wille keurige een voor ons gemaakt. Om het test token te genereren, moeten we de ContentKeyId ophalen om de ContentKeyIdentifierClaim-claim in te stellen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Een DASH-streaming-URL bouwen

Nu de [streaming-Locator](https://docs.microsoft.com/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-url's ophalen. Als u een URL wilt maken, moet u de hostnaam van de [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) en het pad van de **streaming-Locator** samen voegen. In dit voorbeeld wordt het *standaard* **streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard* **streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen behalve objecten die u wilt hergebruiken (meestal gebruikt u trans formaties, streaming-Locators, enzovoort). Als u wilt dat uw account wordt gereinigd na het experimenteren, verwijdert u de resources die u niet wilt hergebruiken. Met de volgende code worden bijvoorbeeld taken verwijderd:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Beveiligen met DRM](protect-with-drm.md)
