---
title: Een vakkennisset maken in een cognitieve Zoek pijplijn-Azure Search
description: Definieer de gegevens extractie, de verwerking van natuurlijke taal of de analyse van installatie kopieën om gestructureerde gegevens uit uw gegevens te verrijken en te extra heren voor gebruik in Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: c67131154fd50a2b20b4b07b7294fad250dfa9d9
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639260"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Een vakkennisset maken in een verrijkings pijplijn

Met de cognitieve zoek functie worden gegevens geëxtraheerd en verrijkt om deze doorzoekbaar te maken in Azure Search. De stappen voor het ophalen en verrijken van informatie worden geadviseerd, gecombineerd tot een *vaardig* heden waarnaar wordt verwezen tijdens het indexeren. Een vaardig heden kan [ingebouwde vaardig heden](cognitive-search-predefined-skills.md) of aangepaste vaardig heden gebruiken (Zie [voor beeld: Een aangepaste vaardigheid maken voor cognitieve Zoek](cognitive-search-create-custom-skill-example.md) opdrachten voor meer informatie).

In dit artikel leert u hoe u een verrijkings pijplijn kunt maken voor de vaardig heden die u wilt gebruiken. Een vakkennisset is gekoppeld aan een Azure Search [indexer](search-indexer-overview.md). Een deel van het pijplijn ontwerp, dat in dit artikel wordt besproken, is de vaardig heden zelf maken. 

> [!NOTE]
> Een ander deel van het pijplijn ontwerp is het opgeven van een Indexeer functie die in de [volgende stap](#next-step)wordt besproken. Een Indexeer functie definitie bevat een verwijzing naar de vaardig heden, plus veld toewijzingen die worden gebruikt voor het verbinden van invoer in uitvoer in de doel index.

Belang rijke punten die moeten worden onthouden:

+ U kunt slechts één vaardig heden per Indexeer functie hebben.
+ Een vaardig heden moet ten minste één vaardigheid hebben.
+ U kunt meerdere vaardig heden van hetzelfde type maken (bijvoorbeeld varianten van de vaardigheid van een afbeeldings analyse).

## <a name="begin-with-the-end-in-mind"></a>Begin met het einde van het denk

Een aanbevolen eerste stap is te bepalen welke gegevens moeten worden opgehaald uit uw onbewerkte gegevens en hoe u deze gegevens wilt gebruiken in een zoek oplossing. Het maken van een afbeelding van de volledige rijkere pijp lijn kan u helpen de benodigde stappen te identificeren.

Stel dat u een set van financiële adviezen wilt verwerken. Voor elk bestand wilt u bedrijfs namen ophalen en de algemene sentiment van de opmerkingen. U kunt ook een aangepaste verrijker schrijven die gebruikmaakt van de Bing Entity Search-service om aanvullende informatie over het bedrijf te vinden, zoals het soort bedrijf waar het bedrijf mee wordt betrokken. In wezen wilt u informatie ophalen zoals de volgende, geïndexeerd voor elk document:

| record-text | bedrijven | sentiment | bedrijfs beschrijvingen |
|--------|-----|-----|-----|
|voor beeld-record| ["Microsoft", "LinkedIn"] | 0,99 | ["Micro soft Corporation is een American Multi National Technology Company...", "LinkedIn is een zakelijk en werkend, arbeids gericht sociaal netwerk..."]

Het volgende diagram illustreert een hypothetische verrijkings pijplijn:

![Een hypothetische verrijkings pijplijn](media/cognitive-search-defining-skillset/sample-skillset.png "Een hypothetische verrijkings pijplijn")


Zodra u een getrouw beeld hebt van wat u in de pijp lijn wilt, kunt u de vaardig heden die deze stappen bieden, uitdrukken. In de meeste gevallen wordt de vaardig heden weer gegeven wanneer u de definitie van de Indexeer functie uploadt naar Azure Search. Voor meer informatie over het uploaden van uw Indexeer functie raadpleegt u de [documentatie bij de Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


In het diagram wordt de stap voor het kraken van het *document* automatisch uitgevoerd. Azure Search weet eigenlijk hoe u bekende bestanden opent en een *inhouds* veld maakt met daarin de tekst die uit elk document is geëxtraheerd. De witte vakken zijn ingebouwde verrijkers en het gestippelde Bing Entity Search vak vertegenwoordigt een aangepaste verrijker die u maakt. Zoals u ziet, bevat de vakkennisset drie vaardig heden.

## <a name="skillset-definition-in-rest"></a>De definitie van de vaardig heden in REST

Een vakkennisset wordt gedefinieerd als een matrix met vaardig heden. Elke vaardigheid definieert de bron van de invoer en de naam van de geproduceerde uitvoer. Met de [rest API vaardig heden maken](https://docs.microsoft.com/rest/api/searchservice/create-skillset)kunt u een vaardighedenset definiëren die overeenkomt met het vorige diagram: 

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

Tijdens het maken van een vaardig heden kunt u een beschrijving opgeven om de vaardig heden zelf documenteren te maken. Een beschrijving is optioneel, maar is handig voor het bijhouden van wat een vakkennisset doet. Omdat vaardig heden een JSON-document is dat geen opmerkingen toestaat, moet u hiervoor een `description` element gebruiken.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Het volgende gedeelte van de vaardig heden is een reeks vaardig heden. U kunt elke vaardigheid beschouwen als een primitieve verrijking. Elke vaardigheid voert een kleine taak uit in deze verrijkings pijplijn. Elk hiervan neemt een invoer (of een set van invoer) en retourneert een aantal uitvoer. In de volgende secties wordt uitgelegd hoe u ingebouwde en aangepaste vaardig heden opgeeft, vaardig heden samenvoegt met behulp van invoer-en uitvoer verwijzingen. Invoer kan afkomstig zijn van bron gegevens of van een andere vaardigheid. Uitvoer kan worden toegewezen aan een veld in een zoek index of worden gebruikt als invoer voor een stroomafwaartse vaardigheid.

## <a name="add-built-in-skills"></a>Ingebouwde vaardig heden toevoegen

Laten we eens kijken naar de eerste vaardigheid, de ingebouwde [vaardigheid voor entiteits herkenning](cognitive-search-skill-entity-recognition.md):

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

* Elke ingebouwde vaardigheid heeft `odata.type`de eigenschappen, `input`en. `output` Specifieke eigenschappen bieden aanvullende informatie die van toepassing is op die kwalificatie. Voor entiteit herkenning `categories` is één entiteit onder een vaste set entiteits typen die het voortrainde model kan herkennen.

* Elke vaardigheid moet een ```"context"```hebben. De context vertegenwoordigt het niveau waarop bewerkingen worden uitgevoerd. In de bovenstaande vaardigheid is de context het hele document, wat inhoudt dat de kwalificatie voor entiteits herkenning één keer per document wordt genoemd. Er worden ook outputs geproduceerd op dat niveau. Meer specifiek ```"organizations"``` worden gegenereerd als lid van ```"/document"```. In downstream-vaardig heden kunt u deze zojuist gemaakte gegevens als ```"/document/organizations"```volgt raadplegen.  Als het ```"context"``` veld niet expliciet is ingesteld, is de standaard context het document.

* De vaardigheid heeft één invoer met de naam ' text ', waarbij een bron invoer ```"/document/content"```is ingesteld op. De vaardigheid (entiteits herkenning) wordt uitgevoerd op het *inhouds* veld van elk document, een standaard veld dat wordt gemaakt door de indexer van Azure Blob. 

* De vaardigheid heeft één uitvoer met ```"organizations"```de naam. Outputs bestaan alleen tijdens de verwerking. Als u deze uitvoer wilt koppelen aan de invoer van een downstream-vaardigheid, ```"/document/organizations"```verwijst u naar de uitvoer als.

* Voor een bepaald document is de waarde van ```"/document/organizations"``` een matrix van organisaties die zijn geëxtraheerd uit de tekst. Bijvoorbeeld:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

In sommige situaties wordt een aanroep voor elk element van een matrix afzonderlijk bepaald. Stel bijvoorbeeld dat u elk element van ```"/document/organizations"``` afzonderlijk wilt door geven aan een andere vaardigheid (zoals de aangepaste Bing-entiteit Zoek verrijker). U kunt naar elk element van de matrix verwijzen door een asterisk toe te voegen aan het pad:```"/document/organizations/*"``` 

De tweede vaardigheid voor sentiment extractie volgt hetzelfde patroon als de eerste verrijker. Het duurt ```"/document/content"``` als invoer en retourneert een sentiment-score voor elk inhouds exemplaar. Omdat u het ```"context"``` veld niet expliciet hebt ingesteld, is de uitvoer (mySentiment) nu een onderliggend element van ```"/document"```.

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

De structuur van de aangepaste Bing entity Search-verrijker intrekken:

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

Deze definitie is een [aangepaste vaardigheid](cognitive-search-custom-skill-web-api.md) die een web-API aanroept als onderdeel van het verrijkings proces. Voor elke organisatie die wordt geïdentificeerd door entiteits herkenning, roept deze vaardigheid een web-API aan om de beschrijving van die organisatie te vinden. De indeling van wanneer de Web-API moet worden aangeroepen en hoe de ontvangen informatie wordt intern verwerkt door de verrijkings engine. De initialisatie die nodig is voor het aanroepen van deze aangepaste API moet echter worden gegeven in de JSON (zoals URI, httpHeaders en de invoer wordt verwacht). Zie [een aangepaste interface definiëren](cognitive-search-custom-skill-interface.md)voor hulp bij het maken van een aangepaste web-API voor de verrijkings pijplijn.

U ziet dat het veld context is ingesteld op ```"/document/organizations/*"``` met een asterisk, wat betekent dat de verrijkings stap wordt aangeroepen *voor elke* organisatie onder. ```"/document/organizations"``` 

In dit geval wordt de beschrijving van het bedrijf voor elke geïdentificeerde organisatie gegenereerd. Wanneer u verwijst naar de beschrijving in een downstream-stap (bijvoorbeeld in extractie van sleutel zinnen), gebruikt u het pad ```"/document/organizations/*/description"``` om dit te doen. 

## <a name="add-structure"></a>Structuur toevoegen

De vaardig heden genereren gestructureerde informatie uit ongestructureerde gegevens. Kijk een naar het volgende voorbeeld:

*"In het vierde kwar taal heeft micro soft $1.100.000.000 in de opbrengst van LinkedIn, het sociale netwerk dat het vorig jaar is gekocht. Dankzij de aanschaf mogelijkheden van micro soft kunnen LinkedIn-mogelijkheden worden gecombineerd met de CRM-en Office-functies. Aandeel houders zijn enthousiast over de voortgang tot nu toe. "*

Een waarschijnlijk resultaat is een gegenereerde structuur die vergelijkbaar is met de volgende afbeelding:

![Voorbeeld uitvoer structuur](media/cognitive-search-defining-skillset/enriched-doc.png "Voorbeeld uitvoer structuur")

Tot nu toe is deze structuur alleen intern, alleen-geheugen en alleen gebruikt in Azure Search indexen. Het toevoegen van een kennis archief biedt een manier om vormige verrijkingen op te slaan voor gebruik buiten de zoek opdracht.

## <a name="add-a-knowledge-store"></a>Een kennis archief toevoegen

[Knowledge Store](knowledge-store-concept-intro.md) is een preview-functie in azure Search om uw verrijkte document op te slaan. Een kennis archief dat u maakt, ondersteund door een Azure Storage-account, is de opslag plaats waar uw verrijkte gegevens worden gelandd. 

Een definitie van een kennis archief wordt toegevoegd aan een vaardig heden. Zie [How to start with Knowledge Store](knowledge-store-howto.md)(Engelstalig) voor een overzicht van het gehele proces.

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

U kunt ervoor kiezen om de verrijkte documenten op te slaan als tabellen met hiërarchische relaties die zijn behouden of als JSON-documenten in Blob Storage. Uitvoer van een van de vaardig heden in de vaardigy kan worden gebrond als de invoer voor de projectie. Als u de gegevens wilt projecteren in een specifieke vorm, kan de bijgewerkte [shaper-vaardigheid](cognitive-search-skill-shaper.md) nu complexe typen maken die u kunt gebruiken. 

<a name="next-step"></a>

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de verrijkings pijplijn en vaardig heden, gaat u verder met het [verwijzen naar aantekeningen in een vaardig heden](cognitive-search-concept-annotations-syntax.md) of [over het toewijzen van uitvoer aan velden in een index](cognitive-search-output-field-mapping.md). 
