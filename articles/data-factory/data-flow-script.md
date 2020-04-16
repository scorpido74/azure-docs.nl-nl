---
title: Script voor gegevensstromen toewijzen
description: Overzicht van de dataflow script code-behind taal van Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.openlocfilehash: e0042960c25d58b72bc0ab884de5a2db62e566d9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413447"
---
# <a name="data-flow-script-dfs"></a>Gegevensstroomscript (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data flow script (DFS) is de onderliggende metagegevens, vergelijkbaar met een codeertaal, die wordt gebruikt om de transformaties uit te voeren die zijn opgenomen in een mapping data flow. Elke transformatie wordt vertegenwoordigd door een reeks eigenschappen die de nodige informatie bieden om de taak goed uit te voeren. Het script is zichtbaar en bewerkbaar vanuit ADF door te klikken op de knop 'script' op het bovenste lint van de gebruikersinterface van de browser.

![Knop Script](media/data-flow/scriptbutton.png "Knop Script")

In een `allowSchemaDrift: true,` brontransformatie wordt bijvoorbeeld de service gevraagd om alle kolommen uit de brongegevensset in de gegevensstroom op te nemen, zelfs als ze niet zijn opgenomen in de schemaprojectie.

## <a name="use-cases"></a>Gebruiksvoorbeelden
De DFS wordt automatisch geproduceerd door de gebruikersinterface. U op de knop Script klikken om het script weer te geven en aan te passen. U ook scripts buiten de ADF-gebruikersinterface genereren en dat vervolgens doorgeven aan de PowerShell-cmdlet. Wanneer u complexe gegevensstromen debugadt, u het gemakkelijker vinden om de scriptcode achter te scannen in plaats van de weergave van de ui-grafiek van uw stromen te scannen.

Hier zijn een paar voorbeeld voorbeelden gevallen:
- Programmatisch produceren van veel gegevensstromen die vrij vergelijkbaar zijn, d.w.z. "stamping-out" datastromen.
- Complexe expressies die moeilijk te beheren zijn in de gebruikersinterface of resulteren in validatieproblemen.
- Foutopsporing en beter begrip van verschillende fouten die tijdens de uitvoering zijn geretourneerd.

Wanneer u een gegevensstroomscript bouwt dat u wilt gebruiken met PowerShell of een API, moet u de opgemaakte tekst samenvouwen in één regel. U tabbladen en nieuwe lijnen als escape personages. Maar de tekst moet worden opgemaakt om in een JSON-eigenschap te passen. Er is een knop op de scripteditor UI aan de onderkant die het script zal formatteren als een enkele regel voor jou.

![knop Kopiëren](media/data-flow/copybutton.png "knop Kopiëren")

## <a name="how-to-add-transforms"></a>Transformaties toevoegen
Het toevoegen van transformaties vereist drie basisstappen: het toevoegen van de kerntransformatiegegevens, het herrouteren van de invoerstroom en het opnieuw routeren van de uitvoerstroom. Dit kan het gemakkelijkst worden gezien in een voorbeeld.
Laten we zeggen dat we beginnen met een eenvoudige bron om de gegevensstroom als volgt te laten zinken:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Als we besluiten om een transformatie toe te voegen, moeten we eerst de kerntransformatietekst maken, die een eenvoudige expressie heeft om een nieuwe kolom in hoofdletters toe te voegen, genaamd: `upperCaseTitle`
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Vervolgens nemen we de bestaande DFS en voegen we de transformatie toe:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

En nu leiden we de binnenkomende stroom om door te bepalen welke transformatie `source1`we willen dat de nieuwe transformatie komt (in dit geval) en de naam van de stroom te kopiëren naar de nieuwe transformatie:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Tot slot identificeren we de transformatie die we na deze nieuwe transformatie `sink1`willen komen, en vervangen we de inputstream (in dit geval) door de naam van de uitvoerstroom van onze nieuwe transformatie:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS-fundamenten
De DFS bestaat uit een reeks verbonden transformaties, waaronder bronnen, sinks en diverse andere die nieuwe kolommen kunnen toevoegen, gegevens filteren, gegevens toevoegen en gegevens kunnen toevoegen en nog veel meer. Meestal begint het script met een of meer bronnen, gevolgd door vele transformaties en eindigend met een of meer putten.

Bronnen hebben allemaal dezelfde basisconstructie:
```
source(
  source properties
) ~> source_name
```

Bijvoorbeeld, een eenvoudige bron met drie kolommen (movieId, titel, genres) zou zijn:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Alle andere transformaties dan bronnen hebben dezelfde basisconstructie:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Een eenvoudige transformatie die een kolom (titel) neemt en deze bijvoorbeeld overschrijft met een hoofdletterversie, is als volgt:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

En een gootsteen zonder schema zou gewoon zijn:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Scriptfragmenten

Scriptfragmenten zijn deelbare code van Data Flow Script die u gebruiken om te delen tussen gegevensstromen. In deze video hieronder wordt beschreven hoe u scriptfragmenten gebruiken en gegevensstroomscript gebruiken om delen van het script achter uw gegevensstroomgrafieken te kopiëren en plakken:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Geaggregeerde overzichtsstatistieken
Voeg een geaggregeerde transformatie toe aan uw gegevensstroom met de naam "SummaryStats" en plak vervolgens in deze code hieronder voor de samengevoegde functie in uw script, ter vervanging van de bestaande SummaryStats. Dit zal een generiek patroon voor gegevens profiel samenvatting statistieken.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
U ook het onderstaande voorbeeld gebruiken om het aantal unieke en het aantal afzonderlijke rijen in uw gegevens te tellen. Het onderstaande voorbeeld kan worden geplakt in een gegevensstroom met aggregaattransformatie genaamd ValueDistAgg. In dit voorbeeld wordt een kolom gebruikt met de naam "titel". Zorg ervoor dat u 'titel' vervangt door de tekenreekskolom in uw gegevens die u wilt gebruiken om waardetellingen te krijgen.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Alle kolommen in een aggregaat opnemen
Dit is een algemeen totaalpatroon dat laat zien hoe u de resterende kolommen in uw uitvoermetagegevens behouden wanneer u aggregaten bouwt. In dit geval gebruiken ```first()``` we de functie om de eerste waarde te kiezen in elke kolom waarvan de naam geen "film" is. Als u dit wilt gebruiken, maakt u een aggregaattransformatie met de naam DistinctRows en plakt u deze vervolgens in het script boven op het bestaande distinctrows-geaggregeerde script.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Vingerafdruk van rijhash maken 
Gebruik deze code in uw gegevensstroomscript om ```DWhash``` een nieuwe ```sha1``` afgeleide kolom te maken die een hash van drie kolommen produceert.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>Volgende stappen

Gegevensstromen verkennen door te beginnen met het [overzichtsartikel gegevensstromen](concepts-data-flow-overview.md)
