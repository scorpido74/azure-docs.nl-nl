---
title: Visual Studio code gebruiken om sjablonen te maken
description: Meer informatie over het installeren en gebruiken van Visual Studio code en de uitbrei ding Azure Resource Manager tools.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483594"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken

Visual Studio code is een licht gewicht, open source-editor. De uitbrei ding van de Azure Resource Manager-sjabloon Hulpprogramma's is een invoeg toepassing voor de ontwikkeling van Resource Manager-sjablonen. De uitbrei ding voegt taal ondersteuning toe voor sjablonen om u IntelliSense, syntaxis markering, in line Help en vele andere taal functies te geven. Samen bieden ze de aanbevolen ervaring voor het ontwikkelen van sjablonen.

## <a name="install-visual-studio-code"></a>Visual Studio Code installeren

Visual Studio code ondersteunt MacOS, Windows en Linux.  Het kan worden geïnstalleerd vanuit [Visual Studio code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>De Resource Manager-Hulpprogram Ma's-extensie installeren

1. Open Visual Studio Code.
1. Selecteer **uitbrei dingen** in het menu links. Of Selecteer in het menu **weer gave** de optie **uitbrei dingen** om het deel venster uitbrei dingen te openen.

    ![De extensie van Visual Studio code Resource Manager-Hulpprogram Ma's installeren](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Zoek naar **Resource Manager**.
1. Selecteer **installeren** onder **Azure Resource Manager-hulpprogram ma's**.

## <a name="the-extension-features"></a>De uitbreidings functies

### <a name="colorization-for-template-language-expressions"></a>Inkleuring voor sjabloon taal expressies

Para meters, variabelen, functies, namen en expressies worden in kleur gecodeerd, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Inkleuring van Visual Studio code Resource Manager-hulpprogram ma's](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

De overzichts weergave van de sjabloon maakt het eenvoudig om door grote sjablonen te navigeren.

### <a name="intellisense"></a>IntelliSense

De Resource Manager-sjabloon extensie kent mogelijke aanvullingen voor functie namen, para meters, variabelen en verwijzingen. Het pop-upvenster IntelliSense Suggestions terwijl u typt. Als u doorgaat met het typen van tekens, wordt de lijst met leden (variabelen, methoden, enzovoort) gefilterd zodat alleen leden met de getypte tekens worden meegenomen. Als u op **Tab** of **Enter** drukt, wordt het geselecteerde lid ingevoegd.

- Ingebouwde functie namen

    ![Functies van de IntelliSense-uitbrei dingen voor Visual Studio code Resource Manager-hulpprogram ma's](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Parameter verwijzingen

    ![IntelliSense-para meters voor Visual Studio code Resource Manager-hulpprogram ma's](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Verwijzingen naar variabelen

    ![IntelliSense-variabelen voor Visual Studio code Resource Manager-hulpprogram ma's](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- eigenschappen van resourceGroup ()

    ![Functies van de IntelliSense-uitbrei dingen voor Visual Studio code Resource Manager-hulpprogram ma's](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Daarnaast werkt IntelliSense ook met eigenschappen van abonnementen () en de eigenschappen van verwijzingen naar variabelen die objecten zijn.

### <a name="signature-help-for-function-parameters"></a>Hand tekening Help voor functie parameters

Bij het aanwijzen van de functie namen, toont de uitbrei ding de hand tekening Help voor functie parameters.

![Hand tekening functie extensies van Visual Studio code Resource Manager-hulpprogram ma's](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Ga naar definitie voor variabele-en parameter verwijzingen

U kunt naar de definitie gaan met **CTRL + klikken**of met behulp van het context menu, zoals wordt weer gegeven in de scherm afbeelding: ![Visual Studio code Resource Manager tools Extensions Ga naar definition](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

U kunt de definitie openen aan de zijkant met **CTRL + ALT + Klik**.

### <a name="peek-for-variable-and-parameter-definitions"></a>De definities van variabelen en para meters bekijken

Als u uitgelichte editor wilt openen, gebruikt u het context menu zoals weer gegeven in de vorige scherm afbeelding.

Op de volgende scherm afbeelding ziet u de weer gave bekijken:

![Visual Studio code Resource Manager-hulpprogram ma's uitbrei dingen bekijken editor](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Alle verwijzingen voor variabelen en para meters zoeken

Als u alle verwijzingen wilt zoeken, gebruikt u het context menu zoals weer gegeven in de vorige scherm afbeelding.

In de volgende scherm afbeelding ziet u hoe de verwijzingen worden gemarkeerd:

![Uitbrei dingen van Visual Studio code Resource Manager-hulpprogram ma's alle verwijzingen zoeken](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Wijzig de naam van alle verwijzingen voor variabelen en para meters

Als u de naam van alle verwijzingen voor variabelen en para meters wilt wijzigen, gebruikt u het context menu zoals weer gegeven in de vorige scherm afbeelding.

### <a name="hover-for-parameter-description"></a>Aanwijzen voor parameter beschrijving

![Uitbrei dingen voor de toewijzing van Visual Studio code Resource Manager-hulpprogram ma's](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Accolades vergelijken

De vier Kante haken worden gemarkeerd zodra de cursor zich boven een van de bevindt. Wanneer u op een accolade klikt, wordt de bijbehorende accolade ook gemarkeerd zoals weer gegeven in de volgende scherm afbeelding:

![Bestemmings accolades van Visual Studio code Resource Manager-hulpprogram ma's](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Fouten en waarschuwingen weer geven

De fouten die worden geïdentificeerd door de uitbrei ding zijn onder andere:

- Niet-gedefinieerde parameter verwijzingen
- Niet-gedefinieerde variabelen verwijzingen
- Niet-herkende functie namen
- gebruik van de functie Reference () in de definitie van de variabele
- Onjuist aantal argumenten in functies

De waarschuwingen zijn onder andere:

- Niet-gebruikte para meters
- Ongebruikte variabelen

## <a name="next-steps"></a>Volgende stappen

- Zie [zelf studie: uw eerste Azure Resource Manager sjabloon maken en implementeren](template-tutorial-create-first-template.md)voor meer informatie over het maken van Azure Resource Manager sjablonen.
- Als u een Snelstartgids wilt door lopen met Visual Studio code, raadpleegt u [Quick Start: Azure Resource Manager sjablonen maken met Visual Studio code](quickstart-create-templates-use-visual-studio-code.md)
