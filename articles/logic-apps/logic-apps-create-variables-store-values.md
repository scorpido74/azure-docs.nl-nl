---
title: Variabelen maken en beheren voor het opslaan en doorgeven van waarden
description: Meer informatie over het opslaan, beheren, gebruiken en doorgeven van waarden met behulp van variabelen in uw geautomatiseerde taken en werkstroom die u maakt met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 55984082a6b287e9f7cdca005a24ef3c18032491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456695"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Waarden opslaan en beheren met behulp van variabelen in Azure Logic Apps

In dit artikel ziet u hoe u variabelen maakt en werkt die u gebruikt om waarden op te slaan in uw logische app. Variabelen kunnen u bijvoorbeeld helpen bij het bijhouden van het aantal keren dat een lus wordt uitgevoerd. Als u over een array wilt herhalen of een array wilt controleren op een specifiek item, u een variabele gebruiken om naar het indexnummer voor elk arrayitem te verwijzen.

U variabelen maken voor gegevenstypen zoals geheel getal, zweven, booleaan, tekenreeks, array en object. Nadat u een variabele hebt gemaakt, u andere taken uitvoeren, bijvoorbeeld:

* De waarde van de variabele inpakken of verwijzen.
* Verhoog of verlaag de variabele met een constante waarde, ook wel *increment* en *decrement genoemd*.
* Wijs een andere waarde toe aan de variabele.
* De waarde van de variabele invoegen of *toevoegen* als de laatste keer in een tekenreeks of array.

Er bestaan variabelen en zijn alleen globaal binnen de logische app-instantie waarmee ze worden gemaakt. Ook blijven ze bestaan in alle lusiteraties in een logische app-instantie. Wanneer u naar een variabele verwijst, gebruikt u de naam van de variabele als token, niet de naam van de actie, wat de gebruikelijke manier is om naar de uitvoer van een actie te verwijzen.

> [!IMPORTANT]
> Standaard worden cycli in een lus 'Voor elke' parallel uitgevoerd. Wanneer u variabelen in lussen gebruikt, voert u de lus [achtereenvolgens uit,](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) zodat variabelen voorspelbare resultaten opleveren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* De logische app waar u de variabele wilt maken

  Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) als eerste stap in uw logische app

  Voordat u acties voor het maken en werken met variabelen toevoegen, moet uw logische app beginnen met een trigger.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Variabele initialiseren

U een variabele maken en het gegevenstype en de oorspronkelijke waarde declareren - allemaal binnen één actie in uw logische app. U alleen variabelen op mondiaal niveau declareren, niet binnen scopes, voorwaarden en lussen.

1. Open uw logische app in de Logische App Designer in de [Azure-portal](https://portal.azure.com) of Visual Studio.

   In dit voorbeeld wordt de Azure-portal en een logische app met een bestaande trigger gebruikt.

1. Volg in uw logica-app onder de stap waarin u een variabele wilt toevoegen een van de volgende stappen: 

   * Als u een actie onder de laatste stap wilt toevoegen, selecteert u **Nieuwe stap**.

     ![Actie toevoegen](./media/logic-apps-create-variables-store-values/add-action.png)

   * Als u een actie tussen de stappen wilt toevoegen, verplaatst**+** u de muis over de verbindingspijl, zodat het plusteken ( ) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Voer **onder Een actie kiezen**in `variables` het zoekvak in als filter. Selecteer In de lijst met acties de optie **Initialiserenvariabele**.

   ![Actie selecteren](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Geef deze informatie over uw variabele zoals hieronder beschreven:

   | Eigenschap | Vereist | Waarde |  Beschrijving |
   |----------|----------|-------|--------------|
   | **Naam** | Ja | <*variabele naam*> | De naam voor de variabele te verhogen |
   | **Type** | Ja | <*variabel type*> | Het gegevenstype voor de variabele |
   | **Waarde** | Nee | <*beginwaarde*> | De beginwaarde van uw variabele <p><p>**Tip:** Hoewel optioneel, stelt u deze waarde in als best practice, zodat u altijd de beginwaarde voor uw variabele weet. |
   |||||

   Bijvoorbeeld:

   ![Variabele initialiseren](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Ga nu verder met het toevoegen van de gewenste acties. Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**.

Als u overschakelt van de ontwerper naar de codeweergaveeditor, wordt de variabele actie **Initialize** weergegeven in de definitie van uw logische app, die in de JSON-indeling (JavaScript Object Notation) is:

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> Hoewel de variabele actie **Initialiseren** een `variables` sectie heeft die is gestructureerd als een array, kan de actie slechts één variabele tegelijk maken. Elke nieuwe variabele vereist een individuele **Initialize variabele** actie.

Hier volgen voorbeelden voor enkele andere variabele typen:

*Tekenreeksvariabele*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Booleaanse variabele*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Matrix met gehele getallen*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Array met tekenreeksen*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>De waarde van de variabele krijgen

Als u de inhoud van een variabele wilt ophalen of verwijzen, u ook de [functie variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) gebruiken in de Logic App Designer en de codevieweditor. Wanneer u naar een variabele verwijst, gebruikt u de naam van de variabele als token, niet de naam van de actie, wat de gebruikelijke manier is om naar de uitvoer van een actie te verwijzen.

Met deze expressie worden bijvoorbeeld de items uit de matrixvariabele [die eerder in dit artikel is gemaakt,](#append-value) opgehaald met behulp van de `variables()` functie. De `string()` functie retourneert de inhoud van de variabele in tekenreeksindeling:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Incrementvariabele 

Als u een variabele wilt verhogen of *verhogen met* een constante waarde, voegt u de variabele actie **Bijvoeren** toe aan uw logische app. Deze actie werkt alleen met gehele en zwevende variabelen.

1. Selecteer in de Logic App Designer onder de stap waarin u een bestaande variabele wilt vergroten, de optie **Nieuwe stap**. 

   Deze logische app heeft bijvoorbeeld al een trigger en een actie die een variabele heeft gemaakt. Voeg dus een nieuwe actie toe onder de volgende stappen:

   ![Actie toevoegen](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Als u een actie tussen bestaande stappen wilt toevoegen, verplaatst u de muis over de verbindingspijl, zodat het plusteken (+) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Voer in het zoekvak 'incrementvariable' in als filter. Selecteer In de lijst met acties de optie **Increment-variabele**.

   ![De actie 'Variabele toename' selecteren](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Geef deze informatie op voor het verhogen van uw variabele:

   | Eigenschap | Vereist | Waarde |  Beschrijving |
   |----------|----------|-------|--------------|
   | **Naam** | Ja | <*variabele naam*> | De naam voor de variabele te verhogen |
   | **Waarde** | Nee | <*increment-waarde*> | De waarde die wordt gebruikt voor het verhogen van de variabele. De standaardwaarde is één. <p><p>**Tip:** Hoewel u optioneel bent, stelt u deze waarde in als een aanbevolen praktijk, zodat u altijd de specifieke waarde voor het verhogen van uw variabele weet. |
   ||||

   Bijvoorbeeld:

   ![Voorbeeld van de waarde van de verhoging](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**.

Als u overschakelt van de ontwerper naar de codeweergaveeditor, wordt de variabele actie **Increment** weergegeven in de definitie van uw logische app, die in JSON-indeling is:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Voorbeeld: Lusteller maken

Variabelen worden vaak gebruikt voor het tellen van het aantal keren dat een lus wordt uitgevoerd. In dit voorbeeld ziet u hoe u variabelen voor deze taak maakt en gebruikt door een lus te maken die de bijlagen in een e-mail telt.

1. Maak in de Azure-portal een lege logische app. Voeg een trigger toe die controleert op nieuwe e-mail en eventuele bijlagen.

   In dit voorbeeld wordt de Office 365 Outlook-trigger gebruikt voor **wanneer een nieuwe e-mail wordt binnenkomt.** U deze trigger alleen instellen om te vuren wanneer de e-mail bijlagen bevat. U echter elke connector gebruiken die controleert op nieuwe e-mails met bijlagen, zoals de Outlook.com-connector.

1. Selecteer In de trigger om te controleren op bijlagen en deze bijlagen door te geven aan de werkstroom van uw logische app, de optie **Ja** voor deze eigenschappen:

   * **Heeft bijlage**
   * **Bijlagen opnemen**

   ![Bijlagen controleren en opnemen](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Voeg de variabele actie [ **Initialiseren** toe](#create-variable). Maak een variabele `Count` integer met de naam met een nulstartwaarde.

   ![Actie toevoegen voor 'Variabele initialiseren'](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Als u door elke bijlage wilt fietsen, voegt u een *voor elke* lus toe.

   1. Selecteer onder de variabele actie **Initialiseren** de optie **Nieuwe stap**.

   1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het `for each` zoekvak in als zoekfilter en selecteer **Voor elk**.

      ![Een lus 'voor elke' toevoegen](./media/logic-apps-create-variables-store-values/add-loop.png)

1. Klik in de lus in het vak **Een uitvoer uit vorige stappen selecteren.** Wanneer de lijst met dynamische inhoud wordt weergegeven, selecteert u **Bijlagen**.

   ![Selecteer 'Bijlagen'](./media/logic-apps-create-variables-store-values/select-attachments.png)

   De eigenschap **Bijlagen** passeert een array, die de e-mailbijlagen van de uitvoer van de trigger heeft, in uw lus.

1. Selecteer **in de lus Voor elke** lus de optie Een actie **toevoegen**.

   ![Selecteer 'Een actie toevoegen'](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. Voer in het zoekvak 'incrementvariable' in als filter. Selecteer In de lijst met acties de optie **Variabele Verhogen**.

   > [!NOTE]
   > Zorg ervoor dat de variabele actie **Toename** in de lus wordt weergegeven. Als de actie buiten de lus wordt weergegeven, sleept u de actie naar de lus.

1. Selecteer in de **variabele actie Toename** in de lijst **Naam** de variabele **Aantal.**

   ![Selecteer de variabele 'Tellen'](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. Voeg onder de lus elke actie toe die u het aantal bijlagen stuurt. Neem in uw actie de waarde op van de variabele **Aantal,** bijvoorbeeld:

   ![Een actie toevoegen die resultaten verzendt](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="test-your-logic-app"></a>Uw logische app testen

1. Als uw logische app niet is ingeschakeld, selecteert u **overzicht**in het menu van de logische app . Selecteer op de werkbalk **Inschakelen**.

1. Selecteer Op de werkbalk Logic App Designer de optie **Uitvoeren**. Met deze stap wordt uw logica-app handmatig gestart.

1. Stuur een e-mail met een of meer bijlagen naar het e-mailaccount dat u in dit voorbeeld hebt gebruikt.

   Met deze stap wordt de trigger van de logische app geactiveerd, waardoor een instantie voor de werkstroom van uw logische app wordt gemaakt en uitgevoerd. Als gevolg hiervan stuurt de logische app u een bericht of e-mail met het aantal bijlagen in de e-mail die u hebt verzonden.

Als u overschakelt van de ontwerper naar de codeweergaveeditor, wordt hier de manier weergegeven waarop de variabele actie **Voor** **elke** lus wordt weergegeven in uw logische app-definitie, die in JSON-indeling is.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Decrement variabele

Als u een variabele wilt verlagen of *verlagen* met een constante waarde, volgt u de stappen voor [het verhogen van een variabele,](#increment-value) behalve dat u de variabele actie **Decrement** in plaats daarvan vindt en selecteert. Deze actie werkt alleen met gehele en zwevende variabelen.

Hier zijn de eigenschappen voor de variabele actie **Decrement:**

| Eigenschap | Vereist | Waarde |  Beschrijving |
|----------|----------|-------|--------------|
| **Naam** | Ja | <*variabele naam*> | De naam voor de variabele tot decrement | 
| **Waarde** | Nee | <*increment-waarde*> | De waarde voor het aflaten van de variabele. De standaardwaarde is één. <p><p>**Tip:** Hoewel optioneel, stelt u deze waarde in als een best practice, zodat u altijd de specifieke waarde weet voor het afzetten van uw variabele. |
||||| 

Als u overschakelt van de ontwerper naar de codeweergaveeditor, wordt hier de manier weergegeven waarop de variabele actie **Decrement** wordt weergegeven in uw logische app-definitie, die in JSON-indeling is.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>Variabele instellen

Als u een andere waarde wilt toewijzen aan een bestaande variabele, voert u de stappen uit voor [het verhogen van een variabele,](#increment-value) behalve dat u:

1. Zoek en selecteer in plaats daarvan de variabele actie **Instellen.**

1. Geef de variabele naam en waarde op die u wilt toewijzen. Zowel de nieuwe waarde als de variabele moeten hetzelfde gegevenstype hebben. De waarde is vereist omdat deze actie geen standaardwaarde heeft.

Dit zijn de eigenschappen voor de variabele actie **Instellen:**

| Eigenschap | Vereist | Waarde |  Beschrijving |
|----------|----------|-------|--------------|
| **Naam** | Ja | <*variabele naam*> | De naam voor de variabele om te wijzigen |
| **Waarde** | Ja | <*nieuwe waarde*> | De waarde die u de variabele wilt toewijzen. Beide moeten hetzelfde gegevenstype hebben. |
||||| 

> [!NOTE]
> Tenzij u variabelen verhoogt of decreeert, *kan* het wijzigen van variabelen binnen lussen onverwachte resultaten opleveren omdat lussen standaard of gelijktijdig parallel worden uitgevoerd. Probeer voor deze gevallen de lus in te stellen om opeenvolgend uit te voeren. Als u bijvoorbeeld wilt verwijzen naar de variabele waarde in de lus en dezelfde waarde verwacht aan het begin en einde van die lusinstantie, voert u de volgende stappen uit om de manier waarop de lus wordt uitgevoerd te wijzigen: 
>
> 1. Selecteer in de rechterbovenhoek van uw lus de knop alipsis (**... )** en selecteer **Vervolgens Instellingen**.
> 
> 2. Wijzig **onder Gelijktijdigheidsbesturingselement**de **instelling Standaard overschrijven** in **Aan**.
>
> 3. Sleep de schuifregelaar **Mate van Parallelisme** naar **1**.

Als u overschakelt van de ontwerper naar de codeweergaveeditor, wordt de variabele actie **instellen** in de definitie van uw logische app weergegeven, die in JSON-indeling is. In dit `Count` voorbeeld wordt de huidige waarde van de variabele gewijzigd in een andere waarde.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Toevoegen aan variabele

Voor variabelen die tekenreeksen of arrays opslaan, u de waarde van een variabele invoegen of *toevoegen* als het laatste item in die tekenreeksen of arrays. U de stappen volgen voor [het verhogen van een variabele,](#increment-value) behalve dat u in plaats daarvan de volgende stappen volgt: 

1. Zoek en selecteer een van deze acties op basis van of uw variabele een tekenreeks of een array is: 

   * **Toevoegen aan tekenreeksvariabele**
   * **Toevoegen aan arrayvariabele** 

1. Geef de waarde op die moet worden toegevoegd als het laatste item in de tekenreeks of array. Deze waarde is verplicht.

Hier zijn de eigenschappen voor de **acties Toevoegen aan...**

| Eigenschap | Vereist | Waarde |  Beschrijving |
|----------|----------|-------|--------------|
| **Naam** | Ja | <*variabele naam*> | De naam voor de variabele om te wijzigen |
| **Waarde** | Ja | <*toevoegen-waarde*> | De waarde die u wilt toevoegen, die elk type kan hebben |
|||||

Als u overschakelt van de ontwerper naar de codeweergaveeditor, wordt de variabele actie **Toevoegen aan array** weergegeven in de definitie van uw logische app, die in JSON-indeling is. In dit voorbeeld wordt een matrixvariabele maakt en wordt een andere waarde toegevoegd als het laatste item in de array. Uw resultaat is een bijgewerkte variabele die deze array bevat:`[1,2,3,"red"]`

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logic Apps-connectors](../connectors/apis-list.md)
