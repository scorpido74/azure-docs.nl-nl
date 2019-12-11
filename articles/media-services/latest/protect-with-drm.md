---
title: Het gebruik van de Digital Rights Management-service voor dynamische versleuteling en licentielevering
titleSuffix: Azure Media Services
description: Informatie over het gebruik van dynamische DRM-versleuteling en licentie leverings service om stromen te leveren die zijn versleuteld met de micro soft PlayReady-, Google Widevine-of Apple FairPlay-licenties.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3d2dc7793c25fb20e267332beaa683f11ddcbfbb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974067"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Zelf studie: DRM dynamische versleuteling en licentie leverings service gebruiken

> [!NOTE]
> Hoewel deze zelf studie de [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) -voor beelden gebruikt, zijn de algemene stappen hetzelfde [voor rest API](https://docs.microsoft.com/rest/api/media/liveevents), [cli](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)of andere ondersteunde [sdk's](media-services-apis-overview.md#sdks).

U kunt Azure Media Services gebruiken voor het leveren van streams die zijn versleuteld met licenties van Microsoft PlayReady, Google Widevine of Apple FairPlay. Zie [Content Protection with dynamic Encryption](content-protection-overview.md)(Engelstalig) voor gedetailleerde uitleg.

Media Services biedt ook een service voor het leveren van PlayReady-, Widevine-en FairPlay DRM-licenties. Wanneer een gebruiker door DRM beveiligde inhoud aanvraagt, vraagt de Player-app een licentie aan bij de Media Services License-Service. Als de app voor de speler is geautoriseerd, wordt door de Media Services License-Service een licentie aan de speler verleend. Een licentie bevat de ontsleutelingssleutel die kan worden gebruikt door de clientspeler om de inhoud te ontsleutelen en te streamen.

Dit artikel is gebaseerd op het voorbeeld waarbij [DRM wordt gebruikt voor de versleuteling](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

In het voorbeeld in dit artikel wordt het volgende resultaat bereikt:

![AMS met met DRM beveiligde video in Azure Media Player](./media/protect-with-drm/ams_player.png)

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Een coderings transformatie maken.
> * De ondertekeningssleutel instellen die wordt gebruik om uw token te verifiëren.
> * Stel vereisten in voor het beleid voor inhouds sleutels.
> * Maak een StreamingLocator met het opgegeven streaming-beleid.
> * Maak een URL die wordt gebruikt om uw bestand af te spelen.

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien:

* Lees het artikel [Content protection overview](content-protection-overview.md) (Overzicht inhoudsbeveiliging).
* Bekijk het [ontwerp multi-DRM-inhouds beschermings systeem met toegangs beheer](design-multi-drm-system-with-access-control.md).
* Installeer Visual Studio Code of Visual Studio.
* Maak een nieuw Media Services-account zoals beschreven in deze [snelstart](create-account-cli-quickstart.md).
* Zorg dat u referenties hebt die nodig zijn om Media Services-API's te kunnen gebruiken via [toegangs-API's](access-api-cli-how-to.md)
* Stel de juiste waarden in het configuratie bestand van de app in (appSettings. json).

## <a name="download-code"></a>Code downloaden

Kloon met de volgende opdracht een GitHub-opslagplaats met het volledige .NET-voorbeeld dat in dit artikel wordt beschreven, op de computer:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Het voorbeeld 'Versleutelen met DRM' bevindt zich in de map [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> In het voor beeld worden elke keer dat u de app uitvoert, unieke bronnen gemaakt. Normaal gesp roken gebruikt u bestaande resources als trans formaties en beleids regels (als de bestaande resource vereiste configuraties heeft).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Maak een **AzureMediaServicesClient** -object om Media Services-api's met .net te gebruiken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Een uitvoeractivum maken  

In de [uitvoerasset](assets-concept.md) wordt het resultaat van de coderingstaak opgeslagen.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Een coderingstransformatie verkrijgen of maken

Bij het maken van een nieuw [transformatie](transforms-jobs-concept.md)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste para meter is een `transformOutput`-object, zoals in de onderstaande code wordt weer gegeven. Elke TransformOutput bevat een **vooraf ingestelde**. Voor instelling worden de stapsgewijze instructies beschreven van de bewerkingen voor video en/of audio verwerking die moeten worden gebruikt om de gewenste TransformOutput te genereren. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming** . De voor instelling codeert de invoer video in een automatisch gegenereerde bitrate-ladder (paren voor de omzetting van de bitsnelheid) op basis van de invoer resolutie en bitrate, en produceert ISO MP4-bestanden met H. 264 video-en AAC-audio die overeenkomt met elke combi natie van de verlaagings oplossing. 

Voordat u een nieuwe **transformatie** gaat maken, moet u eerst controleren of er al een bestaat. Dit doet u met de methode **Ophalen**, zoals weergegeven in de volgende code.  In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Taak indienen

Zoals eerder vermeld, is het **transformatie**-object het recept en is de [taak](transforms-jobs-concept.md) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** geeft informatie op zoals de locatie van de invoer video en de locatie voor de uitvoer.

In deze zelf studie maken we de invoer van de taak op basis van een bestand dat rechtstreeks vanuit een [https-bron-URL](job-input-from-http-how-to.md)wordt opgenomen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

Het duurt enige tijd voordat de taak is voltooid. Wanneer dit het geval is, wilt u een melding ontvangen. In het onderstaande codevoorbeeld ziet u hoe de status van de **taak** kan worden opgevraagd in de service. Polling is geen aanbevolen best practice voor productie-apps vanwege een mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken. Zie [Gebeurtenissen routeren naar een aangepast eindpunt](job-state-events-cli-how-to.md).

De **taak** doorloopt meestal de volgende statussen: **gepland**, **in wachtrij**, **wordt verwerkt**, **voltooid** (definitieve status). Als er een fout is opgetreden in de taak, krijgt u de **fout** status. Als de taak wordt geannuleerd, wordt deze **geannuleerd** en **geannuleerd** wanneer deze is voltooid...

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Een inhouds sleutel beleid maken

Een inhoudssleutel biedt veilige toegang tot uw assets. U moet een beleid voor [inhouds sleutels](content-key-policy-concept.md) maken wanneer u uw inhoud versleutelt met een DRM. Het beleid configureert hoe de inhouds sleutel wordt geleverd aan de eind clients. De inhouds sleutel is gekoppeld aan een streaming-Locator. Media Services verzorgt ook de sleutelleveringsservice waarmee versleutelingssleutels en -licenties aan gemachtigde gebruikers worden geleverd.

U moet de vereisten (beperkingen) instellen voor het beleid voor de **inhouds sleutel** waaraan moet worden voldaan om sleutels te kunnen leveren met de opgegeven configuratie. In dit voorbeeld worden de volgende configuraties en vereisten ingesteld:

* Configuratie

    De licenties van [PlayReady](playready-license-template-overview.md) en [Widevine](widevine-license-template-overview.md) zijn geconfigureerd om te kunnen worden geleverd door de Media Services-service voor het leveren van licenties. Hoewel deze voor beeld-app de [Fairplay](fairplay-license-overview.md) -licentie niet configureert, bevat deze een methode die u kunt gebruiken om Fairplay te configureren. U kunt de FairPlay-configuratie als een andere optie toevoegen.

* Beperking

    De App stelt een JSON Web Token (JWT)-token type beperking voor het beleid in.

Wanneer een stream wordt aangevraagd door een speler, maakt Media Services gebruik van de opgegeven sleutel om uw inhoud dynamisch te versleutelen. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Een streaming-locator maken

Nadat de codering is voltooid en het inhoudssleutelbeleid is ingesteld, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U maakt de video beschikbaar in twee stappen:

1. Maak een [streaming-Locator](streaming-locators-concept.md).
2. De streaming-URL's bouwen die clients kunnen gebruiken.

Het proces voor het maken van de **streaming-Locator** wordt publicatie genoemd. De **streaming-Locator** is standaard onmiddellijk geldig nadat u de API-aanroepen hebt gemaakt. De laatste keer dat deze wordt verwijderd, tenzij u de optionele begin-en eind tijden configureert.

Wanneer u een **streaming-Locator**maakt, moet u de gewenste `StreamingPolicyName`opgeven. In deze zelf studie gebruiken we een van de vooraf gedefinieerde streaming-beleids regels, waarmee wordt aangegeven Azure Media Services hoe de inhoud voor streaming moet worden gepubliceerd. In dit voorbeeld stellen we StreamingLocator.StreamingPolicyName in op het beleid Predefined_MultiDrmCencStreaming. De sleutels PlayReady en Widevine worden toegepast en de sleutel wordt door gegeven aan de Play-client op basis van de geconfigureerde DRM-licenties. Als u wilt dat uw stream ook met CBCS (FairPlay) wordt versleuteld, gebruikt u Predefined_MultiDrmStreaming.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](streaming-policy-concept.md) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal StreamingPolicy-vermeldingen. Het is niet mogelijk om voor elke StreamingLocator een nieuwe StreamingPolicy te maken.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Een test-token ophalen

In deze zelfstudie geven we voor het inhoudssleutelbeleid op dat het een tokenbeperking heeft. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) -indelingen en dat is wat we configureren in het voor beeld.

De ContentKeyIdentifierClaim wordt in de ContentKeyPolicy gebruikt, wat inhoudt dat de token die aan de sleutelleveringsservice wordt gepresenteerd, de identificatie van de ContentKey moet bevatten. In het voor beeld geven we geen inhouds sleutel op bij het maken van de streaming-Locator. het systeem creëert een wille keurige voor ons. Om het test token te genereren, moeten we de ContentKeyId ophalen om de ContentKeyIdentifierClaim-claim in te stellen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Een streaming-URL bouwen

Nu de [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen. Als u een URL wilt maken, moet u de hostnaam van de [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) en het pad van de **streaming-Locator** samen voegen. In dit voorbeeld wordt het *standaard* **streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard* **streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Wanneer u de app uitvoert, wordt het volgende scherm weer gegeven:

![Beschermen met drm](./media/protect-with-drm/playready_encrypted_url.png)

U kunt een browser openen en de resulterende URL erin plakken om de demopagina van Azure Media Player te starten waarin de URL en token al zijn ingevuld.

## <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen behalve objecten die u wilt hergebruiken (meestal gebruikt u trans formaties, StreamingLocators, enzovoort). Als u wilt dat uw account wordt gereinigd na het experimenteren, verwijdert u de resources die u niet wilt hergebruiken. Met de volgende code worden bijvoorbeeld taken verwijderd:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de service voorwaarden en het privacybeleid van Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

Ga naar het

> [!div class="nextstepaction"]
> [Beveiligen met AES-128](protect-with-aes128.md)