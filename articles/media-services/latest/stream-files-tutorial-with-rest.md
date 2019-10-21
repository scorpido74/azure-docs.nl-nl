---
title: Extern bestand coderen op basis van URL en streamen met Azure Media Services - REST | Microsoft Docs
description: Volg de stappen van deze zelfstudie om met behulp van REST een bestand te coderen op basis van een URL en inhoud te streamen met Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: bb62a28798010d3e18c5f19fa0062001a70b9622
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675655"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Zelf studie: een extern bestand coderen op basis van URL en de video-REST streamen

Met Azure Media Services kunt u mediabestanden coderen in indelingen die kunnen worden afgespeeld met een groot aantal verschillende browsers en apparaten. Zo kunt u bijvoorbeeld inhoud streamen in de indelingen Apple HLS of MPEG DASH. Voordat u gaat streamen, moet u uw digitale mediabestand van hoge kwaliteit coderen. Zie [Coderen](encoding-concept.md) voor richtlijnen voor codering.

In deze zelfstudie leert u hoe u met behulp van REST een bestand kunt coderen en de video kunt streamen met Azure Media Services. 

![De video afspelen](./media/stream-files-tutorial-with-api/final-video.png)

In deze zelfstudie ontdekt u hoe u:    

> [!div class="checklist"]
> * Een Media Services-account kunt maken
> * Toegang kunt krijgen tot de Media Services API
> * Postman-bestanden downloaden
> * Postman configureren
> * Aanvragen verzenden met behulp van Postman
> * De streaming-URL testen
> * Resources opschonen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](create-account-cli-how-to.md).

    Vergeet niet welke waarden u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account

- Installeer de [Postman](https://www.getpostman.com/) REST-client als u de REST-API's wilt uitvoeren die in een aantal AMS REST-zelfstudies worden weergegeven. 

    We gebruiken **Postman** maar elk ander REST-hulpprogramma is hiervoor geschikt. Andere alternatieven zijn: **Visual Studio Code** met de REST-invoegtoepassing of **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Postman-bestanden downloaden

Kloon een GitHub-opslagplaats die de Postman verzameling en -omgevingsbestanden bevat.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Postman configureren

### <a name="configure-the-environment"></a>De omgeving configureren 

1. Open de **postman** -app.
2. Selecteer rechts van het scherm de optie **Manage environment**.

    ![Omgeving beheren](./media/develop-with-postman/postman-import-env.png)
4. Klik in het dialoogvenster **Manage environment** op **Import**.
2. Blader naar het bestand `Azure Media Service v3 Environment.postman_environment.json` dat is gedownload toen u `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` kloonde.
6. De omgeving **Azure Media Service v3 Environment** is toegevoegd.

    > [!Note]
    > Werk de toegangsvariabelen bij met waarden die u hebt gekregen in de sectie **Toegang krijgen tot de Media Services API** hierboven.

7. Dubbelklik op het geselecteerde bestand en voer de waarden in die u hebt verkregen door de stappen voor het verkrijgen van [toegang tot API's](#access-the-media-services-api) te volgen.
8. Sluit het dialoogvenster.
9. Selecteer de omgeving **Azure Media Service v3 Environment** in de vervolgkeuzelijst.

    ![Omgeving kiezen](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>De collectie configureren

1. Klik op **Import** om het verzamelingbestand te importeren.
1. Blader naar het bestand `Media Services v3.postman_collection.json` dat is gedownload toen u `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` kloonde
3. Kies het bestand **Media Services v3.postman_collection.json**.

    ![Een bestand importeren](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Aanvragen verzenden met behulp van Postman

In deze sectie verzenden we aanvragen die relevant zijn voor het coderen en maken van URL's zodat uw bestand kan worden gestreamd. In het bijzonder worden de volgende aanvragen verzonden:

1. Azure AD-token verkrijgen voor service-principal-verificatie
2. Een uitvoeractivum maken
3. Een **transformatie** maken
4. Een **taak** maken
5. Een **streaming-locator** te maken
6. Toont paden van de **streaming-locator**

> [!Note]
>  In deze zelfstudie wordt ervan uitgegaan dat u alle resources maakt met unieke namen.  

### <a name="get-azure-ad-token"></a>Azure AD-token verkrijgen 

1. Selecteer in het linkerdeel venster van de Postman-app ' stap 1: AAD-verificatie Token ophalen '.
2. Selecteer vervolgens Get Azure AD Token for Service Principal Authentication.
3. Druk op **Verzenden**.

    De volgende **POST**-bewerking wordt verzonden.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Het antwoord bevat de token en stelt de omgevingsvariabele AccessToken in op de tokenwaarde. Klik op het tabblad **Tests** voor de code waarmee AccessToken wordt ingesteld. 

    ![AAD-token verkrijgen](./media/develop-with-postman/postman-get-aad-auth-token.png)

### <a name="create-an-output-asset"></a>Een uitvoeractivum maken

In de [uitvoerasset](https://docs.microsoft.com/rest/api/media/assets) wordt het resultaat van de coderingstaak opgeslagen. 

1. Selecteer ' assets ' in het linkerdeel venster van de Postman-app.
2. Selecteer vervolgens Create or update an Asset.
3. Druk op **Verzenden**.

    * De volgende **PUT**-bewerking wordt verzonden:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * De bewerking heeft de volgende hoofdtekst:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>Een transformatie maken

Bij het coderen of verwerken van inhoud in Media Services is het een gangbaar patroon om de coderingsinstellingen als recept in te stellen. U dient vervolgens een **taak** in te dienen om het recept toe te passen op een video. Door voor elke nieuwe video nieuwe taken in te dienen, past u dat recept toe op alle video's in de bibliotheek. Een recept in Media Services wordt aangeroepen als een **transformatie**. Zie [Transformaties en taken](transform-concept.md) voor meer informatie. Het voorbeeld dat wordt beschreven in deze zelfstudie definieert een recept dat de video codeert om het te streamen naar tal van iOS- en Android-apparaten. 

Bij het maken van een nieuw [transformatie](https://docs.microsoft.com/rest/api/media/transforms)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. De vereiste parameter is een **TransformOutput**-object. Elke **transformatie-uitvoer** bevat een **voorinstelling**. **Voorinstelling** bevat de stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste **TransformOutput**. Het voorbeeld dat in dit artikel wordt beschreven, maakt gebruik van een ingebouwde voorinstelling genaamd **AdaptiveStreaming** . De voorinstelling codeert de invoervideo in een automatisch gegenereerde bitrate-ladder (bitrate-resolutieparen) op basis van de invoerresolutie en bitsnelheid en produceert ISO MP4-bestanden met H.264-video en AAC-audio die overeenkomen met elk bitrate-resolutiepaar. Zie [een bitrate-ladder automatisch genereren](autogen-bitrate-ladder.md) voor meer informatie over deze voorinstelling.

U kunt een ingebouwde EncoderNamedPreset gebruiken of aangepaste voorinstellingen gebruiken. 

> [!Note]
> Bij het maken van een [transformatie](https://docs.microsoft.com/rest/api/media/transforms) moet u met de methode **Get** eerst controleren of er al een bestaat. In deze zelfstudie wordt ervan uitgegaan dat u alle transformaties maakt met een unieke naam.

1. Selecteer in het linkerdeel venster van de Postman-app de optie code ring en analyse.
2. Selecteer daarna Create Transform.
3. Druk op **Verzenden**.

    * De volgende **PUT**-bewerking wordt verzonden.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * De bewerking heeft de volgende hoofdtekst:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Een taak maken

Een [taak](https://docs.microsoft.com/rest/api/media/jobs) is de eigenlijke aanvraag aan Media Services om de gemaakte **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** bevat informatie zoals de locatie van de invoervideo en de locatie voor de uitvoer.

In dit voor beeld is de invoer van de taak gebaseerd op een HTTPS-URL (https: \//nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/).

1. Selecteer in het linkerdeel venster van de Postman-app de optie code ring en analyse.
2. Selecteer vervolgens Create or Update Job.
3. Druk op **Verzenden**.

    * De volgende **PUT**-bewerking wordt verzonden.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * De bewerking heeft de volgende hoofdtekst:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

De taak neemt enige tijd in beslag en wanneer deze is voltooid, wordt u hiervan op de hoogte gesteld. Als u de voortgang van de taak wilt zien, kunt u het best Event Grid gebruiken. Dit is ontworpen voor hoge beschikbaarheid, consistente prestaties en dynamische schaalbaarheid. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. Eenvoudige, op HTTP gebaseerde reactieve gebeurtenisafhandeling maakt het mogelijk om efficiënte oplossingen te bouwen met behulp van intelligente filtering en routering van gebeurtenissen.  Zie [Gebeurtenissen routeren naar een aangepast eindpunt](job-state-events-cli-how-to.md).

De **taak** doorloopt meestal de volgende statussen: **gepland**, **in wachtrij**, **wordt verwerkt**, **voltooid** (definitieve status). Als bij de taak een fout is opgetreden is, krijgt u de status **Fout**. Als de taak momenteel wordt geannuleerd, krijgt u de melding **Wordt geannuleerd** en **Geannuleerd** wanneer het annuleren is voltooid.

#### <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Een streaming-locator te maken

Wanneer de coderingstaak is voltooid, gaat u in de volgende stap de video in de uitvoer**asset** beschikbaar maken voor weergave door clients. U kunt dit doen in twee stappen: maak eerst een [streaming-locator](https://docs.microsoft.com/rest/api/media/streaminglocators) en bouw vervolgens de streaming-URL's die clients kunnen gebruiken. 

Het proces van het maken van een **streaming-locator** wordt publiceren genoemd. De **streaming-locator** is standaard onmiddellijk geldig nadat u de API-aanroepen hebt gemaakt en totdat deze wordt verwijderd, tenzij u de optionele start- en eindtijden configureert. 

Wanneer u een [streaming-Locator](https://docs.microsoft.com/rest/api/media/streaminglocators)maakt, moet u de gewenste **StreamingPolicyName**opgeven. In dit voor beeld moet u de inhoud-de-Clear-(of niet-versleutelde) streamen. Daarom wordt het vooraf gedefinieerde beleid ' Predefined_ClearStreamingOnly ' voor het wissen van duidelijke gegevens gebruikt.

> [!IMPORTANT]
> Wanneer u een aangepast [streamingbeleid](https://docs.microsoft.com/rest/api/media/streamingpolicies) gebruikt, moet u een beperkte set met dergelijke beleidsregels ontwerpen voor uw Media Service-account, en deze opnieuw gebruiken voor de StreamingLocators wanneer dezelfde versleutelingsopties en protocollen nodig zijn. 

Uw Media Service-account heeft een quotum voor het aantal **streaming-beleidsvermeldingen**. U hoeft geen nieuw **streaming-beleid** te maken voor elke **streaming-locator**.

1. Selecteer in het linkerdeel venster van de Postman-app ' streaming-beleid '.
2. Selecteer vervolgens Create a Streaming Locator.
3. Druk op **Verzenden**.

    * De volgende **PUT**-bewerking wordt verzonden.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}
        ```
    * De bewerking heeft de volgende hoofdtekst:

        ```json
        {
            "properties":{
            "assetName": "{{assetName}}",
            "streamingPolicyName": "{{streamingPolicyName}}"
            }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Paden weergeven en streaming-URL's maken

#### <a name="list-paths"></a>Paden weergeven

Nu de [streaming-locator](https://docs.microsoft.com/rest/api/media/streaminglocators) is gemaakt, kunt u de streaming-URL's ophalen

1. Selecteer in het linkerdeel venster van de Postman-app ' streaming-beleid '.
2. Selecteer vervolgens List Paths.
3. Druk op **Verzenden**.

    * De volgende **POST**-bewerking wordt verzonden.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * De bewerking heeft geen hoofdtekst:
        
4. Noteer een van de paden die u voor streaming wilt gebruiken. U hebt deze nodig in de volgende sectie. In dit geval worden de volgende paden geretourneerd:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>De streaming-URL's maken

In deze sectie gaat u een URL voor HLS-streaming maken. URL's bestaan uit de volgende waarden:

1. Het protocol waarmee gegevens worden verzonden. In dit geval 'https'.

    > [!NOTE]
    > Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

2. De hostnaam van het StreamingEndpoint. In dit geval is de naam 'amsaccount-usw22.streaming.media.azure.net'.

    U kunt de volgende GET-bewerking gebruiken om de hostnaam op te halen:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. Een pad dat u in de vorige sectie (Paden weergeven) hebt verkregen.  

Als resultaat wordt de volgende HLS-URL gemaakt

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>De streaming-URL testen


> [!NOTE]
> Controleer of het **streaming-eindpunt** vanwaar u wilt streamen, wordt uitgevoerd.

In dit artikel gebruiken we Azure Media Player om de stream te testen. 

1. Open een browser en ga naar [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Plak in de gemaakte URL in het vak **URL:** . 
3. Klik op **Update Player**.

Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

Over het algemeen moet u alles opschonen, behalve objecten die u van plan bent te hergebruiken (meestal gebruikt u **transformaties** opnieuw en behoudt u **streaming-locators**, enzovoort). Als u wilt dat uw account na het experiment is opgeschoond, moet u de resources verwijderen die u niet van plan bent te hergebruiken.  

Als u een resource wilt verwijderen, selecteert u de Delete-bewerking onder de resource die u wilt verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources van de resourcegroep niet meer nodig hebt, met inbegrip van de Media Services en opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep die u eerder hebt gemaakt.  

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Bekijk het [Azure Media Services Community](media-services-community.md) -artikel voor verschillende manieren om vragen te stellen, feedback te geven en updates te ontvangen over Media Services.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw video kunt uploaden, coderen en streamen, kunt u doorgaan naar het volgende artikel: 

> [!div class="nextstepaction"]
> [Video's analyseren](analyze-videos-tutorial-with-api.md)
