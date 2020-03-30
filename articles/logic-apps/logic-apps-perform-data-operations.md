---
title: Bewerkingen uitvoeren op gegevens
description: Gegevensuitvoer en -indelingen in Azure Logic Apps converteren, beheren en manipuleren
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283938"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Gegevensbewerkingen uitvoeren in Azure Logic Apps

In dit artikel ziet u hoe u met gegevens in uw logische apps werken door acties voor deze taken toe te voegen en meer:

* Tabellen maken op arrays.
* Arrays maken van andere arrays op basis van een voorwaarde.
* Maak gebruiksvriendelijke tokens van Json-objecteigenschappen (JavaScript Object Notation), zodat u deze eigenschappen eenvoudig gebruiken in uw werkstroom.

Als u de gewenste actie hier niet vindt, probeert u te bladeren door de vele verschillende [functies voor gegevensmanipulatie](../logic-apps/workflow-definition-language-functions-reference.md) die Azure Logic Apps biedt.

Deze tabellen geven een overzicht van de gegevensbewerkingen die u gebruiken en zijn geordend op basis van de brongegevenstypen waar de bewerkingen aan werken, maar elke beschrijving wordt alfabetisch weergegeven.

**Matrixacties** 

Met deze acties u werken met gegevens in arrays.

| Actie | Beschrijving |
|--------|-------------|
| [**CSV-tabel maken**](#create-csv-table-action) | Maak een door komma's gescheiden waarde (CSV) van een array. |
| [**HTML-tabel maken**](#create-html-table-action) | Maak een HTML-tabel op basis van een array. |
| [**Filterarray**](#filter-array-action) | Maak een arraysubset op basis van het opgegeven filter of de opgegeven voorwaarde. |
| [**Join**](#join-action) | Maak een tekenreeks van alle items in een array en scheid elk item met het opgegeven teken. |
| [**Selecteer**](#select-action) | Maak een array op basis van de opgegeven eigenschappen voor alle items in een andere array. |
||| 

**JSON-acties**

Met deze acties u werken met gegevens in de JSON-indeling (JavaScript Object Notation).

| Actie | Beschrijving |
|--------|-------------|
| [**Opstellen**](#compose-action) | Maak een bericht of tekenreeks op basis van meerdere ingangen die verschillende gegevenstypen kunnen bevatten. U deze tekenreeks vervolgens als één invoer gebruiken, in plaats van herhaaldelijk dezelfde ingangen in te voeren. U bijvoorbeeld één JSON-bericht maken via verschillende ingangen. |
| [**Parse JSON**](#parse-json-action) | Maak gebruiksvriendelijke datatokens voor eigenschappen in JSON-inhoud, zodat u de eigenschappen in uw logische apps gemakkelijker gebruiken. |
|||

Zie [Geavanceerde JSON-transformaties uitvoeren met Liquid-sjablonen](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)voor het maken van complexere JSON-transformaties.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app waar u de bewerking nodig hebt voor het werken met gegevens

  Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) als eerste stap in uw logische app 

  Gegevensbewerkingen zijn alleen beschikbaar als acties, dus voordat u deze acties gebruiken, start u uw logische app met een trigger en bevat u alle andere acties die nodig zijn voor het maken van de gewenste uitvoer.

<a name="compose-action"></a>

## <a name="compose-action"></a>Actie samenstellen

Als u één uitvoer wilt maken, zoals een JSON-object, van meerdere ingangen, u de actie **Samenstellen** gebruiken. Uw ingangen kunnen verschillende typen bevatten, zoals gehele getallen, Booleans, arrays, JSON-objecten en elk ander native type dat Azure Logic Apps ondersteunt, bijvoorbeeld binair en XML. U de uitvoer vervolgens gebruiken in acties die volgen na de actie **Samenstellen.** De actie **Samenstellen** kan u ook besparen op het herhaaldelijk invoeren van dezelfde ingangen terwijl u de workflow van uw logische app bouwt.

U bijvoorbeeld een JSON-bericht samenstellen op basis van meerdere variabelen, zoals tekenreeksvariabelen die de voornamen en achternamen van mensen opslaan, en een variabele integer die de leeftijden van mensen opslaat. Hier accepteert de **actie Compose** de volgende input:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

en creëert deze uitvoer:

`{"age":35,"fullName":"Owens,Sophie"}`

Als u een voorbeeld wilt uitproberen, voert u deze stappen uit met de Logic App Designer. Of als u liever in de codeweergaveeditor werkt, u het voorbeeld (variabele actiedefinities uit dit **artikel) kopiëren** en **initialiseren naar** de onderliggende werkstroomdefinitie van uw eigen logische app: voorbeelden van [gegevensbewerkingscode - Componeren](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Open uw logische app in de [Azure-portal](https://portal.azure.com) of Visual Studio in Logic App Designer.

   In dit voorbeeld wordt de Azure-portal en een logische app met een **recidieftrigger** en verschillende **initialiserende variabele** acties gebruikt. Deze acties zijn ingesteld voor het maken van twee tekenreeksvariabelen en een variabele geheelgetal. Wanneer u later uw logische app test, u uw app handmatig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voorbeeldlogica-app starten voor actie 'Componeren'](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. Voer in uw logische app waar u de uitvoer wilt maken een van de volgende stappen uit: 

   * Als u een actie onder de laatste stap wilt toevoegen, selecteert u **Nieuwe stap**.

     ![Selecteer 'Nieuwe stap' voor de actie 'Componeren'](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Als u een actie tussen de stappen wilt toevoegen,**+** verplaatst u de muis over de verbindingspijl zodat het plusteken ( ) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Voer **onder Een actie kiezen**in `compose` het zoekvak in als filter. Selecteer in de lijst met acties de actie **Samenstellen.**

   ![De actie 'Componeren' selecteren](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Geef in het vak **Ingangen** de gewenste ingangen op voor het maken van de uitvoer.

   Wanneer u in het vak **Invoer** klikt, wordt in dit voorbeeld de lijst met dynamische inhoud weergegeven, zodat u de eerder gemaakte variabelen selecteren:

   ![Invoer selecteren die u wilt gebruiken voor de actie 'Componeren'.](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Hier is het voltooide voorbeeld Stel actie **samen:** 

   ![Voorbeeld voor 'Componeren' actie](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Zie de [actie Samenstellen](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)voor meer informatie over deze actie in de onderliggende werkstroomdefinitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt bevestigen of de actie **Samenstellen** de verwachte resultaten maakt, stuurt u uzelf een melding met uitvoer uit de actie **Samenstellen.**

1. Voeg in uw logica-app een actie toe waarmee u de resultaten van de actie **Samenstellen** kan verzenden.

1. Klik in die actie op de gewenste resultaten. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u onder de actie **Uitvoeren** de optie **Uitvoer**.

   In dit voorbeeld wordt de actie **Een e-mail verzenden** gebruikt en worden de velden **Uitvoer** in de hoofdtekst en het onderwerp van de e-mail weergegeven:

   ![Velden 'Uitvoer' voor de actie 'Componeren'](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Voer nu handmatig uw logische app uit. Selecteer Uitvoeren op de werkbalk **van**de ontwerper .

   Op basis van de e-mailconnector die u hebt gebruikt, zijn hier de resultaten die u krijgt:

   ![E-mail met actieresultaten 'Componeren'](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Csv-tabelactie maken

Als u een csv-tabel (comma-separated value) wilt maken met de eigenschappen en waarden van JSON-objecten (JavaScript Object Notation) in een array, gebruikt u de actie **CSV-tabel maken.** U de resulterende tabel vervolgens gebruiken in acties die de actie **CSV-tabel maken** volgen.

Als u liever in de codeweergaveeditor werkt, u het voorbeeld **CSV-tabel maken** en variabele actiedefinities uit dit artikel **initialiseren** naar de onderliggende werkstroomdefinitie van uw eigen logische app: [voorbeelden van gegevensbewerkingscode - CSV-tabel maken](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. Open uw logische app in de [Azure-portal](https://portal.azure.com) of Visual Studio in Logic App Designer.

   In dit voorbeeld wordt de Azure-portal en een logische app met een **recidief-trigger** en een variabele actie **Initialize** gebruikt. De actie is ingesteld voor het maken van een variabele waarvan de initiële waarde een array is met een aantal eigenschappen en waarden in json-indeling. Wanneer u later uw logische app test, u uw app handmatig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voorbeeldlogica-app starten voor actie CSV-tabel maken](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Voer in uw logische app waar u de CSV-tabel wilt maken een van de volgende stappen uit: 

   * Als u een actie onder de laatste stap wilt toevoegen, selecteert u **Nieuwe stap**.

     ![Selecteer 'Nieuwe stap' voor actie CSV-tabel maken](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Als u een actie tussen de stappen wilt toevoegen,**+** verplaatst u de muis over de verbindingspijl zodat het plusteken ( ) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Voer **onder Een actie kiezen**in `create csv table` het zoekvak in als filter. Selecteer in de lijst met acties de actie **CSV-tabel maken.**

   ![De actie 'CSV-tabel maken' selecteren](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Geef in het vak **Van** de gewenste array of expressie op voor het maken van de tabel.

   Wanneer u in dit voorbeeld in het vak **Van** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u de eerder gemaakte variabele selecteren:

   ![Arrayuitvoer selecteren voor het maken van CSV-tabel](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Als u gebruiksvriendelijke tokens wilt maken voor de eigenschappen in JSON-objecten, zodat u deze eigenschappen als invoer selecteren, gebruikt u de [Parse JSON](#parse-json-action) voordat u de actie **CSV-tabel maken** aanroept.

   Hier is het voltooide voorbeeld **CSV-tabelactie maken:** 

   ![Voorbeeld van het programma 'CSV-tabel maken'](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="customize-table-format"></a>Tabelindeling aanpassen

De eigenschap **Kolommen** is standaard ingesteld om automatisch de tabelkolommen te maken op basis van de arrayitems. Voer de volgende stappen uit om aangepaste kopteksten en waarden op te geven:

1. Open de lijst **Kolommen** en selecteer **Aangepast**.

1. Geef in de eigenschap **Koptekst** de aangepaste koptekst op die in plaats daarvan moet worden gebruikt.

1. Geef in de eigenschap **Waarde** de aangepaste waarde op die u wilt gebruiken.

Als u waarden uit de array [ `item()` ](../logic-apps/workflow-definition-language-functions-reference.md#item) wilt retourneren, u de functie gebruiken met de actie **CSV-tabel maken.** In `For_each` een lus u de [ `items()` functie](../logic-apps/workflow-definition-language-functions-reference.md#items)gebruiken.

Stel dat u tabelkolommen wilt met alleen de eigenschapswaarden en niet de eigenschapsnamen van een array. Als u alleen deze waarden wilt retourneren, voert u deze stappen uit om in de ontwerpweergave of in de codeweergave te werken. Dit voorbeeld geeft als volgt het resultaat:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Werken in de ontwerpweergave

Houd in de actie de kolom **Koptekst** leeg. Geef in elke rij in de kolom **Waarde** deverwijzing naar elke gewenste arrayeigenschap. Elke rij onder **Waarde** retourneert alle waarden voor de opgegeven arrayeigenschap en wordt een kolom in de tabel.

1. Klik onder **Waarde**op elke gewenste rij in het vak bewerken zodat de lijst met dynamische inhoud wordt weergegeven.

1. Selecteer **Expressie**in de lijst met dynamische inhoud .

1. Voer in de expressieeditor deze expressie in die de gewenste waarde van de arrayeigenschap opgeeft en selecteer **OK**.

   `item()?['<array-property-name>']`

   Bijvoorbeeld:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Deverwijzing 'Beschrijving' voor 'CSV-tabel maken'](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Herhaal de vorige stappen voor elke gewenste arrayeigenschap. Wanneer u klaar bent, ziet uw actie er als volgt uit:

   !["item()" functie in "CSV-tabel maken"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Als u expressies wilt oplossen in meer beschrijvende versies, schakelt u over naar de codeweergave en gaat u terug naar de ontwerpweergave en opent u de samengevouwen actie opnieuw:

   De actie **CSV-tabel maken** wordt nu weergegeven als volgt:

   !["CSV-tabel maken" - opgeloste expressies, geen kopteksten](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Werken in de codeweergave

Stel in de JSON-definitie `columns` van de `header` actie binnen de array de eigenschap in op een lege tekenreeks. Voor `value` elke eigenschap u de gewenste arrayeigenschap naverwijzen.

1. Selecteer op de werkbalk van de ontwerper **de codeweergave**.

1. Voeg in de codeeditor in `columns` de array `header` van de `value` actie de lege eigenschap en deze expressie toe voor elke kolom met matrixwaarden die u wilt:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Bijvoorbeeld:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Ga terug naar de ontwerpweergave en open de samengevouwen actie opnieuw.

   De actie **CSV-tabel maken** wordt nu weergegeven als dit voorbeeld en de expressies zijn opgelost in meer beschrijvende versies:

   !["CSV-tabel maken" - opgeloste expressies en geen kopteksten](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Zie de [actie Tabel](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)voor meer informatie over deze actie in de onderliggende werkstroomdefinitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of de actie **CSV-tabel maken** de verwachte resultaten maakt, stuurt u uzelf een melding met uitvoer uit de actie **CSV-tabel maken.**

1. Voeg in uw logica-app een actie toe waarmee u de resultaten van de actie **CSV-tabel maken** verzenden.

1. Klik in die actie op de gewenste resultaten. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u onder de actie **CSV-tabel maken** de optie **Uitvoer**. 

   In dit voorbeeld wordt de actie Office 365 Outlook **verzonden** en wordt het veld **Uitvoer** in de hoofdtekst van de e-mail weergegeven:

   ![Velden 'Uitvoer' voor de actie CSV-tabel maken](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Voer nu handmatig uw logische app uit. Selecteer Uitvoeren op de werkbalk **van**de ontwerper .

   Op basis van de e-mailconnector die u hebt gebruikt, zijn hier de resultaten die u krijgt:

   ![E-mail met actieresultaten 'CSV-tabel maken'](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>HTML-tabelactie maken

Als u een HTML-tabel wilt maken met de eigenschappen en waarden van JSON-objecten (JavaScript Object Notation) in een array, gebruikt u de actie **HTML-tabel maken.** U de resulterende tabel vervolgens gebruiken in acties die de actie **HTML-tabel maken** volgen.

Als u liever in de codeweergaveeditor werkt, u het voorbeeld **HTML-tabel maken** kopiëren en variabele actiedefinities uit dit artikel **initialiseren** naar de onderliggende werkstroomdefinitie van uw eigen logische app: [voorbeelden van gegevensbewerkingscode - HTML-tabel maken](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Open uw logische app in de [Azure-portal](https://portal.azure.com) of Visual Studio in Logic App Designer.

   In dit voorbeeld wordt de Azure-portal en een logische app met een **recidief-trigger** en een variabele actie **Initialize** gebruikt. De actie is ingesteld voor het maken van een variabele waarvan de initiële waarde een array is met een aantal eigenschappen en waarden in json-indeling. Wanneer u later uw logische app test, u uw app handmatig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voorbeeldlogica-app starten voor 'HTML-tabel maken'](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Voer in uw logische app een van de volgende stappen uit:

   * Als u een actie onder de laatste stap wilt toevoegen, selecteert u **Nieuwe stap**.

     ![Selecteer 'Nieuwe stap' voor actie HTML-tabel maken](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Als u een actie tussen de stappen wilt toevoegen,**+** verplaatst u de muis over de verbindingspijl zodat het plusteken ( ) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Voer **onder Een actie kiezen**in `create html table` het zoekvak in als filter. Selecteer in de lijst met acties de actie **HTML-tabel maken.**

   ![De actie 'HTML-tabel maken' selecteren](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Geef in het vak **Van** de gewenste array of expressie op voor het maken van de tabel.

   Wanneer u in dit voorbeeld in het vak **Van** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u de eerder gemaakte variabele selecteren:

   ![Arrayuitvoer selecteren voor het maken van HTML-tabel](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Als u gebruiksvriendelijke tokens wilt maken voor de eigenschappen in JSON-objecten, zodat u deze eigenschappen als invoer selecteren, gebruikt u de [Parse JSON](#parse-json-action) voordat u de actie **HTML-tabel maken** aanroept.

   Hier is het voltooide voorbeeld **HTML-tabelactie** maken:

   ![Voorbeeld van voltooid voor 'HTML-tabel maken'](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="customize-table-format"></a>Tabelindeling aanpassen

De eigenschap **Kolommen** is standaard ingesteld om automatisch de tabelkolommen te maken op basis van de arrayitems. Voer de volgende stappen uit om aangepaste kopteksten en waarden op te geven:

1. Open de lijst **Kolommen** en selecteer **Aangepast**.

1. Geef in de eigenschap **Koptekst** de aangepaste koptekst op die in plaats daarvan moet worden gebruikt.

1. Geef in de eigenschap **Waarde** de aangepaste waarde op die u wilt gebruiken.

Als u waarden uit de array [ `item()` ](../logic-apps/workflow-definition-language-functions-reference.md#item) wilt retourneren, u de functie gebruiken met de actie **HTML-tabel maken.** In `For_each` een lus u de [ `items()` functie](../logic-apps/workflow-definition-language-functions-reference.md#items)gebruiken.

Stel dat u tabelkolommen wilt met alleen de eigenschapswaarden en niet de eigenschapsnamen van een array. Als u alleen deze waarden wilt retourneren, voert u deze stappen uit om in de ontwerpweergave of in de codeweergave te werken. Dit voorbeeld geeft als volgt het resultaat:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Werken in de ontwerpweergave

Houd in de actie de kolom **Koptekst** leeg. Geef in elke rij in de kolom **Waarde** deverwijzing naar elke gewenste arrayeigenschap. Elke rij onder **Waarde** retourneert alle waarden voor de opgegeven eigenschap en wordt een kolom in de tabel.

1. Klik onder **Waarde**op elke gewenste rij in het vak bewerken zodat de lijst met dynamische inhoud wordt weergegeven.

1. Selecteer **Expressie**in de lijst met dynamische inhoud .

1. Voer in de expressieeditor deze expressie in die de gewenste waarde van de arrayeigenschap opgeeft en selecteer **OK**.

   `item()?['<array-property-name>']`

   Bijvoorbeeld:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Eigenschap Dereference in actie 'HTML-tabel maken'](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Herhaal de vorige stappen voor elke gewenste arrayeigenschap. Wanneer u klaar bent, ziet uw actie er als volgt uit:

   !["item()" functie in 'HTML-tabel maken'](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Als u expressies wilt oplossen in meer beschrijvende versies, schakelt u over naar de codeweergave en gaat u terug naar de ontwerpweergave en opent u de samengevouwen actie opnieuw:

   De actie **HTML-tabel maken** wordt nu weergegeven als volgt:

   !["HTML-tabel maken" - opgeloste expressies, geen kopteksten](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Werken in de codeweergave

Stel in de JSON-definitie `columns` van de `header` actie binnen de array de eigenschap in op een lege tekenreeks. Voor `value` elke eigenschap u de gewenste arrayeigenschap naverwijzen.

1. Selecteer op de werkbalk van de ontwerper **de codeweergave**.

1. Voeg in de codeeditor in `columns` de array `header` van de `value` actie de lege eigenschap en deze expressie toe voor elke kolom met matrixwaarden die u wilt:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Bijvoorbeeld:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Ga terug naar de ontwerpweergave en open de samengevouwen actie opnieuw.

   De actie **HTML-tabel maken** wordt nu weergegeven als dit voorbeeld en de expressies zijn opgelost in meer beschrijvende versies:

   !["HTML-tabel maken" - opgeloste expressies en geen kopteksten](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Zie de [actie Tabel](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)voor meer informatie over deze actie in de onderliggende werkstroomdefinitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt bevestigen of de actie **HTML-tabel maken** de verwachte resultaten maakt, stuurt u uzelf een melding met uitvoer uit de actie **HTML-tabel maken.**

1. Voeg in uw logica-app een actie toe waarmee u de resultaten van de actie **HTML-tabel maken** verzenden.

1. Klik in die actie op de gewenste resultaten. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u onder de actie **HTML-tabel maken** de optie **Uitvoer**. 

   In dit voorbeeld wordt de actie Office 365 Outlook **verzonden** en wordt het veld **Uitvoer** in de hoofdtekst van de e-mail weergegeven:

   ![Velden 'Uitvoer' voor 'HTML-tabel maken'](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Wanneer u de HTML-tabeluitvoer opneemt in een e-mailactie, moet u ervoor zorgen dat u de eigenschap **IS HTML** instelt op **Ja** in de geavanceerde opties van de e-mailactie. Op die manier maakt de e-mailactie de HTML-tabel correct op.

1. Voer nu handmatig uw logische app uit. Selecteer Uitvoeren op de werkbalk **van**de ontwerper .

   Op basis van de e-mailconnector die u hebt gebruikt, zijn hier de resultaten die u krijgt:

   ![E-mail met de resultaten van de HTML-tabel maken](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Arrayactie filteren

Als u een kleinere array wilt maken met items die voldoen aan specifieke criteria, van een bestaande array, gebruikt u de actie **Filterarray.** U de gefilterde array vervolgens gebruiken in acties die volgen na de actie **Filterarray.**

> [!NOTE]
> Alle filtertekst die u in uw conditie gebruikt, is hoofdlettergevoelig. Met deze actie kan ook de indeling of onderdelen van items in de array niet worden gewijzigd. 
> 
> Als u de arrayuitvoer van de arrayactie **Filter** wilt gebruiken, moeten deze acties arrays accepteren als invoer of moet u de uitvoerarray mogelijk omzetten in een andere compatibele indeling.
> 
> Als u een HTTP-eindpunt aanroept en een JSON-antwoord ontvangt, gebruikt u de actie **Parse JSON** om het JSON-antwoord te verwerken. 
> Anders kan de arrayactie **Filter** alleen de responsbody lezen en niet de structuur van de JSON-payload.

Als u liever in de codeweergaveeditor werkt, u het **voorbeeldfilterarray** kopiëren en variabele actiedefinities uit dit artikel **initialiseren** naar de onderliggende werkstroomdefinitie van uw eigen logica-app: [voorbeelden van gegevensbewerkingscode - Array filteren](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. Open uw logische app in de [Azure-portal](https://portal.azure.com) of Visual Studio in Logic App Designer.

   In dit voorbeeld wordt de Azure-portal en een logische app met een **recidief-trigger** en een variabele actie **Initialize** gebruikt. De actie is ingesteld voor het maken van een variabele waarvan de oorspronkelijke waarde een array is met enkele voorbeeldgehele gehele getallen. Wanneer u later uw logische app test, u uw app handmatig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   > [!NOTE]
   > Hoewel in dit voorbeeld een eenvoudige gehele array wordt gebruikt, is deze actie vooral handig voor JSON-objectarrays waar u filteren op basis van de eigenschappen en waarden van de objecten.

   ![Voorbeeldlogica-app starten voor actie 'Array filteren'](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. Voer in uw logische app waar u de gefilterde array wilt maken een van de volgende stappen uit: 

   * Als u een actie onder de laatste stap wilt toevoegen, selecteert u **Nieuwe stap**.

     ![Selecteer 'Nieuwe stap' voor de actie 'Array filteren'](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Als u een actie tussen de stappen wilt toevoegen,**+** verplaatst u de muis over de verbindingspijl zodat het plusteken ( ) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Voer in het `filter array` zoekvak in als filter. Selecteer in de lijst met acties de actie **Array filteren.**

   ![De actie 'Array filteren' selecteren](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Geef in het vak **Van** de array of expressie op die u wilt filteren.

   Wanneer u in dit voorbeeld in het vak **Van** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u de eerder gemaakte variabele selecteren:

   ![Arrayuitvoer selecteren voor het maken van gefilterde array](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Geef voor de voorwaarde de te vergelijken arrayitems op, selecteer de vergelijkingsoperator en geef de vergelijkingswaarde op.

   In dit `item()` voorbeeld wordt de functie gebruikt voor de toegang tot elk item in de array, terwijl de actie **Filterarray** zoekt naar arrayitems waarvan de waarde groter is dan één:

   ![Voorbeeld van voltooid voorbeeld voor actie 'Array filteren'](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Zie [Queryactie](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action)voor meer informatie over deze actie in de onderliggende werkstroomdefinitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt bevestigen of de actie **Filterarray** de verwachte resultaten maakt, stuurt u uzelf een melding met uitvoer uit de **arrayactie Filter.**

1. Voeg in uw logische app een actie toe waarmee u de resultaten van de **arrayactie Filter** verzenden.

1. Klik in die actie op de gewenste resultaten. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u **Expressie**. Als u de arrayuitvoer wilt ophalen uit de actie **Filterarray,** voert u deze expressie in die de naam van de actie **Filterarray** bevat:

   `@actionBody('Filter_array')`

   In dit voorbeeld wordt de office 365 **Outlook-actie verzonden** en worden de uitvoer van de expressie **actionBody('Filter_array'** in de hoofdtekst van de e-mail weergegeven:

   ![Actie-uitvoer van actie 'Array filteren'](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Voer nu handmatig uw logische app uit. Selecteer Uitvoeren op de werkbalk **van**de ontwerper .

   Op basis van de e-mailconnector die u hebt gebruikt, zijn hier de resultaten die u krijgt:

   ![E-mail met actieresultaten 'Array filteren'](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Meedoen actie

Als u een tekenreeks wilt maken met alle items uit een array en deze items wilt scheiden met een specifiek scheidingstekenteken, gebruikt u de actie **Deelnemen.** U de tekenreeks vervolgens gebruiken in acties die volgen na de actie **Deelnemen.**

Als u liever in de codeweergaveeditor werkt, u het voorbeeld **Join en** **Initialiseren variabele** actiedefinities uit dit artikel kopiëren naar de onderliggende workflowdefinitie van uw eigen logische app: voorbeelden van [gegevensbewerkingscode - Deelnemen](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. Open uw logische app in de [Azure-portal](https://portal.azure.com) of Visual Studio in Logic App Designer.

   In dit voorbeeld wordt de Azure-portal en een logische app met een **recidief-trigger** en een variabele actie **Initialize** gebruikt. Deze actie is ingesteld voor het maken van een variabele waarvan de oorspronkelijke waarde een array is met enkele voorbeeldgehele gehele getallen. Wanneer u uw logica-app later test, u uw app handmatig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voorbeeldlogica-app starten voor actie 'Deelnemen'](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. Voer in uw logische app waar u de tekenreeks wilt maken vanuit een array een van de volgende stappen uit:

   * Als u een actie onder de laatste stap wilt toevoegen, selecteert u **Nieuwe stap**.

     ![SSelect "Nieuwe stap" voor de actie 'Deelnemen'](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Als u een actie tussen de stappen wilt toevoegen,**+** verplaatst u de muis over de verbindingspijl zodat het plusteken ( ) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Voer in het `join` zoekvak in als filter. Selecteer deze actie in de lijst met acties: **Deelnemen**

   ![Selecteer actie 'Deelnemen'](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Geef in het vak **Van** de array op met de items waaraan u als tekenreeks wilt deelnemen.

   Wanneer u in dit voorbeeld in het vak **Van** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u de eerder gemaakte variabele selecteren:  

   ![Arrayuitvoer selecteren voor het maken van de tekenreeks](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Voer **in het** vak Join met het gewenste teken het gewenste teken in voor het scheiden van elk arrayitem. 

   In dit voorbeeld wordt een dubbele punt (:) als de separator.

   ![Het scheidingsteken opgeven](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Zie de [actie Deelnemen](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action)voor meer informatie over deze actie in de onderliggende werkstroomdefinitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt bevestigen of de actie **Deelnemen** de verwachte resultaten maakt, stuurt u uzelf een melding met uitvoer uit de actie **Deelnemen.**

1. Voeg in uw logica-app een actie toe waarmee u de resultaten van de actie **Deelnemen** kan verzenden.

1. Klik in die actie op de gewenste resultaten. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u onder de actie **Deelnemen** de optie **Uitvoer**. 

   In dit voorbeeld wordt de actie Office 365 Outlook **verzonden** en wordt het veld **Uitvoer** in de hoofdtekst van de e-mail weergegeven:

   ![Velden 'Uitvoer' voor de actie Deelnemen](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Voer nu handmatig uw logische app uit. Selecteer Uitvoeren op de werkbalk **van**de ontwerper .

   Op basis van de e-mailconnector die u hebt gebruikt, zijn hier de resultaten die u krijgt:

   ![E-mail met actieresultaten 'Deelnemen'.](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Parse JSON actie

Als u wilt verwijzen naar of toegang wilt krijgen tot eigenschappen in Json-inhoud (JavaScript Object Notation), u gebruiksvriendelijke velden of tokens voor die eigenschappen maken met behulp van de actie **Parse JSON.** Op die manier u deze eigenschappen selecteren in de lijst met dynamische inhoud wanneer u invoer opgeeft voor uw logische app. Voor deze actie u een JSON-schema opnemen of een JSON-schema genereren uit uw voorbeeld JSON-inhoud of -payload.

Als u liever in de codeweergaveeditor werkt, u het voorbeeld **Parse JSON** kopiëren en variabele actiedefinities uit dit artikel **initialiseren** naar de onderliggende werkstroomdefinitie van uw eigen logische app: [voorbeelden van gegevensbewerkingscode - Parse JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. Open uw logische app in de [Azure-portal](https://portal.azure.com) of Visual Studio in Logic App Designer.

   In dit voorbeeld wordt de Azure-portal en een logische app met een **recidief-trigger** en een variabele actie **Initialize** gebruikt. De actie is ingesteld voor het maken van een variabele waarvan de initiële waarde een JSON-object is met eigenschappen en waarden. Wanneer u later uw logische app test, u uw app handmatig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voorbeeldlogica-app starten voor de actie 'Parse JSON'](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. Volg in uw logische app waar u de JSON-inhoud wilt ontleden, een van de volgende stappen:

   * Als u een actie onder de laatste stap wilt toevoegen, selecteert u **Nieuwe stap**.

     ![Selecteer 'Nieuwe stap' voor de actie 'Parse JSON'](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Als u een actie tussen de stappen wilt toevoegen,**+** verplaatst u de muis over de verbindingspijl zodat het plusteken ( ) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Voer in het `parse json` zoekvak in als filter. Selecteer in de lijst met acties de actie **Parse JSON.**

   ![Selecteer de actie 'Parse JSON'](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. Geef in het vak **Inhoud** de JSON-inhoud op die u wilt ontleden.

   Wanneer u in dit voorbeeld in het vak **Inhoud** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u de eerder gemaakte variabele selecteren:

   ![Json-object selecteren voor de actie PARSe JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Voer het JSON-schema in waarin de JSON-inhoud wordt beschreven die u ontleden.

   Voor dit voorbeeld is hier het JSON-schema:

   ![Json-schema opbieden voor het JSON-object dat u wilt ontleden](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Als u het schema niet hebt, u dat schema genereren op basis van de JSON-inhoud of *de payload*die u ontleedt. 
   
   1. Selecteer in de actie **Parse JSON** de optie **Voorbeeldpayload gebruiken om schema te genereren.**

   1. Geef **onder Een voorbeeld JSON-payload in of plak**deze, geef de JSON-inhoud op en selecteer **Gereed**.

      ![Voer de JSON-inhoud in voor het genereren van het schema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Zie [Parse JSON-actie](../logic-apps/logic-apps-workflow-actions-triggers.md)voor meer informatie over deze actie in de onderliggende werkstroomdefinitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt bevestigen of de **parse JSON-actie** de verwachte resultaten maakt, stuurt u uzelf een melding met uitvoer van de **actie Parse JSON.**

1. Voeg in uw logica-app een actie toe waarmee u de resultaten van de **actie Parse JSON** kan verzenden.

1. Klik in die actie op de gewenste resultaten. Wanneer de lijst met dynamische inhoud wordt geopend, u onder de actie **Parse JSON** nu de eigenschappen selecteren uit de geparseerde JSON-inhoud.

   In dit voorbeeld wordt de functie Office 365 **Outlook verzonden** en de velden **Voornaam,** **Achternaam**en **E-mail** in de hoofdtekst van de e-mail bevat:

   ![JSON-eigenschappen in de actie 'Een e-mail verzenden'](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Hier is de afgewerkte e-mail actie:

   ![Voorbeeld voor e-mailactie voltooid](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Voer nu handmatig uw logische app uit. Selecteer Uitvoeren op de werkbalk **van**de ontwerper . 

   Op basis van de e-mailconnector die u hebt gebruikt, zijn hier de resultaten die u krijgt:

   ![E-mail met actieresultaten "Parse JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Actie selecteren

Als u een array wilt maken met JSON-objecten die zijn gebouwd op basis van waarden in een bestaande array, gebruikt u de actie **Selecteren.** U bijvoorbeeld een JSON-object maken voor elke waarde in een gehele gehele array door de eigenschappen op te geven die elk JSON-object moet hebben en hoe u de waarden in de bronarray aan die eigenschappen toewijzen. En hoewel u de componenten in die JSON-objecten wijzigen, heeft de uitvoerarray altijd hetzelfde aantal items als de bronarray.

> [!NOTE]
> Als u de arrayuitvoer van de actie **Selecteren** wilt gebruiken, moeten deze acties arrays accepteren als invoer of moet u de uitvoerarray mogelijk omzetten in een andere compatibele indeling. 

Als u liever in de codeweergaveeditor werkt, u het voorbeeld Variabele actiedefinities uit dit artikel **selecteren** en **initialiseren** naar de onderliggende werkstroomdefinitie van uw eigen logische app: [voorbeelden van gegevensbewerkingscode - Selecteren](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Open uw logische app in de [Azure-portal](https://portal.azure.com) of Visual Studio in Logic App Designer.

   In dit voorbeeld wordt de Azure-portal en een logische app met een **recidief-trigger** en een variabele actie **Initialize** gebruikt. De actie is ingesteld voor het maken van een variabele waarvan de oorspronkelijke waarde een array is met enkele voorbeeldgehele gehele getallen. Wanneer u later uw logische app test, u uw app handmatig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voorbeeldlogica-app starten voor de actie Selecteren](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. Voer in uw logische app waar u de array wilt maken een van de volgende stappen uit: 

   * Als u een actie onder de laatste stap wilt toevoegen, selecteert u **Nieuwe stap**.

     ![Selecteer 'Nieuwe stap' voor de actie Selecteren](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Als u een actie tussen de stappen wilt toevoegen,**+** verplaatst u de muis over de verbindingspijl zodat het plusteken ( ) wordt weergegeven. Selecteer het plusteken en selecteer **Een actie toevoegen**.

1. Selecteer **Ingebouwde**actie onder **Een actie kiezen**. Voer in het `select` zoekvak in als filter. Selecteer in de lijst met acties de actie **Selecteren.**

   ![Selecteer de actie 'Selecteren'](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Geef in het vak **Van** de gewenste bronarray op.

   Wanneer u in dit voorbeeld in het vak **Van** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u de eerder gemaakte variabele selecteren:

   ![Bronarray selecteren voor actie Selecteren](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Geef in de linkerkolom **van het** vak Kaart de eigenschapsnaam op die u elke waarde in de bronarray wilt toewijzen. Geef in de rechterkolom een expressie op die de waarde vertegenwoordigt die u de eigenschap wilt toewijzen.

   In dit voorbeeld wordt 'Product_ID' opgegeven als de eigenschapsnaam `item()` om elke waarde in de gehele array toe te wijzen met behulp van de functie in een expressie die toegang geeft tot elk arrayitem. 

   ![Json-objecteigenschap en -waarden opgeven om array te maken](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Hier is de voltooide actie:

   ![Voorbeeld van voltooid voorbeeld voor actie 'Selecteren'](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Zie [Actie selecteren](../logic-apps/logic-apps-workflow-actions-triggers.md)voor meer informatie over deze actie in de onderliggende werkstroomdefinitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of de actie **Selecteren** de verwachte resultaten maakt, stuurt u uzelf een melding met uitvoer uit de actie **Selecteren.**

1. Voeg in uw logica-app een actie toe waarmee u de resultaten van de actie **Selecteren** kan verzenden.

1. Klik in die actie op de gewenste resultaten. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u **Expressie**. Voer deze expressie in **Select** met de naam van de actie **Selecteren:**

   `@actionBody('Select')`

   In dit voorbeeld wordt de actie Office 365 Outlook `@actionBody('Select')` **verzonden** en worden de uitvoer van de expressie in de hoofdtekst van de e-mail weergegeven:

   ![Actie-uitvoer van de actie Selecteren](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Voer nu handmatig uw logische app uit. Selecteer Uitvoeren op de werkbalk **van**de ontwerper .

   Op basis van de e-mailconnector die u hebt gebruikt, zijn hier de resultaten die u krijgt:

   ![E-mail met actieresultaten selecteren](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logic Apps-connectors](../connectors/apis-list.md)
