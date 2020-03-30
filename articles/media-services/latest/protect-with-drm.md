---
title: DRM-versleuteling en licentieleveringsservice azure Media Services
titleSuffix: Azure Media Services
description: Meer informatie over het gebruik van drm dynamische versleuteling en licentieleveringsservice om streams te leveren die zijn versleuteld met Microsoft PlayReady-, Google Widevine- of Apple FairPlay-licenties.
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
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086733"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Zelfstudie: Drm-dynamische versleutelingen en licentieleveringsservice gebruiken

> [!NOTE]
> Hoewel deze zelfstudie de [.NET SDK-voorbeelden](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) gebruikt, zijn de algemene stappen hetzelfde voor [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)of andere ondersteunde [SDK's](media-services-apis-overview.md#sdks).

U kunt Azure Media Services gebruiken voor het leveren van streams die zijn versleuteld met licenties van Microsoft PlayReady, Google Widevine of Apple FairPlay. Zie [Inhoudsbescherming met dynamische versleuteling](content-protection-overview.md)voor diepgaande uitleg.

Media Services biedt ook een service voor het leveren van PlayReady-, Widevine- en FairPlay DRM-licenties. Wanneer een gebruiker met DRM-beveiligde inhoud om drm-beveiligde inhoud vraagt, vraagt de speler-app een licentie aan van de licentieservice Media Services. Als de speler-app is geautoriseerd, geeft de licentieservice Media Services een licentie af aan de speler. Een licentie bevat de ontsleutelingssleutel die kan worden gebruikt door de clientspeler om de inhoud te ontsleutelen en te streamen.

Dit artikel is gebaseerd op het voorbeeld waarbij [DRM wordt gebruikt voor de versleuteling](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

In het voorbeeld in dit artikel wordt het volgende resultaat bereikt:

![AMS met DRM-beveiligde video in Azure Media Player](./media/protect-with-drm/ams_player.png)

In deze handleiding ontdekt u hoe u:

> [!div class="checklist"]
> * Maak een coderingstransformatie.
> * De ondertekeningssleutel instellen die wordt gebruik om uw token te verifiëren.
> * Stel vereisten in voor het inhoudssleutelbeleid.
> * Maak een StreamingLocator met het opgegeven streamingbeleid.
> * Maak een URL die wordt gebruikt om uw bestand af te spelen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien:

* Lees het artikel [Content protection overview](content-protection-overview.md) (Overzicht inhoudsbeveiliging).
* Bekijk het [ontwerpsysteem voor multi-DRM-inhoudmet toegangscontrole](design-multi-drm-system-with-access-control.md).
* Installeer Visual Studio Code of Visual Studio.
* Maak een nieuw Media Services-account zoals beschreven in deze [snelstart](create-account-cli-quickstart.md).
* Zorg dat u referenties hebt die nodig zijn om Media Services-API's te kunnen gebruiken via [toegangs-API's](access-api-cli-how-to.md)
* Stel de juiste waarden in het app-configuratiebestand in (appsettings.json).

## <a name="download-code"></a>Code downloaden

Kloon met de volgende opdracht een GitHub-opslagplaats met het volledige .NET-voorbeeld dat in dit artikel wordt beschreven, op de computer:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Het voorbeeld 'Versleutelen met DRM' bevindt zich in de map [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Het voorbeeld creëert unieke bronnen elke keer dat u de app uitvoert. Doorgaans gebruikt u bestaande bronnen zoals transformaties en beleidsregels opnieuw (als bestaande resources configuraties vereisen).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Als u API's van Media Services wilt gaan gebruiken met .NET, maakt u een **AzureMediaServicesClient-object.** Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Een uitvoeractivum maken  

In de [uitvoerasset](assets-concept.md) wordt het resultaat van de coderingstaak opgeslagen.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Een coderingstransformatie verkrijgen of maken

Bij het maken van een nieuw [transformatie](transforms-jobs-concept.md)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter `transformOutput` is een object, zoals in de onderstaande code. Elke transformatie-uitvoer bevat een **voorinstelling**. Voorinstelling bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste TransformOutput. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming **. De preset codeert de invoervideo in een automatisch gegenereerde bitrateladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitrate en produceert ISO MP4-bestanden met H.264-video en OC-audio die overeenkomen met elk paar met bitrate-resolutie. 

Voordat u een nieuwe **transformatie** gaat maken, moet u eerst controleren of er al een bestaat. Dit doet u met de methode **Ophalen**, zoals weergegeven in de volgende code.  In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Taak indienen

Zoals eerder vermeld, is het **transformatie**-object het recept en is de [taak](transforms-jobs-concept.md) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** geeft informatie op, zoals de locatie van de invoervideo en de locatie voor de uitvoer.

In deze zelfstudie maken we de invoer van de taak op basis van een bestand dat rechtstreeks wordt ingenomen vanuit een URL van de [HTTPs-bron.](job-input-from-http-how-to.md)

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

De klus duurt enige tijd. Als dat het zo is, wilt u op de hoogte worden gesteld. In het onderstaande codevoorbeeld ziet u hoe de status van de **taak** kan worden opgevraagd in de service. Polling is geen aanbevolen aanbevolen best practice voor productie-apps vanwege mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken. Zie [Gebeurtenissen routeren naar een aangepast eindpunt](job-state-events-cli-how-to.md).

De **taak** doorloopt meestal de volgende statussen: **gepland**, **in wachtrij**, **wordt verwerkt**, **voltooid** (definitieve status). Als de taak een fout is opgelopen, krijgt u de **status Fout.** Als de taak wordt geannuleerd, wordt u **geannuleerd** en **geannuleerd** wanneer deze is uitgevoerd.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Een inhoudssleutelbeleid maken

Een inhoudssleutel biedt veilige toegang tot uw assets. U moet een [inhoudssleutelbeleid](content-key-policy-concept.md) maken wanneer u uw inhoud versleutelt met een DRM. Het beleid configureert hoe de inhoudssleutel wordt geleverd aan eindclients. De inhoudssleutel is gekoppeld aan een streaminglocator. Media Services verzorgt ook de sleutelleveringsservice waarmee versleutelingssleutels en -licenties aan gemachtigde gebruikers worden geleverd.

U moet de vereisten (beperkingen) instellen voor het **beleid voor inhoudssleutel** waaraan moet worden voldaan om sleutels met de opgegeven configuratie te leveren. In dit voorbeeld worden de volgende configuraties en vereisten ingesteld:

* Configuratie

    De licenties van [PlayReady](playready-license-template-overview.md) en [Widevine](widevine-license-template-overview.md) zijn geconfigureerd om te kunnen worden geleverd door de Media Services-service voor het leveren van licenties. Hoewel deze voorbeeld-app de [FairPlay-licentie](fairplay-license-overview.md) niet configureert, bevat deze een methode die u gebruiken om FairPlay te configureren. U FairPlay-configuratie als een andere optie toevoegen.

* Beperking

    De app stelt een Json-webtokenbeperking (JWT) in op het beleid.

Wanneer een stream wordt aangevraagd door een speler, maakt Media Services gebruik van de opgegeven sleutel om uw inhoud dynamisch te versleutelen. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. De service evalueert het door u opgegeven beleid voor de inhoudssleutel om te bepalen of de gebruiker is gemachtigd om de sleutel op te halen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Een streaming-locator maken

Nadat de codering is voltooid en het inhoudssleutelbeleid is ingesteld, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. U maakt de video beschikbaar in twee stappen:

1. Een [streaminglocator maken.](streaming-locators-concept.md)
2. De streaming-URL's bouwen die clients kunnen gebruiken.

Het proces van het maken van de **Streaming Locator** heet publiceren. Standaard is de **Streaming Locator** direct geldig nadat u de API-aanroepen hebt gemaakt. Het duurt tot het is verwijderd, tenzij u de optionele begin- en eindtijden configureert.

Bij het maken van een **Streaming Locator**moet u de gewenste `StreamingPolicyName`. In deze zelfstudie gebruiken we een van de vooraf gedefinieerde streamingbeleidsregels, waarmee Azure Media Services wordt uitgelegd hoe de inhoud voor streaming moet worden gepubliceerd. In dit voorbeeld stellen we StreamingLocator.StreamingPolicyName in op het beleid Predefined_MultiDrmCencStreaming. De PlayReady- en Widevine-versleutelingen worden toegepast en de sleutel wordt geleverd aan de afspeelclient op basis van de geconfigureerde DRM-licenties. Als u wilt dat uw stream ook met CBCS (FairPlay) wordt versleuteld, gebruikt u Predefined_MultiDrmStreaming.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](streaming-policy-concept.md) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. Uw Media Service-account heeft een quotum voor het aantal StreamingPolicy-vermeldingen. U moet geen nieuw streamingbeleid maken voor elke StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Een test-token ophalen

In deze zelfstudie geven we voor het inhoudssleutelbeleid op dat het een tokenbeperking heeft. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de [JWT-indelingen](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) en dat is wat we in het voorbeeld configureren.

De ContentKeyIdentifierClaim wordt in de ContentKeyPolicy gebruikt, wat inhoudt dat de token die aan de sleutelleveringsservice wordt gepresenteerd, de identificatie van de ContentKey moet bevatten. In het voorbeeld geven we geen inhoudssleutel op bij het maken van de Streaming Locator, het systeem maakt een willekeurige voor ons. Om het testtoken te genereren, moeten we de ContentKeyId in de ContentKeyIdentifierClaim-claim laten plaatsen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Een streaming-URL bouwen

Nu de [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen. Als u een URL wilt maken, moet u de hostnaam [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) en het **pad van streaming locator met** zich meebrengt. In dit voorbeeld wordt het *standaard* **streaming-eindpunt** gebruikt. Wanneer u voor het eerst een Media Service-account maakt, wordt dit *standaard* **streaming-eindpunt** gestopt. Daarom moet u **Start** aanroepen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Wanneer u de app uitvoert, ziet u het volgende scherm:

![Beschermen met drm](./media/protect-with-drm/playready_encrypted_url.png)

U kunt een browser openen en de resulterende URL erin plakken om de demopagina van Azure Media Player te starten waarin de URL en token al zijn ingevuld.

## <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen, behalve objecten die u opnieuw wilt gebruiken (meestal gebruikt u transformaties, StreamingLocators, enzovoort). Als u wilt dat uw account schoon is na het experimenteren, verwijdert u de bronnen die u niet opnieuw wilt gebruiken. De volgende code verwijdert bijvoorbeeld Taken:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

Uitchecken

> [!div class="nextstepaction"]
> [Bescherm met AES-128](protect-with-aes128.md)
