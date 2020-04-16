---
title: IntelliSense in Azure Stream Analytics-hulpprogramma's voor Visual Studio Code
description: In dit artikel wordt beschreven hoe intelliSense-functies worden gebruikt in Azure Stream Analytics-hulpprogramma's voor Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394395"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense in Azure Stream Analytics-hulpprogramma's voor Visual Studio Code

IntelliSense is beschikbaar voor [Stream Analytics Query language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) in Azure Stream Analytics tools voor VS [Code.](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) IntelliSense is een hulpprogramma voor het aanvullen van code die een aantal functies bevat: Lijstleden, Parameterinfo, Quick Info en Word voltooien. IntelliSense-functies worden soms genoemd door andere namen, zoals "code voltooiing", "content assist", en "code hinting".

![IntelliSense-demo](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense-functies

De IntelliSense-functies in Stream Analytics-hulpprogramma's voor VS-code worden aangedreven door een taalservice. Een taalservice analyseert uw broncode en biedt intelligente codevoltooiingen op basis van taalsemantiek. Als een taalservice mogelijke voltooiingen kent, worden IntelliSense-suggesties op de gewenste manier getypeerd. Als u doorgaat met typen, wordt een lijst met leden, zoals variabelen en methoden, gefilterd om alleen leden op te nemen die de tekens bevatten die u hebt getypt. Wanneer u `Tab` op `Enter` de or-toetsen drukt, voegt IntelliSense het geselecteerde lid in.

U IntelliSense in elk editorvenster activeren door een triggerteken te typen, zoals het stipteken. `.`

![intellisense autocompletion](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> De widget Suggesties ondersteunt CamelCase-filtering. U de letters typen die in hoofdletters zijn aangebracht in een methodenaam om de suggesties te beperken. Bijvoorbeeld, "cra" zal snel brengen "createApplication".

### <a name="types-of-completions"></a>Soorten voltooiingen

Stream Analytics-hulpprogramma's voor VS Code IntelliSense bieden verschillende soorten voltooiingen, waaronder suggesties voor taalservers, fragmenten en eenvoudige tekstuele voltooiingen op basis van woorden.

|       |         |       |
| ----- | ------- | ----- |
| Trefwoorden | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Naam gegevensset| `input`, `output`, `intermediate result set`|
| Kolomnaam gegevensset|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Naamvoltooiing

Naast het automatisch aanvullen van trefwoorden, leest Stream Analytics-tools voor VS-code de lijst met taakinvoer- en uitvoernamen, evenals de namen van de kolommen in uw gegevensbronnen wanneer ze zijn geconfigureerd. De extensie onthoudt deze informatie om mogelijkheden voor het voltooien van namen te bieden die handig zijn voor het invoeren van instructies met weinig toetsaanslagen:

Tijdens het coderen hoeft u de editor niet te verlaten om zoekopdrachten uit te voeren op namen van taakinvoer, uitvoernamen en kolomnamen. U uw context behouden, de informatie vinden die u nodig hebt, elementen direct in uw code invoegen en IntelliSense uw typering voor u laten voltooien.

Houd er rekening mee dat u lokale invoer of live-invoer moet configureren en het configuratiebestand moet opslaan om naamvoltooiing te kunnen gebruiken.

![naam voltooiing](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Parametergegevens

Met de optie **IntelliSense-parameterinfo** opent u een parameterslijst met informatie over het aantal, de namen en de typen parameters die door een functie worden vereist. De parameter vet geeft de volgende parameter aan die nodig is als u een functie typt.

De parameterlijst wordt ook weergegeven voor geneste functies. Als u een functie als parameter naar een andere functie typt, worden in de parameterlijst de parameters voor de binnenfunctie weergegeven. Wanneer de lijst met parameters met de binnenfunctie is voltooid, wordt de parameterlijst vervolgens teruggezet naar het weergeven van de parameters van de buitenste functie.

![parametergegevens](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Snelle informatie

Zoals de taalservice biedt, u **Quick Info** zien voor elke id in uw code. Enkele voorbeelden van id's zijn invoer, uitvoer, een tussenliggende resultaatset of functie. Wanneer u de muisaanwijzer over een id beweegt, wordt de declaratie weergegeven in een pop-upvenster. De eigenschappen en gegevensschema's voor invoer, indien geconfigureerd, en tussenliggende gegevensset worden weergegeven.

![snelle informatie](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Problemen met IntelliSense oplossen

Dit probleem wordt veroorzaakt door ontbrekende invoerconfiguratie die gegevens biedt. U controleren of een [lokale invoer](visual-studio-code-local-run.md#define-a-local-input) of [live-invoer](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) correct is geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een Azure Stream Analytics-taak maken in Visual Studio-code](quick-create-vs-code.md)
* [Query's van TestStream Analytics lokaal met voorbeeldgegevens met Behulp van Visual Studio Code](visual-studio-code-local-run.md)
* [Test Stream Analytics-query's lokaal tegen live stream-invoer met behulp van Visual Studio Code](visual-studio-code-local-run-live-input.md)
