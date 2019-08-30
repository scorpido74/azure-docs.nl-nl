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
ms.openlocfilehash: c500c4b2d35a449a9f9c0f693a02c008ea6e3c5e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141698"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressies en functies in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-functions-variables.md)
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
Expressies kunnen ergens in een JSON-teken reeks waarde worden weer gegeven en hebben altijd een andere JSON-waarde. Als een JSON-waarde een expressie is, de hoofdtekst van de expressie wordt opgehaald door het verwijderen van het apenstaartje (\@). Als een letterlijke tekenreeks is die met begint nodig \@, deze moet worden weergegeven met behulp van \@ \@. In de volgende voor beelden ziet u hoe expressies worden geëvalueerd.  
  
|JSON-waarde|Resultaat|  
|----------------|------------|  
|instellen|De tekens ' para meters ' worden geretourneerd.|  
|"para meters [1]"|De tekens ' para meters [1] ' worden geretourneerd.|  
|"\@\@"|Een tekenreeks zijn 1 teken met '\@' wordt geretourneerd.|  
|" \@"|Een 2-tekenreeks met ' \@' wordt geretourneerd.|  
  
 Expressies kan ook verschijnen in tekenreeksen met de functie met de naam *tekenreeks interpolatie* waarbij expressies zijn verpakt in `@{ ... }`. Bijvoorbeeld: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Als u de teken reeks interpolatie gebruikt, is het resultaat altijd een teken reeks. Stel dat ik is `myNumber` gedefinieerd `42` als `myString` en `foo`als:  
  
|JSON-waarde|Resultaat|  
|----------------|------------|  
|"\@pijp lijn (). para meters. MyString"| Retourneert `foo` als een teken reeks.|  
|"\@{pipeline ()-.parameters.myString}"| Retourneert `foo` als een teken reeks.|  
|"\@pipeline ()-.parameters.myNumber"| Retourneert `42` als een *getal*.|  
|"\@{pipeline (). para meters. myNumber}"| Retourneert `42` als een *teken reeks*.|  
|"Het antwoord is: @{pipeline ()-.parameters.myNumber}"| Retourneert de teken `Answer is: 42`reeks.|  
|'\@concat (' antwoord is: ', String (pijp lijn (). para meters. myNumber)) '| Retourneert de teken reeks`Answer is: 42`|  
|"Het antwoord is: \@ \@{pipeline ()-.parameters.myNumber}"| Retourneert de teken `Answer is: @{pipeline().parameters.myNumber}`reeks.|  
  
### <a name="examples"></a>Voorbeelden

#### <a name="a-dataset-with-a-parameter"></a>Een gegevensset met een para meter
In het volgende voor beeld gebruikt de BlobDataset een para meter met de naam **Path**. De waarde wordt gebruikt om een waarde voor de eigenschap **FolderPath** in te stellen met behulp `dataset().path`van de expressie:. 

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
In het volgende voor beeld worden de para meters **inputPath** en **outputPath** gebruikt voor de pijp lijn. Het **pad** voor de geparametriseerde BLOB-gegevensset wordt ingesteld met behulp van de waarden van deze para meters. De syntaxis die hier wordt gebruikt `pipeline().parameters.parametername`, is:. 

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
  
|Functienaam|Description|  
|-------------------|-----------------|  
|concat|Elk wille keurig aantal teken reeksen combi neren. Als parameter1 `foo,` bijvoorbeeld de volgende expressie retourneert `somevalue-foo-somevalue`:`concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Naam**: Teken reeks *n*<br /><br /> **Beschrijving**: Vereist. De teken reeksen die moeten worden gecombineerd tot één teken reeks.|  
|subtekenreeks|Retourneert een subset tekens uit een teken reeks. U kunt bijvoorbeeld de volgende expressie:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> retourneert<br /><br /> `foo`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks waaruit de subtekenreeks wordt opgehaald.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Index starten<br /><br /> **Beschrijving**: Vereist. De index van waar de subtekenreeks begint in para meter 1. Start index is gebaseerd op nul. <br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: Hoogte<br /><br /> **Beschrijving**: Vereist. De lengte van de subtekenreeks.|  
|replace|Vervangt een teken reeks door een opgegeven teken reeks. De expressie bijvoorbeeld:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> retourneert<br /><br /> `the new string`<br /><br /> **Parameter nummer**: 1<br /><br /> **Name**: teken reeks<br /><br /> **Beschrijving**: Vereist.  Als para meter 2 is gevonden in para meter 1, de teken reeks die wordt doorzocht op para meter 2 en bijgewerkt met para meter 3.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Oude teken reeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die moet worden vervangen door para meter 3 wanneer een overeenkomst wordt gevonden in para meter 1<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: Nieuwe teken reeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die wordt gebruikt om de teken reeks in para meter 2 te vervangen wanneer een overeenkomst wordt gevonden in para meter 1.|  
|guid| Genereert een wereld wijd unieke teken reeks (ook wel. GUID). De volgende uitvoer kan bijvoorbeeld worden gegenereerd `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Indeling<br /><br /> **Beschrijving**: Optioneel. Een instructie met één indeling die aangeeft [hoe de waarde van deze GUID moet worden ingedeeld](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). De notatie parameter kan "N", "D", "B", "P" of "X" zijn. Als de indeling niet is ingesteld, wordt D gebruikt.|  
|toLower|Zet een teken reeks om in kleine letters. Zo wordt bijvoorbeeld het volgende geretourneerd `two by two is four`:`toLower('Two by Two is Four')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die moet worden geconverteerd naar een lager hoofdletter gebruik. Als een teken in de teken reeks geen kleine letters bevat, wordt deze ongewijzigd opgenomen in de geretourneerde teken reeks.|  
|toUpper|Converteert een teken reeks naar hoofd letters. De volgende expressie retourneert `TWO BY TWO IS FOUR`bijvoorbeeld:`toUpper('Two by Two is Four')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die moet worden geconverteerd naar een bovenste behuizing. Als een teken in de teken reeks geen hoofd letter-equivalent heeft, wordt deze ongewijzigd opgenomen in de geretourneerde teken reeks.|  
|indexof|De index van een waarde binnen een teken reeks zoeken die niet hoofdletter gevoelig is. Index is gebaseerd op nul. De volgende expressie retourneert `7`bijvoorbeeld:`indexof('hello, world.', 'world')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die de waarde kan bevatten.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De waarde waarnaar moet worden gezocht in de index van.|  
|lastindexof|De laatste index van een waarde binnen een teken reeks zoeken die niet hoofdletter gevoelig is. Index is gebaseerd op nul. De volgende expressie retourneert `3`bijvoorbeeld:`lastindexof('foofoo', 'foo')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die de waarde kan bevatten.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De waarde waarnaar moet worden gezocht in de index van.|  
|startsWith|Hiermee wordt gecontroleerd of de teken reeks begint met een waarde die ongevoelig is. De volgende expressie retourneert `true`bijvoorbeeld:`startswith('hello, world', 'hello')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die de waarde kan bevatten.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De waarde waarmee de teken reeks kan beginnen.|  
|endswith|Controleert of de teken reeks eindigt met een waarde die niet hoofdletter gevoelig is. De volgende expressie retourneert `true`bijvoorbeeld:`endswith('hello, world', 'world')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die de waarde kan bevatten.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De waarde die de teken reeks kan eindigen met.|  
|split|Hiermee wordt de teken reeks gesplitst met een scheidings teken. De volgende expressie retourneert `["a", "b", "c"]`bijvoorbeeld:`split('a;b;c',';')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die wordt gesplitst.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. Het scheidings teken.|  
  
  
## <a name="collection-functions"></a>Verzamelings functies  
 Deze functies worden toegepast op verzamelingen zoals matrices, teken reeksen en soms woorden lijsten.  
  
|Functienaam|Description|  
|-------------------|-----------------|  
|bevat|Retourneert True als de woorden lijst een sleutel bevat, de List bevat een waarde of een teken reeks bevat een subtekenreeks. De volgende expressie retourneert bijvoorbeeld`true:``contains('abacaba','aca')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Binnen verzameling<br /><br /> **Beschrijving**: Vereist. De verzameling waarnaar moet worden gezocht.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Object zoeken<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden gevonden in de **verzameling**.|  
|length|Retourneert het aantal elementen in een matrix of teken reeks. De volgende expressie retourneert `3`bijvoorbeeld:`length('abc')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Collection<br /><br /> **Beschrijving**: Vereist. De verzameling waarvan de lengte moet worden opgehaald.|  
|Leeg|Retourneert waar als object, matrix of teken reeks leeg is. De volgende expressie retourneert `true`bijvoorbeeld:<br /><br /> `empty('')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Collection<br /><br /> **Beschrijving**: Vereist. De verzameling die moet worden gecontroleerd of deze leeg is.|  
|Snij punt|Retourneert een enkele matrix of een object met de gemeen schappelijke elementen tussen de matrices of objecten die hieraan zijn door gegeven. Deze functie retourneert `[1, 2]`bijvoorbeeld:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> De para meters voor de functie kunnen bestaan uit een set objecten of een set matrices (geen mengsel daarvan). Als er twee objecten met dezelfde naam zijn, wordt het laatste object met die naam weer gegeven in het laatste object.<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Naam**: Verzameling *n*<br /><br /> **Beschrijving**: Vereist. De verzamelingen die moeten worden geëvalueerd. Er moet een object zijn in alle verzamelingen die worden door gegeven om weer te geven in het resultaat.|  
|union|Retourneert een enkele matrix of een object met alle elementen in een matrix of object dat hieraan is door gegeven. Deze functie retourneert bijvoorbeeld`[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> De para meters voor de functie kunnen bestaan uit een set objecten of een set matrices (geen mengsel daarvan). Als er twee objecten zijn met dezelfde naam in de laatste uitvoer, wordt het laatste object met die naam weer gegeven in het laatste object.<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Naam**: Verzameling *n*<br /><br /> **Beschrijving**: Vereist. De verzamelingen die moeten worden geëvalueerd. Een object dat wordt weer gegeven in een van de verzamelingen, wordt weer gegeven in het resultaat.|  
|instantie|Retourneert het eerste element in de matrix of teken reeks die is door gegeven. Deze functie retourneert `0`bijvoorbeeld:<br /><br /> `first([0,2,3])`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Collection<br /><br /> **Beschrijving**: Vereist. De verzameling waaruit het eerste object moet worden opgehaald.|  
|duren|Retourneert het laatste element in de matrix of teken reeks die is door gegeven. Deze functie retourneert `3`bijvoorbeeld:<br /><br /> `last('0123')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Collection<br /><br /> **Beschrijving**: Vereist. De verzameling waaruit het laatste object moet worden opgehaald.|  
|Houd|Retourneert het eerste **aantal** elementen uit de door gegeven matrix of teken reeks, bijvoorbeeld retourneert `[1, 2]`deze functie:`take([1, 2, 3, 4], 2)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Collection<br /><br /> **Beschrijving**: Vereist. De verzameling waaruit het eerste **aantal** objecten moet worden opgehaald.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Count<br /><br /> **Beschrijving**: Vereist. Het aantal objecten dat uit de verzameling moet worden **opgehaald**. Moet een positief geheel getal zijn.|  
|overslaan|Retourneert de elementen in de matrix, te beginnen bij index **telling**, bijvoorbeeld als resultaat `[3, 4]`van deze functie:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Collection<br /><br /> **Beschrijving**: Vereist. De verzameling waarnaar de eerste **telling** objecten moeten worden overgeslagen.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Count<br /><br /> **Beschrijving**: Vereist. Het aantal objecten dat aan de voor zijde van de **verzameling**moet worden verwijderd. Moet een positief geheel getal zijn.|  
  
## <a name="logical-functions"></a>Logische functies  
 Deze functies zijn handig in voor waarden en kunnen worden gebruikt om elk type logica te evalueren.  
  
|Functienaam|Description|  
|-------------------|-----------------|  
|equals|Retourneert waar als twee waarden gelijk zijn. Als parameter1 bijvoorbeeld Foo is, retourneert `true`de volgende expressie:`equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Object 1<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden vergeleken met **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Object 2<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden vergeleken met **object 1**.|  
|minder|Retourneert waar als het eerste argument kleiner is dan de seconde. Let op: waarden kunnen alleen van het type integer, float of string zijn. De volgende expressie retourneert `true`bijvoorbeeld:`less(10,100)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Object 1<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden gecontroleerd als het kleiner is dan **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Object 2<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden gecontroleerd of het groter is dan **object 1**.|  
|lessOrEquals|Retourneert waar als het eerste argument kleiner dan of gelijk aan de seconde is. Let op: waarden kunnen alleen van het type integer, float of string zijn. De volgende expressie retourneert `true`bijvoorbeeld:`lessOrEquals(10,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Object 1<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden gecontroleerd als het kleiner is dan of gelijk is aan **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Object 2<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden gecontroleerd als dit groter is dan of gelijk is aan **object 1**.|  
|greater|Retourneert waar als het eerste argument groter is dan de seconde. Let op: waarden kunnen alleen van het type integer, float of string zijn. De volgende expressie retourneert `false`bijvoorbeeld:`greater(10,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Object 1<br /><br /> **Beschrijving**: Vereist. Het object waarop moet worden gecontroleerd of het groter is dan **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Object 2<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden gecontroleerd als het kleiner is dan **object 1**.|  
|greaterOrEquals|Retourneert waar als het eerste argument groter is dan of gelijk is aan de seconde. Let op: waarden kunnen alleen van het type integer, float of string zijn. De volgende expressie retourneert `false`bijvoorbeeld:`greaterOrEquals(10,100)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Object 1<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden gecontroleerd als het groter is dan of gelijk is aan **object 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Object 2<br /><br /> **Beschrijving**: Vereist. Het object dat moet worden gecontroleerd als het kleiner is dan of gelijk is aan **object 1**.|  
|and|Retourneert waar als beide para meters waar zijn. Beide argumenten moeten Booleaanse waarden hebben. De volgende retour `false`waarde is:`and(greater(1,10),equals(0,0))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Booleaanse waarde 1<br /><br /> **Beschrijving**: Vereist. Het eerste argument dat moet worden `true`.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Booleaanse waarde 2<br /><br /> **Beschrijving**: Vereist. Het tweede argument moet zijn `true`.|  
|of|Retourneert waar als een van de para meters waar is. Beide argumenten moeten Booleaanse waarden hebben. De volgende retour `true`waarde is:`or(greater(1,10),equals(0,0))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Booleaanse waarde 1<br /><br /> **Beschrijving**: Vereist. Het eerste argument dat mogelijk is `true`.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Booleaanse waarde 2<br /><br /> **Beschrijving**: Vereist. Het tweede argument kan zijn `true`.|  
|not|Retourneert waar als de para meter `false`is. Beide argumenten moeten Booleaanse waarden hebben. De volgende retour `true`waarde is:`not(contains('200 Success','Fail'))`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Boolean-waarde<br /><br /> **Beschrijving**: Retourneert waar als de para meter `false`is. Beide argumenten moeten Booleaanse waarden hebben. De volgende retour `true`waarde is:`not(contains('200 Success','Fail'))`|  
|if|Retourneert een opgegeven waarde op basis van als de expressie resulteert in `true` of `false`.  Zo wordt bijvoorbeeld het volgende geretourneerd `"yes"`:`if(equals(1, 1), 'yes', 'no')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Expressie<br /><br /> **Beschrijving**: Vereist. Een Booleaanse waarde die bepaalt welke waarde door de expressie wordt geretourneerd.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Waar<br /><br /> **Beschrijving**: Vereist. De waarde die moet worden geretourneerd als de `true`expressie is.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: False<br /><br /> **Beschrijving**: Vereist. De waarde die moet worden geretourneerd als de `false`expressie is.|  
  
## <a name="conversion-functions"></a>Conversiefuncties  
 Deze functies worden gebruikt om te converteren tussen de systeem eigen typen in de taal:  
  
-   string  
  
-   integer  
  
-   float  
  
-   boolean  
  
-   matrices  
  
-   woorden lijsten  
  
|Functienaam|Description|  
|-------------------|-----------------|  
|int|Zet de para meter om in een geheel getal. De volgende expressie retourneert bijvoorbeeld 100 als een getal in plaats van een teken reeks:`int('100')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Value<br /><br /> **Beschrijving**: Vereist. De waarde die wordt geconverteerd naar een geheel getal.|  
|string|Zet de para meter om in een teken reeks. De volgende expressie retourneert `'10'`bijvoorbeeld:  `string(10)`U kunt een object ook converteren naar een teken reeks, bijvoorbeeld als de para meter **Foo** een object met één eigenschap `bar : baz`is. vervolgens wordt het volgende `{"bar" : "baz"}` geretourneerd`string(pipeline().parameters.foo)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Value<br /><br /> **Beschrijving**: Vereist. De waarde die wordt geconverteerd naar een teken reeks.|  
|json|Converteer de para meter naar een JSON-type waarde. Dit is het tegenovergestelde van string (). De volgende expressie retourneert `[1,2,3]` bijvoorbeeld als een matrix, in plaats van een teken reeks:<br /><br /> `json('[1,2,3]')`<br /><br /> Op dezelfde manier kunt u een teken reeks naar een object converteren. `json('{"bar" : "baz"}')` Retourneert bijvoorbeeld:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks die wordt geconverteerd naar een systeem eigen type waarde.<br /><br /> De json-functie ondersteunt ook XML-invoer. Bijvoorbeeld, de parameter waarde van:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> wordt geconverteerd naar de volgende JSON:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Converteer het parameter argument naar een drijvende-komma getal. De volgende expressie retourneert `10.333`bijvoorbeeld:`float('10.333')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Value<br /><br /> **Beschrijving**: Vereist. De waarde die wordt geconverteerd naar een getal met een drijvende komma.|  
|bool|Converteer de para meter naar een Booleaanse waarde. De volgende expressie retourneert `false`bijvoorbeeld:`bool(0)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Value<br /><br /> **Beschrijving**: Vereist. De waarde die wordt geconverteerd naar een Boole.|  
|Voeg|Retourneert het eerste niet-null-object in de argumenten die zijn door gegeven in. Opmerking: een lege teken reeks is niet null. Als para meters 1 en 2 bijvoorbeeld niet zijn gedefinieerd, wordt het `fallback`volgende geretourneerd:`coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Parameter nummer**: 1... *n*<br /><br /> **Naam**: Object*n*<br /><br /> **Beschrijving**: Vereist. De objecten waarop moet worden `null`gecontroleerd.|  
|base64|Retourneert de base64-weer gave van de invoer teken reeks. De volgende expressie retourneert `c29tZSBzdHJpbmc=`bijvoorbeeld:`base64('some string')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Teken reeks 1<br /><br /> **Beschrijving**: Vereist. De teken reeks die moet worden gecodeerd in Base64-weer gave.|  
|base64ToBinary|Retourneert een binaire weer gave van een base64-gecodeerde teken reeks. De volgende expressie retourneert bijvoorbeeld de binaire weer gave van een teken reeks: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De base64-gecodeerde teken reeks.|  
|base64ToString|Retourneert een teken reeks representatie van een teken reeks met based64-code ring. De volgende expressie retourneert bijvoorbeeld een teken reeks: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De base64-gecodeerde teken reeks.|  
|Binary|Retourneert een binaire weer gave van een waarde.  De volgende expressie retourneert bijvoorbeeld een binaire weer gave van een teken reeks:`binary(‘some string’).`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Value<br /><br /> **Beschrijving**: Vereist. De waarde die is geconverteerd naar binair.|  
|dataUriToBinary|Retourneert een binaire weer gave van een gegevens-URI. De volgende expressie retourneert bijvoorbeeld de binaire weer gave van een teken reeks:`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De gegevens-URI die moet worden geconverteerd naar de binaire weer gave.|  
|dataUriToString|Retourneert een teken reeks representatie van een gegevens-URI. De volgende expressie retourneert bijvoorbeeld een teken reeks:`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br />**Beschrijving**: Vereist. De gegevens-URI die moet worden geconverteerd naar een teken reeks weergave.|  
|dataUri|Retourneert een gegevens-URI van een waarde. De volgende expressie retourneert bijvoorbeeld gegevens:`text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Parameter nummer**: 1<br /><br />**Naam**: Value<br /><br />**Beschrijving**: Vereist. De waarde die moet worden geconverteerd naar de gegevens-URI.|  
|decodeBase64|Retourneert een teken reeks weergave van een invoer based64 teken reeks. De volgende expressie retourneert `some string`bijvoorbeeld:`decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Retourneert een teken reeks weergave van een invoer based64 teken reeks.|  
|encodeUriComponent|URL: verstuurt de teken reeks die wordt door gegeven in. De volgende expressie retourneert `You+Are%3ACool%2FAwesome`bijvoorbeeld:`encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks waarmee URL-onveilige tekens van worden teruggestuurd.|  
|decodeUriComponent|Un-URL: de teken reeks die wordt door gegeven, wordt in de-de opgegeven waarde verescapet. De volgende expressie retourneert `You Are:Cool/Awesome`bijvoorbeeld:`encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De teken reeks waaruit de URL-onveilige tekens moeten worden gedecodeerd.|  
|decodeDataUri|Retourneert een binaire representatie van een invoer gegevens-URI-teken reeks. De volgende expressie retourneert bijvoorbeeld de binaire weer gave van `some string`:`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br /> **Beschrijving**: Vereist. De dataURI die moet worden gedecodeerd in een binaire weer gave.|  
|uriComponent|Retourneert een URI-gecodeerde representatie van een waarde. De volgende expressie retourneert bijvoorbeeld`You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Parameter Details: Telwoord 1, naam: Teken reeks, beschrijving: Vereist. De teken reeks die moet worden gecodeerd met een URI.|  
|uriComponentToBinary|Retourneert een binaire weer gave van een gecodeerde URI-teken reeks. De volgende expressie retourneert bijvoorbeeld een binaire weer gave van `You Are:Cool/Awesome`:`uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Tekenreeks<br /><br />**Beschrijving**: Vereist. De teken reeks met URI-code ring.|  
|uriComponentToString|Retourneert een teken reeks representatie van een teken reeks met URI-code ring. De volgende expressie retourneert `You Are:Cool/Awesome`bijvoorbeeld:`uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Parameter nummer**: 1<br /><br />**Naam**: Tekenreeks<br /><br />**Beschrijving**: Vereist. De teken reeks met URI-code ring.|  
|xml|Retourneert een XML-weer gave van de waarde. De volgende expressie retourneert bijvoorbeeld een XML-inhoud die wordt vertegenwoordigd `'\<name>Alan\</name>'`door `xml('\<name>Alan\</name>')`:. De functie XML ondersteunt ook JSON-object invoer. De para meter `{ "abc": "xyz" }` wordt bijvoorbeeld geconverteerd naar een XML-inhoud`\<abc>xyz\</abc>`<br /><br /> **Parameter nummer**: 1<br /><br />**Naam**: Value<br /><br />**Beschrijving**: Vereist. De waarde die moet worden geconverteerd naar XML.|  
|XPath|Retourneert een matrix van XML-knoop punten die overeenkomen met de XPath-expressie van een waarde die door de XPath-expressie wordt geëvalueerd.<br /><br />  **Voorbeeld 1**<br /><br /> Stel dat de waarde van de para meter P1 een teken reeks representatie van de volgende XML is:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Deze code:`xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> retourneert<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> maar<br /><br /> 2. Deze code:`xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> retourneert<br /><br /> `13`<br /><br /> <br /><br /> **Voorbeeld 2**<br /><br /> Op basis van de volgende XML-inhoud:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Deze code:`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> of<br /><br /> 2. Deze code:`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> retourneert<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> and<br /><br /> 3. Deze code:`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> retourneert<br /><br /> ``bar``<br /><br /> **Parameter nummer**: 1<br /><br />**Naam**: Xml<br /><br />**Beschrijving**: Vereist. De XML waarop de XPath-expressie moet worden geëvalueerd.<br /><br /> **Parameter nummer**: 2<br /><br />**Naam**: XPath<br /><br />**Beschrijving**: Vereist. De XPath-expressie die moet worden geëvalueerd.|  
|array|De para meter naar een matrix Converteren.  De volgende expressie retourneert `["abc"]`bijvoorbeeld:`array('abc')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Value<br /><br /> **Beschrijving**: Vereist. De waarde die wordt omgezet in een matrix.|
|createArray|Hiermee wordt een matrix gemaakt op basis van de para meters.  De volgende expressie retourneert `["a", "c"]`bijvoorbeeld:`createArray('a', 'c')`<br /><br /> **Parameter nummer**: 1... nvt<br /><br /> **Naam**: Elke n<br /><br /> **Beschrijving**: Vereist. De waarden die in een matrix moeten worden gecombineerd.|

## <a name="math-functions"></a>Wiskundige functies  
 Deze functies kunnen worden gebruikt voor beide typen getallen: **gehele** getallen en **zwevende**waarden.  
  
|Functienaam|Description|  
|-------------------|-----------------|  
|toevoegen|Retourneert het resultaat van de toevoeging van de twee getallen. Deze functie retourneert `20.333`bijvoorbeeld:`add(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Summand 1<br /><br /> **Beschrijving**: Vereist. Het getal dat moet worden toegevoegd aan **Summand 2**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Summand 2<br /><br /> **Beschrijving**: Vereist. Het getal dat moet worden toegevoegd aan **Summand 1**.|  
|sub|Retourneert het resultaat van de aftrekking van de twee getallen. Deze functie retourneert bijvoorbeeld: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Aftrek getal<br /><br /> **Beschrijving**: Vereist. Het getal waarvan **aftrekker** is verwijderd.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Aftrekker<br /><br /> **Beschrijving**: Vereist. Het getal dat moet worden verwijderd uit de **aftrek getal**.|  
|mul|Retourneert het resultaat van de vermenigvuldiging van de twee getallen. Zo wordt bijvoorbeeld het volgende geretourneerd `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Multiplicand 1<br /><br /> **Beschrijving**: Vereist. Het getal waarmee **Multiplicand 2** moet worden vermenigvuldigd met.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Multiplicand 2<br /><br /> **Beschrijving**: Vereist. Het getal om **Multiplicand 1** te vermenigvuldigen met.|  
|div|Retourneert het resultaat van de deling van de twee getallen. Zo wordt bijvoorbeeld het volgende geretourneerd `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Dividend<br /><br /> **Beschrijving**: Vereist. Het getal dat moet worden gedeeld door de **deler**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Reken<br /><br /> **Beschrijving**: Vereist. Het getal waarop het **dividend** moet worden verdeeld.|  
|mod|Retourneert het resultaat van het rest getal na de deling van de twee getallen (modulo). De volgende expressie retourneert `2`bijvoorbeeld:<br /><br /> `mod(10,4)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Dividend<br /><br /> **Beschrijving**: Vereist. Het getal dat moet worden gedeeld door de **deler**.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Reken<br /><br /> **Beschrijving**: Vereist. Het getal waarop het **dividend** moet worden verdeeld. Na de deling wordt de rest genomen.|  
|min|Er zijn twee verschillende patronen voor het aanroepen van deze functie: `min([0,1,2])`Hier wordt min een matrix gebruikt. Deze expressie retourneert `0`. Deze functie kan ook een lijst met door komma's gescheiden waarden hebben:  `min(0,1,2)`Deze functie retourneert ook 0. Houd er rekening mee dat alle waarden getallen moeten zijn, dus als de para meter een matrix is, mag deze alleen getallen bevatten.<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Verzameling of waarde<br /><br /> **Beschrijving**: Vereist. Het kan een matrix met waarden zijn om de minimum waarde te vinden, of de eerste waarde van een set.<br /><br /> **Parameter nummer**: 2... *n*<br /><br /> **Naam**: Waarde *n*<br /><br /> **Beschrijving**: Optioneel. Als de eerste para meter een waarde is, kunt u aanvullende waarden door geven en wordt het minimum van alle door gegeven waarden geretourneerd.|  
|max|Er zijn twee verschillende patronen voor het aanroepen van deze functie:`max([0,1,2])`<br /><br /> Hier is Max een matrix. Deze expressie retourneert `2`. Deze functie kan ook een lijst met door komma's gescheiden waarden hebben:  `max(0,1,2)`Deze functie retourneert 2. Houd er rekening mee dat alle waarden getallen moeten zijn, dus als de para meter een matrix is, mag deze alleen getallen bevatten.<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Verzameling of waarde<br /><br /> **Beschrijving**: Vereist. Het kan een matrix met waarden zijn om de maximum waarde te vinden, of de eerste waarde van een set.<br /><br /> **Parameter nummer**: 2... *n*<br /><br /> **Naam**: Waarde *n*<br /><br /> **Beschrijving**: Optioneel. Als de eerste para meter een waarde is, kunt u aanvullende waarden door geven en wordt het maximum van alle door gegeven waarden geretourneerd.|  
|bereik| Genereert een matrix van gehele getallen vanaf een bepaald getal en definieert de lengte van de geretourneerde matrix. Deze functie retourneert `[3,4,5,6]`bijvoorbeeld:<br /><br /> `range(3,4)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Index starten<br /><br /> **Beschrijving**: Vereist. Het is het eerste geheel getal in de matrix.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Count<br /><br /> **Beschrijving**: Vereist. Aantal gehele getallen in de matrix.|  
|ASELECT| Hiermee wordt een wille keurig geheel getal binnen het opgegeven bereik gegenereerd (inclusief op beide einden. Dit kan bijvoorbeeld het volgende retour `42`neren:<br /><br /> `rand(-1000,1000)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Minimum<br /><br /> **Beschrijving**: Vereist. Het kleinste gehele getal dat kan worden geretourneerd.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Maximum<br /><br /> **Beschrijving**: Vereist. Het hoogste gehele getal dat kan worden geretourneerd.|  
  
## <a name="date-functions"></a>Datum functies  
  
|Functienaam|Description|  
|-------------------|-----------------|  
|utcnow|Retourneert de huidige tijds tempel als een teken reeks. Bijvoorbeeld `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Indeling<br /><br /> **Beschrijving**: Optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|addseconds|Hiermee wordt een geheel getal van seconden toegevoegd aan een teken reeks tijds tempel dat wordt door gegeven. Het aantal seconden kan positief of negatief zijn. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Timestamp<br /><br /> **Beschrijving**: Vereist. Een teken reeks die de tijd bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Seconden<br /><br /> **Beschrijving**: Vereist. Het aantal seconden dat moet worden toegevoegd. Kan negatief zijn om seconden af te trekken.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: Indeling<br /><br /> **Beschrijving**: Optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|addminutes|Hiermee wordt een geheel getal van minuten toegevoegd aan een teken reeks tijds tempel dat wordt door gegeven. Het aantal minuten kan positief of negatief zijn. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Timestamp<br /><br /> **Beschrijving**: Vereist. Een teken reeks die de tijd bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Minuten<br /><br /> **Beschrijving**: Vereist. Het aantal minuten dat moet worden toegevoegd. Kan negatief zijn om minuten af te trekken.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: Indeling<br /><br /> **Beschrijving**: Optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|addhours|Voegt een geheel getal van uren toe aan een teken reeks tijds tempel dat wordt door gegeven. Het aantal uren kan positief of negatief zijn. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Timestamp<br /><br /> **Beschrijving**: Vereist. Een teken reeks die de tijd bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Uren<br /><br /> **Beschrijving**: Vereist. Het aantal uren dat moet worden toegevoegd. Kan negatief zijn om uren af te trekken.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: Indeling<br /><br /> **Beschrijving**: Optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|addDays|Hiermee wordt een geheel getal van dagen toegevoegd aan een teken reeks tijds tempel dat wordt door gegeven. Het aantal dagen kan positief of negatief zijn. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Timestamp<br /><br /> **Beschrijving**: Vereist. Een teken reeks die de tijd bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Dagen<br /><br /> **Beschrijving**: Vereist. Het aantal dagen dat moet worden toegevoegd. Kan negatief zijn om dagen af te trekken.<br /><br /> **Parameter nummer**: 3<br /><br /> **Naam**: Indeling<br /><br /> **Beschrijving**: Optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  
|formatDateTime|Retourneert een teken reeks in de datum notatie. Het resultaat is een teken reeks in ISO 8601-indeling ("o") standaard, tenzij een indelings specificatie wordt gegeven. Bijvoorbeeld `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Parameter nummer**: 1<br /><br /> **Naam**: Date<br /><br /> **Beschrijving**: Vereist. Een teken reeks die de datum bevat.<br /><br /> **Parameter nummer**: 2<br /><br /> **Naam**: Indeling<br /><br /> **Beschrijving**: Optioneel. Een teken reeks met [één indeling](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) of een [aangepast opmaak patroon](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) dat aangeeft hoe de waarde van deze tijds tempel moet worden opgemaakt. Als er geen indeling wordt gegeven, wordt de ISO 8601-indeling ("o") gebruikt.|  

## <a name="next-steps"></a>Volgende stappen
Zie [systeem variabelen](control-flow-system-variables.md)voor een lijst met systeem variabelen die u in expressies kunt gebruiken.
