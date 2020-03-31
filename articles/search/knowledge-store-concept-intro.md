---
title: Inleiding tot kenniswinkel (preview)
titleSuffix: Azure Cognitive Search
description: Verzend verrijkte documenten naar Azure Storage waar u verrijkte documenten bekijken, hervormen en gebruiken in Azure Cognitive Search en in andere toepassingen. Deze functie is beschikbaar voor openbare preview.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942986"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Inleiding tot kenniswinkels in Azure Cognitive Search

> [!IMPORTANT] 
> Knowledge store is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt preview functies. Er is momenteel beperkte portalondersteuning en geen .NET SDK-ondersteuning.

Knowledge store is een functie van Azure Cognitive Search die blijft uitvoeren van een [AI-verrijkingpijplijn](cognitive-search-concept-intro.md) voor onafhankelijke analyse of downstream verwerking. Een *verrijkt document* is de uitvoer van een pijplijn, gemaakt op basis van inhoud die is geëxtraheerd, gestructureerd en geanalyseerd met behulp van AI-processen. In een standaard AI-pijplijn zijn verrijkte documenten van voorbijgaande aard, alleen gebruikt tijdens het indexeren en vervolgens weggegooid. Met kenniswinkel worden verrijkte documenten bewaard. 

Als u in het verleden cognitieve vaardigheden hebt gebruikt, weet u al dat *skillsets* een document door een reeks verrijkingen verplaatsen. Het resultaat kan een zoekindex zijn, of (nieuw in deze preview) projecties in een kenniswinkel. De twee uitgangen, zoekindex en kenniswinkel, zijn producten van dezelfde pijplijn; afgeleid van dezelfde ingangen, maar resulterend in output die is gestructureerd, opgeslagen en gebruikt op zeer verschillende manieren.

Fysiek is een kennisarchief [Azure Storage,](https://docs.microsoft.com/azure/storage/common/storage-account-overview)Azure Table-opslag, Azure Blob-opslag of beide. Elk hulpprogramma of proces dat verbinding kan maken met Azure Storage kan de inhoud van een kennisarchief verbruiken.

![Kennisarchief in pijplijndiagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kennisarchief in pijplijndiagram")

## <a name="benefits-of-knowledge-store"></a>Voordelen van kenniswinkel

Een kennisarchief biedt u structuur, context en werkelijke inhoud - verkregen uit ongestructureerde en semi-gestructureerde gegevensbestanden zoals blobs, afbeeldingsbestanden die analyse hebben ondergaan of zelfs gestructureerde gegevens, zijn hervormd tot nieuwe vormen. In een [stapsgewijze walkthrough](knowledge-store-create-rest.md)u uit de eerste hand zien hoe een dicht JSON-document wordt verdeeld in substructuren, wordt gereconstrueerd in nieuwe structuren en anderszins beschikbaar wordt gesteld voor downstreamprocessen zoals machine learning en data science-workloads.

Hoewel het handig is om te zien wat een AI-verrijkingspijplijn kan produceren, is het echte potentieel van een kenniswinkel de mogelijkheid om gegevens te hervormen. Je zou kunnen beginnen met een basisskillset, en dan herhalen over het toe te voegen toenemende niveaus van structuur, die u vervolgens combineren in nieuwe structuren, verbruiksgoederen in andere apps naast Azure Cognitive Search.

Opgesomd, de voordelen van kennis op te slaan omvatten de volgende:

+ Gebruik verrijkte documenten in [andere analyse- en rapportagetools](#tools-and-apps) dan zoeken. Power BI met Power Query is een aantrekkelijke keuze, maar elk hulpprogramma of app dat verbinding kan maken met Azure Storage kan worden opgehaald uit een kennisarchief dat u maakt.

+ Verfijn een AI-indexeringspijplijn terwijl u stappen en skillset-definities debugt. Een kenniswinkel toont u het product van een skillset-definitie in een AI-indexeringspijplijn. U deze resultaten gebruiken om een betere vaardigheden te ontwerpen, omdat u precies zien hoe de verrijkingen eruit zien. U [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in Azure Storage gebruiken om de inhoud van een kennisarchief weer te geven.

+ Vorm de gegevens in nieuwe vormen. De herinrichting is gecodificeerd in skillsets, maar het punt is dat een skillset nu deze mogelijkheid kan bieden. De [Shaper-vaardigheid](cognitive-search-skill-shaper.md) in Azure Cognitive Search is uitgebreid om deze taak aan te kunnen. Met opnieuw vormgeven u een projectie definiëren die aansluit bij het beoogde gebruik van de gegevens met behoud van relaties.

> [!Note]
> Nieuw in AI verrijking en cognitieve vaardigheden? Azure Cognitive Search integreert met functies Cognitive Services Vision en Language om brongegevens te extraheren en te verrijken met behulp van Optical Character Recognition (OCR) over afbeeldingsbestanden, entiteitsherkenning en sleutelzinextractie uit tekstbestanden, en meer. Zie [AI-verrijking in Azure Cognitive Search](cognitive-search-concept-intro.md)voor meer informatie.

## <a name="physical-storage"></a>Fysieke opslag

De fysieke expressie van een kenniswinkel `projections` wordt `knowledgeStore` verwoord door het element van een definitie in een Skillset. De projectie definieert een structuur van de uitvoer, zodat deze overeenkomt met het beoogde gebruik.

Projecties kunnen worden gearticuleerd als tabellen, objecten of bestanden.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Het type projectie dat u in deze structuur opgeeft, bepaalt het type opslag dat wordt gebruikt door het kennisarchief.

+ Tabelopslag wordt gebruikt `tables`wanneer u definieert . Definieer een tabelprojectie wanneer u tabelrapportagestructuren nodig hebt voor invoer naar analytische hulpprogramma's of exporteren als gegevensframes naar andere gegevensopslag. U `tables` meerdere opgeven om een subset of doorsnede van verrijkte documenten te krijgen. Binnen dezelfde projectiegroep blijven tabelrelaties behouden, zodat u met ze allemaal werken.

+ Blob-opslag wordt gebruikt `objects` `files`wanneer u definieert of . De fysieke weergave `object` van een is een hiërarchische JSON-structuur die een verrijkt document vertegenwoordigt. A `file` is een afbeelding die uit een document is gehaald en intact is overgebracht naar blobopslag.

Eén projectieobject bevat één `tables` `objects`set `files`, , en voor veel scenario's kan het maken van één projectie voldoende zijn. 

Het is echter mogelijk om `table` - `object` - `file` meerdere sets projecties te maken en dat u doen als u verschillende gegevensrelaties wilt. Binnen een set zijn gegevens gerelateerd, ervan uitgaande dat deze relaties bestaan en kunnen worden gedetecteerd. Als u extra sets maakt, zijn de documenten in elke groep nooit gerelateerd. Een voorbeeld van het gebruik van meerdere projectiegroepen kan zijn als u dezelfde gegevens wilt geprojecteerd voor gebruik met uw online systeem en deze moet op een specifieke manier worden weergegeven, u wilt ook dezelfde gegevens die worden geprojecteerd voor gebruik in een pijplijn voor gegevenswetenschap die wordt weergegeven Anders.

## <a name="requirements"></a>Vereisten 

[Azure Storage](https://docs.microsoft.com/azure/storage/) is vereist. Het biedt fysieke opslag. U Blob-opslag, tabelopslag of beide gebruiken. Blob-opslag wordt gebruikt voor intacte verrijkte documenten, meestal wanneer de uitvoer naar downstreamprocessen gaat. Tabelopslag is voor segmenten van verrijkte documenten, vaak gebruikt voor analyse en rapportage.

[Skillset](cognitive-search-working-with-skillsets.md) is vereist. Het bevat `knowledgeStore` de definitie, en het bepaalt de structuur en de samenstelling van een verrijkt document. U geen kenniswinkel maken met een lege vaardighedenset. Je moet ten minste één vaardigheid in een skillset hebben.

[Indexeris](search-indexer-overview.md) vereist. Een skillset wordt aangeroepen door een indexeerder, die de uitvoering aanstuurt. Indexers komen met hun eigen set van eisen en attributen. Verschillende van deze attributen hebben een directe invloed op een kenniswinkel:

+ Indexers hebben een [ondersteunde Azure-gegevensbron](search-indexer-overview.md#supported-data-sources) nodig (de pijplijn die uiteindelijk het kennisarchief maakt, begint met het ophalen van gegevens uit een ondersteunde bron op Azure). 

+ Indexers vereisen een zoekindex. Een indexer vereist dat u een indexschema opgeeft, zelfs als u nooit van plan bent het te gebruiken. Een minimale index heeft één tekenreeksveld, aangeduid als de sleutel.

+ Indexers bieden optionele veldtoewijzingen die worden gebruikt om een bronveld naar een doelveld te aliasen. Als een standaardveldtoewijzing moet worden gewijzigd (om een andere naam of type te gebruiken), u een [veldtoewijzing](search-indexer-field-mappings.md) maken binnen een indexer. Voor het uitvoeren van kennisopslag kan de bestemming een veld in een blobobject of -tabel zijn.

+ Indexers hebben schema's en andere eigenschappen, zoals wijzigingsdetectiemechanismen die door verschillende gegevensbronnen worden geleverd, kunnen ook worden toegepast op een kennisarchief. U bijvoorbeeld op gezette tijden verrijking [plannen](search-howto-schedule-indexers.md) om de inhoud te vernieuwen. 

## <a name="how-to-create-a-knowledge-store"></a>Hoe maak je een kenniswinkel

Als u kennisarchief wilt maken, gebruikt`api-version=2019-05-06-Preview`u de portal of de preview REST API ( ).

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

De wizard **Gegevens importeren** bevat opties voor het maken van een kennisarchief. Voor de eerste verkenning, [maak je eerste kennis winkel in vier stappen](knowledge-store-connect-power-bi.md).

1. Selecteer een ondersteunde gegevensbron.

1. Verrijking opgeven: voeg een resource toe, selecteer vaardigheden en geef een kennisarchief op. 

1. Maak een indexschema. De wizard vereist het en kan er een voor u afleiden.

1. Voer de wizard uit. Extractie, verrijking en opslag vinden plaats in deze laatste stap.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Skillset maken en de voorbeeld-API REST gebruiken

A `knowledgeStore` wordt gedefinieerd binnen een [skillset](cognitive-search-working-with-skillsets.md), die op zijn beurt wordt aangeroepen door een [indexeerder](search-indexer-overview.md). Tijdens de verrijking creëert Azure Cognitive Search een ruimte in uw Azure Storage-account en projecteert de verrijkte documenten als blobs of in tabellen, afhankelijk van uw configuratie.

Momenteel is de preview REST API het enige mechanisme waarmee u een kennisarchief programmatisch maken. Een eenvoudige manier om te verkennen is [het creëren van uw eerste kennis op te slaan met behulp van Postman en de REST API](knowledge-store-create-rest.md).

Referentie-inhoud voor deze voorbeeldfunctie bevindt zich in de sectie [API-verwijzing](#kstore-rest-api) van dit artikel. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Verbinding maken met tools en apps

Zodra de verrijkingen in opslag bestaan, kan elk hulpprogramma of technologie die verbinding maakt met Azure Blob- of Tabelopslag worden gebruikt om de inhoud te verkennen, te analyseren of te consumeren. De volgende lijst is een begin:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) om verrijkte documentstructuur en -inhoud weer te geven. Beschouw dit als uw basislijntool voor het bekijken van de inhoud van kennisopslag.

+ [Power BI](knowledge-store-connect-power-bi.md) voor rapportage en analyse. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) voor verdere manipulatie.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API-verwijzing

REST API-versie `2019-05-06-Preview` biedt kennisopslag door middel van aanvullende definities op skillsets. Zie Naast de verwijzing [een kenniswinkel maken met Postman](knowledge-store-create-rest.md) voor meer informatie over hoe u de API's bellen.

+ [Skillset maken (api-versie=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Skillset bijwerken (api-versie=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Volgende stappen

Kennisarchief biedt volharding van verrijkte documenten, handig bij het ontwerpen van een skillset of het maken van nieuwe structuren en inhoud voor consumptie door clienttoepassingen die toegang hebben tot een Azure Storage-account.

De eenvoudigste benadering voor het maken van verrijkte documenten is [via de portal,](knowledge-store-create-portal.md)maar u ook Postman en REST API gebruiken, wat handiger is als u inzicht wilt in hoe objecten worden gemaakt en waarnaar wordt verwezen.

> [!div class="nextstepaction"]
> [Maak een kenniswinkel met Postbode en REST](knowledge-store-create-rest.md)

Voor meer informatie over projecties, de mogelijkheden en hoe u [ze definieert in een vaardighedenset](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projecties in een kenniswinkel](knowledge-store-projection-overview.md)

Voor een zelfstudie over geavanceerde projecties concepten zoals snijden, inline vormgeving en relaties, beginnen met [projecties definiëren in een kennis op te slaan](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Projecties definiëren in een kenniswinkel](knowledge-store-projections-examples.md)