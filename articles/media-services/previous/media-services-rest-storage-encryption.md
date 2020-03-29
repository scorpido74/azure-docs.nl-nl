---
title: Uw inhoud versleutelen met opslagversleuteling met AMS REST API
description: Meer informatie over het versleutelen van uw inhoud met opslagversleuteling met AMS REST API's.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 2a5ef1837375cc395a871f9a9860fa8bde572a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773588"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Uw inhoud versleutelen met opslagversleuteling 

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.   > Er worden geen nieuwe functies of functionaliteit toegevoegd aan Media Services v2. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)
>   

Het wordt ten zeerste aanbevolen om uw inhoud lokaal te versleutelen met behulp van AES-256-bits versleuteling en deze vervolgens te uploaden naar Azure Storage, waar deze in rust versleuteld wordt opgeslagen.

In dit artikel vindt u een overzicht van AMS-opslagversleuteling en wordt u weergegeven hoe u de versleutelde opslaginhoud uploadt:

* Maak een inhoudssleutel.
* Maak een asset. Stel de optie AssetCreationin in op StorageEncryption bij het maken van het item.
  
     Versleutelde elementen zijn gekoppeld aan inhoudssleutels.
* Koppel de inhoudssleutel aan het item.  
* Stel de coderingsgerelateerde parameters in op de assetfile-entiteiten.

## <a name="considerations"></a>Overwegingen 

Als u een versleuteld opslagmiddel wilt leveren, moet u het leveringsbeleid van het actief configureren. Voordat uw asset kan worden gestreamd, verwijdert de streamingserver de opslagversleuteling en streamt u uw inhoud met behulp van het opgegeven leveringsbeleid. Zie [Beleid voor het configureren van asset delivery policies](media-services-rest-configure-asset-delivery-policy.md)voor meer informatie .

Wanneer u toegang krijgt tot entiteiten in Media Services, moet u specifieke koptekstvelden en -waarden instellen in uw HTTP-aanvragen. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)meer informatie. 

### <a name="storage-side-encryption"></a>Versleuteling aan de opslagzijde

|Versleuteling, optie|Beschrijving|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-opslagversleuteling|AES-256 encryptie, sleutel beheerd door Media Services|Ondersteund<sup>(1)</sup>|Niet ondersteund<sup>(2)</sup>|
|[Opslagserviceversleuteling voor gegevens in rust](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Server-side encryptie aangeboden door Azure Storage, sleutel beheerd door Azure of door de klant|Ondersteund|Ondersteund|
|[Versleuteling aan de clientzijde van opslag](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client-side encryptie aangeboden door Azure-opslag, sleutel beheerd door de klant in Key Vault|Niet ondersteund|Niet ondersteund|

<sup>1</sup> Hoewel Media Services de behandeling van inhoud in de duidelijke/zonder enige vorm van versleuteling ondersteunt, wordt dit niet aanbevolen.

<sup>2</sup> In Media Services v3 wordt opslagversleuteling (AES-256-versleuteling) alleen ondersteund voor achterwaartse compatibiliteit wanneer uw assets zijn gemaakt met Media Services v2. Dit betekent dat V3 werkt met bestaande opslagversleutelde assets, maar het maken van nieuwe niet toestaat.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [Toegang tot de Azure Media Services API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor informatie over hoe u verbinding maken met de AMS-API. 

## <a name="storage-encryption-overview"></a>Overzicht van opslagversleuteling
De AMS-opslagversleuteling past versleuteling in de **AES-CTR-modus** toe op het hele bestand.  De AES-CTR-modus is een blokcijfer dat willekeurige lengtegegevens kan versleutelen zonder dat u hoeft op te vullen. Het werkt door het versleutelen van een teller blok met de AES-algoritme en vervolgens XOR-ing de output van AES met de gegevens te versleutelen of te decoderen.  Het gebruikte tellerblok wordt geconstrueerd door de waarde van de Initialisatievector te kopiëren naar bytes 0 tot 7 van de tegenwaarde en bytes 8 tot 15 van de tegenwaarde worden ingesteld op nul. Van het 16-byte tellerblok worden bytes 8 tot 15 (dat wil zeggen de minst significante bytes) gebruikt als een eenvoudig 64-bits niet-ondertekend geheel dat wordt verhoogd met één voor elk volgend blok gegevens dat wordt verwerkt en in netwerkbytevolgorde wordt bewaard. Als dit gehele getal de maximale waarde bereikt (0xFFFFFFFFFFFFFFFFFFF), wordt de blokteller opnieuw ingesteld op nul (bytes 8 tot 15) zonder dat dit gevolgen heeft voor de andere 64 bits van de teller (dat wil zeggen bytes 0 tot 7).   Om de beveiliging van de versleuteling van de AES-CTR-modus te behouden, moet de initialisatievectorwaarde voor een bepaalde sleutel-id voor elke inhoudssleutel uniek zijn voor elk bestand en moeten bestanden minder dan 2^64 blokken lang zijn.  Deze unieke waarde is om ervoor te zorgen dat een tegenwaarde nooit wordt hergebruikt met een bepaalde sleutel. Zie [deze wikipagina](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) voor meer informatie over de CTR-modus (het wikiartikel gebruikt de term "Nonce" in plaats van "Initialisatievector").

Gebruik **opslagversleuteling** om uw duidelijke inhoud lokaal te versleutelen met Behulp van AES-256-bits versleuteling en upload deze vervolgens naar Azure Storage, waar deze in rust versleuteld is opgeslagen. Activa die worden beschermd met opslagversleuteling worden automatisch onversleuteld en in een versleuteld bestandssysteem geplaatst voordat ze worden gecodeerd en worden optioneel opnieuw versleuteld voordat ze worden geüpload als een nieuw uitvoerelement. De primaire use case voor opslagversleuteling is wanneer u uw hoogwaardige invoermediabestanden wilt beveiligen met sterke versleuteling in rust op schijf.

Als u een opslagversleuteld activum wilt leveren, moet u het leveringsbeleid van het activum configureren, zodat Media Services weet hoe u uw inhoud wilt leveren. Voordat uw asset kan worden gestreamd, verwijdert de streamingserver de opslagversleuteling en streamt u uw inhoud met behulp van het opgegeven leveringsbeleid (bijvoorbeeld AES, algemene versleuteling of geen versleuteling).

## <a name="create-contentkeys-used-for-encryption"></a>ContentKeys maken die worden gebruikt voor versleuteling
Versleutelde elementen zijn gekoppeld aan opslagversleutelingssleutels. Maak de inhoudssleutel die moet worden gebruikt voor versleuteling voordat u de assetbestanden maakt. In deze sectie wordt beschreven hoe u een inhoudssleutel maakt.

Hieronder volgen algemene stappen voor het genereren van inhoudssleutels die u koppelt aan elementen die u wilt versleutelen. 

1. Voor opslagversleuteling genereert u willekeurig een AES-toets van 32 byte. 
   
    De AES-sleutel van 32 byte is de inhoudssleutel voor uw asset, wat betekent dat alle bestanden die aan dat item zijn gekoppeld, dezelfde inhoudssleutel moeten gebruiken tijdens decryptie. 
2. Bel de [getprotectionkeyid-](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) en [getprotectionkey-methoden](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) om het juiste X.509-certificaat te krijgen dat moet worden gebruikt om uw inhoudssleutel te versleutelen.
3. Versleutel uw inhoudssleutel met de openbare sleutel van het X.509-certificaat. 
   
   Media Services .NET SDK gebruikt RSA met OAEP bij het maken van de versleuteling.  U een .NET-voorbeeld zien in de [functie EncryptSymmetricKeyData.](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)
4. Maak een checksumwaarde berekend met behulp van de sleutel-id en inhoudssleutel. In het volgende .NET-voorbeeld wordt de checksum berekend met behulp van het GUID-gedeelte van de sleutel-id en de duidelijke inhoudssleutel.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. Maak de **inhoudssleutel** met de versleutelde contentsleutel (geconverteerd naar de tekenreeks met basis64), **ProtectionKeyId,** **ProtectionKeyType,** **ContentKeyType**en **Checksum-waarden** die u in eerdere stappen hebt ontvangen.

    Voor opslagversleuteling moeten de volgende eigenschappen in de aanvraaginstantie worden opgenomen.

    Lichaam aanvragen    | Beschrijving
    ---|---
    Id | De ContentKey ID wordt gegenereerd met de volgende indeling"nb:kid:UUID:\<NEW GUID>".
    ContentKeyType | Het type inhoudssleutel is een geheel getal dat de sleutel definieert. Voor opslagversleutelingsindeling is de waarde 1.
    Versleutelde ContentKey | We maken een nieuwe waarde voor inhoudssleutel met een waarde van 256 bits (32 bytes). De sleutel wordt versleuteld met het X.509-certificaat voor opslagversleuteling dat we ophalen uit Microsoft Azure Media Services door een HTTP GET-aanvraag uit te voeren voor de methoden GetProtectionKeyId en GetProtectionKey. Zie bijvoorbeeld de volgende .NET-code: de [hier](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)gedefinieerde **methode EncryptSymmetricKeyData.**
    ProtectionKeyId | Dit is de beveiligingssleutel-ID voor het X.509-certificaat voor opslagversleuteling dat werd gebruikt om onze inhoudssleutel te versleutelen.
    ProtectionKeyType | Dit is het versleutelingstype voor de beveiligingssleutel die is gebruikt om de inhoudssleutel te versleutelen. Deze waarde is StorageEncryption(1) voor ons voorbeeld.
    Controlesom |De MD5 berekende checksum voor de inhoudssleutel. Het wordt berekend door de inhouds-ID te versleutelen met de inhoudssleutel. De voorbeeldcode laat zien hoe u de checksum berekent.


### <a name="retrieve-the-protectionkeyid"></a>De ProtectionKeyId ophalen
In het volgende voorbeeld ziet u hoe u de ProtectionKeyId, een duimafdruk van het certificaat, ophaalt voor het certificaat dat u moet gebruiken bij het versleutelen van uw inhoudssleutel. Doe deze stap om ervoor te zorgen dat u al het juiste certificaat op uw machine hebt.

Aanvraag:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

Reactie:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Haal de ProtectionKey voor de ProtectionKeyId op
In het volgende voorbeeld ziet u hoe u het X.509-certificaat ophaalt met behulp van de ProtectionKeyId die u in de vorige stap hebt ontvangen.

Aanvraag:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Reactie:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>De inhoudstoets maken
Nadat u het X.509-certificaat hebt opgehaald en de openbare sleutel hebt gebruikt om uw inhoudssleutel te versleutelen, maakt u een **ContentKey-entiteit** en stelt u de eigenschapswaarden dienovereenkomstig in.

Een van de waarden die u moet instellen bij het maken van de inhoudssleutel is het type. Bij het gebruik van opslagversleuteling moet de waarde worden ingesteld op '1'. 

In het volgende voorbeeld ziet u hoe u een **ContentKey** maakt met een **ContentKeyType-set** voor opslagversleuteling ("1") en **protectionkeytype** ingesteld op '0' om aan te geven dat de beveiligingssleutel-id de duimafdruk van het X.509-certificaat is.  

Aanvraag

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Reactie:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Een asset maken
In het volgende voorbeeld ziet u hoe u een actief maakt.

**HTTP-aanvraag**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**HTTP-antwoord**

Als dit is gelukt, wordt het volgende antwoord geretourneerd:

    HTP/1.1 201 Created
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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>De ContentKey koppelen aan een asset
Nadat u de ContentKey hebt gemaakt, koppelt u deze aan uw asset met behulp van de $links bewerking, zoals in het volgende voorbeeld wordt weergegeven:

Aanvraag:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Reactie:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Een assetbestand maken
De entiteit [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) vertegenwoordigt een video- of audiobestand dat is opgeslagen in een blobcontainer. Een assetbestand is altijd gekoppeld aan een actief en een actief kan een of meer activabestanden bevatten. De encoder-taak Media Services mislukt als een assetbestandobject niet is gekoppeld aan een digitaal bestand in een blobcontainer.

De **instantie AssetFile** en het werkelijke mediabestand zijn twee verschillende objecten. De instantie AssetFile bevat metagegevens over het mediabestand, terwijl het mediabestand de werkelijke media-inhoud bevat.

Nadat u uw digitale mediabestand hebt geüpload naar een blobcontainer, gebruikt u het **HTTP-verzoek SAMENVOEGEN** om het AssetFile bij te werken met informatie over uw mediabestand (niet in dit artikel). 

**HTTP-aanvraag**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
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
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
