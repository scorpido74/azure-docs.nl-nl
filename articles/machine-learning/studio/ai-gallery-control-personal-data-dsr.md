---
title: Azure AI Gallery gegevens beheren
titleSuffix: ML Studio (classic) - Azure
description: U kunt exporteren en verwijderen van uw gebruikergegevens in het product uit Azure AI Gallery met behulp van de interface of de API van AI-galerie Catalog. In dit artikel ziet u hoe u.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204542"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Weergeven en verwijderen van gegevens in het product van gebruiker uit Azure AI Gallery

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

U kunt bekijken en uw gebruikergegevens in het product verwijderen uit Azure AI Gallery met behulp van de interface of de API van AI-galerie Catalog. In dit artikel leest u hoe.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Uw gegevens weergeven in AI-galerie met de gebruikersinterface

U kunt items die u hebt gepubliceerd via de website van Azure AI Gallery gebruikersinterface weergeven. Gebruikers kunnen zowel openbare als niet-vermelde oplossingen, projecten, experimenten en andere gepubliceerde items bekijken:

1.    Meld u aan bij de [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klik op de profielfoto in de rechterbovenhoek en vervolgens de naam van het laden van uw profielpagina.
3.    De profielpagina geeft alle items die zijn gepubliceerd naar de galerie, met inbegrip van niet-vermelde vermeldingen.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>De API van AI-galerie Catalog gebruiken om uw gegevens weer te geven

U kunt de gegevens die zijn verzameld via de AI Gallery Catalog-API weer geven die toegankelijk is op https://catalog.cortanaanalytics.com/entities. Als u wilt weergeven van gegevens, moet u uw auteur-ID. Als u niet-vermelde entiteiten via de API-catalogus, moet u een toegangstoken.

Catalogus-antwoorden worden geretourneerd in JSON-indeling.

### <a name="get-an-author-id"></a>Een auteur-ID ophalen
De auteur-ID is gebaseerd op het e-mailadres gebruikt bij het publiceren naar de Azure AI Gallery. Deze wijzigen niet:

1.    Meld u aan bij [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klik op de profielfoto in de rechterbovenhoek en vervolgens de naam van het laden van uw profielpagina.
3.    De URL in de adres balk bevat de alfanumerieke ID die volgt `authorId=`. Bijvoorbeeld voor de URL: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Auteur-ID: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Uw toegangstoken

U moet een toegangstoken om niet-vermelde entiteiten via de API-catalogus weer te geven. Zonder een toegangstoken, kunnen gebruikers nog steeds bekijken openbare entiteiten en andere gebruikersgegevens.

Als u een toegangs token wilt ophalen, moet u de `DataLabAccessToken`-header van een HTTP-aanvraag controleren die in de browser wordt gemaakt met de API van de catalogus terwijl u bent aangemeld:

1.    Meld u aan bij de [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klik op de profielfoto in de rechterbovenhoek en vervolgens de naam van het laden van uw profielpagina.
3.    Het deelvenster van de hulpprogramma's voor ontwikkelaars browser openen door op F12 te drukken, selecteer het tabblad netwerk en vernieuw de pagina. 
4. Filter aanvragen op de teken reeks *catalogus* door te typen in het tekstvak filter.
5.    In aanvragen voor de URL `https://catalog.cortanaanalytics.com/entities`, zoek een GET-aanvraag en selecteer het tabblad *headers* . Schuif omlaag naar de sectie *aanvraag headers* .
6.    Onder de header `DataLabAccessToken` is het alfanumerieke token. Om te helpen uw gegevens veilig houden, is dit token delen niet.

### <a name="view-user-information"></a>Gebruikersgegevens
Bekijk de informatie in het profiel van een gebruiker met behulp van de auteur-ID die u in de vorige stappen hebt gekregen, door `[AuthorId]` in de volgende URL te vervangen:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Deze URL-aanvraag:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Retourneert een antwoord, zoals:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Openbare entiteiten weergeven

De catalogus-API slaat informatie over gepubliceerde entiteiten op in de Azure AI Gallery die u ook rechtstreeks kunt bekijken op de website van de [AI-galerie](https://gallery.azure.ai/). 

Als u gepubliceerde entiteiten wilt weer geven, gaat u naar de volgende URL en vervangt u `[AuthorId]` door de auteur-ID die is verkregen in [een Auteur-id ophalen](#get-an-author-id) hierboven.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Bijvoorbeeld:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Niet-vermelde en openbare entiteiten weergeven

Deze query geeft alleen openbare entiteiten. Als u wilt weergeven van alle entiteiten, inclusief niet-vermelde, bieden toegang tot de token verkregen van de vorige sectie.

1.    Maak met een hulp programma zoals [postman](https://www.getpostman.com)een HTTP GET-aanvraag naar de catalogus-URL, zoals wordt beschreven in [uw toegangs Token ophalen](#get-your-access-token).
2.    Maak een HTTP-aanvraag header met de naam `DataLabAccessToken`, waarbij de waarde is ingesteld op het toegangs token.
3.    De HTTP-aanvraag indienen.

> [!TIP]
> Als niet-vermelde entiteiten niet in de reacties van de API-catalogus weergegeven worden, wordt de gebruiker mogelijk een ongeldige of verlopen toegangstoken. Meld u af bij de Azure AI Gallery en herhaal de stappen in [uw toegangs Token ophalen](#get-your-access-token) om het token te vernieuwen. 
