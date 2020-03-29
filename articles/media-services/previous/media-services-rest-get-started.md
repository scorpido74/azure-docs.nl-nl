---
title: Aan de slag met het leveren van content op aanvraag met REST | Microsoft Documenten
description: In deze zelfstudie u de stappen doorlopen waarmee u een on-demand contentdelivery-toepassing met Azure Media Services implementeert met behulp van REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8989acc6d21a3c53be9d97c74ed7fbf03ba54819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773686"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Aan de slag met het leveren van content on demand met REST  

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Met deze quickstart u de stappen doorlopen waarmee u een VoD-toepassing (Video-on-Demand) (content delivery) implementeert met BEHULP van REST API's (Azure Media Services).

In deze zelfstudie maakt u kennis met de algemene werkstroom voor Media Services en de meest algemene programmeerobjecten en -taken die zijn vereist voor het ontwikkelen van Media Services. Na afloop van de zelfstudie u een voorbeeldmediabestand streamen of downloaden dat u hebt geüpload, gecodeerd en gedownload.

In de volgende afbeelding ziet u een aantal van de meest gebruikte objecten bij het ontwikkelen van VoD-toepassingen in het Media Services OData-model.

Klik op de afbeelding om deze in volledig formaat weer te geven.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Vereisten
De volgende voorwaarden zijn vereist om te beginnen met het ontwikkelen met Media Services met REST API's.

* Een Azure-account. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* Een Media Services-account. Zie [Een Media Services-account maken](media-services-portal-create-account.md) voor meer informatie over het maken van een Media Services-account.
* Inzicht in hoe te ontwikkelen met Media Services REST API. Zie Media [Services REST API-overzicht](media-services-rest-how-to-use.md)voor meer informatie.
* Een toepassing naar keuze die HTTP-verzoeken en -antwoorden kan verzenden. Deze zelfstudie maakt gebruik [van Fiddler](https://www.telerik.com/download/fiddler).

De volgende taken worden weergegeven in deze snelstart.

1. Streaming-eindpunt starten (vanuit Azure Portal).
2. Maak verbinding met het Media Services-account met REST API.
3. Maak een nieuw item en upload een videobestand met REST API.
4. Codeer het bronbestand in een set adaptieve bitrate MP4-bestanden met REST API.
5. Publiceer het item en ontvang streaming- en progressieve download-URL's met REST API.
6. Uw inhoud afspelen.

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-ID als u altijd dezelfde dagen / toegangsmachtigingen gebruikt, bijvoorbeeld beleid voor locators die bedoeld zijn om lang te blijven bestaan (niet-uploadbeleid). Zie voor meer informatie [dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel.

Zie [Azure Media Services REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)voor meer informatie over AMS REST-entiteiten die in dit artikel worden gebruikt. Zie ook [Azure Media Services-concepten](media-services-concepts.md).

>[!NOTE]
>Wanneer u toegang krijgt tot entiteiten in Media Services, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)meer informatie.

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Streaming-eindpunten starten met behulp van Azure Portal

Wanneer u met Azure Media Services werkt, is een van de meest voorkomende scenario's het leveren van video via adaptieve bitratestreaming. Media Services biedt dynamische pakketten waarmee u uw Adaptive Bitrate MP4-inhoud 'just in time' kunt leveren in de streaming-indelingen die door Media Services worden ondersteund (MPEG DASH, HLS, Smooth Streaming), zonder dat u vooraf verpakte versies van elk van deze streaming-indelingen hoeft op te slaan.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben.

U start het streaming-eindpunt als volgt:

1. Log in bij de [Azure-portal](https://portal.azure.com/).
2. Klik in het venster Instellingen op Streaming-eindpunten.
3. Klik op het standaardstreaming-eindpunt.

    Het venster DETAILS VAN STANDAARDSTREAMING-EINDPUNT wordt weergegeven.

4. Klik op het pictogram Start.
5. Klik op de knop Opslaan om uw wijzigingen op te slaan.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Verbinding maken met het Media Services-account met REST API

Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor informatie over hoe u verbinding maken met de AMS-API. 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Een nieuw item maken en een videobestand uploaden met REST API

In Media Services uploadt u de digitale bestanden naar (of neemt u deze op in) een asset. De **entiteit Asset** kan video,audio, afbeeldingen, miniatuurverzamelingen, teksttracks en ondertitelingsbestanden (en de metagegevens over deze bestanden) bevatten.  Zodra de bestanden zijn geüpload naar het item, wordt uw inhoud veilig opgeslagen in de cloud voor verdere verwerking en streaming.

Een van de waarden die u moet opgeven bij het maken van een asset zijn opties voor het maken van activa. De eigenschap **Opties** is een opsommingswaarde die de versleutelingsopties beschrijft waarmee een asset kan worden gemaakt. Een geldige waarde is een van de waarden uit de onderstaande lijst, niet een combinatie van waarden uit deze lijst:

* **Geen** = **0** - Er wordt geen versleuteling gebruikt. Bij gebruik van deze optie is uw inhoud niet beveiligd tijdens het transport of in rust in opslag.
    Als u een MP4-bestand wilt leveren via progressief downloaden, gebruikt u deze optie.
* **StorageEncrypted** = **1** - Versleutelt uw duidelijke inhoud lokaal met behulp van AES-256-bits versleuteling en uploadt deze vervolgens naar Azure Storage, waar deze in rust versleuteld wordt opgeslagen. De versleuteling van assets die zijn beveiligd met Storage Encryption, wordt automatisch ongedaan gemaakt en de assets worden automatisch in een versleuteld bestandssysteem geplaatst voordat ze worden gecodeerd. Eventueel kunnen ze opnieuw worden versleuteld voordat ze opnieuw worden geüpload als een nieuwe uitvoerasset. Storage Encryption wordt voornamelijk gebruikt om uw invoerbestanden met media van hoge kwaliteit die zijn opgeslagen op de schijf, te beveiligen met een sterke versleuteling.
* **CommonEncryptionProtected** = **2** - Gebruik deze optie als u inhoud uploadt die al is versleuteld en beveiligd met Common Encryption of PlayReady DRM (bijvoorbeeld Smooth Streaming protected met PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** – Gebruik deze optie als u HLS versleuteld uploadt met AES. De bestanden moeten zijn gecodeerd en versleuteld door Transform Manager.

### <a name="create-an-asset"></a>Een asset maken
Een item is een container voor meerdere typen of sets objecten in Media Services, waaronder video, audio, afbeeldingen, miniatuurverzamelingen, teksttracks en bestanden met ondertiteling. In de REST-API moet het maken van een actief post-aanvraag naar Media Services verzenden en alle eigendomsgegevens over uw asset in de aanvraaginstantie plaatsen.

In het volgende voorbeeld ziet u hoe u een actief maakt.

**HTTP-aanvraag**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**HTTP-antwoord**

Als dit is gelukt, wordt het volgende geretourneerd:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Een assetbestand maken
De entiteit [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) vertegenwoordigt een video- of audiobestand dat is opgeslagen in een blobcontainer. Een assetbestand is altijd gekoppeld aan een actief en een asset kan een of meer AssetFiles bevatten. De encoder-taak Media Services mislukt als een assetbestandobject niet is gekoppeld aan een digitaal bestand in een blobcontainer.

Nadat u uw digitale mediabestand hebt geüpload naar een blobcontainer, gebruikt u het **HTTP-verzoek SAMENVOEGEN** om het AssetFile bij te werken met informatie over uw mediabestand (zoals later in het onderwerp wordt weergegeven).

**HTTP-aanvraag**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-antwoord**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Het AccessPolicy maken met schrijftoestemming
Voordat u bestanden uploadt naar blob-opslag, stelt u de toegangsbeleidsrechten voor het schrijven in op een actief. Om dat te doen, plaatst u een HTTP-verzoek naar de entiteitsset AccessPolicies. Definieer een DurationInMinutes-waarde bij het maken of u ontvangt een foutbericht van 500 interne server als reactie. Zie [Toegangsbeleid](https://docs.microsoft.com/rest/api/media/operations/accesspolicy)voor meer informatie over toegangsbeleid.

In het volgende voorbeeld ziet u hoe u een AccessPolicy maakt:

**HTTP-aanvraag**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**HTTP-antwoord**

Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>De URL van het uploaden ophalen

Als u de werkelijke upload-URL wilt ontvangen, maakt u een SAS-locator. Locators definiëren de begintijd en het type verbindingseindpunt voor clients die toegang willen krijgen tot bestanden in een asset. U meerdere locator-entiteiten maken voor een bepaald AccessPolicy- en Asset-paar om verschillende clientaanvragen en -behoeften te verwerken. Elk van deze locators gebruikt de StartTime-waarde plus de waarde DurationInMinutes van het AccessPolicy om te bepalen hoe lang een URL kan worden gebruikt. Zie [Locator](https://docs.microsoft.com/rest/api/media/operations/locator)voor meer informatie.

Een SAS-URL heeft de volgende indeling:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Hierbij geldt het volgende:

* U niet meer dan vijf unieke locators tegelijk aan een bepaald item hebben. 
* Als u uw bestanden onmiddellijk moet uploaden, moet u uw StartTime-waarde instellen op vijf minuten voor de huidige tijd. Dit komt omdat er mogelijk een klok scheeftrekking tussen uw client machine en Media Services. Uw StartTime-waarde moet ook in de volgende DateTime-indeling staan: YYYY-MM-DDTHH:mm:ssZ (bijvoorbeeld "2014-05-23T17:53:50Z").    
* Er kan een vertraging van 30-40 seconden zijn nadat een Locator is gemaakt wanneer deze beschikbaar is voor gebruik. Dit probleem is van toepassing op zowel [De URL van SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) als de Plaats van de OorsprongsLocators.

In het volgende voorbeeld ziet u hoe u een SAS URL-locator maakt, zoals gedefinieerd door de eigenschap Type in de aanvraaginstantie ("1" voor een SAS-locator en "2" voor een on-demand origin locator). De eigenschap **Path** die is geretourneerd, bevat de URL die u moet gebruiken om uw bestand te uploaden.

**HTTP-aanvraag**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP-antwoord**

Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Een bestand uploaden naar een blobopslagcontainer
Zodra u de accesspolicy- en locator-set hebt ingesteld, wordt het werkelijke bestand geüpload naar een Azure blob-opslagcontainer met behulp van de AZURE Storage REST API's. U moet de bestanden uploaden als blokblobs. Paginablobs worden niet ondersteund door Azure Media Services.  

> [!NOTE]
> U moet de bestandsnaam voor het bestand dat u wilt uploaden toevoegen aan de waarde van het **pad** van de locator die in de vorige sectie is ontvangen. Bijvoorbeeld `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Zie [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)voor meer informatie over het werken met Azure-opslagblobs.

### <a name="update-the-assetfile"></a>Het AssetFile bijwerken
Nu u uw bestand hebt geüpload, werkt u de gegevens van de bestandsgrootte (en andere) bij. Bijvoorbeeld:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-antwoord**

Als dit is gelukt, wordt het volgende geretourneerd:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>De locator en accesspolicy verwijderen
**HTTP-aanvraag**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-antwoord**

Als dit is gelukt, wordt het volgende geretourneerd:

    HTTP/1.1 204 No Content
    ...

**HTTP-aanvraag**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-antwoord**

Als dit is gelukt, wordt het volgende geretourneerd:

    HTTP/1.1 204 No Content
    ...

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Codeer het bronbestand in een set adaptieve bitrate MP4-bestanden

Na inname van assets in Media Services kunnen media worden gecodeerd, getransmuxeerd, watergemarkeerd, enzovoort, voordat het aan klanten wordt geleverd. Deze activiteiten worden gepland en uitgevoerd op meerdere achtergrondrolinstanties om hoge prestaties en een hoge beschikbaarheid te garanderen. Deze activiteiten worden Jobs genoemd en elke taak bestaat uit atomaire taken die het eigenlijke werk aan het assetbestand uitvoeren (zie [Taak](https://docs.microsoft.com/rest/api/media/operations/job), [taakbeschrijvingen](https://docs.microsoft.com/rest/api/media/operations/task) voor meer informatie).

Zoals eerder vermeld, is bij het werken met Azure Media Services een van de meest voorkomende scenario's het leveren van adaptieve bitrate streaming aan uw klanten. Media Services kan een set adaptieve bitrate MP4-bestanden dynamisch verpakken in een van de volgende indelingen: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

In de volgende sectie ziet u hoe u een taak maakt die één coderingstaak bevat. De taak geeft aan het mezzaninebestand te transcoderen in een set adaptieve bitrate MP4's met behulp van **Media Encoder Standard**. De sectie laat ook zien hoe u de voortgang van de taakverwerking controleren. Wanneer de taak is voltooid, u locators maken die nodig zijn om toegang te krijgen tot uw assets.

### <a name="get-a-media-processor"></a>Een mediaprocessor
In Media Services is een mediaprocessor een onderdeel dat een specifieke verwerkingstaak verwerkt, zoals het coderen, converteren van formaten, versleutelen of decoderen van media-inhoud. Voor de coderingstaak die in deze zelfstudie wordt weergegeven, gaan we de Media Encoder-standaard gebruiken.

De volgende code vraagt de encoder id.

**HTTP-aanvraag**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-antwoord**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Een taak maken
Elke taak kan een of meer taken hebben, afhankelijk van het type verwerking dat u wilt uitvoeren. Via de REST API u taken en bijbehorende taken op twee manieren maken: Taken kunnen inline worden gedefinieerd via de eigenschap Tasks navigation op taakentiteiten of via OData-batchverwerking. De Media Services SDK maakt gebruik van batchverwerking. Voor de leesbaarheid van de codevoorbeelden in dit artikel worden taken echter inline gedefinieerd. Zie [OData-batchverwerking (Open Data Protocol) voor](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)informatie over batchverwerking.

In het volgende voorbeeld ziet u hoe u een taak maakt en plaatst met één taakset om een video te coderen met een specifieke resolutie en kwaliteit. De volgende documentatiesectie bevat de lijst met alle [taakvoorinstellingen](https://msdn.microsoft.com/library/mt269960) die worden ondersteund door de Media Encoder Standard-processor.  

**HTTP-aanvraag**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP-antwoord**

Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Er zijn een paar belangrijke dingen op te merken in een job verzoek:

* Eigenschappen van taaktekst MOETEN letterlijke XML gebruiken om het aantal invoer- of uitvoerelementen te definiëren dat door de taak wordt gebruikt. Het artikel Taak bevat de XML-schemadefinitie voor de XML.
* In de definitie van taakinstantie `<inputAsset>` `<outputAsset>` moet elke innerlijke waarde voor en moet worden ingesteld als JobInputAsset(waarde) of JobOutputAsset(waarde).
* Een taak kan meerdere uitvoerelementen hebben. Eén JobOutputAsset(x) kan slechts één keer worden gebruikt als uitvoer van een taak in een taak.
* U JobInputAsset of JobOutputAsset opgeven als invoerelement van een taak.
* Taken mogen geen cyclus vormen.
* De waardeparameter die u doorgeeft aan JobInputAsset of JobOutputAsset vertegenwoordigt de indexwaarde voor een actief. De werkelijke activa worden gedefinieerd in de navigatie-eigenschappen InputMediaAssets en OutputMediaAssets op de definitie van de entiteit Job.

> [!NOTE]
> Omdat Media Services is gebouwd op OData v3, worden de afzonderlijke assets in InputMediaAssets en OutputMediaAssets navigatievastgoedverzamelingen verwezen via een "__metadata: uri" naamwaardepaar.
>
>

* InputMediaAssets brengt een of meer assets die u hebt gemaakt in Media Services. OutputMediaAssets worden gemaakt door het systeem. Ze verwijzen niet naar een bestaand actief.
* OutputMediaAssets kan worden benoemd met het kenmerk assetName. Als dit kenmerk niet aanwezig is, is de naam van de `<outputAsset>` OutputMediaAsset wat de waarde van de binnentekst van het element ook is met een achtervoegsel van de waarde Taaknaam of de waarde van het taak-id (in het geval dat de eigenschap Naam niet is gedefinieerd). Als u bijvoorbeeld een waarde voor assetName instelt op 'Voorbeeld', wordt de eigenschap OutputMediaAsset Name ingesteld op 'Voorbeeld'. Als u echter geen waarde voor assetName hebt ingesteld, maar de taaknaam hebt ingesteld op 'NewJob', dan is de naam OutputMediaAsset "JobOutputAsset(value)_NewJob".

    In het volgende voorbeeld ziet u hoe u het kenmerk assetName instelt:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Ga als u taakketenen inschakelen:

  * Een taak moet ten minste twee taken hebben
  * Er moet ten minste één taak zijn waarvan de input de uitvoer van een andere taak in de taak is.

Zie voor meer informatie [een coderingstaak maken met de API Media Services REST](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Voortgang verwerking controleren
U de status Van taak ophalen met behulp van de eigenschap Status, zoals in het volgende voorbeeld wordt weergegeven:

**HTTP-aanvraag**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP-antwoord**

Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Een taak annuleren
Met Media Services u lopende taken annuleren via de functie CancelJob. Met deze oproep wordt een foutcode van 400 geretourneerd als u een taak probeert te annuleren wanneer de status is geannuleerd, geannuleerd, foutloos of voltooid.

In het volgende voorbeeld ziet u hoe u CancelJob aanroepen.

**HTTP-aanvraag**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Als dit lukt, wordt een 204-antwoordcode geretourneerd zonder berichthoofd.

> [!NOTE]
> U moet de taak-id (normaal nb:jid:UUID: somevalue) coderen wanneer u deze als parameter doorgeeft aan CancelJob.
>
>

### <a name="get-the-output-asset"></a>De uitvoerasset ophalen
In de volgende code ziet u hoe u de uitvoerasset-id opvraagt.

**HTTP-aanvraag**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-antwoord**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Publiceer het item en ontvang streaming- en progressieve download-URL's met REST API

Als u een asset wilt streamen of downloaden, moet u deze eerste publiceren door een locator te maken. Locators bieden toegang tot bestanden in de asset. Media Services ondersteunt twee typen locators: OnDemandOrigin-locators, voor het streamen van media (bijvoorbeeld MPEG DASH, HLS, of Smooth Streaming) en SAS-locators (Shared Access Signature), voor het downloaden van media-bestanden. 

Zodra u de locators hebt gemaakt, u de URL's bouwen die worden gebruikt om uw bestanden te streamen of te downloaden.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben.

Een streaming-URL voor Smooth Streaming heeft de volgende indeling:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Een streaming-URL voor HLS heeft de volgende indeling:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Een streaming-URL voor MPEG DASH heeft de volgende indeling:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Een SAS-URL die wordt gebruikt om bestanden te downloaden, heeft de volgende indeling:

    {blob container name}/{asset name}/{file name}/{SAS signature}

In deze sectie ziet u hoe u de volgende taken uitvoert die nodig zijn om uw assets te "publiceren".  

* Het AccessPolicy maken met leestoestemming
* Een SAS-URL maken voor het downloaden van inhoud
* Een oorsprongs-URL maken voor het streamen van inhoud

### <a name="creating-the-accesspolicy-with-read-permission"></a>Het AccessPolicy maken met leestoestemming
Voordat u media-inhoud downloadt of streamt, definieert u eerst een AccessPolicy met leesmachtigingen en maakt u de juiste Locator-entiteit die het type leveringsmechanisme aangeeft dat u voor uw klanten wilt inschakelen. Zie [Eigenschappen van accesspolicy entity](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties)voor meer informatie over de beschikbare eigenschappen.

In het volgende voorbeeld ziet u hoe u het Toegangsbeleid voor leesmachtigingen voor een bepaald item opgeeft.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Als dit is gelukt, wordt een succescode van 201 geretourneerd waarin de entiteit AccessPolicy wordt beschreven die u hebt gemaakt. Vervolgens gebruikt u de AccessPolicy Id samen met de asset-id van het actief dat het bestand bevat dat u wilt leveren (zoals een uitvoerelement) om de entiteit Locator te maken.

> [!NOTE]
> Deze basiswerkstroom is hetzelfde als het uploaden van een bestand bij het innemen van een asset (zoals eerder in dit onderwerp werd besproken). Als u (of uw klanten) onmiddellijk toegang moet krijgen tot uw bestanden, stelt u ook uw StartTime-waarde in op vijf minuten voor de huidige tijd als u (of uw klanten) onmiddellijk toegang moet krijgen tot uw bestanden. Deze actie is noodzakelijk omdat er een klokscheefheid kan zijn tussen de client en Media Services. De waarde StartTime moet in de volgende Datumtijd-indeling staan: YYYY-MM-DDTHH:mm:ssZ (bijvoorbeeld "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Een SAS-URL maken voor het downloaden van inhoud
In de volgende code ziet u hoe u een URL krijgt die kan worden gebruikt om een eerder gemaakt en geüpload mediabestand te downloaden. Het AccessPolicy heeft leesmachtigingen ingesteld en het pad locator verwijst naar een SAS-download-URL.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

De eigenschap geretourneerd **pad** bevat de SAS-URL.

> [!NOTE]
> Als u opslagversleutelde inhoud downloadt, moet u deze handmatig decoderen voordat u deze rendert, of de MediaProcessor opslagdecryptie gebruiken in een verwerkingstaak om verwerkte bestanden in de clear naar een OutputAsset uit te voeren en vervolgens te downloaden van dat actief. Zie Een coderingstaak maken met de API voor mediaservices voor meer informatie over verwerking. SAS URL-locators kunnen ook niet worden bijgewerkt nadat ze zijn gemaakt. U bijvoorbeeld niet dezelfde Locator met een bijgewerkte StartTime-waarde opnieuw gebruiken. Dit komt door de manier waarop SAS URL's worden gemaakt. Als u toegang wilt krijgen tot een item om te downloaden nadat een locator is verlopen, moet u een nieuwe maken met een nieuwe StartTime.
>
>

### <a name="download-files"></a>Bestanden downloaden
Zodra u de accesspolicy- en locator-set hebt ingesteld, u bestanden downloaden met behulp van de AZURE Storage REST API's.  

> [!NOTE]
> U moet de bestandsnaam voor het bestand dat u wilt downloaden toevoegen aan de waarde van het **zoekpad** dat in de vorige sectie is ontvangen. Bijvoorbeeld: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Zie [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)voor meer informatie over het werken met Azure-opslagblobs.

Als gevolg van de coderingstaak die u eerder hebt uitgevoerd (coderen in Adaptieve MP4-set), hebt u meerdere MP4-bestanden die u geleidelijk downloaden. Bijvoorbeeld:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Een streaming-URL maken voor het streamen van inhoud
In de volgende code ziet u hoe u een streaming URL-locator maakt:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Als u een URL van de oorsprong van vloeiend streamen wilt streamen in een streaming mediaspeler, moet u de eigenschap Pad toevoegen met de naam van het manifestbestand Vloeiend streamen, gevolgd door "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Om HLS te streamen, wordt u toegevoegd (format=m3u8-aapl) na het "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Als u MPEG DASH wilt streamen, wordt u toegevoegd (format=mpd-time-csf) na het "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a name="play-your-content"></a><a id="play"></a>Uw inhoud afspelen
Gebruik [Azure Media Services Player](https://aka.ms/azuremediaplayer) om uw video te streamen.

Als u progressief downloaden wilt testen, plakt u een URL in een browser (bijvoorbeeld IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Volgende stappen: Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
