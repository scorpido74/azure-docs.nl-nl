---
title: Overzicht van restapi-activiteiten van Media Services | Microsoft Documenten
description: De API 'Media Services Operations REST' wordt gebruikt voor het maken van taken, assets, livekanalen en andere bronnen in een Media Services-account. Dit artikel bevat een overzicht van de Azure Media Services v2 REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 597839f633ed2b925b86c5f859a0fb2d3b64dd59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773664"
---
# <a name="media-services-operations-rest-api-overview"></a>Overzicht van REST API-beheer van Media Services 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

De **Media Services Operations REST** API wordt gebruikt voor het maken van taken, assets, live kanalen en andere bronnen in een Media Services-account. Zie [Media Services Operations Rest API-referentie](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)voor meer informatie .

Media Services biedt een REST API die zowel JSON- als atom+pub XML-indeling accepteert. Media Services REST API vereist specifieke HTTP-headers die elke client moet verzenden wanneer hij verbinding maakt met Media Services, evenals een set optionele kopteksten. In de volgende secties worden de kopteksten en HTTP-werkwoorden beschreven die u gebruiken bij het maken van aanvragen en het ontvangen van reacties van Media Services.

Verificatie naar de Media Services REST-API wordt uitgevoerd via Azure Active Directory-verificatie die wordt beschreven in het artikel [Azure AD-verificatie gebruiken om toegang te krijgen tot de Azure Media Services API met REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing bij het gebruik van REST.

* Bij het opvragen van entiteiten is er een limiet van 1000 entiteiten die tegelijk worden geretourneerd omdat openbare REST v2 queryresultaten beperkt tot 1000 resultaten. U moet **Overslaan** en **nemen** (.NET)/ **top** (REST) gebruiken zoals beschreven in [dit .NET-voorbeeld](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) en [dit VOORBEELD van de REST API.](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) 
* Wanneer u JSON gebruikt en opgeeft het **__metadata** trefwoord in de aanvraag te gebruiken (bijvoorbeeld om naar een gekoppeld object te verwijzen), moet u de **koptekst Accepteren** instellen op [de indeling JSON Verbose](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (zie het volgende voorbeeld). Odata begrijpt de **__metadata** eigenschap in het verzoek niet, tenzij u deze instelt op verbose.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standaard HTTP-aanvraagkoppen die worden ondersteund door Media Services
Voor elk gesprek dat u in Media Services voert, is er een reeks vereiste kopteksten die u in uw aanvraag moet opnemen en ook een reeks optionele kopteksten die u wilt opnemen. De onderstaande tabel bevat de vereiste kopteksten:

| Header | Type | Waarde |
| --- | --- | --- |
| Autorisatie |Drager |Drager is het enige geaccepteerde autorisatiemechanisme. De waarde moet ook het toegangstoken bevatten dat wordt geleverd door Azure Active Directory. |
| x-ms-versie |Decimal |2.17 (of meest recente versie)|
| DataServiceVersie |Decimal |3.0 |
| MaxDataServiceVersie |Decimal |3.0 |

> [!NOTE]
> Omdat Media Services OData gebruikt om de REST API's bloot te leggen, moeten de DataServiceVersion- en MaxDataServiceVersion-headers in alle aanvragen worden opgenomen; als dit echter niet het geval is, gaat Media Services er momenteel van uit dat de in gebruik zijnde Waarde Van DataServiceVersion 3.0 is.
> 
> 

Het volgende is een set optionele kopteksten:

| Header | Type | Waarde |
| --- | --- | --- |
| Date |RFC 1123 datum |Tijdstempel van het verzoek |
| Accepteren |Inhoudstype |Het gevraagde inhoudstype voor het antwoord, zoals:<p> -applicatie/json;odata=verbose<p> - toepassing/atoom+xml<p> Reacties kunnen een ander inhoudstype hebben, zoals een blob ophalen, waarbij een succesvol antwoord de blobstream als payload bevat. |
| Accepteren-codering |Gzip, leeglopen |GZIP en DEFLATE codering, indien van toepassing. Opmerking: Voor grote bronnen kan Media Services deze koptekst negeren en niet-gecomprimeerde gegevens retourneren. |
| Accept-Language |"nl", "es", enzovoort. |Hiermee geeft u de gewenste taal voor het antwoord op. |
| Accepteren-Charset |Charset type als "UTF-8" |Standaard is UTF-8. |
| X-HTTP-methode |HTTP-methode |Hiermee kunnen clients of firewalls die HTTP-methoden zoals PUT of DELETE ondersteunen, deze methoden gebruiken, getunneld via een GET-oproep. |
| Content-Type |Inhoudstype |Inhoudstype van de aanvraaginstantie in PUT- of POST-verzoeken. |
| client-request-id |Tekenreeks |Een door de beller gedefinieerde waarde die de opgegeven aanvraag identificeert. Indien opgegeven, wordt deze waarde opgenomen in het antwoordbericht als een manier om de aanvraag in kaart te brengen. <p><p>**Belangrijk**<p>Waarden moeten worden afgetopt op 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standaard HTTP-antwoordkoppen die worden ondersteund door Media Services
Het volgende is een set kopteksten die aan u kunnen worden geretourneerd, afhankelijk van de resource die u hebt aangevraagd en de actie die u wilt uitvoeren.

| Header | Type | Waarde |
| --- | --- | --- |
| request-id |Tekenreeks |Een unieke id voor de huidige bewerking, gegenereerde service. |
| client-request-id |Tekenreeks |Een id die door de beller in het oorspronkelijke verzoek is opgegeven, indien aanwezig. |
| Date |RFC 1123 datum |De datum/tijd waarop de aanvraag is verwerkt. |
| Content-Type |Varieert |Het inhoudstype van de reactiebody. |
| Content-Encoding |Varieert |Gzip of leeglopen, indien van toepassing. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standaard HTTP-werkwoorden ondersteund door Media Services
Het volgende is een volledige lijst met HTTP-werkwoorden die kunnen worden gebruikt bij het indienen van HTTP-verzoeken:

| Verb | Beschrijving |
| --- | --- |
| GET |Geeft als resultaat de huidige waarde van een object. |
| POST |Hiermee maakt u een object op basis van de verstrekte gegevens of verzendt u een opdracht. |
| PUT |Hiermee wordt een object vervangen of wordt een benoemd object gemaakt (indien van toepassing). |
| DELETE |Hiermee verwijdert u een object. |
| Samenvoegen |Hiermee wordt een bestaand object met benoemde eigenschapswijzigingen bijgewerkt. |
| HEAD |Retourneert metagegevens van een object voor een GET-antwoord. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Het entiteitsmodel Media Services ontdekken en doorbladeren
Om mediaservices-entiteiten beter vindbaar te maken, kan de $metadata-bewerking worden gebruikt. Hiermee u alle geldige entiteitstypen, entiteitseigenschappen, associaties, functies, acties, enzovoort ophalen. Door de $metadata bewerking toe te voegen aan het einde van uw API-eindpunt voor Media Services, hebt u toegang tot deze detectieservice.

 /api/$metadata.

U moet "?api-version=2.x" toevoegen aan het einde van de URI als u de metagegevens in een browser wilt bekijken of de x-ms-versieheader niet in uw aanvraag wilt opnemen.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Verifiëren met Media Services REST met Azure Active Directory

Verificatie op de REST-API gebeurt via Azure Active Directory(AAD).
Zie Toegang tot de Azure Media Services API met Azure [AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het ophalen van vereiste verificatiegegevens voor uw Media Services-account via de Azure Portal.

Zie het artikel [Azure AD-verificatie gebruiken om toegang te krijgen tot de Azure Media Services API met REST](media-services-rest-connect-with-aad.md)voor meer informatie over het schrijven van code die verbinding maakt met de REST API met Azure AD-verificatie.

## <a name="next-steps"></a>Volgende stappen
Zie [Azure AD-verificatie gebruiken om toegang te krijgen tot de Azure Media Services API met REST.](media-services-rest-connect-with-aad.md)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

