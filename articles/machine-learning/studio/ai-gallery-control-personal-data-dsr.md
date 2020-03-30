---
title: Azure AI-galeriegegevens beheren
titleSuffix: ML Studio (classic) - Azure
description: U uw in-product gebruikersgegevens exporteren en verwijderen uit Azure AI Gallery met behulp van de interface of AI Gallery Catalog API. Dit artikel laat zien hoe.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 03341b9e663398f2c42266dead0d2dd01e97c3f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204542"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Gebruikersgegevens in het product weergeven en verwijderen uit Azure AI Gallery

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

U uw in-product gebruikersgegevens uit Azure AI Gallery weergeven en verwijderen met behulp van de interface of AI Gallery Catalog API. Dit artikel vertelt je hoe.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Uw gegevens weergeven in AI-galerie met de gebruikersinterface

U items bekijken die u hebt gepubliceerd via de gebruikersinterface van de Azure AI Gallery-website. Gebruikers kunnen zowel openbare als niet-vermelde oplossingen, projecten, experimenten en andere gepubliceerde items bekijken:

1.    Meld u aan bij de [Azure AI-galerie](https://gallery.azure.ai/).
2.    Klik op de profielfoto in de rechterbovenhoek en vervolgens op de accountnaam om uw profielpagina te laden.
3.    Op de profielpagina worden alle items weergegeven die in de galerie zijn gepubliceerd, inclusief niet-vermelde vermeldingen.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>De API voor AI-galeriecatalogus gebruiken om uw gegevens weer te geven

U programmatisch gegevens bekijken die zijn verzameld via de https://catalog.cortanaanalytics.com/entitiesAPI voor AI-galerijcatalogus, die toegankelijk is op. Als u gegevens wilt weergeven, hebt u uw auteurs-id nodig. Als u niet-vermelde entiteiten wilt weergeven via de catalog-API, hebt u een toegangstoken nodig.

Catalogusreacties worden geretourneerd in JSON-indeling.

### <a name="get-an-author-id"></a>Een auteurs-ID opschrijven
De auteurs-id is gebaseerd op het e-mailadres dat wordt gebruikt bij het publiceren naar de Azure AI-galerie. Het verandert niet:

1.    Meld u aan bij [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klik op de profielfoto in de rechterbovenhoek en vervolgens op de accountnaam om uw profielpagina te laden.
3.    Op de URL in de adresbalk `authorId=`wordt de alfanumerieke id weergegeven. Bijvoorbeeld voor de URL:`https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Auteur-id:`99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Uw toegangstoken ophalen

U hebt een toegangstoken nodig om niet-vermelde entiteiten te bekijken via de catalog-API. Zonder Access Token kunnen gebruikers nog steeds openbare entiteiten en andere gebruikersinformatie bekijken.

Als u een toegangstoken wilt `DataLabAccessToken` krijgen, moet u de kop tekst van een HTTP-verzoek van de browser naar de catalogus-API inspecteren terwijl u bent ingelogd:

1.    Meld u aan bij de [Azure AI-galerie](https://gallery.azure.ai/).
2.    Klik op de profielfoto in de rechterbovenhoek en vervolgens op de accountnaam om uw profielpagina te laden.
3.    Open het deelvenster Hulpmiddelen voor browserontwikkelaars door op F12 te drukken, het tabblad Netwerk te selecteren en de pagina te vernieuwen. 
4. Filter aanvragen in de *tekenreekscatalogus* door in het tekstvak Filter te typen.
5.    Zoek in aanvragen `https://catalog.cortanaanalytics.com/entities`naar de URL een GET-aanvraag en selecteer het tabblad *Kopteksten.* Blader omlaag naar de sectie *Kopteksten aanvragen.*
6.    Onder de `DataLabAccessToken` koptekst is het alfanumerieke token. Om uw gegevens veilig te houden, deelt u dit token niet.

### <a name="view-user-information"></a>Gebruikersinformatie weergeven
Met de auteurs-id die u in de vorige stappen hebt `[AuthorId]` gekregen, u informatie in het profiel van een gebruiker weergeven door te worden vervangen in de volgende URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Deze URL-aanvraag:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Geeft als resultaat een antwoord zoals:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Openbare entiteiten weergeven

De API voor catalogus slaat informatie over gepubliceerde entiteiten op in de Azure AI-galerie die u ook rechtstreeks op de [AI Gallery-website](https://gallery.azure.ai/)bekijken. 

Als u gepubliceerde entiteiten wilt bekijken, `[AuthorId]` gaat u naar de volgende URL, ter vervanging van de auteur-id die is verkregen in [Een auteurs-id](#get-an-author-id) ophalen hierboven.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Bijvoorbeeld:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Niet-beursgenoteerde en openbare entiteiten weergeven

In deze query worden alleen openbare entiteiten weergegeven. Als u al uw entiteiten wilt bekijken, inclusief niet-vermelde entiteiten, geeft u het toegangstoken op dat is verkregen uit de vorige sectie.

1.    Met een tool als [Postman](https://www.getpostman.com)maakt u een HTTP GET-verzoek naar de URL van de catalogus zoals beschreven in [Get your access token.](#get-your-access-token)
2.    Maak een HTTP-aanvraagkopkop met `DataLabAccessToken`de waarde die is ingesteld op het toegangstoken.
3.    Dien de HTTP-aanvraag in.

> [!TIP]
> Als niet-vermelde entiteiten niet worden weergegeven in reacties uit de catalogus-API, heeft de gebruiker mogelijk een ongeldig of verlopen toegangstoken. Meld u af bij de Azure AI-galerie en herhaal vervolgens de stappen in [Uw toegangstoken downloaden](#get-your-access-token) om het token te vernieuwen. 
