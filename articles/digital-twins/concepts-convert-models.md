---
title: In de industrie gangbare modellen converteren
titleSuffix: Azure Digital Twins
description: Inzicht in het patroon voor het converteren van OWL-modellen (Industry-Standard) naar DTDL
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2d30410cf201475b69adce39eb6a66a7831cad8d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503246"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Industrie standaard modellen converteren naar DTDL voor Azure Digital Apparaatdubbels

Modellen in azure Digital Apparaatdubbels worden weer gegeven in de op JSON-LD gebaseerde [**Digital Apparaatdubbels Definition Language (DTDL)**](concepts-models.md). Als u bestaande modellen buiten Azure Digital Apparaatdubbels hebt die zijn gebaseerd op een industrie standaard, zoals RDF of OWL, moet u **deze converteren naar DTDL** om ze te kunnen gebruiken met Azure Digital apparaatdubbels. De DTDL-versie wordt vervolgens de bron van waarheid voor het model in azure Digital Apparaatdubbels.

In dit artikel wordt een patroon beschreven voor het converteren van op RDF gebaseerde industrie of aangepaste modellen naar DTDL. Het bevat:
* **Richt lijnen op strategie niveau** die kunnen worden toegepast op diverse standaarden en model typen
* [**Voorbeeld code** voor een RDF-specifiek conversie programma](#sample-converter-application)

## <a name="industry-models"></a>Branche modellen  

Het gebruik van industrie modellen biedt een rijk begin punt bij het ontwerpen van uw Azure Digital Apparaatdubbels-model. Het gebruik van industrie modellen helpt ook bij standaardisatie en het delen van gegevens. 

Enkele algemene branche modellen zijn:  

| Verticale sector | Modelleren |
| --- | --- | 
| Gebouw/faciliteit beheer | [RealEstateCore](https://www.realestatecore.io/)<br>[BRICK-schema](https://brickschema.org/ontology/1.1/)<br>[Topologie Ontology (BOT) bouwen](https://w3c-lbd-cg.github.io/bot/)<br>[Semantische sensor netwerk](https://www.w3.org/TR/vocab-ssn/)<br>[IFC-klassen (buildingSmart Industry Foundation)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Smart steden | [ETSI NGSI-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Naslag informatie over Smart Applications (SAREF)](https://saref.etsi.org/)<br>[Fiware](https://www.fiware.org/)<br>[& Agile-steden (OASC) openen](https://oascities.org/) |
| Energie raster | [CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968)<br>[ADRM-modellen](http://www.adrm.com/) voor energie-en grondstoffen handel |
| Auto's | [Specificatie van voertuig signalen](https://github.com/GENIVI/vehicle_signal_specification/tree/master/spec) |

Afhankelijk van uw behoeften kunt u ook DTDL gebruiken om branche modellen aan te passen of te uitbreiden of uw eigen aangepaste model helemaal zelf te ontwikkelen. 

## <a name="create-and-edit-models"></a>Modellen maken en bewerken

De eerste stap in het model leren maakt uw modellen. U kunt uw industrie standaard modellen maken en volt ooien voordat u ze converteert naar DTDL, maar u kunt ze ook converteren naar DTDL Early en het bewerken ervan als DTDL documenten voortzetten.

### <a name="with-industry-standards"></a>Met industrie normen

De meeste branche modellen (ook wel **Ontologies**genoemd) zijn gebaseerd op semantische web-standaarden zoals [Owl](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF)en [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

In sommige gevallen wilt u mogelijk een model maken of bewerken met behulp van OWL-model hulpprogramma's. U kunt een wille keurig aantal hulpprogram ma's voor het ontwerpen van modellen gebruiken om een OWL model te ontwerpen, met inbegrip van het volgende.
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) en [Protégé Desktop](https://protege.stanford.edu/products.php#desktop-protege) zijn populaire voor beelden. U kunt branche modellen importeren in meerdere indelingen, een model bewerken of uitbreiden en het model exporteren. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) is een ander populair hulp programma voor het visualiseren van modellen. 

Als u een model maakt met behulp van een van de industrie normen die niet DTDL zijn, moet u deze vervolgens converteren naar DTDL en dit bestand uploaden naar Azure Digital Apparaatdubbels. 

#### <a name="common-model-file-formats"></a>Algemene indelingen voor model bestanden 

RDF, OWL en RDFS zijn de basis bouwstenen van het semantische web. 

**RDF** biedt een conceptuele structuur voor het beschrijven van dingen, in de vorm van **triples**. Drie delen bestaan: **onderwerp**, **predicaat**en **object**. Objecten kunnen worden gemaakt van Uri's. 

Hier volgen enkele voor beelden van RDF-drieën:

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Deze voor beelden zijn alle geldige RDF, maar de laatste instructie is semantisch ongeldig. In deze situatie wordt de afbeelding in de OWL-specificatie ingevoerd. **Owl** definieert wat u kunt schrijven met RDF om geldige Ontology te hebben.

Hier volgt een voor beeld van het gebruik van OWL: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**RDFS** biedt extra woordenlijst semantiek waarmee u klassen kunt definiëren en beschrijven. Een van deze klassen is bijvoorbeeld `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

Modellen kunnen worden opgeslagen, geïmporteerd en geëxporteerd in verschillende bestands indelingen, waaronder:  
* RDF/XML 
* Schildpad (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>Met DTDL

Azure Digital Apparaatdubbels maakt gebruik van de JSON-LD-gebaseerde **Digital-definitie taal (DTDL)** voor model lering. Elk model dat wordt gebruikt met Azure Digital Apparaatdubbels, moet oorspronkelijk zijn geschreven of worden geconverteerd naar DTDL.

Wanneer u werkt met modellen in DTDL, kunt u de [**uitbrei ding DTDL**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   beschikbaar voor [Visual Studio code](https://code.visualstudio.com/)gebruiken, die syntaxis validatie en andere functies biedt om het schrijven van DTDL modellen te vergemakkelijken.

Meer informatie over Azure Digital Apparaatdubbels-modellen en hun onderdelen vindt u in [*concepten: aangepaste modellen*](concepts-models.md) en [*instructies: aangepaste modellen beheren*](how-to-manage-model.md).

## <a name="convert-models-to-dtdl"></a>Modellen naar DTDL converteren

Als u een model met Azure Digital Apparaatdubbels wilt gebruiken, moet dit de DTDL-indeling hebben. In deze sectie wordt beschreven hoe u op RDF gebaseerde modellen converteert naar DTDL, zodat deze kunnen worden gebruikt met Azure Digital Apparaatdubbels.

Er zijn verschillende bibliotheken van derden die kunnen worden gebruikt bij het converteren van op RDF gebaseerde modellen naar DTDL. Met sommige van deze bibliotheken kunt u uw model bestand laden in een grafiek. U kunt door de grafiek kijken naar specifieke RDFS-en OWL-constructies en deze converteren naar DTDL.   

De volgende tabel is een voor beeld van hoe RDFS-en OWL-constructies kunnen worden toegewezen aan DTDL. 

| RDFS/OWL-concept | RDFS/OWL-construct | DTDL-concept | DTDL-constructie |
| --- | --- | --- | --- |
| Klassen | `owl:Class`<br>Achtervoegsel voor IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subklassen | `owl:Class`<br>Achtervoegsel voor IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Eigenschappen van gegevens type | `owl:DatatypeProperty`<br>`rdfs:label` of `INode`<br>`rdfs:label`<br>`rdfs:range` | Interface-eigenschappen | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Object eigenschappen | `owl:ObjectProperty`<br>`rdfs:label` of `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relatie | `type:Relationship`<br>`name`<br>`target` (of als er geen waarde wordt opgegeven `rdfs:range` )<br>`comment`<br>`displayName`<br>

In het volgende code fragment van C# ziet u hoe een RDF-model bestand in een grafiek wordt geladen en wordt geconverteerd naar DTDL, met behulp van de [**dotNetRDF**](https://www.dotnetrdf.org/) -bibliotheek. 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>Valideer en upload DTDL-modellen

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Zodra een model is geconverteerd en gevalideerd, kunt u **het uploaden naar uw Azure Digital apparaatdubbels-exemplaar**. Zie voor meer informatie over dit proces het gedeelte [*modellen uploaden*](how-to-manage-model.md#upload-models) van *instructies: aangepaste modellen beheren*.

## <a name="sample-converter-application"></a>Voor beeld-Converter toepassing 

Er is een voorbeeld toepassing beschikbaar waarmee een op RDF gebaseerd model bestand wordt geconverteerd naar [DTDL (versie 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) voor gebruik door de Azure Digital apparaatdubbels-service. Het voor beeld is een .NET core-opdracht regel toepassing met de naam **RdfToDtdlConverter**.

U kunt het voor beeld hier ophalen: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Als u de code wilt downloaden naar uw computer, klikt u op de knop voor het downloaden van de *zip* onder de titel op de pagina voor beeld van land. Hiermee wordt een *zip* -bestand met de naam *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*gedownload, dat u vervolgens kunt uitpakken en verkennen.

U kunt dit voor beeld gebruiken om de conversie patronen in de context te bekijken en als bouw steen voor uw eigen toepassingen model conversies uit te voeren op basis van uw eigen specifieke behoeften.

## <a name="next-steps"></a>Volgende stappen 

Meer informatie over het ontwerpen en beheren van digitale twee modellen:
 
* [*Concepten: Aangepaste modellen*](concepts-models.md)
* [*Instructies: Aangepaste modellen beheren*](how-to-manage-model.md)
* [*Instructies: modellen parseren en valideren*](how-to-parse-models.md)