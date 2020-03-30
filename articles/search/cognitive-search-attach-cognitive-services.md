---
title: Cognitieve services koppelen aan een skillset
titleSuffix: Azure Cognitive Search
description: Instructies voor het koppelen van een Cognitive Services all-in-one-abonnement aan een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472448"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Een resource voor Cognitive Services koppelen aan een skillset in Azure Cognitive Search 

Wanneer u een verrijkingspijplijn configureert in Azure Cognitive Search, u een beperkt aantal documenten gratis verrijken. Voor grotere en frequentere workloads moet u een factureerbare resource voor Cognitive Services toevoegen.

In dit artikel leert u hoe u een resource koppelt door een sleutel toe te voegen aan een skillset die een verrijkingspijplijn definieert.

## <a name="resources-used-during-enrichment"></a>Middelen die tijdens de verrijking worden gebruikt

Azure Cognitive Search is afhankelijk van Cognitive Services, waaronder [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) voor beeldanalyse en optische tekenherkenning (OCR), [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) voor natuurlijke taalverwerking en andere verrijkingen zoals [Tekstvertaling.](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) In de context van verrijking in Azure Cognitive Search worden deze AI-algoritmen verpakt in een *vaardigheid*, geplaatst in een *skillset*en verwezen door een *indexeerder* tijdens het indexeren.

## <a name="how-billing-works"></a>Werking van facturering

+ Azure Cognitive Search gebruikt de resourcesleutel Cognitive Services die u op een skillset opgeeft om te factureren voor beeld- en tekstverrijking. Uitvoering van factureerbare vaardigheden is tegen de [Cognitive Services pay-as-you go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/)

+ Imageextractie is een Azure Cognitive Search-bewerking die optreedt wanneer documenten worden gekraakt voordat ze worden verrijkt. Beeldextractie is factureerbaar. Zie de [prijspagina azure cognitive search](https://go.microsoft.com/fwlink/?linkid=2042400)voor de prijzen van afbeeldingen.

+ Tekstextractie vindt ook plaats tijdens de zin voor het kraken van documenten. Het is niet factureerbaar.

+ Vaardigheden die geen cognitieve services aanroepen, waaronder voorwaardelijke, vormvormer- en tekstsamenvoeging- en tekstgesplitstvaardigheden, zijn niet factureerbaar.

## <a name="same-region-requirement"></a>Eis voor dezelfde regio

We vereisen dat Azure Cognitive Search en Azure Cognitive Services binnen dezelfde regio bestaan. Anders krijgt u dit bericht tijdens de looptijd:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Er is geen manier om een service over regio's te verplaatsen. Als u deze fout krijgt, moet u een nieuwe bron voor Cognitieve services maken in dezelfde regio als Azure Cognitive Search.

> [!NOTE]
> Sommige ingebouwde vaardigheden zijn gebaseerd op niet-regionale cognitieve diensten (bijvoorbeeld de [Text Translation Skill).](cognitive-search-skill-text-translation.md) Het gebruik van een niet-regionale vaardigheid betekent dat uw aanvraag mogelijk wordt onderhouden in een andere regio dan de azure cognitive search-regio. Zie de pagina Cognitive Services [product per regio voor](https://aka.ms/allinoneregioninfo) meer informatie over niet-regionale diensten.

## <a name="use-free-resources"></a>Gratis bronnen gebruiken

U een beperkte, gratis verwerkingsoptie gebruiken om de AI-verrijkingszelfstudie en quickstart-oefeningen te voltooien.

Gratis (Beperkte verrijkingen) middelen zijn beperkt tot 20 documenten per dag, per indexer. U de indexer verwijderen en opnieuw maken om de teller opnieuw te instellen.

1. Open de wizard Gegevens importeren:

   ![De wizard Gegevens importeren openen](media/search-get-started-portal/import-data-cmd.png "De wizard Gegevens importeren openen")

1. Kies een gegevensbron en ga verder **met AI-verrijking toevoegen (optioneel).** Zie [Een index maken in de Azure-portal](search-get-started-portal.md)voor een stapsgewijze doorloop van deze wizard.

1. Breid **Cognitieve services bij voegen** uit en selecteer vervolgens Gratis **(beperkte verrijkingen):**

   ![Uitgebreide sectie Cognitieve services bijvoegen](./media/cognitive-search-attach-cognitive-services/attach1.png "Uitgebreide sectie Cognitieve services bijvoegen")

1. U nu doorgaan naar de volgende stappen, waaronder **Cognitieve vaardigheden toevoegen.**

## <a name="use-billable-resources"></a>Factureerbare bronnen gebruiken

Voor workloads die meer dan 20 verrijkingen per dag maken, moet u een factureerbare resource voor Cognitive Services toevoegen. We raden u aan altijd een factureerbare Cognitive Services-bron toe te voegen, zelfs als u nooit van plan bent API's voor Cognitive Services te bellen. Als u een resource koppelt, wordt de dagelijkse limiet overschreven.

U wordt alleen in rekening gebracht voor vaardigheden die de API's voor cognitieve services noemen. U wordt niet gefactureerd voor [aangepaste vaardigheden](cognitive-search-create-custom-skill-example.md)of vaardigheden zoals [tekstfusie,](cognitive-search-skill-textmerger.md) [tekstsplitter](cognitive-search-skill-textsplit.md)en [shaper,](cognitive-search-skill-shaper.md)die niet op API's zijn gebaseerd.

1. Open de wizard Gegevens importeren, kies een gegevensbron en ga verder **met AI-verrijking toevoegen (optioneel).**

1. Vouw **Cognitieve services bijvoegen** uit en selecteer vervolgens **Nieuwe resource voor cognitieve services maken**. Er wordt een nieuw tabblad geopend, zodat u de resource maken:

   ![Een Cognitive Services-resource maken](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Een Cognitive Services-resource maken")

1. Selecteer **in** de lijst Locatie het gebied waar uw Azure Cognitive Search-service zich bevindt. Zorg ervoor dat u deze regio gebruikt om prestatieredenen. Als u deze regio gebruikt, worden ook uitgaande bandbreedtekosten in verschillende regio's ongeldig gemaakt.

1. Selecteer **In** de lijst Met prijzen **S0** om de alles-in-één verzameling functies voor cognitieve services te krijgen, inclusief de functies Visie en Taal die de ingebouwde vaardigheden van Azure Cognitive Search weergeven.

   Voor de s0-laag vindt u tarieven voor specifieke workloads op de [prijspagina Cognitive Services.](https://azure.microsoft.com/pricing/details/cognitive-services/)
  
   + Controleer in de lijst **Aanbieding selecteren** of **cognitieve services** zijn geselecteerd.
   + Onder **Taalfuncties** zijn de tarieven voor **Text Analytics Standard** van toepassing op AI-indexering.
   + Onder **Vision-functies** zijn de tarieven voor **Computer Vision S1** van toepassing.

1. Selecteer **Maken** om de nieuwe resource Voor Cognitieve Services in te richten.

1. Ga terug naar het vorige tabblad, dat de wizard Gegevens importeren bevat. Selecteer **Vernieuwen** om de resource Cognitive Services weer te geven en selecteer vervolgens de resource:

   ![De resource Cognitive Services selecteren](./media/cognitive-search-attach-cognitive-services/attach2.png "De resource Cognitive Services selecteren")

1. Vouw de sectie **Cognitieve vaardigheden toevoegen uit** om de specifieke cognitieve vaardigheden te selecteren die u op uw gegevens wilt uitvoeren. Voltooi de rest van de wizard.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Een bestaande skillset koppelen aan een resource voor Cognitive Services

Als u een bestaande skillset hebt, u deze koppelen aan een nieuwe of andere resource voor Cognitive Services.

1. Selecteer op de pagina **Serviceoverzicht** de optie **Skillsets:**

   ![Tabblad Skillsets](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Tabblad Skillsets")

1. Selecteer de naam van de skillset en selecteer vervolgens een bestaande resource of maak een nieuwe resource. Selecteer **OK** om uw wijzigingen te bevestigen.

   ![Lijst met skillset-bronnen](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lijst met skillset-bronnen")

   Houd er rekening mee dat de optie **Gratis (Beperkte verrijkingen)** u beperkt tot 20 documenten per dag en dat u **nieuwe resource voor cognitieve services** maken om een nieuwe factureerbare bron in te richten. Als u een nieuwe bron maakt, selecteert u **Vernieuwen** om de lijst met bronnen voor cognitieve services te vernieuwen en selecteert u de bron.

## <a name="attach-cognitive-services-programmatically"></a>Cognitieve services programmatisch koppelen

Wanneer u de skillset programmatisch definieert, `cognitiveServices` voegt u een sectie toe aan de skillset. Neem in die sectie de sleutel op van de resource Cognitive Services die u wilt koppelen aan de skillset. Houd er rekening mee dat de bron zich in dezelfde regio moet bevinden als uw Azure Cognitive Search-bron. Ook, `@odata.type`en stel `#Microsoft.Azure.Search.CognitiveServicesByKey`het in op .

In het volgende voorbeeld ziet u dit patroon. Let `cognitiveServices` op de sectie aan het einde van de definitie.

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

Als u de kosten wilt schatten die zijn gekoppeld aan het indexeren van cognitief zoeken, begint u met een idee van hoe een gemiddeld document eruit ziet, zodat u bepaalde getallen uitvoeren. U bijvoorbeeld bij benadering:

+ 1000 PDF's.
+ Zes pagina's per stuk.
+ Eén afbeelding per pagina (6.000 afbeeldingen).
+ 3.000 tekens per pagina.

Neem een pijplijn die bestaat uit het kraken van documenten van elke PDF, beeld- en tekstextractie, optische tekenherkenning (OCR) van afbeeldingen en entiteitsherkenning van organisaties.

De prijzen in dit artikel zijn hypothetisch. Ze worden gebruikt om het schattingsproces te illustreren. Uw kosten kunnen lager zijn. Zie [Prijzen voor cognitieve services](https://azure.microsoft.com/pricing/details/cognitive-services)voor de werkelijke prijzen van transacties.

1. Voor het kraken van documenten met tekst- en afbeeldingsinhoud is tekstextractie momenteel gratis. Voor 6.000 afbeeldingen, neem $1 voor elke 1.000 beelden geëxtraheerd. Dat kost $6,00 voor deze stap.

2. Voor OCR van 6.000 afbeeldingen in het Engels gebruikt de OCR-cognitieve vaardigheid het beste algoritme (DescribeText). Ervan uitgaande dat een kostprijs van $ 2,50 per 1.000 beelden worden geanalyseerd, zou u betalen $ 15,00 voor deze stap.

3. Voor entiteitsextractie hebt u in totaal drie tekstrecords per pagina. Elke plaat bestaat uit 1000 tekens. Drie tekstrecords per pagina vermenigvuldigd met 6.000 pagina's zijn gelijk aan 18.000 tekstrecords. Ervan uitgaande dat $ 2,00 per 1.000 tekst records, zou deze stap kosten $36.00.

Om alles bij elkaar te zetten, zou je ongeveer $ 57,00 betalen om 1.000 PDF-documenten van dit type in te nemen met de beschreven vaardigheden.

## <a name="next-steps"></a>Volgende stappen
+ [Prijspagina Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Skillset (REST) maken](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Verrijkte velden in kaart brengen](cognitive-search-output-field-mapping.md)
