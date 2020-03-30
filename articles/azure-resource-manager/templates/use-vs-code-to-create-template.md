---
title: Visual Studio Code gebruiken om sjablonen te maken
description: Meer informatie over het installeren en gebruiken van Visual Studio Code en de azure resource manager tools-extensie.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273629"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Visual Studio-code gebruiken om Azure Resource Manager-sjablonen te maken

Visual Studio Code is een lichtgewicht, multiplatform, open-source editor. De extensie Azure Resource Manager Template Tools is een plug-in voor de sjabloonontwikkeling van Resource Manager. De extensie voegt taalondersteuning voor sjablonen toe om u intellisense, syntaxismarkering, inlinehelp en vele andere taalfuncties te bieden. Samen bieden ze aanbevolen ervaring met het ontwikkelen van sjablonen.

## <a name="install-visual-studio-code"></a>Visual Studio Code installeren

Visual studio Code ondersteunt MacOS, Windows en Linux.  Het kan worden geïnstalleerd vanuit [Visual Studio Code.](https://code.visualstudio.com/)

## <a name="install-resource-manager-tools-extension"></a>Extensie Hulpmiddelen voor Resource Beheer installeren

1. Open Visual Studio Code.
1. Selecteer **Extensies** in het linkermenu. Of selecteer **Extensies** in het menu **Weergave** om het deelvenster Extensies te openen.

    ![De extensie Hulpprogramma's voor Visual Studio Code Resource Manager installeren](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Zoeken naar **Resource Manager**.
1. Selecteer **Installeren** onder **Azure Resource Manager Tools**.

## <a name="the-extension-features"></a>De uitbreidingsfuncties

### <a name="colorization-for-template-language-expressions"></a>Kleuring voor sjabloontaaluitdrukkingen

Parameters, variabelen, functies, namen en expressies zijn kleurgecodeerd, zoals in de volgende schermafbeelding wordt weergegeven:

![Hulpmiddelen voor Visual Studio Code Resource Manager-extensies inkleuren](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

Met de overzichtsweergave van de sjabloon u eenvoudig door grote sjablonen navigeren.

### <a name="intellisense"></a>Intellisense

De sjabloonextensie Resourcebeheer kent mogelijke aanvullingen voor functienamen, parameters, variabelen en verwijzingen. De IntelliSense-suggesties verschijnen tijdens het typen. Als u doorgaat met het typen van tekens, wordt de lijst met leden (variabelen, methoden, enz.) gefilterd om alleen leden met uw getypte tekens op te nemen. Als u **op Tab** drukt of Het geselecteerde lid **invoegt,** wordt het geselecteerde lid ingevoegd.

- Ingebouwde functienamen

    ![Visual Studio Code Resource Manager tools extensies intellisense functies](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Parameterverwijzingen

    ![Visual Studio Code Resource Manager-hulpprogramma's voor intellisenseparameters](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Variabele verwijzingen

    ![Visual Studio Code Resource Manager-hulpprogramma's voor intellisensevariabelen](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroep() eigenschappen

    ![Visual Studio Code Resource Manager tools extensies intellisense functies](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Daarnaast werkt intellisense ook met eigenschappen van subscription() en de eigenschappen van verwijzingen naar variabelen die objecten zijn.

### <a name="signature-help-for-function-parameters"></a>Help voor handtekening voor functieparameters

Wanneer u over functienamen zweeft, wordt in de extensie de handtekeninghelp voor functieparameters weergegeven.

![Handtekeningfunctie voor visual studiocoderesourcebeheer-extensies](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Ga naar definitie voor variabelen- en parameterverwijzingen

U naar de definitie gaan met **Ctrl+Click**, of door ![het contextmenu te gebruiken zoals weergegeven in de schermafbeelding: Instellingen voor Visual Studio Code Resource Manager gaan naar definitie](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

U de definitie aan de zijkant openen met **Ctrl+Alt+Klik**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Peek voor variabele en parameterdefinities

Als u een gegluurde editor wilt openen, gebruikt u het contextmenu zoals in de vorige schermafbeelding.

De volgende schermafbeelding toont de peek-editor:

![Visual Studio Code Resource Manager-hulpprogramma's voor peek-editor](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Alle referenties voor variabelen en parameters zoeken

Als u alle verwijzingen wilt vinden, gebruikt u het contextmenu zoals in de vorige schermafbeelding.

In de volgende schermafbeelding ziet u hoe de verwijzingen worden gemarkeerd:

![Visual Studio Code Resource Manager-hulpprogramma's vinden alle referenties](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>De naam van alle verwijzingen voor variabelen en parameters wijzigen

Als u de naam van alle verwijzingen voor variabelen en parameters wilt wijzigen, gebruikt u het contextmenu zoals weergegeven in de vorige schermafbeelding.

### <a name="hover-for-parameter-description"></a>Zweven voor parameterbeschrijving

![Visual Studio Code Resource Manager-extensies zweven definitie](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Brace matching

Overeenkomende haakjes worden gemarkeerd zodra de cursor zich in de buurt van een van hen bevindt. Wanneer u op een brace klikt, wordt de bijpassende brace ook gemarkeerd zoals weergegeven in de volgende schermafbeelding:

![Visual Studio Code Resource Manager-hulpprogramma's brace matching](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Fouten en waarschuwingen weergeven

De fouten die door de extensie worden geïdentificeerd, zijn onder meer:

- Ongedefinieerde parameterverwijzingen
- Niet-gedefinieerde variabele verwijzingen
- Niet-herkende functienamen
- referentie() functiegebruik in variabele definitie
- Onjuist aantal argumenten in functies

De waarschuwingen omvatten:

- Ongebruikte parameters
- Ongebruikte variabelen

## <a name="next-steps"></a>Volgende stappen

- Zie [Zelfstudie: Uw eerste Azure Resource Manager-sjabloon maken en implementeren](template-tutorial-create-first-template.md)voor meer informatie over het maken van Azure Resource Manager-sjablonen.
- Zie [Quickstart: Azure Resource Manager-sjablonen maken met Visual Studio Code als](quickstart-create-templates-use-visual-studio-code.md) u een snelle start wilt doorlopen met Visual Studio Code
