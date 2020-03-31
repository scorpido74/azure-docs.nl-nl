---
title: Foutcodes azure Media Services | Microsoft Documenten
description: Mogelijk ontvangt u HTTP-foutcodes van de service, afhankelijk van problemen zoals verificatietokens die verlopen voor acties die niet worden ondersteund in Media Services. In dit artikel vindt u een overzicht van de v2-foutcodes van Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f5a2dd68d86a7a38fc7f2942351c42c84742d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887064"
---
# <a name="azure-media-services-error-codes"></a>Foutcodes azure Media Services
Wanneer u Microsoft Azure Media Services gebruikt, ontvangt u mogelijk HTTP-foutcodes van de service, afhankelijk van problemen zoals verificatietokens die verlopen voor acties die niet worden ondersteund in Media Services. Het volgende is een lijst met **HTTP-foutcodes** die kunnen worden geretourneerd door Media Services en de mogelijke oorzaken voor hen.  

## <a name="400-bad-request"></a>400 Slecht verzoek
Het verzoek bevat ongeldige informatie en wordt afgewezen om een van de volgende redenen:

* Er is een niet-ondersteunde API-versie opgegeven. Zie [Setup for Media Services REST API Development voor](media-services-rest-how-to-use.md)de meest recente versie .
* De API-versie van Media Services is niet opgegeven. Zie [Media Services Operations Rest API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)voor informatie over het opgeven van de API-versie.
  
  > [!NOTE]
  > Als u de .NET- of Java-SDK's gebruikt om verbinding te maken met Media Services, wordt de API-versie voor u opgegeven wanneer u probeert actie te ondernemen tegen Media Services.
  > 
  > 
* Er is een niet-gedefinieerde eigenschap opgegeven. De naam van de eigenschap staat in het foutbericht. Alleen de eigenschappen die lid zijn van een bepaalde entiteit kunnen worden opgegeven. Zie [API-referentie voor Azure Media Services REST](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) voor een lijst met entiteiten en hun eigenschappen.
* Er is een ongeldige eigenschapswaarde opgegeven. De naam van de eigenschap staat in het foutbericht. Zie de vorige koppeling voor geldige eigenschapstypen en hun waarden.
* Een eigenschapswaarde ontbreekt en is vereist.
* Een deel van de opgegeven URL bevat een slechte waarde.
* Er is geprobeerd een WriteOnce-eigenschap bij te werken.
* Er is een poging gedaan om een taak te maken met een invoeractief met een primaire AssetFile die niet is opgegeven of niet kan worden bepaald.
* Er is een poging gedaan om een SAS Locator bij te werken. SAS-locators kunnen alleen worden gemaakt of verwijderd. Streaming locators kunnen worden bijgewerkt. Zie [Locators voor](https://docs.microsoft.com/rest/api/media/operations/locator)meer informatie.
* Er is een niet-ondersteunde bewerking of query ingediend.

## <a name="401-unauthorized"></a>401 Ongeautoriseerd
Het verzoek kan niet worden geverifieerd (voordat het kan worden geautoriseerd) vanwege een van de volgende redenen:

* Ontbrekende verificatiekop.
* Slechte waarde van de verificatiekop.
  * Het token is verlopen. 
  * Het token bevat een ongeldige handtekening.

## <a name="403-forbidden"></a>403 Verboden
Het verzoek is niet toegestaan om een van de volgende redenen:

* Het Media Services-account kan niet worden gevonden of is verwijderd.
* Het Media Services-account is uitgeschakeld en het aanvraagtype is http get niet. Servicebewerkingen geven ook een 403-respons.
* Het verificatietoken bevat geen referentiegegevens van de gebruiker: AccountName en/of SubscriptionId. U deze informatie vinden in de Gebruikersinterface-extensie Media Services voor uw Media Services-account in de Azure Management Portal.
* De bron is niet toegankelijk.
  
  * Er is een poging gedaan om een MediaProcessor te gebruiken die niet beschikbaar is voor uw Media Services-account.
  * Er is een poging gedaan om een JobTemplate bij te werken die is gedefinieerd door Media Services.
  * Er is een poging gedaan om de Locator van een ander Media Services-account te overschrijven.
  * Er is geprobeerd de ContentKey van een ander Media Services-account te overschrijven.
* De resource kan niet worden gemaakt vanwege een servicequotum dat is bereikt voor het Media Services-account. Zie [Quota en beperkingen](media-services-quotas-and-limitations.md)voor meer informatie over de servicequota.

## <a name="404-not-found"></a>404 Niet gevonden
De aanvraag is niet toegestaan op een resource vanwege een van de volgende redenen:

* Er is geprobeerd een entiteit bij te werken die niet bestaat.
* Er is geprobeerd een entiteit te verwijderen die niet bestaat.
* Er is een poging gedaan om een entiteit te maken die een koppeling maakt naar een entiteit die niet bestaat.
* Er is een poging gedaan om een entiteit te krijgen die niet bestaat.
* Er is een poging gedaan om een opslagaccount op te geven dat niet is gekoppeld aan het Media Services-account.  

## <a name="409-conflict"></a>409 Conflict
Het verzoek is niet toegestaan om een van de volgende redenen:

* Meer dan één AssetFile heeft de opgegeven naam binnen het actief.
* Er is een poging gedaan om een tweede primaire AssetFile binnen het actief te maken.
* Er is geprobeerd een ContentKey te maken met de opgegeven id die al is gebruikt.
* Er is een poging gedaan om een locator te maken met de opgegeven id die al is gebruikt.
* Meer dan één IngestManifestFile heeft de opgegeven naam in het IngestManifest.
* Er is een poging gedaan om een tweede opslagversleuteling ContentKey te koppelen aan het opslagversleutelde asset.
* Er is geprobeerd dezelfde ContentKey aan het actief te koppelen.
* Er is een poging gedaan om een locator te maken voor een actief waarvan de opslagcontainer ontbreekt of niet langer is gekoppeld aan het actief.
* Er is geprobeerd een locator te maken voor een asset die al 5 locators in gebruik heeft. (Azure Storage handhaaft de limiet van vijf beleid voor gedeelde toegang voor één opslagcontainer.)
* Het koppelen van opslagrekening van een actief aan een IngestManifestAsset is niet hetzelfde als het opslagaccount dat wordt gebruikt door het bovenliggende IngestManifest.  

## <a name="500-internal-server-error"></a>500 Interne serverfout
Tijdens de verwerking van de aanvraag stuit Media Services op een fout die voorkomt dat de verwerking doorgaat. Dit kan een van de volgende oorzaken hebben:

* Het maken van een asset of functie mislukt omdat de servicequotumgegevens van het Media Services-account tijdelijk niet beschikbaar zijn.
* Het maken van een asset- of ingestManifest blobopslagcontainer mislukt omdat de opslagaccountgegevens van het account tijdelijk niet beschikbaar zijn.
* Andere onverwachte fout.

## <a name="503-service-unavailable"></a>503 Service niet beschikbaar
De server kan momenteel geen aanvragen ontvangen. Deze fout kan worden veroorzaakt door overmatige verzoeken aan de service. Media Services beperking van het gebruik van resources voor toepassingen die buitensporig verzoek indienen bij de service.

> [!NOTE]
> Controleer de foutbericht en de foutcodetekenreeks om meer gedetailleerde informatie te krijgen over de reden waarom u de 503-fout hebt ontvangen. Deze fout betekent niet altijd beperking.
> 
> 

Mogelijke statusbeschrijvingen zijn:

* "Server is bezet. Eerdere runs van dit soort {0} aanvragen duurden meer dan seconden."
* "Server is bezet. Meer {0} dan aanvragen per seconde kunnen worden beperkt."
* "Server is bezet. Meer {0} dan {1} verzoeken binnen enkele seconden kunnen worden beperkt."

Om deze fout te verwerken, raden we u aan exponentiële back-off retry-logica te gebruiken. Dat betekent dat het gebruik van geleidelijk langere wachttijden tussen nieuwe pogingen voor opeenvolgende foutreacties.  Zie [Tijdelijke foutafhandelingstoepassingsblok](https://msdn.microsoft.com/library/hh680905.aspx)voor meer informatie .

> [!NOTE]
> Als u [Azure Media Services SDK voor .Net gebruikt,](https://github.com/Azure/azure-sdk-for-media-services/tree/master)is de logica voor het opnieuw proberen van de 503-fout geïmplementeerd door de SDK.  
> 
> 

## <a name="see-also"></a>Zie ook
[Foutcodes voor mediaservicesbeheer](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

