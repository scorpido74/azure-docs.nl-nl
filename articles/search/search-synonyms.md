---
title: Synoniemen voor query-uitbreiding boven een zoekindex
titleSuffix: Azure Cognitive Search
description: Maak een synoniemenkaart om het bereik van een zoekopdracht uit te breiden op een Azure Cognitive Search-index. Het bereik wordt uitgebreid met gelijkwaardige termen die u in een lijst opgeeft.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: aa573e84fa9fff83bd6a894f516ce5f67b3afa79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194339"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synoniemen in Azure Cognitive Search

Synoniemen in zoekmachines associëren gelijkwaardige termen die impliciet het bereik van een query uitbreiden, zonder dat de gebruiker de term daadwerkelijk hoeft op te geven. Bijvoorbeeld, gezien de term "hond" en synoniem verenigingen van "hond" en "puppy", alle documenten met "hond", "hond" of "puppy" zal vallen binnen het bereik van de query.

In Azure Cognitive Search wordt synoniemenuitbreiding uitgevoerd op querytijd. U synoniemenkaarten toevoegen aan een service zonder onderbreking van bestaande bewerkingen. U een eigenschap **synonymMaps** toevoegen aan een velddefinitie zonder dat u de index hoeft opnieuw op te bouwen.

## <a name="create-synonyms"></a>Synoniemen maken

Er is geen portalondersteuning voor het maken van synoniemen, maar u de REST API of .NET SDK gebruiken. Om aan de slag te gaan met REST, raden we aan [om Postman](search-get-started-postman.md) te gebruiken en aanvragen te formuleren met behulp van deze API: [Synoniemkaarten maken.](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map) Voor C#-ontwikkelaars u aan de slag met [Synoniemen toevoegen in Azure Cognitive Searching met C#](search-synonyms-tutorial-sdk.md).

Optioneel u die bescherming toepassen op de inhoud van uw synoniemkaart als u door de [klant beheerde sleutels](search-security-manage-encryption-keys.md) gebruikt voor versleuteling aan de servicezijde.

## <a name="use-synonyms"></a>Synoniemen gebruiken

In Azure Cognitive Search is synoniemondersteuning gebaseerd op synoniemenkaarten die u definieert en uploadt naar uw service. Deze kaarten vormen een onafhankelijke bron (zoals indexen of gegevensbronnen) en kunnen worden gebruikt door elk doorzoekbaar veld in een index in uw zoekservice.

Synoniemkaarten en -indexen worden onafhankelijk onderhouden. Zodra u een synoniemenkaart definieert en uploadt naar uw service, u de synoniemfunctie op een veld inschakelen door een nieuwe eigenschap toe te voegen die **synoniemen worden genoemd: synoniemEnin** de velddefinitie. Het maken, bijwerken en verwijderen van een synoniemenkaart is altijd een bewerking met hele documenten, wat betekent dat u delen van de synoniemenkaart niet stapsgewijs maken, bijwerken of verwijderen. Het bijwerken van zelfs een enkele vermelding vereist een herladen.

Het opnemen van synoniemen in uw zoektoepassing is een proces in twee stappen:

1.  Voeg een synoniemkaart toe aan uw zoekservice via de onderstaande API's.  

2.  Configureer een doorzoekbaar veld om de synoniemenkaart in de indexdefinitie te gebruiken.

U meerdere synoniemenkaarten maken voor uw zoektoepassing (bijvoorbeeld op taal als uw toepassing een meertalig klantenbestand ondersteunt). Momenteel kan een veld slechts één van deze velden gebruiken. U de eigenschap synonymMaps van een veld op elk gewenst moment bijwerken.

### <a name="synonymmaps-resource-apis"></a>SynoniemMaps-bron-API's

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Voeg een synoniemenkaart onder uw service toe of bij, met behulp van POST of PUT.

Synoniemkaarten worden via POST of PUT naar de service geüpload. Elke regel moet worden afgebakend door het nieuwe regelteken ('\n'). U maximaal 5.000 regels per synoniemkaart definiëren in een gratis service en 20.000 regels per kaart in alle andere SKU's. Elke regel kan maximaal 20 uitbreidingen hebben.

Synoniemkaarten moeten zich in het Apache Solr-formaat begeven dat hieronder wordt uitgelegd. Als u een bestaand synoniem woordenboek in een ander formaat en wilt u het direct te gebruiken, laat het ons weten op [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

U een nieuwe synoniemenkaart maken met HTTP POST, zoals in het volgende voorbeeld:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

U PUT ook gebruiken en de naam van de synoniemkaart opgeven op de URI. Als de synoniemenkaart niet bestaat, wordt deze gemaakt.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Synoniemindeling Apache Solr

De Solr-indeling ondersteunt gelijkwaardige en expliciete synoniemtoewijzingen. Kaartregels voldoen aan de open-source synoniemfilterspecificatie van Apache Solr, beschreven in dit document: [SynoniemFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Hieronder vindt u een voorbeeldregel voor gelijkwaardige synoniemen.
```
USA, United States, United States of America
```

Met de bovenstaande regel wordt een zoekopdracht "USA" uitgebreid naar "USA" OF "Verenigde Staten" OF "Verenigde Staten van Amerika".

Expliciete toewijzing wordt aangeduid met een pijl "=>". Wanneer opgegeven, wordt een termreeks van een zoekopdracht die overeenkomt met de linkerkant van "=>" vervangen door de alternatieven aan de rechterkant. Gezien de regel hieronder, zoekopdrachten "Washington", "Wash." of "WA" zal allemaal worden herschreven naar "WA". Expliciete toewijzing is alleen van toepassing in de opgegeven richting en herschrijft de query "WA" in dit geval niet naar 'Washington'.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Lijst synoniemkaarten onder uw service.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Ontvang een synoniemkaart onder uw service.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Verwijder een synoniemenkaart onder uw service.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configureer een doorzoekbaar veld om de synoniemenkaart in de indexdefinitie te gebruiken.

Een nieuw **veldeigenschapsynoniemMaps** kan worden gebruikt om een synoniemenkaart op te geven die moet worden gebruikt voor een doorzoekbaar veld. Synoniemkaarten zijn bronnen op serviceniveau en kunnen worden verwezen door elk veld van een index onder de service.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synoniemKaarten** kunnen worden opgegeven voor doorzoekbare velden van het type 'Edm.String' of 'Collection(Edm.String)'.

> [!NOTE]
> U slechts één synoniemkaart per veld hebben. Als u meerdere synoniemenkaarten wilt gebruiken, laat het ons dan weten op [UserVoice.](https://feedback.azure.com/forums/263029-azure-search)

## <a name="impact-of-synonyms-on-other-search-features"></a>Impact van synoniemen op andere zoekfuncties

De synoniemenfunctie herschrijft de oorspronkelijke query met synoniemen met de OPERATOR OR. Om deze reden, hit markeren en scoren profielen behandelen de oorspronkelijke term en synoniemen als gelijkwaardig.

Synoniemfunctie is van toepassing op zoekopdrachten en is niet van toepassing op filters of facetten. Evenzo zijn suggesties alleen gebaseerd op de oorspronkelijke term; synoniemen worden niet weergegeven in het antwoord.

Synoniemenuitbreidingen zijn niet van toepassing op zoektermen met jokertekens; voorvoegsel, fuzzy en regex-termen worden niet uitgebreid.

Als u één query moet uitvoeren die synoniemenuitbreiding en wildcard, regex of vage zoekopdrachten toepast, u de query's combineren met de syntaxis OK. Als u bijvoorbeeld synoniemen wilt combineren met jokertekens voor `<query> | <query>*`eenvoudige querysyntaxis, is de term .

Als u een bestaande index in een ontwikkelomgeving (niet-productie) hebt, experimenteert u met een klein woordenboek om te zien hoe de toevoeging van synoniemen de zoekervaring verandert, inclusief impact op scoreprofielen, hitmarkeringen en suggesties.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een synoniemenkaart maken](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)