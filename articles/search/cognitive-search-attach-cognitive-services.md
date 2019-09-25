---
title: Een Cognitive Services resource koppelen met een vaardig heden-Azure Search
description: Instructies voor het koppelen van een Cognitive Services alles-in-één-abonnement op een cognitieve verrijkings pijplijn in Azure Search.
manager: nitinme
author: LuisCabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.openlocfilehash: 113286f829b628d4740fbba34e7279741a934aef
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265933"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Een Cognitive Services resource koppelen aan een vakkennis in Azure Search 

AI-algoritmen zorgen voor de [cognitieve indexerings pijplijnen](cognitive-search-concept-intro.md) die worden gebruikt voor document verrijking in azure Search. Deze algoritmen zijn gebaseerd op Azure Cognitive Services-resources, waaronder [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) voor het analyseren van afbeeldingen en optische teken herkenning (OCR) en [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) voor entiteits herkenning, extractie van sleutel zinnen en andere verrijkingen . Zoals wordt gebruikt door Azure Search voor verrijkings doeleinden van documenten, worden de algoritmen in een *vaardigheid*verpakt, in een *vaardig heden*geplaatst en tijdens het indexeren verwezen door een *Indexeer functie* .

U kunt een beperkt aantal gratis documenten verrijken. U kunt ook een factureer bare Cognitive Services resource koppelen aan een *vaardig heden* voor grotere en frequentere workloads. In dit artikel leert u hoe u een factureer bare Cognitive Services resource kunt koppelen aan verrijkte documenten tijdens het Azure Search [indexeren](search-what-is-an-index.md).

> [!NOTE]
> Factureer bare gebeurtenissen omvatten aanroepen naar Cognitive Services-API's en extractie van afbeeldingen als onderdeel van de fase voor het kraken van documenten in Azure Search. Er worden geen kosten in rekening gebracht voor de extractie van tekst uit documenten of voor vaardig heden die Cognitive Services niet aanroepen.
>
> Het uitvoeren van factureer bare vaardig heden bevindt zich op de Cognitive Services prijs op basis van [betalen per gebruik](https://azure.microsoft.com/pricing/details/cognitive-services/). Zie de [pagina met Azure Search prijzen](https://go.microsoft.com/fwlink/?linkid=2042400)voor meer informatie over het ophalen van images.

## <a name="same-region-requirement"></a>Dezelfde regio vereiste

Azure Search en Azure Cognitive Services bestaan alleen binnen dezelfde regio. Anders wordt dit bericht weer gegeven tijdens de uitvoering:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Er is geen manier om een service te verplaatsen tussen regio's. Als u deze fout ontvangt, moet u een nieuwe Cognitive Services-resource maken in dezelfde regio als Azure Search.

> [!NOTE]
> Sommige ingebouwde vaardig heden zijn gebaseerd op niet-regionale Cognitive Services (bijvoorbeeld de [tekst Vertaal vaardigheid](cognitive-search-skill-text-translation.md)). Houd er rekening mee dat als u een van deze vaardig heden aan uw vaardig heden toevoegt die niet gegarandeerd dat uw gegevens in dezelfde regio blijven als uw Azure Search of Cognitive Services resource. Raadpleeg de [pagina service status](https://aka.ms/allinoneregioninfo) voor meer informatie.

## <a name="use-free-resources"></a>Gratis bronnen gebruiken

U kunt een beperkte, gratis verwerkings optie gebruiken voor het volt ooien van de zelf studie voor cognitieve zoek acties en Quick Start-oefeningen.

Gratis (beperkte verrijkingen) resources zijn beperkt tot 20 documenten per dag, per abonnement.

1. Open de wizard gegevens importeren:

   ![De wizard gegevens importeren openen](media/search-get-started-portal/import-data-cmd.png "De wizard gegevens importeren openen")

1. Kies een gegevens bron en blijf een **cognitieve zoek opdracht toevoegen (optioneel)** . Zie [import, index en query met behulp van portal tools](search-get-started-portal.md)voor een stapsgewijze uitleg van deze wizard.

1. Vouw **Cognitive Services koppelen** uit en selecteer vervolgens **gratis (beperkte verrijkingen)** :

   ![Sectie uitgebreide Cognitive Services toevoegen](./media/cognitive-search-attach-cognitive-services/attach1.png "Sectie uitgebreide Cognitive Services toevoegen")

1. Ga door naar de volgende stap en **Voeg verrijkingen toe**. Zie [voor een beschrijving van de beschik bare vaardig heden in de portal stap 2: Voeg cognitieve vaardig](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) heden toe aan de Snelstartgids voor de cognitieve zoek functie.

## <a name="use-billable-resources"></a>Factureer bare resources gebruiken

Voor workloads die meer dan 20 verrijkingen per dag maken, moet u ervoor zorgen dat u een factureer bare Cognitive Services resource koppelt. U wordt aangeraden altijd een factureer bare Cognitive Services resource te koppelen, zelfs als u niet van plan bent om Cognitive Services-API's aan te roepen. Als u een resource koppelt, wordt de dagelijkse limiet overschreven.

Er worden alleen kosten in rekening gebracht voor de vaardig heden die de Cognitive Services-API's aanroepen. U wordt niet gefactureerd voor [aangepaste vaardig heden](cognitive-search-create-custom-skill-example.md)of vaardig heden zoals [tekst fusie](cognitive-search-skill-textmerger.md), [tekst splitsing](cognitive-search-skill-textsplit.md)en [shaper](cognitive-search-skill-shaper.md), die geen API-gebaseerd zijn.

1. Open de wizard gegevens importeren, kies een gegevens bron en blijf een **cognitieve zoek opdracht toevoegen (optioneel)** .

1. Vouw **Cognitive Services koppelen** uit en selecteer vervolgens **nieuwe Cognitive Services resource maken**. Er wordt een nieuw tabblad geopend, zodat u de resource kunt maken:

   ![Een Cognitive Services resource maken](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Een Cognitive Services resource maken")

1. Selecteer in de lijst **locatie** de regio waar uw Azure Search-service zich bevindt. Zorg ervoor dat u deze regio gebruikt om prestatie redenen. Als u deze regio gebruikt, worden ook de kosten voor uitgaande band breedte tussen regio's verwijderd.

1. Selecteer in de lijst **prijs categorie** de optie **s0** om de alles-in-één-verzameling van Cognitive Services-functies op te halen, met inbegrip van de visie-en taal functies waarmee de vooraf gedefinieerde vaardig heden die door Azure Search worden gebruikt, worden weer gegeven.

   Voor de S0-laag kunt u tarieven voor specifieke werk belastingen vinden op de [pagina met Cognitive Services prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Zorg ervoor dat **Cognitive Services** is geselecteerd in de lijst **aanbieding selecteren** .
   + Onder **taal** functies zijn de tarieven voor **Text Analytics standaard** van toepassing op AI-indexering.
   + Onder **Vision** -functies zijn de tarieven voor **Computer Vision S1** van toepassing.

1. Selecteer **maken** om de nieuwe Cognitive Services resource in te richten.

1. Ga terug naar het vorige tabblad, dat de wizard gegevens importeren bevat. Selecteer **vernieuwen** om de Cognitive Services resource weer te geven en selecteer vervolgens de resource:

   ![De Cognitive Services resource selecteren](./media/cognitive-search-attach-cognitive-services/attach2.png "De Cognitive Services resource selecteren")

1. Vouw de sectie **verrijkingen toevoegen** uit om de specifieke cognitieve vaardig heden te selecteren die u wilt uitvoeren op uw gegevens. Voltooi de rest van de wizard. Zie [voor een beschrijving van de beschik bare vaardig heden in de portal stap 2: Voeg cognitieve vaardig](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) heden toe aan de Snelstartgids voor de cognitieve zoek functie.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Een bestaande vaardig heden koppelen aan een Cognitive Services resource

Als u een bestaande vaardig heden hebt, kunt u deze koppelen aan een nieuwe of andere Cognitive Services resource.

1. Selecteer op de pagina **service overzicht** de optie **vaardig heden**:

   ![Tabblad vaardig heden](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Tabblad vaardig heden")

1. Selecteer de naam van de vaardig heden en selecteer vervolgens een bestaande resource of maak een nieuwe. Selecteer **OK** om uw wijzigingen te bevestigen.

   ![Lijst met vaardig heden-resources](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lijst met vaardig heden-resources")

   Houd er rekening mee dat de optie **gratis (beperkte verrijkingen)** u per dag beperkt tot 20 documenten en dat u **nieuwe Cognitive Services resource maken** kunt gebruiken om een nieuwe factureer bare resource in te richten. Als u een nieuwe resource maakt, selecteert u **vernieuwen** om de lijst met Cognitive services resources te vernieuwen en selecteert u vervolgens de resource.

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services via een programma koppelen

Wanneer u de vaardig heden programmatisch definieert, voegt u een `cognitiveServices` sectie toe aan de vaardig heden. In dat gedeelte moet u de sleutel van de Cognitive Services resource die u wilt koppelen aan de vaardig heden, toevoegen. Houd er rekening mee dat de resource zich in dezelfde regio als uw Azure Search-resource moet bevinden. Neem `@odata.type`ook de naam op en stel `#Microsoft.Azure.Search.CognitiveServicesByKey`deze in op.

In het volgende voor beeld ziet u dit patroon. U ziet `cognitiveServices` de sectie aan het einde van de definitie.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Voorbeeld: Kosten schatten

Als u een schatting wilt maken van de kosten die zijn gekoppeld aan het indexeren van cognitieve Zoek opdrachten, begint u met een idee van wat een gemiddeld document lijkt te zijn, zodat u enkele getallen kunt uitvoeren. U kunt bijvoorbeeld ongeveer het volgende doen:

+ 1\.000 Pdf's.
+ Zes pagina's per.
+ Eén afbeelding per pagina (6.000 afbeeldingen).
+ 3\.000 tekens per pagina.

Stel dat u een pijp lijn hebt die bestaat uit het kraken van een document van elke PDF, extractie van afbeeldingen en tekst, optische teken herkenning (OCR) van afbeeldingen en entiteits herkenning van organisaties.

De prijzen die in dit artikel worden weer gegeven, zijn hypothetisch. Ze worden gebruikt om het schattings proces te illustreren. De kosten zijn lager. Zie [Cognitive Services prijzen](https://azure.microsoft.com/pricing/details/cognitive-services)voor de werkelijke prijzen van trans acties.

1. Voor het kraken van documenten met tekst-en afbeeldings inhoud is tekst extractie momenteel gratis. Voor 6.000 installatie kopieën wordt aangenomen dat $1 voor elke 1.000 installatie kopieën worden geëxtraheerd. Dat is een prijs van $6,00 voor deze stap.

2. Voor OCR van 6.000-afbeeldingen in het Engels gebruikt de OCR cognitieve vaardigheid het beste algoritme (DescribeText). Uitgaande van een kosten van $2,50 per 1.000 installatie kopieën, betaalt u $15,00 voor deze stap.

3. Voor het uitpakken van de entiteit hebt u per pagina in totaal drie tekst records. Elke record is 1.000 tekens. Drie tekst records per pagina vermenigvuldigd met 6.000 pagina's is gelijk aan 18.000 tekst records. Als er $2,00 per 1.000-tekst records worden berekend, wordt deze stap $36,00.

U betaalt ongeveer $57,00 tot opname van 1.000 PDF-documenten van dit type met de beschreven vaardig heden.

## <a name="next-steps"></a>Volgende stappen
+ [Pagina met Azure Search prijzen](https://azure.microsoft.com/pricing/details/search/)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardig heden maken (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Verrijkte velden toewijzen](cognitive-search-output-field-mapping.md)
