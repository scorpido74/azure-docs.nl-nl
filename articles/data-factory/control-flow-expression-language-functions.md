---
title: Expressie en functies in Azure Data Factory | Microsoft Docs
description: Dit artikel bevat informatie over expressies en functies die u kunt gebruiken om data factory entiteiten te maken.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9fc8dd1e22e16ef5342b405b602f8baaf8ce7edf
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692846"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressies en functies in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-functions-variables.md)
> * [Huidige versie](control-flow-expression-language-functions.md)

Dit artikel bevat informatie over expressies en functies die door Azure Data Factory worden ondersteund. 

## <a name="introduction"></a>Inleiding
JSON-waarden in de definitie kunnen letterlijk zijn of expressies die tijdens runtime worden geëvalueerd. Bijvoorbeeld:  
  
```json
"name": "value"
```

 of  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Expressies  
Expressies kunnen ergens in een JSON-teken reeks waarde worden weer gegeven en hebben altijd een andere JSON-waarde. Als een JSON-waarde een expressie is, wordt de hoofd tekst van de expressie geëxtraheerd door het (\@)-teken te verwijderen. Als er een letterlijke teken reeks nodig is die begint met \@, moet deze worden ontsnapd met behulp van \@ \@. In de volgende voor beelden ziet u hoe expressies worden geëvalueerd.  
  
|JSON-waarde|Resultaat|  
|----------------|------------|  
|instellen|De tekens ' para meters ' worden geretourneerd.|  
|"para meters [1]"|De tekens ' para meters [1] ' worden geretourneerd.|  
|' \@ \@ '|Er wordt een teken reeks van 1 teken met de waarde ' \@ ' geretourneerd.|  
|"\@"|Een teken reeks van 2 tekens met de waarde ' \@ ' wordt geretourneerd.|  
  
 Expressies kunnen ook worden weer gegeven in teken reeksen, met behulp van een functie met de naam *teken reeks interpolatie* waarbij expressies worden ingepakt in `@{ ... }`. Bijvoorbeeld: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Als u de teken reeks interpolatie gebruikt, is het resultaat altijd een teken reeks. Stel dat ik `myNumber` als `42` en `myString` als `foo` heeft gedefinieerd:  
  
|JSON-waarde|Resultaat|  
|----------------|------------|  
|"\@pipeline (). para meters. myString"| Retourneert `foo` als een teken reeks.|  
|"\@ {pipeline (). para meters. myString}"| Retourneert `foo` als een teken reeks.|  
|"\@pipeline (). para meters. myNumber"| Retourneert `42` als een *getal*.|  
|"\@ {pipeline (). para meters. myNumber}"| Retourneert `42` als een *teken reeks*.|  
|"Antwoord is: @ {pipeline (). para meters. myNumber}"| Retourneert de teken reeks `Answer is: 42`.|  
|"\@concat (' antwoord is: ', String (pijp lijn (). para meters. myNumber))"| Retourneert de teken reeks `Answer is: 42`|  
|"Antwoord is: \@ \@ {pipeline (). para meters. myNumber}"| Retourneert de teken reeks `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Voorbeelden

#### <a name="a-dataset-with-a-parameter"></a>Een gegevensset met een para meter
In het volgende voor beeld gebruikt de BlobDataset een para meter met de naam **Path**. De waarde wordt gebruikt om een waarde in te stellen voor de eigenschap **FolderPath** met behulp van de expressie: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Een pijp lijn met een para meter
In het volgende voor beeld worden de para meters **inputPath** en **outputPath** gebruikt voor de pijp lijn. Het **pad** voor de geparametriseerde BLOB-gegevensset wordt ingesteld met behulp van de waarden van deze para meters. De syntaxis die hier wordt gebruikt, is: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
#### <a name="tutorial"></a>Zelfstudie
In deze [zelf studie](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) leert u hoe u para meters kunt door geven tussen een pijp lijn en activiteit en tussen de activiteiten.

  
## <a name="functions"></a>Functies  
 U kunt functies aanroepen binnen expressies. De volgende secties bevatten informatie over de functies die kunnen worden gebruikt in een expressie.  

## <a name="string-functions"></a>Tekenreeksfuncties  
 De volgende functies zijn alleen van toepassing op teken reeksen. U kunt ook een aantal van de verzamelings functies op teken reeksen gebruiken.  
  
|Functie naam|Beschrijving|  
|-------------------|-----------------|  
|concat|Elk wille keurig aantal teken reeksen combi neren. Als parameter1 bijvoorbeeld `foo,`, retourneert de volgende expressie `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Naam**: teken reeks *n*<br /><br /> **Beschrijving**: vereist. De teken reeksen die moeten worden gecombineerd tot één teken reeks.|  
|subtekenreeks|Retourneert een subset tekens uit een teken reeks. U kunt bijvoorbeeld de volgende expressie:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> retourneert<br /><br /> `foo`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks waaruit de subtekenreeks wordt opgehaald.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: start index<br /><br /> **Beschrijving**: vereist. De index van waar de subtekenreeks begint in para meter 1. Start index is gebaseerd op nul. <br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: lengte<br /><br /> **Beschrijving**: vereist. De lengte van de subtekenreeks.|  
|vervangen|Vervangt een teken reeks door een opgegeven teken reeks. De expressie bijvoorbeeld:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> retourneert<br /><br /> `the new string`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist.  Als para meter 2 is gevonden in para meter 1, de teken reeks die wordt doorzocht op para meter 2 en bijgewerkt met para meter 3.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: oude teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die moet worden vervangen door para meter 3 wanneer een overeenkomst wordt gevonden in para meter 1<br /><br /> **Parameter nummer**: 3<br /><br /> **Name**: nieuwe teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die wordt gebruikt om de teken reeks in para meter 2 te vervangen wanneer een overeenkomst wordt gevonden in para meter 1.|  
|GPT| Genereert een wereld wijd unieke teken reeks (ook wel. GUID). De volgende uitvoer kan bijvoorbeeld `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` worden gegenereerd:<br /><br /> `guid()`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: indeling<br /><br /> **Beschrijving**: optioneel. Een instructie met één indeling die aangeeft [hoe de waarde van deze GUID moet worden ingedeeld](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). De notatie parameter kan "N", "D", "B", "P" of "X" zijn. Als de indeling niet is ingesteld, wordt D gebruikt.|  
|toLower|Zet een teken reeks om in kleine letters. Het volgende retourneert bijvoorbeeld `two by two is four`: `toLower('Two by Two is Four')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die moet worden geconverteerd naar een lager hoofdletter gebruik. Als een teken in de teken reeks geen kleine letters bevat, wordt deze ongewijzigd opgenomen in de geretourneerde teken reeks.|  
|toUpper|Converteert een teken reeks naar hoofd letters. De volgende expressie retourneert bijvoorbeeld `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die moet worden geconverteerd naar een bovenste behuizing. Als een teken in de teken reeks geen hoofd letter-equivalent heeft, wordt deze ongewijzigd opgenomen in de geretourneerde teken reeks.|  
|indexof|De index van een waarde binnen een teken reeks zoeken die niet hoofdletter gevoelig is. Index is gebaseerd op nul. De volgende expressie retourneert bijvoorbeeld `7`: `indexof('hello, world.', 'world')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die de waarde kan bevatten.<br /><br /> **Parameter nummer**: 2<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De waarde waarnaar moet worden gezocht in de index van.|  
|lastindexof|De laatste index van een waarde binnen een teken reeks zoeken die niet hoofdletter gevoelig is. Index is gebaseerd op nul. De volgende expressie retourneert bijvoorbeeld `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die de waarde kan bevatten.<br /><br /> **Parameter nummer**: 2<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De waarde waarnaar moet worden gezocht in de index van.|  
|startsWith|Hiermee wordt gecontroleerd of de teken reeks begint met een waarde die ongevoelig is. De volgende expressie retourneert bijvoorbeeld `true`: `startswith('hello, world', 'hello')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die de waarde kan bevatten.<br /><br /> **Parameter nummer**: 2<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De waarde waarmee de teken reeks kan beginnen.|  
|endswith|Controleert of de teken reeks eindigt met een waarde die niet hoofdletter gevoelig is. De volgende expressie retourneert bijvoorbeeld `true`: `endswith('hello, world', 'world')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die de waarde kan bevatten.<br /><br /> **Parameter nummer**: 2<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De waarde die de teken reeks kan eindigen met.|  
|split|Hiermee wordt de teken reeks gesplitst met een scheidings teken. De volgende expressie retourneert bijvoorbeeld `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die wordt gesplitst.<br /><br /> **Parameter nummer**: 2<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. Het scheidings teken.|  
  
  
## <a name="collection-functions"></a>Verzamelings functies  
 Deze functies worden toegepast op verzamelingen zoals matrices, teken reeksen en soms woorden lijsten.  
  
|Functie naam|Beschrijving|  
|-------------------|-----------------|  
|daarin|Retourneert True als de woorden lijst een sleutel bevat, de List bevat een waarde of een teken reeks bevat een subtekenreeks. De volgende expressie retourneert bijvoorbeeld `true:``contains('abacaba','aca')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: binnen verzameling<br /><br /> **Beschrijving**: vereist. De verzameling waarnaar moet worden gezocht.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: object zoeken<br /><br /> **Beschrijving**: vereist. Het object dat moet worden gevonden in de **verzameling**.|  
|lange|Retourneert het aantal elementen in een matrix of teken reeks. De volgende expressie retourneert bijvoorbeeld `3`: `length('abc')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: verzameling<br /><br /> **Beschrijving**: vereist. De verzameling waarvan de lengte moet worden opgehaald.|  
|gelaten|Retourneert waar als object, matrix of teken reeks leeg is. De volgende expressie retourneert bijvoorbeeld `true`:<br /><br /> `empty('')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: verzameling<br /><br /> **Beschrijving**: vereist. De verzameling die moet worden gecontroleerd of deze leeg is.|  
|Snij punt|Retourneert een enkele matrix of een object met de gemeen schappelijke elementen tussen de matrices of objecten die hieraan zijn door gegeven. Deze functie retourneert bijvoorbeeld `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> De para meters voor de functie kunnen bestaan uit een set objecten of een set matrices (geen mengsel daarvan). Als er twee objecten met dezelfde naam zijn, wordt het laatste object met die naam weer gegeven in het laatste object.<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Name**: verzameling *n*<br /><br /> **Beschrijving**: vereist. De verzamelingen die moeten worden geëvalueerd. Er moet een object zijn in alle verzamelingen die worden door gegeven om weer te geven in het resultaat.|  
|Réunion|Retourneert een enkele matrix of een object met alle elementen in een matrix of object dat hieraan is door gegeven. Deze functie retourneert bijvoorbeeld `[1, 2, 3, 10, 101]:`<br /><br /> : `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> De para meters voor de functie kunnen bestaan uit een set objecten of een set matrices (geen mengsel daarvan). Als er twee objecten zijn met dezelfde naam in de laatste uitvoer, wordt het laatste object met die naam weer gegeven in het laatste object.<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Name**: verzameling *n*<br /><br /> **Beschrijving**: vereist. De verzamelingen die moeten worden geëvalueerd. Een object dat wordt weer gegeven in een van de verzamelingen, wordt weer gegeven in het resultaat.|  
|instantie|Retourneert het eerste element in de matrix of teken reeks die is door gegeven. Deze functie retourneert bijvoorbeeld `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: verzameling<br /><br /> **Beschrijving**: vereist. De verzameling waaruit het eerste object moet worden opgehaald.|  
|Duren|Retourneert het laatste element in de matrix of teken reeks die is door gegeven. Deze functie retourneert bijvoorbeeld `3`:<br /><br /> `last('0123')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: verzameling<br /><br /> **Beschrijving**: vereist. De verzameling waaruit het laatste object moet worden opgehaald.|  
|Houd|Retourneert de eerste **Count** -elementen uit de door gegeven matrix of teken reeks, bijvoorbeeld retourneert deze functie `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: verzameling<br /><br /> **Beschrijving**: vereist. De verzameling waaruit het eerste **aantal** objecten moet worden opgehaald.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: aantal<br /><br /> **Beschrijving**: vereist. Het aantal objecten dat uit de verzameling moet worden **opgehaald**. Moet een positief geheel getal zijn.|  
|verdergaan|Retourneert de elementen in de matrix die beginnen bij de index **telling**, bijvoorbeeld deze functie retourneert `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: verzameling<br /><br /> **Beschrijving**: vereist. De verzameling waarnaar de eerste **telling** objecten moeten worden overgeslagen.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: aantal<br /><br /> **Beschrijving**: vereist. Het aantal objecten dat aan de voor zijde van de **verzameling**moet worden verwijderd. Moet een positief geheel getal zijn.|  
  
## <a name="logical-functions"></a>Logische functies  
 Deze functies zijn handig in voor waarden en kunnen worden gebruikt om elk type logica te evalueren.  
  
|Functie naam|Beschrijving|  
|-------------------|-----------------|  
|gelijk is aan|Retourneert waar als twee waarden gelijk zijn. Als parameter1 bijvoorbeeld Foo is, retourneert de volgende expressie `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: object 1<br /><br /> **Beschrijving**: vereist. Het object dat moet worden vergeleken met **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: object 2<br /><br /> **Beschrijving**: vereist. Het object dat moet worden vergeleken met **object 1**.|  
|jonge|Retourneert waar als het eerste argument kleiner is dan de seconde. Let op: waarden kunnen alleen van het type integer, float of string zijn. De volgende expressie retourneert bijvoorbeeld `true`: `less(10,100)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: object 1<br /><br /> **Beschrijving**: vereist. Het object dat moet worden gecontroleerd als het kleiner is dan **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: object 2<br /><br /> **Beschrijving**: vereist. Het object dat moet worden gecontroleerd of het groter is dan **object 1**.|  
|lessOrEquals|Retourneert waar als het eerste argument kleiner dan of gelijk aan de seconde is. Let op: waarden kunnen alleen van het type integer, float of string zijn. De volgende expressie retourneert bijvoorbeeld `true`: `lessOrEquals(10,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: object 1<br /><br /> **Beschrijving**: vereist. Het object dat moet worden gecontroleerd als het kleiner is dan of gelijk is aan **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: object 2<br /><br /> **Beschrijving**: vereist. Het object dat moet worden gecontroleerd als dit groter is dan of gelijk is aan **object 1**.|  
|groter|Retourneert waar als het eerste argument groter is dan de seconde. Let op: waarden kunnen alleen van het type integer, float of string zijn. De volgende expressie retourneert bijvoorbeeld `false`: `greater(10,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: object 1<br /><br /> **Beschrijving**: vereist. Het object waarop moet worden gecontroleerd of het groter is dan **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: object 2<br /><br /> **Beschrijving**: vereist. Het object dat moet worden gecontroleerd als het kleiner is dan **object 1**.|  
|greaterOrEquals|Retourneert waar als het eerste argument groter is dan of gelijk is aan de seconde. Let op: waarden kunnen alleen van het type integer, float of string zijn. De volgende expressie retourneert bijvoorbeeld `false`: `greaterOrEquals(10,100)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: object 1<br /><br /> **Beschrijving**: vereist. Het object dat moet worden gecontroleerd als het groter is dan of gelijk is aan **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: object 2<br /><br /> **Beschrijving**: vereist. Het object dat moet worden gecontroleerd als het kleiner is dan of gelijk is aan **object 1**.|  
|en de|Retourneert waar als beide para meters waar zijn. Beide argumenten moeten Booleaanse waarden hebben. De volgende retourneert `false`: `and(greater(1,10),equals(0,0))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Boole 1<br /><br /> **Beschrijving**: vereist. Het eerste argument dat moet worden `true`.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Boole 2<br /><br /> **Beschrijving**: vereist. Het tweede argument moet `true` zijn.|  
|of|Retourneert waar als een van de para meters waar is. Beide argumenten moeten Booleaanse waarden hebben. De volgende retourneert `true`: `or(greater(1,10),equals(0,0))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Boole 1<br /><br /> **Beschrijving**: vereist. Het eerste argument dat kan worden `true`.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Boole 2<br /><br /> **Beschrijving**: vereist. Het tweede argument kan worden `true`.|  
|ten|Retourneert waar als de para meter `false` is. Beide argumenten moeten Booleaanse waarden hebben. De volgende retourneert `true`: `not(contains('200 Success','Fail'))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Booleaans<br /><br /> **Beschrijving**: retourneert waar als de para meter `false` is. Beide argumenten moeten Booleaanse waarden hebben. De volgende retourneert `true`: `not(contains('200 Success','Fail'))`|  
|if|Retourneert een opgegeven waarde op basis van als de expressie resulteert in `true` of `false`.  Het volgende retourneert bijvoorbeeld `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: Expression<br /><br /> **Beschrijving**: vereist. Een Booleaanse waarde die bepaalt welke waarde door de expressie wordt geretourneerd.<br /><br /> **Parameter nummer**: 2<br /><br /> **Name**: True<br /><br /> **Beschrijving**: vereist. De waarde die moet worden geretourneerd als de expressie `true` is.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: onwaar<br /><br /> **Beschrijving**: vereist. De waarde die moet worden geretourneerd als de expressie `false` is.|  
  
## <a name="conversion-functions"></a>Conversie functies  
 Deze functies worden gebruikt om te converteren tussen de systeem eigen typen in de taal:  
  
-   string  
  
-   geheel getal  
  
-   float  
  
-   booleaans  
  
-   matrices  
  
-   woorden lijsten  
  
|Functie naam|Beschrijving|  
|-------------------|-----------------|  
|int|Zet de para meter om in een geheel getal. De volgende expressie retourneert bijvoorbeeld 100 als een getal in plaats van een teken reeks: `int('100')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: waarde<br /><br /> **Beschrijving**: vereist. De waarde die wordt geconverteerd naar een geheel getal.|  
|string|Zet de para meter om in een teken reeks. De volgende expressie retourneert bijvoorbeeld `'10'`: `string(10)` u een object ook kunt converteren naar een teken reeks, bijvoorbeeld als de **Foo** -para meter een object met één eigenschap is `bar : baz`, dan retourneert het volgende `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: waarde<br /><br /> **Beschrijving**: vereist. De waarde die wordt geconverteerd naar een teken reeks.|  
|json|Converteer de para meter naar een JSON-type waarde. Dit is het tegenovergestelde van string (). De volgende expressie retourneert bijvoorbeeld `[1,2,3]` als een matrix, in plaats van een teken reeks:<br /><br /> `json('[1,2,3]')`<br /><br /> Op dezelfde manier kunt u een teken reeks naar een object converteren. @No__t_0 retourneert bijvoorbeeld:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks die wordt geconverteerd naar een systeem eigen type waarde.<br /><br /> De json-functie ondersteunt ook XML-invoer. Bijvoorbeeld, de parameter waarde van:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> wordt geconverteerd naar de volgende JSON:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Converteer het parameter argument naar een drijvende-komma getal. De volgende expressie retourneert bijvoorbeeld `10.333`: `float('10.333')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: waarde<br /><br /> **Beschrijving**: vereist. De waarde die wordt geconverteerd naar een getal met een drijvende komma.|  
|bool|Converteer de para meter naar een Booleaanse waarde. De volgende expressie retourneert bijvoorbeeld `false`: `bool(0)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: waarde<br /><br /> **Beschrijving**: vereist. De waarde die wordt geconverteerd naar een Boole.|  
|Voeg|Retourneert het eerste niet-null-object in de argumenten die zijn door gegeven in. Opmerking: een lege teken reeks is niet null. Als de para meters 1 en 2 bijvoorbeeld niet zijn gedefinieerd, retourneert dit `fallback`: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Naam**: object*n*<br /><br /> **Beschrijving**: vereist. De objecten waarop moet worden gecontroleerd op `null`.|  
|base64|Retourneert de base64-weer gave van de invoer teken reeks. De volgende expressie retourneert bijvoorbeeld `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: teken reeks 1<br /><br /> **Beschrijving**: vereist. De teken reeks die moet worden gecodeerd in Base64-weer gave.|  
|base64ToBinary|Retourneert een binaire weer gave van een base64-gecodeerde teken reeks. De volgende expressie retourneert bijvoorbeeld de binaire weer gave van een teken reeks: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De base64-gecodeerde teken reeks.|  
|base64ToString|Retourneert een teken reeks representatie van een teken reeks met based64-code ring. De volgende expressie retourneert bijvoorbeeld een teken reeks: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De base64-gecodeerde teken reeks.|  
|waarde|Retourneert een binaire weer gave van een waarde.  De volgende expressie retourneert bijvoorbeeld een binaire weer gave van een teken reeks: `binary(‘some string’).`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: waarde<br /><br /> **Beschrijving**: vereist. De waarde die is geconverteerd naar binair.|  
|dataUriToBinary|Retourneert een binaire weer gave van een gegevens-URI. De volgende expressie retourneert bijvoorbeeld de binaire weer gave van een teken reeks: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De gegevens-URI die moet worden geconverteerd naar de binaire weer gave.|  
|dataUriToString|Retourneert een teken reeks representatie van een gegevens-URI. De volgende expressie retourneert bijvoorbeeld een teken reeks: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br />**Beschrijving**: vereist. De gegevens-URI die moet worden geconverteerd naar een teken reeks weergave.|  
|dataUri|Retourneert een gegevens-URI van een waarde. De volgende expressie retourneert bijvoorbeeld gegevens: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Parameter nummer**: 1<br /><br />**Naam**: waarde<br /><br />**Beschrijving**: vereist. De waarde die moet worden geconverteerd naar de gegevens-URI.|  
|decodeBase64|Retourneert een teken reeks weergave van een invoer based64 teken reeks. De volgende expressie retourneert bijvoorbeeld `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: retourneert een teken reeks weergave van een invoer based64 teken reeks.|  
|encodeUriComponent|URL: verstuurt de teken reeks die wordt door gegeven in. De volgende expressie retourneert bijvoorbeeld `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks waarmee URL-onveilige tekens van worden teruggestuurd.|  
|decodeUriComponent|Un-URL: de teken reeks die wordt door gegeven, wordt in de-de opgegeven waarde verescapet. De volgende expressie retourneert bijvoorbeeld `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De teken reeks waaruit de URL-onveilige tekens moeten worden gedecodeerd.|  
|decodeDataUri|Retourneert een binaire representatie van een invoer gegevens-URI-teken reeks. De volgende expressie retourneert bijvoorbeeld de binaire weer gave van `some string`: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: vereist. De dataURI die moet worden gedecodeerd in een binaire weer gave.|  
|uriComponent|Retourneert een URI-gecodeerde representatie van een waarde. De volgende expressie retourneert bijvoorbeeld `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Parameter Details: nummer: 1, naam: teken reeks, beschrijving: vereist. De teken reeks die moet worden gecodeerd met een URI.|  
|uriComponentToBinary|Retourneert een binaire weer gave van een gecodeerde URI-teken reeks. De volgende expressie retourneert bijvoorbeeld een binaire weer gave van `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br />**Beschrijving**: vereist. De teken reeks met URI-code ring.|  
|uriComponentToString|Retourneert een teken reeks representatie van een teken reeks met URI-code ring. De volgende expressie retourneert bijvoorbeeld `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br />**Name**: teken reeks<br /><br />**Beschrijving**: vereist. De teken reeks met URI-code ring.|  
|xml|Retourneert een XML-weer gave van de waarde. De volgende expressie retourneert bijvoorbeeld een XML-inhoud die wordt vertegenwoordigd door `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. De functie XML ondersteunt ook JSON-object invoer. De para meter `{ "abc": "xyz" }` bijvoorbeeld wordt geconverteerd naar een XML-inhoud `\<abc>xyz\</abc>`<br /><br /> **Parameter nummer**: 1<br /><br />**Naam**: waarde<br /><br />**Beschrijving**: vereist. De waarde die moet worden geconverteerd naar XML.|  
|XPath|Retourneert een matrix van XML-knoop punten die overeenkomen met de XPath-expressie van een waarde die door de XPath-expressie wordt geëvalueerd.<br /><br />  **Voor beeld 1**<br /><br /> Stel dat de waarde van de para meter P1 een teken reeks representatie van de volgende XML is:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. deze code: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> retourneert<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> maar<br /><br /> 2. deze code: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> retourneert<br /><br /> `13`<br /><br /> <br /><br /> **Voor beeld 2**<br /><br /> Op basis van de volgende XML-inhoud:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1. deze code: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> of<br /><br /> 2. deze code: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Retourneert<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> en de<br /><br /> 3. deze code: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Retourneert<br /><br /> ``bar``<br /><br /> **Parameter nummer**: 1<br /><br />**Naam**: XML<br /><br />**Beschrijving**: vereist. De XML waarop de XPath-expressie moet worden geëvalueerd.<br /><br /> **Parameter nummer**: 2<br /><br />**Naam**: XPath<br /><br />**Beschrijving**: vereist. De XPath-expressie die moet worden geëvalueerd.|  
|matrix|De para meter naar een matrix Converteren.  De volgende expressie retourneert bijvoorbeeld `["abc"]`: `array('abc')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: waarde<br /><br /> **Beschrijving**: vereist. De waarde die wordt omgezet in een matrix.|
|createArray|Hiermee wordt een matrix gemaakt op basis van de para meters.  De volgende expressie retourneert bijvoorbeeld `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Parameter nummer**: 1... nvt<br /><br /> **Naam**: elke n<br /><br /> **Beschrijving**: vereist. De waarden die in een matrix moeten worden gecombineerd.|

## <a name="math-functions"></a>Wiskundige functies  
 Deze functies kunnen worden gebruikt voor beide typen getallen: **gehele** getallen en **zwevende**waarden.  
  
|Functie naam|Beschrijving|  
|-------------------|-----------------|  
|add|Retourneert het resultaat van de toevoeging van de twee getallen. Deze functie retourneert bijvoorbeeld `20.333`: `add(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Summand 1<br /><br /> **Beschrijving**: vereist. Het getal dat moet worden toegevoegd aan **Summand 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Summand 2<br /><br /> **Beschrijving**: vereist. Het getal dat moet worden toegevoegd aan **Summand 1**.|  
|chronische|Retourneert het resultaat van de aftrekking van de twee getallen. Deze functie retourneert bijvoorbeeld: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: aftrek getal<br /><br /> **Beschrijving**: vereist. Het getal waarvan **aftrekker** is verwijderd.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: aftrekker<br /><br /> **Beschrijving**: vereist. Het getal dat moet worden verwijderd uit de **aftrek getal**.|  
|mul|Retourneert het resultaat van de vermenigvuldiging van de twee getallen. Het volgende retourneert bijvoorbeeld `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Multiplicand 1<br /><br /> **Beschrijving**: vereist. Het getal waarmee **Multiplicand 2** moet worden vermenigvuldigd met.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Multiplicand 2<br /><br /> **Beschrijving**: vereist. Het getal om **Multiplicand 1** te vermenigvuldigen met.|  
|div|Retourneert het resultaat van de deling van de twee getallen. Het volgende retourneert bijvoorbeeld `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: deeltal<br /><br /> **Beschrijving**: vereist. Het getal dat moet worden gedeeld door de **deler**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: deler<br /><br /> **Beschrijving**: vereist. Het getal waarop het **dividend** moet worden verdeeld.|  
|mod|Retourneert het resultaat van het rest getal na de deling van de twee getallen (modulo). De volgende expressie retourneert bijvoorbeeld `2`:<br /><br /> `mod(10,4)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: deeltal<br /><br /> **Beschrijving**: vereist. Het getal dat moet worden gedeeld door de **deler**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: deler<br /><br /> **Beschrijving**: vereist. Het getal waarop het **dividend** moet worden verdeeld. Na de deling wordt de rest genomen.|  
|min.|Er zijn twee verschillende patronen voor het aanroepen van deze functie: `min([0,1,2])` hier min. er wordt een matrix gebruikt. Deze expressie retourneert `0`. U kunt deze functie ook gebruiken om een lijst met door komma's gescheiden waarden te maken: `min(0,1,2)` deze functie ook 0 retourneert. Houd er rekening mee dat alle waarden getallen moeten zijn, dus als de para meter een matrix is, mag deze alleen getallen bevatten.<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: verzameling of waarde<br /><br /> **Beschrijving**: vereist. Het kan een matrix met waarden zijn om de minimum waarde te vinden, of de eerste waarde van een set.<br /><br /> **Parameter nummer**: 2... *n*<br /><br /> **Naam**: waarde *n*<br /><br /> **Beschrijving**: optioneel. Als de eerste para meter een waarde is, kunt u aanvullende waarden door geven en wordt het minimum van alle door gegeven waarden geretourneerd.|  
|aantal|Er zijn twee verschillende patronen voor het aanroepen van deze functie: `max([0,1,2])`<br /><br /> Hier is Max een matrix. Deze expressie retourneert `2`. U kunt deze functie ook gebruiken om een lijst met door komma's gescheiden waarden te maken: `max(0,1,2)` deze functie retourneert 2. Houd er rekening mee dat alle waarden getallen moeten zijn, dus als de para meter een matrix is, mag deze alleen getallen bevatten.<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: verzameling of waarde<br /><br /> **Beschrijving**: vereist. Het kan een matrix met waarden zijn om de maximum waarde te vinden, of de eerste waarde van een set.<br /><br /> **Parameter nummer**: 2... *n*<br /><br /> **Naam**: waarde *n*<br /><br /> **Beschrijving**: optioneel. Als de eerste para meter een waarde is, kunt u aanvullende waarden door geven en wordt het maximum van alle door gegeven waarden geretourneerd.|  
|bereik| Genereert een matrix van gehele getallen vanaf een bepaald getal en definieert de lengte van de geretourneerde matrix. Deze functie retourneert bijvoorbeeld `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: start index<br /><br /> **Beschrijving**: vereist. Het is het eerste geheel getal in de matrix.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: aantal<br /><br /> **Beschrijving**: vereist. Aantal gehele getallen in de matrix.|  
|ASELECT| Hiermee wordt een wille keurig geheel getal binnen het opgegeven bereik gegenereerd (inclusief op beide einden. Dit kan bijvoorbeeld `42` retour neren:<br /><br /> `rand(-1000,1000)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: minimum<br /><br /> **Beschrijving**: vereist. Het kleinste gehele getal dat kan worden geretourneerd.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: maximum<br /><br /> **Beschrijving**: vereist. Het hoogste gehele getal dat kan worden geretourneerd.|  
  
## <a name="date-functions"></a>Datum functies  
  
|Functie naam|Beschrijving|  
|-------------------|-----------------|  
|utcnow|Retourneert de huidige tijds tempel als een teken reeks. Bijvoorbeeld `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: indeling<br /><br /> **Beschrijving**: optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|addseconds|Hiermee wordt een geheel getal van seconden toegevoegd aan een teken reeks tijds tempel dat wordt door gegeven. Het aantal seconden kan positief of negatief zijn. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: tijds tempel<br /><br /> **Beschrijving**: vereist. Een teken reeks die de tijd bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: seconden<br /><br /> **Beschrijving**: vereist. Het aantal seconden dat moet worden toegevoegd. Kan negatief zijn om seconden af te trekken.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: indeling<br /><br /> **Beschrijving**: optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|addminutes|Hiermee wordt een geheel getal van minuten toegevoegd aan een teken reeks tijds tempel dat wordt door gegeven. Het aantal minuten kan positief of negatief zijn. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: tijds tempel<br /><br /> **Beschrijving**: vereist. Een teken reeks die de tijd bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: minuten<br /><br /> **Beschrijving**: vereist. Het aantal minuten dat moet worden toegevoegd. Kan negatief zijn om minuten af te trekken.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: indeling<br /><br /> **Beschrijving**: optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|addhours|Voegt een geheel getal van uren toe aan een teken reeks tijds tempel dat wordt door gegeven. Het aantal uren kan positief of negatief zijn. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: tijds tempel<br /><br /> **Beschrijving**: vereist. Een teken reeks die de tijd bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: uren<br /><br /> **Beschrijving**: vereist. Het aantal uren dat moet worden toegevoegd. Kan negatief zijn om uren af te trekken.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: indeling<br /><br /> **Beschrijving**: optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|addDays|Hiermee wordt een geheel getal van dagen toegevoegd aan een teken reeks tijds tempel dat wordt door gegeven. Het aantal dagen kan positief of negatief zijn. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: tijds tempel<br /><br /> **Beschrijving**: vereist. Een teken reeks die de tijd bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: dagen<br /><br /> **Beschrijving**: vereist. Het aantal dagen dat moet worden toegevoegd. Kan negatief zijn om dagen af te trekken.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: indeling<br /><br /> **Beschrijving**: optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|formatDateTime|Retourneert een teken reeks in de datum notatie. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br />Gebruik formatDateTime (utcnow (), JJJJ/MM/DD) om een datum in te delen in JJJJ/MM/DD.</br>Als u een naam wilt toevoegen aan de datum, gebruikt u @concat (' foo-', '/', formatDateTime (utcnow (), ' JJJJ/MM/DD ').<br><br> **Parameter nummer**: 1<br /><br /> **Naam**: datum<br /><br /> **Beschrijving**: vereist. Een teken reeks die de datum bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: indeling<br /><br /> **Beschrijving**: optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt. |  

## <a name="next-steps"></a>Volgende stappen
Zie [systeem variabelen](control-flow-system-variables.md)voor een lijst met systeem variabelen die u in expressies kunt gebruiken.
