---
title: Een Azure-asset coderen met Media Encoder Standard | Microsoft Documenten
description: Meer informatie over het gebruik van Media Encoder Standard om media-inhoud te coderen in Azure Media Services. Codevoorbeelden maken gebruik van REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 6854400f2152a5952a7b24dbd860d7ad4bfc943d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774922"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Een asset coderen met Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [Rest](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Overzicht

Als u digitale video via internet wilt leveren, moet u de media comprimeren. Digitale videobestanden zijn groot en mogelijk te groot om via internet te leveren, of om de apparaten van uw klanten goed weer te geven. Codering is het proces van het comprimeren van video en audio, zodat uw klanten uw media kunnen bekijken.

Coderingstaken zijn een van de meest voorkomende verwerkingsbewerkingen in Azure Media Services. U maakt coderingstaken om mediabestanden van de ene codering naar de andere te converteren. Wanneer u codeert, u de ingebouwde encoder van Media Services (Media Encoder Standard) gebruiken. U ook gebruik maken van een encoder die wordt geleverd door een Media Services-partner. Externe encoders zijn beschikbaar via de Azure Marketplace. U de details van coderingstaken opgeven met vooraf ingestelde tekenreeksen die zijn gedefinieerd voor uw encoder of met vooraf ingestelde configuratiebestanden. Zie [Taakvoorinstellingen voor Media Encoder Standard](https://msdn.microsoft.com/library/mt269960)voor het bekijken van de typen voorinstellingen die beschikbaar zijn.

Elke taak kan een of meer taken hebben, afhankelijk van het type verwerking dat u wilt uitvoeren. Via de REST API u op twee manieren taken en bijbehorende taken maken:

* Taken kunnen inline worden gedefinieerd via de eigenschap Tasks navigation op Taakentiteiten.
* Gebruik OData batchverwerking.

We raden u aan uw bronbestanden altijd te coderen in een adaptieve bitrate MP4-set en de set vervolgens om te zetten naar het gewenste formaat met behulp van [dynamische verpakking.](media-services-dynamic-packaging-overview.md)

Als uw uitvoerasset is versleuteld met opslag, moet u het beleid voor het leveren van activa configureren. Zie [Beleid voor het configureren van asset delivery voor](media-services-rest-configure-asset-delivery-policy.md)meer informatie .

## <a name="considerations"></a>Overwegingen

Wanneer u toegang krijgt tot entiteiten in Media Services, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)meer informatie.

Voordat u mediaprocessors gaat verwijzen, controleert u of u over de juiste mediaprocessor-id beschikt. Zie [Mediaprocessors ophalen](media-services-rest-get-media-processor.md)voor meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor informatie over hoe u verbinding maken met de AMS-API. 

## <a name="create-a-job-with-a-single-encoding-task"></a>Een taak maken met één coderingstaak

> [!NOTE]
> Wanneer u met de API Media Services REST werkt, zijn de volgende overwegingen van toepassing:
>
> Wanneer u toegang krijgt tot entiteiten in Media Services, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API development voor](media-services-rest-how-to-use.md)meer informatie.
>
> Wanneer u JSON gebruikt en opgeeft het **__metadata** trefwoord in de aanvraag te gebruiken (bijvoorbeeld om naar een gekoppeld object te verwijzen), moet u de **koptekst Accepteren** instellen op [json verbose-indeling](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Accepteren: toepassing/json;odata=verbose.
>
>

In het volgende voorbeeld ziet u hoe u een taak maakt en plaatst met één taakdie is ingesteld om een video te coderen met een specifieke resolutie en kwaliteit. Wanneer u codeert met Media Encoder Standard, u [hier](https://msdn.microsoft.com/library/mt269960)voorinstellingen voor taakconfiguratie gebruiken.

Aanvraag:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Reactie:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>De naam van het uitvoeritem instellen
In het volgende voorbeeld ziet u hoe u het kenmerk assetName instelt:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Overwegingen
* Eigenschappen van taaktekst moeten letterlijke XML gebruiken om het aantal invoer- of uitvoerelementen te definiëren dat door de taak wordt gebruikt. Het taakartikel bevat de XML-schemadefinitie voor de XML.
* In de definitie van taakinstantie `<inputAsset>` `<outputAsset>` moet elke innerlijke waarde voor en moet worden ingesteld als JobInputAsset(waarde) of JobOutputAsset(waarde).
* Een taak kan meerdere uitvoerelementen hebben. Eén JobOutputAsset(x) kan slechts één keer worden gebruikt als uitvoer van een taak in een taak.
* U JobInputAsset of JobOutputAsset opgeven als invoerelement van een taak.
* Taken mogen geen cyclus vormen.
* De waardeparameter die u doorgeeft aan JobInputAsset of JobOutputAsset vertegenwoordigt de indexwaarde voor een actief. De werkelijke activa worden gedefinieerd in de navigatie-eigenschappen InputMediaAssets en OutputMediaAssets op de definitie van de taakentiteit.
* Omdat Media Services is gebouwd op OData v3, worden de afzonderlijke elementen in de navigation property collections inputmediaassets en OutputMediaAssets-navigatieeigendom verwijzingen via een naamwaardepaar '__metadata: uri'.
* InputMediaAssets brengt een of meer elementen in kaart die u hebt gemaakt in Media Services. OutputMediaAssets worden gemaakt door het systeem. Ze verwijzen niet naar een bestaand actief.
* OutputMediaAssets kan worden benoemd met behulp van het kenmerk assetName. Als dit kenmerk niet aanwezig is, is de naam van de `<outputAsset>` OutputMediaAsset wat de waarde van de binnentekst van het element ook is met een achtervoegsel van de waarde Taaknaam of de waarde van het taak-id (in het geval dat de eigenschap Naam niet is gedefinieerd). Als u bijvoorbeeld een waarde voor assetName instelt op 'Voorbeeld', wordt de eigenschap OutputMediaAsset Name ingesteld op 'Voorbeeld'. Als u echter geen waarde voor assetName hebt ingesteld, maar de taaknaam hebt ingesteld op 'NewJob', dan is de naam OutputMediaAsset 'JobOutputAsset(waarde)_NewJob.'

## <a name="create-a-job-with-chained-tasks"></a>Een taak maken met geketende taken
In veel toepassingsscenario's willen ontwikkelaars een reeks verwerkingstaken maken. In Media Services u een reeks geketende taken maken. Elke taak voert verschillende verwerkingsstappen uit en kan verschillende mediaprocessors gebruiken. De geketende taken kunnen een asset van de ene taak naar de andere afstaan en een lineaire reeks taken op het item uitvoeren. De taken die in een taak worden uitgevoerd, hoeven echter niet in een reeks te zijn uitgevoerd. Wanneer u een geketende taak maakt, worden de geketende **ITask-objecten** gemaakt in één **IJob-object.**

> [!NOTE]
> Er is momenteel een limiet van 30 taken per taak. Als u meer dan 30 taken moet koppelen, maakt u meer dan één taak om de taken te bevatten.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Overwegingen
Ga als u taakketenen inschakelen:

* Een taak moet ten minste twee taken hebben.
* Er moet ten minste één taak zijn waarvan de input de output is van een andere taak in de taak.

## <a name="use-odata-batch-processing"></a>OData-batchverwerking gebruiken
In het volgende voorbeeld ziet u hoe u OData-batchverwerking gebruiken om een taak en taken te maken. Zie [OData-batchverwerking (Open Data Protocol) voor](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)informatie over batchverwerking.

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Een taak maken met behulp van een JobTemplate
Wanneer u meerdere elementen verwerkt met een algemene set taken, gebruikt u een JobTemplate om de standaardtaakvoorinstellingen op te geven of om de volgorde van taken in te stellen.

In het volgende voorbeeld ziet u hoe u een JobTemplate maakt met een Taaksjabloon die inline is gedefinieerd. De TaskTemplate gebruikt de Media Encoder Standard als MediaProcessor om het assetbestand te coderen. Echter, andere MediaProcessors kunnen ook worden gebruikt.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> In tegenstelling tot andere Media Services-entiteiten moet u voor elke TaskTemplate een nieuwe GUID-id definiëren en deze in de eigenschap taskTemplateId en Id in uw aanvraaginstantie plaatsen. Het inhoudsidentificatieschema moet het schema volgen dat is beschreven in Azure Media Services-entiteiten identificeren. Ook kan JobTemplates niet worden bijgewerkt. In plaats daarvan moet u een nieuwe maken met uw bijgewerkte wijzigingen.
>
>

Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTTP/1.1 201 Created

    . . .


In het volgende voorbeeld ziet u hoe u een taak maakt die verwijst naar een JobTemplate-id:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Geavanceerde coderingsfuncties om te verkennen
* [Miniaturen genereren](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniaturen genereren tijdens het coderen](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Video's bijsnijden tijdens het coderen](media-services-crop-video.md)
* [Coderingsvoorinstellingen aanpassen](media-services-custom-mes-presets-with-dotnet.md)
* [Een video met een afbeelding bedekken of markeren met een afbeelding](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u een taak maken om een item te coderen, raadpleegt u [Hoe u de voortgang van de taak controleren met Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Zie ook
[Mediaprocessors krijgen](media-services-rest-get-media-processor.md)
