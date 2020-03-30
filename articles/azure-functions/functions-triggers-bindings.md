---
title: Triggers en bindingen in Azure-functies
description: Leer triggers en bindingen gebruiken om uw Azure-functie te verbinden met online gebeurtenissen en cloudservices.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276502"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concepten van Azure Functions-triggers en -bindingen

In dit artikel leert u de concepten op hoog niveau rondom functies triggers en bindingen.

Triggers zijn de oorzaak van een functie uit te voeren. Een trigger definieert hoe een functie wordt aangeroepen en een functie moet precies één trigger hebben. Aan triggers zijn gegevens gekoppeld. Meestal is dit de nettolading waarmee de functie is geactiveerd. 

Binding aan een functie is een manier om een andere bron declaratief met de functie te verbinden; bindingen kunnen worden aangesloten als *inputbindingen,* *outputbindingen*of beide. Gegevens van bindingen worden als parameters aan de functie geleverd.

U kunt verschillende bindingen mixen en matchen om aan uw behoeften te voldoen. Bindingen zijn optioneel en een functie kan een of meerdere invoer- en/of uitvoerbindingen hebben.

Met triggers en bindingen u harde coderingstoegang tot andere services vermijden. Uw functie ontvangt gegevens (bijvoorbeeld de inhoud van een wachtrijbericht) in functieparameters. U verzendt gegevens (bijvoorbeeld om een ​​wachtrijbericht te maken) door de retourwaarde van de functie te gebruiken. 

Houd rekening met de volgende voorbeelden van hoe u verschillende functies implementeren.

| Voorbeeldscenario | Trigger | Invoerbinding | Uitvoerbinding |
|-------------|---------|---------------|----------------|
| Er komt een nieuw wachtrijbericht binnen waarin een functie wordt uitgevoerd om naar een andere wachtrij te schrijven. | Wachtrij<sup>*</sup> | *Geen* | Wachtrij<sup>*</sup> |
|Een geplande taak leest blobopslaginhoud en maakt een nieuw Cosmos DB-document. | Timer | Blob Storage | Cosmos DB |
|Het gebeurtenisraster wordt gebruikt om een afbeelding van Blob Storage en een document van Cosmos DB te lezen om een e-mail te verzenden. | Event Grid | Blob-opslag en Cosmos DB | SendGrid |
| Een webhook die Microsoft Graph gebruikt om een Excel-blad bij te werken. | HTTP | *Geen* | Microsoft Graph |

<sup>\*</sup>Vertegenwoordigt verschillende wachtrijen

Deze voorbeelden zijn niet bedoeld om volledig te zijn, maar worden verstrekt om te illustreren hoe u triggers en bindingen samen gebruiken.

###  <a name="trigger-and-binding-definitions"></a>Trigger- en bindende definities

Triggers en bindingen worden verschillend gedefinieerd, afhankelijk van de ontwikkelingsaanpak.

| Platform | Triggers en bindingen worden geconfigureerd door... |
|-------------|--------------------------------------------|
| C# klassenbibliotheek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decoratiemethoden en parameters met C#-kenmerken |
| Alle andere (inclusief Azure-portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[updatefunctie.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

De portal biedt een gebruikersinterface voor deze configuratie, maar u het bestand rechtstreeks bewerken door de **geavanceerde editor** te openen die beschikbaar is via het tabblad **Integreren** van uw functie.

In .NET definieert het parametertype het gegevenstype voor invoergegevens. Gebruik bijvoorbeeld `string` om te binden aan de tekst van een wachtrijtrigger, een bytearray die moet worden gelezen als binair en een aangepast type om te deserialiseren naar een object.

Voor talen die dynamisch zijn getypt, zoals `dataType` JavaScript, gebruikt u de eigenschap in het *bestand function.json.* Als u bijvoorbeeld de inhoud van een HTTP-aanvraag in binaire indeling wilt lezen, ingesteld `dataType` op `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andere opties `dataType` `stream` voor `string`zijn en .

## <a name="binding-direction"></a>Bindende richting

Alle triggers en bindingen hebben een `direction` eigenschap in het bestand [function.json:](./functions-reference.md)

- Voor triggers is de richting altijd`in`
- Gebruik `in` van invoer- en uitvoerbindingen`out`
- Sommige bindingen ondersteunen `inout`een speciale richting. Als u `inout`de geavanceerde editor gebruikt, is alleen de **Advanced-editor** beschikbaar via het tabblad **Integreren** in de portal.

Wanneer u [kenmerken in een klassenbibliotheek](functions-dotnet-class-library.md) gebruikt om triggers en bindingen te configureren, wordt de richting in een kenmerkconstructor opgegeven of afgeleid van het parametertype.

## <a name="supported-bindings"></a>Ondersteunde bindingen

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Zie [Ondersteunde talen](supported-languages.md)voor informatie over welke bindingen in preview zijn of zijn goedgekeurd voor productiegebruik.

## <a name="resources"></a>Resources
- [Bindende expressies en patronen](./functions-bindings-expressions-patterns.md)
- [De retourwaarde azure-functie gebruiken](./functions-bindings-return-value.md)
- [Een bindende expressie registreren](./functions-bindings-register.md)
- Testing:
  - [Strategieën voor het testen van uw code in Azure Functions](functions-test-a-function.md)
  - [Handmatig een niet door HTTP geactiveerde functie uitvoeren](functions-manually-run-non-http.md)
- [Bindende fouten verwerken](./functions-bindings-errors.md)

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Azure Functions-bindingsextensies registreren](./functions-bindings-register.md)
