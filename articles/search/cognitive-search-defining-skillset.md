---
title: Een set vaardigheden maken
titleSuffix: Azure Cognitive Search
description: Definieer stappen voor gegevensextractie, verwerking van natuurlijke taal of beeldanalyse om gestructureerde informatie uit uw gegevens te verrijken en te extraheren voor gebruik in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754561"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Een skillset maken in een AI-verrijkingspijplijn in Azure Cognitive Search 

AI-verrijking haalt en verrijkt gegevens om deze doorzoekbaar te maken in Azure Cognitive Search. We noemen extractie en verrijking stappen *cognitieve vaardigheden*, gecombineerd in een *skillset* verwezen tijdens het indexeren. Een skillset kan gebruik maken van ingebouwde vaardigheden of aangepaste vaardigheden (zie [Voorbeeld: Een aangepaste vaardigheid maken in een AI-verrijkingspijplijn](cognitive-search-create-custom-skill-example.md) voor meer informatie). [built-in skills](cognitive-search-predefined-skills.md)

In dit artikel leert u hoe u een verrijkingspijplijn maakt voor de vaardigheden die u wilt gebruiken. Er is een skillset gekoppeld aan een Azure Cognitive [Search-indexeerder](search-indexer-overview.md). Een deel van het ontwerp van de pijplijn, die in dit artikel, is de bouw van de skillset zelf. 

> [!NOTE]
> Een ander deel van het ontwerp van de pijplijn is het specificeren van een indexer, die in de [volgende stap](#next-step). Een indexerdefinitie bevat een verwijzing naar de skillset, plus veldtoewijzingen die worden gebruikt voor het aansluiten van ingangen op uitvoer in de doelindex.

Belangrijkste punten om te onthouden:

+ U slechts één skillset per indexer hebben.
+ Een skillset moet ten minste één vaardigheid hebben.
+ U meerdere vaardigheden van hetzelfde type maken (bijvoorbeeld varianten van een vaardigheid voor beeldanalyse).

## <a name="begin-with-the-end-in-mind"></a>Begin met het einde in gedachten

Een aanbevolen eerste stap is beslissen welke gegevens te extraheren uit uw ruwe gegevens en hoe u die gegevens wilt gebruiken in een zoekoplossing. Door een illustratie van de gehele verrijkingspijplijn te maken, u de benodigde stappen identificeren.

Stel dat u geïnteresseerd bent in het verwerken van een reeks financiële analistenopmerkingen. Voor elk bestand wilt u bedrijfsnamen en het algemene sentiment van de opmerkingen extraheren. U ook een aangepaste verrijking schrijven die de Bing Entity Search-service gebruikt om aanvullende informatie over het bedrijf te vinden, zoals in welk soort bedrijf het bedrijf actief is. In wezen wilt u informatie extraheren, zoals de volgende, geïndexeerd voor elk document:

| recordtekst | Bedrijven | Sentiment | bedrijfsbeschrijvingen |
|--------|-----|-----|-----|
|voorbeeldrecord| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation is een Amerikaans multinationaal technologiebedrijf ..." , "LinkedIn is een zakelijke en op werkgelegenheid gerichte sociale netwerken..."]

In het volgende diagram wordt een hypothetische verrijkingspijplijn geïllustreerd:

![Een hypothetische verrijkingspijplijn](media/cognitive-search-defining-skillset/sample-skillset.png "Een hypothetische verrijkingspijplijn")


Zodra u een goed idee hebt van wat u in de pijplijn wilt, u de vaardigheden uitdrukken die deze stappen biedt. Functioneel wordt de skillset uitgedrukt wanneer u uw indexerdefinitie uploadt naar Azure Cognitive Search. Zie de [indexerdocumentatie](https://docs.microsoft.com/rest/api/searchservice/create-indexer)voor meer informatie over het uploaden van uw indexer.


In het diagram gebeurt de *stap voor het kraken van documenten* automatisch. In wezen weet Azure Cognitive Search bekende bestanden te openen en maakt een *inhoudsveld* met de tekst uit elk document. De witte vakken zijn ingebouwde verrijkenden en het vak 'Bing Entity Search' vertegenwoordigt een aangepaste verrijking die u maakt. Zoals geïllustreerd, de skillset bevat drie vaardigheden.

## <a name="skillset-definition-in-rest"></a>Skillset-definitie in REST

Een skillset wordt gedefinieerd als een scala aan vaardigheden. Elke vaardigheid definieert de bron van de ingangen en de naam van de geproduceerde uitgangen. Met de [API Skillset REST maken](https://docs.microsoft.com/rest/api/searchservice/create-skillset)u een skillset definiëren die overeenkomt met het vorige diagram: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Een set vaardigheden maken

Tijdens het maken van een skillset u een beschrijving geven om de zelfdocumenterende skillset te maken. Een beschrijving is optioneel, maar handig om bij te houden wat een skillset doet. Omdat skillset een JSON-document is, dat geen `description` opmerkingen toestaat, moet u hiervoor een element gebruiken.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Het volgende stuk in de skillset is een scala aan vaardigheden. Je elke vaardigheid zien als een primitief van verrijking. Elke vaardigheid voert een kleine taak uit in deze verrijkingspijplijn. Elk neemt een ingang (of een set van ingangen), en retourneert een aantal uitgangen. De volgende secties richten zich op het specificeren van ingebouwde en aangepaste vaardigheden, het aan elkaar ketenen van vaardigheden door middel van input- en uitvoerreferenties. Ingangen kunnen afkomstig zijn van brongegevens of van een andere vaardigheid. Uitgangen kunnen worden toegewezen aan een veld in een zoekindex of worden gebruikt als input voor een downstreamvaardigheid.

## <a name="add-built-in-skills"></a>Ingebouwde vaardigheden toevoegen

Laten we eens kijken naar de eerste vaardigheid, dat is de ingebouwde [entiteit erkenning vaardigheid:](cognitive-search-skill-entity-recognition.md)

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Elke ingebouwde vaardigheid `odata.type` `input`heeft `output` , , en eigenschappen. Vaardigheidsspecifieke eigenschappen bieden aanvullende informatie die van toepassing is op die vaardigheid. Voor entiteitsherkenning `categories` is één entiteit tussen een vaste set entiteitstypen die het vooraf ingestelde model kan herkennen.

* Elke vaardigheid moet ```"context"```een . De context geeft het niveau weer waarop bewerkingen plaatsvinden. In de bovenstaande vaardigheid is de context het hele document, wat betekent dat de entiteitsherkenningsvaardigheid eenmaal per document wordt aangeroepen. Ook op dat niveau worden outputs geproduceerd. Meer in ```"organizations"``` het bijzonder, ```"/document"```worden gegenereerd als lid van . In downstream vaardigheden, u verwijzen ```"/document/organizations"```naar deze nieuw gecreëerde informatie als.  Als ```"context"``` het veld niet expliciet is ingesteld, is de standaardcontext het document.

* De vaardigheid heeft een ingang genaamd "tekst", ```"/document/content"```met een broninvoer ingesteld op . De vaardigheid (entiteitsherkenning) werkt op het *inhoudsveld* van elk document, een standaardveld dat is gemaakt door de Azure blob-indexer. 

* De vaardigheid heeft ```"organizations"```een uitgang genaamd . Uitgangen bestaan alleen tijdens de verwerking. Als u deze uitvoer wilt ketenen aan de ```"/document/organizations"```invoer van een downstreamvaardigheid, verwijst u de uitvoer naar .

* Voor een bepaald document ```"/document/organizations"``` is de waarde van een array van organisaties die uit de tekst zijn gehaald. Bijvoorbeeld:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

In sommige situaties wordt elk element van een array afzonderlijk verwezen. Stel dat u elk element afzonderlijk ```"/document/organizations"``` wilt doorgeven aan een andere vaardigheid (zoals de aangepaste zoeker van de Bing-entiteit). U naar elk element van de array verwijzen door een sterretje toe te voegen aan het pad:```"/document/organizations/*"``` 

De tweede vaardigheid voor sentiment extractie volgt hetzelfde patroon als de eerste verrijken. Het ```"/document/content"``` duurt als input, en retourneert een sentiment score voor elke inhoud instantie. Aangezien u het ```"context"``` veld niet expliciet hebt ingesteld, is de ```"/document"```uitvoer (mySentiment) nu een onderliggend kind van .

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Een aangepaste vaardigheid toevoegen

De structuur van de aangepaste Bing-entiteitszoeker inroepen:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Deze definitie is een [aangepaste vaardigheid](cognitive-search-custom-skill-web-api.md) die een web-API aanroept als onderdeel van het verrijkingsproces. Voor elke organisatie die wordt geïdentificeerd door entiteitsherkenning, roept deze vaardigheid een web-API aan om de beschrijving van die organisatie te vinden. De orkestratie van wanneer de web-API moet worden aanroepen en hoe de ontvangen informatie moet worden doorgegeven, wordt intern verwerkt door de verrijkingsengine. De initialisatie die nodig is voor het aanroepen van deze aangepaste API moet echter worden geleverd in de JSON (zoals uri, httpHeaders en de verwachte ingangen). Zie [Een aangepaste interface definiëren](cognitive-search-custom-skill-interface.md)voor richtlijnen voor het maken van een aangepaste web-API voor de verrijkingspijplijn.

Merk op dat het veld ```"/document/organizations/*"``` "context" is ingesteld op een sterretje, wat ```"/document/organizations"```betekent dat de verrijkingsstap wordt opgeroepen voor *elke* organisatie onder . 

Uitvoer, in dit geval een bedrijfsbeschrijving, wordt gegenereerd voor elke geïdentificeerde organisatie. Wanneer u verwijst naar de beschrijving in een downstreamstap (bijvoorbeeld in ```"/document/organizations/*/description"``` trefwoordextractie), gebruikt u het pad om dit te doen. 

## <a name="add-structure"></a>Structuur toevoegen

De skillset genereert gestructureerde informatie uit ongestructureerde gegevens. Kijk een naar het volgende voorbeeld:

*"In het vierde kwartaal boekte Microsoft een omzet van 1,1 miljard dollar van LinkedIn, het sociale netwerkbedrijf dat het vorig jaar kocht. De overname stelt Microsoft in staat om LinkedIn-mogelijkheden te combineren met zijn CRM- en Office-mogelijkheden. Aandeelhouders zijn enthousiast over de vooruitgang tot nu toe."*

Een waarschijnlijke uitkomst zou een gegenereerde structuur zijn die vergelijkbaar is met de volgende illustratie:

![Voorbeelduitvoerstructuur](media/cognitive-search-defining-skillset/enriched-doc.png "Voorbeelduitvoerstructuur")

Tot nu toe is deze structuur alleen intern, alleen met geheugen en alleen gebruikt in Azure Cognitive Search-indexen. De toevoeging van een kenniswinkel geeft u een manier om gevormde verrijkingen op te slaan voor gebruik buiten de zoekopdracht.

## <a name="add-a-knowledge-store"></a>Een kenniswinkel toevoegen

[Knowledge store](knowledge-store-concept-intro.md) is een preview-functie in Azure Cognitive Search voor het opslaan van uw verrijkte document. Een kennisarchief dat u maakt, ondersteund door een Azure-opslagaccount, is de opslagplaats waar uw verrijkte gegevens worden ondergebracht. 

Een definitie van een kenniswinkel wordt toegevoegd aan een skillset. Zie [Een kenniswinkel maken in REST](knowledge-store-create-rest.md)voor een doorloop van het hele proces.

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

U ervoor kiezen de verrijkte documenten op te slaan als tabellen met hiërarchische relaties behouden of als JSON-documenten in blobopslag. Output van een van de vaardigheden in de skillset kan worden verkregen als de input voor de projectie. Als u de gegevens in een specifieke vorm wilt projecteren, kan de bijgewerkte [shaper-vaardigheid](cognitive-search-skill-shaper.md) nu complexe typen modelleren die u gebruiken. 

<a name="next-step"></a>

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de [verrijkingspijplijn](cognitive-search-output-field-mapping.md)en skillsets, gaat u verder met [Hoe annotaties in een skillset te verwijzen](cognitive-search-concept-annotations-syntax.md) of Uitvoer toewijzen aan velden in een index . 
