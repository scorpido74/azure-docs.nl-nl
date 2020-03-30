---
title: Bestanden uploaden naar een Azure Media Services-account met REST | Microsoft Documenten
description: Meer informatie over het verkrijgen van media-inhoud in Media Services door het maken en uploaden van assets.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d5b84a9d216457720e9bd4e17b002d6ab9490f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888603"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Bestanden uploaden naar een Media Services-account met REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [Rest](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

In Media Services uploadt u de digitale bestanden naar (of neemt u deze op in) een asset. De [entiteit Asset](https://docs.microsoft.com/rest/api/media/operations/asset) kan video,audio, afbeeldingen, miniatuurverzamelingen, teksttracks en ondertitelingsbestanden (en de metagegevens over deze bestanden) bevatten.  Zodra de bestanden zijn geüpload naar het item, wordt uw inhoud veilig opgeslagen in de cloud voor verdere verwerking en streaming. 

In deze zelfstudie leert u hoe u een bestand en andere bewerking uploadt die eraan is gekoppeld:

> [!div class="checklist"]
> * Postman instellen voor alle uploadbewerkingen
> * Verbinding met Media Services maken 
> * Een toegangsbeleid maken met schrijftoestemming
> * Een asset maken
> * Een SAS-locator maken en de upload-URL maken
> * Een bestand uploaden naar blob-opslag met de URL van het uploaden
> * Een metagegevens maken in het item voor het mediabestand dat u hebt geüpload

## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
- [Maak een Azure Media Services-account met behulp van de Azure-portal.](media-services-portal-create-account.md)
- Bekijk het [overzichtsartikel Accessing Azure Media Services API met AAD-verificatieoverzicht.](media-services-use-aad-auth-to-access-ams-api.md)
- Ook voor meer informatie Raadpleeg de [Azure AD-verificatie gebruiken om toegang te krijgen tot de Media Services API met](https://docs.microsoft.com/azure/media-services/previous/media-services-rest-connect-with-aad) REST-artikel.
- Postman configureren **zoals** beschreven in [Api-aanroepen configureren voor Media Services REST.](media-rest-apis-with-postman.md)

## <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing bij het gebruik van de Media Services REST API:
 
* Wanneer u toegang krijgt tot entiteiten met de API voor Media Services REST, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)meer informatie. <br/>De Postman collectie gebruikt in deze tutorial zorgt voor het instellen van alle benodigde headers.
* Media Services gebruikt de waarde van de eigenschap IAssetFile.Name bij het bouwen van URL's voor de streaming-inhoud (bijvoorbeeld http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Om deze reden is procentcodering niet toegestaan. De waarde van de eigenschap **Naam** kan geen van de volgende tekens hebben [die zijn gereserveerd](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();::@&=+$,/?%#[]". Ook kan er slechts één '.' zijn voor de bestandsnaamextensie.
* De lengte van de naam mag niet groter zijn dan 260 tekens.
* Er is een limiet voor de maximale bestandsgrootte die wordt ondersteund voor verwerking in Media Services. Raadpleeg [dit](media-services-quotas-and-limitations.md) artikel voor meer informatie over de maximale bestandsgrootte.

## <a name="set-up-postman"></a>Postman instellen

Zie [Postbode configureren](media-rest-apis-with-postman.md)voor stappen over het instellen van Postbode voor deze zelfstudie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

1. Voeg verbindingswaarden toe aan uw omgeving. 

    Sommige variabelen die deel uitmaken van de **MediaServices-omgeving** [environment](postman-environment.md) moeten handmatig worden ingesteld voordat u beginnen met het uitvoeren van bewerkingen die zijn gedefinieerd in de [verzameling.](postman-collection.md)

    Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor de eerste vijf variabelen. 

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-import-env.png)
2. Geef de waarde op voor de omgevingsvariabele **MediaFileName.**

    Geef de bestandsnaam op van de media die u wilt uploaden. In dit voorbeeld gaan we de BigBuckBunny.mp4 uploaden. 
3. Onderzoek het **azureMediaServices.postman_environment.json-bestand.** U zult zien dat bijna alle bewerkingen in de collectie een "test" script uitvoeren. De scripts nemen bepaalde waarden die door het antwoord worden geretourneerd en stellen de juiste omgevingsvariabelen in.

    De eerste bewerking krijgt bijvoorbeeld een toegangstoken en stelt deze in op de **AccessToken-omgevingsvariabele** die in alle andere bewerkingen wordt gebruikt.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. Klik links van het **venster Postbode** op **1. Ontvang AAD Auth-token** -> **Get Azure AD-token voor serviceprincipal**.

    Het URL-gedeelte is gevuld met de **azureadstsendendpoint-omgevingsvariabele** (eerder in de zelfstudie stelt u de waarden in van omgevingsvariabelen die de verzameling ondersteunen).

    ![Bestand uploaden](./media/media-services-rest-upload-files/postment-get-token.png)

5. Druk op **Verzenden**.

    U het antwoord zien dat access_token bevat. Het "test"-script neemt deze waarde en stelt de **AccessToken-omgevingsvariabele** in (zoals hierboven beschreven). Als u uw omgevingsvariabelen onderzoekt, ziet u dat deze variabele nu de waarde van het toegangstoken (token aan toonder) bevat die in de rest van de bewerkingen wordt gebruikt. 

    Als het token verloopt, gaat u opnieuw door de stap Azure AD Token for Service Principal. 

## <a name="create-an-access-policy-with-write-permission"></a>Een toegangsbeleid maken met schrijftoestemming

### <a name="overview"></a>Overzicht 

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Zie voor meer informatie [dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel.

Voordat u bestanden uploadt naar blob-opslag, stelt u de toegangsbeleidsrechten voor het schrijven in op een actief. Om dat te doen, plaatst u een HTTP-verzoek naar de entiteitsset AccessPolicies. Definieer een DurationInMinutes-waarde bij het maken of u ontvangt een foutbericht van 500 interne server als reactie. Zie [Toegangsbeleid](https://docs.microsoft.com/rest/api/media/operations/accesspolicy)voor meer informatie over toegangsbeleid.

### <a name="create-an-access-policy"></a>Een toegangsbeleid maken

1. Selecteer **AccessPolicy** -> **AccessPolicy Create AccessPolicy for Upload**.
2. Druk op **Verzenden**.

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-access-policy.png)

    Het 'test'-script krijgt de AccessPolicy Id en stelt de juiste omgevingsvariabele in.

## <a name="create-an-asset"></a>Een asset maken

### <a name="overview"></a>Overzicht

Een [item](https://docs.microsoft.com/rest/api/media/operations/asset) is een container voor meerdere typen of sets objecten in Media Services, waaronder video, audio, afbeeldingen, miniatuurverzamelingen, teksttracks en bestanden met ondertiteling. In de REST-API moet het maken van een actief post-aanvraag naar Media Services verzenden en alle eigendomsgegevens over uw asset in de aanvraaginstantie plaatsen.

Een van de eigenschappen die u toevoegen bij het maken van een asset is **Opties**. U een van de volgende versleutelingsopties opgeven: **Geen** (standaard, geen versleuteling wordt gebruikt), **StorageEncrypted** (voor inhoud die vooraf is versleuteld met opslagversleuteling aan de clientzijde), **CommonEncryptionProtected**of **EnvelopeEncryptionProtected**. Wanneer u een versleuteld item hebt, moet u een leveringsbeleid configureren. Zie [Beleid voor het configureren van asset delivery .](media-services-rest-configure-asset-delivery-policy.md)

Als uw asset is versleuteld, moet u een **ContentKey** maken en deze koppelen aan uw asset zoals beschreven in het volgende artikel: [Een ContentKey maken.](media-services-rest-create-contentkey.md) Nadat u de bestanden in het activum hebt geüpload, moet u de versleutelingseigenschappen van de **entiteit AssetFile** bijwerken met de waarden die u hebt gekregen tijdens de **assetversleuteling.** Doe dit met het **HTTP-verzoek SAMENVOEGEN.** 

In dit voorbeeld maken we een onversleuteld actief. 

### <a name="create-an-asset"></a>Een asset maken

1. Selecteer **Activa** -> **Maken Asset**.
2. Druk op **Verzenden**.

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-create-asset.png)

    Het "test"-script krijgt de Asset Id en stelt de juiste omgevingsvariabele in.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Een SAS-locator maken en de URL van uploaden maken

### <a name="overview"></a>Overzicht

Zodra u de accesspolicy- en locator-set hebt ingesteld, wordt het werkelijke bestand geüpload naar een Azure Blob Storage-container met behulp van de AZURE Storage REST API's. U moet de bestanden uploaden als blokblobs. Paginablobs worden niet ondersteund door Azure Media Services.  

Zie [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)voor meer informatie over het werken met Azure-opslagblobs.

Als u de werkelijke upload-URL wilt ontvangen, maakt u een SAS Locator (zie hieronder). Locators definiëren de begintijd en het type verbindingseindpunt voor clients die toegang willen krijgen tot bestanden in een asset. U meerdere locator-entiteiten maken voor een bepaald AccessPolicy- en Asset-paar om verschillende clientaanvragen en -behoeften te verwerken. Elk van deze locators gebruikt de StartTime-waarde plus de waarde DurationInMinutes van het AccessPolicy om te bepalen hoe lang een URL kan worden gebruikt. Zie [Locator](https://docs.microsoft.com/rest/api/media/operations/locator)voor meer informatie.

Een SAS-URL heeft de volgende indeling:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Overwegingen

Hierbij geldt het volgende:

* U niet meer dan vijf unieke locators tegelijk aan een bepaald item hebben. Zie Locator voor meer informatie.
* Als u uw bestanden onmiddellijk moet uploaden, moet u uw StartTime-waarde instellen op vijf minuten voor de huidige tijd. Dit komt omdat er mogelijk een klok scheeftrekking tussen uw client machine en Media Services. Uw StartTime-waarde moet ook in de volgende DateTime-indeling staan: YYYY-MM-DDTHH:mm:ssZ (bijvoorbeeld "2014-05-23T17:53:50Z").    
* Er kan een vertraging van 30-40 seconden zijn nadat een Locator is gemaakt wanneer deze beschikbaar is voor gebruik.

### <a name="create-a-sas-locator"></a>Een SAS-locator maken

1. Selecteer **Locator** -> **Create SAS Locator**.
2. Druk op **Verzenden**.

    Met het script 'test' wordt de URL 'Uploaden' gemaakt op basis van de opgegeven mediabestandsnaam en sas-locator-informatie en wordt de juiste omgevingsvariabele ingesteld.

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Een bestand uploaden naar blob-opslag met de URL van het uploaden

### <a name="overview"></a>Overzicht

Nu u de upload-URL hebt, moet u bepaalde code schrijven met behulp van de Azure Blob API's om uw bestand rechtstreeks naar de SAS-container te uploaden. Raadpleeg voor meer informatie de volgende artikelen:

- [De Azure Storage REST API gebruiken](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Blob plaatsen](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Blobs uploaden naar Blob-opslag](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Een bestand uploaden met Postman

Als voorbeeld gebruiken we Postman om een klein .mp4-bestand te uploaden. Er kan een limiet voor bestandsgrootte zijn voor het uploaden van binaire via Postman.

De uploadaanvraag maakt geen deel uit van de **AzureMedia-verzameling.** 

Een nieuw verzoek maken en instellen:
1. Druk **+** op , om een nieuw tabblad van aanvragen te maken.
2. Selecteer **PUT-bewerking** en plak **{{UploadURL}}** in de URL.
2. Het tabblad **Autorisatie** laten zoals het is (stel het niet in op het **token aan toonder).**
3. Geef op het tabblad **Kopteksten** op: **Toets:**"x-ms-blob-type" en **Waarde:**"BlockBlob".
2. Klik op het tabblad **Hoofd** op **binair**.
4. Kies het bestand met de naam die u hebt opgegeven in de omgevingsvariabele **MediaFileName.**
5. Druk op **Verzenden**.

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Een metagegevens maken in het item

Zodra het bestand is geüpload, moet u een metagegevens maken in het item voor het mediabestand dat u hebt geüpload naar de blob-opslag die is gekoppeld aan uw asset.

1. Selecteer **AssetFiles** -> **CreateFileInfos**.
2. Druk op **Verzenden**.

    ![Bestand uploaden](./media/media-services-rest-upload-files/postman-create-file-info.png)

Het bestand moet worden geüpload en de metadata set.

## <a name="validate"></a>Valideren

Als u wilt valideren dat het bestand is geüpload, u het [AssetFile opvragen](https://docs.microsoft.com/rest/api/media/operations/assetfile) en de **ContentFileSize** (of andere details) vergelijken met wat u verwacht te zien in het nieuwe item. 

De volgende **GET-bewerking** brengt bijvoorbeeld bestandsgegevens voor uw assetbestand met zich mee (in of in het geval het BigBuckBunny.mp4-bestand). De query gebruikt de [omgevingsvariabelen](postman-environment.md) die u eerder hebt ingesteld.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Het antwoord bevat grootte, naam en andere informatie.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Volgende stappen

U kunt nu de geüploade assets coderen. Zie [Assets coderen](media-services-portal-encode.md) voor meer informatie.

U kunt ook Azure Functions gebruiken om een coderingstaak te activeren op basis van een bestand dat binnenkomt in de geconfigureerde container. Zie [dit voorbeeld](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ) voor meer informatie.

