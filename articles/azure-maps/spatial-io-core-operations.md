---
title: Kern-IO-activiteiten | Microsoft Azure Maps
description: Leer hoe u XML en gegevens efficiënt lezen en schrijven met behulp van kernbibliotheken uit de ruimtelijke IO-module.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371446"
---
# <a name="core-io-operations"></a>Core IO-bewerkingen

De ruimtelijke IO-module biedt niet alleen tools om ruimtelijke gegevensbestanden te lezen, maar stelt ook onderliggende bibliotheken bloot om XML te lezen en te schrijven en gegevens snel en efficiënt af te gebak.

De `atlas.io.core` naamruimte bevat twee klassen op laag niveau die snel CSV- en XML-gegevens kunnen lezen en schrijven. Deze basisklassen geven de aanmaningen en schrijvers van ruimtelijke gegevens in de Module Ruimtelijke IO. Gebruik ze gerust om extra lees- en schrijfondersteuning toe te voegen voor CSV- of XML-bestanden.
 
## <a name="read-delimited-files"></a>Afgebakende bestanden lezen

De `atlas.io.core.CsvReader` klasse leest tekenreeksen die afgebakende gegevenssets bevatten. Deze klasse biedt twee methoden voor het lezen van gegevens:

- De `read` functie leest de volledige gegevensset en retourneert een tweedimensionale reeks tekenreeksen die alle cellen van de afgebakende gegevensset vertegenwoordigen.
- De `getNextRow` functie leest elke regel tekst in een afgebakende gegevensset en retourneert een reeks tekenreeksen die alle cellen in die regel van gegevensset weergeeft. De gebruiker kan de rij verwerken en elk onnodig geheugen uit die rij verwijderen voordat hij de volgende rij verwerkt. Dus, functie is efficiënter geheugen.

Standaard gebruikt de lezer het kommateken als de scheidingsteken. De scheidingsteken kan echter worden gewijzigd in een `'auto'`enkel teken of ingesteld op . Wanneer ingesteld `'auto'`op , de lezer analyseert de eerste regel tekst in de tekenreeks. Vervolgens selecteert u het meest voorkomende teken in de onderstaande tabel om te gebruiken als de scheidingsteken.

| | |
| :-- | :-- |
| Komma | `,` |
| Tab | `\t` |
| Pipe | `|` |

Deze lezer ondersteunt ook tekstkwalificatietoernooien die worden gebruikt om cellen te verwerken die het tekensteken van de scheiding bevatten. Het teken`'"'`( ) is de standaard tekstkwalificatie, maar kan worden gewijzigd in een enkel teken.

## <a name="write-delimited-files"></a>Afgebakende bestanden schrijven

Het `atlas.io.core.CsvWriter` schrijft een array van objecten als een afgebakende tekenreeks. Elk teken kan worden gebruikt als scheidingsteken of als tekstkwalificatie. De standaardscheidingsscheiding is`','`komma ( ) en de`'"'`standaardtekstkwalificatie is het teken () aanhalinggeven.

Volg de onderstaande stappen om deze klasse te gebruiken:

- Maak een instantie van de klasse en stel optioneel een aangepaste scheidingsteken of tekstkwalificatie in.
- Schrijf gegevens naar de `write` klasse `writeRow` met behulp van de functie of de functie. Geef `write` voor de functie een tweedimensionale array van objecten door die meerdere rijen en cellen vertegenwoordigen. Als u `writeRow` de functie wilt gebruiken, geeft u een array met objecten door die een rij gegevens met meerdere kolommen vertegenwoordigen.
- Roep `toString` de functie aan om de afgebakende tekenreeks op te halen. 
- Roep eventueel de `clear` methode aan om de schrijver herbruikbaar te `delete` maken en de toewijzing van resources te verminderen, of roep de methode aan om de instantie van de schrijver te verwijderen.

> [!Note]
> Het aantal geschreven kolommen wordt beperkt tot het aantal cellen in de eerste rij van de gegevens die aan de schrijver worden doorgegeven.

## <a name="read-xml-files"></a>XML-bestanden lezen

De `atlas.io.core.SimpleXmlReader` klasse is sneller bij het `DOMParser`ontwijs maken van XML-bestanden dan . De `atlas.io.core.SimpleXmlReader` klasse vereist echter dat XML-bestanden goed worden opgemaakt. XML-bestanden die niet goed zijn opgemaakt, bijvoorbeeld ontbrekende sluittags, zullen waarschijnlijk resulteren in een fout.

De volgende code laat zien `SimpleXmlReader` hoe u de klasse gebruikt om een XML-tekenreeks in een JSON-object te ontleden en deze te serialiseren tot een gewenste indeling.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>XML-bestanden schrijven

De `atlas.io.core.SimpleXmlWriter` klasse schrijft goed geformatteerde XML op een geheugenefficiënte manier.

De volgende code laat zien `SimpleXmlWriter` hoe u de klasse gebruiken om een goed opgemaakte XML-tekenreeks te genereren.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

De gegenereerde XML uit de bovenstaande code ziet er als volgt uit.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter (CsvWriter)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)